---
title: Migrace virtuálních počítačů se systémem Linux do Azure Premium Storage s využitím Azure Site Recovery
description: Migrujte stávající virtuální počítače do Azure Premium Storage pomocí Site Recovery. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy náročné na vstupně-výstupní operace běžící na Azure Virtual Machines.
author: luywang
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 5f8f72ec296be3127a2f8c1ddc87d883d7e2deed
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504571"
---
# <a name="use-site-recovery-to-migrate-to-premium-storage"></a>Migrace na Premium Storage pomocí Site Recovery

[Azure Premium SSD](../disks-types.md) poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých běží úlohy náročné na vstupně-výstupní operace. Tato příručka vám pomůže migrovat disky virtuálních počítačů z účtu úložiště úrovně Standard na účet Premium Storage pomocí [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery je služba Azure, která přispívá k strategii pro provozní kontinuitu a zotavení po havárii tím, že orchestruje replikaci místních fyzických serverů a virtuálních počítačů do cloudu (Azure) nebo do sekundárního datacentra. Pokud dojde k výpadkům v primárním umístění, převezmete služby při selhání do sekundárního umístění, aby aplikace a úlohy zůstaly dostupné. Vrátíte se zpátky do svého primárního umístění, až se vrátí k normální operaci. 

Site Recovery poskytuje testovací převzetí služeb při selhání pro podporu zotavení po havárii, aniž by to ovlivnilo produkční prostředí. Můžete spustit převzetí služeb při selhání s minimálními ztrátami dat (v závislosti na četnosti replikace) pro neočekávané katastrofy. Ve scénáři migrace na Premium Storage můžete pomocí [převzetí služeb při selhání v Site Recovery](../../site-recovery/site-recovery-failover.md) migrovat cílové disky na účet Premium Storage.

Doporučujeme migrovat na Premium Storage pomocí Site Recovery, protože tato možnost poskytuje minimální prostoje. Tato možnost také zabraňuje ručnímu spuštění kopírování disků a vytváření nových virtuálních počítačů. Site Recovery bude systematicky kopírovat vaše disky a vytvářet nové virtuální počítače během převzetí služeb při selhání. 

Site Recovery podporuje různé typy převzetí služeb při selhání s minimálními nebo žádnými výpadky. Informace o tom, jak naplánovat výpadky a ztráty dat, najdete v tématu [typy převzetí služeb při selhání v Site Recovery](../../site-recovery/site-recovery-failover.md). Pokud [připravujete připojení k virtuálním počítačům Azure po převzetí služeb při selhání](../../site-recovery/vmware-azure-tutorial.md), měli byste se po převzetí služeb při selhání připojit k virtuálnímu počítači Azure pomocí protokolu RDP.

![Diagram zotavení po havárii][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery komponenty

Tyto součásti Site Recovery jsou relevantní pro tento scénář migrace:

* **Konfigurační server** je virtuální počítač Azure, který koordinuje komunikaci a spravuje procesy replikace a obnovení dat. Na tomto virtuálním počítači spustíte jeden soubor instalace a nainstalujete konfigurační server a další komponentu, jako je třeba procesový Server, jako bránu replikace. Přečtěte si o [požadavcích konfiguračního serveru](../../site-recovery/vmware-azure-tutorial.md). Konfigurační server se nastavuje jenom jednou a můžete ho použít pro všechny migrace do stejné oblasti.

* **Procesový Server** je bránou replikace, která: 

  1. Přijímá data replikace ze zdrojových virtuálních počítačů.
  2. Optimalizuje data ukládáním do mezipaměti, kompresí a šifrováním.
  3. Odešle data do účtu úložiště. 

  Také zpracovává nabízenou instalaci služby mobility na zdrojové virtuální počítače a provádí automatické zjišťování zdrojových virtuálních počítačů. Výchozí procesový Server je nainstalovaný na konfiguračním serveru. Můžete nasadit další samostatné procesní servery pro škálování vašeho nasazení. Přečtěte si o [osvědčených postupech pro nasazení procesového serveru a o](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) [nasazení dalších procesových serverů](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Procesový Server se nastavuje jenom jednou a můžete ho použít pro všechny migrace do stejné oblasti.

* **Služba mobility** je komponenta, která je nasazená na všech standardních virtuálních počítačích, které chcete replikovat. Zachycuje zápisy dat na standardním virtuálním počítači a předá je na procesový Server. Přečtěte si o [požadavcích na replikovaný počítač](../../site-recovery/vmware-azure-tutorial.md).

Tento obrázek ukazuje, jak tyto komponenty pracují:

![Interakce Site Recoverych komponent][15]

> [!NOTE]
> Site Recovery nepodporuje migraci disků prostorů úložiště.

Další součásti pro jiné scénáře najdete v tématu [Architektura scénáře](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Základy Azure

Toto jsou požadavky Azure pro tento scénář migrace:

* Předplatné Azure.
* Účet služby Azure Premium Storage pro ukládání replikovaných dat.
* Virtuální síť Azure, ke které se virtuální počítače připojí při jejich vytvoření při převzetí služeb při selhání. Virtuální síť Azure musí být ve stejné oblasti jako ta, ve které Site Recovery běží.
* Účet úložiště Azure standard pro ukládání protokolů replikace. Může to být stejný účet úložiště pro disky virtuálních počítačů, které se migrují.

## <a name="prerequisites"></a>Požadavky

* Pochopení relevantních součástí scénáře migrace v předchozí části.
* Naplánujte prostoje o [převzetí služeb při selhání v Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Kroky instalace a migrace

K migraci virtuálních počítačů Azure IaaS mezi oblastmi nebo ve stejné oblasti můžete použít Site Recovery. Následující pokyny jsou přizpůsobené pro tento scénář migrace z článku [replikace virtuálních počítačů VMware nebo fyzických serverů do Azure](../../site-recovery/vmware-azure-tutorial.md). Podrobnější postup najdete v odkazech v tomto článku.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: vytvoření trezoru Recovery Services

1. Otevřete [Azure Portal](https://portal.azure.com).
2. Vyberte **vytvořit**  >  **správce** prostředků  >  **zálohování** a **Site Recovery (OMS)**. Případně můžete vybrat **Procházet**  >  **Recovery Services trezor**  >  **Přidat**. 
3. Zadejte oblast, do které se budou virtuální počítače replikovat. Pro účely migrace ve stejné oblasti vyberte oblast, ve které jsou zdrojové virtuální počítače a účty zdrojového úložiště. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: výběr cílů ochrany 

1. Na virtuálním počítači, kam chcete nainstalovat konfigurační server, otevřete [Azure Portal](https://portal.azure.com).
2. V nastavení **Recovery Services trezory**  >    >  **Site Recovery**  >  **Krok 1: Příprava** na  >  **cíl ochrany** infrastruktury.

   ![Procházení k podoknu cíle ochrany][2]

3. V části **cíl ochrany** vyberte v prvním rozevíracím seznamu možnost **Azure**. V druhém rozevíracím seznamu vyberte **nevirtualizované/jiné** a pak vyberte **OK**.

   ![Podokno cíle ochrany s poli s výplní][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: nastavení zdrojového prostředí (konfigurační server)

1. Stáhněte si **Azure Site Recovery Unified Setup** a registrační klíč trezoru, a to tak, že se vrátíte do podoken **připravit infrastrukturu**  >  **připravit zdroj**  >  **Přidat server** . 
 
   Abyste mohli spustit sjednocenou instalaci, budete potřebovat registrační klíč trezoru. Klíč je platný pět dní od jeho vygenerování.

   ![Procházení k podoknu přidat server][4]

2. V podokně **Přidat server** Přidejte konfigurační server.

   ![Přidat podokno serveru s vybraným konfiguračním serverem][5]

3. Na virtuálním počítači, který používáte jako konfigurační server, spusťte sjednocené nastavení a nainstalujte konfigurační server a procesový Server. K dokončení instalace můžete projít [snímky obrazovky](../../site-recovery/vmware-azure-tutorial.md) . Kroky zadané pro tento scénář migrace můžete vyhledat na následujících snímcích obrazovky.

   1. V části **než začnete** vyberte **nainstalovat konfigurační server a procesový Server**.

      ![Než začnete stránku][6]

   2. V části **registrace** vyhledejte a vyberte registrační klíč, který jste si stáhli z trezoru.

      ![Registrační stránka][7]

   3. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. V případě tohoto scénáře migrace vyberte možnost **ne**.

      ![Stránka s podrobnostmi prostředí][8]

4. Po dokončení instalace proveďte v okně **Microsoft Azure Site Recovery konfiguračního serveru** následující kroky:
 
   1. Karta **Spravovat účty** slouží k vytvoření účtu, který Site Recovery může použít pro automatické zjišťování. (Ve scénáři ochrany fyzických počítačů není nastavení účtu relevantní, ale potřebujete aspoň jeden účet, abyste mohli povolit jeden z následujících kroků. V takovém případě můžete účet a heslo pojmenovat jako všechny.) 
   2. Kartu **registrace trezoru** použijte k nahrání souboru s přihlašovacími údaji trezoru.

      ![Karta registrace trezoru][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: nastavení cílového prostředí

Vyberte **připravit**  >  **cíl** infrastruktury a zadejte model nasazení, který chcete použít pro virtuální počítače po převzetí služeb při selhání. V závislosti na vašem scénáři můžete zvolit možnost **Classic** nebo **Správce prostředků**.

![Cílové podokno][10]

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. 

> [!NOTE]
> Pokud pro replikovaná data používáte účet Premium Storage, budete muset nastavit další účet úložiště úrovně Standard pro ukládání protokolů replikace.

### <a name="step-5-set-up-replication-settings"></a>Krok 5: nastavení replikace

Pokud chcete ověřit, jestli je konfigurační server úspěšně přidružený k zásadě replikace, kterou vytvoříte, postupujte podle pokynů nastavení [replikace](../../site-recovery/vmware-azure-tutorial.md).

### <a name="step-6-plan-capacity"></a>Krok 6: plánování kapacity

1. Použijte [Plánovač kapacity](../../site-recovery/site-recovery-capacity-planner.md) k přesnému odhadu šířky pásma sítě, úložiště a dalších požadavků pro splnění požadavků na replikaci. 
2. Až to budete mít, vyberte **Ano, mám jste hotové** **plánování kapacity?**.

   ![Box pro potvrzení, že jste dokončili plánování kapacity][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: instalace služby mobility a povolení replikace

1. Můžete zvolit [nabízenou instalaci](../../site-recovery/vmware-azure-tutorial.md) do zdrojových virtuálních počítačů nebo [ručně nainstalovat službu mobility](../../site-recovery/vmware-azure-install-mobility-service.md) na zdrojové virtuální počítače. Požadavek na vložení instalace a cestu k ručnímu instalačnímu programu najdete v zadaném odkazu. Pokud provádíte ruční instalaci, možná budete muset k vyhledání konfiguračního serveru použít interní IP adresu.

   ![Stránka s podrobnostmi konfiguračního serveru][12]

   Virtuální počítač s podporou převzetí služeb při selhání bude mít dva dočasné disky: jednu z primárního virtuálního počítače a druhou vytvořenou během zřizování virtuálního počítače v oblasti obnovení. Pokud chcete dočasný disk před replikací vyloučit, nainstalujte před povolením replikace službu mobility. Další informace o tom, jak vyloučit dočasný disk, najdete v tématu [vyloučení disků z replikace](../../site-recovery/vmware-azure-tutorial.md).

2. Replikaci povolte následujícím způsobem:
   1. Vyberte **replikovat**  >  **zdroj** aplikace. Po prvním povolení replikace výběrem **+ replikovat** v trezoru Povolte replikaci pro další počítače.
   2. V kroku 1 nastavte **zdroj** jako procesový Server.
   3. V kroku 2 určete model nasazení po převzetí služeb při selhání, účet Premium Storage, který se má migrovat na, standardní účet úložiště pro ukládání protokolů a virtuální síť, která se nezdařila.
   4. V kroku 3 přidejte chráněné virtuální počítače podle IP adresy. (K jejich vyhledání možná budete potřebovat interní IP adresu.)
   5. V kroku 4 nakonfigurujte vlastnosti tak, že vyberete účty, které jste na procesovém serveru nastavili dříve.
   6. V kroku 5 vyberte zásadu replikace, kterou jste vytvořili dříve v části "krok 5: nastavení replikace."
   7. Vyberte **OK**.

   > [!NOTE]
   > Pokud je virtuální počítač Azure uvolněný a znovu spuštěný, není nijak zaručeno, že bude mít stejnou IP adresu. Pokud se změní IP adresa konfiguračního serveru/procesového serveru nebo chráněných virtuálních počítačů Azure, nemusí replikace v tomto scénáři fungovat správně.

   ![Povolit podokno replikace se zvoleným zdrojem][13]

Při návrhu Azure Storageho prostředí doporučujeme pro každý virtuální počítač ve skupině dostupnosti použít samostatné účty úložiště. Doporučujeme, abyste podle osvědčeného postupu ve vrstvě úložiště [používali pro každou skupinu dostupnosti více účtů úložiště](../availability.md). Distribuce disků virtuálních počítačů do několika účtů úložiště pomáhá zlepšit dostupnost úložiště a distribuuje vstupně-výstupní operace v infrastruktuře úložiště Azure.

Pokud jsou vaše virtuální počítače ve skupině dostupnosti, místo replikace disků všech virtuálních počítačů do jednoho účtu úložiště důrazně doporučujeme migrovat několik virtuálních počítačů vícekrát. Virtuální počítače ve stejné skupině dostupnosti tak nebudou sdílet jeden účet úložiště. Pomocí podokna **Povolit replikaci** můžete nastavit cílový účet úložiště pro každý virtuální počítač v jednom okamžiku.
 
Podle potřeby můžete zvolit model nasazení po převzetí služeb při selhání. Pokud zvolíte Azure Resource Manager jako model nasazení po převzetí služeb při selhání, můžete převzít služby virtuálního počítače (Správce prostředků) na virtuální počítač (Správce prostředků), nebo můžete převzít služby při selhání virtuálního počítače (Classic) na virtuální počítač (Správce prostředků).

### <a name="step-8-run-a-test-failover"></a>Krok 8: spuštění testovacího převzetí služeb při selhání

Pokud chcete zjistit, jestli je vaše replikace dokončená, vyberte instanci Site Recovery a pak vyberte **Nastavení**  >  **replikované položky**. Zobrazí se stav a procento procesu replikace. 

Po dokončení počáteční replikace spusťte testovací převzetí služeb při selhání, abyste ověřili strategii replikace. Podrobné kroky testovacího převzetí služeb při selhání najdete v tématu [spuštění testovacího převzetí služeb při selhání v Site Recovery](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> Před spuštěním převzetí služeb při selhání se ujistěte, že vaše virtuální počítače a strategie replikace splňují požadavky. Další informace o spuštění testovacího převzetí služeb při selhání najdete v tématu [Test převzetí služeb při selhání do Azure v Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Stav testovacího převzetí služeb při selhání můžete zobrazit v **Nastavení**  >  **úlohy**  >  *YOUR_FAILOVER_PLAN_NAME*. V podokně uvidíte rozpis kroků a výsledků úspěch/selhání. Pokud se testovací převzetí služeb při selhání v jakémkoli kroku nepovede, vyberte krok a zkontrolujte chybovou zprávu. 

### <a name="step-9-run-a-failover"></a>Krok 9: spuštění převzetí služeb při selhání

Po dokončení testovacího převzetí služeb při selhání spusťte převzetí služeb při selhání pro migraci disků Premium Storage a replikaci instancí virtuálních počítačů. Postupujte podle podrobných kroků v části [spuštění převzetí služeb při selhání](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Nezapomeňte vybrat **vypnout virtuální počítače a synchronizovat nejnovější data**. Tato možnost určuje, že Site Recovery by se měla pokusit vypnout chráněné virtuální počítače a synchronizovat data, aby se při selhání převzala nejnovější verze dat. Pokud tuto možnost nevyberete nebo pokud tento pokus neproběhne úspěšně, převzetí služeb při selhání bude z posledního dostupného bodu obnovení pro virtuální počítač. 

Site Recovery vytvoří instanci virtuálního počítače, jejíž typ je stejný jako nebo podobný virtuálnímu počítači podporujícímu Premium Storage. Můžete kontrolovat výkon a cenu různých instancí virtuálních počítačů, a to tak, že kliknete na ceny [Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo ceny [Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Postup po migraci

1. **Pokud je to možné, nakonfigurujte replikované virtuální počítače na skupinu dostupnosti**. Site Recovery nepodporuje migraci virtuálních počítačů společně se sadou dostupnosti. V závislosti na nasazení replikovaného virtuálního počítače proveďte jednu z následujících akcí:
   * Pro virtuální počítač vytvořený prostřednictvím modelu nasazení Classic: přidejte virtuální počítač do skupiny dostupnosti v Azure Portal. Podrobný postup najdete v popisu [Přidání existujícího virtuálního počítače do skupiny dostupnosti](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * Pro virtuální počítač vytvořený pomocí modelu nasazení Správce prostředků: uložte konfiguraci virtuálního počítače a pak odstraňte a znovu vytvořte virtuální počítače ve skupině dostupnosti. Provedete to tak, že použijete skript v [nastavení Azure Resource Manager skupinu dostupnosti virtuálního počítače](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Před spuštěním tohoto skriptu si Projděte jeho omezení a naplánujte výpadky.

2. **Odstraňte staré virtuální počítače a disky**. Zajistěte, aby byly prémiové disky konzistentní se zdrojovými disky a aby nové virtuální počítače prováděly stejnou funkci jako u zdrojových virtuálních počítačů. Odstraňte virtuální počítač a odstraňte disky ze zdrojového účtu úložiště v Azure Portal. Pokud dojde k potížím, kdy se disk neodstranil, i když jste virtuální počítač odstranili, přečtěte si téma [řešení chyb při odstraňování prostředků úložiště](../troubleshooting/storage-resource-deletion-errors.md).

3. **Vyčistěte infrastrukturu Azure Site Recovery**. Pokud už Site Recovery nepotřebujete, můžete svou infrastrukturu vyčistit. Odstraňte replikované položky, konfigurační server a zásady obnovení a pak odstraňte trezor Azure Site Recovery.

## <a name="troubleshooting"></a>Řešení potíží

* [Monitorování a odstraňování potíží s ochranou pro virtuální počítače a fyzické servery](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Microsoft Q&stránku s otázkou pro Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Další kroky

Konkrétní scénáře migrace virtuálních počítačů najdete v následujících zdrojích informací:

* [Migrace Azure Virtual Machines mezi účty úložiště](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Nahrání virtuálního pevného disku se systémem Linux](upload-vhd.md)
* [Migrace Virtual Machines z Amazon AWS do Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Další informace o Azure Storage a službě Azure Virtual Machines najdete v následujících zdrojích informací:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Výběr typu disku pro virtuální počítače IaaS](../disks-types.md)

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