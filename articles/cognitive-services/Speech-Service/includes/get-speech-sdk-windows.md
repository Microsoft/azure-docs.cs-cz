---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400022"
---
:::row:::
    :::column span="3":::
        Sada Speech SDK podporuje windows 10 a Windows Server 2016 nebo novější verze. Starší verze **nejsou** oficiálně podporovány. Je možné použít části sady Speech SDK se staršími verzemi systému Windows, i když to není doporučeno.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Systémové požadavky

Sada Speech SDK v systému Windows vyžaduje <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">sadu Microsoft Visual C++ Redistributable for Visual Studio 2019 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v systému.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalace pro x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalace pro x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalace pro ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Pro vstup mikrofonu musí být nainstalovány knihovny Media Foundation. Tyto knihovny jsou součástí Windows 10 a Windows Server 2016. Je možné použít sadu Speech SDK bez těchto knihoven, pokud se jako vstupní zvukové zařízení nepoužívá mikrofon.

Požadované soubory sady Speech SDK lze nasadit ve stejném adresáři jako aplikace. Tímto způsobem může vaše aplikace přímo přistupovat ke knihovnám. Ujistěte se, že jste vybrali správnou verzi (x86/x64), která odpovídá vaší aplikaci.

| Název                                            | Funkce                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Základní sada SDK, požadovaná pro nativní a spravované nasazení |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Požadováno pro spravované nasazení                      |

> [!NOTE]
> Počínaje verzí 1.3.0 soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodáván v předchozích verzích) již není potřeba. Funkce je nyní integrována do základní sady SDK.

> [!IMPORTANT]
> Pro projekt C# aplikace Windows Forms (.NET Framework) zkontrolujte, zda jsou knihovny zahrnuty v nastavení nasazení projektu. Můžete zkontrolovat v `Properties -> Publish Section`části . Klikněte `Application Files` na tlačítko a vyhledejte odpovídající knihovny ze seznamu posunout dolů. Zkontrolujte, zda je `Included`hodnota nastavena na hodnotu . Visual Studio bude obsahovat soubor při publikování nebo nasazení projektu.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
