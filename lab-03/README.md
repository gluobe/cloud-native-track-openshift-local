# Lab 03 - Using the web console

In this lab we will explore the Openshift Web Console.

## Task 1: Login to the web console

Open the URL that was shown when issuing the `crc start` command:

```
The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing
```

![login](../images/login.png "Login")

Login with the `username` and `password` that were shown when issuing the `crc start` command:

```
Log in as user:
  Username: developer
  Password: developer
```

![new_project](../images/new_project.png "New Project")

Click the `Create a new project` button.

![new_project_lab-03](../images/new_project_lab-03.png "New Project lab-03")

Fill out the **Name** field: `lab-03`

![developer_catalog](../images/developer_catalog.png "Developer Catalog")

Click **+Add** in the menu on the left, after that click on **All Services**.

![php](../images/php.png "PHP")

Enter `php` in the search field and click on the **PHP** builder image on the right.

![php2](../images/php2.png "PHP")

Click the blue **Create Application** button.

![git](../images/git.png "Git")

Click the **Try sample** link, to fill out a sample repo.

![name](../images/name.png "Name")

Use `php` as the **Name** and next click the blue `Create` button.

![topology](../images/topology.png "Topology")

Next click **Topology** in the menu on the left.

![route](../images/route.png "Route")

Wait until the build is complete and the pod is running. Next click the link in the route section.

## Task 2: Delete your project

You are also able to delete the project via the `Web Console`.

![topology](../images/topology.png "Topology")

To delete the project click **Project** in the menu on the left, select **Actions** and click on **Delete Project**.