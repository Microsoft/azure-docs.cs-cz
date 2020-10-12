---
title: Povolení pro zotavení po havárii virtuálních počítačů VMware pomocí Azure Site Recovery
description: Tento článek popisuje, jak povolit replikaci virtuálních počítačů VMware pro zotavení po havárii pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 74870d10348421bf726b9bdc58504a74cf4105a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129929"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Povolení replikace do Azure pro virtuální počítače VMware

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů VMware do Azure.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že váš systém splňuje následující kritéria:

- [Nastavte své místní zdrojové prostředí](vmware-azure-set-up-source.md).
- [Nastavte své cílové prostředí v Azure](vmware-azure-set-up-target.md).
- Než začnete, [ověřte požadavky a předpoklady](vmware-physical-azure-support-matrix.md) . Důležité informace, které je potřeba vzít v paměti:
  - [Podporované operační systémy](vmware-physical-azure-support-matrix.md#replicated-machines) pro replikované počítače.
  - Podpora [úložiště/disku](vmware-physical-azure-support-matrix.md#storage) .
  - [Požadavky na Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) , které by měly splňovat místní počítače.

### <a name="resolve-common-issues"></a>Řešení běžných problémů

- Každý disk by měl být menší než 4 TB.
- Disk s operačním systémem by měl být základní disk, nikoli dynamický disk.
- U virtuálních počítačů s podporou rozhraní UEFI generace 2 musí být řada operačních systémů Windows a spouštěcí disk by měl být menší než 300 GB.

## <a name="before-you-start"></a>Než začnete

Při replikaci virtuálních počítačů VMware mějte na paměti tyto informace:

- Váš uživatelský účet Azure musí mít určitá [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) , aby bylo možné povolit replikaci nového virtuálního počítače do Azure.
- Virtuální počítače VMware se objevují každých 15 minut. Může trvat 15 minut nebo déle, než se virtuální počítače objeví v Azure Portal po zjištění. Když přidáte nový Server vCenter nebo hostitele vSphere, zjišťování může trvat 15 minut nebo déle.
- Může trvat 15 minut nebo déle, než se změny prostředí na virtuálním počítači aktualizují na portálu. Například instalace nástrojů VMware.
- Můžete kontrolovat čas posledního zjištění pro virtuální počítače VMware: v poli **Poslední kontakt** na stránce **konfigurační servery** pro hostitele vCenter Server/vSphere.
- Pokud chcete přidat virtuální počítače pro replikaci bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (ale neklikejte na něj) a vyberte **aktualizovat**.
- Pokud povolíte replikaci, procesový Server ve virtuálním počítači automaticky nainstaluje službu Azure Site Recovery mobility.

## <a name="enable-replication"></a>Povolení replikace

Před provedením kroků v této části si přečtěte následující informace:

- Azure Site Recovery se teď replikuje přímo na spravované disky pro všechny nové replikace. Procesový Server zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti. Tyto protokoly slouží k vytváření bodů obnovení na spravovaných discích repliky, které mají zásady vytváření názvů `asrseeddisk` .
- Podpora PowerShellu pro replikaci na spravované disky je k dispozici od začátku pomocí [AZ. RecoveryServices Module verze 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) .
- V době převzetí služeb při selhání se k vytvoření cíle spravovaného disku použije vybraný bod obnovení.
- Virtuální počítače, které byly dřív nakonfigurované pro replikaci na cílové účty úložiště, to neovlivní.
- Replikace do účtů úložiště pro nový virtuální počítač je k dispozici jenom prostřednictvím rozhraní REST API pro reprezentaci přenosů stavu (REST) a PowerShellu. Pro replikaci do účtů úložiště použijte Azure REST API verze 2016-08-10 nebo 2018-01-10.

Pokud chcete povolit replikaci, postupujte následovně:

1. Přejít na **Krok 2: replikování**  >  **zdroje**aplikace. Po prvním povolení replikace vyberte **+ replikovat** v trezoru, abyste povolili replikaci pro další virtuální počítače.
1. **Na zdrojové stránce >** **zdroj**vyberte konfigurační server.
1. Jako **typ počítače**vyberte **Virtual Machines** nebo **fyzické počítače**.
1. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud provádíte replikaci fyzických počítačů.
1. Vyberte procesový Server. Pokud nevytvoříte žádné další procesní servery, bude v rozevírací nabídce k dispozici sestavený procesový Server konfiguračního serveru. Stav každého procesového serveru je uvedený podle doporučených omezení a dalších parametrů. Vyberte procesový Server v pořádku. Nelze zvolit [kritický](vmware-physical-azure-monitor-process-server.md#process-server-alerts) procesový Server. Můžete buď vyřešit chyby [, vyřešit](vmware-physical-azure-troubleshoot-process-server.md) je, **nebo** nastavit [procesový Server se škálováním na více](vmware-azure-set-up-process-server-scale.md)instancí.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Povolit okno zdroje replikace":::

   > [!NOTE]
   > Od [verze 9,24](site-recovery-whats-new.md)jsou zavedeny další výstrahy, které zvyšují výstrahy týkající se stavu procesového serveru. Aby se vygenerovaly všechny výstrahy, upgradujte součásti Site Recovery na verzi 9,24 nebo vyšší.

1. V části **cíl**vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače, u kterých došlo k převzetí služeb při selhání. Vyberte model nasazení, který chcete v Azure použít pro virtuální počítače, u kterých došlo k převzetí služeb při selhání.
1. Vyberte síť Azure a podsíť, ke kterým se budou virtuální počítače Azure připojovat po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor služby Site Recovery.

   Vyberte **Konfigurovat pro vybrané počítače** a použijte nastavení sítě na všechny virtuální počítače, které jste vybrali pro ochranu. Pokud chcete vybrat síť Azure na virtuální počítač, vyberte **Konfigurovat později** . Pokud nemáte síť, musíte ji vytvořit. Pokud chcete vytvořit síť pomocí Azure Resource Manager, vyberte **vytvořit novou**. Vyberte podsíť, pokud je k dispozici, a pak vyberte **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Povolit okno zdroje replikace":::

1. Pro **virtuální počítače**  >  **vyberte virtuální**počítače a vyberte všechny virtuální počítače, které chcete replikovat. Můžete vybrat jenom virtuální počítače, pro které je možné povolit replikaci. Pak vyberte **OK**. Pokud nemůžete zobrazit nebo vybrat žádný konkrétní virtuální počítač, přečtěte si téma [zdrojový počítač není uveden v Azure Portal](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) k vyřešení problému.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Povolit okno zdroje replikace":::

1. V části **vlastnosti**  >  **Konfigurace vlastností**vyberte účet, který procesový Server používá k automatické instalaci služby Site Recovery mobility na virtuálním počítači. Také vyberte typ cílového spravovaného disku, který se má použít pro replikaci na základě vzorů četnosti změn dat.
1. Ve výchozím nastavení se replikují všechny disky zdrojového virtuálního počítače. Pokud chcete vyloučit disky z replikace, zrušte zaškrtnutí políčka **Zahrnout** pro všechny disky, které nechcete replikovat. Pak vyberte **OK**. Později můžete nastavit další vlastnosti. [Přečtěte si další informace](vmware-azure-exclude-disk.md) o vyloučení disků.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Povolit okno zdroje replikace":::

1. V **nastavení replikace**  >  **nakonfigurujte nastavení replikace**a ověřte, jestli je vybraná správná zásada replikace. Nastavení zásad replikace můžete upravit nastavením zásady **Settings**  >  **replikace**nastavení  >  _název_  >  **Upravit nastavení**. Změny použité u zásad platí taky pro replikaci a nové virtuální počítače.
1. Pokud chcete shromáždit virtuální počítače do replikační skupiny, povolte **konzistenci pro víc virtuálních počítačů**. Zadejte název skupiny a pak vyberte **OK**.

   > [!NOTE]
   > - Virtuální počítače v replikační skupině se replikují společně a mají při převzetí služeb při selhání sdílené body obnovení konzistentní s havárií a konzistentní vzhledem k aplikacím.
   > - Shromážděte virtuální počítače a fyzické servery tak, aby zrcadlí vaše úlohy. Povolení konzistence s více virtuálními počítači může mít vliv na výkon úloh. To udělejte jenom v případě, že virtuální počítače používají stejnou úlohu a potřebujete konzistenci.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Povolit okno zdroje replikace":::

1. Vyberte **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat v části **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**. Po spuštění úlohy **Dokončit ochranu** je virtuální počítač připravený na převzetí služeb při selhání.

## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

Pak ověřte vlastnosti zdrojového virtuálního počítače. Pamatujte, že název virtuálního počítače Azure musí splňovat [požadavky na virtuální počítače Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Přejít na **Nastavení**  >  **replikované položky**a potom vyberte virtuální počítač. Na stránce **základy** se zobrazí informace o nastavení a stavu virtuálního počítače.
1. V části **vlastnosti**můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální počítač.
1. V **výpočetních a síťových**  >  **výpočetních vlastnostech**můžete změnit více vlastností virtuálního počítače.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Povolit okno zdroje replikace":::

   - **Název virtuálního počítače Azure**: v případě potřeby upravte název tak, aby splňoval požadavky Azure.
   - **Velikost cílového virtuálního počítače nebo typ virtuálního**počítače: výchozí velikost virtuálního počítače se volí na základě parametrů, které zahrnují počet disků, počet síťových adaptérů, počet jader procesoru, paměť a dostupné velikosti rolí virtuálních počítačů v cílové oblasti Azure. Azure Site Recovery vybírá první dostupnou velikost virtuálního počítače, která splňuje všechna kritéria. V závislosti na vašich potřebách můžete kdykoli vybrat jinou velikost virtuálního počítače před převzetím služeb při selhání. Velikost disku virtuálního počítače je taky založená na velikosti zdrojového disku a dá se změnit jenom po převzetí služeb při selhání. Přečtěte si další informace o velikostech disků a frekvenci IOPS při [škálovatelnosti a cílech výkonu pro disky virtuálních počítačů ve Windows](../virtual-machines/windows/disk-scalability-targets.md).
   - **Skupina prostředků**: můžete vybrat [skupinu prostředků](../azure-resource-manager/management/overview.md#resource-groups), ze které se virtuální počítač stal součástí po převzetí služeb při selhání. Toto nastavení můžete kdykoli změnit v průběhu převzetí služeb při selhání. Pokud po převzetí služeb při selhání migrujete virtuální počítač do jiné skupiny prostředků, nastavení ochrany této přerušení virtuálního počítače.
   - **Skupina dostupnosti**: Pokud váš virtuální počítač musí být součástí po převzetí služeb při selhání, můžete vybrat [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) . Když vyberete skupinu dostupnosti, mějte na paměti následující informace:
     - V seznamu se zobrazí jenom skupiny dostupnosti, které patří do zadané skupiny prostředků.
     - Virtuální počítače, které jsou v různých virtuálních sítích, nemůžou být součástí stejné skupiny dostupnosti.
     - Pouze virtuální počítače se stejnou velikostí můžou být součástí skupiny dostupnosti.

1. Můžete taky přidat informace o cílové síti, podsíti a IP adrese, která je přiřazená k virtuálnímu počítači Azure.
1. V části **disky**můžete zobrazit operační systém a datové disky na virtuálním počítači, který se bude replikovat.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurace sítí a IP adres

Můžete nastavit cílovou IP adresu:

- Pokud adresu nezadáte, virtuální počítač s převzetím služeb při selhání používá službu DHCP.
- Pokud nastavíte adresu, která není dostupná při převzetí služeb při selhání, převzetí služeb při selhání nefunguje.
- Pokud je adresa dostupná v síti testovacího převzetí služeb při selhání, můžete pro testovací převzetí služeb při selhání použít stejnou cílovou IP adresu.

Počet síťových adaptérů se určuje podle velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:

- Pokud je počet síťových adaptérů ve zdrojovém virtuálním počítači menší nebo roven počtu adaptérů, které jsou povoleny pro velikost cílového virtuálního počítače, má cíl stejný počet adaptérů jako zdroj.
- Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro velikost cílového virtuálního počítače, použije se maximální velikost cíle. Pokud má zdrojový virtuální počítač například dva síťové adaptéry a velikost cílového virtuálního počítače podporuje čtyři, má cílový virtuální počítač dva adaptéry. Pokud má zdrojový virtuální počítač dva adaptéry, ale cílová velikost podporuje pouze jeden, cílový virtuální počítač má pouze jeden adaptér.
- Pokud má virtuální počítač několik síťových adaptérů, připojí se ke stejné síti. První adaptér, který je zobrazený v seznamu, se také stal výchozím síťovým adaptérem na virtuálním počítači Azure.

### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Zákazníci se službou Software Assurance společnosti Microsoft mohou pomocí Zvýhodněné hybridní využití Azure ušetřit náklady na licencování pro počítače se systémem Windows Server, které jsou migrovány do Azure. Výhoda se vztahuje také na zotavení po havárii Azure. Pokud máte nárok, můžete přidělit výhod virtuálnímu počítači, který Site Recovery vytvoří, pokud dojde k převzetí služeb při selhání.

1. Přejít do **vlastností počítače a sítě** replikovaného virtuálního počítače.
1. Odpovězte, když máte licenci Windows serveru, která vám poskytne nárok na Zvýhodněné hybridní využití Azure.
1. Potvrďte, že máte způsobilou licenci na Windows Server se Software Assurance, kterou můžete použít pro použití výhody pro virtuální počítač, který se vytvoří při převzetí služeb při selhání.
1. Uložte nastavení pro replikovaný virtuální počítač.

[Přečtěte si další informace](https://azure.microsoft.com/pricing/hybrid-benefit/) o zvýhodněné hybridní využití Azure.

## <a name="next-steps"></a>Další kroky

Až virtuální počítač dosáhne chráněného stavu, zkuste [převzetí služeb při selhání](site-recovery-failover.md) , abyste zkontrolovali, jestli se vaše aplikace zobrazuje v Azure.

- [Přečtěte si další informace](site-recovery-manage-registration-and-protection.md) o tom, jak vyčistit nastavení registrace a ochrany a zakázat replikaci.
- [Přečtěte si další informace](vmware-azure-disaster-recovery-powershell.md) o tom, jak automatizovat replikaci pro virtuální počítače pomocí PowerShellu.
