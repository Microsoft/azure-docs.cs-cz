---
title: Přesunutí úložiště Azure Site Recovery do jiné oblasti
description: Popisuje, jak přesunout Recovery Services trezor (Azure Site Recovery) do jiné oblasti Azure.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87422636"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Přesunutí trezoru Recovery Services a konfigurace Azure Site Recovery do jiné oblasti Azure

Existují různé scénáře, ve kterých byste mohli chtít přesunout existující prostředky Azure z jedné oblasti do druhé. Příklady jsou možnosti správy, důvody zásad správného řízení nebo z důvodu fúze a akvizice společnosti. Jeden ze souvisejících prostředků, které můžete chtít přesunout při přesunu virtuálních počítačů Azure, je konfigurace zotavení po havárii. 

Neexistuje žádná první třída způsob, jak přesunout existující konfiguraci zotavení po havárii z jedné oblasti do druhé. Důvodem je to, že jste nakonfigurovali cílovou oblast na základě vaší zdrojové oblasti virtuálních počítačů. Pokud se rozhodnete změnit zdrojovou oblast, dříve existující konfigurace cílové oblasti se nedají znovu použít a musí se resetovat. Tento článek popisuje podrobný postup, jak znovu nakonfigurovat nastavení zotavení po havárii a přesunout ho do jiné oblasti.

V tomto dokumentu budete:

> [!div class="checklist"]
> * Ověřte požadavky pro přesun.
> * Identifikujte prostředky, které byly používány Azure Site Recovery.
> * Zakažte replikaci.
> * Odstraňte prostředky.
> * Nastavte Site Recovery v závislosti na nové zdrojové oblasti pro virtuální počítače.

> [!IMPORTANT]
> V současné době neexistuje žádný první způsob, jak přesunout Recovery Services trezor a konfiguraci zotavení po havárii, protože se jedná o jinou oblast. Tento článek vás provede procesem zakázání replikace a její nastavení v nové oblasti.

## <a name="prerequisites"></a>Požadavky

- Před pokusem o přesunutí virtuálních počítačů Azure do jiné oblasti nezapomeňte odebrat a odstranit konfiguraci zotavení po havárii. 

  > [!NOTE]
  > Pokud je vaše nová cílová oblast pro virtuální počítač Azure stejná jako cílová oblast zotavení po havárii, můžete použít stávající konfiguraci replikace a přesunout ji. Postupujte podle kroků v části [Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure](azure-to-azure-tutorial-migrate.md).

- Ujistěte se, že provedete rozhodnutí v informování a že se zúčastněné strany informují. Váš virtuální počítač nebude chráněný proti katastrofám, dokud se přesun virtuálního počítače nedokončí.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifikujte prostředky, které používal Azure Site Recovery
Doporučujeme, abyste provedli tento krok předtím, než přejdete k dalšímu. Při replikaci virtuálních počítačů je snazší identifikovat relevantní prostředky.

Pro každý virtuální počítač Azure, který se právě replikuje, můžete přejít na **chráněné položky**  >  vlastnosti **replikované položky**  >   a určit následující prostředky:

- Cílová skupina prostředků
- Účet úložiště mezipaměti
- Cílový účet úložiště (v případě nespravovaného virtuálního počítače Azure založeného na disku) 
- Cílová síť


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Zakázat existující konfiguraci zotavení po havárii

1. Přejít do trezoru Recovery Services.
2. V části **chráněné položky**  >  **replikované** položky klikněte pravým tlačítkem na počítač a vyberte **Zakázat replikaci**.
3. Tento krok opakujte pro všechny virtuální počítače, které chcete přesunout.

> [!NOTE]
> Služba mobility se z chráněných serverů neodinstaluje. Je nutné ji odinstalovat ručně. Pokud plánujete Server chránit znovu, můžete přeskočit odinstalaci služby mobility.

## <a name="delete-the-resources"></a>Odstranit prostředky

1. Přejít do trezoru Recovery Services.
2. Vyberte **Odstranit**.
3. Odstraňte všechny další prostředky, které jste [předtím identifikovali](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Přesun virtuálních počítačů Azure do nové cílové oblasti

Postupujte podle kroků v těchto článcích v závislosti na vašem požadavku na přesun virtuálních počítačů Azure do cílové oblasti:

- [Přesun virtuálních počítačů Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)
- [Přesun virtuálních počítačů Azure do Zóny dostupnosti](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Nastavení Site Recovery v závislosti na nové zdrojové oblasti pro virtuální počítače

Nakonfigurujete zotavení po havárii pro virtuální počítače Azure, které se přesunuly do nové oblasti, podle kroků uvedených v části [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-tutorial-enable-replication.md).
