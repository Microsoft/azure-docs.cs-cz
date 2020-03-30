---
title: Vyřazení zotavení po havárii mezi weby spravovanými zákazníky (s VMM) pomocí Azure Site Recovery | Dokumenty společnosti Microsoft
description: Podrobnosti o nadcházejícím vyřazení zotavení po havárii mezi weby vlastněnými zákazníky pomocí technologie Hyper-V a mezi weby spravovanými společností SCVMM do Azure a alternativními možnostmi
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661666"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Vyřazení zotavení po havárii mezi weby spravovanými zákazníky (s VMM) pomocí Azure Site Recovery

Tento článek popisuje nadcházející plán vyřazení, odpovídající důsledky a alternativní možnosti, které jsou k dispozici pro zákazníky pro následující scénář:

Zotavení po havárii mezi weby vlastněnými zákazníky spravovanými správcem virtuálních strojů System Center (SCVMM) pomocí site recovery

> [!IMPORTANT]
> Zákazníkům se doporučuje, aby co nejdříve podnikli nápravná opatření, aby nedošlo k narušení jejich prostředí. 

## <a name="what-changes-should-you-expect"></a>Jaké změny byste měli očekávat?

- Od března 2020 obdržíte oznámení na portálu Azure & e-mailovou komunikaci s nadcházejícím vyřazením replikace virtuálních počítačů Hyper-V od lokality. Toto rozhodnutí je plánováno na březen 2023.

- Pokud máte existující konfiguraci, nebude mít žádný vliv na nastavení.

- Jakmile jsou scénáře zastaralé, pokud zákazník následuje alternativní přístupy, existující replikace může dojít k narušení. Zákazníci nebudou moct zobrazit, spravovat ani pročítat žádné operace související s DR prostřednictvím prostředí Azure Site Recovery na webu Azure Portal.
 
## <a name="alternatives"></a>Alternativy 

Níže jsou uvedeny alternativy, které si zákazník může vybrat, aby zajistily, že jejich strategie zotavení po havárii není ovlivněna, jakmile je scénář zastaralá. 

- Možnost 1 (doporučeno): Zvolte, zda [chcete začít používat Azure jako cíl zotavení po havárii](hyper-v-vmm-azure-tutorial.md).


- Možnost 2: Zvolte pokračování replikace mezi lokalitami pomocí základního [řešení repliky Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), ale nebudete moci spravovat konfigurace zotavení po havárii pomocí Azure Site Recovery na webu Azure portal. 


## <a name="remediation-steps"></a>Kroky nápravy

Pokud se rozhodnete zvolit možnost 1, proveďte následující kroky:

1. [Zakázat ochranu všech virtuálních počítačů přidružených k vms](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Použijte možnost **Zakázat replikaci a odebrat** nebo spusťte uvedené skripty, abyste zajistili, že místní nastavení replikace bude vyčištěno. 

2. [Zrušení registrace všech serverů VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) z konfigurace replikace mezi lokalitami.

3. [Připravte prostředky Azure](tutorial-prepare-azure-for-hyperv.md) pro povolení replikace virtuálních počítačů.
4. [Příprava místních serverů Hyper-V](hyper-v-prepare-on-premises-tutorial.md)
5. [Nastavení replikace pro virtuální množinu v cloudu VMM](hyper-v-vmm-azure-tutorial.md)
6. Volitelné, ale doporučené: [Spuštění dr.](tutorial-dr-drill-azure.md)

Pokud se rozhodnete použít možnost 2 použití repliky technologie Hyper-V, proveďte následující kroky:

1. V **části Replikované položky chráněné** > **položky**klepněte pravým tlačítkem myši na počítač > **Zakázat replikaci**.
2. V **popřípadě Zakázat replikaci**vyberte **Odebrat**.

    Tím odeberete replikovanou položku z Azure Site Recovery (fakturace se zastaví). Konfigurace replikace v místním virtuálním počítači **nebude** vyčištěna. 

## <a name="next-steps"></a>Další kroky
Naplánujte si vyřazení a zvolte alternativní možnost, která je nejvhodnější pro vaši infrastrukturu a podnikání. V případě, že máte nějaké dotazy týkající se tohoto, oslovte podporu společnosti Microsoft

