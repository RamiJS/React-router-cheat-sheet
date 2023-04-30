# React Router

## Installation

```
npm install react-router-dom
```



## Setting up the routes file

We create a file, we name it to ```routes.tsx```. We then import ```createBrowserRouter``` from ```react-router-dom```.

To define routes, we use the ```createBrowserRouter``` function, we pass in an array of objects that contain routes info.

```react
createBrowserRouter([
{path: '/', element: <HomeComponent />}
])
```

The object takes two properties, ```path``` and ```element```. 

Now in ```main.tsx``` file, we import ```RouterProvider``` from ```React-router-dom```, and we replace ```<App />``` component with ```<RouterProvicder />```. but we still have to do one more thing, we must pass a ```router``` property that includes our ```router.tsx``` file we created early. 

```react
<RouterProvider router={router} />
```



## Prevent full page reload when navigating

We must use ```<Link to"/path" />``` instead of default anchor tags. 



## How to navigate programmatically 

Sometimes we might want to navigate to another page depending on a certain action, like after submitting a form. To do that, we need to use ```useNavigate``` hook.

```react
import { useNavigate } from 'react-router-dom'
const navigate = useNavigate();
```

All we have to do after is, pass the link inside the ```navigate()``` function.

```react
<button
onClick={() => navigate('/path')}
>Click</button>
```



## How to pass data with route parameters

Sometimes we might want to pass data to our routes, like ID for example. when we click on an item, we want to show the details for that item in a nested route. to achieve that, we firstly need to set ```/:id``` when we define our route's path.

```react
{ path: '/items/:id'}, element: <SingleItemPage /> }
```

Then, we pass the id this way:

```react
<Link to={`/items/${item.id}`}>{item.name}</Link>
```



## Get Information about current Route

If we ever need to know what page we are on currently, or which parameter values are being passed, or what query string's are used We could use multiple hooks that **React router dom** has. Lets start with:

- useParams: We use this hook to access the current parameters that are being passed to our url.
- useSearchParams: We use this hook to access the current query strings that are being passed to our url. the ```useSearchParams``` returns us two items, a value and a setter. We may only use the setter inside event handlers or useEffect hook.
- useLocation: We use this hook to retrieve information about our current route. it returns us pathname, search (query strings) and state.



## Nested Routes

To properly pass nested routes, we may treat them as children of a parent route. to do that, we must use ```<Outlet />``` in the parent component. the ```<Outlet />``` component is like a placeholder for our child routes.

Now in our routes file we we defined all routes. we could pass a ```children``` array property to our route.

```react
{ 
path: '/',
element: <Main />,
children: [
    { path: '', element: <HomePage />},
    { path: 'items', element: <ItemsPage />},
    { path: 'items/:id', element: <SingleItemPage />},
]
}
```

note that instead of passing an empty string to our home page child route, we could replace ```path: ' '``` with ```index: true```.

Notice how in all paths we defined, they didn't start with a ```/```. That's because they are child routers. But, if we ever wanted to use ```<Link to="" />``` to pass paths, we need to start with ```/``` anyways. 

```react
<Link to="/items">Items</Link>
```

 

## Styling Active Links

So far, we have been using ```<Link>``` tags to pass our URLs. There is another tag called ```<NavLink>``` that will automatically set the ```active``` class to the selected route. 

But what if we wanted to pass a custom class to the selected route? We can achieve that by extracting the ```isActive``` property. Example:

```react
className={ ({ isActive }) => isActive ? 'custom class' : '' }
```



## Handling non-existing routes

What if user tried to go to a route that doesn't exist in our application? 

By default, React Router will show a ```404 not found``` page. But we could replace it with a customized one. to achieve that we will: 

- create an ```ErrorPage.tsx``` file.

-   Set ```errorElement``` property to our Parent Route

  - ```react
    {
        path: '/',
        element: 'Home /',
        errorElement: '<ErrorPage />'
    }
    ```



The benefit of this ```errorElement``` property is that whenever something goes wrong in our application, it will redirect the user to the error page.

Now to log the error, we will use another hook provided by React Router called ```useRouteError```

```react
import { useRouteError } from 'react-router-dom'
const error = useRouteError();
```

To show a specific message in the ErrorPage to the user when he enters a wrong/non-existing route, We could use ```isRouteErrorResponse``` property that is also provided by React Router.

```react
return (
	{ 
        isRouteErrorResponse(error) ? <p>Page does not exist</p> : <p>Something 		Went Wrong</p>  
	}
)
```



## Private Routes

If we want to restrict un-authenticated users from accessing certain routes, We could check if user is logged in or not. If not, we could redirect the user to the login page.

```react
if(!user)
	return <Navigate to="/login" />
```

Note that we are using ```<Navigate to="/" />``` Here instead of ```useNavigation``` that we talked about earlier. That's because the``` useNavigation``` function has a side effect, which means the page will be loaded first, and then execute our function.

This solution is not scalable, because for every route we want to protect, we need to repeat this approach. Which is not recommended.

There is a better way to do this, we could create a ```PrivateRoutes.tsx``` component, and apply our previous logic to it. then, in the ```routes.tsx``` file, we could create a new route object, set it's element to our ```<PrivateRoutes.tsx />``` element, and put all private routes in it's children properties.

```react
{
	element: <PrivateRoutes />,
	children:
	[
		// All private routes
	]
}
```



our ```PrivateRoutes.tsx``` file should look this way :

```react
import { Navigate, Outlet } from 'react-router-dom'

const PrivateRoutes = () => {
if(!user)
	return <Navigate to="/login" />
return <Outlet />	
}
export default PrivateRoutes
```
