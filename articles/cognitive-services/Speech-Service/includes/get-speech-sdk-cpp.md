---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656480"
---
:::row:::
    :::column span="3":::
        Sada C++ Speech SDK je k dispozici ve Windows, Linuxu a macOS. Další informace naleznete v tématu <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Balíček C++ NuGet

Sada C++ Speech SDK lze nainstalovat ze `Install-Package` Správce **balíčků** pomocí následujícího příkazu.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Binární soubory a soubory hlaviček jazyka C++

Alternativně c++ řeči SDK lze nainstalovat z binárních souborů. Stáhněte sadu SDK jako <a href="https://aka.ms/csspeech/linuxbinary" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> balíček .tar</a> a rozbalte soubory v adresáři podle vašeho výběru. Obsah tohoto balíčku (které obsahují soubory hlaviček pro cílové architektury x86 i x64) jsou strukturovány takto:

  | Cesta                   | Popis                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licence                                              |
  | `ThirdPartyNotices.md` | Oznámení třetích stran                                  |
  | `include`              | Soubory hlaviček pro c++                                 |
  | `lib/x64`              | Nativní knihovna x64 pro propojení s vaší aplikací |
  | `lib/x86`              | Nativní knihovna x86 pro propojení s vaší aplikací |

  Chcete-li vytvořit aplikaci, zkopírujte nebo přesuňte požadované binární soubory (a knihovny) do vývojového prostředí. Zahrňte je podle potřeby v procesu sestavení.

#### <a name="additional-resources"></a>Další zdroje

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Zdrojový kód C++ systému Windows, Linux a macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>