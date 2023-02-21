[SSR | TanStack Query Docs](https://tanstack.com/query/latest/docs/react/guides/ssr)

React Query에는 서버에서 데이터를 prefetching하고 `queryClient`에 전달하는 방법이 두가지있다

1. 데이터를 직접 prefetch하고 `initialData`로 전달하는 방법
    - Next.js의 **`getStaticProps`** 또는 **`getServerSideProps`** 를 사용하여 fetch한 데이터를 useQuery의 `initialData` 옵션을 사용하여 데이터를 전달할 수 있다
    - 간단한 케이스일때 빠르게 설정가능
    - 고려해야하는 몇가지 단점이 있다
        1. useQuery를 사용하는 컴포넌트가 트리의 깊은 곳에 있다면 `initialData`를 전달해야함
        2. 여러 위치에서 같은 쿼리를 사용할때 모든 위치에 `initialData`를 전달해야함
        3. 서버에서 쿼리를 가져온 시간을 알 수 있는 방법이 없으므로 `dataUpdatedAt` 및 쿼리를 다시 가져올 필요가 있는지 여부는 페이지가 로드된 시간에 따라 결정된다
    
    ```jsx
    export async function getStaticProps() {
      const posts = await getPosts()
      return { props: { posts } }
    }
    function Posts(props) {
      const { data } = useQuery({
        queryKey: ['posts'],
        queryFn: getPosts,
        initialData: props.posts,
      })
    // ...
    }
    ```
    
2. 서버에서 쿼리를 prefetch하고, 캐시를 dehydrate 하고 클라이언트에서 다시 rehydrate 한다
    - 약간 더 많은 설정이 필요하다
    - 리액트 쿼리는 Next.js의 서버에서 여러 쿼리를 미리 가져온다음 queryClient에게 해당 쿼리를 dehydrating하는 기능을 지원한다
    - 이것은 서버가 페이지 로드 시 즉시 사용할 수 있는 마크업을 미리 렌더링할 수 있다는 것을 의미한다
    - js가 사용한 즉시 리액트 쿼리는 라이브러리의 전체 기능으로 이러한 쿼리를 업그레이드하거나 hydrate 할 수 있다.
    - 여기에는 서버에서 렌더링된 이후로 쿼리가 오래된 경우 클라이언트에서 쿼리를 다시 페치하는 작업이 포함된다
    
    - 서버에서 캐싱 쿼리를 지원하고 hydration을 설정하려면
        - 앱 내부와 인스턴스 ref(또는 리액트 상태)에 새로운 QueryClient를 생성한다
        - 이렇게 하면 서로 다른 사용자와 요청간에 데이터를 공유하지않고 컴포넌트 수명주기당 한번만 QueryClient를 생성할 수 있다.
        - 앱 컴포넌트를 <**QueryClientProvider>로 감싸고 client 인스턴스를 전달**
        - 앱 컴포넌트를<**Hydrate>로 감싸고 pageProps의 dehydratedState을 전달**
        
        ```tsx
        // _app.jsx
        import {
          Hydrate,
          QueryClient,
          QueryClientProvider,
        } from '@tanstack/react-query'
        
        export default function MyApp({ Component, pageProps }) {
          const [queryClient] = React.useState(() => new QueryClient())
        
          return (
            <QueryClientProvider client={queryClient}>
              <Hydrate state={pageProps.dehydratedState}>
                <Component {...pageProps} />
              </Hydrate>
            </QueryClientProvider>
          )
        }
        ```
        
        - 이제  `getStaticProps` (for SSG) 또는 `getServerSideProps` (for SSR) 를 사용해서 데이터를 prefetch할 준비가 되었다. React Query의 관점에서 이들은 동일한 방식으로 통합됩니다
        - **각 페이지 요청에 대해** 새 `QueryClient`인스턴스 를 만듭니다. **이렇게 하면 사용자와 요청 간에 데이터가 공유되지 않습니다.**
        - 클라이언트의 prefetchQuery 메서드를 사용해서 데이터를 Prefetch하고 완료될 때까지 기다린다
        - dehydrate를 사용하여 쿼리 캐시를 탈수하고 dehydratedState prop을 통해 페이지로 전달한다.이것은 _app.js에서 캐시가 선택될 것과 같은 prop이다
        
        ```tsx
        // pages/posts.jsx
        import { dehydrate, QueryClient, useQuery } from '@tanstack/react-query'
        export async function getStaticProps() {
          const queryClient = new QueryClient()
          await queryClient.prefetchQuery(['posts'], getPosts)
          return {
            props: {
              dehydratedState: dehydrate(queryClient),
            },
          }
        }
        function Posts() {
        // This useQuery could just as well happen in some deeper child to
        // the "Posts"-page, data will be available immediately either way
          const { data } = useQuery({ queryKey: ['posts'], queryFn: getPosts })
        // This query was not prefetched on the server and will not start
        // fetching until on the client, both patterns are fine to mix
          const { data: otherData } = useQuery({
            queryKey: ['posts-2'],
            queryFn: getPosts,
          })
        // ...
        }
        
        ```
        
        - 설명했듯이 일부 쿼리를 미리 가져오고 다른 쿼리는 queryClient에서 가져오도록 하는 것이 좋습니다. `prefetchQuery`즉 , 특정 쿼리에 대해 추가하거나 제거하여 콘텐츠 서버가 렌더링하는 내용을 제어할 수 있습니다.
        
        ****[Caveat for Next.js rewrites](https://tanstack.com/query/latest/docs/react/guides/ssr#caveat-for-nextjs-rewrites)****
        
        There's a catch if you're using [Next.js' rewrites feature](https://nextjs.org/docs/api-reference/next.config.js/rewrites) together with **[Automatic Static Optimization](https://nextjs.org/docs/advanced-features/automatic-static-optimization)** or **`getStaticProps`**: It will cause a second hydration by React Query. That's because **[Next.js needs to ensure that they parse the rewrites](https://nextjs.org/docs/api-reference/next.config.js/rewrites#rewrite-parameters)** on the client and collect any params after hydration so that they can be provided in **`router.query`**.
        
        The result is missing referential equality for all the hydration data, which for example triggers wherever your data is used as props of components or in the dependency array of `useEffect`s/`useMemo`s.
        
        
