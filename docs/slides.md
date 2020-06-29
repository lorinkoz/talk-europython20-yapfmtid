name: title
class: middle

# Yet another package for<br/>multi-tenancy in Django

Lorenzo Peña &middot; @lorinkoz

---

## The agenda

1. Django in 2020
2. Django, multi-tenancy & you
3. The challanges ahead
    - Database, models & managers
    - Routing requests to tenants
    - The scope of everything
    - Cross-tenant security
    - Late game monsters
4. Yet another package for this

---

class: middle

# Django in 2020

---

## 2020 is going great so far

![Dinosaur in traffic with meteors falling](images/dinosaur-traffic.jpg)

---

name: django

## But so is Django <small>(no... seriously)</small>

![Django banner](images/django.png)

---

template: django

-   Mature, solid and battle tested.
-   For perfectionists (like you)
-   For people with deadlines (like you)

---

template: django

-   Vast ecosystem - over 4k projects .ref[1]
-   Bright minds are second-guessing the modern web .ref[2]
-   People are doing email services today in vanilla monolith makers .ref[3]

.bottom[
.footnote[.ref[1] As listed in https://djangopackages.org]
.footnote[.ref[2] https://macwright.org/2020/05/10/spa-fatigue.html]
.footnote[.ref[3] https://twitter.com/dhh/status/1275901955995385856?s=20]
]

---

class: middle

# Django, multi-tenancy & you

--

What do Django, multi-tenancy and you have in common?

---

## I have an hypothesis

--

The world is divided in two kinds of Djangonauts:

-   Those who **have done** multi-tenancy in Django.
-   Those who **will be doing** multi-tenancy in Django (anytime soon).

--

.box[🔥 Hot take, isn't it?]

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

class: middle

What do Django, multi-tenancy and you have in common?

--

The future... at least 😉

---

## What is multi-tenancy

Software architecture in which a single instance of software runs on a server and serves multiple tenants.

A tenant is a group of users who share a common access with specific privileges to the software instance. .ref[1]

.bottom[
.footnote[.ref[1] https://en.wikipedia.org/wiki/Multitenancy]
]

---

## Multiple types of multi-tenancy

-   Users can be seen as a tenants
-   Users exist regardless of tenants
-   Users exist inside a tenant

---

## How do we get to it?

-   Many things to do, and do right.
-   Non trivial parts.
-   Help is very much appreciated.

---

## Package first?

-   There is a number of solid packages to help with the multi-tenancy problem in Django.
-   The whole idea of this talk came from my experience forking one of those packages.

.box[✋ But let's not take a package-first approach]

Instead, let's pretend we're going to implement multi-tenancy from scratch, without the help of any package.

---

class: middle

# The challenges ahead

---

## The active tenant

We have to adjust our mindset:

.box[Most operations will require a tenant to be considered **active**]

-   How to get / set the active tenant?
-   When there is no active tenant, what will happen to the operation?

---

class: middle

# Database, models & managers

---

## Database architecture

1. Isolated: Multiple databases, one per tenant
2. Semi-isolated: One database, one schema per tenant (PostgreSQL)
3. Shared: One database, tenant column on (almost) every table
4. Standalone: Code and database containerized .ref[1]

.bottom[
.footnote[.ref[1] Although this doesn't fit too well the concept of multi-tenancy we saw before]
]

---

name: isolated-db

## Isolated databases

---

template: isolated-db

Multi-database configuration in Django settings

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

template: isolated-db

Model operations and query need to define the active tenant.

---

name: shared-db

## Shared database

---

template: shared-db

Assign active tenant before creating a model instance:

```python
order = Order(...)
order.tenant = get_active_tenant()
order.save()
```

---

template: shared-db

Use active tenant in all queries:

```python
# In regular queries
Order.objects.create(tenant=get_active_tenant(), ...)
Order.objects.filter(tenant=get_active_tenant(), ...)

# But also in related queries
some_customer.orders.filter(
    order__tenant=get_active_tenant(),
    ...
)
```

---

template: shared-db

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

class: middle

# Routing requests to tenants

---

class: middle

# The scope of everything

---

class: middle

# Cross-tenant security

---

class: middle

# Late game monsters

---

class: middle

# Yet another package for this

---

template: title
