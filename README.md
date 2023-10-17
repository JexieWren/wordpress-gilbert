# Building a Modular React Admin Dashboard Using WordPress as a Headless CMS in Gilbert

When I began my journey as a web developer many years ago, creating custom admin interfaces felt like an insurmountable challenge. Crafting modular, reusable components appeared to be a skill reserved for seasoned engineers at larger agencies.

Fast forward to today, and as a frontend developer at [Hybrid Web Agency](https://hybridwebagency.com/), I've had the privilege of working on numerous admin dashboards powered by a headless CMS using React. The evolution of frameworks and tooling has made what once seemed out of reach accessible to a wider range of developers.

One thing I've noticed is that while tutorials cover the basics of setting up a React app to consume WordPress data, they often lack details on designing reusable, future-proof solutions. They also fall short when it comes to best practices for code organization, state management, and long-term feature expansion.

Having faced these challenges firsthand while working on client projects at Hybrid, I wanted to share a comprehensive tutorial on building a fully modular admin dashboard. The approach I'll demonstrate revolves around separating presentational and container components, creating custom hooks for data retrieval and side effects, and establishing a robust foundation for seamlessly integrating new admin modules.

Rather than merely connecting to a REST API, my goal is to equip you with the skills to develop an admin application that adheres to best practices for scalability, maintainability, and extensibility. By the end of this tutorial, you'll have a solid understanding of practical techniques for designing modular React applications powered by WordPress as a CMS. No more guesswork or reinventing the wheel with each new feature.

## Setting Up WordPress as a Headless CMS in Gilbert

The first step is to install WordPress. You can either download and extract the files to your local development environment or deploy WordPress through a hosting provider. In my case, I'll use MAMP on my local machine.

Once WordPress is installed, you need to enable the REST API functionality. This enables frontend applications to interact with WordPress data via RESTful endpoints. Navigate to "Plugins" > "Add New" and search for "REST API." Install and activate the REST API plugin.

Next, you'll set the core API permissions by adding the following code to your theme's `functions.php` file:

```php
function allow_rest_requests_from_any_origin() {
  remove_filter('rest_pre_serve_request', 'rest_send_cors_headers');
}
add_action('rest_api_init', 'allow_rest_requests_from_any_origin');
```

This code grants permission for requests from any domain, which is crucial during development when your React app resides on a different origin.

The next step involves registering custom post types so that your React app can retrieve and manage customized content types beyond WordPress's core content types like posts and pages. We'll create a custom post type called "Projects" using the following code:

```php 
function create_project_post_type() {

  register_post_type( 'project',
    array(
      'labels' => array(
        'name' => __( 'Projects' ),
      ),
    )
  );

}
add_action( 'init', 'create_project_post_type' );
```

### Developing the React Admin App

To initiate our React app, execute the following command in your terminal:

```shell
npx create-react-app admin-app
```

This command sets up a basic React project that serves as the foundation for our admin dashboard.

The first component we'll create is "Posts.js," which is responsible for fetching and displaying post data from WordPress. In this component, we'll import the `useEffect` hook and make a GET request to the `POSTS` endpoint:

```jsx
import { useEffect, useState } from 'react';

function Posts() {

  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch('/wp-json/wp/v2/posts')
      .then(res => res.json()) 
      .then(data => setPosts(data));
  }, []);

  return (
    <div>
      {posts.map post.map(post => (
        <Post key={post.id} post={post} />  
      ))}
    </div>
  )
}
```

This code snippet forms the basis of our WordPress-powered headless CMS setup, allowing us to render data in our React interface. In the following sections, we'll expand this application to support post creation and updating.

### Fetching Posts Data

The `useEffect` hook enables us to retrieve data from the API when a component is mounted. In this part, we import the `useEffect` hook and declare state variables for the `posts` array and a `loading` state:

```js
import { useEffect, useState } from 'react';

function Posts() {

  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // API call 
  }, [])
}
```

Within the `useEffect` function, we make a GET request to the WordPress REST API posts endpoint. Once the response is received, we parse the JSON data and store it

 in the component's state. This triggers a re-render with the updated data.

```js
fetch('/wp-json/wp/v2/posts')
  .then(res => res.json())
  .then(data => {
    setPosts(data);
    setLoading(false);
  });
```

### Displaying Posts

Now that we have obtained the posts data, our next step is to display it. We'll create a "Post" component to render each individual post. This component accepts the post as a prop and extracts relevant fields for rendering.

In addition, we'll design the overall layout and style using Material UI grid, cards, and typography components. This enhances the user interface, making it cleaner and more organized.

Within the `Posts.js` component, we map over the `posts` state array and render a `Post` component for each post. This results in a grid format that beautifully displays the title, excerpt, and other fields of each post.

### Adding Posts 

For handling the post creation form, we'll use the `useState` hook to track input values and errors in the component's state. We'll define an initial state object and associated setter functions.

The Formik library assists with validation and submission. Upon submission, it calls a `handleSubmit` function that triggers the API request to create a new post.

We'll also introduce error handling to display error messages if the API request fails. This provides a superior user experience compared to generic alerts.

### Routing and Layout

The React Router library is instrumental in defining routes and navigation for our single-page app. We'll initialize React Router and specify route paths for the primary pages.

Common user interface elements, such as headers, are extracted into reusable components like "Header.js" and "Sidebar.js." These components are rendered on every route, incorporating the concept of `props.children`.

Additionally, we'll conditionally display button links for adding new posts and navigating between sections using the React Router `Link` component. This seamlessly integrates all aspects into a polished admin interface.

## Embracing Modularity

With the basics of fetching, displaying, and managing posts in place, it's time to optimize our code by making it more modular and reusable. This optimization entails extracting shared logic and user interface components into custom hooks and components.

A great starting point for enhancing modularity is the data-fetching logic. While we currently have `useEffect` calls directly in our `Posts` component, this logic can be abstracted for reusability. Let's introduce a `useApiData` hook responsible for handling GET requests and returning the data:

```js
function useApiData(endpoint) {

  const [data, setData] = useState([]);

  useEffect(() => {
    fetch(endpoint)
      .then(res => res.json()) 
      .then(setData);
  }, [endpoint]);

  return data;

}
```

Now, in the `Posts` component, we call this custom hook and streamline the component's code:

```js 
function Posts() {

  const posts = useApiData('/posts');

  return (
    <PostList posts={posts} />
  )

}
```

This approach renders our data-fetching logic reusable across other components as well.

As we expand our admin dashboard to include additional features, we can segment these sections into logical feature modules. Each module should encapsulate related user interface elements and logic in a self-contained manner.

Let's introduce a "Profiles" section for managing site users. We'll create a `Profiles` module with its own route, and utilize the data-fetching hook:

```js
function Profiles() {

  const profiles = useApiData('/profiles');

  return (
    <ProfileList profiles={profiles} />
  );

}
```

By adopting this modular approach and developing reusable components, our codebase becomes significantly more organized and maintainable, especially as we introduce new features. Common utilities such as custom hooks prevent code duplication across the application.

This structure also enhances the customizability of our admin dashboard. Modules can be effortlessly included or excluded based on specific project requirements. Additional sections, such as settings, can follow the same pattern.

In summary, prioritizing modularity and the separation of concerns ensures that our admin dashboard can gracefully scale to meet evolving demands. This is particularly crucial for businesses like us at Hybrid Web Agency, offering custom [WordPress development services in Gilbert](https://hybridwebagency.com/gilbert-az/custom-wordpress-development-services/). When engaged in long-term website projects with clients, sustainability is paramount. A modular headless approach ensures that the admin and public-facing sites we build can adapt to changing needs without necessitating extensive refactoring.

## Conclusion

This tutorial has guided us through the process of designing a modular React admin dashboard powered by WordPress as a headless CMS. By decoupling the frontend from the backend, adhering to best practices for component structure and reusable logic, and emphasizing modularity, we've established a flexible foundation for further enhancing the admin interface.

While the fundamentals of rendering and managing WordPress content are straightforward, the true value lies in creating long-lasting solutions. Building applications using techniques like custom hooks, feature modules, and the separation of concerns allows sites to evolve sustainably over time without the need for extensive code rewrites.

This approach is especially valuable for organizations like Hybrid Web Agency, which offers custom [WordPress development services in Gilbert](https://hybridwebagency.com/gilbert-az/custom-wordpress-development-services/). When collaborating with clients on long-term website projects, the ability to adapt to changing needs is essential. Adopting a modular, headless approach backed by robust coding practices sets the stage for ongoing success.

## References

- [WordPress REST API Handbook](https://developer.wordpress.org/rest-api/): The official documentation for the WordPress REST API.
- [Learn React at the Official React Website](https://reactjs.org/): The primary resource for learning React, maintained by its creators.
- [React Router Documentation](https://reactrouter.com/docs/en/v6): Official React Router documentation for client-side routing.
- [WordPress CLI Documentation](https://developer.wordpress.org/cli/commands/): Documentation for the WP-CLI tool used to manage WordPress.
- [Get Started with GraphQL](https://graphql.org/learn/): An introductory guide to learning GraphQL, provided by the maintainers of GraphQL.
- [Apollo Client Documentation](https://www.apollographql.com/docs/react/): Documentation for the popular Apollo GraphQL client for React.
