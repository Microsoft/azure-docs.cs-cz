---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 6768b1b8e0f5d7d3644779268025551c4e1aef9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964111"
---
Vyberte **sestavení** otevřete dialogové okno. Vyberte složku pro export projektů Xcode.

Po dokončení exportu do složky se zobrazí obsahující exportovaném projektu Xcode.

> [!NOTE]
> Pokud otevře se dialogové okno s dotazem, jestli chcete **nahradit** nebo **Append**, **připojit** se doporučuje, protože je rychlejší. By měl pouze potřebujete provést **nahradit** Pokud měníte prostředky ve scéně (přidání, odebrání, Změna nadřazené a podřízené vztahy, přidání/odebrání/Změna vlastnosti atd). Pokud provádíte jenom změny zdrojového kódu, **připojit** by vám měly dostatečně.

### <a name="open-the-xcode-project"></a>Otevřete projekt Xcode

Ve složce projektu exportované Xcode spusťte následující příkaz, v terminálu nainstalujte CocoaPods nezbytné pro projekt:

```bash
pod install --repo-update
```

Nyní můžete otevřít `Unity-iPhone.xcworkspace` projekt otevřít v Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Pokud se zobrazí `library not found for -lPods-Unity-iPhone` chyba, pravděpodobně otevřením `.xcodeproj` souborů namísto `.xcworkspace`. Otevřít `.xcworkspace` a zkuste to znovu.

Vyberte kořenové **Unity – iPhone** uzel k zobrazení nastavení projektu a vyberte **Obecné** kartu.

V části **podepisování**, zkontrolujte, že **automaticky spravovat podepisování** je povolená. Pokud není, aktivovat ji a vyberte **povolit automatické** v dialogovém okně, které nejspíše obnovuje nastavení sestavení.

V části **informace o nasazení**, ujistěte se, že **cíl nasazení** je nastavena na `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace na zařízení s Iosem

Připojit zařízení s Iosem do Mac a nastavte **aktivní schéma** na zařízení s Iosem.

![Vybrat zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **sestavení a poté spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)