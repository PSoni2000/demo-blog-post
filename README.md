# About Project App

## Language / Library Used

1. React JS
2. react-router-dom

## Future Plans

# NOTES

## Lazy Loading - Load code only when it's needed

Which means that we wanna load certain peaces of code only when they are needed.

So for example, when we visit this website and we load on the homepage we don't need the blog page code yet. We will only need it later once we navigate there. and at that point of time, once we navigate there. it should be downloaded. thats exactly what lazy loading will do for us.

So, in order to load blog page lazily -

1. remove import
   ```
   import BlogPage, { loader as postsLoader } from './pages/Blog';
   ```
   otherwise it's always loaded.
2. We have to re-add it but in a way that it's only loaded when it's needed.

   **To Load functions lazily -**

   ```
   // old way
   // loader: postsLoader,

   // lazy loading
   loader: () =>
             import('./pages/Blog')
               .then((module) => module.loader()),
   ```

   here this import() is same import we use for loading a file. but it turns out that you can actually also call import as a function and in that case it will import something dynamically, only when it's needed.

   Note = that loader function `module.loader()` should be executed. as we know it will return a promise which yield a response.

   now only when we try to visit the blog page. only then the blog file will be imported and this loader function from that file will be executed.

   **To Load components lazily -**

   Loading components lazily is similar to loading functions lazily with just a small change

   the reason why we cannot load components lazily similar to functions is that

   ```
   const BlogPage = () => import('./pages/Blog');
   ```

   is not a valid component. A function is only a valid component only when its returning JSX code or something like that.

   but this function `() => import('./pages/Blog');` here is returning a promise.
   To solve this problem React give us a special function, which we have to wrap around this function.

   ```
   import { lazy } from 'react';
   ```

   lazy is executed and takes this function, with the dynamic import as an argument. and now blog page can indeed be used as a component.

   ```
   // old way
   // import BlogPage, { loader as postsLoader } from './pages/Blog';

   // lazy loading
   const BlogPage = lazy(() => import('./pages/Blog'));
   ```

3. Now the code will work again but it will take some time to load the code for this component because that code has to be downloaded after all. and the effort you must wrap this with suspense component. (suspense component - wait for content to be loaded before actually rendering the content)

   ```
   import { lazy, Suspense } from 'react';

   ...

   {
      path: ':id',
      element: (
        <Suspense fallback={<p>Loading...</p>}>
          <PostPage />
        </Suspense>
      ),
      loader: (meta) =>
        import('./pages/Post').then((module) => module.loader(meta)),
   },
   ```
