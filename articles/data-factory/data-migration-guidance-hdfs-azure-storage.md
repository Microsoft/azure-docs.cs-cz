---
title: Migrace dat z místního clusteru Hadoop do Azure Storage
description: Zjistěte, jak pomocí Azure Data Factory migrovat data z místního clusteru Hadoop do Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417131"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Použití Azure Data Factory k migraci dat z místního clusteru Hadoop do Úložiště Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat ve velkém měřítku z místního HDFS do úložiště objektů blob Azure nebo Azure Data Lake Storage Gen2. 

Data Factory nabízí dva základní přístupy pro migraci dat z místníhdfs do Azure. Můžete vybrat přístup na základě vašeho scénáře. 

- **Režim DistCp data Factory** (doporučeno): V Factory dat můžete použít [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (distribuovaná kopie) ke kopírování souborů tak, jak je, do úložiště objektů Blob Azure (včetně [fázované kopie)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)nebo Azure Data Lake Store Gen2. Pomocí data factory integrované s DistCp využít výhod existující výkonný cluster k dosažení nejlepší propustnost kopírování. Můžete také získat výhodu flexibilníplánování a jednotné monitorování zkušenosti z Data Factory. V závislosti na konfiguraci data factory aktivita kopírování automaticky vytvoří příkaz DistCp, odešle data do clusteru Hadoop a poté sleduje stav kopírování. Doporučujeme režim DistCp data Factory pro migraci dat z místního clusteru Hadoop do Azure.
- **Nativní režim runtime integrace data factory**: DistCp není možnost ve všech scénářích. Například v prostředí virtuálních sítí Azure nástroj DistCp nepodporuje privátní partnerský vztah Azure ExpressRoute s koncovým bodem virtuální sítě Azure Storage. Kromě toho v některých případech nechcete používat existující cluster Hadoop jako modul pro migraci dat, takže do clusteru nepřikládáte velká zatížení, což může ovlivnit výkon existujících úloh ETL. Místo toho můžete použít nativní funkce modulu runtime integrace data factory jako modul, který kopíruje data z místníhdfs do Azure.

Tento článek obsahuje následující informace o obou přístupech:
> [!div class="checklist"]
> * Výkon 
> * Odolnost kopírování
> * Zabezpečení sítě
> * Architektura řešení na vysoké úrovni 
> * Osvědčené postupy při provádění  

## <a name="performance"></a>Výkon

V režimu DistCp data Factory je propustnost stejná, jako když používáte nástroj DistCp nezávisle. Režim DistCp data Factory maximalizuje kapacitu stávajícího clusteru Hadoop. DistCp můžete použít pro velké kopírování mezi clustery nebo uvnitř clusteru. 

DistCp používá MapReduce k provedení jeho distribuce, zpracování chyb a obnovení a vytváření sestav. Rozbalí seznam souborů a adresářů do vstupu pro mapování úkolů. Každý úkol zkopíruje oddíl souboru, který je zadán ve zdrojovém seznamu. Pomocí data factory integrované s DistCp můžete vytvářet kanály pro plné využití šířky pásma sítě, viposlužby úložiště a šířky pásma pro maximalizaci propustnost pohybu dat pro vaše prostředí.  

Data Factory nativní integrace runtime režim také umožňuje paralelismus na různých úrovních. Paralelismu můžete použít k plnému využití šířky pásma sítě, vipos úložiště a šířky pásma k maximalizaci propustnost pohybu dat:

- Jedna aktivita kopírování může využít škálovatelné výpočetní prostředky. S runtime integrace s vlastním hostitelem můžete ručně vertikálně vertikálně navýšit kapacitu počítače nebo škálovat kapacitu na více počítačů[(až čtyři uzly).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) Jedna aktivita kopírování rozdělí svůj soubor nastavený ve všech uzlech. 
- Jedna kopie aktivity čte z a zapisuje do úložiště dat pomocí více vláken. 
- Tok řízení data factory můžete spustit více aktivit kopírování paralelně. Můžete například použít [smyčku For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Další informace naleznete v [průvodci výkonem aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odolnost

V režimu DistCp data můžete použít různé parametry příkazového řádku `-i`DistCp `-update`(například , ignorovat selhání nebo zapisovat data, pokud se velikost zdrojového souboru a cílového souboru liší) pro různé úrovně odolnosti.

V režimu runtime nativní integrace data factory, v jednom spuštění aktivity kopírování, Data Factory má vestavěný mechanismus opakování. Může zpracovat určitou úroveň přechodných chyb v úložištích dat nebo v základní síti. 

Při provádění binární kopírování z místníhdfs do úložiště objektů blob a z místníHDFS do úložiště dat Lake Store Gen2, Data Factory automaticky provádí vytváření kontrolních bodů do značné míry. Pokud se nezdaří spuštění aktivity kopírování nebo časový rozsah, při následném opakování (ujistěte se, že počet opakování je > 1), kopie pokračuje od posledního bodu selhání namísto spuštění na začátku.

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení data factory přenáší data z místníhdfs do úložiště objektů blob nebo Azure Data Lake Storage Gen2 pomocí šifrovaného připojení přes protokol HTTPS. Protokol HTTPS poskytuje šifrování dat při přenosu a zabraňuje odposlouchávání a útokům prostředníkem. 

Případně, pokud nechcete, aby se data přenášela přes veřejný internet, můžete pro vyšší zabezpečení přenášet data prostřednictvím soukromého partnerského propojení přes ExpressRoute. 

## <a name="solution-architecture"></a>Architektura řešení

Tento obrázek znázorňuje migraci dat přes veřejný internet:

![Diagram znázorňující architekturu řešení pro migraci dat přes veřejnou síť](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- V této architektuře jsou data bezpečně přenášena pomocí protokolu HTTPS přes veřejný internet. 
- Doporučujeme používat režim DistCp datové továrny v prostředí veřejné sítě. Můžete využít výkonné existující clusteru k dosažení nejlepší propustnost kopírování. Můžete také získat výhodu flexibilní plánování a jednotné monitorování zkušenosti z Data Factory.
- Pro tuto architekturu je nutné nainstalovat runtime integrace s vlastním hostitelem data factory na počítači se systémem Windows za podnikovou bránou firewall, abyste odeslali příkaz DistCp do clusteru Hadoop a sledovali stav kopírování. Vzhledem k tomu, že stroj není motor, který bude přesouvat data (pouze pro řídicí účely), kapacita počítače nemá vliv na propustnost pohybu dat.
- Jsou podporovány existující parametry z příkazu DistCp.

Tento obrázek znázorňuje migraci dat přes soukromý odkaz: 

![Diagram znázorňující architekturu řešení pro migraci dat přes privátní síť](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- V této architektuře se data migrují přes privátní partnerský vztah přes Azure ExpressRoute. Data nikdy neprocházejí přes veřejný internet.
- Nástroj DistCp nepodporuje privátní partnerský vztah ExpressRoute s koncovým bodem virtuální sítě Azure Storage. Doporučujeme použít nativní funkce Data Factory prostřednictvím prostředí integrace runtime k migraci dat.
- Pro tuto architekturu je nutné nainstalovat runtime integrace s vlastním hostitelem data factory na virtuální ms Windows ve vaší virtuální síti Azure. Můžete ručně vertikálně škálovat virtuální počítač nebo škálovat na více virtuálních počítačích, abyste mohli plně využívat vaše síť a vstupně-diagramy vstupně-diagramy úložiště nebo šířku pásma.
- Doporučená konfigurace pro každý virtuální počítač Azure (s nainstalovaným runtime vlastní hostovanou integrací Datové továrny) je Standard_D32s_v3 s 32 virtuálními procesory a 128 GB paměti. Můžete sledovat využití procesoru a paměti virtuálního počítače během migrace dat, abyste zjistili, jestli potřebujete vertikálně navýšit kapacitu virtuálního počítače pro lepší výkon nebo na vertikálně snížit kapacitu virtuálního počítače, abyste snížili náklady.
- Horizontální navýšení kapacity můžete také tak, že přisuzujete až čtyři uzly virtuálních virtuálních stránek s jedním prostředím integrace s vlastním hostitelem. Úloha jedné kopie spuštěná proti prostředí runtime s vlastním hostitelem integrace automaticky rozdělí sadu souborů a použije všechny uzly virtuálních aplikací ke kopírování souborů paralelně. Pro vysokou dostupnost doporučujeme začít se dvěma uzly virtuálních počítače, abyste se vyhnuli scénáři selhání jednoho bodu selhání během migrace dat.
- Při použití této architektury, počáteční snímek migrace dat a migrace delta data jsou k dispozici.

## <a name="implementation-best-practices"></a>Osvědčené postupy při provádění

Doporučujeme dodržovat tyto osvědčené postupy při implementaci migrace dat.

### <a name="authentication-and-credential-management"></a>Ověřování a správa pověření 

- Chcete-li ověřit hdfs, můžete použít [systém Windows (Kerberos) nebo anonymní](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Pro připojení k úložišti objektů Blob Azure je podporováno více typů ověřování.  Důrazně doporučujeme používat [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Spravované identity, postavené na automaticky spravované identitě Data Factory ve službě Azure Active Directory (Azure AD), umožňují konfigurovat kanály bez zadání přihlašovacích údajů v definici propojené služby. Případně můžete ověřit úložiště objektů Blob pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [podpisu sdíleného přístupu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Pro připojení k úložišti Data Lake Storage Gen2 je také podporováno více typů ověřování.  Důrazně doporučujeme používat [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), ale můžete také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) nebo klíč účtu [úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 
- Pokud nepoužíváte spravované identity pro prostředky Azure, důrazně doporučujeme [ukládat přihlašovací údaje do služby Azure Key Vault,](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) abyste usnadnili centrální správu a střídání klíčů bez úprav propojených služeb Data Factory. To je také [osvědčený postup pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Počáteční migrace dat snímku 

V režimu DistCp data můžete vytvořit jednu aktivitu kopírování a odeslat příkaz DistCp a použít různé parametry k řízení počátečního chování při migraci dat. 

V nativním režimu runtime datové továrny doporučujeme datový oddíl, zejména když migrujete více než 10 TB dat. Chcete-li rozdělit data, použijte názvy složek na HDFS. Potom každá úloha kopírování datové továrny můžete zkopírovat jeden oddíl složky najednou. Můžete spustit více úloh kopírování data factory současně pro lepší propustnost.

Pokud některá z úloh kopírování selže z důvodu problémů s přechodným úložištěm sítě nebo dat, můžete znovu spustit neúspěšnou úlohu kopírování a znovu načíst tento konkrétní oddíl z hdfs. Ostatní úlohy kopírování, které načítají jiné oddíly, nejsou ovlivněny.

### <a name="delta-data-migration"></a>Migrace rozdílových dat 

V režimu DistCp data můžete použít parametr `-update`příkazového řádku DistCp , zapisovat data, když se velikost zdrojového souboru a cílového souboru liší velikostí, pro migraci rozdílových dat.

V režimu nativní integrace data factory nejvýkonnější způsob, jak identifikovat nové nebo změněné soubory z HDFS je pomocí časově dělené konvence pojmenování. Pokud byla data v HDFS časově rozdělena s informacemi o časovém úseku v názvu souboru nebo složky (například */yyyy/mm/dd/file.csv),* může váš kanál snadno identifikovat, které soubory a složky se mají kopírovat postupně.

Případně pokud vaše data v HDFS není čas-partitioned, Data Factory můžete identifikovat nové nebo změněné soubory pomocí jejich **LastModifiedDate** hodnotu. Data Factory prohledá všechny soubory z HDFS a zkopíruje pouze nové a aktualizované soubory, které mají naposledy upravené časové razítko, které je větší než nastavená hodnota. 

Pokud máte velký počet souborů v HDFS, může počáteční prohledávání souborů trvat dlouhou dobu, bez ohledu na to, kolik souborů odpovídá podmínce filtru. V tomto scénáři doporučujeme nejprve rozdělit data pomocí stejného oddílu, který jste použili pro počáteční migraci snímku. Potom může dojít k prohledávání souborů paralelně.

### <a name="estimate-price"></a>Odhad ceny 

Zvažte následující kanál pro migraci dat z HDFS do úložiště objektů blob Azure: 

![Diagram, který znázorňuje cenový kanál](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Předpokládejme následující informace: 

- Celkový objem dat je 1 PB.
- Data můžete migrovat pomocí nativního režimu runtime integrace data factory.
- 1 PB je rozdělena do 1000 oddílů a každá kopie přesune jeden oddíl.
- Každá aktivita kopírování je konfigurována s jedním prostředím integrace s vlastním hostitelem, který je přidružen ke čtyřem počítačům a který dosahuje propustnosti 500 Mb/s.
- ProKaždá souběžnost je nastavena na **4** a agregační propustnost je 2 BPS.
- Celkem trvá dokončení migrace 146 hodin.

Zde je odhadovaná cena na základě našich předpokladů: 

![Tabulka s výpočty cen](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Toto je hypotetický příklad ceny. Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí.
> Cena za virtuální počítač Azure windows (s nainstalovanou runtime vlastní integrace) není zahrnuta.

### <a name="additional-references"></a>Další odkazy

- [HDFS konektor](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Konektor úložiště objektů blob Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopírovat průvodce optimalizací výkonu aktivity](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytvoření a konfigurace místního prostředí Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Vysoká dostupnost a škálovatelnost prostředí runtime s vlastním hostitelem](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Aspekty zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopírování souboru postupně na základě názvu souboru s časovým rozdělením](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopírovat nové a změněné soubory na základě LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Cenová stránka Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)