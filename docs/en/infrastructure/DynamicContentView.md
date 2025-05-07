# DynamicContentView
DynamicContentView is a view that can be used to display different content based on the current state. It inherits from `ContentView` and provides a flexible way to render different content based on a value. Multiple conditions can be defined to **DynamicContentView** and it finds the matching condition according to your rules and render matching condition's content. Conditions use `DataTemplate` to define content. So, how many conditions you define isn't so important. They won't be initialized and attached to visual tree until your condition has met. It's a memory efficient way to render different content instead setting IsVisible properties of views.

## Usage

DynamicContentView is defined in `UraniumUI.Views` namespace. You can use it in XAML like this:

```
xmlns:uranium="http://schemas.enisn-projects.io/dotnet/maui/uraniumui"
```

Then you can use it with `uranium:DynamicContentView` tag.

```xml
<uranium:DynamicContentView Value="{Binding IsBusy}">
    <uranium:DynamicContentView.Conditions>

        <uranium:ValueCondition Equal="True">
            <DataTemplate>
                <ActivityIndicator IsRunning="True" />
            </DataTemplate>
        </uranium:ValueCondition>

        <uranium:ValueCondition Equal="False">
            <DataTemplate>
                <Label  Text="Actual Content Here"/>
            </DataTemplate>
        </uranium:ValueCondition>

    </uranium:DynamicContentView.Conditions>
</uranium:DynamicContentView>
```

### Value Property
The `Value` property is the source of truth for condition evaluation. It uses a custom `DynamicContentValueTypeConverter` to handle type conversion, allowing you to bind various types of values including:
- Boolean values
- Numeric values
- String values
- Custom objects (as long as they implement proper equality comparison)

### Conditions
`Condition` is a bindable object that contains a `DataTemplate` as Content and `Equal`, `GreaterThan`, `LessThan`, `Not` properties. Each property represents a comparison to `Value` property of `DynamicContentView`. When `Value` property of `DynamicContentView` changes, it finds the matching condition and renders the content of it. If no condition matches, it doesn't render anything. You can define multiple conditions and it finds the matching condition according to your rules. If multiple condition matches, it renders the first matching condition's content. So, order of condition definition is important. You can place a fallback condition at the bottom that matches everything as a fallback condition.

The Conditions collection is an `ObservableCollection<ValueCondition>`, which means it automatically triggers updates when conditions are added, removed, or modified.

Here are some examples of different condition types:

```xml
<!-- Numeric comparison -->
<uranium:DynamicContentView Value="{Binding MyNumber}">
    <uranium:DynamicContentView.Conditions>
        <uranium:ValueCondition GreaterThan="5">
            <DataTemplate>
                <Label Text="Value is greater than 5" TextColor="Green"/>
            </DataTemplate>
        </uranium:ValueCondition>
        
        <uranium:ValueCondition Equal="5">
            <DataTemplate>
                <Label Text="Value is exactly 5"/>
            </DataTemplate>
        </uranium:ValueCondition>

        <uranium:ValueCondition LessThan="5">
            <DataTemplate>
                <Label Text="Value is less than 5" TextColor="Red"/>
            </DataTemplate>
        </uranium:ValueCondition>
    </uranium:DynamicContentView.Conditions>
</uranium:DynamicContentView>

<!-- String comparison -->
<uranium:DynamicContentView Value="{Binding Status}">
    <uranium:DynamicContentView.Conditions>
        <uranium:ValueCondition Equal="Success">
            <DataTemplate>
                <Label Text="Operation completed successfully" TextColor="Green"/>
            </DataTemplate>
        </uranium:ValueCondition>
        
        <uranium:ValueCondition Equal="Error">
            <DataTemplate>
                <Label Text="An error occurred" TextColor="Red"/>
            </DataTemplate>
        </uranium:ValueCondition>
    </uranium:DynamicContentView.Conditions>
</uranium:DynamicContentView>

<!-- Not condition -->
<uranium:DynamicContentView Value="{Binding IsLoading}">
    <uranium:DynamicContentView.Conditions>
        <uranium:ValueCondition Not="True">
            <DataTemplate>
                <Label Text="Content is ready"/>
            </DataTemplate>
        </uranium:ValueCondition>
    </uranium:DynamicContentView.Conditions>
</uranium:DynamicContentView>
```

### Performance Considerations
- Conditions are evaluated in order, so place the most specific conditions first
- Content is only created when a condition is met, making it memory efficient
- The view automatically updates when the Value property changes
- The view automatically updates when the Conditions collection changes