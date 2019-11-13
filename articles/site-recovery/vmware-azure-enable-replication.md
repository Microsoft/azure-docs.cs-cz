---
title: Povolení pro zotavení po havárii virtuálních počítačů VMware pomocí Azure Site Recovery
description: Tento článek popisuje, jak povolit replikaci virtuálních počítačů VMware pro zotavení po havárii pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 1cc1ee82b45ecab17e4bcfb3a909fc90b33a1545
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954435"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Povolení replikace do Azure pro virtuální počítače VMware

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů VMware do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte:

- [Nastavte své místní zdrojové prostředí](vmware-azure-set-up-source.md).
- [Nastavte své cílové prostředí v Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Než začnete
Při replikaci virtuálních počítačů VMware mějte na paměti tyto informace:

* Váš uživatelský účet Azure musí mít určitá [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) , aby bylo možné povolit replikaci nového virtuálního počítače do Azure.
* Virtuální počítače VMware se objevují každých 15 minut. Může trvat 15 minut nebo déle, než se virtuální počítače objeví v Azure Portal po zjištění. Podobně zjišťování může trvat 15 minut nebo déle, když přidáte nový Server vCenter nebo hostitele vSphere.
* Může trvat 15 minut nebo i déle, než se změny prostředí na virtuálním počítači (například instalace nástrojů VMware) aktualizují na portálu.
* Můžete kontrolovat čas posledního zjištění pro virtuální počítače VMware: v poli **Poslední kontakt** na stránce **konfigurační servery** pro hostitele vCenter Server/vSphere.
* Pokud chcete přidat virtuální počítače pro replikaci bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (ale neklikejte na něj) a vyberte **aktualizovat**.
* Pokud povolíte replikaci, pak pokud je virtuální počítač připravený, nainstaluje procesový Server na něj automaticky službu Azure Site Recovery mobility.

## <a name="enable-replication"></a>Povolení replikace

Než budete postupovat podle kroků v této části, poznamenejte si následující informace:
* Azure Site Recovery se teď replikuje přímo na spravované disky pro všechny nové replikace. Procesový Server zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti. Tyto protokoly slouží k vytváření bodů obnovení na spravovaných discích repliky, které mají konvence pojmenování asrseeddisk.
* Podpora PowerShellu pro replikaci na spravované disky je k dispozici v [modulu AZ. RecoveryServices verze 2.0.0 a vyšší](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) . 
* V době převzetí služeb při selhání se k vytvoření cíle spravovaného disku použije vybraný bod obnovení.
* Virtuální počítače, které byly dřív nakonfigurované pro replikaci na cílové účty úložiště, to neovlivní.
* Replikace do účtů úložiště pro nový virtuální počítač je k dispozici jenom prostřednictvím rozhraní REST API pro reprezentaci přenosů stavu (REST) a PowerShellu. Pro replikaci do účtů úložiště použijte Azure REST API verze 2016-08-10 nebo 2018-01-10.

Pokud chcete povolit replikaci, postupujte prosím podle následujících kroků:
1. Přejít ke **kroku 2: replikace** > ho **zdroje**aplikace. Po prvním povolení replikace vyberte **+ replikovat** v trezoru, abyste povolili replikaci pro další virtuální počítače.
2. **Na zdrojové stránce >** **zdroj**vyberte konfigurační server.
3. Jako **typ počítače**vyberte **Virtual Machines** nebo **fyzické počítače**.
4. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud provádíte replikaci fyzických počítačů.
5. Vyberte procesový Server. Pokud nevytvoříte žádné další procesní servery, bude v rozevíracím seznamu k dispozici sestavený procesový Server konfiguračního serveru. Stav každého procesového serveru je uvedený podle doporučených omezení a dalších parametrů. Vyberte procesový Server v pořádku. Nelze zvolit [kritický](vmware-physical-azure-monitor-process-server.md#process-server-alerts) procesový Server. Můžete buď vyřešit chyby [, vyřešit](vmware-physical-azure-troubleshoot-process-server.md) je, **nebo** nastavit [procesový Server se škálováním na více](vmware-azure-set-up-process-server-scale.md)instancí.
    ![povolení okna zdroje replikace](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Od [verze 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)jsou zavedeny další výstrahy, které zvyšují výstrahy týkající se stavu procesového serveru. Aby se vygenerovaly všechny výstrahy, upgradujte Site Recovery součásti na verzi 9,24 nebo vyšší.

6. V části **cíl**vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače s podporou převzetí služeb při selhání. Vyberte model nasazení, který chcete v Azure použít pro virtuální počítače s podporou převzetí služeb při selhání.
2. Vyberte síť Azure a podsíť, ke kterým se budou virtuální počítače Azure připojovat po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor služby Site Recovery.

   Vyberte **Konfigurovat pro vybrané počítače** a použijte nastavení sítě na všechny virtuální počítače, které jste vybrali pro ochranu. Pokud chcete vybrat síť Azure na virtuální počítač, vyberte **Konfigurovat později** . Pokud nemáte síť, musíte ji vytvořit. Pokud chcete vytvořit síť pomocí Azure Resource Manager, vyberte **vytvořit novou**. Vyberte podsíť, pokud je k dispozici, a pak vyberte **OK**.
   
   ![Povolit cílové okno replikace](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Pro **virtuální počítače** > **vyberte virtuální**počítače, vyberte všechny virtuální počítače, které chcete replikovat. Můžete vybrat jenom virtuální počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud nemůžete zobrazit nebo vybrat žádný konkrétní virtuální počítač, přečtěte si téma [zdrojový počítač není uveden v Azure Portal](https://aka.ms/doc-plugin-VM-not-showing) k vyřešení problému.

    ![Povolit okno Vybrat virtuální počítače pro replikaci](./media/vmware-azure-enable-replication/enable-replication5.png)

1. U **vlastnosti** > **Konfigurovat vlastnosti**vyberte účet, který procesový Server používá k automatické instalaci služby Site Recovery mobility na virtuálním počítači. Také vyberte typ cílového spravovaného disku, který se má replikovat na základě vzorů četnosti změn dat.
10. Ve výchozím nastavení se replikují všechny disky zdrojového virtuálního počítače. Pokud chcete vyloučit disky z replikace, zrušte zaškrtnutí políčka **Zahrnout** pro všechny disky, které nechcete replikovat. Pak vyberte **OK**. Později můžete nastavit další vlastnosti. Přečtěte si další informace o [vyloučení disků](vmware-azure-exclude-disk.md).

    ![Povolit okno pro konfiguraci replikace – vlastnosti](./media/vmware-azure-enable-replication/enable-replication6.png)

1. V **nastavení replikace** > **nakonfigurovat nastavení replikace**, ověřte, jestli je vybraná správná zásada replikace. Nastavení zásad replikace můžete změnit v **nastavení** > **Zásady replikace** > ***název zásad*** > **Upravit nastavení**. Změny, které použijete u zásad, platí i pro replikaci a nové virtuální počítače.
1. Pokud chcete shromáždit virtuální počítače do replikační skupiny, povolte **konzistenci pro víc virtuálních počítačů** . Zadejte název skupiny a pak vyberte **OK**.

    > [!NOTE]
    >    * Virtuální počítače v replikační skupině se replikují společně a mají při převzetí služeb při selhání sdílené body obnovení konzistentní s havárií a konzistentní vzhledem k aplikacím.
    >    * Shromážděte virtuální počítače a fyzické servery tak, aby zrcadlí vaše úlohy. Povolení konzistence s více virtuálními počítači může mít vliv na výkon úloh. To udělejte jenom v případě, že virtuální počítače používají stejnou úlohu a potřebujete konzistenci.

    ![Povolit okno replikace](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Vyberte **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat v **nastavení** > **úlohy** > **Site Recovery úloh**. Po spuštění úlohy **Dokončit ochranu** je virtuální počítač připravený na převzetí služeb při selhání.

## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

Pak ověřte vlastnosti zdrojového virtuálního počítače. Pamatujte, že název virtuálního počítače Azure musí splňovat [požadavky na virtuální počítače Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. V části **nastavení** > **replikované položky**a vyberte virtuální počítač. Na stránce **základy** se zobrazí informace o nastavení a stavu virtuálního počítače.
1. V části **Vlastnosti** můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální počítač.
1. V **výpočetních a síťových** > **výpočetních vlastnostech**můžete změnit více vlastností virtuálního počítače. 

    ![Okno Vlastnosti výpočtů a sítě](./media/vmware-azure-enable-replication/vmproperties.png)

    * Název virtuálního počítače Azure: v případě potřeby upravte název tak, aby splňoval požadavky Azure.
    * Velikost cílového virtuálního počítače nebo typ virtuálního počítače: výchozí velikost virtuálního počítače se volí na základě několika parametrů, které zahrnují počet disků, počet síťových adaptérů, počet jader procesoru, paměť a dostupné velikosti rolí virtuálních počítačů v cílové oblasti Azure. Azure Site Recovery vybírá první dostupnou velikost virtuálního počítače, která splňuje všechna kritéria. V závislosti na vašich potřebách můžete kdykoli vybrat jinou velikost virtuálního počítače před převzetím služeb při selhání. Všimněte si, že velikost disku virtuálního počítače je taky založená na velikosti zdrojového disku a dá se změnit jenom po převzetí služeb při selhání. Přečtěte si další informace o velikostech disků a frekvenci IOPS při [škálovatelnosti a cílech výkonu pro disky virtuálních počítačů ve Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Skupina prostředků: můžete vybrat [skupinu prostředků](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), ze které se virtuální počítač stal součástí po převzetí služeb při selhání. Toto nastavení můžete kdykoli změnit v průběhu převzetí služeb při selhání. Pokud po převzetí služeb při selhání migrujete virtuální počítač do jiné skupiny prostředků, nastavení ochrany této přerušení virtuálního počítače.
    * Skupina dostupnosti: Pokud váš virtuální počítač musí být součástí po převzetí služeb při selhání, můžete vybrat [skupinu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) . Když vyberete skupinu dostupnosti, mějte na paměti následující informace:

        * V seznamu se zobrazí jenom skupiny dostupnosti, které patří do zadané skupiny prostředků.  
        * Virtuální počítače, které jsou v různých virtuálních sítích, nemůžou být součástí stejné skupiny dostupnosti.
        * Pouze virtuální počítače se stejnou velikostí můžou být součástí skupiny dostupnosti.
1. Můžete taky přidat informace o cílové síti, podsíti a IP adrese, která je přiřazená k virtuálnímu počítači Azure.
2. V části **disky**můžete zobrazit operační systém a datové disky na virtuálním počítači, který se bude replikovat.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurace sítí a IP adres

Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, používá virtuální počítač s podporou převzetí služeb při selhání službu DHCP. Pokud nastavíte adresu, která není dostupná při převzetí služeb při selhání, převzetí služeb při selhání nefunguje. Pokud je adresa dostupná v síti testovacího převzetí služeb při selhání, můžete pro testovací převzetí služeb při selhání použít stejnou cílovou IP adresu.

Počet síťových adaptérů se určuje podle velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:

- Pokud je počet síťových adaptérů ve zdrojovém virtuálním počítači menší nebo roven počtu adaptérů, které jsou povoleny pro velikost cílového virtuálního počítače, má cíl stejný počet adaptérů jako zdroj.
- Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro velikost cílového virtuálního počítače, použije se maximální velikost cíle. Pokud má zdrojový virtuální počítač například dva síťové adaptéry a velikost cílového virtuálního počítače podporuje čtyři, má cílový virtuální počítač dva adaptéry. Pokud má zdrojový virtuální počítač dva adaptéry, ale cílová velikost podporuje pouze jeden, cílový virtuální počítač má pouze jeden adaptér.
- Pokud má virtuální počítač několik síťových adaptérů, připojí se ke stejné síti. První adaptér, který je zobrazený v seznamu, se také stal *výchozím* síťovým adaptérem na virtuálním počítači Azure. 

### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Zákazníci se službou Software Assurance společnosti Microsoft mohou pomocí Zvýhodněné hybridní využití Azure ušetřit náklady na licencování pro počítače se systémem Windows Server, které jsou migrovány do Azure. Výhoda se vztahuje také na zotavení po havárii Azure. Pokud máte nárok, můžete přidělit výhod virtuálnímu počítači, který Site Recovery vytvoří, pokud dojde k převzetí služeb při selhání. Provedete to podle těchto kroků:
1. Přejít do **vlastností počítače a sítě** replikovaného virtuálního počítače.
2. Odpovězte, když máte licenci Windows serveru, která vám poskytne nárok na Zvýhodněné hybridní využití Azure.
3. Potvrďte, že máte způsobilou licenci na Windows Server se Software Assurance, kterou můžete použít pro použití výhody pro virtuální počítač, který se vytvoří při převzetí služeb při selhání.
4. Uložte nastavení pro replikovaný virtuální počítač.

Přečtěte si další informace o [zvýhodněné hybridní využití Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Řešení běžných problémů

* Každý disk by měl být menší než 4 TB.
* Disk s operačním systémem by měl být základní disk, nikoli dynamický disk.
* V případě generace 2/virtuálních počítačů s podporou rozhraní UEFI by měla být řada operačních systémů Windows a spouštěcí disk by měl být menší než 300 GB.

## <a name="next-steps"></a>Další kroky

Až virtuální počítač dosáhne chráněného stavu, zkuste [převzetí služeb při selhání](site-recovery-failover.md) , abyste zkontrolovali, jestli se vaše aplikace zobrazuje v Azure.

* Naučte se [vyčistit nastavení registrace a ochrany](site-recovery-manage-registration-and-protection.md) a zakázat replikaci.
* Naučte se [automatizovat replikaci pro virtuální počítače pomocí PowerShellu](vmware-azure-disaster-recovery-powershell.md).
