---
title: Migrace dat z místního serveru Netezza do Azure pomocí Azure Data Factory | Microsoft Docs
description: K migraci dat z místního Netezza serveru do Azure použijte Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259558"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Použití Azure Data Factory k migraci dat z místního serveru Netezza do Azure 

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat ve velkém měřítku z místního serveru Netezza na Azure Storage nebo Azure SQL Data Warehouse. Tento článek poskytuje následující informace pro inženýry dat a vývojáře:

> [!div class="checklist"]
> * Výkon 
> * Kopírovat odolnost
> * Zabezpečení sítě
> * Architektura řešení vysoké úrovně 
> * Osvědčené postupy implementace  

## <a name="performance"></a>Výkon

Azure Data Factory nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních, což vývojářům umožňuje vytvářet kanály pro plnou využívání šířky pásma sítě a šířku pásma databáze k maximalizaci propustnosti přesunu dat pro vaše hlediska.

![výkon](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Jediná aktivita kopírování může využít výhod škálovatelných výpočetních prostředků: při použití Azure Integration Runtime můžete pro každou aktivitu kopírování v rámci serveru zadat [až 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) . Při použití Integration Runtime v místním prostředí můžete ručně navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů ([až 4 uzly](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) a jedna aktivita kopírování bude distribuovat svůj oddíl napříč všemi uzly. 
- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí více vláken. 
- Tok řízení Azure Data Factory může spustit více aktivit kopírování paralelně, například pomocí [pro každou smyčku](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Další podrobnosti můžete získat z [Průvodce výkonem aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) .

## <a name="resilience"></a>Odolnost

V rámci jedné aktivity kopírování Azure Data Factory má vestavěný mechanismus opakování, aby mohl zpracovávat určitou úroveň přechodných chyb v úložištích dat nebo v podkladové síti.

Aktivita kopírování Azure Data Factory také nabízí dva způsoby, jak zpracovávat nekompatibilní řádky při kopírování dat mezi zdroji a úložišti dat jímky. Pokud dojde k nekompatibilním datům nebo pokud chcete pokračovat v kopírování dat REST, přeskočí nekompatibilní řádky dat, můžete aktivitu kopírování přerušit a převzít. Kromě toho můžete protokolovat nekompatibilní řádky v úložišti objektů BLOB v Azure nebo Azure Data Lake Store, abyste zjistili příčinu selhání, opravovat data ve zdroji dat a opakovat aktivitu kopírování.

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení Azure Data Factory přenáší data z místního serveru Netezza do Azure Storage nebo Azure SQL Data Warehouse pomocí šifrovaného připojení přes protokol HTTPS. Zajišťuje šifrování dat při přenosu a znemožňuje odposlouchávání a útoky prostředníkem.

Případně, pokud nechcete, aby se data přenesla prostřednictvím veřejného Internetu, můžete dosáhnout vyššího zabezpečení prostřednictvím přenosu dat přes privátní partnerský vztah přes Azure Express Route. Informace o tom, jak se dá dosáhnout, najdete v níže uvedené architektuře řešení.

## <a name="solution-architecture"></a>Architektury řešení

Migrace dat prostřednictvím veřejného Internetu:

![řešení – architektura – veřejná síť](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- V této architektuře se data přenáší zabezpečeně pomocí protokolu HTTPS prostřednictvím veřejného Internetu.
- Aby bylo možné dosáhnout této architektury, je nutné nainstalovat Azure Data Factory místního prostředí Integration runtime na počítač s Windows za podnikovou bránou firewall. Ujistěte se, že váš Azure Data Factory místní prostředí Integration runtime na počítači s Windows může přímo získat přístup k vašemu Netezza serveru. Ruční horizontální navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů umožňuje plně využívat síť a úložiště dat pro kopírování dat.
- Pomocí této architektury se dá dosáhnout jak migrace dat počátečního snímku, tak migrace rozdílových dat.

Migrovat data prostřednictvím privátního propojení: 

![řešení – architektura – privátní síť](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- V této architektuře se migrace dat provádí přes privátní partnerský vztah přes Azure Express Route tak, aby se data nikdy neprováděla přes veřejný Internet. 
- Aby bylo možné dosáhnout této architektury, je nutné nainstalovat Azure Data Factory místního prostředí Integration runtime na virtuální počítač s Windows v rámci služby Azure Virtual Network. Můžete ručně škálovat virtuální počítače nebo škálovat na více virtuálních počítačů, abyste mohli plně využívat síť a úložiště dat pro kopírování dat.
- Pomocí této architektury se dá dosáhnout jak migrace dat počátečního snímku, tak migrace rozdílových dat.

## <a name="implementation-best-practices"></a>Osvědčené postupy implementace 

### <a name="authentication-and-credential-management"></a>Ověřování a Správa přihlašovacích údajů 

- K ověření v Netezza můžete použít ověřování pomocí [rozhraní ODBC prostřednictvím připojovacího řetězce](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 
- Pro připojení k Azure Blob Storage se podporuje víc typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) se důrazně doporučuje: postavené na automaticky spravované Azure Data Factory identifikovat v Azure AD, umožňuje konfigurovat kanály bez zadání přihlašovacích údajů v definici propojené služby.  Případně můžete provést ověření v Azure Blob Storage pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sdíleného přístupového podpisu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Pro připojení k Azure Data Lake Storage Gen2 se podporuje taky více typů ověřování.  Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) se důrazně doporučuje, i když je možné také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) nebo [klíč účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Pro připojení k Azure SQL Data Warehouse se podporuje taky více typů ověřování. Použití [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) se důrazně doporučuje, i když je také možné použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) nebo [ověřování SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) .
- Pokud nepoužíváte spravované identity pro prostředky Azure, doporučuje se [ukládat přihlašovací údaje v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) důrazně doporučujeme, abyste usnadnili centrální správu a střídání klíčů bez nutnosti úprav Azure Data Factory propojených služeb.  Toto je také jedním z [osvědčených postupů pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrace dat počátečního snímku 

V případě malých tabulek, pokud je velikost svazku menší než 100 GB nebo se dá migrovat do Azure do 2 hodin, můžete vytvořit každou úlohu kopírování dat na každou tabulku. Můžete spustit několik úloh kopírování Azure Data Factory pro současné načtení různých tabulek pro lepší propustnost. 

V rámci každé úlohy kopírování můžete také dosáhnout určité úrovně paralelismu pomocí možnosti [parallelCopies nastavení](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) s datovým oddílem a spouštět paralelní dotazy a kopírovat data podle oddílů. K dispozici jsou dvě možnosti datového oddílu s podrobnostmi níže.
- Doporučujeme začít od datového řezu, protože je efektivnější.  Ujistěte se, že počet paralelismus v parallelCopies je pod celkovým počtem oddílů datových řezů v tabulce na serveru Netezza.  
- Pokud je velikost svazku pro každý oddíl datového řezu pořád velká (například větší než 10 GB), doporučujeme přepnout na oddíl dynamického rozsahu, kde budete mít větší flexibilitu, abyste mohli definovat počet oddílů a velikost svazku pro každý oddíl. podle sloupce oddílu, horní meze a dolní mez.

V případě rozsáhlých tabulek, pokud je velikost svazku větší než 100 GB nebo v rámci 2 hodin není možné migrovat do Azure, doporučujeme rozdělit data na oddíly vlastními dotazy a pak vytvořit každou úlohu kopírování vždy po zkopírování jednotlivých oddílů. Pro lepší propustnost můžete spustit více úloh kopírování Azure Data Factory současně. Uvědomte si, že pro každý cíl úlohy kopírování tak, aby jeden oddíl načetl vlastní dotaz, můžete přesto povolit paralelismus prostřednictvím datového řezu nebo dynamického rozsahu a zvýšit tak propustnost. 

Pokud některá z úloh kopírování selže kvůli přechodnému problému se sítí nebo úložiště dat, můžete znovu spustit úlohu, která se nezdařila, a znovu znovu načíst konkrétní oddíl z tabulky. Všechny ostatní úlohy kopírování načítající jiné oddíly nebudou mít vliv na.

V případě, že se data načítají do Azure SQL Data Warehouse, doporučujeme, aby se v rámci úlohy kopírování v úložišti objektů BLOB v Azure jako příprava aktivovala.

### <a name="delta-data-migration"></a>Migrace rozdílových dat 

Způsob, jak identifikovat nové nebo aktualizované řádky z tabulky, používá sloupec časového razítka nebo přírůstek klíče ve schématu a pak uloží nejnovější hodnotu jako horní mez v externí tabulce, která se dá použít k filtrování rozdílových dat pro příští načítání dat. 

Různé tabulky mohou pro identifikaci nových nebo aktualizovaných řádků použít jiný sloupec meze. Doporučujeme vám vytvořit tabulku externích ovládacích prvků, kde každý řádek představuje jednu tabulku na Netezza serveru s názvem sloupce jeho konkrétní meze a hodnotou horní meze. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Konfigurace místního prostředí Integration runtime na virtuálním počítači Azure nebo na počítači

Vzhledem k tomu, že migrujete data ze serveru Netezza do Azure, nemusíte Netezza Server zaslíbit za bránou firewall nebo v rámci prostředí virtuální sítě, a proto je potřeba nainstalovat modul runtime integrace v místním prostředí do počítače s Windows nebo virtuálního počítače, který je modulem pro přesun. údajů.

- Doporučená konfigurace, kterou začíná pro každý počítač nebo virtuální počítač, je 32 vCPU a 128-GB paměti. Během migrace dat můžete sledovat využití procesoru a paměti INFRAČERVENého počítače a zjistit, jestli potřebujete počítač dále škálovat, aby se zajistil vyšší výkon, nebo škálovat počítač tak, aby ušetřil náklady.
- Horizontální navýšení kapacity můžete také škálovat tak, že přiřadíte až 4 uzly s jedním místně hostovaným IR. Jedna úloha kopírování spuštěná v místním prostředí IR bude automaticky využívat všechny uzly virtuálních počítačů ke kopírování dat paralelně. Pro zajištění vysoké dostupnosti se doporučuje začít se dvěma uzly virtuálních počítačů, aby při migraci dat nedocházelo k jednomu bodu selhání.

### <a name="rate-limiting"></a>Omezení rychlosti

Osvědčeným postupem je provést ověření výkonnosti pomocí reprezentativní vzorové datové sady, abyste mohli určit vhodnou velikost oddílu pro každou aktivitu kopírování. Doporučujeme, abyste každý oddíl načetli do Azure do 2 hodin.  

Pokud chcete zkopírovat tabulku, začněte s jednou aktivitou kopírování s jedním místně hostovaným počítačem IR. Postupně zvyšujte nastavení parallelCopies na základě počtu oddílů datových řezů v tabulce a podívejte se, jestli se celá tabulka dá načíst do Azure během 2 hodin podle propustnosti, kterou vidíte v úloze kopírování. 

Pokud není možné dosáhnout, a současně není kapacita uzlu IR v místním prostředí a úložiště dat plně využitá, postupně zvyšujte počet souběžných aktivit kopírování, dokud nedosáhnete limitů sítě nebo šířky pásma úložišť dat. 

Sledujte využití CPU nebo paměti v místním prostředí IR a připravte se na horizontální navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů, když vidíte, že CPU nebo paměť jsou plně využité. 

Když narazíte na chyby omezování hlášené Azure Data Factory aktivitou kopírování, zmenšete nastavení souběžnosti nebo parallelCopies v Azure Data Factory nebo zvažte zvýšení limitů šířky pásma a IOPS úložišť sítě a dat. 


### <a name="estimating-price"></a>Odhad ceny 

Vezměte v úvahu následující kanál vytvořený pro migraci dat z místního serveru Netezza do služby Azure SQL Data Warehouse:

![ceny – kanál](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Můžeme předpokládat následující: 

- Celkový objem dat je 50 TB. 
- Migrace dat pomocí první architektury řešení (Netezza Server je místní za bránou firewall)
- 50 TB je rozdělené do 500 oddílů a každá aktivita kopírování přesune jeden oddíl.
- Každá aktivita kopírování je nakonfigurovaná s jedním místním prostředím IR na 4 počítače a propustností 20 MB/s. (V rámci aktivity kopírování je parallelCopies nastaveno na 4 a každé vlákno načtení dat z tabulky dosahuje propustnosti 5 MB/s.)
- Souběžnost ForEach je nastavená na 3 a agregovaná propustnost je 60 MB/s.
- V celkovém případě dokončení migrace trvá 243 hodin.

Tady je odhadovaná cena na základě výše uvedených předpokladů: 

![ceny – tabulka](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Toto je hypotetický příklad ceny. Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí. Není zahrnuta cena za počítač se systémem Windows (s nainstalovaným místním prostředím Integration Runtime). 

### <a name="additional-references"></a>Další odkazy 
- [Migrace dat z místního relačního datového skladu do Azure pomocí Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Konektor Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Konektor ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Konektor Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Konektor Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Průvodce laděním výkonu aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytváření a konfigurace Integration Runtime pro místní hostování](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Vysoce hostované prostředí Integration runtime – HA a škálovatelnost](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Požadavky na zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Ukládat přihlašovací údaje v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Přírůstkové kopírování dat z jedné tabulky](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Přírůstkové kopírování dat z více tabulek](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Stránka s cenami Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)