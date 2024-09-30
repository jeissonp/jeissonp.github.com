---
layout: post
title: Módulo Drupal "Statistics by content type"
categories: [Porfolio]
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


### Tecnolog&iacute;a Utilizada
 - [Django](https://www.djangoproject.com/)
 - [Python](https://www.python.org)
