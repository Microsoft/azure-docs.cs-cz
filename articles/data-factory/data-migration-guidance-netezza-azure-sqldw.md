---
title: Migrace dat z místního serveru Netezza do Azure
description: Azure Data Factory slouží k migraci dat z místního serveru Netezza do Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: 80c9929f37b4890387a7625f04db6ce3e37f0cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74922124"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Migrace dat z místního serveru Netezza do Azure pomocí Azure Data Factory 

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat ve velkém měřítku z místního serveru Netezza do vašeho účtu úložiště Azure nebo databáze Azure SQL Data Warehouse. 

Tento článek obsahuje následující informace pro datové inženýry a vývojáře:

> [!div class="checklist"]
> * Výkon 
> * Odolnost kopírování
> * Zabezpečení sítě
> * Architektura řešení na vysoké úrovni 
> * Osvědčené postupy při provádění  

## <a name="performance"></a>Výkon

Azure Data Factory nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních. Pokud jste vývojář, znamená to, že můžete vytvářet kanály pro plné využití šířky pásma sítě i databáze k maximalizaci propustnost přesunu dat pro vaše prostředí.

![Výkonnostní diagram](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Předchozí diagram lze interpretovat takto:

- Jedna aktivita kopírování může využít škálovatelné výpočetní prostředky. Při použití prostředí Azure Integration Runtime můžete zadat [až 256 DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) pro každou aktivitu kopírování způsobem bez serveru. S runtime integrace s vlastním hostitelem (samoobslužné infračervené ovládání) můžete ručně vertikálně vertikálně navýšit kapacitu počítače nebo horizontální navýšení kapacity na více počítačů[(až čtyři uzly)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)a jedna aktivita kopírování distribuuje svůj oddíl mezi všechny uzly. 

- Jedna kopie aktivity čte z a zapisuje do úložiště dat pomocí více vláken. 

- Tok řízení Azure Data Factory můžete spustit více aktivit kopírování paralelně. Můžete je například spustit pomocí [smyčky For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Další informace naleznete v [tématu Kopírování výkonu aktivity a průvodce škálovatelností](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odolnost

V rámci spuštění aktivity jedné kopie má Azure Data Factory integrovaný mechanismus opakování, který mu umožňuje zpracovat určitou úroveň přechodných selhání v úložišti dat nebo v základní síti.

S aktivitou kopírování Azure Data Factory při kopírování dat mezi zdrojovým a jímacími datovými úložišti máte dva způsoby zpracování nekompatibilních řádků. Můžete přerušit a nepodaří aktivitu kopírování nebo pokračovat v kopírování zbývajících dat přeskočením nekompatibilních řádků dat. Kromě toho můžete zjistit příčinu selhání, můžete protokolovat nekompatibilní řádky v úložišti objektů Blob Azure nebo Azure Data Lake Store, opravit data na zdroj dat a opakovat aktivitu kopírování.

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení azure data factory přenáší data z místního serveru Netezza do účtu úložiště Azure nebo databáze Azure SQL Data Warehouse pomocí šifrovaného připojení přes server HTTPS (HTTP). Protokol HTTPS poskytuje šifrování dat při přenosu a zabraňuje odposlouchávání a útokům prostředníkem.

Případně pokud nechcete, aby se data přenášela přes veřejný internet, můžete dosáhnout vyššího zabezpečení přenosem dat přes privátní partnerský vztah přes Azure Express Route. 

V další části se popisuje, jak dosáhnout vyšší zabezpečení.

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje dva způsoby migrace dat.

### <a name="migrate-data-over-the-public-internet"></a>Migrace dat přes veřejný internet

![Migrace dat přes veřejný internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Předchozí diagram lze interpretovat takto:

- V této architektuře můžete přenášet data bezpečně pomocí protokolu HTTPS přes veřejný internet.

- K dosažení této architektury je třeba nainstalovat runtime integrace Azure Data Factory (vlastní hostované) na počítači se systémem Windows za podnikovou bránou firewall. Ujistěte se, že tento integrační runtime může přímo přistupovat k serveru Netezza. Chcete-li plně využít šířku pásma sítě a úložišť dat ke kopírování dat, můžete ručně vertikálně navýšit kapacitu počítače nebo škálovat na více počítačů.

- Pomocí této architektury můžete migrovat počáteční snímek data a delta data.

### <a name="migrate-data-over-a-private-network"></a>Migrace dat v privátní síti 

![Migrace dat v privátní síti](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Předchozí diagram lze interpretovat takto:

- V této architektuře migrujete data přes privátní propojení partnerského vztahu přes Azure Express Route a data nikdy neprocházejí přes veřejný internet. 

- K dosažení této architektury je potřeba nainstalovat runtime integrace Azure Data Factory (vlastní hostovaný) na virtuálním počítači (VM) Windows v rámci virtuální sítě Azure. Pokud chcete plně využít šířku pásma sítě a datových úložišť ke kopírování dat, můžete ručně vertikálně navýšit kapacitu virtuálního počítače nebo vertikálně navýšit kapacitu na více virtuálních počítačích.

- Pomocí této architektury můžete migrovat počáteční snímek data a delta data.

## <a name="implement-best-practices"></a>Implementace osvědčených postupů 

### <a name="manage-authentication-and-credentials"></a>Správa ověřování a přihlašovacích údajů 

- Chcete-li se ověřit na netezzu, můžete použít [ověřování ODBC pomocí připojovacího řetězce](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 

- Ověření do úložiště objektů blob Azure: 

   - Důrazně doporučujeme používat [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Spravované identity postavené na automaticky spravované identitě Azure Data Factory ve službě Azure Active Directory (Azure AD) umožňují konfigurovat kanály bez nutnosti zadávat přihlašovací údaje v definici propojené služby.  

   - Případně můžete ověřit úložiště objektů blob Azure pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [podpisu sdíleného přístupu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 

- Ověření na Azure Data Lake Storage Gen2: 

   - Důrazně doporučujeme používat [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity).
   
   - Můžete také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) nebo [klíč účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 

- Ověření do Datového skladu Azure SQL:

   - Důrazně doporučujeme používat [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity).
   
   - Můžete také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) nebo [ověřování SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- Pokud nepoužíváte spravované identity pro prostředky Azure, důrazně doporučujeme [ukládat přihlašovací údaje do služby Azure Key Vault,](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) abyste usnadnili centrální správu a střídání klíčů bez nutnosti úpravy propojených služeb Azure Data Factory. To je také jeden z [osvědčených postupů pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrace počátečních dat snímku 

U malých tabulek (tj. tabulek se svazkem menším než 100 GB nebo s míváme do Azure do dvou hodin) můžete provést data načítání jednotlivých úloh kopírování na tabulku. Pro větší propustnost můžete spustit více úloh kopírování Azure Data Factory a načíst samostatné tabulky současně. 

V rámci každé úlohy kopírování můžete spustit paralelní dotazy a kopírovat data pomocí oddílů, můžete také dosáhnout určité úrovně paralelismu pomocí [ `parallelCopies` nastavení vlastnosti](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) s některou z následujících možností oddílu dat:

- Chcete-li dosáhnout vyšší efektivity, doporučujeme začít od řezu dat.  Ujistěte se, že `parallelCopies` hodnota v nastavení je menší než celkový počet oddílů s řezy dat v tabulce na serveru Netezza.  

- Pokud je objem každého oddílu s řezem dat stále velký (například 10 GB nebo více), doporučujeme přepnout na oddíl s dynamickým rozsahem. Tato možnost poskytuje větší flexibilitu definovat počet oddílů a svazek každého oddílu podle sloupce oddílu, horní mez a dolní mez.

U větších tabulek (tj. tabulek se svazkem 100 GB nebo větším nebo *které* nelze migrovat do Azure do dvou hodin) doporučujeme rozdělit data podle vlastního dotazu a potom vytvořit každý zkopírovací balíček po jednotlivých oddílech. Pro lepší propustnost můžete spustit více úloh kopírování Azure Data Factory současně. Pro každý cíl úlohy kopírování načítání jednoho oddílu podle vlastního dotazu můžete zvýšit propustnost povolením paralelismu prostřednictvím řezu dat nebo dynamického rozsahu. 

Pokud se jakákoli úloha kopírování nezdaří z důvodu přechodného problému v síti nebo úložišti dat, můžete znovu spustit neúspěšnou úlohu kopírování a znovu načíst tento konkrétní oddíl z tabulky. Ostatní úlohy kopírování, které načítají jiné oddíly, nejsou ovlivněny.

Když načtete data do databáze Azure SQL Data Warehouse, doporučujeme povolit PolyBase v rámci úlohy kopírování s úložištěm objektů blob Azure jako pracovní.

### <a name="migrate-delta-data"></a>Migrace rozdílových dat 

Chcete-li identifikovat nové nebo aktualizované řádky z tabulky, použijte sloupec časového razítka nebo obnovovací klíč v rámci schématu. Nejnovější hodnotu pak můžete uložit jako vodoznak s vysokým napětím do externí tabulky a potom ji použít k filtrování rozdílových dat při příštím načítání dat. 

Každá tabulka může k identifikaci nových nebo aktualizovaných řádků použít jiný sloupec vodoznaku. Doporučujeme vytvořit tabulku externího ovládacího prvku. V tabulce představuje každý řádek jednu tabulku na serveru Netezza s jeho specifickým názvem sloupce vodoznaku a hodnotou vysokého vodoznaku. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Konfigurace prostředí runtime integrace s vlastním hostitelem

Pokud migrujete data ze serveru Netezza do Azure, ať už je server místní za bránou firewall vaší společnosti nebo v prostředí virtuální sítě, musíte nainstalovat infračervený přenos s vlastním hostitelem na počítač s Windows nebo virtuální počítač, což je modul, který se používá k přesunout data. Při instalaci infračerveného systému s vlastním hostitelem doporučujeme následující postup:

- Pro každý počítač s Windows nebo virtuální počítač, začněte s konfigurací 32 virtuálních procesorů a 128 GB paměti. Během migrace dat můžete sledovat využití procesoru a paměti infračerveného počítače a zjistit, zda je třeba dále vertikálně navýšit kapacitu počítače pro lepší výkon nebo zmenšit kapacitu počítače, abyste ušetřili náklady.

- Horizontální navýšení kapacity můžete také tak, že přisuzujete až čtyři uzly pomocí jednoho infračerveného přenosového souboru s vlastním hostitelem. Úloha jedné kopie, která je spuštěna proti infračervenému přenosu s vlastním hostitelem, automaticky použije všechny uzly virtuálního soudu ke kopírování dat paralelně. Pokud chcete získat vysokou dostupnost, začněte se čtyřmi uzly virtuálních aplikací, abyste se vyhnuli jedinému bodu selhání během migrace dat.

### <a name="limit-your-partitions"></a>Omezte své oddíly

Jako osvědčený postup proveďte ověření výkonu konceptu (POC) s reprezentativní ukázkovou datovou sadou, abyste mohli určit vhodnou velikost oddílu pro každou aktivitu kopírování. Doporučujeme načíst každý oddíl do Azure do dvou hodin.  

Chcete-li zkopírovat tabulku, začněte s aktivitou jedné kopie pomocí jednoho počítače infračerveného počítače s vlastním hostitelem. Postupně zvyšujte `parallelCopies` nastavení na základě počtu oddílů s řezy dat v tabulce. Zjistěte, jestli celou tabulku lze načíst do Azure do dvou hodin, podle propustnost, která je výsledkem úlohy kopírování. 

Pokud se do Azure nenačte do dvou hodin a kapacita infračerveného uzlu hostovaného na vlastním trhu a úložiště dat není plně využita, postupně zvyšujte počet souběžných aktivit kopírování, dokud nedosáhnete limitu sítě nebo limitu šířky pásma dat. Obchody. 

Mějte sledování využití procesoru a paměti na samoobslužné infračervený počítač a buďte připraveni vertikálně navýšit kapacitu počítače nebo horizontální navýšení kapacity do více počítačů, když uvidíte, že procesor a paměť jsou plně používány. 

Když narazíte na chyby omezení, jak je uvedeno v aktivitě `parallelCopies` kopírování Azure Data Factory, buď snížit souběžnost nebo nastavení v Azure Data Factory, nebo zvážit zvýšení šířky pásma nebo vstupně-v.I. operace za sekundu (IOPS) limity sítě a úložiště dat. 


### <a name="estimate-your-pricing"></a>Odhad cen 

Zvažte následující kanál, který se konstruuje k migraci dat z místního serveru Netezza do databáze datového skladu Azure SQL:

![Cenový kanál](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Předpokládejme, že jsou pravdivá následující tvrzení: 

- Celkový datový svazek je 50 terabajtů (TB). 

- Přesouváme data pomocí architektury prvního řešení (server Netezza je místní za bránou firewall).

- Svazek 50 TB je rozdělen na 500 oddílů a každá aktivita kopírování přesune jeden oddíl.

- Každá aktivita kopírování je konfigurována s jedním vlastním hostitelem infračerveného přenosu proti čtyřem počítačům a dosahuje propustnost 20 megabajtů za sekundu (MB/s). (V rámci `parallelCopies` aktivity kopírování je nastavena na 4 a každé vlákno pro načtení dat z tabulky dosahuje propustnost 5 Mb/s.)

- Souběžnost ForEach je nastavena na 3 a agregační propustnost je 60 Mb/s.

- Celkem trvá dokončení migrace 243 hodin.

Na základě předchozích předpokladů, zde je odhadovaná cena: 

![Cenová tabulka](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Ceny uvedené v předchozí tabulce jsou hypotetické. Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí. Cena za počítač se systémem Windows (s nainstalovaným infračerveným zástupcem, který je umístěn samostatně. 

### <a name="additional-references"></a>Další odkazy

Další informace naleznete v následujících článcích a průvodcích:

- [Migrace dat z místní relační databáze datového skladu do Azure pomocí Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Konektor Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Konektor ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Konektor úložiště objektů blob Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Konektor datového skladu Azure SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Kopírovat průvodce optimalizací výkonu aktivity](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytvoření a konfigurace místního prostředí Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Runtime HA integrace s vlastním hostitelem a škálovatelnost](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Aspekty zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopírování dat postupně z jedné tabulky](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Kopírování dat postupně z více tabulek](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Cenová stránka Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
