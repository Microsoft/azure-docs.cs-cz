---
title: Migrace místních počítačů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje migraci místních počítačů do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0134f6c83548ae5ffb4924ecf7d652ce89910340
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210614"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrace místních počítačů do Azure

Službu [Azure Site Recovery](site-recovery-overview.md) můžete použít nejen ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure s cílem zajistit provozní kontinuitu a zotavení po havárii (BCDR), ale můžete pomocí ní také spravovat migraci místních počítačů do Azure.


V tomto kurzu se dozvíte, jak migrovat místní virtuální počítače a fyzické servery do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Výběr cíle replikace
> * Nastavení zdrojového a cílového prostředí
> * Nastavení zásady replikace
> * Povolení replikace
> * Spuštění testu migrace za účelem ověření, že vše funguje podle očekávání
> * Spuštění jednorázového převzetí služeb při selhání do Azure

Toto je třetí kurz ze specializované série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. Příprava místních serverů [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)

Než začnete, doporučujeme zkontrolovat architekturu [VMware](vmware-azure-architecture.md) nebo [Hyper-V](hyper-v-azure-architecture.md) určenou pro zotavení po havárii.


## <a name="prerequisites"></a>Požadavky

Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.



## <a name="select-a-replication-goal"></a>Výběr cíle replikace

Vyberte, jak chcete počítače replikovat a kam je chcete replikovat.
1. Klikněte na **Trezory služby Recovery Services** > trezor.
2. V nabídce prostředku klikněte na **Site Recovery** > **Příprava infrastruktury** > **Cíl ochrany**.
3. V části **Cíl ochrany** vyberte, co chcete migrovat.
    - **VMware:** Vyberte **Do Azure** > **Ano, s VMWare vSphere Hypervisor**.
    - **Fyzický počítač:** Vyberte **Do Azure** > **Nevirtualizované/jiné**.
    - **Hyper-V:** Vyberte **Do Azure** > **Ano, s technologií Hyper-V**. Pokud jsou virtuální počítače Hyper-V spravované nástrojem VMM, vyberte **Ano**.


## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

- [Nastavení](vmware-azure-tutorial.md#set-up-the-source-environment) zdrojového prostředí pro virtuální počítače VMware
- [Nastavení](physical-azure-disaster-recovery.md#set-up-the-source-environment) zdrojového prostředí pro fyzické servery
- [Nastavení](hyper-v-azure-tutorial.md#set-up-the-source-environment) zdrojového prostředí pro virtuální počítače Hyper-V

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

- [Nastavení zásady replikace](vmware-azure-tutorial.md#create-a-replication-policy) pro virtuální počítače VMware
- [Nastavení zásady replikace](physical-azure-disaster-recovery.md#create-a-replication-policy) pro fyzické servery
- [Nastavení zásady replikace](hyper-v-azure-tutorial.md#set-up-a-replication-policy) pro virtuální počítače Hyper-V


## <a name="enable-replication"></a>Povolení replikace

- [Povolení replikace](vmware-azure-tutorial.md#enable-replication) pro virtuální počítače VMware
- [Povolení replikace](physical-azure-disaster-recovery.md#enable-replication) pro fyzické servery
- Povolení replikace pro virtuální počítače Hyper-V [s VMM](hyper-v-vmm-azure-tutorial.md#enable-replication) nebo [bez VMM](hyper-v-azure-tutorial.md#enable-replication)


## <a name="run-a-test-migration"></a>Spuštění testu migrace

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Nastavení šifrovacího klíče není pro tento scénář podstatné.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
5. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. Tím se dokončí proce migrace, zastaví se replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro daný virtuální počítač.

    ![Dokončení migrace](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Replikace virtuálního počítače se před spuštěním převzetí služeb při selhání zastaví. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího trvání testovacího převzetí služeb při selhání u fyzických serverů, počítačů VMware s Linuxem, virtuálních počítačů VMware, které nemají povolenou službu DHCP, a virtuálních počítačů VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Po migraci

Po migraci počítačů do Azure musíte provést celou řadu kroků.

Některé kroky se dají automatizovat jako součást migračního procesu pomocí integrovaných automatizačních skriptů v [plánech obnovení]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation).   


### <a name="post-migration-steps-in-azure"></a>Kroky po migraci v Azure

- Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery. 
- U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
- [Agent virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) spravuje interakci virtuálního počítače s kontrolerem prostředků infrastruktury Azure. Agent se vyžaduje u některých služeb Azure, jako jsou Azure Backup, Site Recovery a Azure Security.
    - Pokud migrujete počítače VMware a fyzické servery, instalační program služby mobility nainstaluje dostupného agenta virtuálního počítače Azure na počítače s Windows. Na linuxových virtuálních počítačích doporučujeme nainstalovat agenta po převzetí služeb při selhání. a
    - Pokud migrujete virtuální počítače Azure do sekundární oblasti, musí být agent virtuálního počítače Azure zřízen na virtuálním počítači ještě před migrací.
    - Pokud migrujete virtuální počítače Hyper-V do Azure, nainstalujte agenta virtuálního počítače Azure na virtuální počítač Azure po migraci.
- Ručně odeberte všechny agenty nebo poskytovatele služby Site Recovery z virtuálního počítače. Pokud migrujete virtuální počítače VMware nebo fyzické servery, [odinstalujte službu mobility][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] z virtuálního počítače Azure.
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

V tomto kurzu jste migrovali místní virtuální počítače na virtuální počítače Azure. Nyní můžete pro virtuální počítače Azure [nastavit zotavení po havárii](azure-to-azure-replicate-after-migration.md) do sekundární oblasti Azure.

  
