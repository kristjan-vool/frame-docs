Quickstart
=====

.. _installation:

Hello, World!
------------

Define core and frame objects.

.. code-block:: c++
   const FrameConfigSQL config = FrameConfigSQL("", "", "");
   const FrameTranslations translations = FrameTranslations(config);
   FrameDatabaseSQL database = FrameDatabaseSQL(config);
   FrameEmails emails = FrameEmails(config);
   FrameRouter router = FrameRouter(config, database, translations, emails);
   FrameRenderer renderer = FrameRenderer(config, translations, {});

Define first route

.. code-block:: c++
   void hello(const Request& request, Response& response) {
    response.send(renderer.render("hello.html", {{"name", "World"}}, nullptr));
   }

Define main

.. code-block:: c++
   int main(int arg_count, char* arg_values[]) {
    CoreServer server = CoreServer(router, 8000, 1);
    server.get("/", hello);
    server.start();
   }

Create template file ``hello.html``

.. code-block:: c++
   Hello, {{ name }}!
