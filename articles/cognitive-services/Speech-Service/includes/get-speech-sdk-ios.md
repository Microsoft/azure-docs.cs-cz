---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f3864b28ba6f5bdbffe6601d059a21328d56a5e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656644"
---
:::row:::
    :::column span="3":::
        Při vývoji pro iOS jsou k dispozici dvě sady SDK řeči. Sada Objective-C Speech SDK je nativně k dispozici jako balíček iOS CocoaPod. Alternativně .NET řeči SDK lze použít s Xamarin.iOS při implementaci .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Podrobnosti pomocí sady Objective-C Speech SDK s swiftem naleznete <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">v tématu Import objective-C do swiftu <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="system-requirements"></a>Požadavky na systém

- MacOS verze 10.3 nebo novější
- Cíl iOS 9.3 nebo novější

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Balíček iOS CocoaPod je k dispozici ke stažení a použití s integrovaným vývojovým prostředím <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">(IDE) Xcode 9.4.1 (nebo novější). <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Nejprve <a href="https://aka.ms/csspeech/iosbinary" target="_blank">si stáhněte binární <span class="docon docon-navigate-external x-hidden-focus"> </span>kakaový pod. </a> Extrahujte pod ve stejném adresáři pro jeho zamýšlené `pod` použití, `target`vytvořte *Podfile* a uveďte jako .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS zpřístupňuje celou sadu iOS SDK vývojářům v technologii .NET. Vytvářejte plně nativní aplikace pro iOS pomocí jazyků C# nebo F# v sadě Visual Studio. Další informace naleznete v tématu <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Další zdroje

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Zdrojový kód cíle C sady iOS Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">Zdrojový kód Swift pro rychlé spuštění sady IOS Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>