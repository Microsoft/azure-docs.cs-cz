---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562475"
---
Vyberte **sestavení**. V dialogovém okně, které se otevře, vyberte složku, do které chcete exportovat projekt Xcode.

Po dokončení exportu se zobrazí složka obsahující exportovaný projekt Xcode.

> [!NOTE]
> Pokud se zobrazí okno s dotazem, jestli chcete nahradit nebo připojit, doporučujeme vybrat **připojit** , protože je rychlejší. Pokud měníte prostředky ve scéně, je nutné vybrat možnost **nahradit** . (Například pokud přidáváte, odebíráte nebo měníte vztahy nadřazený-podřízený nebo pokud přidáváte, odebíráte nebo měníte vlastnosti.) Pokud provádíte pouze změny zdrojového kódu, musí být příkaz **Append** dostatečně k dispozici.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Převeďte projekt Xcode na xcworkspace obsahující odkazy na prostorové kotvy Azure.

V exportované složce projektu Xcode spusťte tento příkaz v terminálu a nainstalujte nezbytný CocoaPods pro projekt:

```bash
pod install --repo-update
```

Nyní můžete otevřít `Unity-iPhone.xcworkspace` a otevřít projekt v Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Pokud se zobrazí `library not found for -lPods-Unity-iPhone` chyba, pravděpodobně jste `.xcodeproj` soubor otevřeli místo `.xcworkspace` souboru. 

Vyberte uzel root **Unity-iPhone** pro zobrazení nastavení projektu a pak vyberte kartu **Obecné** .

V části **podepisování**se ujistěte, že je povoleno **Automatické řízení podepisování** . Pokud není, povolte ji a potom v dialogovém okně, které se zobrazí, vyberte **Povolit automatické** obnovení nastavení sestavení.

V části **informace o nasazení**zajistěte, aby byl **cíl nasazení** nastaven na `11.0`hodnotu.

### <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do zařízení s iOS

Připojte zařízení s iOS k počítači Mac a nastavte **aktivní schéma** na zařízení s iOS.

![Vybrat zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **Sestavit a potom spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)