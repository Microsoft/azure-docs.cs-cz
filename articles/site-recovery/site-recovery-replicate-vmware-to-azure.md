---
title: Replikovat aplikace (VMware do Azure) | Microsoft Docs
description: "Tento článek popisuje, jak nastavit replikaci virtuálních počítačů spuštěných na VMware do Azure."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: asgang
ms.openlocfilehash: 1b63515970f81b1cab679287d84707d531fd102a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="replicate-applications-running-on-vmware-virtual-machines-to-azure"></a>Replikovat aplikací běžících na virtuálních počítačů VMware do Azure



Tento článek popisuje, jak nastavit replikaci virtuálních počítačů (VM) běžící ve VMware do Azure.
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte:

1.  [Nastavení prostředí pro místní zdroje](site-recovery-set-up-vmware-to-azure.md).
2.  [Nastavení cílového prostředí v Azure](site-recovery-prepare-target-vmware-to-azure.md).


## <a name="enable-replication"></a>Povolení replikace
### <a name="before-you-start"></a>Než začnete
Při replikaci virtuálních počítačů VMware:

* Azure uživatelský účet musí mít určité [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) k povolení replikace nového virtuálního počítače do Azure.
* Virtuální počítače VMware, jsou zjišťovány každých 15 minut. Může trvat 15 minut nebo déle se objevily na portálu Azure po zjišťování. Podobně zjišťování může trvat 15 minut nebo déle při přidání nového hostitele serveru nebo vSphere vCenter.
* Změny prostředí na virtuálním počítači (jako je třeba instalace nástroje VMware) může trvat 15 minut nebo déle aktualizovat na portálu.
* Čas poslední zjištěné můžete zkontrolovat pro virtuální počítače VMware v **poslední obraťte se na** pole pro vCenter server vSphere hostitele, na **konfigurační servery** stránky.
* Chcete-li přidat počítače pro replikaci bez čekání na naplánovaného zjišťování, zvýrazněte konfigurační server (nemáte klikněte na něj) a klikněte na tlačítko **aktualizovat** tlačítko.
* Když povolíte replikaci, pokud je počítač připravený, procesový server automaticky nainstaluje služba Mobility na něm.


### <a name="enable-replication-as-follows"></a>Následujícím způsobem povolte replikaci

1. Klikněte na **Krok 2: Replikujte aplikaci** > **Zdroj**. Po prvním povolení replikace kliknutím na **+Replikovat** v trezoru povolte replikaci pro další počítače.
2. V **zdroj** stránky > **zdroj**, vyberte konfigurační server.
3. V **počítač typ**, vyberte **virtuální počítače** nebo **fyzických počítačů**.
4. V části **vCenter/vSphere Hypervisor** vyberte vCenter Server, který spravuje hostitele vSphere, nebo vyberte samotného hostitele. Toto nastavení není relevantní, pokud replikujete fyzických počítačů.
5. Vyberte procesní server, který bude mít název serveru, konfigurace, pokud jste nevytvořili žádné další proces servery. Pak klikněte na **OK**.

    ![Povolení replikace zdroje](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. V **cíl**, vyberte předplatné a skupině prostředků, kde chcete vytvořit virtuální počítače při selhání. Vyberte model nasazení, který chcete použít v Azure (classic nebo Azure Resource Manager) pro virtuální počítače při selhání.

7. Vyberte účet úložiště Azure, kterou chcete použít pro replikaci dat. 

    > [!NOTE]

    >   * Můžete vybrat premium nebo standardní účet úložiště. Pokud vyberete prémiový účet, budete muset zadat účet další standardní úložiště pro protokoly probíhající replikace. Účty musí být ve stejné oblasti jako trezor služeb zotavení.
    >   * Pokud chcete použít jiný účet úložiště, můžete [vytvořit](../storage/common/storage-create-storage-account.md). Chcete-li vytvořit účet úložiště pomocí Správce prostředků, klikněte na tlačítko **vytvořit nový**. Pokud chcete vytvořit účet úložiště pomocí klasického modelu, udělat na portálu Azure.

8. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se zprovozní po převzetí služeb při selhání. Síť musí být ve stejné oblasti jako trezor Služeb zotavení. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač. Pokud nemáte k síti, budete muset [vytvořit](#set-up-an-azure-network). Chcete-li vytvořit síť pomocí Resource Manager, klikněte na tlačítko **vytvořit nový**. Pokud chcete vytvořit síť pomocí klasického modelu, udělat [na portálu Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Vyberte podsíť, pokud je k dispozici a pak klikněte na tlačítko **OK**.

    ![Povolit nastavení cíle replikace](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. V **virtuální počítače** > **vybrat virtuální počítače**, vyberte každý počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak klikněte na **OK**.

    ![Povolit replikaci vyberte virtuální počítače](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. V **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet použít procesní server k automatické instalaci služby Mobility na počítači.  
11. Ve výchozím nastavení se replikují všechny disky. Pokud chcete z replikace vyloučit disky, klikněte na tlačítko **všechny disky** a zrušte zaškrtnutí všech disků, které nechcete, aby k replikaci.  Pak klikněte na **OK**. Později můžete nastavit další vlastnosti. [Další informace](site-recovery-exclude-disk.md) o s výjimkou disků.

    ![Povolení replikace nakonfigurovat vlastnosti](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. V **nastavení replikace** > **nakonfigurujete nastavení replikace**, ověřte, zda je vybrán správný replikace zásad. Můžete upravit nastavení zásad replikace v **nastavení** > **zásady replikace** > (název zásady) > **upravit nastavení**. Změny, které použijete k zásadě platí také pro replikaci a nové počítače.
13. Povolit **konzistence pro víc Virtuálních** Pokud chcete shromažďovat počítače do replikační skupiny. Zadejte název skupiny a pak klikněte na tlačítko **OK**. 

    > [!NOTE]

    >    * Počítače v replikační skupině replikovat společně a mít sdílené body obnovení konzistentní při selhání a konzistentní s aplikací, když se převzetí služeb při selhání.
    >    * Shromažďování virtuálních počítačů a fyzických serverů, aby odpovídaly vašich úloh. Povolení konzistence více virtuálních počítačů může ovlivnit výkon pracovního vytížení. Použijte pouze v případě, že počítače běží stejné zatížení a potřebujete konzistenci.

    ![Povolení replikace](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.

> [!NOTE]
> Pokud je počítač připravený na nabízenou instalaci, součást služby Mobility je nainstalována, když je povolena ochrana. Poté, co je nainstalována na počítači, spustí úlohu ochrany a vyvolá chybu. Po selhání je potřeba restartovat ručně každý počítač. Po restartování znovu začne úlohu ochrany a dojde k počáteční replikaci.
>
>

## <a name="view-and-manage-vm-properties"></a>Zobrazení a správa vlastností virtuálního počítače

V dalším kroku ověřit vlastnosti zdrojového počítače. Mějte na paměti, že musí odpovídat názvu virtuálního počítače Azure [požadavky na virtuální počítač Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Klikněte na tlačítko **nastavení** > **replikované položky** > a potom vyberte počítač. **Essentials** stránka zobrazuje informace o stavu a nastavení počítače.
2. V části **Vlastnosti** můžete zobrazit informace o replikaci a převzetí služeb při selhání pro virtuální počítač.
3. V části **Výpočty a síť** > **Výpočetní vlastnosti** můžete zadat název a cílovou velikost virtuálního počítače Azure. Název pro dosažení souladu s požadavky na Azure, v případě potřeby změňte.

    ![Výpočty a vlastnosti sítě](./media/site-recovery-vmware-to-azure/vmproperties.png)

4.  Můžete vybrat [skupiny prostředků](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) ze které je počítač se stane součástí selhání post. Toto nastavení kdykoli před převzetí služeb při selhání, můžete změnit. Převzetí služeb při selhání, POST, pokud provádíte migraci počítače k jiné skupině prostředků, nastavení ochrany pro tento počítač přerušení.
5. Můžete vybrat [skupinu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) Pokud váš počítač musí být součástí selhání post. Když vybíráte skupinu dostupnosti, mějte na paměti, že:

    * Jsou uvedeny pouze skupiny dostupnosti patří do skupiny zadaný prostředek.  
    * Počítače s jinou virtuální sítě nesmí být součástí stejné skupiny dostupnosti.
    * Pouze virtuální počítače stejnou velikost může být součástí skupiny dostupnosti.
5. Můžete také zobrazit a přidání informací o Cílová síť, podsíť a IP adresy přiřazené k virtuálnímu počítači Azure.
6. V **disky**, uvidíte operačního systému a datové disky na virtuálním počítači, které se mají replikovat.

### <a name="network-adapters-and-ip-addressing"></a>Síťové adaptéry a adresování IP

- Můžete nastavit cílovou IP adresu. Pokud adresu nezadáte, počítače při selhání používá protokol DHCP. Pokud nastavíte adresu, která není k dispozici na převzetí služeb při selhání, převzetí služeb při selhání nebude fungovat. Pokud je adresa k dispozici v testovací síti převzetí služeb při selhání, stejnou cílovou IP adresu můžete použít pro testovací převzetí služeb při selhání.
- Počet síťových adaptérů závisí na velikosti, kterou zadáte pro cílový virtuální počítač, a to následujícím způsobem:
    - Pokud počet síťových adaptérů na zdrojovém počítači je menší než nebo roven počtu adaptérů, které jsou povolené pro velikost cílového počítače, cíl má stejný počet adaptérů jako zdroj.
    - Pokud počet adaptérů pro zdrojový virtuální počítač překračuje počet povolený pro cílovou velikost, použije se maximální velikost cíle.
    Například pokud má zdrojový počítač dva síťové adaptéry a velikost cílového počítače podporuje čtyři, má cílový počítač dva adaptéry. Pokud má zdrojový počítač dva adaptéry, ale podporovaná velikost cíle podporuje pouze jeden, cílového počítače má jenom jeden adaptér.
    - Pokud virtuální počítač má několik síťových adaptérů, budou všechny připojit ke stejné síti. Navíc bude první z nich uvedené v seznamu *výchozí* síťový adaptér ve virtuálním počítači Azure.

### <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Programu Microsoft Software Assurance zákazníci mohou využít výhody použití Azure hybridní uložit na licenční náklady pro Windows Server počítače, které se migrují do Azure, nebo použít Azure pro zotavení po havárii. Pokud jste vhodné používat Azure hybridní použít využívat, můžete zadat, že virtuální počítač přiřazen této výhody je ten, který vytvoří Azure Site Recovery, pokud dojde převzetí služeb při selhání. Použijte následující postup:
- Přejděte k části Vlastnosti výpočtů a sítě replikované virtuální počítače.
- Odpovězte na otázku, která požaduje, pokud máte licence systému Windows Server, že si uživatel opravňuje k Benefitu použití hybridní Azure.
- Výběrem zaškrtávacího políčka potvrďte, že máte licenci oprávněné systému Windows Server pomocí programu Software Assurance, které můžete použít výhodou použití hybridní na počítači, který bude vytvořena na převzetí služeb při selhání.
- Uložte nastavení pro replikovaného počítače.

Další informace o [Azure hybridní použití zvýhodnění](https://aka.ms/azure-hybrid-use-benefit-pricing).

## <a name="common-issues"></a>Běžné problémy

* Každý disk by měla být menší než velikost 1 TB.
* Disk operačního systému musí být základní disk a nikoli dynamický disk.
* Generace 2/UEFI-povoleno virtuální počítače operační systém řady musí být Windows a spouštěcí disk by měla být menší než 300 GB.

## <a name="next-steps"></a>Další postup

Po dokončení ochrany si je a počítač byl dosažen chráněném stavu, můžete zkusit [převzetí služeb při selhání](site-recovery-failover.md) ke kontrole, jestli vaše aplikace se zobrazí v Azure nebo ne.

Pokud chcete zakázat ochranu, přečtěte si postup [vyčistit nastavení registrace a protection](site-recovery-manage-registration-and-protection.md).
