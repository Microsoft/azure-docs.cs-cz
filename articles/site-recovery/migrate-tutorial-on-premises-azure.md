---
title: Migrace místních počítačů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje migraci místních počítačů do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fc15db91b8f4cc6dbdecd0e7321abdbf81744f08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193893"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrace místních počítačů do Azure


Tento článek popisuje migraci místních počítačů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Obecně platí Site Recovery se používá ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure. Však ho můžete také použít pro migraci. Migrace používá stejný postup jako zotavení po havárii s jednou výjimkou. Při migraci převzetí služeb počítače při selhání z místní lokality se v posledním kroku. Na rozdíl od zotavení po havárii nemůžete po obnovení zpět do místního ve scénáři migrace.


V tomto kurzu se dozvíte, jak migrovat místní virtuální počítače a fyzické servery do Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení zdrojového a cílového prostředí pro migraci
> * Nastavení zásady replikace
> * Povolení replikace
> * Spuštění testu migrace za účelem ověření, že vše funguje podle očekávání
> * Spuštění jednorázového převzetí služeb při selhání do Azure


> [!TIP]
> Služba Azure Migrate je teď nabízí náhled pro nové prostředí bez agentů pro migrace virtuálních počítačů VMware do Azure. [Více informací najdete](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>Než začnete

Všimněte si, že zařízení exportovaná paravirtualizovanými ovladači se nepodporují.


## <a name="prepare-azure-and-on-premises"></a>Příprava Azure i v místním prostředí

1. Příprava Azure, jak je popsáno v [v tomto článku](tutorial-prepare-azure.md). I když tento článek popisuje přípravné kroky pro zotavení po havárii, postup platí také pro migraci.
2. Příprava místních serverů [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) Pokud migrujete fyzické počítače, nemusíte připravovat nic dalšího. Právě ověřte, [systém podpory replikace z](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Výběr cíle replikace

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.
1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce prostředku klikněte na **Site Recovery** > **Příprava infrastruktury** > **Cíl ochrany**.
3. V části **Cíl ochrany** vyberte, co chcete migrovat.
    - **VMware**: Vyberte **do Azure** > **Ano, s VMWare vSphere Hypervisor**.
    - **Fyzický počítač**: Vyberte **do Azure** > **nevirtualizované/jiné**.
    - **Hyper-V**: Vyberte **do Azure** > **Ano, s Hyper-V**. Pokud jsou virtuální počítače Hyper-V spravované nástrojem VMM, vyberte **Ano**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

**Scénář** | **Podrobnosti**
--- | --- 
VMware | Nastavit [zdrojové prostředí](vmware-azure-set-up-source.md)a nastavit [konfigurační server](vmware-azure-deploy-configuration-server.md).
Fyzický počítač | [Nastavit](physical-azure-set-up-source.md) zdrojovém prostředí a konfigurace serveru.
Hyper-V | Nastavit [zdrojové prostředí](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Nastavit [zdrojové prostředí](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) pro Hyper-V nasazené pomocí nástroje System Center VMM.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery zkontroluje prostředky Azure.
    - Pokud migrujete virtuální počítače VMware nebo fyzické servery, Site Recovery ověřuje, že máte síť Azure, ve které virtuální počítače Azure budou umístěné při jejich vytvoření po převzetí služeb při selhání.
    - Pokud migrujete virtuální počítače Hyper-V, Site Recovery ověřuje, že máte účet kompatibilní úložiště Azure a síť.
4. Pokud migrujete virtuální počítače Hyper-V jsou spravovány nástrojem System Center VMM, nastavte si [mapování sítě](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

**Scénář** | **Podrobnosti**
--- | --- 
VMware | Nastavení [zásady replikace](vmware-azure-set-up-replication.md) pro virtuální počítače VMware.
Fyzický počítač | Nastavení [zásady replikace](physical-azure-disaster-recovery.md#create-a-replication-policy) pro fyzické počítače.
Hyper-V | Nastavení [zásady replikace](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Nastavení [zásady replikace](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) pro Hyper-V nasazené pomocí nástroje System Center VMM.

## <a name="enable-replication"></a>Povolení replikace

**Scénář** | **Podrobnosti**
--- | --- 
VMware | [Povolení replikace](vmware-azure-enable-replication.md) pro virtuální počítače VMware
Fyzický počítač | [Povolení replikace](physical-azure-disaster-recovery.md#enable-replication) pro fyzické počítače.
Hyper-V | [Povolení replikace](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Povolení replikace](hyper-v-vmm-azure-tutorial.md#enable-replication) pro Hyper-V nasazené pomocí nástroje System Center VMM.


## <a name="run-a-test-migration"></a>Spuštění testu migrace

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Nastavení šifrovacího klíče není pro tento scénář podstatné.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
5. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. To provede následující akce:

   - Proce migrace, zastaví se replikace místních virtuálních počítačů a zastaví se fakturace služby Site Recovery pro virtuální počítač.
   - Tento krok vyčištění dat replikace. Nedojde k odstranění migrovaných virtuálních počítačů.

     ![Dokončení migrace](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nepřerušujte převzetí služeb při selhání v průběhu**: Před spuštěním převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího trvání testovacího převzetí služeb při selhání u fyzických serverů, počítačů VMware s Linuxem, virtuálních počítačů VMware, které nemají povolenou službu DHCP, a virtuálních počítačů VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.

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
- Ručně odeberte všechny agenty nebo poskytovatele služby Site Recovery z virtuálního počítače. Pokud provádíte migraci virtuálních počítačů VMware nebo fyzické servery, z virtuálního počítače odinstalujte službu Mobility.
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




## <a name="next-steps"></a>Další postup

V tomto kurzu jste migrovali místní virtuální počítače na virtuální počítače Azure. Ihned

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii](azure-to-azure-replicate-after-migration.md) do sekundární oblasti Azure pro virtuální počítače Azure.

  
