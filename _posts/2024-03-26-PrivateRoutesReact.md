---
layout: post
title: Private Routes in React
categories: [ری‌اکت]
---

چطور Private Route را در React پیاده سازی کنیم؟

در
React
برای ساختن و مدیریت مسیر های کامپوننت ها از
react-router-dom
استفاده می‌کنیم.

گاهی اوقات برای بعضی از صفحات نیاز به احراز هویت داریم. برای اینکار تابعی پیاده سازی می‌کنیم تا تنها اگر کاربر لاگین کرده بود به مسیر مورد نظر دسترسی داشته باشه. در غیر این صورت به صفحه لاگین هدایت بشه.

## قدم 1 : نصب و وارد کردن react-router-dom

اگر  
 react-router-dom
رو نصب ندارید با این دستور نصب کنید:

```sh
npm install react-router-dom
```

و به این صورت از کامپوننت های اون داخل پروژه استفاده کنید:

```sh
import { BrowserRouter, Route, Routes } from "react-router-dom";
```

## قدم 2 : پیاده سازی تابع

نکته ای که اول باید بگم این هست که در نسخه 6 react-router-dom
تغییراتی ایجاد شده.
اول با نسخه 5 رو توضیح میدم و بعدش با نسخه جدیدتر.

### اگر از react-router-dom V5 استفاده میکنید

در اینجا سه مسیر به Home ,Dashboard و Login
داریم که مسیر Dashboard خصوصی است.

```sh
// App.js
import React, { useState } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import PrivateRoute from './PrivateRoute';
import Home from './Home';
import Dashboard from './Dashboard';
import Login from './Login';

const App = () => {
  return (
    <Router>
      <Switch>
        <Route exact path="/" component={Home} />
        <PrivateRoute path="/dashboard" component={Dashboard}  />
        <Route path="/login" component={Login} />
      </Switch>
    </Router>
  );
};

export default App;
```

در کامپوننت PrivateRoute ،
اگر isAuthenticated = True بود،
به کامپوننت موردنظر می‌رود،
در غیر این صورت به لاگین Redirect می‌کند.

```sh
// PrivateRoute.js
import React from 'react';
import { Route, Redirect } from 'react-router-dom';

const PrivateRoute = ({ component: Component, ...rest }) => {
  isAuthenticated = //your authenticate method
  return (
    <Route
      {...rest}
      render={(props) =>
        isAuthenticated ? (
          <Component {...props} />
        ) : (
          <Redirect to={ pathname: '/login', state: { from: props.location } } />
        )
      }
    />
  );
};

export default PrivateRoute;
```

### اگر از react-router-dom V6 استفاده میکنید

در این نسخه باید روش دیگه ای در پیش بگیریم.
از تغییراتی که باید حواسمون باشه:

- به جای Navigate ، Redirect جایگزین شده
- به جای Switch از کامپوننت جدید Routes استفاده میشه
- Route تغییراتی کرده است

دیگر نمی‌توانیم یک کامپوننت PrivateRoute از Route بسازیم.
در عوض باید از یک کامپوننت مستقل استفاده کنیم.

کد App.js قبلی به این شکل درمیاد:

```sh
// App.js
import React, { useState } from 'react';
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
import PrivateRoute from './PrivateRoute';
import Home from './Home';
import Dashboard from './Dashboard';
import Login from './Login';

const App = () => {
  return (
    <Router>
      <Routes>
        <Route exact path="/" element={<Home />} />
        <Route
          path="/dashboard"
          element={
            <PrivateRoute>
              <Dashboard />
            </PrivateRoute>
          }
        />
        <Route path="/login" element={<Login />} />
      </Routes>
    </Router>
  );
};

export default App;
```

```sh
// PrivateRoute.js
import React from 'react';
import { Route, Navigate } from 'react-router-dom';

function PrivateRoute({ children }) {
  isAuthenticated = //your authenticate method
  return isAuthenticated ? <>{children}</> : <Navigate to="/login" />;
}
```

#### یک روش دیگه

میتونیم هم از Route های تودرتو استفاده کنیم.
برای این روش این Outlet استفاده می‌کنیم.
با اینکار می‌تونیم چندین مسیر شخصی رو زیرمجموعه یک Route قرار بدیم.

```sh
// App.js
import React, { useState } from 'react';
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
import PrivateRoute from './PrivateRoute';
import Home from './Home';
import Dashboard from './Dashboard';
import Login from './Login';

const App = () => {
  return (
    <Router>
      <Routes>
        <Route exact path="/" element={<Home />} />
        <Route path="/dashboard" element={<PrivateRoute />}>
            <Route element={<Dashboard />}  />
        </Route>
        <Route path="/login" element={<Login />} />
      </Routes>
    </Router>
  );
};

export default App;
```

```sh
// PrivateRoute.js
import React from 'react';
import { Route, Navigateو Outlet } from 'react-router-dom';

function PrivateRoute() {
  isAuthenticated = //your authenticate method
  return isAuthenticated  ? <Outlet /> : <Navigate to="/login" />;
}
```

[یک مثال کامل]

[یک مثال کامل]: https://codesandbox.io/p/sandbox/react-router-v6-security-gojb0?
