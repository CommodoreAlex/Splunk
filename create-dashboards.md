# Guide to Creating Dashboards in Splunk

Dashboards in Splunk provide a powerful way to visualize and analyze your data. In this guide, we’ll walk through the steps of creating a Splunk dashboard, from defining properties to adding and customizing panels.

For a visual video (12 minutes - highly recommended) see: 
https://www.youtube.com/watch?v=uQUAvY5M3RU

## Steps to Create a Dashboard

### 1. Navigate to Dashboards

To start creating a dashboard, follow these steps:
- Log into the Splunk Web interface.
- Go to **Search & Reporting**.
- On the left sidebar, click on **Dashboards**.
- Click on **Create New Dashboard**.

This will open a window where you can define your dashboard's properties.

### 2. Define Dashboard Properties

Before adding any panels, you’ll need to define the basic properties of your dashboard.
#### Steps:
- **Name your dashboard**: Give your dashboard a descriptive name that represents its purpose.
- **Select a default time range**: Choose the default time range for the dashboard. This could be something like `Last 24 hours`, `Last 7 days`, or a custom range.
- **Set permissions**: You can set who can view or edit the dashboard. Choose from the available roles to control access.

### 3. Add Panels to the Dashboard

Panels are the building blocks of your dashboard and display the data visualizations. Splunk offers several types of panels you can add.

#### Types of Panels:
- **Table**: Displays raw data in tabular form.
- **Chart**: Displays data in graphical form, including bar charts, line charts, and pie charts.
- **Time chart**: Visualizes time-series data, such as trends over time.

To add a panel:
1. Click **Add Panel**.
2. Choose the type of panel you want to add.

### 4. Customize the Panel

Once a panel is added, you can customize it by specifying the data to visualize using **Search Processing Language (SPL)**.

#### Example: Time chart Panel
If you want to visualize time-series data, a **time chart** panel is ideal. Below is an example of SPL used to create a time chart showing the count of events over time:

```spl
index=_internal | timechart span=1h count
```

`index=_internal`: This specifies the data source (you can change this to any index you need).

`timechart span=1h count`: This creates a time chart with a 1-hour time span and displays the count of events for each hour.

You can adjust the SPL query depending on the data you want to visualize.

#### Other Panel Customizations:
- **Chart Customization**: Choose between bar, line, or pie charts.
- **Table Customization**: Select the fields you want to display in the table.
- **Styling**: Adjust the size, layout, and appearance of the panel to suit your needs.

### 5. Save and Share the Dashboard

After creating and customizing your panels, don’t forget to save your dashboard.

#### Save:
- Click on **Save** at the top right of the dashboard.
- Choose whether to share it with others by setting the appropriate permissions.

#### Share:
- You can share the dashboard by adjusting its permissions for specific users or user roles in your Splunk environment. This allows you to control who can view or edit the dashboard.

---

## Example Use Case

### Creating a Dashboard for System Monitoring

Let's say you want to create a dashboard that monitors the internal Splunk logs:
1. **Create a New Dashboard**: Name it `System Monitoring`.

2. **Set Time Range**: Set the default time range to `Last 24 hours`.

3. **Add Panels**:

**Panel 1**: A **Timechart** panel to display the event count over the last 24 hours.
- SPL: `index=_internal | timechart span=1h count`

**Panel 2**: A **Table** panel to display detailed logs from the `index=_internal`.
- SPL: `index=_internal | head 10`

**Save and Share**: Save the dashboard and set permissions for your team to view it.

---

By defining dashboard properties, adding panels, customizing them with SPL, and saving/sharing the dashboard, you can create powerful visualizations to monitor system performance, track trends, and gain valuable insights.
