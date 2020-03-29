---
title: Migrace virtuálních stránek SWindows do Azure Premium Storage pomocí Azure Site Recovery
description: Migrujte své stávající virtuální počítače do úložiště Azure Premium pomocí site recovery. Úložiště Premium storage nabízí vysoce výkonnou podporu disku s nízkou latencí pro úlohy náročné na vstupně-výstupní služby spuštěné na virtuálních počítačích Azure.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: bd5f9fc787a6299e8d7c14f4b99f6f4d59cf78af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74819065"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrace do úložiště Premium pomocí Azure Site Recovery

[Azure premium SSD](disks-types.md) poskytují vysoce výkonnou diskovou podporu s nízkou latencí pro virtuální počítače, na kterých běží úlohy náročné na vstupně-výstupní služby. Tato příručka vám pomůže migrovat disky virtuálních počítačů ze standardního účtu úložiště na účet úložiště pro premium pomocí [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery je služba Azure, která přispívá k vaší strategii pro kontinuitu podnikání a zotavení po havárii tím, že organizuje replikaci místních fyzických serverů a virtuálních počítačů do cloudu (Azure) nebo do sekundárního datového centra. Pokud dojde k výpadkům v primárním umístění, převzetí služeb při selhání do sekundárního umístění, aby aplikace a úlohy k dispozici. Po návratu do primárního umístění po návratu do normálního provozu se vrátí tezi na vrácení se zpět do primárního umístění. 

Site Recovery poskytuje převzetí služeb při selhání test pro podporu cvičení zotavení po havárii bez ovlivnění produkčních prostředí. Převzetí služeb při selhání můžete spustit s minimální ztrátou dat (v závislosti na četnosti replikace) pro neočekávané havárie. Ve scénáři migrace do úložiště Premium můžete použít [převzetí služeb při selhání v site recovery](../../site-recovery/site-recovery-failover.md) k migraci cílových disků do účtu úložiště s prémií.

Doporučujeme migrovat do úložiště Premium pomocí site recovery, protože tato možnost poskytuje minimální prostoje. Tato možnost také zabraňuje ručnímu spuštění kopírování disků a vytváření nových virtuálních počítačů. Site Recovery bude systematicky kopírovat disky a vytvářet nové virtuální počítače během převzetí služeb při selhání. 

Site Recovery podporuje řadu typů převzetí služeb při selhání s minimálnínebo žádné prostoje. Pokud chcete naplánovat prostoje a odhadnout ztrátu dat, podívejte se na [typy převzetí služeb při selhání v site recovery](../../site-recovery/site-recovery-failover.md). Pokud [se připravíte pro připojení k virtuálním počítačům Azure po převzetí služeb při selhání](../../site-recovery/vmware-walkthrough-overview.md), měli byste být schopni se připojit k virtuálnímu počítači Azure pomocí RDP po převzetí služeb při selhání.

![Diagram zotavení po havárii][1]

## <a name="azure-site-recovery-components"></a>Součásti obnovení webu Azure

Tyto součásti obnovení webu jsou relevantní pro tento scénář migrace:

* **Konfigurační server** je virtuální počítač Azure, který koordinuje komunikaci a spravuje procesy replikace a obnovení dat. Na tomto virtuálním počítači spustíte jeden instalační soubor pro instalaci konfiguračního serveru a další součást, nazývanou procesní server, jako replikační bránu. Přečtěte si o [požadavcích konfiguračního serveru](../../site-recovery/vmware-walkthrough-overview.md). Konfigurační server nastavíte pouze jednou a můžete jej použít pro všechny migrace do stejné oblasti.

* **Procesní server** je replikační brána, která: 

  1. Přijímá data replikace ze zdrojových virtuálních měn.
  2. Optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování.
  3. Odešle data do účtu úložiště. 

  Také zpracovává nabízenou instalaci služby mobility do zdrojových virtuálních zařízení a provádí automatické zjišťování zdrojových virtuálních zařízení. Na konfiguračním serveru je nainstalován výchozí procesní server. Můžete nasadit další samostatné procesní servery pro škálování vašeho nasazení. Přečtěte si [o doporučených postupech pro nasazení procesního serveru](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) a [nasazení dalších procesních serverů](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Procesní server nastavíte pouze jednou a můžete jej použít pro všechny migrace do stejné oblasti.

* **Služba mobility** je komponenta, která se nasadí na každý standardní virtuální počítač, který chcete replikovat. Zachycuje zápisy dat na standardní virtuální počítač a předává je na procesový server. Přečtěte si o [požadavcích replikovaného počítače](../../site-recovery/vmware-walkthrough-overview.md).

Tento obrázek znázorňuje interakci těchto součástí:

![Interakce součástí obnovení lokality][15]

> [!NOTE]
> Site Recovery nepodporuje migraci disků prostorů úložiště.

Další součásti pro jiné scénáře naleznete v [tématu Architektura scénářů](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Základy Azure

Toto jsou požadavky Azure pro tento scénář migrace:

* Předplatné Azure.
* Účet úložiště Azure premium pro ukládání replikovaných dat.
* Virtuální síť Azure, ke které se virtuální počítače připojí, když se vytvoří při převzetí služeb při selhání. Virtuální síť Azure musí být ve stejné oblasti jako ve kterém je spuštěna služba Site Recovery.
* Účet standardního úložiště Azure pro ukládání protokolů replikace. Může se jedná o stejný účet úložiště pro disky virtuálních počítačů, které jsou migrovány.

## <a name="prerequisites"></a>Požadavky

* Seznamte se s příslušnými součástmi scénáře migrace v předchozí části.
* Naplánujte si prostoje tím, že se dozvíte o [převzetí služeb při selhání v site recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Kroky nastavení a migrace

Site Recovery můžete použít k migraci virtuálních stránek Azure IaaS mezi oblastmi nebo ve stejné oblasti. Následující pokyny jsou přizpůsobeny pro tento scénář migrace z článku [Replikovat virtuální počítače VMware nebo fyzické servery do Azure](../../site-recovery/vmware-walkthrough-overview.md). Kromě pokynů v tomto článku postupujte podle odkazů na podrobné kroky.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: Vytvoření trezoru služby Recovery Services

1. Otevřete [portál Azure](https://portal.azure.com).
2. Vyberte **možnost Vytvořit** > zálohu**pro správu** > prostředků**a obnovení lokality (OMS).** Případně můžete vybrat **možnost Přidat** > **trezor** > **služby Pro obnovení .**
   >[!NOTE]
   >Zálohování a site recovery byl dříve součástí [sady OMS](/azure/azure-monitor/terminology#april-2018---retirement-of-operations-management-suite-brand).
1. Zadejte oblast, do které budou virtuální chod replikovány. Pro účely migrace ve stejné oblasti vyberte oblast, kde jsou vaše zdrojové virtuální počítače a účty zdrojového úložiště. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: Vyberte si cíle ochrany 

1. Na virtuálním počítači, kde chcete nainstalovat konfigurační server, otevřete [portál Azure](https://portal.azure.com).
2. Přejděte do **trezorů služby** > Recovery Services**Nastavení** > **obnovení webu** > **Krok 1: Připravit**cíl > **ochrany**infrastruktury .

   ![Procházení podokna cílů ochrany][2]

3. V části **Cíl ochrany**vyberte v prvním rozevíracím seznamu možnost **Do Azure**. V druhém rozevíracím seznamu vyberte **Možnost Není virtualizována / Jiná**a pak vyberte **OK**.

   ![Podokno cíle ochrany s vyplněnými rámečky][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: Nastavení zdrojového prostředí (konfigurační server)

1. Stáhněte si **sjednocené nastavení obnovení webu Azure** a registrační klíč trezoru tak, že přejdete do **panelu Připravit** > **zdroj Připravit zdroj** > **Přidat server.** 
 
   Ke spuštění jednotného nastavení budete potřebovat registrační klíč trezoru. Klíč je platný pět dní od jeho vygenerování.

   ![Procházení podokna Přidat server][4]

2. V podokně **Přidat server** přidejte konfigurační server.

   ![Podokno Přidat server s vybranou možností Konfigurační server][5]

3. Na virtuálním počítači, který používáte jako konfigurační server, spusťte sjednocené nastavení a nainstalujte konfigurační server a procesní server. Můžete [projít screenshoty](../../site-recovery/vmware-walkthrough-overview.md) k dokončení instalace. Můžete odkazovat na následující snímky obrazovky pro kroky určené pro tento scénář migrace.

   1. V **části Before You Begin**vyberte Install the configuration server and process **server**.

      ![Stránka Před zahájením][6]

   2. V **části Registrace**projděte a vyberte registrační klíč, který jste stáhli z trezoru.

      ![Registrační stránka][7]

   3. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pro tento scénář migrace zvolte **Ne**.

      ![Stránka Podrobnosti o prostředí][8]

4. Po dokončení instalace postupujte v okně **Microsoft Azure Site Recovery Configuration Server** následujícím způsobem:
 
   1. Na kartě **Spravovat účty** vytvořte účet, který může site recovery použít pro automatické zjišťování. (Ve scénáři o ochraně fyzických počítačů není nastavení účtu relevantní, ale k povolení jednoho z následujících kroků potřebujete alespoň jeden účet. V takovém případě můžete účet a heslo pojmenovat jako jakékoli jiné.) 
   2. Na kartě **Registrace úložiště** můžete nahrát soubor přihlašovacích údajů trezoru.

      ![Karta Registrace úložiště][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: Nastavení cílového prostředí

Vyberte Připravit**cíl** **infrastruktury** > a zadejte model nasazení, který chcete použít pro virtuální počítače po převzetí služeb při selhání. V závislosti na scénáři můžete zvolit **Klasický** nebo **Správce prostředků**.

![Cílové podokno][10]

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. 

> [!NOTE]
> Pokud používáte účet úložiště premium pro replikovaná data, musíte nastavit další účet standardního úložiště pro ukládání protokolů replikace.

### <a name="step-5-set-up-replication-settings"></a>Krok 5: Nastavení replikace

Chcete-li ověřit, zda je konfigurační server úspěšně přidružen k zásadám replikace, kterou vytvoříte, postupujte podle pokynů [Nastavit nastavení replikace](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Krok 6: Plánování kapacity

1. Pomocí [plánovače kapacit](../../site-recovery/site-recovery-capacity-planner.md) můžete přesně odhadnout šířku pásma sítě, úložiště a další požadavky, aby vyhovovaly vašim potřebám replikace. 
2. Až budete hotovi, vyberte **Ano, udělal jsem to** v **Už jste dokončili plánování kapacity?**.

   ![Box pro potvrzení, že jste dokončili plánování kapacity][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: Instalace služby mobility a povolení replikace

1. Můžete se [rozhodnout, že instalaci předáte](../../site-recovery/vmware-walkthrough-overview.md) zdrojovým virtuálním počítačům nebo [ručně nainstalujete službu mobility](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) na zdrojové virtuální počítače. Požadavek na zatlačení instalace a cestu ručního instalátoru naleznete v poskytnutém odkazu. Pokud provádíte ruční instalaci, možná budete muset k nalezení konfiguračního serveru použít interní adresu IP.

   ![Stránka Podrobnosti konfiguračního serveru][12]

   Virtuální modul s převzetím služeb při selhání bude mít dva dočasné disky: jeden z primárního virtuálního počítače a druhý vytvořený během zřizování virtuálního počítače v oblasti obnovení. Chcete-li dočasný disk před replikací vyloučit, nainstalujte před povolením replikace službu mobility. Další informace o tom, jak dočasný disk vyloučit, najdete v [tématu Vyloučení disků z replikace](../../site-recovery/vmware-walkthrough-overview.md).

2. Replikaci povolte následujícím způsobem:
   1. Vyberte **možnost Replikovat** > **zdroj**aplikace . Po prvním povolení replikace vyberte v trezoru **možnost +Replikovat,** chcete-li replikovat pro další počítače.
   2. V kroku 1 nastavte **zdroj** jako procesní server.
   3. V kroku 2 zadejte model nasazení po převzetí služeb při selhání, účet úložiště premium, na který se má migrovat, účet standardního úložiště pro uložení protokolů a selhání virtuální sítě.
   4. V kroku 3 přidejte chráněné virtuální počítače podle IP adresy. (K jejich vyhledání může být potřeba interní IP adresa.)
   5. V kroku 4 nakonfigurujte vlastnosti výběrem účtů, které jste nastavili dříve na procesovém serveru.
   6. V kroku 5 zvolte zásady replikace, které jste vytvořili dříve v kroku 5: Nastavení replikace.
   7. Vyberte **OK**.

   > [!NOTE]
   > Když je virtuální počítač Azure navrácena a znovu spuštěna, neexistuje žádná záruka, že získá stejnou IP adresu. Pokud se změní IP adresa konfiguračního serveru/procesního serveru nebo chráněné virtuální počítače Azure, replikace v tomto scénáři nemusí fungovat správně.

   ![Povolit podokno replikace s vybranou položkou Zdroj][13]

Při navrhování prostředí Azure Storage doporučujeme použít samostatné účty úložiště pro každý virtuální počítač v sadě dostupnosti. Doporučujeme, abyste postupovali podle osvědčených postupů ve vrstvě úložiště [a používali pro každou sadu dostupnosti více účtů úložiště](../linux/manage-availability.md). Distribuce disků virtuálních počítačů do více účtů úložiště pomáhá zlepšit dostupnost úložiště a distribuuje vstupně-va napříč infrastrukturou úložiště Azure.

Pokud jsou vaše virtuální počítače v sadě dostupnosti, namísto replikace disků všech virtuálních počítačů do jednoho účtu úložiště, důrazně doporučujeme migrovat více virtuálních počítačů vícekrát. Tímto způsobem virtuální chody ve stejné sadě dostupnosti nesdílejí jeden účet úložiště. Podokno **Povolit replikaci** slouží k nastavení cílového účtu úložiště pro každý virtuální účet po jednom.
 
Můžete zvolit model nasazení po převzetí služeb při selhání podle potřeby. Pokud jako model nasazení po převzetí služeb při selhání zvolíte Správce prostředků Azure, můžete přepojit virtuální počítač (Správce prostředků) na virtuální počítač (Resource Manager) nebo můžete převést na místo služeb při selhání virtuálního počítače (klasické) na virtuální počítač (Správce prostředků).

### <a name="step-8-run-a-test-failover"></a>Krok 8: Spuštění zkušebního převzetí služeb při selhání

Chcete-li zkontrolovat, zda je replikace dokončena, vyberte instanci obnovení lokality a pak vyberte **Nastavení** > **replikovaných položek**. Zobrazí se stav a procento procesu replikace. 

Po dokončení počáteční replikace spusťte zkušební převzetí služeb při selhání k ověření strategie replikace. Podrobné kroky převzetí služeb při selhání testu naleznete v [tématu Spuštění převzetí služeb při selhání testu v obnovení webu](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Před spuštěním jakékoli převzetí služeb při selhání, ujistěte se, že vaše virtuální počítače a strategie replikace splňují požadavky. Další informace o spuštění testu převzetí služeb při selhání najdete v [tématu Test převzetí služeb při selhání do Azure v site recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Stav převzetí služeb při selhání testu najdete v*YOUR_FAILOVER_PLAN_NAME*úloh**y** >  **Nastavení** > . V podokně se zobrazí rozpis kroků a výsledky úspěšnosti/neúspěchu. Pokud test převzetí služeb při selhání selže v libovolném kroku, vyberte krok pro kontrolu chybové zprávy. 

### <a name="step-9-run-a-failover"></a>Krok 9: Spuštění převzetí služeb při selhání

Po dokončení testu převzetí služeb při selhání spusťte převzetí služeb při selhání, chcete-li migrovat disky do úložiště Premium a replikovat instance virtuálních počítačů. Postupujte podle podrobných kroků v [spuštění převzetí služeb při selhání](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Nezapomeňte vybrat **Vypnout virtuální uživatele a synchronizovat nejnovější data**. Tato možnost určuje, že site recovery by se měl pokusit vypnout chráněné virtuální počítače a synchronizovat data tak, aby nejnovější verze dat bude převzetí osobních údajů převzetí osobních údajů. Pokud tuto možnost nevyberete nebo pokus neproběhne úspěšně, převzetí služeb při selhání bude z nejnovějšího dostupného bodu obnovení pro virtuální hod. 

Site Recovery vytvoří instanci virtuálního zařízení, jejíž typ je stejný nebo podobný virtuálnímu virtuálnímu ms s podporou úložiště Premium. Výkon a cenu různých instancí virtuálních počítačů můžete zkontrolovat tak, že přejdete na [Ceny virtuálních počítačů Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo Linux Virtual [Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Kroky po migraci

1. **Nakonfigurujte replikované virtuální počítače na sadu dostupnosti, pokud je to možné**. Site Recovery nepodporuje migraci virtuálních aplikací spolu s sadou dostupnosti. V závislosti na nasazení replikovaného virtuálního počítače proveďte jednu z následujících akcí:
   * Pro virtuální počítač vytvořený pomocí klasického modelu nasazení: Přidejte virtuální počítač do sady dostupnosti na webu Azure Portal. Podrobné kroky najdete v části [Přidání existujícího virtuálního počítače do skupiny dostupnosti](../linux/classic/configure-availability-classic.md).
   * Pro virtuální počítač vytvořený pomocí modelu nasazení Správce prostředků: Uložte konfiguraci virtuálního počítače a potom odstraňte a znovu vytvořte virtuální počítače v sadě dostupnosti. Chcete-li tak učinit, použijte skript na [nastavit Azure Resource Manager Dostupnost sady .](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) Před spuštěním tohoto skriptu zkontrolujte jeho omezení a naplánujte si prostoje.

2. **Odstraňte staré virtuální počítače a disky**. Ujistěte se, že disky Premium jsou konzistentní se zdrojovými disky a že nové virtuální počítače vykonávají stejnou funkci jako zdrojové virtuální počítače. Odstraňte virtuální počítač a odstraňte disky z účtů zdrojového úložiště na webu Azure Portal. Pokud se problém, ve kterém disk není odstraněn, i když jste odstranili virtuální ho, najdete [v tématu Poradce při potížích odstranění prostředků úložiště](storage-resource-deletion-errors.md).

3. **Vyčistěte infrastrukturu Azure Site Recovery**. Pokud obnovení webu již není potřeba, můžete vyčistit jeho infrastrukturu. Odstraňte replikované položky, konfigurační server a zásady obnovení a odstraňte trezor obnovení webu Azure.

## <a name="troubleshooting"></a>Řešení potíží

* [Sledování a odstraňování potíží s ochranou virtuálních počítačů a fyzických serverů](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum pro obnovení webu Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Další kroky

Konkrétní scénáře migrace virtuálních počítačů najdete v následujících zdrojích informací:

* [Migrace virtuálních počítačů Azure mezi účty úložiště](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvořte a nahrajte virtuální pevný disk Windows Serveru na Azure](upload-generalized-managed.md)
* [Migrace virtuálních počítačů z Amazon AWS do Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Další informace o Azure Storage a Virtuálních počítačích Azure najdete v následujících zdrojích informací:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Virtuální počítače Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)

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
