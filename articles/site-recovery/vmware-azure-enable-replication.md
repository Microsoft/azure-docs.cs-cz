---
title: Povolení replikace virtuálních počítačů VMware pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit replikaci virtuálních počítačů VMware pro zotavení po havárii do Azure pomocí Azure Site Recovery.
author: mayurigupta13
ms.service: site-recovery
ms.date: 1/29/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: be6823486490ca6bc414e89c62a22f996aa27089
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329943"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Povolit replikaci do Azure pro virtuální počítače VMware


Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů VMware do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte:

1.  [V místním zdrojové prostředí nastavíte](vmware-azure-set-up-source.md).
2.  [Nastavení cílového prostředí v Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Než začnete
Při replikaci virtuálních počítačů VMware:

* Váš uživatelský účet Azure je potřeba některé [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) k replikaci nového virtuálního počítače do Azure.
* Virtuální počítače VMware se zjistí každých 15 minut. Může trvat 15 minut nebo i delší dobu, aby se objevily na portálu Azure portal po zjišťování. Obdobně zjišťování může trvat 15 minut nebo déle přidáte nového serveru vCenter nebo vSphere hostiteli.
* Změny prostředí na virtuálním počítači (například instalace nástrojů VMware) může trvat 15 minut nebo déle aktualizovat na portálu.
* Čas posledního zjištění virtuálních počítačů VMware v můžete zkontrolovat **poslední kontakt** pole pro serveru vCenter/hostiteli vSphere, na **konfiguračních serverů** stránky.
* Chcete-li přidat počítače pro replikaci bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klikněte na tlačítko **aktualizovat** tlačítko.
* Když povolíte replikaci, pokud je počítač připravený, procesový server na něm automaticky nainstaluje službu Mobility.


## <a name="enable-replication"></a>Povolení replikace

1. Klikněte na tlačítko **krok 2: Replikovat aplikaci** > **zdroj**. Po prvním povolení replikace kliknutím na **+Replikovat** v trezoru povolte replikaci pro další počítače.
2. V **zdroj** stránky > **zdroj**, vyberte konfigurační server.
3. V **typ počítače**vyberte **virtuálních počítačů** nebo **fyzické počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud replikujete fyzické počítače.
5. Vyberte procesový server, který může mít název konfiguračního serveru, pokud jste ještě nevytvořili žádné dalších procesových serverů. Pak klikněte na **OK**.

    ![Povolení replikace zdroje](./media/vmware-azure-enable-replication/enable-replication2.png)

6. V **cílové**, vyberte předplatné a skupinu prostředků, ve kterém chcete vytvořit převzetím služeb při selhání virtuálních počítačů. Vyberte model nasazení, který chcete použít v Azure pro virtuální počítače s převzetím služeb při selhání.

7. Vyberte účet služby Azure Storage, který chcete použít pro replikaci dat. 

    > [!NOTE]

    >   * Můžete vybrat účet úložiště úrovně standard a premium. Pokud vyberete účet premium, musíte zadat ještě standardní účet úložiště pro protokoly probíhající replikaci. Účty musí být ve stejné oblasti jako trezor služby Recovery Services.
    >   * Pokud chcete použít jiný účet úložiště, můžete si [vytvořit](../storage/common/storage-create-storage-account.md). Chcete-li vytvořit účet úložiště pomocí Resource Manageru, klikněte na tlačítko **vytvořit nový**. 

8. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se zprovozní po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor Služeb zotavení. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač. Pokud nejste připojeni k síti, musíte ho vytvořit. Pokud chcete vytvořit síť pomocí Resource Manageru, klikněte na tlačítko **vytvořit nový**. Vyberte podsíť, pokud je k dispozici a potom klikněte na tlačítko **OK**.

    ![Povolit nastavení cíle replikace](./media/vmware-azure-enable-replication/enable-rep3.png)
9. V části **Virtuální počítače** > **Výběr virtuálních počítačů** vyberte každý počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak klikněte na **OK**. Pokud nejste schopni zobrazení/vybrat konkrétní virtuální počítač, klikněte na tlačítko [tady](https://aka.ms/doc-plugin-VM-not-showing) k vyřešení daného problému.

    ![Povolení replikace vyberte virtuálních počítačů](./media/vmware-azure-enable-replication/enable-replication5.png)
10. V **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet používaný službou procesový server při automatické instalaci služby Mobility na počítači.  
11. Ve výchozím nastavení se replikují všechny disky. Chcete-li vyloučit disky z replikace, klikněte na tlačítko **všechny disky** a zrušte všechny disky, které nechcete replikovat.  Pak klikněte na **OK**. Později můžete nastavit další vlastnosti. [Další informace](vmware-azure-exclude-disk.md) o vyloučení disků.

    ![Povolení replikace nakonfigurovat vlastnosti](./media/vmware-azure-enable-replication/enable-replication6.png)

12. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace. Můžete upravit nastavení zásad replikace v **nastavení** > **zásady replikace** > (název zásady) > **upravit nastavení**. Změny použít na zásady, platí také pro replikaci a nové počítače.
13. Povolit **konzistenci** Pokud chcete shromažďovat počítače do replikační skupiny. Zadejte název pro skupinu a pak klikněte na tlačítko **OK**. 

    > [!NOTE]

    >    * Počítače v replikační skupině replikovat společně a mít sdílené body obnovení konzistentní při selhání a konzistentní při jejich převzetí služeb při selhání.
    >    * Shromažďování virtuálních počítačů a fyzických serverů, aby odpovídaly vaší úlohy. Povolení konzistence více virtuálních počítačů může mít vliv na výkon úloh. Použijte jenom v případě, že počítačích běží stejná úloha a potřebujete konzistenci.

    ![Povolení replikace](./media/vmware-azure-enable-replication/enable-replication7.png)
14. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.



## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

V dalším kroku ověřit vlastnosti zdrojového počítače. Mějte na paměti, že název virtuálního počítače Azure musí splňovat [požadavky na virtuální počítač Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Klikněte na tlačítko **nastavení** > **replikované položky** > a potom vyberte počítač. **Essentials** stránku s informacemi o nastavení počítače a stav.
2. V části **Vlastnosti** můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální počítač.
3. V **výpočty a síť** > **výpočetní vlastnosti**, můžete změnit propoerties více virtuálních počítačů:
* Azure VM name - upravit název, který má v případě potřeby v souladu s požadavky na Azure
* Cílovou velikost virtuálního počítače nebo typ – výchozí velikost virtuálního počítače je vybrán založené na zdroji velikost virtuálního počítače. Můžete vybrat jinou velikost virtuálního počítače podle potřeb kdykoli před převzetí služeb při selhání. Všimněte si, že velikost zdrojového disku také podle velikosti disku virtuálního počítače a může být pouze změní po převzetí služeb při selhání. Další informace o velikosti disku a vstupně-výstupních operací v našich [cíle škálovatelnosti pro disky](../virtual-machines/windows/disk-scalability-targets.md) článku.

    ![Výpočetní prostředky a vlastnosti sítě](./media/vmware-azure-enable-replication/vmproperties.png)

*  Skupina prostředků – můžete vybrat [skupiny prostředků](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) z které počítače s stane součástí příspěvek převzetí služeb při selhání. Můžete změnit toto nastavení kdykoli před převzetí služeb při selhání. Příspěvek převzetí služeb při selhání, pokud tento počítač migrovat na jinou skupinu prostředků, nastavení ochrany pro tento počítač přerušení.
* Skupina dostupnosti – můžete vybrat [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Pokud váš počítač musí být součástí příspěvek převzetí služeb při selhání. Když vybíráte skupinu dostupnosti, mějte na paměti, který:

    * Jsou uvedeny jenom skupiny dostupnosti patří do zadané skupině prostředků.  
    * Počítače s jinou virtuální sítí nemůže být součástí stejné skupiny dostupnosti.
    * Pouze virtuální počítače se stejnou velikostí můžou být součástí nastavení dostupnosti.
4. Můžete také zobrazit a přidat informace o cílové síti, podsíti a IP adresa přiřazená k virtuálnímu počítači Azure.
5. V **disky**, uvidíte operačního systému a datové disky na virtuálním počítači se musí replikovat.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurace sítě a IP adresy

- Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, použije počítač převzetím služeb při selhání DHCP. Pokud nastavíte adresu, která není k dispozici na převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Pokud je adresa k dispozici v testovací síti převzetí služeb při selhání, lze se stejnou cílovou IP adresu pro testovací převzetí služeb při selhání.
- Počet síťových adaptérů závisí na velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:
    - Pokud počet síťových adaptérů na zdrojovém počítači je menší než nebo roven počtu adaptérů, povolené pro velikost cílového počítače, cíl má stejný počet adaptérů jako zdroj.
    - Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro cílovou velikost, použije se maximální velikost cíle.
    Například pokud má zdrojový počítač dva síťové adaptéry a velikost cílového počítače podporuje čtyři, má cílový počítač dva adaptéry. Pokud má zdrojový počítač dva adaptéry, ale podporovaná velikost cíle podporuje pouze jeden, má cílový počítač pouze jeden adaptér.
    - Pokud má virtuální počítač více síťových adaptérů, všechny připojí ke stejné síti. Kromě toho se stane první z nich v seznamu zobrazí *výchozí* síťový adaptér ve virtuálním počítači Azure.

### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Zákazníkům programu Microsoft Software Assurance můžete použít zvýhodněné hybridní využití Azure ušetříte na licenční náklady na pro počítače s Windows serverem, které se migrují do Azure nebo použijte Azure pro zotavení po havárii. Pokud mají nárok na využití Azure Hybrid Benefit můžete zadat, že virtuální počítač přiřazený tuto výhodu je ten, který vytvoří Azure Site Recovery při převzetí služeb. Použijte následující postup:
- Přejděte do části vlastností výpočty a síť replikovaného virtuálního počítače.
- Odpovězte na otázku, s dotazem, pokud máte licenci k Windows serveru, který vám umožňuje být nárok na zvýhodněné hybridní využití Azure.
- Vyberte zaškrtnutím políčka potvrďte, že máte způsobilou licenci na Windows Server s programem Software Assurance, což vám umožní použít v programu Azure Hybrid Benefit na počítači, který bude vytvořena na převzetí služeb při selhání.
- Nastavení, aby replikovaný počítač uložte.

Další informace o [zvýhodněné hybridní využití Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Běžné problémy

* Každý disk by měl být menší než 4 TB.
* Disk s operačním systémem musí být základní disk a ne dynamický disk.
* Generace 2/UEFI – virtuální počítače s povoleným třeba řada operačních systémů Windows a spouštěcí disk by měl být menší než 300 GB.

## <a name="next-steps"></a>Další postup

Po dokončení ochrany si je a počítač byl dosažen chráněném stavu, můžete se pokusit [převzetí služeb při selhání](site-recovery-failover.md) ke kontrole, jestli vaše aplikace se zobrazí v Azure nebo ne.

* Zjistěte, jak [vyčistit nastavení registrace a ochrany](site-recovery-manage-registration-and-protection.md) zakázat replikaci.
* Zjistěte, jak [automatizace replikace pro virtuální počítače pomocí Powershellu](vmware-azure-disaster-recovery-powershell.md)
