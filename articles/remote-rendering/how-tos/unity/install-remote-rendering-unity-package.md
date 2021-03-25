---
title: Instalace balíčku Remote Renderingu pro Unity
description: Vysvětluje, jak nainstalovat knihovny DLL klienta vzdáleného vykreslování pro Unity.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047724"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalace balíčku Remote Renderingu pro Unity

Vzdálené vykreslování Azure používá balíček Unity k zapouzdření integrace do Unity.
Tento balíček obsahuje celé rozhraní C# API a také všechny binární soubory modulů plug-in, které jsou nutné pro použití vzdáleného vykreslování Azure s Unity.
Následující schéma pojmenování v Unity pro balíčky se nazývá **com. Microsoft. Azure. Remote-rendering**.

Pro instalaci balíčku Unity můžete vybrat jednu z následujících možností.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Nainstalovat balíček vzdáleného vykreslování pomocí nástroje funkce Mixed reality

[Nástroj funkce Mixed reality](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([stažení](https://aka.ms/mrfeaturetool)) je nástroj, který slouží k integraci balíčků funkcí hybridní reality do projektů Unity. Balíček není součástí [úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering)a není dostupný z vnitřního registru balíčku Unity.

Chcete-li přidat balíček do projektu, který potřebujete:
1. [Stažení nástroje funkce Mixed reality](https://aka.ms/mrfeaturetool)
1. Postupujte podle [podrobných pokynů](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) k používání tohoto nástroje.
1. Na stránce **zjišťování funkcí** zaškrtněte políčko pro balíček **Microsoft Azure vzdáleného vykreslování** a vyberte verzi balíčku, který chcete přidat do projektu.

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Pokud chcete aktualizovat místní balíček, stačí vybrat novější verzi z nástroje funkce Mixed reality a nainstalovat ji. Aktualizace balíčku občas může vést k chybám konzoly. Pokud k tomu dojde, zkuste projekt zavřít a znovu otevřít.

## <a name="install-remote-rendering-package-manually"></a>Nainstalovat balíček vzdáleného vykreslování ručně

Chcete-li nainstalovat balíček vzdáleného vykreslování ručně, je nutné provést následující kroky:

1. Stáhněte balíček z informačního kanálu NPM Packages Mixed reality na adrese `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Můžete buď použít [npm](https://www.npmjs.com/get-npm) , a spuštěním následujícího příkazu Stáhnout balíček do aktuální složky.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Případně můžete použít PowerShellový skript `Scripts/DownloadUnityPackages.ps1` z [úložiště GitHub Azure-Remote-rendering](https://github.com/Azure/azure-remote-rendering).
        * Upravit obsah `Scripts/unity_sample_dependencies.json` pro
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Spuštěním následujícího příkazu v PowerShellu Stáhněte balíček do zadaného cílového adresáře.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Nainstalujte stažený balíček](https://docs.unity3d.com/Manual/upm-ui-tarball.html) pomocí Správce balíčků Unity.

Pokud chcete aktualizovat místní balíček, stačí znovu spustit příslušný příkaz, který jste použili, a znovu naimportovat balíček. Aktualizace balíčku občas může vést k chybám konzoly. Pokud k tomu dojde, zkuste projekt zavřít a znovu otevřít.

## <a name="unity-render-pipelines"></a>Kanály vykreslování Unity

Vzdálené vykreslování funguje s **:::no-loc text="Universal render pipeline":::** a **:::no-loc text="Standard render pipeline":::** . Z důvodů výkonu doporučujeme kanál univerzálního vykreslování.

Pokud chcete použít **:::no-loc text="Universal render pipeline":::** , musí být jeho balíček nainstalovaný v Unity. To se dá udělat buď v uživatelském rozhraní **Správce balíčků** Unity (název balíčku **Universal RP**, verze 7.3.1 nebo novější), nebo prostřednictvím `Packages/manifest.json` souboru, jak je popsáno v [kurzu nastavení projektu Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Další kroky

* [Objekty a komponenty hry Unity](objects-components.md)
* [Kurz: zobrazení vzdálených modelů](../../tutorials/unity/view-remote-models/view-remote-models.md)