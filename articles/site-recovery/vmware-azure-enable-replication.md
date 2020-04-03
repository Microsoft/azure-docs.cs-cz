---
title: Povolení virtuálních počítačů VMware pro zotavení po havárii pomocí Azure Site Recovery
description: Tento článek popisuje, jak povolit replikaci virtuálních počítačů VMware pro zotavení po havárii pomocí služby Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584133"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Povolení replikace do virtuálních počítačů Azure pro Virtuální Měše

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů VMware (VM) do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že váš systém splňuje následující kritéria:

- [Nastavte místní zdrojové prostředí](vmware-azure-set-up-source.md).
- [Nastavte si cílové prostředí v Azure](vmware-azure-set-up-target.md).
- Před zahájením [ověřte požadavky a požadavky.](vmware-physical-azure-support-matrix.md) Důležité věci, které je třeba poznamenat, zahrnují:
  - [Podporované operační systémy](vmware-physical-azure-support-matrix.md#replicated-machines) pro replikované počítače.
  - [Podpora úložiště/disku.](vmware-physical-azure-support-matrix.md#storage)
  - [Požadavky Azure,](vmware-physical-azure-support-matrix.md#azure-vm-requirements) které by měly splňovat místní počítače.

### <a name="resolve-common-issues"></a>Řešení běžných problémů

- Každý disk by měl být menší než 4 TB.
- Disk operačního systému by měl být běžný disk, nikoli dynamický disk.
- Pro virtuální počítače s podporou UEFI generace 2 by měla být rodina operačního systému Windows a spouštěcí disk by měl být menší než 300 GB.

## <a name="before-you-start"></a>Než začnete

Při replikaci virtuálních počítačů VMware mějte na paměti tyto informace:

- Váš uživatelský účet Azure musí mít určitá [oprávnění,](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) která umožní replikaci nového virtuálního počítače do Azure.
- Virtuální mamy VMware jsou zjištěny každých 15 minut. Může trvat 15 minut nebo více pro virtuální počítače se zobrazí na webu Azure portal po zjišťování. Když přidáte nový server vCenter nebo hostitele vSphere, může zjišťování trvat 15 minut nebo déle.
- Může trvat 15 minut nebo více pro změny prostředí ve virtuálním počítači, které mají být aktualizovány na portálu. Například instalace nástrojů VMware.
- Můžete zkontrolovat naposledy zjištěný čas virtuálních počítačů VMware: Podívejte se na pole **Poslední kontakt na** stránce **Konfigurační servery** pro hostitele serveru vCenter/vSphere.
- Pokud chcete přidat virtuální počítače pro replikaci bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (ale neklikejte na něj) a vyberte **Aktualizovat**.
- Když povolíte replikaci, pokud je připravený virtuální počítač, procesní server automaticky nainstaluje službu Mobility azure site recovery na virtuální ms.

## <a name="enable-replication"></a>Povolení replikace

Než provedete kroky v této části, přečtěte si následující informace:

- Azure Site Recovery se teď replikuje přímo na spravované disky pro všechny nové replikace. Procesní server zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti. Tyto protokoly se používají k vytvoření bodů obnovení na `asrseeddisk`replikách spravovaných disků, které mají konvence pojmenování aplikace .
- Podpora prostředí PowerShell pro replikaci na spravované disky je k dispozici počínaje [modulem Az.RecoveryServices verze 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- V době převzetí služeb při selhání se vybraný bod obnovení použije k vytvoření cílového spravovaného disku.
- Virtuální počítače, které byly dříve nakonfigurované pro replikaci na cílové účty úložiště, nejsou ovlivněny.
- Replikace na účty úložiště pro nový virtuální počítač je k dispozici jenom prostřednictvím rozhraní API reprezentace přenosu stavu (REST) a prostředí PowerShell. Pro replikaci na účty úložiště použijte rozhraní AZURE REST API verze 2016-08-10 nebo 2018-01-10.

Chcete-li povolit replikaci, postupujte takto:

1. Přejděte na **krok 2: Replikace** > **zdroje**aplikace . Po prvním povolení replikace vyberte možnost **+Replikovat** v úschovně, chcete-li povolit replikaci pro další virtuální počítače.
1. Na **zdrojové** stránce > **zdroj**vyberte konfigurační server.
1. V **případě typu Počítač**vyberte možnost Virtuální **počítače** nebo Fyzické **počítače**.
1. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud replikujete fyzické počítače.
1. Vyberte procesní server. Pokud nejsou vytvořeny žádné další procesní servery, bude v rozevírací nabídce k dispozici vestavěný procesní server konfiguračního serveru. Stav každého procesního serveru je uveden podle doporučených limitů a dalších parametrů. Zvolte server pro zpracování v pořádku. Kritický [critical](vmware-physical-azure-monitor-process-server.md#process-server-alerts) procesový server nelze vybrat. Můžete buď [vyřešit a vyřešit](vmware-physical-azure-troubleshoot-process-server.md) chyby **nebo** nastavit [horizontální navýšení kapacity procesu serveru](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Povolení zdrojového okna replikace":::

   > [!NOTE]
   > Počínaje [verzí 9.24](site-recovery-whats-new.md)jsou zavedeny další výstrahy, které vylepšují výstrahy stavu serveru procesu. Upgradujte součásti site recovery na verzi 9.24 nebo vyšší, aby byly generovány všechny výstrahy.

1. V **části Cíl**vyberte skupinu předplatného a prostředků, ve které chcete vytvořit počet počítačů, které se nezdařily nad virtuálními počítači. Vyberte model nasazení, který chcete použít v Azure pro opakované virtuální počítače.
1. Vyberte síť Azure a podsíť, ke které se virtuální počítače Azure připojí po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor služby Obnovení webu.

   Vyberte **Konfigurovat nyní pro vybrané počítače** použít nastavení sítě na všechny virtuální počítače, které vyberete pro ochranu. Vyberte **Konfigurovat později** a vyberte síť Azure pro každý virtuální počítač. Pokud síť nemáte, musíte ji vytvořit. Pokud chcete vytvořit síť pomocí Správce prostředků Azure, vyberte **Vytvořit nový**. Vpřípadě potřeby vyberte podsíť a pak vyberte **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Povolení cílového okna replikace":::

1. U **virtuálních počítačů** > **Vyberte virtuální počítače**vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze virtuální počítače, pro které lze povolit replikaci. Pak vyberte **OK**. Pokud nevidíte nebo nevybíráte žádný konkrétní virtuální počítač, přečtěte si téma [Zdrojový počítač není uvedený na webu Azure Portal,](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) kde problém vyřešíte.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Povolení replikace Okno Vybrat virtuální počítače":::

1. V **části Vlastnosti** > **Konfigurace vlastností**vyberte účet, který procesový server používá k automatické instalaci služby Mobility obnovení lokality na virtuální počítač. Vyberte také typ cílového spravovaného disku, který se má použít pro replikaci na základě vzorů změn dat.
1. Ve výchozím nastavení jsou replikovány všechny disky zdrojového virtuálního počítače. Chcete-li vyloučit disky z replikace, zrušte zaškrtnutí políčka **Zahrnout** u všech disků, které nechcete replikovat. Pak vyberte **OK**. Později můžete nastavit další vlastnosti. [Přečtěte si další informace](vmware-azure-exclude-disk.md) o vyloučení disků.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Povolit okno konfigurovat vlastnosti replikace":::

1. V >  **nastavení replikace****Nakonfigurujte nastavení replikace**ověřte, zda je vybrána správná zásada replikace. Nastavení zásad replikace můžete upravit na adrese **Nastavení** > _policy name_ > zásad > **replikace****Název zásad Upravit nastavení**. Změny použité v zásadách platí také pro replikaci a nové virtuální počítače.
1. Pokud chcete shromáždit virtuální počítače do replikační skupiny, povolte **konzistenci více virtuálních počítačů**. Zadejte název skupiny a pak vyberte **OK**.

   > [!NOTE]
   > - Virtuální počítače v replikační skupině replikují společně a při převzetí služeb při selhání mají sdílené body obnovení konzistentní s havárií a konzistentní s aplikací.
   > - Shromážděte virtuální počítače a fyzické servery dohromady, aby odrážely vaše úlohy. Povolení konzistence více virtuálních montovny může ovlivnit výkon pracovního vytížení. To provést pouze v případě, že virtuální počítače běží stejné úlohy a potřebujete konzistenci.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Povolit okno replikace":::

1. Vyberte **Povolit replikaci**. Průběh úlohy **Povolit ochranu** můžete sledovat v části**Úlohy** > obnovení webu úloh **y nastavení** > **úloh**. Po spuštění **úlohy Finalize Protection** je virtuální počítač připraven k převzetí služeb při selhání.

## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

Dále ověřte vlastnosti zdrojového virtuálního počítače. Nezapomeňte, že název virtuálního počítače Azure musí splňovat [požadavky na virtuální počítač Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Přejděte na **Nastavení** > **replikovaných položek**a vyberte virtuální počítač. Stránka **Essentials** zobrazuje informace o nastavení a stavu virtuálního počítače.
1. Ve **vlastnostech**můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální hod.
1. Ve **vlastnostech Výpočetní výkon a Síťový** > **výpočetní výkon**můžete změnit více vlastností virtuálního počítače.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Okno Výpočetní a síťové vlastnosti":::

   - **Název virtuálního počítače Azure**: V případě potřeby upravte název tak, aby splňoval požadavky Azure.
   - **Velikost cílového virtuálního počítače nebo typ virtuálního počítače**: Výchozí velikost virtuálního počítače se volí na základě parametrů, které zahrnují počet disků, počet nenohů, počet jader procesoru, paměť a dostupné velikosti rolí virtuálních počítačů v cílové oblasti Azure. Azure Site Recovery vybere první dostupnou velikost virtuálního počítače, která splňuje všechna kritéria. Před převzetím služeb při selhání můžete kdykoli vybrat jinou velikost virtuálního počítače podle vašich potřeb. Velikost disku virtuálního počítače je také založen na velikosti zdrojového disku a lze ji změnit pouze po převzetí služeb při selhání. Další informace o velikosti disků a rychlosti videa VOPS na [škálovatelnost a cíle výkonu pro disky virtuálních počítačů v systému Windows](/azure/virtual-machines/windows/disk-scalability-targets).
   - **Skupina prostředků**: Můžete vybrat [skupinu prostředků](/azure/azure-resource-manager/management/overview#resource-groups), ze které se virtuální počítač stane součástí převzetí služeb při selhání. Toto nastavení můžete kdykoli před převzetím služeb při selhání změnit. Po převzetí služeb při selhání, pokud migrujete virtuální počítač do jiné skupiny prostředků, nastavení ochrany pro tento konec virtuálního počítače.
   - **Dostupnost:** Můžete vybrat [sadu dostupnosti,](/azure/virtual-machines/windows/tutorial-availability-sets) pokud váš virtuální počítač musí být součástí převzetí služeb při selhání. Když vyberete sadu dostupnosti, mějte na paměti následující informace:
     - Jsou uvedeny pouze skupiny dostupnosti, které patří do zadané skupiny prostředků.
     - Virtuální počítače, které jsou v různých virtuálních sítích, nemohou být součástí stejné skupiny dostupnosti.
     - Součástí sady dostupnosti mohou být pouze virtuální počítače stejné velikosti.

1. Můžete také přidat informace o cílové síti, podsíti a IP adrese, která je přiřazená k virtuálnímu počítači Azure.
1. V **disky**, můžete vidět operační systém a datové disky na virtuálním počítači, který bude replikován.

### <a name="configure-networks-and-ip-addresses"></a>Konfigurace sítí a adres IP

Cílovou ADRESU IP můžete nastavit:

- Pokud nezadáte adresu, převzetí služby při selhání virtuálního virtuálního aplikace používá DHCP.
- Pokud nastavíte adresu, která není k dispozici při převzetí služeb při selhání, převzetí služeb při selhání nefunguje.
- Pokud je adresa k dispozici v testovací síti s podporou převzetí služeb při selhání, můžete pro převzetí služeb při selhání testu použít stejnou cílovou adresu IP.

Počet síťových adaptérů je dán velikostí, kterou zadáte pro cílový virtuální počítač, a to následovně:

- Pokud je počet síťových adaptérů ve zdrojovém virtuálním počítači menší nebo roven počtu adaptérů, které jsou povoleny pro velikost cílového virtuálního počítače, cíl má stejný počet adaptérů jako zdroj.
- Pokud počet adaptérů pro zdrojový virtuální počítač překročí počet, který je povolen pro velikost cílového virtuálního počítače, použije se maximální velikost cílové velikosti. Například pokud zdrojový virtuální počítač má dva síťové adaptéry a velikost cílového virtuálního počítače podporuje čtyři, cílový virtuální počítač má dva adaptéry. Pokud zdrojový virtuální počítače má dva adaptéry, ale cílová velikost podporuje pouze jeden, cílový virtuální počítače má jenom jeden adaptér.
- Pokud má virtuální počítač více síťových adaptérů, všechny se připojují ke stejné síti. První adaptér, který se zobrazí v seznamu, se také stane výchozím síťovým adaptérem ve virtuálním počítači Azure.

### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Zákazníci microsoft software assurance mohou pomocí hybridnívýhody Azure ušetřit na licenčních nákladech pro počítače se systémem Windows Server, které jsou migrovány do Azure. Výhoda platí také pro zotavení po havárii Azure. Pokud máte nárok, můžete přiřadit výhodu virtuálnímu počítači, který site recovery vytvoří, pokud dojde k převzetí služeb při selhání.

1. Přejděte na **vlastnosti počítače a sítě** replikovaného virtuálního počítače.
1. Odpovězte na otázku, zda máte licenci windows serveru, která vám dává nárok na hybridní výhody Azure.
1. Zkontrolujte, zda máte oprávněnou licenci windows serveru s programem Software Assurance, kterou můžete použít k použití výhody pro virtuální hod, který bude vytvořen při převzetí služeb při selhání.
1. Uložte nastavení replikovaného virtuálního počítače.

[Přečtěte si další informace](https://azure.microsoft.com/pricing/hybrid-benefit/) o hybridních výhodach Azure.

## <a name="next-steps"></a>Další kroky

Jakmile virtuální počítač dosáhne chráněného stavu, zkuste [převzetí služeb při selhání](site-recovery-failover.md) zkontrolovat, jestli se vaše aplikace zobrazí v Azure.

- [Přečtěte si další informace](site-recovery-manage-registration-and-protection.md) o tom, jak vyčistit nastavení registrace a ochrany a zakázat replikaci.
- [Přečtěte si další informace](vmware-azure-disaster-recovery-powershell.md) o automatizaci replikace pro virtuální počítače pomocí PowerShellu.
