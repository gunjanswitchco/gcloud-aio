Asyncio Python Client for Google Cloud Pub/Sub
==============================================

|pypi| |pythons|

Installation
------------

.. code-block:: console

    $ pip install --upgrade gcloud-aio-pubsub

Usage
-----

This Pub/Sub implementation is a slim wrapper around
``google-cloud-pubsub==0.25.0`` with a few key features:

- intermittent gRPC communication errors are (optionally) captured
- an optionally infinite async subscription poller has been introduced

The upstream pubsub implementation has been pinned to 0.25 since newer versions
can cause odd errors when combined with ``asyncio`` processing. At the time of
this writing, a new version of the upstream pubsub implementation is being
generated -- once we've tested that version, will update this library.

Here's the rough usage pattern for subscribing:

.. code-block:: python

    import gcloud.aio.pubsub as pubsub

    subscriber = pubsub.Client(project)
    topic = subscriber.topic(topic)
    subscription = topic.subscription(subscription)

    async for job_id, message in subscription.poll():
        # do something with message
        await subscription.acknowledge([job_id])

A couple additional features you might want to play with:

.. code-block:: python

    # creates a subscription/topic if it does not exist
    subscription.create_if_missing()
    topic.create_if_missing()

    # the retries argument can be used to retry acks when intermittent gRPC
    # errors are received
    await subscription.acknowledge([job_ids], retries=3)

    # the max_intermittent_errors argument can be used to retry pulls when
    # intermittent gRPC errors are received
    # setting max_intermittent_errors=None allows any number of these errors
    async for job_id, message in subscription.poll(max_intermittent_errors=3):
        ...

    # similarly, the max_errors argument can be used to retry pulls when
    # any non-intermittent error is generated by the pull command
    # setting max_errors=None allows any number of these errors
    async for job_id, message in subscription.poll(max_errors=3):
        ...

Contributing
------------

Please see our `contributing guide`_.

.. _contributing guide: https://github.com/talkiq/gcloud-aio/blob/master/.github/CONTRIBUTING.rst

.. |pypi| image:: https://img.shields.io/pypi/v/gcloud-aio-pubsub.svg?style=flat-square
    :alt: Latest PyPI Version
    :target: https://pypi.org/project/gcloud-aio-pubsub/

.. |pythons| image:: https://img.shields.io/pypi/pyversions/gcloud-aio-pubsub.svg?style=flat-square
    :alt: Python Version Support
    :target: https://pypi.org/project/gcloud-aio-pubsub/
