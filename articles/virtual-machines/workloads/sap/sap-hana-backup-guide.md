---
title: Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Průvodce zálohováním pro SAP HANA poskytuje dvě hlavní možnosti zálohování pro SAP HANA na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255231"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure

## <a name="getting-started"></a>Začínáme

Průvodce zálohováním pro SAP HANA spuštěný na virtuálních počítačích Azure bude popisovat jenom témata specifická pro Azure. Obecné položky související se zálohováním SAP HANA naleznete v dokumentaci SAP HANA. Očekáváme, že budete seznámeni s principem zálohování databáze strategie, důvody a motivace mít zvuk a platné strategie zálohování, a jsou si vědomi požadavků vaší společnosti má pro zálohování postup, doba uchovávání záloh a obnovení Postup.

SAP HANA je oficiálně podporovaná na různých typech virtuálních počítačů Azure, jako je Azure M-Series. Úplný seznam virtuálních počítačů Azure s certifikací SAP HANA a jednotek velké instance HANA najdete v části [Najít certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure nabízí řadu jednotek, kde SAP HANA běží nevirtualizované na fyzických serverech. Tato služba se nazývá [HANA Velké instance](hana-overview-architecture.md). Tato příručka nebude zahrnovat procesy zálohování a nástroje pro velké instance HANA. Ale bude omezena na virtuální počítače Azure. Podrobnosti o procesech zálohování a obnovení pomocí velkých instancí HANA naleznete v článku [Zálohování a obnovení hli](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).

Tento článek se zaměřuje na tři možnosti zálohování pro SAP HANA na virtuálních počítačích Azure:

- Zálohování HANA prostřednictvím [služby Azure Backup Services](https://docs.microsoft.com/azure/backup/backup-overview) 
- Záloha HANA do systému souborů ve virtuálním počítači Azure Linux (viz [ZÁLOHOVÁNÍ SAP HANA Azure na úrovni souborů)](sap-hana-backup-file-level.md)
- Záloha HANA založená na snapshotech úložiště pomocí funkce snímek objektu blob úložiště Azure ručně nebo služby Azure Backup


SAP HANA nabízí rozhraní API pro zálohování, které umožňuje zálohování nástrojů třetích stran pro integraci přímo s SAP HANA. Produkty, jako je služba Azure Backup nebo [Commvault,](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) používají toto proprietární rozhraní k aktivaci databáze SAP HANA nebo k opakování záloh protokolu. 


Informace o tom, jak můžete najít, jaký software SAP je v Azure podporován, najdete v článku [Jaký software SAP je podporován pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure).

## <a name="azure-backup-service"></a>Služba Zálohování Azure

První zobrazený scénář je scénář, kde služba Azure `backint` Backup Service používá buď rozhraní SAP HANA k provedení zálohy streamování z databáze SAP HANA. Nebo použijete obecnější funkci služby Azure Backup k vytvoření snímku disku konzistentního s aplikací a přenesete ho do služby Azure Backup.

Azure Backup integruje a je certifikován jako řešení zálohování pro SAP HANA pomocí proprietární sap hana rozhraní s názvem [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Další podrobnosti o řešení, jeho možnostech a oblastech Azure, kde je k dispozici, načtěte článek [Matice podpory pro zálohování databází SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). Podrobnosti a zásady týkající se služby Azure Backup pro HANA našlápne článek [Zálohování databáze SAP HANA ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-about). 

Druhou možností využití služby Azure Backup je vytvoření zálohy konzistentní s aplikací pomocí snímků disku služby Azure Premium Storage. Ostatní úložiště Azure certifikovaná HANA, jako je [disk Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) a soubory Azure [NetApp,](https://azure.microsoft.com/services/netapp/) tento druh snímku nepodporují prostřednictvím služby Azure Backup. Čtení těchto článků:

- [Plánování infrastruktury zálohování virtuálních počítačů v Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Zálohování virtuálních počítačů Azure Linux konzistentní s aplikacemi](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

tato sekvence činnosti se objeví:

- Azure Backup musí spustit skript pre-snímek, který umístí aplikaci, v tomto případě SAP HANA, do konzistentního stavu
- Jak je tento konzistentní stav potvrzen, Azure Backup spustí snímky disku
- Po dokončení snímků Azure Backup vrátí aktivitu, kterou provedla ve skriptu pre-snapshot
- Po úspěšném spuštění azure backup bude streamovat data do trezoru zálohování

V případě SAP HANA většina zákazníků používá Azure Write Accelerator pro svazky, které obsahují protokol opakování SAP HANA. Služba Azure Backup tyto svazky automaticky vyloučí ze snímků. Toto vyloučení nepoškozuje schopnost HANA obnovit. Ačkoli by to zablokovalo možnost obnovení s téměř všemi ostatními podporovanými DBMS podporovanými SAP.

Nevýhodou této možnosti je skutečnost, že je třeba vytvořit svůj vlastní pre- a post-snímek skript. Skript pre-snapshot musí vytvořit snímek HANA a zpracování případných případů výjimky. Vzhledem k tomu, že skript post-snímek musí znovu odstranit snímek HANA. Další podrobnosti o požadované logice, začněte s [poznámkou podpory SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). Důležité informace o části "Konzistence dat SAP HANA při pořizování snímků úložiště" v tomto článku plně platí pro tento druh zálohování.

> [!NOTE]
> Zálohy založené na snímekdisku disku pro SAP HANA v nasazeních, kde se používá více databázových kontejnerů, vyžadují minimální vydání HANA 2.0 SP04
> 

Podrobnosti o snímcích úložiště dále v tomto dokumentu.

![Na tomto obrázku jsou uvedeny dvě možnosti uložení aktuálního stavu virtuálního mísy.](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Jiné metody zálohování HANA
Existují tři další metody zálohování nebo cesty, které lze považovat:

- Zálohování proti sdílené sfs, která je založena na soubory Azure NetApp (ANF). ANF má opět možnost vytvářet snímky těchto svazků, na kterých ukládáte zálohy. Vzhledem k propustnosti, kterou nakonec budete potřebovat k zápisu záloh, může se toto řešení stát nákladnou metodou. Ačkoli snadno vytvořit, protože HANA můžete zapisovat zálohy přímo do sdílené složky Azure nativní nfs
- Spuštění zálohy HANA proti připojeným diskům virtuálního počítače standardního ssd disku nebo úložiště Azure Premium. Jako další krok můžete zkopírovat tyto záložní soubory proti úložišti objektů Blob Azure. Tato strategie by mohla být cenová atraktivita
- Spuštění zálohy HANA proti připojeným diskům virtuálního počítače standardního ssd disku nebo úložiště Azure Premium. Jako další krok disk dostane snímek v pravidelných intervalech. Po prvním snímku lze přírůstkové snímky použít ke snížení nákladů

![Tento obrázek znázorňuje možnosti pro zálohování souborů SAP HANA uvnitř virtuálního počítače](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Tento obrázek znázorňuje možnosti pro zálohování souborů SAP HANA uvnitř virtuálního počítače a jeho uložení záložních souborů HANA někde jinde pomocí různých nástrojů. Všechna řešení, která nezahrnují službu zálohování třetí strany nebo službu Azure Backup, však mají několik překážek společného. Některé z nich mohou být uvedeny, jako je správa uchovávání informací, proces automatického obnovení a poskytování automatického obnovení v čase, jak poskytuje služba Azure Backup nebo jiné specializované sady a služby zálohování třetích stran. Mnoho z těchto služeb třetích stran, které jsou možné spustit v Azure. 


## <a name="sap-resources-for-hana-backup"></a>Prostředky SAP pro zálohování HANA

### <a name="sap-hana-backup-documentation"></a>Dokumentace zálohování SAP HANA

- [Úvod do sap hana správy](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Naplánovat zálohování HANA pomocí ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Naplánovat zálohování dat (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Nejčastější dotazy týkající se zálohování SAP HANA v [poznámce SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Nejčastější dotazy týkající se databáze A snímků úložiště SAP HANA v [poznámce SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nevhodné síťové souborové systémy pro zálohování a obnovu v [poznámce SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Jak ověřit správnost zálohy SAP HANA
Nezávisle na vaší metodě zálohování je spuštění testovacího obnovení proti jinému systému naprostou nutností. Tento přístup poskytuje způsob, jak zajistit, že záloha je správná a interní procesy pro zálohování a obnovení práce podle očekávání. Zatímco obnovení záloh může být překážkou místní kvůli jeho požadavku na infrastrukturu, je mnohem jednodušší dosáhnout v cloudu tím, že dočasně poskytnepotřebné prostředky pro tento účel. Je správné, že existují nástroje dodávané s HANA, které mohou kontrolovat záložní soubory na schopnost obnovit. Účelem cvičení častého obnovení je však otestovat proces obnovení databáze a trénovat tento proces s provozním personálem.

Mějte na paměti, že provedení jednoduchéobnovení a kontrolu, pokud HANA je v provozu není dostačující. Měli byste spustit kontrolu konzistence tabulky, abyste se ujistili, že obnovená databáze je v pořádku. SAP HANA nabízí několik druhů kontrol konzistence popsaných v [poznámce SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informace o kontrole konzistence tabulky lze také nalézt na webu SAP na [webu Tabulka a Kontrola konzistence katalogu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Výhody a nevýhody zálohy HANA versus snímek úložiště

SAP&#39;neupřednostňuje buď hana zálohování versus snímek úložiště. Uvádí jejich výhody a nevýhody, takže je možné určit, které použít v závislosti na situaci a dostupné technologii úložiště (viz [Plánování strategie zálohování a obnovení).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

V Azure si uvědomte, že funkce snímek objektu blob Azure neposkytuje&#39;konzistenci systému souborů na více discích (viz [Použití snímků objektů blob s Prostředím PowerShell).](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) 

Kromě toho je třeba pochopit důsledky fakturace při časté práci s snímky objektů blob, jak je popsáno v tomto článku: [Principy jak snímky časově rozlišují poplatky](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– není&#39;není tak zřejmé, jako pomocí virtuálních disků Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Konzistence dat SAP HANA při pořizování snímků úložiště

Jak bylo zdokumentováno dříve, popis možností zálohování snímků azure zálohování, konzistence systému souborů a aplikací je povinné při pořizování snímků úložiště. Nejjednodušší způsob, jak se vyhnout problémům by bylo vypnout SAP HANA, nebo možná i celý virtuální počítač. Něco, co není možné pro produkční instance.

> [!NOTE]
> Zálohy založené na snímekdisku disku pro SAP HANA v nasazeních, kde se používá více databázových kontejnerů, vyžadují minimální vydání HANA 2.0 SP04
> 

Úložiště Azure, neposkytuje konzistenci systému souborů mezi více disků nebo svazků, které jsou připojené k virtuálnímu počítači během procesu snímek. To znamená, že konzistence aplikace během snímku musí být doručena aplikací, v tomto případě SAP HANA sám. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) má důležité informace o zálohách SAP HANA podle snímků úložiště. Například u souborových systémů XFS je nutné před spuštěním snímku úložiště spustit **xfs\_freeze,** aby byla zajištěna konzistence aplikace (viz [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) pro podrobnosti o zmrazení **xfs\_**).

Za předpokladu, že existuje systém souborů XFS zahrnující čtyři virtuální disky Azure, následující kroky poskytují konzistentní snímek, který představuje datovou oblast HANA:

1. Vytvořit snímek dat HANA připravit
1. Ukotvit souborové systémy všech disků/svazků (například použít **xfs\_freeze)**
1. Vytvoření všech nezbytných snímků objektů blob v Azure
1. Uvolnění systému souborů
1. Potvrďte snímek dat HANA (odstraní snímek)

Při použití Azure Backup schopnost provádět zálohování konzistentní snímek aplikace, kroky, #1 je třeba kódovat nebo skriptovat v pro skript pre-snímek. Služba Azure Backup provede kroky #2 a #3. Kroky #4 a #5 musí být znovu poskytnuty vaším kódem ve skriptu po snímku. Pokud nepoužíváte službu zálohování Azure, musíte také kód/skript krok #2 a #3 na vlastní pěst.
Další informace o vytváření snímků dat HANA naleznete v těchto článcích:

- [Snímky dat HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Další podrobnosti k provedení #1 kroků naleznete v článku [Vytvoření snímku dat (nativní SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Podrobnosti pro potvrzení/ odstranění snímků dat HANA podle potřeby v kroku #5 lze nalézt v článku [Vytvoření snímku dat (nativní SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Je důležité potvrdit snímek HANA. Z důvodu &quot;kopírování při zápisu&quot; SAP HANA nemusí vyžadovat další místo na disku v tomto režimu příprava snímku. Není&#39;také možné spustit nové zálohy, dokud nebude potvrzen snímek SAP HANA.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategie plánování zálohování SAP HANA

Článek SAP HANA [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) uvádí základní plán pro zálohování. Při definování strategie zálohování a obnovení a procesu pro SAP HANA se můžete spolehnout na dokumentaci SAP kolem hana a na vaše zkušenosti s ostatními technologiemi DBMS. Posloupnost různých typů záloh a doba uchování jsou vysoce závislé na sla, které je třeba zadat.


### <a name="sap-hana-backup-encryption"></a>Šifrování zálohování SAP HANA

SAP HANA nabízí šifrování dat a protokolu. Pokud nejsou zašifrována data a protokol SAP HANA, zálohy nejsou ve výchozím nastavení šifrovány. SAP HANA však nabízí samostatné šifrování záloh, jak je popsáno v [SAP HANA Backup Encryption](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Pokud používáte starší verze SAP HANA, možná budete muset zkontrolovat, zda šifrování zálohy bylo součástí již poskytnuté funkce.  


## <a name="next-steps"></a>Další kroky
* [SAP HANA Azure Backup na úrovni souboru](sap-hana-backup-file-level.md) popisuje možnost zálohování na základě souborů.
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
