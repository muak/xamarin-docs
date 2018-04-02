---
title: "Android Platform-Specifics"
description: "Platform-specifics allow you to consume functionality that's only available on a specific platform, without implementing custom renderers or effects. This article demonstrates how to consume the Android platform-specifics that are built into Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
---

# Android Platform-Specifics

_Platform-specificsはCustom RenderersやEffectsを実装することなく、特定のプラットフォームでのみ利用できる機能の使用を可能にします。この記事ではXamarin.Forms組み込みのAndroidのplatform-specificsの使い方を説明します。_

Androidでは、Xamarin.Formsは以下のようなplatform-specificがあります。

- ソフトキーボードの操作モードの設定。詳細は[Setting the Soft Keyboard Input Mode](#soft_input_mode)を参照。
- [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)でのfast scrollingの有効化。詳細は[Enabling Fast Scrolling in a ListView](#fastscroll)を参照。
- [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)でのページ間のスワイプ操作の有効化。詳細は[Enabling Swiping Between Pages in a TabbedPage](#enable_swipe_paging)を参照。
- 描画の順番を決定するためのVisualElementsのZ-orderの制御。詳細は[Controlling the Elevation of Visual Elements](#elevation)を参照。
- AppCompatを使ったアプリケーションで、pauseやresumeで[`Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)や[`Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) といったpageのライフサイクルのイベントをそれぞれ無効化する。詳細は[Disabling the Disappearing and Appearing Page Lifecycle Events](#disable_lifecycle_events)を参照。

<a name="soft_input_mode"></a>

## Setting the Soft Keyboard Input Mode

このplatform-specificはソフトキーボードの入力エリアのための操作方式を設定するために使われ、Xamlで[`Application.WindowSoftInputModeAdjust`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty/)添付プロパティに[`WindowSoftInputModeAdjust`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)列挙型の値を設定して使用します。

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

あるいは、C#からfluent APIを使って使用することができます。

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`メソッドはAndroid上でのみ動作することを指定します。[`Application.UseWindowSoftInputModeAdjust`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)メソッドは、Xamarin.Forms.PlatformConfiguration.AndroidSpecific名前空間に存在し、[`WindowSoftInputModeAdjust`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)列挙型が提供する2つの値（[`Pan`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan/) / [`Resize`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/)）を使って、ソフトキーボードの入力エリアの操作方式を設定するために使用します。`Pan`は[`AdjustPan`](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/)調整オプションを使用しますが、それは入力コントローラがフォーカスを持つ時にwindowをリサイズしません。その代わり、windowのコンテンツはソフトキーボードによって現在のフォーカスが隠れないように移動します。`Resize`は[`AdjustResize`](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/)調整オプションを使用し、入力コントロールがフォーカスを持つ時にソフトキーボードのスペースを空けるためにリサイズします。

その結果、入力コントロールがフォーカスを持つ時のソフトキーボードの入力エリアの操作方式を設定することができます。

[![](android-images/pan-resize.png "Soft Keyboard Operating Mode Platform-Specific")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll"></a>

## Enabling Fast Scrolling in a ListView

このplatform-specificは[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)のデータを通過する高速なスクロールを有効にするために使われます。これはXamlで`ListView.IsFastScrollEnabled`添付プロパティに`boolean`値を設定することで使用します。

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
	<StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
		</ListView>
	</StackLayout>
</ContentPage>
```

あるいは、C#からfluent APIを使って使用することができます。

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`メソッドはこのplatform-specificがAndroid上でのみ動作することを指定します。`ListView.SetIsFastScrollEnabled`メソッドは、[`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)名前空間に存在し、[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)のデータを通過する高速なスクロールを可能にするために使われます。さらに、`SetIsFastScrollEnabled`メソッドは高速スクロールが有効かどうかを返す`IsFastScrollEnabled`メソッドを呼ぶことで高速スクロールを切り替えるために使用することができます。

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

その結果、[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)のデータを通過する高速スクロールが有効になり、スクロールのつまみのサイズが変更されます。

[![](android-images/fastscroll.png "ListView FastScroll Platform-Specific")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging"></a>

## Enabling Swiping Between Pages in a TabbedPage

このplatform-specificは[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)のぺージ間の水平ジェスチャーによるスワイプ移動を有効にするために使われます。これはXamlで[`TabbedPage.IsSwipePagingEnabled`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty/)添付プロパティに`boolean` 値を設定することで使用します。

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

あるいはC#からfluent APIを使って使用することができます。

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`メソッドはこのplatform-specificがAndroid上でのみ動作することを指定します。[`TabbedPage.SetIsSwipePagingEnabled`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/)メソッドは、[`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)名前空間に存在し、[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)のぺージ間のスワイプ移動を有効にするために使われます。`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`名前空間の`TabbedPage`クラスには、このplatform-specificを有効にする[`EnableSwipePaging`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)メソッドと無効にする[`DisableSwipePaging`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)メソッドもあります。[`TabbedPage.OffscreenPageLimit`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/)添付プロパティと[`SetOffscreenPageLimit`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/)メソッドは現在のぺージの両側でアイドル状態のままで保持すべきぺージ数を設定するために使用します。

その結果、[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)によって表示されたぺージ間のスワイプ遷移が有効になります。

![](android-images/tabbedpage-swipe.png)

<a name="elevation"></a>

## Controlling the Elevation of Visual Elements

このplatform-specificは、ターゲットがAPI21以上のアプリケーションでvisual elementsのz-order（重なりの順番）やelevation（高度）を制御するために使われます。visual elementのelevationは、高いZの値を持つvisual elementsが低いZの値をもつvisual elementsを塞ぐように、自身の描画順を決定します。これはXamlで`Elevation.Elevation`添付プロパティに`boolean`値を設定して使用します。


```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:Elevation.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

あるいはC#からfluent APIを使って使用することができます。

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

`Button.On<Android>`メソッドは、このplatform-specificがAndroid上でのみ動作することを指定します。`Elevation.SetElevation`メソッドは、[`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)名前空間に存在し、visual elementのelevationにnull許容型の`float`値を設定するために使われます。さらに、`Elevation.GetElevation`メソッドは、visual elementのelevation値を取得するために使用できます。

その結果、visual elementsのelevationは、高いZ値のvisual elementsが低いZ値のvisual elementsを塞ぐように制御されます。それによって、この例では2番目の[`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)は、より高いelevation値を持つため、[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)の上に描画されます。

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## Disabling the Disappearing and Appearing Page Lifecycle Events

This platform-specific is used to disable the [`Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) and [`Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) page events on application pause and resume respectively, for applications that use AppCompat. In addition, it includes the ability to control whether the soft keyboard is displayed on resume, if it was displayed on pause, provided that the operating mode of the soft keyboard is set to [`WindowSoftInputModeAdjust.Resize`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

> [!NOTE]
> Note that these events are enabled by default to preserve existing behavior for applications that rely on the events. Disabling these events makes the AppCompat event cycle match the pre-AppCompat event cycle.

This platform-specific can be consumed in XAML by setting the [`Application.SendDisappearingEventOnPause`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty/), [`Application.SendAppearingEventOnResume`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty/), and [`Application.ShouldPreserveKeyboardOnResume`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty/) attached properties to `boolean` values:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Alternatively, it can be consumed from C# using the fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

The `Application.Current.On<Android>` method specifies that this platform-specific will only run on Android. The [`Application.SendDisappearingEventOnPause`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) method, in the [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) namespace, is used to enable or disable firing the [`Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) page event, when the application enters the background. The [`Application.SendAppearingEventOnResume`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) method is used to enable or disable firing the [`Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) page event, when the application resumes from the background. The [`Application.ShouldPreserveKeyboardOnResume`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) method is used control whether the soft keyboard is displayed on resume, if it was displayed on pause, provided that the operating mode of the soft keyboard is set to [`WindowSoftInputModeAdjust.Resize`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

The result is that the [`Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) and [`Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) page events won't be fired on application pause and resume respectively, and that if the soft keyboard was displayed when the application was paused, it will also be displayed when the application resumes:

[![](android-images/keyboard-on-resume.png "Lifecycle Events Platform-Specific")](android-images/keyboard-on-resume-large.png#lightbox "Lifecycle Events Platform-Specific")

## Summary

This article demonstrated how to consume the Android platform-specifics that are built into Xamarin.Forms. Platform-specifics allow you to consume functionality that's only available on a specific platform, without implementing custom renderers or effects.


## Related Links

- [Creating Platform-Specifics](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (sample)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
