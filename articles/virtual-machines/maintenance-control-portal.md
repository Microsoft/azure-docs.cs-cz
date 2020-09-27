---
title: Řízení údržby pro virtuální počítače Azure pomocí Azure Portal
description: Naučte se řídit, kdy se na virtuální počítače Azure používá údržba pomocí řízení údržby a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: b174e2631131e6bf26d7b1cb62442c8a99102e2e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397279"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Řízení aktualizací s využitím řízení údržby a Azure Portal

Řízení údržby vám umožní určit, kdy se mají aktualizace použít pro izolované virtuální počítače a vyhrazené hostitele Azure. Toto téma popisuje možnosti Azure Portal pro řízení údržby. Další informace o výhodách použití řízení údržby, jejich omezení a dalších možností správy najdete v tématu [Správa aktualizací platformy pomocí řízení údržby](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Vytvořit konfiguraci údržby

1. Přihlaste se k portálu Azure.

1. Vyhledejte **Konfigurace údržby**.

   ![Snímek obrazovky znázorňující otevření konfigurací údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Klikněte na **Přidat**.

   ![Snímek obrazovky ukazující, jak přidat konfiguraci údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Vyberte předplatné a skupinu prostředků, zadejte název konfigurace a vyberte oblast. Klikněte na **Next** (Další).

   ![Snímek obrazovky se základními informacemi o konfiguraci údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Přidejte značky a hodnoty. Klikněte na **Next** (Další).

   ![Snímek obrazovky ukazující Přidání značek do konfigurace údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Zkontrolujte souhrnné informace. Klikněte na **Vytvořit**.

   ![Snímek obrazovky ukazující, jak vytvořit konfiguraci údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Po dokončení nasazení klikněte na **Přejít k prostředku**.

   ![Snímek obrazovky znázorňující dokončení nasazení konfigurace údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Na stránce podrobností v konfiguraci údržby klikněte na přiřazení a pak klikněte na **přiřadit prostředek**. 

![Snímek obrazovky ukazující, jak přiřadit prostředek](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Vyberte prostředky, ke kterým chcete přiřazenou konfiguraci údržby, a klikněte na **OK**. Ve sloupci **typ** se zobrazuje, jestli je prostředek izolovaný virtuální počítač nebo vyhrazený hostitel Azure. Aby bylo možné přiřadit konfiguraci, musí být spuštěný virtuální počítač. Pokud se pokusíte přiřadit konfiguraci k virtuálnímu počítači, který je zastavený, dojde k chybě. 

<!---Shantanu to add details about the error case--->

![Snímek obrazovky ukazující, jak vybrat prostředek](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Ověřit konfiguraci

Můžete ověřit, že se konfigurace použila správně, nebo zkontrolovat, jestli je konfigurace údržby, která je aktuálně přiřazená pomocí **konfigurací údržby**. Sloupec **typ** zobrazuje, zda je konfigurace přiřazena k IZOLOVANému virtuálnímu počítači nebo vyhrazenému hostiteli Azure. 

![Snímek obrazovky ukazující, jak ověřit konfiguraci údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Na stránce vlastností můžete také ověřit konfiguraci pro konkrétní virtuální počítač. Kliknutím na **Údržba** zobrazíte konfiguraci přiřazenou tomuto virtuálnímu počítači.

![Snímek obrazovky znázorňující kontrolu údržby hostitele](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Vyhledat nedokončené aktualizace

Existují také dva způsoby, jak ověřit, zda čekají aktualizace na konfiguraci údržby. V části **Konfigurace údržby**na stránce Podrobnosti o konfiguraci klikněte na **přiřazení** a zaškrtněte políčko **stav údržby**.

![Snímek obrazovky ukazující, jak kontrolovat probíhající aktualizace](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Můžete také kontrolovat konkrétního hostitele pomocí **Virtual Machines** nebo vlastností vyhrazeného hostitele. 

![Snímek obrazovky zobrazující zvýrazněný stav údržby](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Instalace aktualizací

Můžete použít nedokončené aktualizace na vyžádání pomocí **Virtual Machines**. Na stránce Podrobnosti o virtuálním počítači klikněte na **Údržba** a hned klikněte na **použít údržbu**.

![Snímek obrazovky ukazující, jak použít nedokončené aktualizace](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Ověřte stav použití aktualizací. 

Můžete se podívat na průběh aktualizací pro konfiguraci v části **Konfigurace údržby** nebo pomocí **Virtual Machines**. Na stránce Podrobnosti o virtuálním počítači klikněte na **Údržba**. V následujícím příkladu **stav údržby** ukazuje, **že probíhá aktualizace.**

![Snímek obrazovky ukazující, jak kontrolovat stav probíhajících aktualizací](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Odstraní konfiguraci údržby.

Pokud chcete konfiguraci odstranit, otevřete podrobnosti konfigurace a klikněte na **Odstranit**.

![Snímek obrazovky, který ukazuje, jak odstranit konfiguraci.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).
