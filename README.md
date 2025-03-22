# Next JS
 This patch all is about tricks and notes about next js.

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

