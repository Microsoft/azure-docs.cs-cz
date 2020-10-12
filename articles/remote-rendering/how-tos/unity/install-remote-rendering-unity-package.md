---
title: Instalace balíčku Remote Renderingu pro Unity
description: Vysvětluje, jak nainstalovat knihovny DLL klienta vzdáleného vykreslování pro Unity.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564300"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalace balíčku Remote Renderingu pro Unity

Vzdálené vykreslování Azure používá balíček Unity k zapouzdření integrace do Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Správa balíčků vzdáleného vykreslování v Unity

Balíčky Unity jsou kontejnery, které se dají spravovat přes [Správce balíčků](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)Unity.
Tento balíček obsahuje celé rozhraní C# API a také všechny binární soubory modulů plug-in, které jsou nutné pro použití vzdáleného vykreslování Azure s Unity.
Následující schéma pojmenování v Unity pro balíčky se nazývá **com. Microsoft. Azure. Remote-rendering**.

Balíček není součástí [úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering)a není dostupný z vnitřního registru balíčku Unity. Chcete-li jej přidat do projektu, je nutné ručně upravit `manifest.md` soubor projektu a přidat následující:

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
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

Po přidání můžete použít Správce balíčků Unity, abyste měli jistotu, že máte nejnovější verzi.
Podrobné pokyny jsou uvedené v tomto [kurzu: zobrazení vzdálených modelů](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Kanály vykreslování Unity

Vzdálené vykreslování funguje s **:::no-loc text="Universal render pipeline":::** a **:::no-loc text="Standard render pipeline":::** . Z důvodů výkonu doporučujeme kanál univerzálního vykreslování.

Pokud chcete použít **:::no-loc text="Universal render pipeline":::** , musí být jeho balíček nainstalovaný v Unity. To se dá udělat buď v uživatelském rozhraní **Správce balíčků** Unity (název balíčku **Universal RP**, verze 7.3.1 nebo novější), nebo prostřednictvím `Packages/manifest.json` souboru, jak je popsáno v [kurzu nastavení projektu Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Další kroky

* [Objekty a komponenty hry Unity](objects-components.md)
* [Kurz: zobrazení vzdálených modelů](../../tutorials/unity/view-remote-models/view-remote-models.md)
