---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: d969b1639dbb39b920c985964abe1239f068d740
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971577"
---
Vyberte **sestavení**. V podokně, které se otevře, vyberte složku, do které se má projekt Xcode exportovat.

   Po dokončení exportu se zobrazí složka, která obsahuje exportovaný projekt Xcode.

   > [!NOTE]
   > Pokud se zobrazí okno se zprávou s dotazem, zda chcete nahradit nebo připojit, doporučujeme vybrat možnost **připojit**, protože je rychlejší. Možnost **nahradit** můžete vybrat jenom v případě, že měníte prostředky ve scéně. Můžete například přidat, odebrat nebo změnit vztahy nadřazenosti/podřízenosti nebo přidat, odebrat nebo změnit vlastnosti. Pokud provádíte pouze změny zdrojového kódu, musí být příkaz **Append** dostatečně k dispozici.

## <a name="open-the-xcode-project"></a>Otevřete projekt Xcode

Nyní můžete `Unity-iPhone.xcodeproj` projekt otevřít v Xcode. 

Můžete buď spustit Xcode a otevřít exportovaný projekt, `Unity-iPhone.xcodeproj` nebo spustit projekt v Xcode spuštěním následujícího příkazu z umístění, kam jste projekt exportovali:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Vyberte uzel root **Unity-iPhone** pro zobrazení nastavení projektu a pak vyberte kartu **Obecné** .

V části **podepisování** se ujistěte, že je povoleno **Automatické řízení podepisování** . Pokud není, povolte ji a pak resetujte nastavení sestavení výběrem možnosti **Povolit automaticky** v zobrazeném podokně.

V části **informace o nasazení** ověřte, že je **cíl nasazení** nastavený na **11,0**.

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do zařízení s iOS

Připojte zařízení s iOS k počítači Mac a nastavte **aktivní schéma** na zařízení s iOS.

   ![Snímek obrazovky s tlačítkem mého iPhonu pro výběr zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **Sestavit a potom spusťte aktuální schéma**.

   ![Snímek obrazovky s tlačítkem "nasadit a spustit" tlačítko se šipkou](./media/spatial-anchors-unity/deploy-run.png)
