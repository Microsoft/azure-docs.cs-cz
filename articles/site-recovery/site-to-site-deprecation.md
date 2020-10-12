---
title: Vyřazení zotavení po havárii mezi zákaznickými weby spravovanými zákazníkem (s VMM) pomocí Azure Site Recovery | Microsoft Docs
description: Podrobnosti o nadcházejících vyřazení DR mezi weby vlastněné zákazníkem pomocí technologie Hyper-V a mezi lokalitami spravovanými SCVMM do Azure a alternativními možnostmi
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421837"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Vyřazení zotavení po havárii mezi zákaznickými weby spravovanými zákazníkem (s VMM) pomocí Azure Site Recovery

Tento článek popisuje nadcházející plán vyřazení, odpovídající důsledky a alternativní možnosti, které jsou zákazníkům k dispozici pro následující scénář:

DR mezi weby vlastněné zákazníkem spravované pomocí System Center Virtual Machine Manager (SCVMM) pomocí Site Recovery

> [!IMPORTANT]
> Zákazníkům se doporučuje provést nápravné kroky, aby se předešlo jakémukoli narušení jejich prostředí. 

## <a name="what-changes-should-you-expect"></a>Jaké změny byste měli očekávat?

- Od března 2020 obdržíte oznámení o Azure Portal & e-mailové komunikaci s nadcházejícím vyřazením replikace virtuálních počítačů Hyper-V mezi lokalitami. Vyřazení je plánováno na březen 2023.

- Pokud máte existující konfiguraci, nebude to mít žádný vliv na nastavení.

- Když jsou scénáře zastaralé, pokud zákazník nenásleduje alternativní přístupy, může dojít k přerušení stávajících replikací. Zákazníci nebudou moct zobrazit, spravovat ani provádět žádné operace, které se týkají programu DR, prostřednictvím prostředí Azure Site Recovery v Azure Portal.
 
## <a name="alternatives"></a>Alternativy 

Níže jsou uvedené alternativy, ze kterých si zákazník může vybrat, aby se zajistilo, že jejich strategie zotavení po havárii není ovlivněná, když je scénář zastaralý. 

- Možnost 1 (doporučeno): vyberte, pokud chcete [začít používat Azure jako cíl pro zotavení po havárii](hyper-v-vmm-azure-tutorial.md).


- Možnost 2: vyberte, pokud chcete pokračovat v replikaci mezi lokalitami pomocí základního [řešení repliky technologie Hyper-V](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), ale nebudete moct spravovat konfigurace DR pomocí Azure Site Recovery v Azure Portal. 


## <a name="remediation-steps"></a>Postup odstranění problému

Pokud se rozhodnete, že zvolíte možnost 1, proveďte následující kroky:

1. [Zakažte ochranu všech virtuálních počítačů přidružených k VMMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Použijte možnost **Zakázat replikaci a odebrat** nebo spustit skripty uvedené k tomu, abyste zajistili, že se vyčistí nastavení replikace místně. 

2. [Zrušte registraci všech serverů VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) z konfigurace replikace mezi lokalitami.

3. [Připravte prostředky Azure](tutorial-prepare-azure-for-hyperv.md) pro povolení replikace vašich virtuálních počítačů.
4. [Příprava místních serverů Hyper-V](hyper-v-prepare-on-premises-tutorial.md)
5. [Nastavení replikace pro virtuální počítače v cloudu VMM](hyper-v-vmm-azure-tutorial.md)
6. Volitelné, ale doporučené: [spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

Pokud se rozhodnete použít možnost 2 z repliky technologie Hyper-V, proveďte následující kroky:

1. V části **chráněné položky**  >  **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
2. V **Zakázat replikaci**vyberte **Odebrat**.

    Tím se odebere replikovaná položka z Azure Site Recovery (fakturace je zastavená). Konfigurace replikace na místním virtuálním počítači **se** nevyčistí. 

## <a name="next-steps"></a>Další kroky
Naplánujte si vyřazení a vyberte jinou možnost, která je nejvhodnější pro vaši infrastrukturu a podnikání. V případě, že máte dotazy týkající se tohoto přístupu, nahlaste se podpora Microsoftu

