---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72933479"
---
Vyberte **Sestavit**. V zobrazeném dialogovém okně vyberte složku, do které chcete exportovat projekt Xcode.

Po dokončení exportu se zobrazí složka, která obsahuje exportovaný projekt Xcode.

> [!NOTE]
> Pokud se zobrazí okno s dotazem, zda chcete nahradit nebo připojit, doporučujeme vybrat **připojit,** protože je rychlejší. Pokud měníte datové zdroje ve scéně, měli byste vybrat **možnost Nahradit.** (Pokud například přidáváte, odebíráte nebo měníte vztahy mezi rodiči a podřízenými vztahy nebo přidáváte, odebíráte nebo měníte vlastnosti.) Pokud provádíte pouze změny zdrojového kódu, append by měl **stačit.**

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Převod projektu Xcode na xcworkspace obsahující odkazy na prostorové kotvy Azure

Ve složce exportovaného projektu Xcode spusťte tento příkaz v terminálu a nainstalujte potřebné kakaové pody pro projekt:

```bash
pod install --repo-update
```

Nyní můžete `Unity-iPhone.xcworkspace` otevřít projekt v Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Pokud máte problémy s KakaoPodem po upgradu na macOS Catalina (10.15), přečtěte si postup řešení [potíží zde.](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015)

Vyberte kořenový uzel **Unity-iPhone, chcete-li** zobrazit nastavení projektu, a pak vyberte kartu **Obecné.**

V **části Podepisování**zkontrolujte, zda je **povolena možnost Automaticky spravovat podepisování.** Pokud není, povolte ji a pak v dialogovém okně, které se zobrazí, vyberte **Povolit automaticky** a resetujte nastavení sestavení.

V části **Informace o nasazení**zkontrolujte, zda je cíl **nasazení** nastaven na `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do iOS zařízení

Připojte iOS zařízení k Macu a nastavte **aktivní schéma** k iOS zařízení.

![Vyberte zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **Sestavit a spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Pokud se `library not found for -lPods-Unity-iPhone` zobrazí chyba, pravděpodobně `.xcodeproj` jste soubor `.xcworkspace` místo souboru otevřeli.
