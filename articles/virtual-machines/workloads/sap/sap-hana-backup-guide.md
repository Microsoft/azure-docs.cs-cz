---
title: Průvodce zálohováním pro SAP HANA v Azure Virtual Machines | Microsoft Docs
description: Průvodce zálohováním pro SAP HANA poskytuje dvě hlavní možnosti zálohování pro SAP HANA na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 522af4bf6cc711bbfdfd30d0443ee58dad56b87e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950019"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Průvodce zálohováním pro SAP HANA v Azure Virtual Machines

## <a name="getting-started"></a>začínáme

Průvodce zálohováním pro SAP HANA běžící na virtuálních počítačích Azure bude popsat jenom Témata specifická pro Azure. Obecné položky SAP HANA související s zálohováním najdete v dokumentaci k SAP HANA. Očekáváme, že budete obeznámeni s principem strategie zálohování databáze, důvody a motivace, které mají zvukovou a platnou strategii zálohování, a mají na paměti informace, které vaše společnost má pro postup zálohování, dobu uchování záloh a postup obnovení.

SAP HANA se oficiálně podporuje u různých typů virtuálních počítačů Azure, jako je Azure M-Series. Úplný seznam SAP HANA certifikovaných virtuálních počítačů Azure a jednotek velkých instancí HANA najdete v části [vyhledání certifikovaných platforem IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure nabízí řadu jednotek, ve kterých SAP HANA spouští nevirtualizované na fyzických serverech. Tato služba se nazývá [velké instance Hana](hana-overview-architecture.md). Tato příručka se nezabývá procesy zálohování a nástroji pro velké instance HANA. Ale budou omezeny na virtuální počítače Azure. Podrobnosti o procesech zálohování a obnovení s velkými instancemi HANA najdete v článku [HLI Backup and Restore](./hana-backup-restore.md).

Tento článek se zaměřuje na tři možnosti zálohování SAP HANA na virtuálních počítačích Azure:

- Zálohování HANA prostřednictvím [služby Azure Backup Services](../../../backup/backup-overview.md) 
- Zálohování HANA do systému souborů na virtuálním počítači Azure Linux (viz [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md))
- Zálohování HANA na základě snímků úložiště pomocí funkce snímku objektu BLOB služby Azure Storage ručně nebo Azure Backup služby


SAP HANA nabízí záložní rozhraní API, které umožňuje nástrojům pro zálohování třetích stran integrovat přímo s SAP HANA. Produkty, jako je Azure Backup služba nebo [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) , používají toto proprietární rozhraní k aktivaci SAP HANA databáze nebo zálohování protokolů znovu. 


Informace o tom, jak najít software SAP v Azure, najdete v článku o [tom, jaký software SAP je podporován pro nasazení Azure](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Služba Azure Backup

Prvním zobrazeným scénářem je situace, kdy služba Azure Backup používá `backint` rozhraní SAP HANA k provedení zálohy streamování z databáze SAP HANA. Nebo můžete použít obecnější schopnost služby Azure Backup k vytvoření snímku disku konzistentního s aplikací a jeho přenesení do služby Azure Backup.

Azure Backup integruje a je certifikovaný jako řešení zálohování pro SAP HANA pomocí vlastního rozhraní SAP HANA s názvem [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Další podrobnosti o řešení, jeho schopnostech a oblastech Azure, kde jsou k dispozici, najdete v článku věnovaném [podpoře pro zálohování SAP HANA databází na virtuálních počítačích Azure v matici podpory](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Podrobnosti a principy služby Azure Backup pro HANA najdete v článku [o SAP HANA zálohování databáze ve virtuálních počítačích Azure](../../../backup/sap-hana-db-about.md). 

Druhou možností, jak službu Azure Backup využít, je vytvoření zálohy konzistentní s aplikací pomocí snímků disku Azure Premium Storage. Další úložiště Azure s certifikací HANA, jako je [Azure Ultra disk](../../disks-enable-ultra-ssd.md) a [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , nepodporují tento druh snímku prostřednictvím služby Azure Backup. Přečtěte si tyto články:

- [Plánování infrastruktury zálohování virtuálních počítačů v Azure](../../../backup/backup-azure-vms-introduction.md)
- [Zálohování virtuálních počítačů Azure s Linuxem konzistentní vzhledem k aplikacím](../../../backup/backup-azure-linux-app-consistent.md) 

Tato posloupnost činnosti:

- Azure Backup musí spustit předsnímkový skript, který aplikaci vloží, v tomto případě SAP HANA v konzistentním stavu.
- V případě, že je tento konzistentní stav potvrzen, Azure Backup spustí snímky disku.
- Po dokončení snímků Azure Backup vrátí zpět aktivitu, kterou provedla ve skriptu před vytvořením snímku.
- Po úspěšném provedení bude Azure Backup streamovat data do trezoru služby Backup.

V případě SAP HANA většina zákazníků používá Azure Akcelerátor zápisu pro svazky, které obsahují protokol SAP HANA znovu. Služba Azure Backup z snímků automaticky vyloučí tyto svazky. Toto vyloučení nepoškozuje schopnost HANA obnovit. I když by to mělo zablokování možnosti obnovení téměř všemi ostatními podporovanými systémy DBMS podporované SAP.

Nevýhodou této možnosti je skutečnost, že potřebujete vyvinout vlastní skript předběžného a následného snímku. Skript předsnímkování musí vytvořit snímek HANA a zpracovat případné případy výjimek. Zatímco skript po snímku potřebuje znovu odstranit snímek HANA. Pokud potřebujete další podrobnosti o požadované logice, začněte s [poznámkou SAP support #2039883](https://launchpad.support.sap.com/#/notes/2039883). Požadavky oddílu "SAP HANA konzistence dat při pořízení snímků úložiště" v tomto článku se plně vztahují na tento typ zálohování.

> [!NOTE]
> Zálohy na základě snímků disku pro SAP HANA v nasazeních, kde se používají víc databázových kontejnerů, vyžaduje minimální verzi HANA 2,0 SP04.
> 

Podívejte se na podrobnosti o snímcích úložiště dále v tomto dokumentu.

![Tento obrázek ukazuje dvě možnosti uložení aktuálního stavu virtuálního počítače.](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Další metody zálohování HANA
Existují tři další metody zálohování nebo cesty, které je možné zvážit:

- Zálohování na sdílenou složku systému souborů NFS, která je založená na Azure NetApp Files (ANF). ANF znovu má možnost vytvářet snímky těchto svazků, na které ukládáte zálohy. Vzhledem k propustnosti, kterou nakonec vyžadujete k zápisu záloh, se může toto řešení stát nákladným způsobem. I když se dá snadno navázat, protože HANA může zapisovat zálohy přímo do sdílené složky systému souborů NFS Azure Native
- Spouští se zálohování HANA pro disky připojené k virtuálnímu počítači SSD úrovně Standard nebo Azure Premium Storage. Jako další krok můžete zkopírovat tyto záložní soubory do úložiště objektů BLOB v Azure. Tato strategie může být zajímavá.
- Spouští se zálohování HANA pro disky připojené k virtuálnímu počítači SSD úrovně Standard nebo Azure Premium Storage. V dalším kroku se disk pravidelně snapshotted. Po prvním snímku můžete použít přírůstkové snímky ke snížení nákladů.

![Tento obrázek ukazuje možnosti pro převzetí SAP HANAho zálohování souborů uvnitř virtuálního počítače.](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Na tomto obrázku jsou uvedené možnosti pro pořizování záloh souborů SAP HANA v rámci virtuálního počítače a jejich ukládání do záložních souborů HANA jinde pomocí různých nástrojů. Všechna řešení, která nezahrnují službu zálohování třetích stran nebo službu Azure Backup, mají v běžném případě několik mezních hodnoty. Některé z nich mohou být uvedeny jako správa uchovávání informací, automatické obnovení a poskytování automatického obnovení k určitému bodu v čase Azure Backup služby nebo jiné specializované zálohovací sady a služby třetích stran. Mnohé z těchto služeb třetích stran můžou běžet v Azure. 


## <a name="sap-resources-for-hana-backup"></a>Prostředky SAP pro zálohování HANA

### <a name="sap-hana-backup-documentation"></a>Dokumentace ke službě SAP HANA Backup

- [Úvod do správy SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Naplánování zálohování HANA pomocí ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Plánování zálohování dat (řídicí panel SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Nejčastější dotazy týkající se SAP HANA Backup v tématu [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Nejčastější dotazy týkající se SAP HANA databází a snímků úložiště v tématu [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nevhodné síťové systémy souborů pro zálohování a obnovení v [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Ověření správnosti SAP HANA zálohy
Nezávisle na vaší metodě zálohování je absolutní nutnost spuštění testu na jiný systém. Tento přístup poskytuje způsob, jak zajistit, aby záloha byla správná, a interní procesy zálohování a obnovy fungují podle očekávání. I když obnovení záloh může být v místním prahovém stavu kvůli jeho požadavkům na infrastrukturu, je mnohem jednodušší provádět v cloudu dočasné poskytování nezbytných prostředků pro tento účel. Je správné, aby byly k dispozici nástroje pro HANA, které mohou kontrolovat záložní soubory při obnovení. Účelem častých procesů obnovení je ale testování procesu obnovení databáze a školení tohoto procesu s provozními pracovníky.

Mějte na paměti, že při jednoduchém obnovení a kontrole, jestli je HANA v provozu, není dostatečná. Měli byste spustit kontrolu konzistence tabulky, abyste měli jistotu, že obnovená databáze je v pořádku. SAP HANA nabízí několik druhů kontrol konzistence popsaných v tématu [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informace o kontrole konzistence tabulky najdete také na webu SAP v části [kontroly konzistence tabulky a katalogu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Specialisté a nevýhody zálohování HANA oproti snímku úložiště

SAP ne&#39;t dává přednost buď na úložiště HANA, tak i na snímek úložiště. Uvádí jejich specialisty a nevýhody, takže je možné určit, která z nich se má použít v závislosti na situaci a dostupné technologii úložiště (viz [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

V Azure si pamatujte na skutečnost, že funkce snímku Azure Blob nenabízí&#39;k zajištění konzistence systému souborů na více discích (viz [použití snímků objektů BLOB pomocí PowerShellu](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

Kromě toho je potřeba pochopit dopad fakturace při častých pracích se snímky objektů blob, jak je popsáno v tomto článku: [Vysvětlení způsobu, jakým se účtují poplatky za](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)to,&#39;t, jako by se používaly virtuální disky Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA konzistenci dat při pořízení snímků úložiště

Jak bylo uvedeno dříve, popis možností zálohování snímků Azure Backup, konzistence systému souborů a aplikace je při pořízení snímků úložiště povinný. Nejjednodušší způsob, jak se vyhnout problémům, je vypnout SAP HANA, nebo možná i celý virtuální počítač. Něco, co není proveditelné pro instanci v produkčním prostředí.

> [!NOTE]
> Zálohy na základě snímků disku pro SAP HANA v nasazeních, kde se používají víc databázových kontejnerů, vyžaduje minimální verzi HANA 2,0 SP04.
> 

Služba Azure Storage neposkytuje konzistenci systému souborů na více discích nebo svazcích, které jsou připojeny k virtuálnímu počítači během procesu snímku. To znamená, že konzistence aplikací během snímku musí být doručena aplikací, v tomto případě SAP HANA sebe sama. [Poznámka 2039883 pro SAP](https://launchpad.support.sap.com/#/notes/2039883) obsahuje důležité informace o SAP HANA zálohování podle snímků úložiště. Například u systémů souborů XFS je nutné před spuštěním snímku úložiště spustit **\_ zablokování XFS** , aby se zajistila konzistence aplikace (podrobnosti o **XFS \_ zablokování** najdete na [stránce o XFS \_ zablokování (8) – Linux Man](https://linux.die.net/man/8/xfs_freeze) ).

Za předpokladu, že existuje souborový systém XFS se čtyřmi virtuálními disky Azure, následující kroky poskytují konzistentní snímek, který představuje datovou oblast HANA:

1. Příprava pro vytvoření snímku dat HANA
1. Zablokovat systémy souborů pro všechny disky nebo svazky (například použít **\_ zablokování XFS**)
1. Vytvoření všech potřebných snímků objektů BLOB v Azure
1. Uvolnit systém souborů
1. Potvrzení snímku dat HANA (odstraní snímek)

Pokud používáte funkci Azure Backup k provádění záloh snímků konzistentních vzhledem k aplikacím, je nutné, aby se v rámci skriptu pro předsnímkování používaly zakódované nebo skriptované #1 kroky. Služba Azure Backup provede kroky #2 a #3. Kroky #4 a #5 je nutné znovu poskytnout vaším kódem ve skriptu po snímku. Pokud službu Azure Backup nepoužíváte, musíte také #2 Krokovat s kódem nebo skriptem a #3 na vlastní.
Další informace o vytváření snímků dat HANA najdete v těchto článcích:

- [Snímky dat HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Další podrobnosti k provedení kroků #1 najdete v článku [vytvoření snímku dat (NATIVNÍ SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) . 
- Podrobnosti o potvrzení/odstranění snímků dat HANA podle potřeby v kroku #5 najdete v článku [vytvoření snímku dat (NATIVNÍ SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) . 

Je důležité potvrdit snímek HANA. Vzhledem k &quot; tomu, že SAP HANA kopírování při zápisu, &quot; nemusí v tomto režimu přípravy snímku vyžadovat další místo na disku. &#39;taky není možné spustit nové zálohy, dokud se nepotvrdí SAP HANA snímku.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA strategie plánování zálohování

Článek SAP HANA [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) uvádí základní plán zálohování. Při definování strategie zálohování a obnovení a procesu SAP HANA se spoléháte na dokumentaci ke službě SAP pro HANA a vaše prostředí s dalšími systémy DBMS. Sekvence různých typů zálohování a doba uchovávání dat jsou vysoce závislé na SLA, kterou potřebujete zadat.


### <a name="sap-hana-backup-encryption"></a>Šifrování zálohování SAP HANA

SAP HANA nabízí šifrování dat a protokolů. Pokud SAP HANA data a protokol nejsou zašifrované, zálohy se ve výchozím nastavení nešifrují. SAP HANA ale nabízí samostatné šifrování zálohování, jak je popsáno v [SAP HANA šifrování záloh](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Pokud používáte starší verze SAP HANA, možná budete muset ověřit, jestli je šifrování zálohy součástí již poskytnutých funkcí.  


## <a name="next-steps"></a>Další kroky
* [SAP HANA Azure Backup na úrovni souboru](sap-hana-backup-file-level.md) popisuje možnost zálohování na základě souborů.
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
