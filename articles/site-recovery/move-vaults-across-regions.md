---
title: Přesunutí trezoru obnovení webu Azure do jiné oblasti
description: Popisuje, jak přesunout trezor služby Recovery Services (Azure Site Recovery) do jiné oblasti Azure.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090294"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Přesunutí trezoru služby Recovery Services a konfigurace obnovení webu Azure do jiné oblasti Azure

Existují různé scénáře, ve kterých můžete chtít přesunout existující prostředky Azure z jedné oblasti do druhé. Příklady jsou pro možnosti správy, z důvodů správy nebo z důvodu fúzí a akvizic společností. Jedním ze souvisejících prostředků, které můžete chtít přesunout při přesunutí virtuálních počítačů Azure je konfigurace zotavení po havárii. 

Neexistuje žádný prvotřídní způsob, jak přesunout existující konfiguraci zotavení po havárii z jedné oblasti do druhé. Důvodem je, že jste nakonfigurovali cílovou oblast na základě zdrojové oblasti virtuálních počítače. Pokud se rozhodnete změnit zdrojovou oblast, dříve existující konfigurace cílové oblasti nelze znovu použít a musí být resetována. Tento článek definuje postupný proces pro překonfiguraci nastavení zotavení po havárii a jeho přesunutí do jiné oblasti.

V tomto dokumentu:

> [!div class="checklist"]
> * Ověřte požadavky pro přesun.
> * Identifikujte prostředky, které byly použity azure site recovery.
> * Zakažte replikaci.
> * Odstraňte prostředky.
> * Nastavte obnovení webu na základě nové zdrojové oblasti pro virtuální počítač.

> [!IMPORTANT]
> V současné době neexistuje žádný prvotřídní způsob, jak přesunout trezor služby Recovery Services a konfiguraci zotavení po havárii, stejně jako do jiné oblasti. Tento článek vás provede procesem zakázání replikace a jeho nastavení v nové oblasti.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že odebrat a odstranit konfiguraci zotavení po havárii před pokusem o přesunutí virtuálních počítačů Azure do jiné oblasti. 

  > [!NOTE]
  > Pokud je vaše nová cílová oblast pro virtuální počítač Azure je stejná jako cílová oblast zotavení po havárii, můžete použít stávající konfiguraci replikace a přesunout ji. Postupujte podle kroků v [části Přesunutí virtuálních počítačů Azure IaaS do jiné oblasti Azure](azure-to-azure-tutorial-migrate.md).

- Ujistěte se, že činíte informované rozhodnutí a že jsou informovány zúčastněné strany. Váš virtuální počítač nebude chráněný před katastrofami, dokud nebude přesunutí virtuálního počítače dokončeno.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifikace prostředků, které byly použity azure site recovery
Doporučujeme provést tento krok dříve, než přejdete k dalšímu kroku. Je jednodušší identifikovat příslušné prostředky, zatímco virtuální chod y replikují.

Pro každý virtuální počítač Azure, který se replikuje, přejděte na**vlastnosti** **replikovaných položek** >  **chráněných položek** > a identifikujte následující prostředky:

- Cílová skupina prostředků
- Účet úložiště mezipaměti
- Cílový účet úložiště (v případě nespravovaného virtuálního počítače Azure založeného na disku) 
- Cílová síť


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Zakázání existující konfigurace zotavení po havárii

1. Přejděte do trezoru služby recovery Services.
2. V**části Replikované položky chráněných** **položek** > klepněte pravým tlačítkem myši na počítač a vyberte **zakázat replikaci**.
3. Tento krok opakujte pro všechny virtuální ho doutchačné, které chcete přesunout.

> [!NOTE]
> Služba mobility nebude odinstalována z chráněných serverů. Je nutné jej odinstalovat ručně. Pokud plánujete server znovu chránit, můžete odinstalovat službu mobility.

## <a name="delete-the-resources"></a>Odstranění prostředků

1. Přejděte do trezoru služby recovery Services.
2. Vyberte **Odstranit**.
3. Odstraňte všechny ostatní prostředky, které jste [dříve identifikovali](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Přesunutí virtuálních počítačů Azure do nové cílové oblasti

Postupujte podle kroků v těchto článcích na základě vašeho požadavku přesunout virtuální počítače Azure do cílové oblasti:

- [Přesun virtuálních počítačů Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)
- [Přesun virtuálních počítačů Azure do zón dostupnosti](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Nastavení obnovení webu na základě nové zdrojové oblasti pro virtuální počítač.

Nakonfigurujte zotavení po havárii pro virtuální počítače Azure, které byly přesunuty do nové oblasti podle kroků v nastavení [zotavení po havárii pro virtuální počítače Azure](azure-to-azure-tutorial-enable-replication.md).
