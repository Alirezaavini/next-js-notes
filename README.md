# Next JS


### How to install 
```
npx create-next-app@latest
```

### Adding meta data
``` tsx
export const metadata = {
    title: {
        template: '%s / The Wild Oasis',
        default: 'Welcome / The Wild Oasis',
    },
    description: '',
};
```

### Importing google fonts

``` tsx
import { Josefin_Sans } from 'next/font/google';

const josefin = Josefin_Sans({
    subsets: ['latin'],
    display: 'swap',
});

<div className={`flex flex-col bg-primary-950 text-primary-100 min-h-screen ${josefin.className}`}>
</div>

```

### How to show loading element

1. make a component for loading in the root you want
``` tsx
function Loading() {
    return <p>Loading data</p>;
}

export default Loading;
```

> [!TIP]
> If you do it in root folder it will apply for all your components

## Images in Next JS
### First Way:
``` tsx
import Image from 'next/image';

<Image src="/logo.png" height="60" width="60" alt="The Wild Oasis logo" />
```

### Second Way:
``` tsx
import logo from '@/public/logo.png';

 <Image
     src={bg}
     fill
     placeholder="blur"
     quality={80}
     className="object-top object-cover"
 />
```

> [!TIP]
> You can use quality

### Tric for images without size
``` tsx
   <div className="col-span-2 relative aspect-square">
        <Image fill src="/about-2.jpg" className="object-cover" alt="alt" />
   </div>

```

## Nested Layout

```
function Layout({ children }: { children: ReactNode }) {
    return (
        <div>
            // layout contetn
            <div>{children}</div>
        </div>
    );
}
```

## Data Fetching
```tsx
import { getCabins } from '../_lib/data-service';
const cabins: any = await getCabins();


export const getCabins = async function () {
  const agent = new https.Agent({
    rejectUnauthorized: false, // Allows self-signed certificates
  });
  const res = await fetch(`${API_URL}/get-cabins`, { agent });
  return res.json();
};

```

## Suspense

### Parent Component


```tsx
<Suspense fallback={<Spinner />}>
      <CabinList />
</Suspense>

```
### CabinList Component
```tsx
import { getCabins } from '../_lib/data-service';
import CabinCard from './CabinCard';

async function CabinList() {
    const cabins: any = await getCabins();

    if (!cabins.length) return <></>;

    return (
        <>
            {cabins.length > 0 && (
                <div className="grid sm:grid-cols-1 md:grid-cols-2 gap-8 lg:gap-12 xl:gap-14">
                    {cabins.map((cabin: any) => (
                        <CabinCard cabin={cabin} key={cabin.id} />
                    ))}
                </div>
            )}
        </>
    );
}

export default CabinList;
```

## Dynamic Routes
1. Make a folder with [id] template 

![dynamic_route](assets/images/dynamic-route-1.jpg)

2. Link to it like bellow
   ```tsx
    <Link
         href={`/cabins/${id}`}>
         Details & reservation &rarr;
    </Link>
   ```
3. Get Params
```tsx
export default function Page({ params }: { params: any }) {
 const cabin = await getCabin(params.cabinId);
}
```
## Dynamic Metadata
```tsx
import { getCabin } from '@/app/_lib/data-service';

export async function generateMetadata({ params }: { params: any }) {
    const { name } = await getCabin(params.cabinId);
    return { title: `Cabin ${name}` };
}

export default async function Page({ params }: { params: any }) {

```
## Error Boundary

### Client Errors

1. Create error page in app route like bellow

```tsx
'use client';

export default function Error({ error, reset }: { error: any; reset: any }) {
    console.log('error =====> ', error);

    return (
        <main className="flex justify-center items-center flex-col gap-6">
            <h1 className="text-3xl font-semibold">Something went wrong!</h1>
            <p className="text-lg">{error.message}</p>

            <button className="inline-block bg-accent-500 text-primary-800 px-6 py-3 text-lg" onClick={reset}>
                Try again
            </button>
        </main>
    );
}

```

### NotFound Error
1. Create not found page in app route like bellow
```tsx
import Link from 'next/link';

function NotFound() {
    return (
        <main className="text-center space-y-6 mt-4">
            <h1 className="text-3xl font-semibold">This page could not be found :(</h1>
            <Link href="/" className="inline-block bg-accent-500 text-primary-800 px-6 py-3 text-lg">
                Go back home
            </Link>
        </main>
    );
}

export default NotFound;

```

### Handle NotFound Error Manaully

```tsx
try {
const res = await fetch(`${API_URL}/get-cabin?id=${id}`, { agent });

// Check if the HTTP response is successful (status 200-299)
if (!res.ok) {
  throw new Error(`HTTP error! Status: ${res.status}`);
 }

// Parse the JSON response
const data = await res.json();
if (!data) notFound();
return data;
} catch (error) {
    // Handle errors here (e.g., log, rethrow, etc.)
    console.error("Error fetching data:", error.message);
    throw error; // Optional: Propagate the error up
  }
```

## Dynamic And Static Pages

### Make Dynamic Pages Static
```tsx

export async function generateStaticParams() {
    const cabins = await getCabins();
    const ids = cabins.map((cabin: { id: number }) => ({
        cabinId: String(cabin.id),
    }));
    return ids;
}

export default async function Page({ params }: { params: any }) {
```

### Static Site Generation (SSG)

1. Edit next.config.mjs
```mjs
output: "export"
```
```mjs
const nextConfig = {      
      output: "export"
};

```
2. Build 
```npm
 npm run build
```

## Partial Pre-Rendering (PPR)
> A whole new way of rendering

> Mix of static and dynamic

> [!WARNING]
> It's not ready yet in next js documentation

## Caching in Next.js

![caching_in_next_js](assets/images/caching-1.jpg)

![caching_in_next_js_2](assets/images/caching-2.jpg)

### Force To Be Dynamic

```tsx
export const revalidate = 0; // turn off caching

/* eslint-disable @typescript-eslint/no-explicit-any */
export const metadata = {
    title: 'Cabins',
};

export default async function Cabins() {
```

### ISR 

For once per hour
```tsx
export const revalidate = 3600; // turn off caching in seconds
```

### Cmponent Level Caching
```tsx
import { unstable_noStore as noStore } from 'next/cache';

async function CabinList() {
    noStore();

```

## Client And Server Interactions

![caching_in_next_js](assets/images/server-client-boundary-1.jpg)

### Importing And Rendering Rules:

![caching_in_next_js](assets/images/server-client-boundary-2.jpg)

> [!IMPORTANT]
> Look C component, somewhere it's **Server Component** and other way it's **Client Component**

## Url Params

### Get Url Params

```tsx
export default async function Cabins({ searchParams }: { searchParams: any }) {
    console.log(searchParams);
    const filter = searchParams?.capacity ?? 'all';
```

### Change Url 

```tsx
    const searchParams = useSearchParams();
    const router = useRouter();
    const pathName = usePathname();

    function handleFilter(filter: string) {            
        const params = new URLSearchParams(searchParams);
        params.set('capacity', filter);
        router.replace(`${pathName}?${params.toString()}`, { scroll: false });
    }
```

## Client And Server Interactoins

### Multi Fetch Request In One Page
```tsx
 const settings = await getSettings();
 const bookedDates = await getBookedDatesByCabinId(params.cabinId);
```

<b> We can use Promise.all </b>

```tsx
const [cabin, settings, bookedDates] = await Promise.all([
        getCabin(params.cabinId),
        getSettings(),
        getBookedDatesByCabinId(params.cabinId),
    ]);

```
<b> Better way is make some other components </b>
```tsx
 <Suspense fallback={<Spinner />}>
         <Reservation cabin={cabin} />
 </Suspense>
```

```tsx
import { getBookedDatesByCabinId, getSettings } from '../_lib/data-service';
import DateSelector from './DateSelector';
import ReservationForm from './ReservationForm';

async function Reservation({ cabin }: { cabin: any }) {
    const [settings, bookedDates] = await Promise.all([getSettings(), getBookedDatesByCabinId(cabin.id)]);

    return (
        <div className="grid grid-cols-2 border border-x-primary-800 min-h-[400px] mb-10">
            <DateSelector settings={settings} bookedDates={bookedDates} cabin={cabin} />
            <ReservationForm cabin={cabin} />
        </div>
    );
}

export default Reservation;
```

## Using Context API For State Management

1. Create Context:

```tsx
'use client';

import { createContext, ReactNode, useContext, useState } from 'react';

const initialState = { from: undefined, to: undefined };

const ReservationContext = createContext({});

function ReservationProvider({ children }: { children: ReactNode }) {
    const [range, setRange] = useState(initialState);
    const resetRange = () => setRange(initialState);
    return <ReservationContext.Provider value={{ range, setRange, resetRange }}>{children}</ReservationContext.Provider>;
}

function useReservation() {
    const context = useContext(ReservationContext);

    if (context === undefined) throw new Error('');
    return context;
}

export { ReservationProvider, useReservation };

```
2. Use Context Provider
```tsx
import { ReservationProvider } from './_components/ReservationContext';


export default function RootLayout({ children }: { children: React.ReactNode }) {
    return (
        <html lang="en">
            <body className={`relative flex flex-col bg-primary-950 text-primary-100 min-h-screen ${josefin.className}`}>
                <Header />
                <div className="flex-1 px-8 py-12 grid">
                    <main className="max-w-7xl mx-auto w-full">
                        <ReservationProvider>{children}</ReservationProvider>
                    </main>
                </div>
            </body>
        </html>
    );
}
```

3. Use Context
```tsx
const { range, resetRange } = useReservation();
```

## Next Auth

1. Make a config file in _lib folder

> This sample is about google

```tsx
import NextAuth from "next-auth"
import Google from "next-auth/providers/google"

const authConfig = {
      providers: [
            Google({
                  clientId: process.env.AUTH_GOOGLE_ID,Client ID
                  clientSecret: process.env.AUTH_GOOGLE_SECRET
            }),
      ],
}
export const { auth, handlers: { GET, POST } } = NextAuth(authConfig);
```
2. Make a file in api/auth/[...nextauth] folder with foute.ts name

```tsx
export { GET, POST } from '@/app/_lib/auth';
```

3. Get google id from google cloud console
[https://console.cloud.google.com/cloud-resource-manager](https://console.cloud.google.com/apis/dashboard?)

Client secret = AUTH_GOOGLE_SECRET

4. Add varibales in .env file

```env
NEXTAUTH_URL=http://localhost:3000/
NEXTAUTH_SECRET="any key"
AUTH_GOOGLE_ID="Client ID"
AUTH_GOOGLE_SECRET="Client secret"
```

### Protect Routes With Next Auth Middleware

1. Create a middleware in route folder (not in app folder)

```ts

import { auth } from '@/app/_lib/auth';

export const middleware = auth;

export const config = {
    matcher: ['/account'],
};
```

2. Modigy authConfig to customize sign in page and callbacks

```js
const authConfig = {
      providers: [
            Google({
                  clientId: process.env.AUTH_GOOGLE_ID,
                  clientSecret: process.env.AUTH_GOOGLE_SECRET
            }),

      ],
      callbacks: {
            authorized({ auth, request }) { //when user goes to matcher routes which we set in middleware.ts it will be executed
                  //auth : current session
                  return !!auth?.user;
            }
      },
      pages: {
            signIn: "/login"
      }
}

export const { auth,
      signIn,
      signOut,
      handlers: { GET, POST } } = NextAuth(authConfig);
```js

3. Create action.ts to customize sign in


```ts
'use server';
import { signIn } from './auth';

export async function signInAction() {
    await signIn('google', { redirectTo: '/account' }); //one of the provider
}
```
4. Use SignInAction

```tsx
import { signInAction } from '../_lib/actions';

function SignInButton() {
    return (
        <form action={signInAction}>
            <button className="flex items-center gap-6 text-lg border border-primary-300 px-10 py-4 font-medium">
                <img src="https://authjs.dev/img/providers/google.svg" alt="Google logo" height="24" width="24" />
                <span>Continue with Google</span>
            </button>
        </form>
    );
}

export default SignInButton;
```

### Create User When Users SignIn In Next Auth 

1. Add signIn Callback:
```js
 callbacks: {
            authorized({ auth, request }) {                  
                  return !!auth?.user;
            },
           async signIn({ user, account, profile }) {
                  try {
                        const existingGuest = await getGuest(user.email);
                        if (!existingGuest) {
                              await createGuest({ email: user.email, fullName: user.name })
                        } else {

                        }

                        return true;
                  } catch {
                        return false
                  }
            },
      },
```

### Add More info to session
For doing that modeify auth.js and add new callback:
```js
async session(session, user) {
                  const guest = getGuest(session.user.email);
                  session.user.guestId = guest.id;
                  return session;
            }            
```

## Server Actions

> [!TIP]
> Server actions will make endpoint automaticly

![dynamic_route](assets/images/server%20actions%20-1.jpg)

![dynamic_route](assets/images/server%20actions%20-2.jpg)

![dynamic_route](assets/images/server%20actions%20-3.jpg)

### Clear Catch Manually

```ts
 revalidatePath('/account');
```

### UseFormtatus

<b>Only works inside of a form</b>

### Use Transition Hook

1. Define Transition
```ts
 const [isPending, startTransition] = useTransition();
```
2. Start Transition
```ts
 startTransition(() => deleteReservation(bookingId))
```
3. Use isPending
```tsx
 {!isPending ?
        <>
          <TrashIcon className='h-5 w-5 text-primary-600 group-hover:text-primary-800 transition-colors' />
          <span className='mt-1'>Delete</span>
        </>
        : <span className='mx-auto'><SpinnerMini /></span>
}
```

## Optimistic UI

```tsx
import { useOptimistic } from 'react';

const [optimisticBookings, optimisticDelete] = useOptimistic(bookings, (currentBookings, bookingId) => {
     return currentBookings.filter((b: any) => b.id !== bookingId);
});

optimisticDelete(bookingId);
```
