# HtlMgmt

HtlMgmt is a complete hostel management system for requesting, allocating and vacating hostels built on sveltekit and strapi. This repository is the frontend endpoint for the application. Checkout [this repo](https://github.com/yogesh174/htl-mgmt-api) for the backend. 

## Develop

```bash
# clone the repo
git clone https://github.com/yogesh174/htl-mgmt.git

# install packages
yarn install

# start the application
yarn run dev --host
```

> Note: To change the backend endpoint, navigate to [`/src/lib/api.js`](https://github.com/yogesh174/htl-mgmt/blob/2a1f78214ccc084651ea38127aee55d9a5953b2e/src/lib/api.js#L1) and modify the `base` variable. 

## Demo

[Live deployment](https://htl-mgmt.vercel.app/)

> Note: It is slow and sometimes doesn't load. Just refresh if it doesn't load or throws some error.

## Similar applications

Checkout other applications similar to this one
- [Library management system](https://github.com/yogesh174/lib-sys)
- [Online tutorials system](https://github.com/yogesh174/vid-onl)
