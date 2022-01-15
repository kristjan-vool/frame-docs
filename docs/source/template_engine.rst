Template Engine
=====

Variables
------------

Variables are rendered within the ``{{ ... }}`` expressions.

**Renderer:**

.. code-block:: C++

  return renderer.render("variables.html", {
    "variable": "value",
    "array": [10, 100, 1000],
    "object": {
      "example": "nested"
    }
  }, language);

**Template file:** ``variables.html``

.. code-block:: mustache

  <!-- Regular -->
  {{ variable }}
  
  <!-- Array index -->
  {{ array[1] }}
  
  <!-- Array index backwards -->
  {{ array[-1] }}
  
  <!-- Nested object -->
  {{ object["example"] }}
  
**Output:**

.. code-block:: html
  
  value
  
  100
  
  1000
  
  nested
  
  
Loops
------------

Loops are also rendered within the ``{{ ... }}`` expressions.

**Renderer:**

.. code-block:: C++

  return renderer.render("loops.html", {
    "examples": [0, 10, 100, 1000, 10000]
  }, language);

**Template file:** ``loops.html``

.. code-block:: mustache

  <!-- Loop all examples -->
  {{ for one_example in examples: {{ one_example }} }}
  
  <!-- Example from specific indexes -->
  {{ examples[0] }}
  {{ examples[-1] }}
  
**Output:**

.. code-block:: html
  
  0 10 100 1000 10000
  
  0
  10000
  
Conditions
------------

Conditions are also rendered within the ``{{ ... }}`` expressions. Condition support ``==`` and ``!=`` for strings and booleans and including ``>=``, ``>``, ``<=``, ``<`` for numbers.

Shorthand ``?:`` operator is also supported and you can even make conditions without any operators.

**Renderer:**

.. code-block:: C++

  return renderer.render("conditions.html", {
    "value_string": "a",
    "value_number": 10,
    "value_boolean: true
  }, language);

**Template file:** ``conditions.html``

.. code-block:: mustache

  {{ if value_string == "a": yes }}
  {{ if value_number == 10: yes }}
  {{ if value_boolean == true: yes }}
  
  {{ if value_exists: no }}
  {{ value_exists ? "yes" : "no" }}
  {{ value_string ?: "no" }}
  {{ value_number > 100 ? "bigger" : "smaller" }}
  
**Output:**

.. code-block:: html
  
  yes
  yes
  yes
  
  no
  no
  a
  smaller
  
Dynamic JSON support
------------

Dynamic JSON can be created and inserted from inside of the template files that gets merged with the original JSON data. Inserted JSON is preferred over original data.

**Renderer:**

.. code-block:: C++

  return renderer.render("dynamic.html", {
    "example": "a"
  }, language);

**Template file:** ``dynamic.html``

.. code-block:: mustache

  {{ user_method({
    "example": "b"
  }) }}
  
**Template file:** ``user_method.html``

.. code-block:: mustache

  {{ example }}
  
**Output:**

.. code-block:: html
  
  b
  
Custom template engine user methods
------------

Defining custom methods that can be used inside the template engine is made easy. All you need to do is extend the ``FrameRenderer`` with your own class and use it for the rendering.

Inside the extended Renderer class you need to define the custom method that you want to use inside the template engine and insert it into the map of available methods.

**custom_renderer.hpp**

.. code-block:: C++

  #ifndef CUSTOM_RENDERER_HPP
  #define CUSTOM_RENDERER_HPP

  #include <frame/render/renderer.hpp>

  class Renderer : public FrameRenderer {
    public:
      Renderer(const FrameConfig &config, const FrameTranslations &translations, const nlohmann::json &data);

    private:
      std::string user_defined_method(const std::string &string, const FrameLanguage *const language);
  };


  #endif
  
**custom_renderer.cpp**

.. code-block:: C++

  #include <client/render/renderer.hpp>

  Renderer::Renderer(const FrameConfig &config, const FrameTranslations &translations, const nlohmann::json &data): FrameRenderer(config, translations, data) {
    functions["user_defined_method"] = [this](const std::string &string, const FrameLanguage *const language) { return user_defined_method(string, language); };
  }

  std::string Renderer::user_defined_method(const std::string &string, const FrameLanguage *const language) {
    const nlohmann::json data = nlohmann::json::parse(string);
    return "User defined method: " + data["custom"];
  }
  

**Renderer:**

.. code-block:: C++

  return renderer.render("user_defined.html", {}, language);

**Template file:** ``user_defined.html``

.. code-block:: mustache

  {{ user_defined_method({
    "custom": "Any value"
  }) }}
  
**Output:**

.. code-block:: html
  
  User defined method: Any value
