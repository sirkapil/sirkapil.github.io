---
layout: post
title: How to set AutomationId for TreeViewItems in WPF
tags: [csharp, wpf]
fullview: true
---

Recently I started to implement UI tests for a WPF application with White. I'm entirely new to WPF, so it wasn't obvious to me how can I set the AutomationId of a TreeViewItem which is generated during runtime based on the contained object's property.
Here's the solution:

{% highlight xml linenos=table%}
<Window x:Class="ItemTemplatePropertySetting.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="350" Width="525">
    <Grid>
        <TreeView Name="treeView" ItemsSource="{Binding RootNode.Children}">
            <TreeView.ItemContainerStyle>
                <Style TargetType="{x:Type TreeViewItem}">
                    <Setter Property="AutomationProperties.AutomationId" Value="{Binding Text, StringFormat={}{0}Node}"/>
                    <!--
                    <Setter Property="AutomationProperties.AutomationId">
                        <Setter.Value>
                            <MultiBinding StringFormat="{}{0}-{1}">
                                <Binding Path="Text" />
                                <Binding Path="Text" />
                            </MultiBinding>
                        </Setter.Value>
                    </Setter>
                    -->
                </Style>
            </TreeView.ItemContainerStyle>
            <TreeView.ItemTemplate>
                <HierarchicalDataTemplate ItemsSource="{Binding Children}">
                    <TextBlock Text="{Binding Text}" VerticalAlignment="Center" Margin="0,0,2,0"/>
                </HierarchicalDataTemplate>
            </TreeView.ItemTemplate>
        </TreeView>
    </Grid>
</Window>
{% endhighlight %}

This is a simple WPF Window with a TreeView control in it. We have a TreeView.ItemContainerStlye definition in line 8. All objects in the tree will have TreeViewItem as it's container.
The key line is the 9th. With this Setter we can define the AutomationId's "format" and this will be applied for all TreeViewItems.

{% highlight xml linenos=table%}
<Setter Property="AutomationProperties.AutomationId" Value="{Binding Text, StringFormat={}{0}Node}"/>
{% endhighlight %}

In the Value property's value there is a binding to the Text property of the stored object. If the stored object's Text property's value is "Foo", the TreeViewItem's AutomationId would be "FooNode" with the applied StringFormat. The first {} after the = sign is for escaping the {0} in the format string. If the right side of the = sign would start with a normal character, we wouldn't need the to escape it.

In the line 10-19 there is an other solution commented out. In this way we can concatenate two property's value. With the previous example the Id now would be "Foo-Foo". You can inspect the AutomationId of a GUI element with various programs, like Snoop or Inspect (in the Windows SDK).

[Example project on GitHub](https://github.com/cSE-/ExampleProjects/ItemTemplatePropertySetting)