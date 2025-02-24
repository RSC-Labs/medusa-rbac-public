# Medusa RBAC

## What is it?

Medusa RBAC provides a framework for Role-Based Access Control in Medusa.

Medusa itself does not provide ability to properly control the access to different functionalities, and also do not expose any API or extensible parts, but this plugin tries to enable such control as much as possible.

Interested? Go to: [What I need to do to have it?](#i-am-interested-what-i-need-to-do). It is a commercial software so it requires a licence to be used.

This repository has been created for instruction purposes and as a place for raising bugs and improvements.

## Found a bug or looking for an improvement?

Please raise an issue in Github issues.

## Installation

1. Install plugin by adding to your `package.json`:

```json
...
"@rsc-labs/medusa-rbac": "1.3.0" // or other available version
...
```
and execute install, e.g. `yarn install`.

2. Add plugin to your `medusa-config.js` with the licence key, which you received:

```js
...
plugins: [
    {
      resolve: "@rsc-labs/medusa-rbac",
      options: {
        licenceKey: <licence-key>
      }
    }
]
...
```

3. Due to: https://github.com/medusajs/medusa/issues/11248 you need also add following configuration to `medusa-config.js`:
```js
admin: {
  vite: () => {
    return {
      optimizeDeps: {
        include: ["@emotion/react", "@mui/material"]
      },
    };
  },
},
projectConfig: {
  ...
}
```

### Database migration

Medusa RBAC introduces new models in database. To have it working, you need to firstly execute migrations:
```bash
npx medusa db:migrate
```

### Seed database

Medusa RBAC comes with predefined permissions - it is recommended to execute a script firstly:
```bash
npx medusa exec node_modules/@rsc-labs/medusa-rbac/.medusa/server/src/scripts/seed-rbac.js
```

## Architecture

Medusa RBAC contains four, main models:

### Permission

Permission describes an action. For instance the permission can be `read /admin/products`. It contains kind of action (like `read`) and target (like `/admin/products`). There are three types of permissions:
- read
- write
- delete

At this moment the target can be only an API path (like `/admin/products`), but it will be extended in the future.

#### Category

Category is a separated model which might contain many permissions. For instance, category `Products` can contain following permissions:
- `read /admin/products` 
- `write /admin/products` 
- `delete /admin/products` 

Category model has been created to simplify the configuration, so you can work on categories, instead of working on single permissions.

### Policy

Policy describes decision of the action. For instance, if the permission is `read /admin/products`, policy makes relation of this permission to decision - for instance `ALLOW read /admin/products`.
Policies cannot be created directly, they are always part of the role and defined when the role is being defined.

Policy can be also assigned to the permission **Category**, however it just an abstraction - it is anyway modelled per permission.

### Member

Member is just the user - the different naming comes from the fact that in the future it can be extended.

### Role

Role describes mapping of policies to the member (user). For instance, the role describes that `user@medusa.com` has the policies 
- `ALLOW read /admin/products`
- `DENY write /admin/products`

Based on the above, the user which has assigned such role is allowed to read products, but not allowed to create such ones.

## Assumptions

### Default role

At this moment every member (user) does not have assigned default role. In `Roles` tab it is shown as `Unassigned`. 

`Unassigned` means that everything is allowed by default (like in Medusa without RBAC). This will be improved in the future, but now it is done like this to prevent situation when noone is allowed to assign a role.

### Kinds of permissions

Permissions have two kinds - `Predefined` and `Custom`. The main difference between them is that `Predefined` are built-in permissions created for the most common use cases. They cannot be modified or deleted. They are seed by executing `seed-rbac` script.

`Custom` permissions are the one which you are creating. They can be modified or deleted. They can be assigned also to custom categories.

The purpose of `Custom` permissions are mainly for your extensions - if you have your own API endpoints, you can easily create new permission which will cover your API endpoint.

## Admin UI

### Available views

After installing a plugin, you will see new extension on the sidebar called `RBAC`.

#### Dashboard

The first view after clicking `RBAC` is a `Dashboard` - this view will be extended in the future together with new functionalities.

#### Roles

At this view you can see roles which you created and you can create new one by clicking `Create` button.

#### Permissions

At this view you can see `Predefined` permission which has been sed by the scipt and `Custom` ones which you have created. 

You can create new `Custom` permission by clicking `Create` button. 

You can also switch between `Permission view` and `Category view`.

## Backend

### Available API

For you custom scenarios, when you have your own API paths, you may want to know if logged-in user has the access to particular route.

This plugin exposes additional endpoint `admin/rbac/check`, which you can execute on your frontend code to get the status.

The API definition can be found here: [Check](./docs/api/check.yaml)

## Q&A

### Where is the source code?

This plugin is now behind the licence, so we cannot share the source code.

### Is it possible to hide sidebar option based on RBAC?

In case of MIT Medusa version - no. The issue comes from the fact that Medusa does not allow to make any condition there. We will try to propose some changes, but we cannot guarantee that Medusa will allow it.
However, we are able to customize your own version of Medusa to make it happen - just let us know.

### Is it possible to do other checks?

Sure, but as mentioned above - Medusa is very limited in this area. If you are using forked version of Admin UI - let us know, we will see what we can do for you.

### I am interested, what I need to do?

As mentioned, this plugin is behind the licence. We offer two types of licences:
- subscription - recommended for people who create the store for their own, so for very low monthly cost you can have RBAC functionality.
- lifetime - recommended for people who create the store for the client (so cannot spend money on monthly basis). For one price, you can get this RBAC forever.


We offer also 14-day free trial if you are not sure if you need this plugin - please reach us via labs@rsoftcon.com or go to https://www.medusa-plugins.com.
