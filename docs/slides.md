name: title
class: middle

# Yet another package for<br/>multi-tenancy in Django

![Badges: Cuba, EuroPython 2020 and Django](images/badges.png)

Lorenzo Peña &middot; @lorinkoz

---

## The agenda

1. Django in 2020
2. Django, multi-tenancy and you
3. The challanges ahead
    - The active tenant
    - Database, models and managers
    - Requests and URL reversing
    - The scope of everything else
4. Yet another package for this?

---

class: middle
layout: false

# Django in 2020

---

## 2020 is going great so far

.center[![Dinosaur in traffic with meteors falling](images/dinosaur-traffic.jpg)]

.bottom[
.footnote[This is arguably one of the most overused jokes of the year]
]

---

layout: true

## But so is Django <small>(no... seriously)</small>

![Django motto](images/django.png)

---

-   Mature, solid and battle tested
-   For perfectionists (like you)
-   For people with deadlines (like you)
-   Vast ecosystem - over 4k projects.ref[1]

.bottom[
.footnote[.ref[1] As listed in https://djangopackages.org]
]

---

-   Bright minds are second-guessing the modern web.ref[1]
-   People are doing email services in vanilla monolith makers.ref[2]
-   There is progress with Django template reactivity.ref[3]

.bottom[
.footnote[.ref[1] https://macwright.org/2020/05/10/spa-fatigue.html]
.footnote[.ref[2] https://twitter.com/dhh/status/1275901955995385856?s=20]
.footnote[.ref[3] https://github.com/edelvalle/reactor]
]

---

class: middle
layout: false

# Django, multi-tenancy and you

--

What do Django, multi-tenancy and you have in common?

---

## I have an hypothesis

--

The world is divided in two kinds of Djangonauts:

-   Those who **have done** multi-tenancy.
-   Those who **will be doing** multi-tenancy anytime soon.

--

.box[🔥 Hot take?]

--

.right[Well...]

---

.top[
![Reddit question about multi-tenancy in Django](images/django-mt-reddit.png)
]

---

.top[
![Reddit question about multi-tenancy in Django](images/django-mt-reddit-1.png)
]

.box[Sooner or later, you're going to have a<br/>🤑 multi-million dollar idea]

---

.top[
![Reddit question about multi-tenancy in Django](images/django-mt-reddit-2.png)
]
.box[And you're going to need some form of<br/>🏘️ multi-tenancy for it]

---

.top[
![Reddit question about multi-tenancy in Django](images/django-mt-reddit-3.png)
]

.box[And as crazy as it may sound, in order to implement it, you're going to use 🥁 Django]

---

.top[
![Reddit question about multi-tenancy in Django](images/django-mt-reddit-4.png)
]

.box[So let's face it:<br/>🔥 This post may as well have been made by you]

---

class: center middle
layout: false

---

## What is multi-tenancy

Software architecture in which a single instance of software runs on a server and serves multiple tenants.

A tenant is a group of users who share a common access with specific privileges to the software instance..ref[1]

.bottom[
.footnote[.ref[1] https://en.wikipedia.org/wiki/Multitenancy]
]

---

layout: true

## Types of multi-tenancy

---

Users exist **within** the context of tenants (one to many)

.center[![Diagram of users inside tenants](images/diagram-users-in-tenants.png)]

---

Users exist **outside** the context of tenants (many to many)

.center[![Diagram of users outside tenants](images/diagram-users-out-tenants.png)]

---

Users are tenants (one to one, equivalent to single-tenancy)

.center[![Diagram of users equalling tenants](images/diagram-users-equal-tenants.png)]

---

layout: true

## How do we get to it?

---

Can I just start hacking my project?

--

Yes, but...

.warning[🤹 There are many things to do, and do right]
.warning[🤯 There are non trivial parts]

---

-   There are solid packages to help with the multi-tenancy problem in Django.
-   The whole idea of this talk came from my experience forking one of those packages.

--

.box[✋ But let's not take a package-first approach]

--

Instead, let's pretend we're going to implement multi-tenancy from scratch, without the help of any package.

---

class: middle
layout: false

# The challenges ahead

---

layout: true

## The active tenant

---

We have to adjust our mindset:

.box[Most operations will now require a tenant to be considered 😎 **the active tenant**]

---

There are things that are currently tracked by Django as an **active something** independent of the request / response cycle. For instance: **timezone** and **language**.

We could use the same logic to also track the **active tenant**, so that we get at our disposal:

```python
tenant = get_active_tenant()
set_active_tenant(tenant)
```

---

```python
from asgiref.local import Local


_active = Local()

def get_active_tenant():
    return getattr(_active, "value", None)

def set_active_tenant(tenant):
    _active.value = tenant
```

--

.warning[⚠️ In general, this should be avoided]

---

```python
tenant = get_active_tenant()

# What do we expect to be the result of the next line?
print(type(tenant))
```

--

.box[💡 Tenants objects should be abstracted above model instances]

???
Here we'll have to explain that an instance of a model might not be sufficient.

---

.box[🤔 What if, for some operation, there is **no active tenant**?]

--

We will have to answer some questions in a case by case basis:

-   Is the lack of a tenant a bug in this context?
-   Does this operation make sense in a tenant agnostic way?
-   Should we interpret the lack of tenant as an indication that the operation must be performed on all tenants?

---

Every part of the framework needs to be able to operate in the scope of the active tenant:

--

.left-column[

-   Database access
-   URL reversing
-   Admin site
-   Cache
    ]

.right-column[

-   Channels (websockets)
-   Management commands
-   Celery tasks
-   File storage
    ]

--

.red[And everything else...]

---

class: middle
layout: false

# Database, models and managers

---

layout: true

## Database architecture

---

.left-column-66[**Isolated:** Multiple databases, one per tenant]
.right-column-33[![Diagram of isolated tenants](images/diagram-isolated.png)]

---

.left-column-66[**Shared:** One database, tenant column on (almost) every table]
.right-column-33[![Diagram of shared database](images/diagram-shared.png)]

---

.left-column-66[**Semi-isolated:** One database, one schema per tenant (PostgreSQL)]
.right-column-33[![Diagram of semi-isolated tenants](images/diagram-semi-isolated.png)]

---

layout: true

## Isolated databases

---

Multi-database configuration in Django settings:

```python
DATABASES = {
    "default": {...},
    "tenant1": {...},
    "tenant2": {...},
    "tenant3": {...},
    ...
}
```

---

Queries need to define the active tenant:

```python
tenant = get_active_tenant()
active_db = get_database_for_tenant(tenant)

order = Order(...)
order.save(using=active_db)

Order.objects.using(active_db).filter(...)
Order.objects.db_manager(active_db).do_something(...)
```

--

.box[🙋 How to control queries outside of your code?]

---

Django has a thing called database routers

```python
class IsolatedTenantsRouter:

    def db_for_read(self, model, **hints):
        tenant = get_active_tenant()
        return get_database_for_tenant(tenant)

    def db_for_write(self, model, **hints):
        tenant = get_active_tenant()
        return get_database_for_tenant(tenant)
```

---

**.green[Benefits]**

-   Optimized for isolation.

**.red[Limitations]**

-   No cross-tenant relations.
-   No relation between tenants and shared data.
-   Increased costs of deployment.
-   Adding tenants require reconfiguring the project.

---

-   Do it if you expect a number of tenants in the lower tens.
-   Or if you can afford to buy a new Lamborghini for every tenant you get.

.right[![Three Lamborghinis](images/lamborghini.png)]

---

layout: true

## Shared database

---

All tenant-specific models require a FK to the model that controls the tenants:

```python
class SharedTenantModel(models.Model):

    tenant = models.ForeignKey(
        "TenantModel",
        on_delete=models.PROTECT,  # No easy tenant deletion
        related_name="%(class)ss"
    )

    class Meta:
        abstract = True
```

---

Assign active tenant before creating a model instance:

```python
order = Order(...)
order.tenant = get_active_tenant()
order.save()
```

---

Scope all queries with active tenant:

```python
# In regular queries
Order.objects.create(tenant=get_active_tenant(), ...)
Order.objects.filter(tenant=get_active_tenant(), ...)

# In related queries
some_customer.orders.filter(order__tenant=get_active_tenant(), ...)
```

---

Set active tenant when saving a form / serializer:

```python
# Form
instance = OrderForm.save(commit=False)
instance.tenant = get_active_tenant()
instance.save()

# DRF Serializer
instance = serializer.save(tenant=get_active_tenant())
```

---

This quickly escalates to:

.center[![Django motto with perfectionists replaced with burned out people](images/django-burned-out.png)]

--

The good news is that:

.box[💡 It's possible to automatically inject the tenant in most use cases, but this requires .emph[additional wizardry]]

---

**.green[Benefits]**

-   Optimized for scalability.

**.red[Limitations]**

-   Extra care to define tenant annotated models.
-   Extra care with tenant annotated queries.

---

**.green[Recommendations]**

-   Put all your tenant anotated queries in a single place.
-   Unit test each one of them, and make the test suite fail if any query is untested.

--

.box[🧸 Tests are a soft pillow]

---

layout: true

## Semi-isolated database

---

Use PostgreSQL schemas.ref[1] to isolate tenants within a single database.

.bottom[
.footnote[.ref[1] https://www.postgresql.org/docs/9.1/ddl-schemas.html]
]

--

-   PostgreSQL schemas are an intermediary layer between database and tables.
-   Tables are scoped by schemas, and can be duplicated across schemas.

--

.warning[⚠️ HYPE WARNING]

---

The concept of **search path** allows for interesting combinations of isolated and shared data.

--

.left-column[
With `search_path=tenant1,shared` tables are searched in the schema of tenant 1 first, then in the shared schema
]
.right-column[![Diagram of schemas](images/schemas.png)]

---

Requires a custom database backend in order to set `search_path` based on active tenant:

```python
from django.db.backends.postgresql import base as postgresql
class DatabaseWrapper(postgresql.DatabaseWrapper):
    def _cursor(self, name=None):  # Over simplified !!!
        cursor = super()._cursor(name=name)
        tenant = get_active_tenant()
*       schemas = get_schemas_from_tenant(tenant)
        search_path = ",".join(schemas)
*       cursor.execute(f"SET search_path = {search_path}")
        return cursor
```

---

Requires a database router in order to control which models are migrated on which schemas.

```python
class SemiIsolatedTenantsRouter:
    def allow_migrate(self, db, app_label, model_name=None,
                      **hints):
        tenant = get_active_tenant()
        if tenant is not None:
*           return app_is_tenant_specific(app_label)
*       return app_is_shared(app_label)
```

---

**.green[Benefits]**

-   Optimized for isolation.
-   Allows relations between tenants and shared data.

**.red[Limitations]**

-   No cross tenant relations.
-   Extra care to define shared apps and tenant specific apps.
-   Extra care to define where to put users, sessions and content types.

---

.center[![Meme of crazy lady and cat about schemas and migrations](images/cat-lady-meme-schemas.png)]

???
Explain that migrations must be run in all schemas, takes discipline to do zero downtime upgrades.

---

layout: false

## Does it scale?

--

.warning[🔥 Yes and no!]

--

.left-column-66[Hit me in the Q&A because this deserves more than a slide. We can talk about sharding too...]
.right-column-33[.right[![Young boy after food fight](images/food-fight.png)]]

---

class: middle
layout: false

# Requests and URL reversing

---

layout: true

## Activating a tenant from the incoming request

---

.left-column[

**Captured in the request**

-   Inferred from the user
-   Stored in the session
-   Specified in the headers
    ]

.right-column[

**Captured in the URL**

-   Via subdomain
-   Via subfolder
-   Via query parameter
    ]

--

.box[A tenant can be activated from an incoming request<br/>via 😎 middleware]

---

```python
def TenantFromSessionMiddleware(get_response):
    def middleware(request):
*       tenant = get_tenant_from_session(request.session)
        if tenant:
            set_active_tenant(tenant)
        return get_response(request)
    return middleware
```

--

.box[💡 Middleware with different retrieval methods<br/>can be chained]

---

.warning[⚠️ The order of middleware is important!]

--

-   If it depends on the session it must go after `SessionMiddleware`.
-   If it depends on the user it must go after `AuthenticationMiddleware`.
-   If instead, users depend on the active tenant, then the tenant middleware must go before `AuthenticationMiddleware`.

---

layout: true

## Reversing tenant-aware URLs

---

--

.box[🙋‍♀️ How to reverse URLs so that the tenant is included?]

--

For some cases it's simply not possible:

|     |                          |
| --- | ------------------------ |
| ❌  | Inferred from the user   |
| ❌  | Stored in the session    |
| ❌  | Specified in the headers |

---

The only possible way is when the tenant is inferred from the URL itself:

|     |                     |                               |
| --- | ------------------- | ----------------------------- |
| ✔️  | Via subdomain       | `tenant1.example.com/view/`   |
| ✔️  | Via subfolder       | `example.com/tenant1/view/`   |
| ✔️  | Via query parameter | `example.com/view/?t=tenant1` |

--

.warning[👀 But it's not trivial either]

---

**Via subdomain**<br/>
Django only reverses the path, so the full domain of the tenant must be prepended.

**Via subfolder**<br/>
In order to abstract away the beginning of the path in a transparent way, .emph[additional wizardry] must be performed.

**Via query parameter**<br/>
All URLs must be appended with the query parameter.

---

class: middle
layout: false

# The scope of everything else

---

## Admin site

-   For the URL it comes as part of your tenant routing scheme.
-   For adjusting the models that are available to edit, you might need to create a custom admin site.

---

## Cache

You can define a tenant-specific cache key function:

```python
# settings.py
CACHES = {
    "default": {
        ...
        "KEY_FUNCTION": "myproject.cache.get_key_from_tenant",
    }
}

# myproject/cache.py
def get_key_from_tenant(key, key_prefix, version):
    tenant = get_active_tenant()
    return "{}:{}:{}:{}".format(tenant, key_prefix, version, key)
```

---

## Celery tasks

You can pass the tenant to activate as one of your task parameters:

```python
@app.task(bind=True)
def some_celery_task(self, tenant_id, ...):
    tenant = get_tenant_from_id(tenant_id)
    set_active_tenant(tenant)
    ...
```

---

## Channels (websockets)

-   Requires custom middleware to activate tenant from request.
-   Requires naming your consumer groups including the tenant (for proper cross-tenant group isolation)

.warning[👀 This requires some boilerplate code]

---

## Management commands

-   For new management commands, you can include a tenant argument, so you can activate the tenant before executing the command.
-   For existing, non tenant-aware commands, you can define a special command wrapper.

.warning[⚠️ This gets trickier the more elegant]

---

## File storage

-   You can define a custom tenant storage that organizes files per tenant.
-   If cross-tenant file access is a security problem for you, you will need a custom view to act as proxy for files and decide if the incoming request has access to the requested file.

---

class: middle
layout: false

# Yet another package for this?

---

layout: true

## Available packages

---

Multi-tenancy grid of **djangopackages.org**

https://djangopackages.org/grids/g/multi-tenancy/

--

.warning[⚠️ Check activity and compatibility of packages]

--

.box[🙏 Add yours if it's not there]

---

**Shared database**

-   [citusdata/django-multitenant](https://github.com/citusdata/django-multitenant)
-   [raphaelm/django-scopes](https://github.com/raphaelm/django-scopes)

--

**Semi-isolated database**

-   [bernardopires/django-tenant-schemas](https://github.com/bernardopires/django-tenant-schemas)
-   [tomturner/django-tenants](https://github.com/tomturner/django-tenants)

???
Packages are opinionated, primarily from the database architecture, but also from tenant routing.

---

layout: false

## Contribute back

-   Do you have specific requirements that are not covered in any of the existing packages?
-   Can the community benefit from those?
-   Can you make it work in a compatible way?

--

.box[⭐ Come, we need .emph[you]!]

---

template: title
