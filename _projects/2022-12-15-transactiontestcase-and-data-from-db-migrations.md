---
title: 'Django, Pytest and data used in Migrations'
date: 2022-12-15 22:24:00
description: TransactionTestCase and data from DB migrations.
featured_image: '/images/demo/demo-square.jpg'
---

## Background

Today, I wanted to increase performance of a pytest test suite, which is running in the gitlab CI on 4 parallel workers.
I already discovered earlier that the load of these 4 workers is not evenly distributed. 
Thus, the straight-forward idea was to execute the tests in random order. Prior to this modification, tests that were located in the same directory were frequently run on the same worker.

The rapid appearance of numerous test faults caught me off guard. I eventually fixed them all, but it took more effort than I thought.

## Today I Learned

The test suite needs data from DB migrations. However, in a few tests people were using the [TransactionTestCase](https://docs.djangoproject.com/en/4.1/topics/testing/tools/#django.test.TransactionTestCase).
They were actually used indirectly through the [@pytest.mark.django_db](https://pytest-django.readthedocs.io/en/latest/helpers.html#pytest.mark.django_db) mark.

As explained [in the docs](https://docs.djangoproject.com/en/4.1/topics/testing/overview/#rollback-emulation), the `TransactionTestCase`, does **not** retain data from data migrations.
In our case, just using the `db` fixture in the tests (they did not really need transaction support) fixed some test failures.

