---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399916"
---
:::row:::
    :::column span="3":::
        Sada C++ Speech SDK je k dispozici v systémech Windows, Linux a macOS. Další informace najdete v tématu <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. cognitiveservices Account. Speech <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Balíček NuGet pro C++

Sadu C++ Speech SDK lze nainstalovat ze **Správce balíčků** pomocí následujícího `Install-Package` příkazu.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Binární soubory a hlavičkové soubory C++

Alternativně je možné sadu C++ Speech SDK nainstalovat z binárních souborů. Stáhněte si sadu SDK jako <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">balíček <span class="docon docon-navigate-external x-hidden-focus"></span> . tar</a> a rozbalte soubory v adresáři dle vašeho výběru. Obsah tohoto balíčku (který zahrnuje hlavičkové soubory pro architektury cílové platformy x86 i x64) je strukturovaný takto:

  | Cesta                   | Description                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licence                                              |
  | `ThirdPartyNotices.md` | Oznámení třetích stran                                  |
  | `include`              | Hlavičkové soubory pro C++                                 |
  | `lib/x64`              | Nativní knihovna x64 pro propojení s vaší aplikací |
  | `lib/x86`              | Nativní knihovna x86 pro propojení s vaší aplikací |

  Chcete-li vytvořit aplikaci, zkopírujte nebo přesuňte požadované binární soubory (a knihovny) do vývojového prostředí. Zahrňte je podle požadavků v procesu sestavení.

#### <a name="additional-resources"></a>Další zdroje

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Zdrojový kód v rychlém startu pro Windows, Linux a macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>