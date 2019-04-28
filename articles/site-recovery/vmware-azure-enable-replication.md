---
title: Povolení replikace virtuálních počítačů VMware pro zotavení po havárii do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit virtuálních počítačů VMware pro replikaci do Azure pro zotavení po havárii pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 4/18/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: ba55afbd62bbbc2290d1daaebf77becc249c1d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922707"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Povolit replikaci do Azure pro virtuální počítače VMware

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů VMware do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte:

- [Nastavit zdrojové prostředí v místním](vmware-azure-set-up-source.md).
- [Nastavení cílového prostředí v Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Než začnete
Při replikaci virtuálních počítačů VMware, mějte tyto informace:

* Váš uživatelský účet Azure je potřeba některé [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) k replikaci nového virtuálního počítače do Azure.
* Virtuální počítače VMware se zjistí každých 15 minut. Může trvat 15 minut nebo i delší dobu se na webu Azure Portal se zobrazí po zjištění virtuálních počítačů. Podobně může zjišťování trvat 15 minut nebo i delší dobu, po přidání nového serveru vCenter nebo vSphere hostiteli.
* Může trvat 15 minut nebo i delší dobu pro změny prostředí na virtuálním počítači (například instalace nástrojů VMware) aktualizovat na portálu.
* Můžete zkontrolovat čas posledního zjištění virtuálních počítačů VMware: Zobrazit **poslední kontakt** na **konfiguračních serverů** stránka serveru vCenter/hostiteli vSphere.
* Pokud chcete přidat virtuální počítače pro replikaci bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (ale neklikejte na něj) a vyberte **aktualizovat**.
* Při povolení replikace, pokud je virtuální počítač připravený, procesového serveru služby Azure Site Recovery Mobility automaticky nainstaluje na něj.

## <a name="enable-replication"></a>Povolení replikace

Než budete postupovat podle kroků v této části, mějte na paměti následující informace:
* Azure Site Recovery replikuje nyní přímo do managed disks pro všechny nové replikace. Procesový server zapíše protokoly replikace do účtu úložiště mezipaměti v cílové oblasti. Tyto protokoly se používají k vytvoření bodů obnovení na spravované disky repliky.
* Během převzetí služeb při selhání vyberete bod obnovení slouží k vytvoření cílového spravovaného disku.
* Virtuální počítače, které už byly nakonfigurované pro replikaci do cílové účty úložiště nejsou ovlivněny.
* Replikace do účtů úložiště pro nový virtuální počítač je pouze k dispozici prostřednictvím REST Representational State Transfer () rozhraní API a Powershellu. Použijte rozhraní Azure REST API verze 2016-08-10 nebo 2018-01-10 se replikuje do účtů úložiště.

1. Přejděte na **krok 2: Replikovat aplikaci** > **zdroj**. Po povolení replikace pro první vyberte **+ replikovat** v trezoru povolíte replikaci pro další virtuální počítače.
1. V **zdroj** stránky > **zdroj**, vyberte konfigurační server.
1. Pro **typ počítače**vyberte **virtuálních počítačů** nebo **fyzické počítače**.
1. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud replikujete fyzické počítače.
1. Vyberte procesový server, které bude konfigurační server, pokud jste ještě nevytvořili žádné dalších procesových serverů. Pak vyberte **OK**.

    ![Povolení replikace zdrojového okna](./media/vmware-azure-enable-replication/enable-replication2.png)

1. Pro **cílové**, vyberte předplatné a skupinu prostředků ve kterém chcete vytvořit převzetím služeb při selhání virtuálních počítačů. Vyberte model nasazení, který chcete použít v Azure pro virtuální počítače s převzetím služeb při selhání.

1. Vyberte síť Azure a podsíť, která virtuální počítače Azure připojí po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor služby Site Recovery.

   Vyberte **nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny virtuální počítače, které jste vybrali pro ochranu. Vyberte **nakonfigurovat později** vyberte síť Azure pro konkrétní virtuální počítač. Pokud nejste připojeni k síti, musíte ho vytvořit. Pokud chcete vytvořit síť pomocí Azure Resource Manageru, vyberte **vytvořit nový**. Vyberte podsíť, pokud je k dispozici a pak vyberte **OK**.
   
   ![Povolení replikace cílového okna](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Pro **virtuálních počítačů** > **výběr virtuálních počítačů**, vyberte všechny virtuální počítače, které chcete replikovat. Můžete vybrat pouze virtuální počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud nelze zobrazit nebo vyberte konkrétní virtuální počítač, přečtěte si téma [zdrojový počítač není uvedená na webu Azure Portal](https://aka.ms/doc-plugin-VM-not-showing) k vyřešení daného problému.

    ![Povolení replikace virtuálních počítačů vyberte okno](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Pro **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet, který použije procesový server při automatické instalaci služby Site Recovery Mobility na virtuálním počítači. Také vyberte typ cílového spravovaného disku k replikaci založený na datech vzory změn.
10. Ve výchozím nastavení se replikují všechny disky zdrojového virtuálního počítače. Chcete-li vyloučit disky z replikace, zrušte **zahrnout** zaškrtávací políčko pro všechny disky, které chcete replikovat. Pak vyberte **OK**. Později můžete nastavit další vlastnosti. Další informace o [vyloučení disků](vmware-azure-exclude-disk.md).

    ![Povolení replikace nakonfigurovat vlastnosti okna](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Na **nastavení replikace** > **konfigurace nastavení replikace**, ověřte, že je vybraná správná zásada replikace. Můžete upravit nastavení zásad replikace na **nastavení** > **zásady replikace** > ***Název_zásady***  >  **Upravit nastavení**. Změny, které se vztahují na zásady, platí také pro replikaci a novými virtuálními počítači.
1. Povolit **konzistenci** Pokud chcete shromažďovat virtuálních počítačů do replikační skupiny. Zadejte název pro skupinu a pak vyberte **OK**.

    > [!NOTE]
    >    * Virtuální počítače v replikační skupině replikovat společně a mít sdílené body obnovení konzistentní při selhání a konzistentní při jejich převzetí služeb při selhání.
    >    * Shromažďování virtuálních počítačů a fyzických serverů, aby odpovídaly vaší úlohy. Povolení konzistence více virtuálních počítačů může ovlivnit výkon úloh. Proveďte pouze v případě, že virtuální počítače běží stejná úloha a potřebujete konzistenci.

    ![Povolení replikace okna](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Vyberte **Povolit replikaci**. Průběh můžete sledovat **povolení ochrany** úlohy na **nastavení** > **úlohy** > **úlohy Site Recovery**. Po **dokončit ochranu** spuštění úlohy, virtuální počítač připravený k převzetí služeb při selhání.

## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

V dalším kroku ověřte vlastnosti zdrojového virtuálního počítače. Mějte na paměti, že název virtuálního počítače Azure musí splňovat [požadavky na virtuální počítač Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Přejděte na **nastavení** > **replikované položky**a potom vyberte virtuální počítač. **Essentials** stránku s informacemi o nastavení a stav Virtuálního počítače.
1. V části **Vlastnosti** můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální počítač.
1. V **výpočty a síť** > **výpočetní vlastnosti**, můžete změnit více vlastností virtuálního počítače. 

    ![Okno vlastností výpočty a síť](./media/vmware-azure-enable-replication/vmproperties.png)

    * Název virtuálního počítače Azure: V případě potřeby změňte název, který má splňovat požadavky na Azure.
    * Cílová velikost virtuálního počítače nebo typ virtuálního počítače: Výchozí velikost virtuálního počítače je zvolena podle několika parametrů, které zahrnují počet disků, počet NIC, procesoru počet jader, paměti a dostupné velikosti rolí virtuálních počítačů v cílové oblasti Azure. Azure Site Recovery vybere první dostupné velikosti virtuálních počítačů, které splňuje všechna kritéria. Můžete vybrat jinou velikost virtuálního počítače na základě vašich potřeb kdykoli před převzetí služeb při selhání. Všimněte si, že velikost zdrojového disku také podle velikosti disku virtuálního počítače, a můžete změnit jenom po převzetí služeb při selhání. Další informace o velikosti disku a vstupně-výstupních operací sazby za [škálovatelnost a výkonnostní cíle pro disky virtuálních počítačů na Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Skupina zdrojů: Můžete vybrat [skupiny prostředků](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), ze které virtuální počítač stane součástí příspěvek převzetí služeb při selhání. Můžete změnit toto nastavení kdykoli před převzetí služeb při selhání. Po převzetí služeb při selhání Pokud migrujete virtuální počítač do jiné skupiny prostředků, nastavení ochrany pro tento virtuální počítač přerušit.
    * Skupina dostupnosti: Můžete vybrat [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Pokud váš virtuální počítač musí být součástí příspěvek převzetí služeb při selhání. Když vyberete skupinu dostupnosti, mějte tyto informace:

        * Jsou uvedeny jenom skupiny dostupnosti, které patří do zadané skupině prostředků.  
        * Virtuální počítače, které jsou v různých virtuálních sítích nemůže být součástí stejné skupiny dostupnosti.
        * Pouze virtuální počítače se stejnou velikostí můžou být součástí nastavení dostupnosti.
1. Můžete také přidat informace o cílové síti, podsíti a IP adresu, která je přiřazená k virtuálnímu počítači Azure.
2. V **disky**, uvidíte operačního systému a datové disky na virtuálním počítači, který bude replikován.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurace sítě a IP adresy

Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, převzetím služeb při selhání virtuálního počítače používá protokol DHCP. Pokud nastavíte adresu, která není k dispozici na převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Pokud je adresa k dispozici v testovací síti převzetí služeb při selhání, můžete použít stejnou cílovou IP adresu pro testovací převzetí služeb při selhání.

Počet síťových adaptérů závisí na velikosti, že zadáte pro cílový virtuální počítač následujícím způsobem:

- Pokud počet síťových adaptérů na zdrojovém virtuálním počítači je menší než nebo roven počtu adaptérů, které jsou povolené pro velikost cílového virtuálního počítače, má cíl stejný počet adaptérů jako zdroj.
- Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet, který je povolené pro velikost cílového virtuálního počítače, použije se maximální velikost cíle. Například pokud má zdrojový virtuální počítač dva síťové adaptéry a velikost cílového virtuálního počítače podporuje čtyři, cílový virtuální počítač má dva adaptéry. Pokud má zdrojový virtuální počítač dva adaptéry, ale velikost cíle podporuje pouze jeden, cílový virtuální počítač má pouze jeden adaptér.
- Pokud má virtuální počítač více síťových adaptérů, všechny připojí ke stejné síti. Kromě toho se stane první adaptér, který se zobrazí v seznamu *výchozí* síťový adaptér ve virtuálním počítači Azure. 

### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Zákazníkům programu Microsoft Software Assurance, můžete použít zvýhodněné hybridní využití Azure a Ušetřete na náklady na licencování pro počítače Windows Server, které se migrují do Azure. Výhody platí také pro zotavení po havárii Azure. Pokud máte nárok, můžete přiřadit výhody virtuální počítač, pokud dojde k selhání nastavení vytvoří Site Recovery. Provedete to podle těchto kroků:
1. Přejděte **vlastnosti počítače a sítě** replikovaného virtuálního počítače.
2. Odpověď, když se zobrazí výzva, pokud máte licenci na Windows Server, který vám umožňuje být nárok na zvýhodněné hybridní využití Azure.
3. Potvrďte, že máte způsobilou licenci na Windows Server s programem Software Assurance, který vám umožní použít tuto výhodu, která bude vytvořena na převzetí služeb při selhání virtuálního počítače.
4. Nastavení pro replikovaný virtuální počítač uložte.

Další informace o [zvýhodněné hybridní využití Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Řešení běžných problémů

* Každý disk by měl být menší než 4 TB.
* Disk s operačním systémem by měla být základní disk, ne dynamický disk.
* Generace 2/UEFI – virtuální počítače s povoleným třeba řada operačních systémů Windows a spouštěcí disk by měl být menší než 300 GB.

## <a name="next-steps"></a>Další postup

Po virtuálním počítači dosáhne chráněném stavu, zkuste [převzetí služeb při selhání](site-recovery-failover.md) ke kontrole, jestli vaše aplikace se zobrazí v Azure.

* Zjistěte, jak [vyčistit nastavení registrace a ochrany](site-recovery-manage-registration-and-protection.md) zakázat replikaci.
* Zjistěte, jak [automatizace replikace pro virtuální počítače pomocí prostředí Powershell](vmware-azure-disaster-recovery-powershell.md).
