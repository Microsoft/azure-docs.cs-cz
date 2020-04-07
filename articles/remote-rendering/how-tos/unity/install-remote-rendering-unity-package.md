---
title: Instalace balíčku vzdáleného vykreslování pro unity
description: Vysvětluje, jak nainstalovat knihovny DLL vzdáleného vykreslování pro jednotu
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681178"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalace balíčku vzdáleného vykreslování pro unity

Azure Remote Rendering používá balíček Unity k zapouzdření integrace do Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Správa balíčků vzdáleného vykreslování v Unity

Unity balíčky jsou kontejnery, které lze spravovat prostřednictvím Unity [správce balíčků](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html).
Tento balíček obsahuje celé rozhraní API jazyka C# a také všechny binární soubory modulů plug-in, které jsou nutné k použití vzdáleného vykreslování Azure s unity.
Po unity schéma pojmenování pro balíčky, balíček se nazývá **com.microsoft.azure.remote-rendering**.

Balíček není součástí [úložiště vzorků ARR](https://github.com/Azure/azure-remote-rendering)a není k dispozici z registru interních balíčků Unity. Chcete-li jej přidat do projektu, musíte ručně `manifest.md` upravit soubor projektu a přidat následující:
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Po přidání můžete pomocí Správce balíčků Unity zajistit, že máte nejnovější verzi.
Komplexnější pokyny jsou uvedeny v [tutoriálu: Nastavení projektu Unity od nuly](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Unity vykreslovat kanály

Vzdálené vykreslování funguje s **kanálem univerzálního vykreslení** i **s kanálem vykreslení Standard**. Z důvodů výkonu univerzální vykreslení kanálu se doporučuje.

Chcete-li použít **kanál univerzální vykreslení**, jeho balíček musí být nainstalován v Unity. To lze provést buď v Unity **package manager** uI (název balíčku **Universal RP**, verze `Packages/manifest.json` 7.2.1 nebo novější), nebo prostřednictvím souboru, jak je popsáno v [unity instalačníkurze projektu](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Další kroky

* [Unity herní objekty a komponenty](objects-components.md)
* [Kurz: Nastavení projektu Unity od nuly](../../tutorials/unity/project-setup.md)
