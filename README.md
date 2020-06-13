# Rails Params and Strong Params
Any time we receive data from a request, whether it's a GET request with parameters parsed from its URL or parameters parsed from the body of a POST request, we have access to this data via the params method.

Example of URL containing parameters to be parsed by the receiver:
```
http://example.com?cats=yesplease&dogs=alrighttoo
```

## Params and GET/DELETE requests
Parameters avaialble with GET and DELETE requests are often used to determine which record/s to get from the database or which records to target for being destroyed. For example, we can use the paramters from a URL in a GET request that hits the index action on a controller to perform a search:
```
# http://example.com?q=not%20carrot%20cake

def index
  search_term = params[:q]
  @results = Result.search(search_term)
end

def destroy
  record = Record.find_by_id(params[:id])
  record.destroy
end
```

## Params and POST/PATCH requests
By default we can't pass params like so to instantiate or create new objects:
```
def create
  @cat = Cat.new(params)
  # more code
end
```

Doing so will cause an error. This is a security measure that helps protect the information in the database. Any user who has access to the front-end can use the DEV Tools to modify or add forms on a webpage. If the user adds fields to a form (or makes a fetch request or uses Postman, for example) in which the name attributes match the column names in the database, they have the ability to add or modify data which they are not supposed to have access to. For example, a user could add a field that maps to an administrative_access column, and set the value to one which provides them with a high-level of access to a website's resources.

This setting can be overridden: https://api.rubyonrails.org/classes/ActionController/Parameters.html

At all times, we can use params in our create/update actions if we list them out separately:
```
def create
  @cat = Cat.create(name: params[:cat][:name], params[:cat][:age])
  # more code
end
```

Using params in this way can get quite cumbersome and repetitive. This is where strong params come in!

## Strong Params for object creation (POST/PATCH)
Strong params allow us to whitelist which fields incoming data is allowed to update in the database.
```
def create
  @cat = Cat.new(cat_params)
  # more code
end

private

def cat_params
  params.require(:cat).permit(:name, :age)
end
```

This helps preserve the integrity of our data while writing less code / keeping it DRY. We can also choose how unwanted parameters are handled: should they be filtered out or should the object be invalidated? 

More info: https://api.rubyonrails.org/classes/ActionController/Parameters.html

### Explore
What is the return value of cat_params above? How could you find out?
