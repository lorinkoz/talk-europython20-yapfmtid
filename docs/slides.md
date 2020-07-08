name: title
class: middle

# Yet another package for<br/>multi-tenancy in Django

![Badges: Cuba, EuroPython 2020 and Django](images/badges.png)

Lorenzo Peña &middot; @lorinkoz

???

Here:

-   Good afternoon.
-   Who am I: name, city, profession, years of experience with Django.
-   Thank the audience for joining, title of the talk.

Transition:

-   Introduction to the 2020 joke: in what's probably the most overused joke of the year...

---

## 2020 is going great so far

.center[![Dinosaur in traffic with meteors falling](images/dinosaur-traffic.jpg)]

.bottom[
.footnote[This is arguably one of the most overused jokes of the year]
]

---

layout: true

## But so is Django <small>(no... seriously)</small>

---

![Django motto](images/django.png)

--

-   Mature, solid and battle tested.
-   For perfectionists with deadlines (like you).
-   Vast ecosystem - over 4k projects..ref[1]

.bottom[
.footnote[.ref[1] As listed in https://djangopackages.org]
]

???

Here: [JUST FOLLOW THE SLIDE]

Transition:

-   Three things have taken place in 2020 which IMO are also good signs of the health of Django as a framework.

---

.left-column[Bright minds are second-guessing the modern web..ref[1]]
.right-column[.right[![Meme of developer adding tons of javascript to website](images/oil-javascript-meme.png)]]

.bottom[
.footnote[.ref[1] https://macwright.org/2020/05/10/spa-fatigue.html]
]

???

-   This article from Tom MacWright (almost three months ago) which you should read if you haven't.
-   This resonated among the brightest minds of the Django community, and outside.
-   We've just added too much oil to the salad.
-   We could be very close to an inflection point in the history of the web in which we start enjoying boring frameworks again.

---

People are doing email services in vanilla monolith makers..ref[1]

.center[![Tweet from DDH about the Hey stack](images/dhh-tweet-hey-stack.png)]

.bottom[
.footnote[.ref[1] https://twitter.com/dhh/status/1275901955995385856]
]

???

-   Email service that launched about a month ago: HEY
-   Regardless of whether you like the service or whether you agree with the philosophical alignment of its public figures, this is an interesting event.
-   Email service made in vanilla Ruby on Rails, no heavyweight frontend, with HTML over the wire.
-   This is good news for Rails, but it also extrapolates as good news for Django.

---

Reactivity (not React) in Django is gaining some traction..ref[1].ref[2]

.center[![Screenshot of edelvalle/reactor on GitHub](images/django-reactor-github.png)]
.center[![Screenshot of jonathan-s/django-sockpuppet on GitHub](images/django-sockpuppet-github.png)]

.bottom[
.footnote[.ref[1] https://github.com/edelvalle/reactor]
.footnote[.ref[2] https://github.com/jonathan-s/django-sockpuppet]
]

???

-   Django reactivity has gained some traction lately.
-   Check out these repositories (one of them from a fellow Cuban).
-   Imagine your templates automatically refreshing on the client from a change in your models, all through websockets.
-   Do you see the future coming from that direction?

---

class: middle center
layout: false

![Meme of Django lifting the heavy weight of 2020 decade](images/django-2020-meme.png)

### Django is in great shape for the starting decade

---

class: middle
layout: false

# Django and multi-tenancy

--

What does it have to do with .emph[you]?

---

## I have an hypothesis

--

The world is divided in two kinds of Djangonauts:

-   Those who **are doing** multi-tenancy.
-   Those who **will be doing** multi-tenancy anytime soon.

--

.box[🔥 Multi-tenancy is inevitable]

???

-   The assumed prophetic wisdom here is that multi-tenancy is inevitable in a Djangonaut's career.
-   Hot take, huh? What do you think?

--

.box[🦉 You don't come to it, it comes to you]

???

-   But it's also true that most likely, you won't wake up one day trying to code something for the sake of multi-tenancy itself.
-   Oportunity usually presents itself as something that works good for a single tenant, and can be smartly expanded to a multi-tenant architecture, say, a million dollar idea for a SaaS.

So, my dear audience, if you're not doing multi-tenancy in Django right now, I suspect that the oportunity can be just around the corner. So be prepared!

---

class: middle center
layout: false

![Meme of "Is this a pigeon?" about multi-tenancy](images/pigeon-meme-multi-tenancy.png)

???

Now, before getting any deeper in the topic, and in case you're wondering what are we talking about, let's get on the same page on some definitions.

---

layout: true

## A tiny bit of theory

.bottom[
.footnote[.ref[1] https://en.wikipedia.org/wiki/Multitenancy]
]

---

**Multi-tenancy:** Software architecture in which a single instance of software runs on a server and serves multiple tenants..ref[1]

.right[![Diagram of multi-tenanacy](images/diagram-multi-tenancy.png)]

???

[JUST REPHRASE AS EXPLANATION]

---

**Tenant:** Group of users who share a common access with specific privileges to the software instance..ref[1]

.right[![Diagram of tenants](images/diagram-tenant.png)]

???

Think of it as a private environment, in a running shared software, where one or many users interact in isolation.

---

layout: true

## Types of multi-tenancy

---

???

Now, the relationship between users and tenants delineates some types of multi-tenancy we should be aware of.

---

Users exist **outside** the context of tenants:

.center[![Diagram of users outside tenants](images/diagram-users-out-tenants.png)]

---

Users exist **within** the context of tenants:

.center[![Diagram of users inside tenants](images/diagram-users-in-tenants.png)]

---

Users exist **as** tenants:

.center[![Diagram of users equalling tenants](images/diagram-users-equal-tenants.png)]

???

Here: [CONTINUE THE EXPLANATION]

Transition:

How do we actually do this in Django?

---

layout: false
class: middle center

![Meme of Boromir "One does not simply walk into Mordor" about multi-tenancy](images/boromir-mordor-meme-multi-tenancy.png)

???

Why?

-   Lots of things to do and do right.
-   Some of those things are non-trivial.

---

layout: true

## A package for multi-tenancy

---

-   There are multiple packages for handling multi-tenancy.
-   Yes, I made one of them (a fork, actually)

???

So, do you need a package as foundation for your multi-tenant project? Most likely yes!

[CONTINUE READING]

--

.box[🦉 But none of them solves it all]

???

Because the topic is too complex and has many many sides.

--

-   Let's pretend we're going to do multi-tenancy from scratch.
-   You'll get a package, but not one you can install.

---

<p>Tired:</p>

> Give someone a fish and you feed them for a day; teach someone to fish and you feed them for a lifetime.

???

Here:

Most likely you've heard of the proverb.

Transition:

Now, the equivalent of this proverb applied to multi-tenancy would be...

--

<p>Wired:</p>

> Give them a package for a SaaS and they will make it; teach them the underlying principles and they will break it.

???

I consider a good thing that you are capable of breaking your SaaS. Only doing that you'll be able to rebuild it better for the benefit of us all.

---

class: middle
layout: false

# The challenges ahead

???

Briefly mention the challenges ahead:

-   Active tenant
-   Database management
-   Routing requests
-   The scope of everything else

---

layout: true

## The active tenant

---

???

This takes a change of mindset.

---

.box[Most operations will now require a tenant to be considered 😎 **the active tenant**]

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

There are things that are currently tracked by Django as an **active something** independent of the request / response cycle. For instance: **timezone** and **language**.

The same idea is applicable to track the **active tenant**, so that you can have:

```python
tenant = get_active_tenant()
set_active_tenant(tenant)
```

---

```python
# Drop-in replacement for threading.locals that works with asyncio
from asgiref.local import Local


_active = Local()

def get_active_tenant():
    return getattr(_active, "value", None)

def set_active_tenant(tenant):
    _active.value = tenant
```

???

Here's a basic implementation example.

--

.warning[⚠️ The use of globals is generally discouraged]

???

Warning:

-   You shouldn't start creating globals for everything!
-   There is a reason why this pattern is so scarce in the Django codebase itself.
-   The more you depend on a global variable, the more coupled becomes your code, harder to test in isolation.
-   This would be an acceptable use case of an otherwise frowned upon pattern.

---

```python
tenant = get_active_tenant()

# What do you expect to be the result of the next line?
*print(type(tenant))
```

???

[READ THE COMMENT IN THE CODE]

--

.box[🙋 Are tenants always represented by a **model**?]

???

[READ QUESTION]

Not necessarily, you might want to have tenants that are less dynamic in nature, such as a blog site, or the help center.

--

.box[💡 Tenants might require a higher abstraction]

???

Be advised! [READ NUGGET]

---

.box[🤔 What if, for some operation, there is **no active tenant**?]

--

You will have to answer some questions in a case by case basis:

-   Is it a bug?
-   Does it make sense without a tenant?
-   Does it imply all tenants are targeted?

---

class: middle
layout: false

# Database, models and managers

???

Once that we have the concept of active tenant settled, it's time to move to one of the most interesting parts of multi-tenancy...

---

## Database architecture

--

**Isolated:**<br/>Multiple databases, one per tenant

**Shared:**<br/>One database, tenant column on (almost) every table

**Semi-isolated:**<br/>One database, one schema per tenant (PostgreSQL)

???

This is the common knowledge you get on most multi-tenancy documents out there.

---

layout: true

## Isolated databases

---

.center[![Diagram of isolated tenants](images/diagram-isolated.png)]

???

One database for every tenant.

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

Django has a thing called database routers:

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

layout: true

## Shared database

---

.center[![Diagram of shared database](images/diagram-shared.png)]

---

Entry-level, tenant-specific models require a FK to the model that controls the tenants:

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

Assign the active tenant before creating a model instance:

```python
# Via model save
order = Order(...)
order.tenant = get_active_tenant()
order.save()

# Via manager create
Order.objects.create(tenant=get_active_tenant(), ...)
```

---

Scope the relevant queries with the active tenant:

```python
# In regular queries
Order.objects.filter(tenant=get_active_tenant(), ...)

# In related queries
some_customer.orders.filter(order__tenant=get_active_tenant(), ...)

```

---

Set the active tenant when saving a form / serializer:

```python
# Form
instance = OrderForm.save(commit=False)
instance.tenant = get_active_tenant()
instance.save()

# DRF Serializer
instance = serializer.save(tenant=get_active_tenant())
```

--

.box[🙋 Do all this allow for some automation?]

---

Tenant field could be automatically **assigned** via:

-   Default value for the field (a callable).
-   Custom field with a `pre_save` hook.
-   `pre_save` signal on relevant models.

---

Tenant scope could be automatically **queried** via:

-   Custom manager.
-   Custom queries.

--

.warning[👀 Certain subqueries and aggregations might still require manual scoping]

---

**.green[Benefits]**

-   Optimized for scalability.

**.red[Limitations]**

-   Extra care to define tenant annotated models.
-   Extra care with tenant annotated queries.

---

.left-column[![Meme of brain not letting you sleep on scoped queries](images/brain-sleep-meme-scopes.png)]

--

.right-column[

-   Bookmark all your tenant anotated queries.
-   Unit test each one of them.
-   Make the test suite fail if any query is untested.
    ]

--

.right-column[
.box[🧸 Tests are a soft pillow]
]

---

layout: true

## Semi-isolated database

---

.right[![Diagram of semi-isolated tenants](images/diagram-semi-isolated.png)]

---

Rely on PostgreSQL schemas.ref[1] to isolate tenants within a single database.

**Schemas** are an intermediary layer between database and tables.

.bottom[
.footnote[.ref[1] https://www.postgresql.org/docs/9.1/ddl-schemas.html]
]

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

Requires a database router in order to control which models are migrated on which schemas:

```python
class SemiIsolatedTenantsRouter:
    def allow_migrate(self, db, app_label, model_name=None,
                      **hints):
        tenant = get_active_tenant()
        if tenant is not None:
*           return app_is_tenant_specific(app_label)
*       return app_is_shared(app_label)
```

???

This also implies that migrations can no longer be run project-wise, but on each schema.

---

**.green[Benefits]**

-   Optimized for isolation.
-   Allows relations between tenants and shared data.

**.red[Limitations]**

-   No cross tenant relations.
-   Extra care to define shared apps and tenant specific apps.
-   Extra care to define where to put users, sessions and ctypes.

---

.center[![Meme of crazy lady and cat about schemas and migrations](images/cat-lady-meme-schemas.png)]

???

-   Migrations now must be run in all schemas.
-   It takes discipline to do zero downtime upgrades.

---

layout: true

## Which one is the best?

---

---

.warning[🔥 Neither!]

--

.left-column-66[Hit me in the Q&A because this deserves more than a slide, although here's some food for thought...]
.right-column-33[.right[![Young boy after a food fight](images/food-fight.png)]]

---

|                       | Isolated | Shared | Semi-isolated |
| --------------------- | -------- | ------ | ------------- |
| Users inside tenants  | 🙂       | 🙂     | 🙂            |
| Users outside tenants | 😱       | 🙂     | 🤔😅          |
| Tenant isolation      | 😃       | 🤔😬   | 🤔😃          |
| Tenant aggregations   | 😬       | 😃     | 😬            |
| Database cost         | 🤑       | 🙂     | 🙂            |
| Database migrations   | 😅       | 😃     | 😅😬          |
| Overall scalability   | 😬       | 😃     | 🤨            |

---

class: middle
layout: false

# Requests and URL reversing

---

layout: true

## Activating a tenant from the incoming request

---

.left-column[

##### Captured in the request

-   Inferred from the user
-   Stored in the session
-   Specified in the headers
    ]

.right-column[

##### Captured in the URL

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
        if tenant and not get_active_tenant():
            set_active_tenant(tenant)
        return get_response(request)
    return middleware
```

--

.box[💡 Middleware with different retrieval methods<br/>can be chained]

---

.warning[⚠️ The order of middleware is important!]

--

.left-column[

```python
SessionMiddleware
*TenantFromSessionMiddleware
AuthenticationMiddleware
*TenantFromUserMiddleware
```

]

.right-column[

```python
*StandaloneTenantMiddleware
SessionMiddleware
AuthenticationMiddleware
```

]

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

.warning[👀 But some combinations are tricky]

---

**Via subdomain**<br/>
Django only reverses the path, so the full domain of the tenant must be prepended.

**Via subfolder**<br/>
All URLs must be interpolated with tenant. In order to make it URLConf transparent, a clever hack is required.

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

--

.warning[👀 This requires some boilerplate code]

---

## Management commands

-   For new management commands, you can include a tenant argument, so you can activate the tenant before executing the command.
-   For existing, non tenant-aware commands, you can define a special command wrapper.

--

.warning[⚠️ This gets trickier the more elegant]

---

## File storage

-   You can define a custom tenant storage that organizes files per tenant.
-   If cross-tenant file access is a security problem for you, you will need a custom view to act as proxy for files and decide if the incoming request has access to the requested file.

---

class: middle
layout: false

# Finally, the fish

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

##### Shared database

-   [citusdata/django-multitenant](https://github.com/citusdata/django-multitenant)
-   [raphaelm/django-scopes](https://github.com/raphaelm/django-scopes)

--

##### Semi-isolated database

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

.right[![Figurines used to represent users with sunglasses](images/figurines.png)]

---

## And that's it!

##### We can keep in touch here:

|         |                                                    |
| ------- | -------------------------------------------------- |
| Twitter | [@lorinkoz](https://twitter.com/lorinkoz)          |
| GitHub  | [github.com/lorinkoz](https://github.com/lorinkoz) |
| Email   | [lorinkoz@gmail.com](mailto:lorinkoz@gmail.com)    |

##### Special thanks to:

Russell Keith-Magee, Raphael Michel

---

template: title
