---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305389"
---
Vyberte **sestavení**. V dialogovém okně, které se otevře vyberte složku pro projekt Xcode pro export.

Po dokončení exportu se zobrazí složka, která obsahuje exportované projektu Xcode.

> [!NOTE]
> Pokud se zobrazí okno s dotazem, zda chcete nahradit nebo doplňovacích objektů BLOB, doporučujeme vám, že vyberete **připojit** vzhledem k tomu, že je rychlejší. By měl stačí vybrat **nahradit** Pokud měníte prostředky ve scéně. (Například pokud přidáváte, odebrání, Změna nadřazené a podřízené vztahy nebo pokud přidáváte, odebrání nebo změny vlastností.) Pokud děláte jenom změny zdrojového kódu, **připojit** by vám měly dostatečně.

### <a name="open-the-xcode-project"></a>Otevřete projekt Xcode

Ve složce projektu exportované Xcode spuštěním tohoto příkazu v terminálu nainstalujte CocoaPods nezbytné pro projekt:

```bash
pod install --repo-update
```

Teď můžete otevřít `Unity-iPhone.xcworkspace` projekt otevřít v Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Pokud se zobrazí `library not found for -lPods-Unity-iPhone` chyba, budete pravděpodobně otevřen `.xcodeproj` souboru místo `.xcworkspace` souboru. 

Vyberte kořenové **Unity – iPhone** uzel k zobrazení nastavení projektu a pak vyberte **Obecné** kartu.

V části **podepisování**, ujistěte se, že **automaticky spravovat podepisování** je povolená. Pokud není, povolte ho a pak vyberte **povolit automatické** v dialogovém okně, které nejspíše obnovuje nastavení sestavení.

V části **informace o nasazení**, ujistěte se, že **cíl nasazení** je nastavena na `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace na zařízení s Iosem

Připojit zařízení s Iosem do Mac a nastavte **aktivní schéma** na zařízení s Iosem.

![Vybrat zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **sestavení a poté spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)