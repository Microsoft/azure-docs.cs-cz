---
title: Přesunutí konfigurace Azure Site Recovery do jiné oblasti Azure | Microsoft Docs
description: Pokyny pro přesunutí konfigurace Site Recovery do jiné oblasti Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708295"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Přesunutí trezoru Recovery Services a Azure Site Recovery konfigurace do jiné oblasti Azure

Existují různé scénáře, ve kterých byste chtěli přesunout stávající prostředky Azure z jedné oblasti do jiné správy, důvody pro řízení nebo v důsledku fúze nebo akvizice společnosti. Jeden ze souvisejících prostředků, které byste mohli chtít přesunout při přesunu virtuálních počítačů Azure, je konfigurace zotavení po havárii pro stejné služby. Neexistuje žádný první způsob, jak přesunout existující konfiguraci zotavení po havárii z jedné oblasti do druhé. To je v podstatě, protože jste nakonfigurovali cílovou oblast na základě vaší zdrojové oblasti virtuálních počítačů a rozhodnete se ji změnit, dříve existující konfigurace cílové oblasti se nedají znovu použít a musí se resetovat. Tento článek popisuje podrobný postup, jak znovu nakonfigurovat nastavení zotavení po havárii a přejít na jinou oblast.

V tomto dokumentu budete:

> [!div class="checklist"]
> * Ověření požadavků pro přesun
> * Identifikujte prostředky, které používal Azure Site Recovery 
> * Zákaz replikace
> * Odstranit prostředky 
> * Znovu nastavte Site Recovery na základě nové zdrojové oblasti virtuálních počítačů.

> [!IMPORTANT]
> V současné době neexistuje způsob, jak přesunout trezor Recovery Services a konfiguraci zotavení po havárii, protože se jedná o jinou oblast, tento dokument provede zákazníka procesem zakázání replikace a resetuje ho v nové oblasti.

## <a name="prerequisites"></a>Požadavky

- Před pokusem o přesunutí virtuálních počítačů Azure do jiné oblasti nezapomeňte odebrat a odstranit konfiguraci zotavení po havárii. 

> [!NOTE]
> Pokud je vaše nová cílová oblast pro virtuální počítač Azure stejná jako cílová oblast zotavení po havárii, můžete použít stávající konfiguraci replikace a přejít na základě kroků uvedených [tady](azure-to-azure-tutorial-migrate.md) . 

- Ujistěte se, že jste provedli informace o tom, že se jedná o rozhodnutí o tom, že se účastníci informují o tom, že váš virtuální počítač nebude po dokončení přesunu virtuálního počítače chráněný. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifikujte prostředky, které používal Azure Site Recovery
Tento krok doporučujeme provést předtím, než přejdete k následujícímu, protože bude snazší identifikovat relevantní prostředky, i když jsou virtuální počítače stále replikovány.

Pro každý virtuální počítač Azure, který se replikuje, přejděte do části **chráněné položky** > **vlastnosti** **replikované položky**>a Identifikujte následující zdroje.

- Cílová skupina prostředků
- Účet úložiště mezipaměti
- Cílový účet úložiště (v případě nespravovaného virtuálního počítače Azure založeného na disku) 
- Cílová síť


## <a name="disable-existing-disaster-recovery-configuration"></a>Zakázat existující konfiguraci zotavení po havárii

1. Přejít do **trezoru Recovery Services** 
2.  V části **chráněné položky** > **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
3. Opakujte tento postup pro všechny virtuální počítače, které chcete přesunout.
> [!NOTE]
> Služba mobility nebude odinstalována z chráněných serverů, je nutné ji odinstalovat ručně. Pokud plánujete Server chránit znovu, můžete přeskočit odinstalaci služby mobility.

## <a name="delete-the-resources"></a>Odstranit prostředky

1. Přejděte do trezoru **Recovery Services** .
2. Klikněte na **Odstranit** .
3. Pokračujte v odstraňování všech dalších prostředků identifikovaných v[ předchozím kroku](#identify-the-resources-that-were-used-by-azure-site-recovery) .
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Přesun virtuálních počítačů Azure do nové cílové oblasti

Postupujte podle kroků uvedených níže v závislosti na vašem požadavku na přesun virtuálních počítačů Azure do cílové oblasti.

- [Přesun virtuálních počítačů Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)
- [Přesun virtuálních počítačů Azure do zón dostupnosti](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Nastavte znovu Site Recovery v závislosti na nové zdrojové oblasti pro virtuální počítače.

Nakonfigurujte zotavení po havárii pro virtuální počítače Azure, které se přesunuly do nové oblasti, pomocí kroků uvedených [tady](azure-to-azure-tutorial-enable-replication.md) .
