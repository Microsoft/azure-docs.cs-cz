---
title: Migrovat virtuální počítače Windows Azure Premium Storage s Azure Site Recovery | Microsoft Docs
description: Migrace stávajících virtuálních počítačů do Azure Premium Storage pomocí Site Recovery. Premium Storage nabízí podporu vysoce výkonné, nízkou latencí disku pro I náročnými úlohy běžící na virtuálních počítačích Azure.
services: virtual-machines-windows
cloud: Azure
documentationcenter: na
author: luywang
manager: jeconnoc
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.openlocfilehash: 8a7becc1dc5a2556ace249b7a743836ebf4cc048
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrace na Storage úrovně Premium pomocí Azure Site Recovery

[Azure Premium Storage](premium-storage.md) nabízí podporu vysoce výkonné, nízkou latencí disků pro virtuální počítače (VM), které jsou spuštěny I náročnými úlohy. Tento průvodce vám pomůže přenést vaše disky virtuálních počítačů z standardní účet úložiště na účet úložiště premium pomocí [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery je služba Azure, která přispívá ke strategii pro provozní kontinuitu a zotavení po havárii orchestrací replikace místní fyzických serverů a virtuálních počítačů do cloudu (Azure) nebo do sekundárního datacentra. Pokud dojde k výpadkům ve vašem primárním umístění, můžete převzetí služeb při selhání do sekundárního umístění, aby aplikace a úlohy zůstaly dostupné. Nedodržíte zpět do primárního umístění, až se obnoví do normálního provozu. 

Site Recovery poskytuje testovací převzetí služeb při selhání na Nácvik zotavení po havárii aniž by to ovlivňovalo produkční prostředí. Když spustíte převzetí služeb při selhání s minimálními ztrátami dat (podle četnosti replikací) pro neočekávané havárie. Ve scénáři migrace na Storage úrovně Premium, můžete použít [převzetí služeb při selhání ve službě Site Recovery](../../site-recovery/site-recovery-failover.md) k migraci disků target na prémiový účet úložiště.

Doporučujeme, abyste migrace na Storage úrovně Premium pomocí Site Recovery, protože tato možnost nabízí minimální dobou výpadku. Tato možnost také zabraňuje provádění ruční kopírování disků a vytvoření nové virtuální počítače. Site Recovery bude systematičtěji zkopírujte vaše disky a vytvořte nové virtuální počítače během převzetí služeb při selhání. 

Site Recovery podporuje několik typů převzetí služeb při selhání s minimální nebo žádný výpadek. Naplánovat odstávka a odhadnout ztrátě dat, přečtěte si téma [typy převzetí služeb při selhání ve službě Site Recovery](../../site-recovery/site-recovery-failover.md). Pokud jste [Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání](../../site-recovery/vmware-walkthrough-overview.md), musí být možné se připojit k virtuálnímu počítači Azure s použitím protokolu RDP po převzetí služeb při selhání.

![Diagram obnovení po havárii][1]

## <a name="azure-site-recovery-components"></a>Součásti Azure Site Recovery

Tyto součásti Site Recovery jsou relevantní pro tento scénář migrace:

* **Konfigurační server** je virtuální počítač Azure, která koordinuje komunikaci a spravuje procesy data replikace a obnovení. Na tomto virtuálním počítači, můžete spustit soubor nastavení jedním instalace konfiguračního serveru a další komponentu, volá procesový server jako replikační brána. Přečtěte si informace o [požadavky konfigurace serveru](../../site-recovery/vmware-walkthrough-overview.md). Můžete nastavit konfigurační server pouze jednou, a můžete ji použít pro všechny migrace do stejné oblasti.

* **Procesový server** je replikační brána který: 

  1. Přijímá data replikace z zdrojové virtuální počítače.
  2. Optimalizuje dat pomocí ukládání do mezipaměti, komprese a šifrování.
  3. Odešle data do účtu úložiště. 

  Také obstará nabízenou instalaci služby mobility na zdrojový virtuální počítače a provádí automatického zjišťování zdrojové virtuální počítače. Výchozí proces server je nainstalována na konfiguračním serveru. Můžete nasadit další samostatných serverů proces škálování vašeho nasazení. Přečtěte si informace o [osvědčené postupy pro nasazení procesového serveru](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) a [nasazení serverů pro další proces](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Můžete nastavit procesový server pouze jednou, a můžete ji použít pro všechny migrace do stejné oblasti.

* **Služba mobility** je komponenta, která je nasazena v každé standardní virtuální počítač, který chcete replikovat. Se zaznamenává datové zápisy na standardní virtuální počítač a předává je na procesní server. Přečtěte si informace o [replikovat počítače požadavky](../../site-recovery/vmware-walkthrough-overview.md).

Tento obrázek znázorňuje interakci tyto komponenty:

![Interakce komponenty Site Recovery][15]

> [!NOTE]
> Site Recovery nepodporuje migraci disků prostorů úložiště.

Další součásti pro další scénáře, najdete v části [architekturu scénáře](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Toto jsou požadavky na Azure pro tento scénář migrace:

* Předplatné Azure.
* Účet Azure premium storage k ukládání replikovaných dat.
* Azure virtuální síť, ke kterému se připojí virtuální počítače, když jste vytvořili v převzetí služeb při selhání. Virtuální síť Azure musí být ve stejné oblasti jako ta, ve kterém se spouští Site Recovery.
* Účet úložiště Azure standardní ukládat protokoly replikace. To může být stejný účet úložiště pro disky virtuálních počítačů, které se migruje.

## <a name="prerequisites"></a>Požadavky

* Seznámení s komponentami scénář relevantní migrace v předchozí části.
* Naplánovat odstávka metodou učení o [převzetí služeb při selhání ve službě Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Postup instalace a migrace

Site Recovery můžete použít k migraci virtuálních počítačů Azure IaaS mezi regiony nebo v rámci stejné oblasti. Následující pokyny jsou přizpůsobené pro tento scénář migrace z článku [replikovat virtuální počítače VMware nebo fyzických serverů do Azure](../../site-recovery/vmware-walkthrough-overview.md). Postupujte podle odkazů pro podrobné kroky kromě podle pokynů v tomto článku.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: Vytvoření trezoru služeb zotavení

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Vyberte **vytvořit prostředek** > **správy** > **zálohování a obnovení lokality (OMS)**. Alternativně můžete vybrat **Procházet** > **trezoru služeb zotavení** > **přidat**. 
3. Zadejte oblast, která bude replikován virtuálních počítačů. Pro účely migrace ve stejné oblasti vyberte oblast, kde jsou zdrojové virtuální počítače a účty zdrojové úložiště. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: Volba cílů ochrany 

1. Na virtuálním počítači, kam chcete nainstalovat konfigurační server, otevřete [portál Azure](https://portal.azure.com).
2. Přejděte na **trezory služeb zotavení** > **nastavení** > **Site Recovery** > **krok 1: Příprava Infrastruktura** > **cíl ochrany**.

   ![Procházení do podokna cíle ochrany][2]

3. V části **cíl ochrany**, v prvním rozevíracím seznamu vyberte **do Azure**. Vyberte v rozevíracím seznamu druhý **není virtualizované / jiné**a potom vyberte **OK**.

   ![Podokno cíle ochrany s vyplněné polí][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: Nastavení zdrojového prostředí (konfigurační server)

1. Stáhněte si **Unified instalace nástroje Azure Site Recovery** a registrační klíč trezoru přechodem na **připravit infrastrukturu** > **připravit zdroj**  >  **Přidat Server** podokna. 
 
   Budete potřebovat registrační klíč trezoru spustit jednotnou instalaci. Klíč je platný pět dní od jeho vygenerování.

   ![Procházení do podokna přidat Server][4]

2. V **přidat Server** podokně Přidat konfigurační server.

   ![Přidání serveru podokna s konfigurační Server vybrané][5]

3. Na virtuálním počítači, který používáte jako konfigurační server spusťte Unified instalačního programu nainstalujte konfigurační server a procesový server. Můžete [provede na snímcích obrazovky](../../site-recovery/vmware-walkthrough-overview.md) k dokončení instalace. Najdete na následujících snímcích obrazovky kroky zadaný pro tento scénář migrace.

   1. V **než začnete**, vyberte **nainstalujte konfigurační server a procesový server**.

      ![Před zahájením stránku][6]

   2. V **registrace**, vyhledejte a vyberte registrační klíč, který jste stáhli z trezoru.

      ![Registrační stránce][7]

   3. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pro tento scénář migrace zvolte **ne**.

      ![Stránka Podrobnosti prostředí][8]

4. Po dokončení instalace proveďte následující kroky **Microsoft Azure Site Recovery konfigurační Server** okno:
 
   1. Použít **Správa účtů** pro automatické zjišťování můžete použít kartu k vytvoření účtu obnovení lokality. (Ve scénáři o ochraně fyzické počítače, nastavení účtu není relevantní, ale musíte mít alespoň jeden účet, aby měla jedna z následujících kroků. V tomto případě můžete pojmenovat účet a heslo jako kterákoli.) 
   2. Použití **registrace trezoru** kartě nahrát soubor s přihlašovacími údaji trezoru.

      ![Karta registrace trezoru][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: Nastavení cílového prostředí

Vyberte **připravit infrastrukturu** > **cíl**a zadejte model nasazení, který chcete použít pro virtuální počítače po převzetí služeb při selhání. Můžete zvolit **Classic** nebo **Resource Manager**, v závislosti na vašem scénáři.

![Cíl podokno][10]

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. 

> [!NOTE]
> Pokud pro replikovaná data používáte účet úložiště premium, budete muset nastavit další standardní účet úložiště k ukládání protokolů replikace.

### <a name="step-5-set-up-replication-settings"></a>Krok 5: Nastavení nastavení replikace

Pokud chcete ověřit, že je konfigurační server úspěšně přidružené k zásadě replikace, který vytvoříte, postupujte podle [nakonfigurování nastavení replikace](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Krok 6: Plánování kapacity

1. Použití [Plánovač kapacity](../../site-recovery/site-recovery-capacity-planner.md) přesně odhadnout šířku pásma sítě, úložiště a dalších požadavků na provádění vaší replikace potřebuje. 
2. Až budete hotoví, vyberte **Ano, mám to hotové** v **dokončili jste plánování kapacity?**.

   ![Pole pro potvrzení, že jste dokončili plánování kapacity][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: Nainstalujte službu mobility a povolení replikace

1. Můžete se rozhodnout [nabízená instalace](../../site-recovery/vmware-walkthrough-overview.md) pro vaše zdrojové virtuální počítače nebo na [ručně nainstalovat službu mobility](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) na zdrojové virtuální počítače. Můžete najít požadavek nabízené instalace a cestu ruční instalační program v zadané odkazu. Pokud provádíte ruční instalace, možná budete muset použít interní IP adresu se najít konfigurační server.

   ![Stránka Podrobnosti o serveru konfigurace][12]

   Virtuální počítač při selhání bude mít dva dočasné disky: jedné z primárního virtuálního počítače a dalších vytvořené při zřizování virtuálního počítače v oblasti obnovení. Vyloučit dočasným diskovým před replikaci, nainstalujte službu mobility před povolením replikace. Další informace o tom, jak vyloučit dočasným diskovým najdete v tématu [z replikace vyloučit disky](../../site-recovery/vmware-walkthrough-overview.md).

2. Replikaci povolte následujícím způsobem:
   1. Vyberte **replikujte aplikaci** > **zdroj**. Když poprvé povolíte replikaci poprvé, vyberte **+ replikovat** v trezoru povolíte replikaci pro další počítače.
   2. V kroku 1, nastavit **zdroje** jako procesový server.
   3. V kroku 2 zadejte model nasazení post-převzetí služeb při selhání, prémiový účet úložiště k migraci na standardní účet úložiště k ukládání protokolů a virtuální sítě, aby v případě selhání.
   4. V kroku 3 přidejte chráněných virtuálních počítačů pomocí IP adresy. (Můžete je vyhledat interní IP adresu.)
   5. V kroku 4 nakonfigurujte vlastnosti výběrem účty, které jste dřív nastavili na procesovém serveru.
   6. V kroku 5, vyberte zásadu replikace, který jste vytvořili dříve v "krok 5: nakonfigurování nastavení replikace."
   7. Vyberte **OK**.

   > [!NOTE]
   > Když virtuální počítač Azure je navrácena a spustit znovu, není zaručeno, že se budou získávat stejnou IP adresu. Pokud se změní IP adresu serveru, proces serveru nebo konfigurace nebo chráněné virtuální počítače Azure, replikace v tomto scénáři nemusí fungovat správně.

   ![Povolení replikace podokna se zdrojem vybrané][13]

Když navrhujete prostředí Azure Storage, doporučujeme použít účty samostatné úložiště pro každý virtuální počítač v nastavení dostupnosti. Doporučujeme vám postupovat podle osvědčených postupů ve vrstvě úložiště pro [používat více účtů úložiště pro každou skupinu dostupnosti](../linux/manage-availability.md). Distribuci disky virtuálních počítačů k několika účtům úložiště pomáhá zvýšit dostupnost úložiště a rozděluje vstupy/výstupy na infrastruktuře úložiště Azure.

Pokud jsou vaše virtuální počítače v nastavení dostupnosti, namísto replikace disků všechny virtuální počítače do jeden účet úložiště, důrazně doporučujeme migraci víc virtuálních počítačů více než jednou. Tímto způsobem, virtuálními počítači ve stejné skupině dostupnosti nesdílejí účet jednoho úložiště. Použití **povolit replikaci** podokně nastavit cílový účet úložiště pro každý virtuální počítač, po jednom.
 
Podle potřeby vašeho můžete vybrat model nasazení post-převzetí služeb při selhání. Pokud zvolíte jako post-převzetí služeb při selhání modelu nasazení Azure Resource Manager, můžete převzít virtuální počítač (Resource Manager) pro virtuální počítač (Resource Manager) nebo přebírány (klasické) virtuální počítač pro virtuální počítač (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Krok 8: Spustit testovací převzetí služeb

Pokud chcete zkontrolovat, jestli se váš replikace nedokončí, vyberte instanci Site Recovery a pak vyberte **nastavení** > **replikované položky**. Zobrazí se stav a procento replikační proces. 

Po dokončení počáteční replikace spustit převzetí služeb při selhání ověření strategie replikace. Podrobné pokyny převzetí služeb při selhání najdete v tématu [spustit testovací převzetí služeb ve službě Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Před spuštěním jakékoli převzetí služeb při selhání, ujistěte se, že virtuální počítače a strategie replikace splňují požadavky. Další informace o spouštění převzetí služeb při selhání najdete v tématu [testovací převzetí služeb při selhání do Azure ve službě Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Zobrazí se stav vaše testovací převzetí služeb při selhání v **nastavení** > **úlohy** > *YOUR_FAILOVER_PLAN_NAME*. V podokně můžete zobrazit rozpis kroky a výsledky úspěch nebo selhání. V případě selhání testu převzetí služeb v kterémkoliv kroku vyberte kroku najdete v chybové zprávě. 

### <a name="step-9-run-a-failover"></a>Krok 9: Spuštění převzetí služeb při selhání

Po testu je dokončit převzetí služeb při selhání, spusťte převzetí služeb při selhání k migraci disků do úložiště úrovně Premium a replikovat instance virtuálních počítačů. Podrobné pokyny v [spustit převzetí služeb při selhání](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Je nutné vybrat **vypněte virtuální počítače a synchronizovat nejnovější data**. Tato možnost určuje, že Site Recovery opakovat vypněte chráněných virtuálních počítačů a synchronizace dat tak, aby nejnovější verzi dat převezme služby při selhání. Pokud tuto možnost nevyberete nebo pokus k neúspěchu, převzetí služeb při selhání bude z posledního bodu obnovení k dispozici pro virtuální počítač. 

Site Recovery se vytvoří instance virtuálního počítače, jejichž typ je stejný jako nebo podobné k virtuálnímu počítači podporující úložiště Premium. Můžete zkontrolovat výkon a cenu instancí virtuálních počítačů v různých přechodem na [ceny virtuálních počítačů Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo [ceny virtuálních počítačů Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Kroky po migraci

1. **Nakonfigurovat replikované virtuální počítače pro skupinu dostupnosti případně**. Site Recovery nepodporuje migraci virtuálních počítačů spolu s skupiny dostupnosti. V závislosti na nasazení replikované virtuální počítač proveďte jednu z následujících akcí:
   * Pro virtuální počítač vytvořený pomocí modelu nasazení classic: Přidání virtuálního počítače pro skupinu dostupnosti na portálu Azure. Podrobné kroky, přejděte na [přidat existující virtuální počítač do skupiny dostupnosti](../linux/classic/configure-availability-classic.md).
   * Pro virtuální počítač vytvořený pomocí modelu nasazení Resource Manager: Uložit konfiguraci virtuálního počítače a pak odstraňte a znovu vytvořit virtuální počítače v sadě dostupnosti. Uděláte to tak, použijte skript v [nastavit Azure Resource Manager virtuálních počítačů sady dostupnosti](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Před spuštěním tohoto skriptu zkontrolujte jeho omezení a naplánovat odstávka.

2. **Odstranit staré virtuální počítače a disky**. Ujistěte se, že prémiové disky jsou konzistentní s disky, zdroje a že nové virtuální počítače provádí stejnou funkci jako zdrojové virtuální počítače. Odstraňte virtuální počítač a odstraňte disky ze zdrojového účtů úložiště na portálu Azure. Pokud dojde k problému, který disk není odstraněn, i když jste odstranili virtuálního počítače naleznete v tématu [řešení chyb odstranění prostředků úložiště](storage-resource-deletion-errors.md).

3. **Vyčištění infrastruktury Azure Site Recovery**. Pokud Site Recovery je již nepotřebujete, můžete smazat svoji infrastrukturu. Odstranit replikované položky, že konfigurační server a obnovení zásad a pak odstraňte trezor Azure Site Recovery.

## <a name="troubleshooting"></a>Řešení potíží

* [Monitorování a řešení ochrany pro virtuální počítače a fyzické servery](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Další postup

U konkrétních scénářů pro migraci virtuálních počítačů najdete v následujících materiálech:

* [Migrovat virtuální počítače, které jsou mezi účty úložiště Azure](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Vytvoření a nahrání virtuálního pevného disku serveru Windows Azure](upload-generalized-managed.md)
* [Migrace virtuálních počítačů z Amazon AWS k Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zkontrolujte také, další informace o Azure Storage a virtuální počítače Azure v následujících zdrojích:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* 
  [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](premium-storage.md)

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
