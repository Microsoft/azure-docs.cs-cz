---
title: Migrace místních počítačů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje migraci místních počítačů do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3e82983ff171e638415d6e7cafe0cb034dd978af
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057151"
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
2. Příprava místních serverů [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V] (hyper-v-prepare-on-premises-tutorial.md)

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
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
5. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
6. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. Tím se dokončí proce migrace, zastaví se replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro daný virtuální počítač.

    ![Dokončení migrace](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Replikace virtuálního počítače se před spuštěním převzetí služeb při selhání zastaví. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího trvání testovacího převzetí služeb při selhání u fyzických serverů, počítačů VMware s Linuxem, virtuálních počítačů VMware, které nemají povolenou službu DHCP, a virtuálních počítačů VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste migrovali místní virtuální počítače na virtuální počítače Azure. Teď můžete pro virtuální počítače Azure nakonfigurovat zotavení po havárii.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii](azure-to-azure-replicate-after-migration.md) pro virtuální počítače Azure po migraci z místní lokality.
