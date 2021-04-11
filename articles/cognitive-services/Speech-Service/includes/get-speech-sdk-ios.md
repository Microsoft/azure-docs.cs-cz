---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 31634abe2768ec47ee2aa66051a7a363f83c6009
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043197"
---
:::row:::
    :::column span="3":::
        Při vývoji pro iOS jsou k dispozici dvě sady Speech SDK. Sada SDK pro rozpoznávání řeči pro cíl-C je nativně k dispozici jako balíček CocoaPod pro iOS. Alternativně lze sadu .NET Speech SDK použít s Xamarin. iOS, protože implementuje .NET Standard 2,0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Podrobné informace o použití sady SDK pro rozpoznávání řeči v cíli-C s SWIFT naleznete v tématu <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importování cíle – C do SWIFT </a>.

### <a name="system-requirements"></a>Požadavky na systém

- MacOS verze 10,3 nebo novější
- Cíl iOS 9,3 nebo novější

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Balíček iOS CocoaPod je k dispozici ke stažení a použití s integrovaným vývojovým prostředím (IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (nebo novějším) </a> . Nejdřív <a href="https://aka.ms/csspeech/iosbinary" target="_blank">Stáhněte binární CocoaPod </a>. Rozbalte pole pod ve stejném adresáři pro zamýšlené použití, vytvořte *souboru podfile* a seznam `pod` jako `target` .
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
        Xamarin.iOS zpřístupňuje celou sadu iOS SDK vývojářům v technologii .NET. Vytvářejte plně nativní aplikace pro iOS pomocí jazyků C# nebo F# v sadě Visual Studio. Další informace najdete v tématu <a href="/xamarin/ios/" target="_blank">Xamarin. iOS </a>.
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

#### <a name="additional-resources"></a>Další zdroje informací

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Cílový kurz pro iOS Speech SDK – zdrojový kód C </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">zdrojový kód rychlé zprovoznění pro iOS Speech SDK </a>