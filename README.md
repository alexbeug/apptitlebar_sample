# apptitlebar_sample

A simple WinUI3 app custom title bar sample extending its content into the title bar.

## Introduction

While I was working on a new project's UI style, I wanted to have it using a custom title bar. 
I followed the description on https://learn.microsoft.com/en-us/windows/apps/develop/title-bar?tabs=wasdk, 
but came up with an unwanted coloring of the title bar. In the end, the guide was correct, 
but did not show the correct setting for transparency in the code samples. 

This app demonstrates the necessary steps to activate the custom title bar with the desired coloring
and also the necessary adjustments for using the NavigationView back button in the app title bar.

## WinUI3 application with default title bar

By default, a WinUI3 application has the default looking Windows title bar with the app name in it.
Depending on our configured Windows theme, the title bar is colored or has no color. Inactive Windows
have no title bar color as in the following screenshot.

![Initial MainWindow of a blank WinUI3 app](/Pictures/1_initial_mainwindow.jpg)

## Adding a navigation view

With a navigation view, the default title bar does't really look stylish anymore. Many apps extend their content
(search window, back navigation, log in user) into the title bar, so we also want this for our app.

![Navigation view with title bar](/Pictures/2_navigationview_added.jpg)

## Extend content into the title bar

To extend the navigation view and into the title bar, we have to define a custom title bar in
the window's xaml file. In our sample we use a horizontal StackPanel with a simple TextBlock, but additional
controls (Image for an icon, search bar, ...) can be added there and will be displayed with the configured
orientation.

To have our custom title bar placed and sized correctly, we use a Grid layout with 2 rows. The first row will
be our title bar and has a fixed size of 32px. The second row will host the apps navigation view and content.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Window
    x:Class="apptitlebar_sample.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:apptitlebar_sample"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="32"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <StackPanel x:Name="AppTitleBar" 
                    Orientation="Horizontal"
                    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
                    Grid.Row="0" 
                    Height="32">
            <TextBlock x:Name="AppTitleTextBlock" Text="apptitlebar_sample"
                        Grid.Column="0"
                        Grid.Row="0"
                        TextWrapping="NoWrap"
                        Style="{StaticResource CaptionTextBlockStyle}" 
                        VerticalAlignment="Top"
                        Margin="8,8,0,0"/>
        </StackPanel>

        <NavigationView Grid.Row="1"
            x:Name="MainNavigationView"
            AlwaysShowHeader="False"
            Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
            IsBackEnabled="{x:Bind ContentFrame.CanGoBack, Mode=OneWay}">
            <NavigationView.MenuItems>
                <NavigationViewItem
                    Content="Home"
                    Icon="Home"
                    Tag="Home" />
                <NavigationViewItem
                    Content="Page1"
                    Icon="Comment"
                    Tag="Page1" />
                <NavigationViewItem
                    Content="Page2"
                    Icon="PostUpdate"
                    Tag="Page2" />
            </NavigationView.MenuItems>
            <Frame x:Name="ContentFrame"/>
        </NavigationView>
    </Grid>
</Window>
```

In the code behind constructor of the main window set **ExtendsContentIntoTitleBar** and call **SetTitleBar**:
```csharp
        public MainWindow()
        {
            this.InitializeComponent();

            ExtendsContentIntoTitleBar = true;
            SetTitleBar(AppTitleBar);
        }
```
As a result, we can see our custom title bar replace the default title bar, but the coloring is different from the window background. 
In the next step we want to change this and make the app title bar appear more itegrated.

![Navigation view with custom app title bar](/Pictures/3_apptitlebar_added.jpg)

### Modify window title bar coloring

To have the title bar appear in the same color as the navigation view, what would look best for our app, we have to override the
default coloring for the window caption background. To do this we have to change the resources in the App.xml. 
We set **WindowCaptionBackground** and **WindowCaptionBackgroundDisabled** to **Transparent**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Application
    x:Class="apptitlebar_sample.App"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:apptitlebar_sample">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" />
                <!-- Other merged dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Other app resources here -->
            <SolidColorBrush x:Key="WindowCaptionBackground">Transparent</SolidColorBrush>
            <SolidColorBrush x:Key="WindowCaptionBackgroundDisabled">Transparent</SolidColorBrush>            
        </ResourceDictionary>
    </Application.Resources>
</Application>
```
This gives us our desired look:

![Navigation view with custom transparent title bar](/Pictures/4_apptitlebar_transparent.jpg)

## Remove back navigation and add an icon

Windows fluent design guide recommends using the back button left of the app icon and title. This requires a little bit more work, 
so we remove it for now but add an an gauge icon (from the Rodentia icon set).

```xml
        <StackPanel x:Name="AppTitleBar" 
                    Orientation="Horizontal"
                    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
                    Grid.Row="0" 
                    Height="32">
            <Image Source="Assets/rodentia-icons_utilities-system-sensors.png"
                        HorizontalAlignment="Left"
                        VerticalAlignment="Center"
                        Width="24" Height="24"
                        Margin="8,8,0,0"/>
            <TextBlock x:Name="AppTitleTextBlock" Text="apptitlebar_sample"
                        Grid.Column="0"
                        Grid.Row="0"
                        TextWrapping="NoWrap"
                        Style="{StaticResource CaptionTextBlockStyle}" 
                        VerticalAlignment="Center"
                        Margin="8,8,0,0"/>
        </StackPanel>
```

![Navigation view with icon](/Pictures/5_hidegoback_addicon.jpg)

## Add back navigation again and span NavigationView on 2 rows

To have the back navigation button enabled and placed in the app title bar as recommended in the design guide, we have to do some final adjustments.
We add a left margon of 40px to leave some space for the back navigation button and also set the ZIndex for the drawing order.

```xml
        <StackPanel x:Name="AppTitleBar" 
                    Orientation="Horizontal"
                    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
                    Grid.Row="0"
                    Height="32"
                    Canvas.ZIndex="1"
                    Margin="40,0,0,0">
            <Image Source="Assets/rodentia-icons_utilities-system-sensors.png"
                        HorizontalAlignment="Left"
                        VerticalAlignment="Center"
                        Width="24" Height="24"
                        Margin="8,8,0,0"/>
            <TextBlock x:Name="AppTitleTextBlock" Text="apptitlebar_sample"
                        Grid.Column="0"
                        Grid.Row="0"
                        TextWrapping="NoWrap"
                        Style="{StaticResource CaptionTextBlockStyle}" 
                        VerticalAlignment="Center"
                        Margin="8,8,0,0"/>
        </StackPanel>
```

Then we place the NavigationView in the same grid row as the app title bar (Grid.Row="0") to have its back butten drawn beside the app icon. 
We also add Grid.RowSpan="2" to have the NavigationView cover both rows of our grid and make the back button visible again.

```xml
        <NavigationView
            x:Name="MainNavigationView"
            AlwaysShowHeader="False"
            Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
            IsBackButtonVisible="Visible"
            Grid.Row="0"
            Grid.RowSpan="2"
            Canvas.ZIndex="0">
```

Unfortunately this leads to a situation where the navigation views content frame is laso partially overlapped by the title bar.

![Navigation view with back button and unadjusted frame](/Pictures/6_back_disabled_icon_added.jpg)

## Adjust NavigationView content frame

To adjust positioning for the content frame, which is part of the NavigationView, we need to modify the NavigationView resources.
This can be done directly in the App.xaml by setting the Thickness for NavigationViewContentMargin.

```xml
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" />
                <!-- Other merged dictionaries here -->
            </ResourceDictionary.MergedDictionaries>

            <!-- Other app resources here -->
            <SolidColorBrush x:Key="WindowCaptionBackground">Transparent</SolidColorBrush>
            <SolidColorBrush x:Key="WindowCaptionBackgroundDisabled">Transparent</SolidColorBrush>

            <!-- Margin for NavigationViewContent to move it below the title bar -->
            <Thickness x:Key="NavigationViewContentMargin">0,48,0,0</Thickness>
        </ResourceDictionary>
    </Application.Resources>
```

Our content frame is now placed correctly below our app title bar. Finally, we do some adjustments to margings and heights to
position back button, icon and app title in a line.

```xml
        <Grid.RowDefinitions>
            <RowDefinition Height="40"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
```

![Navigation view with transparent title bar](/Pictures/7_back_disabled_icon_added_adjusted.jpg)

## Conclusion

As a starter in WinUI 3 it took me a while to get the things figured out to have my app title bar displayed correctly as I wanted it to be.
It was hard to find some good and easy examples, that clearly pointed out the important points to get the desired result. In the end I found 
myselft debugging the WinUI gallery, so I decided to put together this little sample + tutorial for anybody else, who is dealing with the same issue. 

The key points are:
- follow the guide from Microsoft's tutorial and set **ExtendsContentIntoTitleBar** and call **SetTitleBar** for your window
- set the **SolidColorBrush** for **WindowCaptionBackground** and **WindowCaptionBackgroundDisabled** to **Transparent**.
- leave some margin for the back navigation button and have the NavigationView span both rows of the main window grid
- set **Thickness** for **NavigationViewContentMargin** to adjust the NavigationView content frame according to the app title bar
 
You can get the complete sample by cloning this repository. If you have any feedback or suggestions feel free to reply.