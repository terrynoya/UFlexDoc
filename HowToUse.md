## Introduction

suppose you want to create a slider and listen the slider value change event
in editor you code is like below
1. write code in OnGUI function
2. save the current slider value
3. compare slider return value to the old value,and trigger change function

```csharp
private int _sliderValue;

protected void OnGUI()
{
    int oldValue = _sliderValue;
    this._sliderValue = EditorGUILayout.IntSlider("aa", _sliderValue, 0, 100);
    if (oldValue != _sliderValue)
    {
        this.OnSliderChanged(oldValue,this._sliderValue);
    }
}

private void OnSliderChanged(int oldValue,int newValue)
{
    Debug.Log(oldValue+","+newValue);
}
```

if you want 2 sliders or more, this code style will become messy  
the idea to make code clean is to wrap the value and the change callback to a class

```csharp
public class SimpleSliderWrapper
{
    private int _value;
    public int MinValue;
    public int MaxValue;

    public string Label;

    public Action<int,int> OnValueChange;

    public void OnGUI()
    {
        int newValue = EditorGUILayout.IntSlider(Label, this._value, 0, 100);
        if (newValue != this._value && this.OnValueChange != null)
        {
            this.OnValueChange(this._value,newValue);
        }
        this._value = newValue;
    }
}
```

then you new your SimpleSliderWrapper class somewhere outside OnGUI

```csharp
this._sliderWrapper = new SimpleSliderWrapper();
this._sliderWrapper.Label = "bb";
this._sliderWrapper.OnValueChange += this.OnSliderValueChange;
```

and call SimpleSliderWrapper's OnGUI in the Unity's OnGUI function

```csharp
protected void OnGUI()
{
    this._sliderWrapper.OnGUI();
}
```

the UFlex Framework do the exactly wrapper thing for you  
when you use UFlex,your code is like blow

```csharp
GUISlider slider = new GUISlider();
slider.MinValue = 10;
slider.MaxValue = 50;
slider.Value = 20;

slider.addEventlistener(GUISliderEvent.CHANGED, this.OnSliderValueChanged);
this.stage.addElement(group);
```

besides,you don't need to write the OnGUI thing  
the stage help you to call OnGUI when you add the component(slider in this case) as a stage child element

