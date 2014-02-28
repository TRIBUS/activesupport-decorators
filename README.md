ActiveSupportDecorators
=======================

The decorator pattern is particularly useful when extending constants in rails engines or vica versa.  To implement
the decorator pattern, you need to load the decorator after the original file has been loaded.  When you reference a
class in a Rails application, ActiveSupport will only load the first file it finds that matches the class name.  This
means that you will need to manually load the additional (decorator) file.  Usually you don't want to want to introduce
hard dependencies such as require statements.  You also don't want to preload a bunch of classes in a Rails initializer.
This is a tiny gem that provides you with a simple way to specify load dependencies.

Example
=======

Lets say your main rails application defines a model called Pet (in app/models/pet.rb):

```Ruby
class Pet < ActiveRecord::Base
end
```

Your rails engine adds the concept of pet owners to the application.  You can extend the Pet model in the engine with
the following model decorator (in my_engine/app/models/pet.rb).

```Ruby
class Pet
  belongs_to :owner
end
```

Now tell ActiveSupportDecorators to load any matching file in my_engine/app/models when a file is loaded from
app/models.  A convenient place to do this is in a Rails initializer in the engine:

```Ruby
module MyEngine
  module Rails
    class Engine < ::Rails::Engine
      initializer :decorator_dependencies do |app|
        ActiveSupportDecorators.add_dependency("#{app.root}/app/models", "#{config.root}/app/models")
      end
    end
  end
end
```

Note that you could specify the path as "/app" to allow decorating controllers, models, helpers, etc.

Debugging
=========

Need to know which decorator files are loaded?  Enable debug output:

```Ruby
ActiveSupportDecorators.debug = true
```