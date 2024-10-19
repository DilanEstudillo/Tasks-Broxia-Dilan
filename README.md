# Tasks-Broxia-Dilan
Describe cómo diseñarías una API RESTful con FastAPI o el framework de tu preferencia para un sistema de gestión de tareas (to-do list).


Primero tengo que estructurar el sistema de gestión de tareas(to-do-list). que sea rápido , flexible, plasmare la estructura del proyecto, en mi caso , usaré Django.

Crear una tarea: Se añadirá una tarea con:
Título
descripción
Estado / pendiente / en proceso / completada.
Leer tareas: Listar tareas y alguna tarea en específico.
Actualizar tareas:
Modificar título
Modificar descripción
Modificar estado de la tarea.
Eliminar Tareas: Borrar tarea en específico.


     b.  Una vez definida la estructura.

instalaré Django manualmente o previamente instalaré mi requirements donde tendre todas las dependencias y versión de Django.
requirements.txt       #Este es mi archivo con todas mis dependencias a instalar en mi entorno virtual.

pip install -r requirements.txt

Después creo mi APP de django , en este caso le pondre como nombre Tasks.
django-admin startproject APITasks
django-admin startapp tasks

La estructura de django se crea automáticamente al crear tu app.

     

 c.   Creare una de las parte mas importantes que es creación de modelos en mi archivo       models.py.
        
         from django.db import models 

        class Task(models.Model): 
              STATUS_CHOICES = [ 
                    ('pending', 'Pending'), 
                    ('in_progress', 'In Progress'),
                    ('completed', 'Completed'), 
               ] 

           title = models.CharField(max_length=255) 
           description = models.TextField(blank=True, null=True) 
           status = models.CharField(max_length=20, 
           choices=STATUS_CHOICES, default='pending') 

          def __str__(self): 
               return self.title

Algo muy importante es que hayas colocado ya tus conexiones a tu base de datos en tu archivo settings.py para poder hacer tus migraciones de modelos a tu base de datos.

DATABASES = {
  'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': tasksdb,
        'USER': 'postgres',
        'PASSWORD': 'broxia123',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
python manage.py makemigrations    # eso sirve para crear el archivo de migración
python manage.py migrate    # este sirve para ejecutar tus archivos de  migraciones 


d. Una vez hecha la migración en el proyecto de tus bases de datos , procedemos a realizar los Serializadores.
en el archivo serializer,py , esto se encarga de convertir los modelos a Formatos que se pueden enviar y recibir en la API.

from rest_framework import serializers
from .models import Task

class TaskSerializer(serializers.ModelSerializer):
    class Meta:
        model = Task
        fields = ['id', 'title', 'description', 'status']


e. Después haremos nuestros Views con sus correspondientes rutas
primero en tu archivo views.py haremos las vistas donde se manejan las solicitudes GET,POST ,PUT y DELETE.


from rest_framework import generics
from .models import Task
from .serializers import TaskSerializer

class TaskListCreateView(generics.ListCreateAPIView):
    queryset = Task.objects.all()
    serializer_class = TaskSerializer

class TaskDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Task.objects.all()
    serializer_class = TaskSerializer

y después creas sus correspondientes rutas

from django.urls import path
from . import views

urlpatterns = [
    path('tasks/', views.TaskListCreateView.as_view(), name='task-list-create'),
    path('tasks/<int:pk>/', views.TaskDetailView.as_view(), name='task-detail'),
]








f. Como paso final tienes que crear las rutas finales del proyecto.

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('tasks.urls')),  # Incluimos las rutas de la API ,esto es muy importante
]



Listo simplemente corres el proyecto y ya queda funcionando el API.

python manage.py runserver





 FUNCIONALIDADES DEL API:
GET /api/tacks/ Obtener la lista de todas las tareas
POST /api/tacks/ Crear nueva tarea.
GET /api/tacks/<id>/ Obtener detalles de una tarea en específico
PUT /api/tacks/<id>/ Actualizar una tarea en específico.
DELETE /api/tacks/<id>/  Eliminar una tarea en específico.
