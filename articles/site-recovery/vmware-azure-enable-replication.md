---
title: Povolení virtuálních počítačů VMware pro zotavení po havárii pomocí Azure Site Recovery
description: Tento článek popisuje, jak povolit replikaci virtuálních počítačů VMware pro zotavení po havárii pomocí služby Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257313"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Povolení replikace do virtuálních počítačů Azure pro Virtuální Měše

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačích VMware do Azure.

## <a name="resolve-common-issues"></a>Řešení běžných problémů

* Každý disk by měl být menší než 4 TB.
* Disk operačního systému by měl být běžný disk, nikoli dynamický disk.
* Pro virtuální počítače s podporou generace 2/UEFI by měla být rodina operačního systému Windows a spouštěcí disk by měl být menší než 300 GB.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte:

- [Nastavte místní zdrojové prostředí](vmware-azure-set-up-source.md).
- [Nastavte si cílové prostředí v Azure](vmware-azure-set-up-target.md).
- Před zahájením [ověřte požadavky a požadavky.](vmware-physical-azure-support-matrix.md) Důležité věci, které je třeba poznamenat, zahrnují:
    - [Podporované operační systémy](vmware-physical-azure-support-matrix.md#replicated-machines) pro replikované počítače.
    - [Podpora úložiště/disku.](vmware-physical-azure-support-matrix.md#storage)
    - [Požadavky Azure,](vmware-physical-azure-support-matrix.md#azure-vm-requirements) které by měly splňovat místní počítače.


## <a name="before-you-start"></a>Než začnete
Při replikaci virtuálních počítačů VMware mějte na paměti tyto informace:

* Váš uživatelský účet Azure musí mít určitá [oprávnění,](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) která umožní replikaci nového virtuálního počítače do Azure.
* Virtuální mamy VMware jsou zjištěny každých 15 minut. Může trvat 15 minut nebo déle pro virtuální počítače se zobrazí na webu Azure portal po zjišťování. Podobně zjišťování může trvat 15 minut nebo déle, když přidáte nový server vCenter nebo hostitele vSphere.
* Aktualizace změn prostředí ve virtuálním počítači (například instalace nástrojů VMware) na portálu může trvat 15 minut nebo déle.
* Můžete zkontrolovat naposledy zjištěný čas virtuálních počítačů VMware: Podívejte se na pole **Poslední kontakt na** stránce **Konfigurační servery** pro hostitele serveru vCenter/vSphere.
* Pokud chcete přidat virtuální počítače pro replikaci bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (ale neklikejte na něj) a vyberte **Aktualizovat**.
* Když povolíte replikaci, pokud je připravený virtuální počítač, procesní server na něj automaticky nainstaluje službu Mobility azure site recovery.

## <a name="enable-replication"></a>Povolení replikace

Než začnete postupovat podle kroků v této části, poznamenejte si následující informace:
* Azure Site Recovery se teď replikuje přímo na spravované disky pro všechny nové replikace. Procesní server zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti. Tyto protokoly se používají k vytvoření bodů obnovení v replikách spravovaných disků, které mají konvence pojmenování asrseeddisk.
* Podpora prostředí Powershell pro replikaci na spravované disky je k dispozici od [modulu Az.RecoveryServices verze 2.0.0 a dále](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* V době převzetí služeb při selhání se vybraný bod obnovení použije k vytvoření cílového spravovaného disku.
* Virtuální počítače, které byly dříve nakonfigurované pro replikaci na cílové účty úložiště, nejsou ovlivněny.
* Replikace na účty úložiště pro nový virtuální počítač je k dispozici pouze prostřednictvím rozhraní API reprezentace přenosu stavu (REST) a Powershellu. Pro replikaci do účtů úložiště použijte rozhraní AZURE REST API verze 2016-08-10 nebo 2018-01-10.

Chcete-li povolit replikaci, postupujte podle následujících kroků:
1. Přejděte na **krok 2: Replikace** > **zdroje**aplikace . Po prvním povolení replikace vyberte možnost **+Replikovat** v úschovně, chcete-li povolit replikaci pro další virtuální počítače.
2. Na **zdrojové** stránce > **zdroj**vyberte konfigurační server.
3. V **případě typu Počítač**vyberte možnost Virtuální **počítače** nebo Fyzické **počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud replikujete fyzické počítače.
5. Vyberte procesní server. Pokud nejsou vytvořeny žádné další procesní servery, bude v rozevíracím seznamu k dispozici vestavěný procesní server konfiguračního serveru. Stav každého procesního serveru je uveden podle doporučených limitů a dalších parametrů. Zvolte server pro zpracování v pořádku. Nelze vybrat [kritický](vmware-physical-azure-monitor-process-server.md#process-server-alerts) procesový server. Můžete buď [vyřešit a vyřešit](vmware-physical-azure-troubleshoot-process-server.md) chyby **nebo** nastavit [horizontální navýšení kapacity procesu serveru](vmware-azure-set-up-process-server-scale.md).
    ![Povolení zdrojového okna replikace](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Od [verze 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)jsou zavedeny další výstrahy pro vylepšení výstrah stavu procesního serveru. Upgradujte součásti site recovery na verze 9.24 nebo vyšší pro všechny výstrahy, které mají být generovány.

6. V **části Cíl**vyberte skupinu předplatného a prostředků, ve které chcete vytvořit virtuální počítače s připojením se k selhání. Vyberte model nasazení, který chcete použít v Azure pro virtuální počítače s převzetím počítače s klientem.
2. Vyberte síť Azure a podsíť, ke které se virtuální počítače Azure připojí po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor služby Obnovení webu.

   Vyberte **Konfigurovat nyní pro vybrané počítače** použít nastavení sítě na všechny virtuální počítače, které vyberete pro ochranu. Vyberte **Konfigurovat později** a vyberte síť Azure pro každý virtuální počítač. Pokud síť nemáte, musíte ji vytvořit. Pokud chcete vytvořit síť pomocí Správce prostředků Azure, vyberte **Vytvořit nový**. Vpřípadě potřeby vyberte podsíť a pak vyberte **OK**.
   
   ![Povolení cílového okna replikace](./media/vmware-azure-enable-replication/enable-rep3.png)

1. U **virtuálních počítačů** > **Vyberte virtuální počítače**vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze virtuální počítače, pro které lze povolit replikaci. Pak vyberte **OK**. Pokud nevidíte nebo nevybíráte žádný konkrétní virtuální počítač, přečtěte si téma [Zdrojový počítač není uvedený na webu Azure Portal,](https://aka.ms/doc-plugin-VM-not-showing) kde problém vyřešíte.

    ![Povolení replikace Okno Vybrat virtuální počítače](./media/vmware-azure-enable-replication/enable-replication5.png)

1. V **části Vlastnosti** > **Konfigurovat vlastnosti**vyberte účet, který procesový server používá k automatické instalaci služby Mobility obnovení lokality do virtuálního počítače. Také zvolte typ cílového spravovaného disku, na který chcete replikovat na základě vzorů změn dat.
10. Ve výchozím nastavení jsou replikovány všechny disky zdrojového virtuálního počítače. Chcete-li vyloučit disky z replikace, zrušte zaškrtnutí políčka **Zahrnout** u všech disků, které nechcete replikovat. Pak vyberte **OK**. Později můžete nastavit další vlastnosti. Další informace o [vyloučení disků](vmware-azure-exclude-disk.md).

    ![Povolit okno konfigurovat vlastnosti replikace](./media/vmware-azure-enable-replication/enable-replication6.png)

1. V >  **nastavení replikace****Nakonfigurujte nastavení replikace**ověřte, zda je vybrána správná zásada replikace. Nastavení zásad replikace můžete upravit na adrese **Nastavení** > ***policy name*** > zásad > **replikace****Název zásad Upravit nastavení**. Změny, které použijete pro zásady platí také pro replikaci a nové virtuální počítače.
1. Pokud chcete shromáždit virtuální počítače do replikační skupiny, povolte **konzistenci více** virtuálních počítačů. Zadejte název skupiny a pak vyberte **OK**.

    > [!NOTE]
    >    * Virtuální počítače v replikační skupině replikují společně a při převzetí služeb při selhání mají sdílené body obnovení konzistentní s havárií a konzistentní s aplikací.
    >    * Shromážděte virtuální počítače a fyzické servery dohromady, aby odrážely vaše úlohy. Povolení konzistence více virtuálních montovny může ovlivnit výkon pracovního vytížení. To provést pouze v případě, že virtuální počítače běží stejné úlohy a potřebujete konzistenci.

    ![Povolit okno replikace](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Vyberte **Povolit replikaci**. Průběh úlohy **Povolit ochranu** můžete sledovat v části**Úlohy** > obnovení webu úloh **y nastavení** > **úloh**. Po spuštění **úlohy Finalize Protection** je virtuální počítač připraven k převzetí služeb při selhání.

## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

Dále ověřte vlastnosti zdrojového virtuálního počítače. Nezapomeňte, že název virtuálního počítače Azure musí splňovat [požadavky na virtuální počítač Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Přejděte na **Nastavení** > **replikovaných položek**a vyberte virtuální počítač. Stránka **Essentials** zobrazuje informace o nastavení a stavu virtuálního počítače.
1. Ve **vlastnostech**můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální hod.
1. Ve **vlastnostech Výpočetní výkon a Síťový** > **výpočetní výkon**můžete změnit více vlastností virtuálního počítače. 

    ![Okno Výpočetní a síťové vlastnosti](./media/vmware-azure-enable-replication/vmproperties.png)

    * Název virtuálního počítače Azure: V případě potřeby upravte název tak, aby splňoval požadavky Azure.
    * Velikost cílového virtuálního počítače nebo typ virtuálního počítače: Výchozí velikost virtuálního počítače se volí na základě několika parametrů, které zahrnují počet disků, počet nic, počet jader procesoru, paměť a dostupné velikosti rolí virtuálních počítačů v cílové oblasti Azure. Azure Site Recovery vybere první dostupnou velikost virtuálního počítače, která splňuje všechna kritéria. Před převzetím služeb při selhání můžete kdykoli vybrat jinou velikost virtuálního počítače podle vašich potřeb. Všimněte si, že velikost disku virtuálního počítače je také založen na velikosti zdrojového disku a lze ji změnit pouze po převzetí služeb při selhání. Další informace o velikosti disků a rychlosti videa VOPS na [škálovatelnost a cíle výkonu pro disky virtuálních počítačů v systému Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Skupina prostředků: Můžete vybrat [skupinu prostředků](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), ze které se virtuální počítač stane součástí převzetí služeb při selhání. Toto nastavení můžete kdykoli před převzetím služeb při selhání změnit. Po převzetí služeb při selhání, pokud migrujete virtuální počítač do jiné skupiny prostředků, nastavení ochrany pro tento konec virtuálního počítače.
    * Sada dostupnosti: Sadu [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) můžete vybrat, pokud váš virtuální počítač musí být součástí převzetí služeb při selhání příspěvku. Když vyberete sadu dostupnosti, mějte na paměti následující informace:

        * Jsou uvedeny pouze skupiny dostupnosti, které patří do zadané skupiny prostředků.  
        * Virtuální počítače, které jsou v různých virtuálních sítích, nemohou být součástí stejné skupiny dostupnosti.
        * Součástí sady dostupnosti mohou být pouze virtuální počítače stejné velikosti.
1. Můžete také přidat informace o cílové síti, podsíti a IP adrese, která je přiřazená k virtuálnímu počítači Azure.
2. V **disky**, můžete vidět operační systém a datové disky na virtuálním počítači, který bude replikován.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurace sítí a adres IP

Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, virtuální počítač s převzetím služby při selhání použije službu DHCP. Pokud nastavíte adresu, která není k dispozici při převzetí služeb při selhání, převzetí služeb při selhání nefunguje. Pokud je adresa k dispozici v testovací síti s podporou převzetí služeb při selhání, můžete pro převzetí služeb při selhání testu použít stejnou cílovou adresu IP.

Počet síťových adaptérů je dán velikostí, kterou zadáte pro cílový virtuální počítač, a to následovně:

- Pokud je počet síťových adaptérů ve zdrojovém virtuálním počítači menší nebo roven počtu adaptérů, které jsou povoleny pro velikost cílového virtuálního počítače, cíl má stejný počet adaptérů jako zdroj.
- Pokud počet adaptérů pro zdrojový virtuální počítač překročí počet, který je povolen pro velikost cílového virtuálního počítače, použije se maximální velikost cílové velikosti. Například pokud zdrojový virtuální počítač má dva síťové adaptéry a velikost cílového virtuálního počítače podporuje čtyři, cílový virtuální počítač má dva adaptéry. Pokud zdrojový virtuální počítače má dva adaptéry, ale cílová velikost podporuje pouze jeden, cílový virtuální počítače má jenom jeden adaptér.
- Pokud má virtuální počítač více síťových adaptérů, všechny se připojují ke stejné síti. První adaptér, který se zobrazí v seznamu, se také stane *výchozím* síťovým adaptérem ve virtuálním počítači Azure. 

### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Zákazníci microsoft software assurance mohou pomocí hybridnívýhody Azure ušetřit na licenčních nákladech pro počítače se systémem Windows Server, které jsou migrovány do Azure. Výhoda platí také pro zotavení po havárii Azure. Pokud máte nárok, můžete přiřadit výhodu virtuálnímu počítači, který site recovery vytvoří, pokud dojde k převzetí služeb při selhání. Provedete to podle těchto kroků:
1. Přejděte na **vlastnosti počítače a sítě** replikovaného virtuálního počítače.
2. Odpovězte na otázku, zda máte licenci windows serveru, která vám dává nárok na hybridní výhody Azure.
3. Zkontrolujte, zda máte oprávněnou licenci windows serveru s programem Software Assurance, kterou můžete použít k použití výhody pro virtuální hod, který bude vytvořen při převzetí služeb při selhání.
4. Uložte nastavení replikovaného virtuálního počítače.

Další informace o [hybridních výhodach Azure](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Další kroky

Jakmile virtuální počítač dosáhne chráněného stavu, zkuste [převzetí služeb při selhání](site-recovery-failover.md) zkontrolovat, jestli se vaše aplikace zobrazí v Azure.

* Přečtěte si, jak [vyčistit nastavení registrace a ochrany a](site-recovery-manage-registration-and-protection.md) zakázat replikaci.
* Zjistěte, jak [automatizovat replikaci virtuálních počítačů pomocí Powershellu](vmware-azure-disaster-recovery-powershell.md).
