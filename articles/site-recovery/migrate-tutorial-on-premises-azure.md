---
title: Migrace místních počítačů do Azure pomocí Azure Site Recovery
description: Tento článek popisuje migraci místních počítačů do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5fa0f05f37164c24ba6441d0c4cb2abc59f331d7
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053049"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrace místních počítačů do Azure


Tento článek popisuje, jak migrovat místní počítače do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Obecně se Site Recovery používá ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure. Dá se ale použít i k migraci. Migrace používá stejný postup jako zotavení po havárii s jednou výjimkou. V rámci migrace je v posledním kroku selhání počítačů, které jsou na vaší místní lokalitě. Na rozdíl od zotavení po havárii nemůžete navrátit služby po obnovení do místního prostředí ve scénáři migrace.


V tomto kurzu se dozvíte, jak migrovat místní virtuální počítače a fyzické servery do Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení zdrojového a cílového prostředí pro migraci
> * Nastavení zásady replikace
> * Povolení replikace
> * Spuštění testu migrace za účelem ověření, že vše funguje podle očekávání
> * Spuštění jednorázového převzetí služeb při selhání do Azure


> [!TIP]
> Teď můžete migrovat místní servery do Azure pomocí služby Azure Migrate. [Další informace](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Než začnete

Zařízení exportovaná pomocí ovladačů paravirtualizovanými se nepodporují.


## <a name="prepare-azure-and-on-premises"></a>Příprava Azure a místního prostředí

1. Připravte Azure podle pokynů v [tomto článku](tutorial-prepare-azure.md). I když tento článek popisuje přípravné kroky pro zotavení po havárii, jsou kroky platné i pro migraci.
2. Příprava místních serverů [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) Pokud migrujete fyzické počítače, nemusíte nic připravovat. Stačí pouze ověřit [matrici podpory](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.
1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce prostředku klikněte na **Site Recovery** > **Příprava infrastruktury** > **Cíl ochrany**.
3. V části **Cíl ochrany** vyberte, co chcete migrovat.
    - **VMware:** Vyberte **Do Azure** > **Ano, s VMWare vSphere Hypervisor**.
    - **Fyzický počítač:** Vyberte **Do Azure** > **Nevirtualizované/jiné**.
    - **Hyper-V:** Vyberte **Do Azure** > **Ano, s technologií Hyper-V**. Pokud jsou virtuální počítače Hyper-V spravované nástrojem VMM, vyberte **Ano**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

**Scénář** | **Podrobnosti**
--- | --- 
VMware | Nastavte [zdrojové prostředí](vmware-azure-set-up-source.md)a nastavte [konfigurační server](vmware-azure-deploy-configuration-server.md).
Fyzický počítač | [Nastavte](physical-azure-set-up-source.md) zdrojové prostředí a konfigurační server.
Hyper-V | Nastavení [zdrojového prostředí](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Nastavte [zdrojové prostředí](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) pro Hyper-V nasazené s nástrojem System Center VMM.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery kontroluje prostředky Azure.
    - Pokud migrujete virtuální počítače VMware nebo fyzické servery, Site Recovery ověříte, že máte síť Azure, ve které budou virtuální počítače Azure při jejich vytvoření po převzetí služeb při selhání k dispozici.
    - Pokud migrujete virtuální počítače Hyper-V, Site Recovery ověříte, že máte kompatibilní účet úložiště Azure a síť.
4. Pokud migrujete virtuální počítače Hyper-V spravované nástrojem System Center VMM, nastavte [mapování sítě](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

**Scénář** | **Podrobnosti**
--- | --- 
VMware | Nastavte [Zásady replikace](vmware-azure-set-up-replication.md) pro virtuální počítače VMware.
Fyzický počítač | Nastavte [Zásady replikace](physical-azure-disaster-recovery.md#create-a-replication-policy) pro fyzické počítače.
Hyper-V | Nastavení [zásad replikace](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Nastavte [Zásady replikace](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) pro Hyper-V nasazené s nástrojem System Center VMM.

## <a name="enable-replication"></a>Povolení replikace

**Scénář** | **Podrobnosti**
--- | --- 
VMware | [Povolení replikace](vmware-azure-enable-replication.md) pro virtuální počítače VMware
Fyzický počítač | [Povolte replikaci](physical-azure-disaster-recovery.md#enable-replication) pro fyzické počítače.
Hyper-V | [Povolení replikace](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Povolte replikaci](hyper-v-vmm-azure-tutorial.md#enable-replication) pro Hyper-V nasazenou pomocí nástroje System Center VMM.


## <a name="run-a-test-migration"></a>Spuštění testu migrace

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Nastavení šifrovacího klíče není pro tento scénář podstatné.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
5. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. Provede následující akce:

   - Dokončí proces migrace, zastaví replikaci pro místní virtuální počítač a zastaví Site Recovery fakturace pro virtuální počítač.
   - Tento krok vyčistí data replikace. Migrované virtuální počítače se neodstraňují.

     ![Dokončení migrace](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Replikace virtuálního počítače se před spuštěním převzetí služeb při selhání zastaví. Pokud převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se nebude znovu replikovat.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího testování doby převzetí služeb při selhání pro fyzické servery, počítače se systémem VMware Linux, virtuální počítače VMware, které nemají povolenou službu DHCP, a virtuální počítače VMware, které nemají následující ovladače pro spouštění: storvsc, VMBus, storflt, Intelide, ATAPI.

## <a name="after-migration"></a>Po migraci

Po migraci počítačů do Azure musíte provést celou řadu kroků.

Některé kroky se dají automatizovat jako součást migračního procesu pomocí integrovaných automatizačních skriptů v [plánech obnovení](site-recovery-runbook-automation.md).   


### <a name="post-migration-steps-in-azure"></a>Kroky po migraci v Azure

- Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery. 
- U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
- [Agent virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) spravuje interakci virtuálního počítače s kontrolerem prostředků infrastruktury Azure. Agent se vyžaduje u některých služeb Azure, jako jsou Azure Backup, Site Recovery a Azure Security.
    - Pokud migrujete počítače VMware a fyzické servery, instalační program služby mobility nainstaluje dostupného agenta virtuálního počítače Azure na počítače s Windows. Na linuxových virtuálních počítačích doporučujeme nainstalovat agenta po převzetí služeb při selhání.
    - Pokud migrujete virtuální počítače Azure do sekundární oblasti, musí být agent virtuálního počítače Azure zřízen na virtuálním počítači ještě před migrací.
    - Pokud migrujete virtuální počítače Hyper-V do Azure, nainstalujte agenta virtuálního počítače Azure na virtuální počítač Azure po migraci.
- Ručně odeberte všechny agenty nebo poskytovatele služby Site Recovery z virtuálního počítače. Pokud migrujete virtuální počítače VMware nebo fyzické servery, odinstalujte službu mobility z virtuálního počítače.
- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](azure-to-azure-quickstart.md).
- Pro zvýšení zabezpečení:
    - Uzamkněte a omezte přístup příchozího provozu pomocí [správy za běhu]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) ve službě Azure Security Center.
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Pro monitorování a správu:
    - Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.

### <a name="post-migration-steps-on-premises"></a>Místní kroky po migraci

- Přesuňte provoz aplikace do aplikace, která běží na instanci migrovaného virtuálního počítače Azure.
- Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
- Odeberte místní virtuální počítače ze záloh.
- Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure.




## <a name="next-steps"></a>Další kroky

V tomto kurzu jste migrovali místní virtuální počítače na virtuální počítače Azure. současné

> [!div class="nextstepaction"]
> [Nastavte zotavení po havárii](azure-to-azure-replicate-after-migration.md) do sekundární oblasti Azure pro virtuální počítače Azure.

  
