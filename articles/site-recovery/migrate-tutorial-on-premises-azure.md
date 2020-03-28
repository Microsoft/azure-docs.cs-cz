---
title: Migrace místních počítačů pomocí Azure Site Recovery
description: Tento článek popisuje migraci místních počítačů do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 24015810a295ef88b7d3e63bfc464ddddef6b55f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73939623"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrace místních počítačů do Azure


Tento článek popisuje, jak migrovat místní počítače do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Obecně se site recovery používá ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure. Lze jej však také použít pro migraci. Migrace používá stejné kroky jako zotavení po havárii s jednou výjimkou. V případě migrace je posledním krokem počítače s selháním z místního webu. Na rozdíl od zotavení po havárii nelze při selhání zpět do místního prostředí ve scénáři migrace.


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

Upozorňujeme, že zařízení exportovaná paravirtualizovanými ovladači nejsou podporována.


## <a name="prepare-azure-and-on-premises"></a>Příprava Azure a místně

1. Připravte Azure, jak je popsáno v [tomto článku](tutorial-prepare-azure.md). Přestože tento článek popisuje kroky přípravy pro zotavení po havárii, kroky jsou platné také pro migraci.
2. Příprava místních serverů [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) Pokud migrujete fyzické stroje, nemusíte nic připravovat. Stačí ověřit [matici podpory](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Výběr cíle ochrany

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.
1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce Zdroje klikněte na **položku Site Recovery** > **Prepare Infrastructure** > **Protection .**
3. V části **Cíl ochrany** vyberte, co chcete migrovat.
    - **VMware:** Vyberte **Do Azure** > **Ano, s VMWare vSphere Hypervisor**.
    - **Fyzický počítač:** Vyberte **Do Azure** > **Nevirtualizované/jiné**.
    - **Hyper-V:** Vyberte **Do Azure** > **Ano, s technologií Hyper-V**. Pokud jsou virtuální počítače Hyper-V spravované nástrojem VMM, vyberte **Ano**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

**Scénář** | **Podrobnosti**
--- | --- 
VMware | Nastavte [zdrojové prostředí](vmware-azure-set-up-source.md)a nastavte [konfigurační server](vmware-azure-deploy-configuration-server.md).
Fyzický stroj | [Nastavte](physical-azure-set-up-source.md) zdrojové prostředí a konfigurační server.
Hyper-V | Nastavení [zdrojového prostředí](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Nastavte [zdrojové prostředí](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) pro technologie Hyper-V nasazené pomocí nástroje VMM system center.

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit cíl infrastruktury** > **Target**a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery zkontroluje prostředky Azure.
    - Pokud migrujete virtuální počítače VMware nebo fyzické servery, obnovení webu ověří, že máte síť Azure, ve které budou virtuální počítače Azure umístěny, když se vytvoří po převzetí služeb při selhání.
    - Pokud migrujete virtuální počítače Hyper-V, obnovení webu ověří, že máte kompatibilní účet úložiště Azure a síť.
4. Pokud migrujete virtuální sítě Hyper-V spravované programem System Center VMM, nastavte [mapování sítě](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

**Scénář** | **Podrobnosti**
--- | --- 
VMware | Nastavte [zásady replikace](vmware-azure-set-up-replication.md) pro virtuální hody VMware.
Fyzický stroj | Nastavte [zásady replikace](physical-azure-disaster-recovery.md#create-a-replication-policy) pro fyzické počítače.
Hyper-V | Nastavení [zásad replikace](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Nastavte [zásady replikace](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) pro technologie Hyper-V nasazené pomocí nástroje VMM system center.

## <a name="enable-replication"></a>Povolení replikace

**Scénář** | **Podrobnosti**
--- | --- 
VMware | [Povolení replikace](vmware-azure-enable-replication.md) pro virtuální počítače VMware
Fyzický stroj | [Povolte replikaci](physical-azure-disaster-recovery.md#enable-replication) pro fyzické počítače.
Hyper-V | [Povolení replikace](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Povolte replikaci](hyper-v-vmm-azure-tutorial.md#enable-replication) pro technologii Hyper-V nasazenou pomocí nástroje VMM system center.


## <a name="run-a-test-migration"></a>Spuštění testu migrace

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V **nastavení** > **Replikované položky** klepněte na počítač > **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Nastavení šifrovacího klíče není pro tento scénář podstatné.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
5. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. Tím postupujte takto:

   - Dokončí proces migrace, zastaví replikaci pro místní virtuální počítač a zastaví fakturaci site recovery pro virtuální počítač.
   - Tento krok vyčistí data replikace. Neodstraní migrované virtuální aplikace.

     ![Dokončení migrace](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Replikace virtuálního počítače se před spuštěním převzetí služeb při selhání zastaví. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Můžete si všimnout delší zkušební doby převzetí služeb při selhání pro fyzické servery, počítače VMware Linux, virtuální počítače VMware, které nemají povolenou službu DHCP, a virtuální počítače VMware, které nemají následující spouštěcí ovladače: storvsc, vmbus, storflt, intelide, atapi.

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
- Ručně odeberte všechny agenty nebo poskytovatele služby Site Recovery z virtuálního počítače. Pokud migrujete virtuální zařízení VMware nebo fyzické servery, odinstalujte službu Mobility z virtuálního zařízení.
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

V tomto kurzu jste migrovali místní virtuální počítače na virtuální počítače Azure. Now

> [!div class="nextstepaction"]
> [Nastavte zotavení po havárii](azure-to-azure-replicate-after-migration.md) do sekundární oblasti Azure pro virtuální počítače Azure.

  
