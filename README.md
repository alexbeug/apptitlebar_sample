# apptitlebar_sample

A simple WinUI3 app custom title bar sample extending its content into the title bar.

## Introduction

While I was working on a new project's UI style, I wanted to have it using a custom title bar. 
I followed the description on https://learn.microsoft.com/en-us/windows/apps/develop/title-bar?tabs=wasdk, 
but came up with an unwanted coloring of the title bar. In the end, the guide was correct, 
but did not show the correct setting for transparency in the code samples. 

This sample demonstrates the necessary steps to activate the custom title bar with the desired coloring.

## WinUI3 application with default title bar

By default, a WinUI3 application has the default looking Windows title bar with the app name in it.
Depending on your configured Windows theme, the title bar is colored or has no color. Inactive Windows
have no title bar color as in the following screenshot.

![Initial MainWindow of a blank WinUI3 app](/Pictures/1_initial_mainwindow.jpg)

## Adding a navigation view

With a navigation view, the default title bar does not look so well anymore. Many apps extend their content
(search window, back navigation, log in user) into the title bar.

![Navigation view with title bar](/Pictures/2_navigationview_added.jpg)

## Extend content into the title bar

To extend the navigation view and content bages into the title bar, we have to define a custom title bar in
the window's xaml file. In our sample we use a horizontal StackPanel with a simple TextBlock, but additional
controls (Image for an icon, search bar, ...) can be added there and will be displayed with the configured
orientation.

To have our custom title bar placed and sized correctly, we use a Grid layout with 2 rows. The first row will
be our title bar and has a fixed site of 32px. The second row will host the apps navigation view and content.

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

![Navigation view with title bar](/Pictures/3_apptitlebar_added.jpg)

### Modify window title bar coloring

To have the title bar appear in the same color as the navigation view, what would look best for our app, we have to override the
default coloring for the window caption background. To do this we have to change the resources in the App.xml. 
**WindowCaptionBackground** and **WindowCaptionBackgroundDisabled** are set to **Transparent**.

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

![Navigation view with transparent title bar](/Pictures/4_apptitlebar_transparent.jpg)

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

![Navigation view with transparent title bar](/Pictures/5_hidegoback_addicon.jpg)
