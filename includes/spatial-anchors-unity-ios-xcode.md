---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56891002"
---
Vyberte **sestavení** otevřete dialogové okno. Vyberte složku pro export projektů Xcode.

Po dokončení exportu do složky se zobrazí obsahující exportovaném projektu Xcode.

### <a name="open-the-xcode-project"></a>Otevřete projekt Xcode

Ve složce projektu exportované Xcode spuštěním následujícího příkazu nainstalujte CocoaPods nezbytné pro projekt:

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

V části **podepisování**vyberte **automaticky spravovat podepisování**. Vyberte **povolit automatické** v dialogovém okně, které nejspíše obnovuje nastavení sestavení.

V části **informace o nasazení**, ujistěte se, že **cíl nasazení** je nastavena na `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace na zařízení s Iosem

Připojit zařízení s Iosem do Mac a nastavte **aktivní schéma** na zařízení s Iosem.

![Vybrat zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **sestavení a poté spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)