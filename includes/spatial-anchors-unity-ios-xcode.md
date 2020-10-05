---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89536246"
---
Vyberte **sestavení**. V dialogovém okně, které se otevře, vyberte složku, do které chcete exportovat projekt Xcode.

Po dokončení exportu se zobrazí složka obsahující exportovaný projekt Xcode.

> [!NOTE]
> Pokud se zobrazí okno s dotazem, jestli chcete nahradit nebo připojit, doporučujeme vybrat **připojit** , protože je rychlejší. Pokud měníte prostředky ve scéně, je nutné vybrat možnost **nahradit** . (Například pokud přidáváte, odebíráte nebo měníte vztahy nadřazený-podřízený nebo pokud přidáváte, odebíráte nebo měníte vlastnosti.) Pokud provádíte pouze změny zdrojového kódu, musí být příkaz **Append** dostatečně k dispozici.

## <a name="open-the-xcode-project"></a>Otevřete projekt Xcode

Nyní můžete otevřít `Unity-iPhone.xcodeproj` v Xcode. Můžete buď spustit Xcode a otevřít exportovaný projekt, `Unity-iPhone.xcodeproj` nebo spustit projekt v Xcode spuštěním následujícího příkazu z umístění, do kterého jste projekt exportovali:

```bash
open ./Unity-iPhone.xcodeproj
```

Vyberte uzel root **Unity-iPhone** pro zobrazení nastavení projektu a pak vyberte kartu **Obecné** .

V části **podepisování**se ujistěte, že je povoleno **Automatické řízení podepisování** . Pokud není, povolte ji a potom v dialogovém okně, které se zobrazí, vyberte **Povolit automatické** obnovení nastavení sestavení.

V části **informace o nasazení**zajistěte, aby byl **cíl nasazení** nastaven na hodnotu `11.0` .

## <a name="deploy-the-app-to-your-ios-device"></a>Nasazení aplikace do zařízení s iOS

Připojte zařízení s iOS k počítači Mac a nastavte **aktivní schéma** na zařízení s iOS.

![Vyberte zařízení](./media/spatial-anchors-unity/select-device.png)

Vyberte **Sestavit a potom spusťte aktuální schéma**.

![Nasazení a spuštění](./media/spatial-anchors-unity/deploy-run.png)
