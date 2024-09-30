---
layout: post
title: Implementando arquitectura limpia en Django
categories: [Content]
tags: [django, python]
---

### Implementando arquitectura limpia en Django


En un proyecto Django que implemente Arquitectura Limpia, CQRS, y algunos de los patrones adicionales mencionados 
(como Event-Driven Architecture, Saga Pattern, Repository Pattern, y Factory Pattern), 
la estructura final podría quedar organizada de la siguiente manera. Cada uno de estos patrones se 
puede aplicar en diferentes partes del proyecto para obtener un código más modular, escalable y desacoplado.


### 1.Estructura Global del Proyecto

![Estructura Global del Proyecto]({{ site.baseurl }}/images/content/django-clean-architecture/django-1.png)

### 2. Explicación de los Componentes

#### config/

Esta carpeta contiene toda la configuración global de tu proyecto Django. Aquí están 
los archivos principales como settings.py, urls.py y wsgi.py.

#### apps/

Cada app en Django se convierte en un módulo independiente, donde cada una tiene 
sus propios directorios de dominio, casos de uso, infraestructura, interfaces, y fábricas. 
Esta organización permite que las aplicaciones sean modulares y fácilmente mantenibles.

Dentro de cada app:

#### domain/

Contiene las entidades y la lógica de negocio específica de la app. Aquí se define el comportamiento de los objetos de 
dominio (como Product, Order, etc.) y se manejan los eventos de dominio si estás utilizando Event-Driven Architecture (EDA).


#### Ejemplo de entidad en product.py:
```python3
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def change_price(self, new_price):
        self.price = new_price
```

#### use_cases/

Aquí es donde residen los casos de uso, los cuales se dividen entre commands y queries según el patrón CQRS.

	•	Commands: Operaciones que modifican el estado del sistema (escritura).
	•	Queries: Operaciones que solo consultan el estado del sistema (lectura).

#### Ejemplo de create_product.py (Command):

```python3
class CreateProductCommand:
    def __init__(self, product_repository):
        self.product_repository = product_repository

    def execute(self, name, price):
        product = Product(name, price)
        self.product_repository.save(product)
```

#### Ejemplo de get_product_list.py (Query):

```python3
class GetProductListQuery:
    def __init__(self, product_repository):
        self.product_repository = product_repository

    def execute(self):
        return self.product_repository.get_all()
```
#### interfaces/

Esta capa contiene los adaptadores para interactuar con otros sistemas, como bases de datos o servicios externos. Aquí es donde se implementa el Repository Pattern, asegurando que el acceso a datos esté desacoplado del dominio.

Ejemplo de un repositorio en repository.py:

```python3
class ProductRepository:
    def get_all(self):
        return ProductModel.objects.all()

    def save(self, product):
        product_model = ProductModel(name=product.name, price=product.price)
        product_model.save()
```

#### infrastructure/

En esta carpeta se incluyen los componentes dependientes de Django, como los modelos, serializadores para la API REST, vistas (controladores) y los mapeos de URLs.

Ejemplo de vista para un comando (crear producto):

```python3
from rest_framework.views import APIView
from rest_framework.response import Response
from ..use_cases.commands.create_product import CreateProductCommand
from ..interfaces.repository import ProductRepository

class CreateProductView(APIView):
    def post(self, request):
        name = request.data.get('name')
        price = request.data.get('price')

        product_repository = ProductRepository()
        command = CreateProductCommand(product_repository)
        command.execute(name, price)
        
        return Response({"message": "Producto creado"}, status=201)
```

#### factories/

Aquí es donde se ubican las fábricas que gestionan la creación de objetos complejos o dependientes de diferentes configuraciones. Esto permite desacoplar la lógica de creación de objetos de la lógica de negocio.

Ejemplo de una fábrica de notificaciones:

```python3
class NotificationFactory:
    @staticmethod
    def create_notification(type, recipient, message):
        if type == 'email':
            return EmailNotification(recipient, message)
        elif type == 'sms':
            return SMSNotification(recipient, message)
        else:
            raise ValueError(f"Unsupported notification type: {type}")
```

### 3. Implementación de Patrones Clave

    1.	CQRS: Separación de comandos y consultas en la carpeta use_cases. Los commands son responsables de modificar el estado y los queries solo de la lectura.
    2.	Event-Driven Architecture (EDA): Puedes manejar eventos de dominio dentro de la carpeta domain/events.py, y con señales de Django o herramientas como Celery, gestionar las reacciones a esos eventos.
    3.	Saga Pattern: La lógica de transacciones distribuidas, como la gestión de pedidos que dependen de múltiples servicios (pago, envío), se maneja en use_cases/sagas/.
    4.	Repository Pattern: Los repositorios en la carpeta interfaces/ desacoplan el acceso a datos de los casos de uso.
    5.	Factory Pattern: Las fábricas en factories/ crean objetos complejos o configurables según los requisitos del negocio.

Conclusión

La estructura resultante tiene las siguientes características clave:

	•	Modularidad: Cada app es independiente y sigue principios de separación de responsabilidades.
	•	Desacoplamiento: La lógica de negocio está completamente separada de la infraestructura de Django. Los casos de uso y los repositorios permiten aislar las dependencias.
	•	Escalabilidad: La aplicación puede crecer fácilmente al añadir nuevas apps o funcionalidades siguiendo este patrón estructurado.
	•	Patrones flexibles: Puedes combinar patrones como CQRS, EDA, Saga, Repositorios, y Fábricas, lo que hace que tu código sea más robusto y mantenible.

Este enfoque es ideal para proyectos grandes o que van a escalar, permitiéndote tener control sobre cada capa de la aplicación y facilitar su crecimiento o mantenimiento.

### Tecnolog&iacute;a Utilizada
 - [Django](https://www.djangoproject.com/)
 - [Python](https://www.python.org)
