
The HTTP api
====================================

The HTTP api exists to make it easy for non-python projects to use rasa NLU, and to make it trivial for projects currently using {wit,LUIS,api}.ai to try it out.

Emulation
-------------------------
rasa NLU can 'emulate' any of these three services by making the ``/parse`` endpoint compatible with your existing code.
For example, if you would normally send your text to be parsed to LUIS, you would make a ``GET`` request to

``https://api.projectoxford.ai/luis/v2.0/apps/<app-id>?subscription-key=<key>&q=hello%20there&verbose=true``

in luis emulation mode you can call rasa by just sending this request to 

``http://localhost:5000/parse?q=hello%20there``

any extra query params are ignored by rasa, so you can safely send them along. 


Endpoints
-------------------------

``POST /parse`` (no emulation)
^^^^^^^^^^^^^^^^^^^^^^^^^

you must POST data in this format ``'{"text":"<your text to parse>"}'``, you can do this with

.. code-block:: console

    $ curl -XPOST localhost:5000/parse -d '{"text":"<your text to parse>"}'


``POST /train``
^^^^^^^^^^^^^^^^^^^^^^^^^

you can post your training data to this endpoint to train a new model. 
this starts a separate process which you can monitor with the ``/status`` endpoint. 

.. code-block:: console
    $ curl -XPOST localhost:5000/train -d @data/demo-restaurants.json


``GET /status``
^^^^^^^^^^^^^^^^^^^^^^^^^

this checks if there is currently a training process running (you can only run one at a time).
also returns a list of available models the server can use to fulfill ``/parse`` requests.

.. code-block:: console
    $ curl localhost:5000/status | python -mjson.tool
    {
      "training" : False
      "models" : []
    }



Authorization
-------------------------
To protect your server, you can specify a token in your rasa NLU configuration, which must be passed as a query parameter in all requests, e.g. :

.. code-block:: console

    $ curl localhost:5000/status?token=12345
