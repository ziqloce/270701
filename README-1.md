## 1. Connect to database
In ```settings.py``` \
Modify database setting by replacing with your actual information.
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'airline',  # Replace with your database name
        'USER': 'postgres', # Replace with your database username
        'PASSWORD': 'password', # Replace with your actual password
        'HOST': 'localhost', # Replace with the host where PostgreSQL is running
        'PORT': '5432',  # Replace with the port PostgreSQL is listening on
    }
}
```

## 2. Model: Create Model.py
Create file ```models.py``` in your project directory. 
```python
from django.db import models

class Aircrafts(models.Model):
    aircraft_code = models.CharField(max_length=3, primary_key=True)
    model = models.JSONField()
    range = models.IntegerField()

    def __str__(self):
        return f"{self.aircraft_code} - {self.model}"

    class Meta:
        db_table = 'aircrafts'
        app_label = 'first_project' 
```

## 3. Controller: Create Function
Define view function in ```views.py``` to retrieve information about the aircraft with the maximum range from the database and render it in a template (max_range_aircraft.html).
```python
def max_range_aircraft(request):
    # Query for the aircraft with the maximum range
    try:
        aircraft = Aircrafts.objects.order_by('-range').first()
        max_range_aircraft = {
            'aircraft_code': aircraft.aircraft_code,
            'model': aircraft.model.get('en', ''),
            'range': aircraft.range,
        }
    except Aircrafts.DoesNotExist:
        max_range_aircraft = None

    return render(request, 'max_range_aircraft.html', {'max_range_aircraft': max_range_aircraft})
```

## 4. URLs Handling
In ```urls.py```, add url path.
```python
path('max_range_aircraft/', views.max_range_aircraft, name='max_range_aircraft')
```

## 5. View: Create html page
**Please noted that: your actual html page will different based on your main.css** \
Create file in templates folder: ```max_range_aircraft.html```
```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Max Range Aircraft</title>
        <link rel="stylesheet" type="text/css" href="{% static 'main.css' %}">
    </head>
    <body>
        <div class="container">
            <h1>Aircraft with Maximum Range!!</h1>
            <div class="aircraft-info">
                <p><span class="field-label">Aircraft Code:</span> {{ max_range_aircraft.aircraft_code }}</p>
                <p><span class="field-label">Model:</span> {{ max_range_aircraft.model }}</p>
                <p><span class="field-label">Range (km):</span> {{ max_range_aircraft.range }}</p>
            </div>  
            <p><a href="{% url 'home' %}">Back to Home</a></p>
        </div>
    </body>
</html>
```
