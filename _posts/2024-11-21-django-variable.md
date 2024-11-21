---
layout: post
title: APP Django Variable
categories: [Content]
tags: [django, python]
---

### APP Django Variable

django_variable es una app de django para definir configuraciones dinámicamente y 
administrables desde el panel de administrador.


### Como trabaja

Establezca los valores de configuración mediante admin (admin/variable_config/configuration/) Estos valores se mantienen en la base de datos (uno por fila) y se almacenan en una caché en memoria para un acceso posterior.



### Requerimientos

Python +3.8
Django >= 5


### Como instalar

pip install django-variable


### Configuración:

```python3
# settings.py

INSTALLED_APPS = [
    # ...

    'django_variable',
]
```

### Después de configurar:

```python3
python manage.py migrate

```

### Como funciona.

```python3

from django_variable.utils import get_config

get_config('KEY_CONFIG', 'DEFAULT_VALUE')

```


### Repositorio.
[https://github.com/jeissonp/django-variable](https://github.com/jeissonp/django-variable)


### Tecnolog&iacute;a Utilizada
 - [Django](https://www.djangoproject.com/)
 - [Python](https://www.python.org)
