---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 36d509bdcd1fda61cb85fae7fa38ed126697f888
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752176"
---
Vyberte **sestavení** otevřete dialogové okno. Vyberte složku pro export projektů Xcode.

Po dokončení exportu do složky se zobrazí obsahující exportovaném projektu Xcode.

### <a name="open-the-xcode-project"></a>Otevřete projekt Xcode

Ve složce projektu exportované Xcode spuštěním následujícího příkazu nainstalujte CocoaPods nezbytné pro projekt:

```bash
pod install
```

Nyní můžete otevřít `Unity-iPhone.xcworkspace` projekt otevřít v Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Pokud se zobrazí `library not found for -lPods-Unity-iPhone` chyba, pravděpodobně otevřením `.xcodeproj` souborů namísto `.xcworkspace`. Otevřít `.xcworkspace` a zkuste to znovu.

Vyberte kořenové **Unity – iPhone** uzel k zobrazení nastavení projektu a vyberte **Obecné** kartu.

V části **podepisování**vyberte **automaticky spravovat podepisování**. Vyberte **povolit automatické** v dialogovém okně, které nejspíše obnovuje nastavení sestavení.

V části **informace o nasazení**, ujistěte se, že **cíl nasazení** je nastavena na `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace na zařízení s Iosem

Připojit zařízení s Iosem do Mac a nastavte **aktivní schéma** na zařízení s Iosem.

![Vybrat zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **sestavení a poté spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)