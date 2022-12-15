---
title: 'Django, Pytest and data used in Migrations'
date: 2022-12-15 22:24:00
description: TransactionTestCase and data from DB migrations.
featured_image: '/images/demo/demo-square.jpg'
---

## Background

Today, I wanted to increase a pytest test suite, which is running in the gitlab CI on 4 parallel workers.
I noted earlier that the load of these 4 workers is not evenly distributed. 
So straight forward idea was to execute the tests in random order. Before this change, tests that were in the same directory were often also executed on the same worker.

I was surprised to see a lot of test errors suddenly. Eventually, I fixed all of them. 

## Today I Learned

One problem was that the test suite needs data from DB migrations. However, in a few tests people were using the [TransactionTestCase](https://docs.djangoproject.com/en/4.1/topics/testing/tools/#django.test.TransactionTestCase).
They were actually used indirectly through the [@pytest.mark.django_db](https://pytest-django.readthedocs.io/en/latest/helpers.html#pytest.mark.django_db) mark.

As explained [in the docs](https://docs.djangoproject.com/en/4.1/topics/testing/overview/#rollback-emulation), the `TransactionTestCase`, does **not** retain data from data migrations.
In our case, just using the `db` fixture in the tests (they did not really need transaction support) fixed some test failures.

