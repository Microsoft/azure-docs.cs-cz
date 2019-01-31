---
title: Migrace virtuálních počítačů Windows na Azure Premium Storage pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Migrace stávajících virtuálních počítačů na Azure Premium Storage s využitím Site Recovery. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy můžu vstupně-výstupními operacemi na Azure Virtual Machines.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 6db263dcfc3195c9b2ab3afe7587845a4632fd1b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456523"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrace na Premium Storage pomocí Azure Site Recovery

[Azure Premium Storage](premium-storage.md) poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače (VM), na kterých běží I intenzivních vstupně-výstupních operací. Tento průvodce vám pomůže migrovat disky virtuálních počítačů z účtu standard storage do účtu služby premium storage s použitím [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery je služba Azure, která přispívá ke strategii pro provozní kontinuitu a zotavení po havárii tím, že orchestruje replikaci místních fyzických serverů a virtuálních počítačů do cloudu (Azure) nebo do sekundárního datacentra. Když ve vaší primární lokalitě dojde k výpadku, převezme služby při selhání sekundárního umístění, aby aplikace a úlohy zůstaly dostupné. Nedodržíte zpět do primárního umístění, když se vrátí do normálního provozu. 

Site Recovery poskytuje testovací převzetí služeb při selhání pro podporu zotavení po havárii, aniž to ovlivní produkční prostředí. Můžete spustit převzetí služeb při selhání se ztrátou dat minimální (v závislosti na četnosti replikací) pro neočekávané havárií. Ve scénáři migrace na Premium Storage, můžete použít [převzetí služeb při selhání v Site Recovery](../../site-recovery/site-recovery-failover.md) migrace cílové disky do účtu služby premium storage.

Doporučujeme migrovat na Premium Storage s využitím Site Recovery, protože tato možnost poskytuje minimálními prostoji. Tato možnost také zabraňuje provedení ruční kopírování disků a vytváření nových virtuálních počítačů. Site Recovery bude systematicky kopírování disků a vytvořit nové virtuální počítače během převzetí služeb při selhání. 

Site Recovery podporuje několik typů převzetí služeb při selhání s minimálními nebo nedojde k žádnému výpadku. Plánování vašeho výpadky a odhadnout ztráty dat, najdete v článku [typy převzetí služeb při selhání v Site Recovery](../../site-recovery/site-recovery-failover.md). Pokud jste [Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání](../../site-recovery/vmware-walkthrough-overview.md), by měl být schopný se připojit k virtuálnímu počítači Azure s použitím protokolu RDP po převzetí služeb při selhání.

![Diagram pro zotavení po havárii][1]

## <a name="azure-site-recovery-components"></a>Komponenty Azure Site Recovery

Tyto součásti Site Recovery jsou relevantní pro tento scénář migrace:

* **Konfigurační server** je virtuální počítač Azure, který koordinuje komunikaci a spravuje replikaci a obnovení procesy data. Na tomto virtuálním počítači, můžete spustit jeden instalační soubor pro instalaci konfiguračního serveru a dalších součástí, volá procesový server jako replikační brána. Přečtěte si informace o [požadavky konfiguračního serveru](../../site-recovery/vmware-walkthrough-overview.md). Nastavíte konfigurační server pouze jednou a lze ho použít pro všechny migrace do stejné oblasti.

* **Procesový server** je brána replikace, který: 

  1. Přijímá data replikace z zdrojové virtuální počítače.
  2. Optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování.
  3. Odešle data do účtu úložiště. 

  Také stará o nabízenou instalaci služby mobility na zdrojový virtuální počítače a provádí automatické zjišťování zdrojové virtuální počítače. Výchozí procesový server je nainstalovaný na konfiguračním serveru. Můžete nasadit samostatný další Procesové servery pro horizontální vašeho nasazení. Přečtěte si informace o [osvědčené postupy pro nasazení procesového serveru](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) a [nasazení dalších procesových serverů](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Pouze jednou nastavíte na procesovém serveru a můžete ho použít pro všechny migrace do stejné oblasti.

* **Služba mobility** je komponenta, která je nasazena na každý standardní virtuální počítač, který chcete replikovat. Zaznamenává datové zápisy na virtuální počítače standard a předává je na procesní server. Přečtěte si informace o [replikované počítače požadavky](../../site-recovery/vmware-walkthrough-overview.md).

Tento obrázek znázorňuje interakci tyto komponenty:

![Interakce komponenty Site Recovery][15]

> [!NOTE]
> Site Recovery nepodporuje migraci disků prostorů úložiště.

Další komponenty pro další scénáře, naleznete v tématu [architektury scénáře](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Toto jsou požadavky na Azure pro tento scénář migrace:

* Předplatné Azure.
* Účtu služby Azure premium storage k ukládání replikovaných dat.
* Azure virtuální sítě, ke kterým se připojí virtuální počítače při jejich vytvoření při převzetí služeb při selhání. Virtuální síť Azure musí být ve stejné oblasti jako ta, ve kterém běží Site Recovery.
* Účet úložiště Azure úrovně standard pro ukládání protokolů replikace. To může být stejný účet úložiště pro disky virtuálních počítačů, které se migrují.

## <a name="prerequisites"></a>Požadavky

* Seznámení s komponentami scénář relevantní migrace v předchozí části.
* Plánování vašeho výpadek podle informací o [převzetí služeb při selhání v Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Postup instalace a migrace

Site Recovery můžete použít k migraci virtuálních počítačů Azure IaaS mezi oblastmi nebo v rámci stejné oblasti. Následující pokyny jsou přizpůsobené pro tento scénář migrace z článku [replikovat virtuální počítače VMware nebo fyzických serverů do Azure](../../site-recovery/vmware-walkthrough-overview.md). Postupujte podle odkazů podrobný postup kromě pokyny v tomto článku.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: Vytvoření trezoru Služeb zotavení

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Vyberte **vytvořit prostředek** > **správu** > **Backup a Site Recovery (OMS)**. Alternativně můžete vybrat **Procházet** > **trezor služby Recovery Services** > **přidat**.
   >[!NOTE]
   >Backup a Site Recovery byl dříve součástí ![sadu OMS](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand).
1. Zadejte oblast, která bude replikován virtuálních počítačů. Pro účely migrace ve stejné oblasti vyberte oblast, kde je zdrojových virtuálních počítačů a účtů úložiště zdroje. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: Volba cílů ochrany 

1. Na virtuálním počítači, ve kterém chcete nainstalovat konfigurační server, otevřete [webu Azure portal](https://portal.azure.com).
2. Přejděte na **trezory služby Recovery Services** > **nastavení** > **Site Recovery** > **krok 1: Příprava infrastruktury** > **cíl ochrany**.

   ![Procházení do podokna cíl ochrany][2]

3. V části **cíl ochrany**, v prvním rozevíracím seznamu vyberte **do Azure**. Ve druhém seznamu, rozevíracího seznamu vyberte **nevirtualizované / jiné**a pak vyberte **OK**.

   ![Podokno cíle ochrany s vyplněné polí][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: Nastavit zdrojové prostředí (konfigurační server)

1. Stáhněte si **Azure Site Recovery Unified Setup** a registrační klíč trezoru tak, že přejdete **připravit infrastrukturu** > **připravit zdroj**  >  **Přidat Server** podoken. 
 
   Budete potřebovat registrační klíč trezoru, ke spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

   ![Procházení do podokna přidat Server][4]

2. V **přidat Server** podokně Přidat konfigurační server.

   ![Přidání podokna Server pomocí konfiguračního serveru vybrané][5]

3. Na virtuálním počítači, který používáte jako konfigurační server spuštění sjednocené instalace nainstalovat konfigurační server a procesový server. Je možné [provede na snímcích obrazovky](../../site-recovery/vmware-walkthrough-overview.md) k dokončení instalace. Najdete na následujících snímcích obrazovky pro kroky zadaný pro tento scénář migrace.

   1. V **před zahájením**vyberte **nainstalovat konfigurační server a procesový server**.

      ![Před zahájením stránku][6]

   2. V **registrace**, procházet a vyberte registrační klíč, který jste stáhli z trezoru.

      ![Stránka pro registraci do][7]

   3. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. V tomto scénáři migrace, zvolte **ne**.

      ![Stránka s podrobnostmi o prostředí][8]

4. Po dokončení instalace provést následující akce v **Microsoft Azure Site Recovery Configuration Server** okno:
 
   1. Použít **spravovat účty** kartu k vytvoření účtu tohoto Site Recovery můžete použít pro automatické zjišťování. (Ve scénáři o ochraně fyzické počítače, nastavení účtu není relevantní, ale budete potřebovat alespoň jeden účet, aby měla jedna z následujících kroků. V takovém případě můžete pojmenovat účet a heslo jako některý.) 
   2. Použití **registrace trezoru** karty nahrát soubor s přihlašovacími údaji trezoru.

      ![Kartu registrace trezoru][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: Nastavení cílového prostředí

Vyberte **připravit infrastrukturu** > **cílové**a zadejte model nasazení, který chcete použít pro virtuální počítače po převzetí služeb při selhání. Můžete zvolit **Classic** nebo **Resource Manageru**, v závislosti na vašem scénáři.

![Cíl podokno][10]

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. 

> [!NOTE]
> Pokud pro replikovaná data používáte účet premium storage, musíte nastavit účet další standard storage k ukládání protokolů replikace.

### <a name="step-5-set-up-replication-settings"></a>Krok 5: Nastavení replikace

Chcete-li ověřit, že je konfigurační server úspěšně přidružené k zásadám replikace, který vytvoříte, postupujte podle [nastavení replikace](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Krok 6: Plánování kapacity

1. Použití [Plánovač kapacity](../../site-recovery/site-recovery-capacity-planner.md) pro přesný odhad šířky pásma sítě, úložiště a dalších požadavků na provádění vašeho replikace potřebuje. 
2. Jakmile budete hotovi, vyberte **Ano, mám to hotové** v **dokončili jste plánování kapacity?**.

   ![Pole pro potvrzení, že jste dokončili, plánování kapacity][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: Instalace služby mobility a povolení replikace

1. Můžete také [nabízená instalace](../../site-recovery/vmware-walkthrough-overview.md) vašich zdrojových virtuálních počítačů nebo do [ručně nainstalovat službu mobility](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) na zdrojové virtuální počítače. Můžete najít požadavek doručením (push) instalace a cestu instalátoru ruční v odkazu zadaná. Pokud provádíte ruční instalace, může být potřeba najít konfigurační server pomocí interní IP adresa.

   ![Stránka Konfigurace podrobnosti serveru][12]

   Virtuální počítač převzetím služeb při selhání bude mít dva dočasné disky: jedné z primárního virtuálního počítače a druhý vytvořené při zřizování virtuálních počítačů v oblasti obnovení. Vyloučit dočasný disk před replikací, instalace služby mobility před povolením replikace. Další informace o tom, jak vyloučit dočasného disku najdete v tématu [vyloučit disky z replikace](../../site-recovery/vmware-walkthrough-overview.md).

2. Replikaci povolte následujícím způsobem:
   1. Vyberte **replikovat aplikaci** > **zdroj**. Po povolení replikace pro první, vyberte **+ replikovat** v trezoru povolíte replikaci pro další počítače.
   2. V kroku 1, nastavit **zdroj** jako procesový server.
   3. V kroku 2 zadejte model nasazení post-převzetí služeb při selhání, migrace do účtu služby premium storage, účet úložiště úrovně standard k ukládání protokolů a virtuální sítě, aby v případě selhání.
   4. V kroku 3 přidejte chráněných virtuálních počítačů podle IP adresy. (Interní IP adresa je vyhledat může být nutné.)
   5. V kroku 4 nakonfigurujte vlastnosti tak, že vyberete účty, které jste dřív nastavili na procesovém serveru.
   6. V kroku 5, vyberte zásadu replikace, kterou jste vytvořili dříve v "krok 5: Nastavení replikace."
   7. Vyberte **OK**.

   > [!NOTE]
   > Když virtuální počítač Azure je navrácena a znova spustit, není zaručeno, že získáte stejnou IP adresu. Pokud se změní IP adresu konfigurační server/procesový server nebo chráněné virtuální počítače Azure, replikace v tomto scénáři nemusí fungovat správně.

   ![Povolení replikace podokna s vybraný zdroj][13]

Když navrhujete prostředí služby Azure Storage, doporučujeme používat samostatné účty úložiště pro každý virtuální počítač ve skupině dostupnosti. Doporučujeme vám, že dodržíte osvědčený postup ve vrstvě úložiště [použití více účtů úložiště pro každou skupinu dostupnosti](../linux/manage-availability.md). Distribuce disky virtuálních počítačů k několika účtům úložiště pomáhá vylepšit dostupnost úložiště a distribuuje vstupně-výstupní operace v infrastruktuře úložiště Azure.

Pokud jsou vaše virtuální počítače ve skupině dostupnosti, namísto replikace disků všech virtuálních počítačů do jednoho účtu úložiště, důrazně doporučujeme migrovat několik virtuálních počítačů více než jednou. Tímto způsobem, virtuální počítače ve stejné skupině dostupnosti nesdílejí na jeden účet úložiště. Použití **povolením replikace** podokně nastavit cílový účet úložiště pro každý virtuální počítač, jeden po druhém.
 
Můžete použít model nasazení post-převzetí služeb při selhání podle vašim požadavkům. Pokud se rozhodnete jako post-převzetí služeb při selhání modelu nasazení Azure Resource Manageru, můžete převzít služby virtuálního počítače (Resource Manager) k virtuálnímu počítači (Resource Manager) nebo je převzetí služeb při selhání virtuální počítač (classic) k virtuálnímu počítači (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Krok 8: Spuštění testovacího převzetí služeb při selhání

Pokud chcete zkontrolovat, jestli se vaše replikace nedokončí, vyberte instanci Site Recovery a pak vyberte **nastavení** > **replikované položky**. Zobrazí se stav a procento procesu replikace. 

Po dokončení počáteční replikace testovacího převzetí služeb při selhání ověřit vaši strategii replikace. Podrobné kroky testovací převzetí služeb při selhání najdete v tématu [spustit testovací převzetí služeb ve službě Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Předtím, než spustíte všechny převzetí služeb při selhání, ujistěte se, že vaše virtuální počítače a strategie replikace splňují požadavky. Další informace o spuštění testovací převzetí služeb při selhání najdete v tématu [testovací převzetí služeb při selhání do Azure v Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Můžete zobrazit stav vaší testovací převzetí služeb při selhání v **nastavení** > **úlohy** > *YOUR_FAILOVER_PLAN_NAME*. V podokně můžete zobrazit rozpis kroky a výsledky o úspěchu nebo selhání. Pokud testovací převzetí služeb při selhání v kterémkoliv kroku, vyberte krok najdete v chybové zprávě. 

### <a name="step-9-run-a-failover"></a>Krok 9: Spuštění převzetí služeb při selhání

Po testovacím převzetí služeb při selhání dokončení převzetí služeb při selhání k migraci disků Premium Storage a replikaci instancí virtuálních počítačů. Postupujte podle podrobných pokynů v [převzetí služeb při selhání](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Je potřeba vybrat možnost **vypne virtuální počítače a synchronizuje nejnovější data**. Tato možnost určuje, Site Recovery se pokuste vypne chráněných virtuálních počítačů a synchronizuje data tak, aby nejnovější data bude možné převzetí služeb při selhání. Pokud tuto možnost nevyberete nebo pokus neproběhne úspěšně, bude převzetí služeb při selhání od nejnovější dostupný bod obnovení pro virtuální počítač. 

Site Recovery vytvoří instanci virtuálního počítače, jehož typ je stejná jako nebo podobné do virtuálního počítače podporující úložiště úrovně Premium. Výkon a cenu různé instance virtuálních počítačů můžete zkontrolovat tak, že přejdete do [ceny Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo [ceny virtuálních počítačů Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Pomigrační kroky

1. **Konfigurace replikované virtuální počítače na skupinu, pokud je k dispozici dostupnosti**. Site Recovery nepodporuje migrace virtuálních počítačů spolu se skupinou dostupnosti. V závislosti na nasazení replikovaný virtuální počítač proveďte jednu z následujících akcí:
   * U virtuálního počítače vytvořená prostřednictvím modelu nasazení classic: Přidáte virtuální počítač na dostupnosti na webu Azure Portal. Podrobný postup najdete v části [přidat existující virtuální počítač do skupiny dostupnosti](../linux/classic/configure-availability-classic.md).
   * U virtuálního počítače vytvořená prostřednictvím modelu nasazení Resource Manager: Uložte konfiguraci virtuálního počítače a pak odstraňte a znovu vytvořte virtuální počítače ve skupině dostupnosti. Uděláte to tak, použijte skript v [nastavit Azure Resource Manageru dostupnosti virtuálních počítačů](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Před spuštěním tohoto skriptu zkontrolujte její omezení a plánování vašeho výpadek.

2. **Odstranit staré virtuální počítače a disky**. Ujistěte se, že disky Premium jsou konzistentní s zdrojový disk a že nové virtuální počítače, provádí stejnou funkci jako zdrojové virtuální počítače. Odstraňte virtuální počítač a odstraňte disky z účtu zdrojového úložiště na webu Azure Portal. Pokud dojde k problému, který disk není odstraněný, i když odstraníte virtuální počítač, naleznete v tématu [řešení chyb při odstraňování prostředků úložiště](storage-resource-deletion-errors.md).

3. **Vyčistit infrastruktury Azure Site Recovery**. Pokud Site Recovery je už je nepotřebujete, můžete vyčistit své infrastruktury. Odstraňte replikované položky, konfigurační server a zásady obnovení a pak odstraňte trezor Azure Site Recovery.

## <a name="troubleshooting"></a>Řešení potíží

* [Monitorování a řešení problémů s ochranou virtuálních počítačů a fyzických serverů](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fóra Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Další postup

U konkrétních scénářů pro migrace virtuálních počítačů naleznete v následujících zdrojích:

* [Migrace virtuálních počítačů Azure mezi účty úložiště](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvoření a nahrání virtuálního pevného disku Windows serverem do Azure](upload-generalized-managed.md)
* [Migrace virtuálních počítačů z služeb Amazon AWS k Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Viz také následující prostředky pro další informace o Azure Storage a Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](premium-storage.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
