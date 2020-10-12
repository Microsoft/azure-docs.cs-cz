---
title: Migrace dat z místního serveru Netezza do Azure
description: K migraci dat z místního Netezza serveru do Azure použijte Azure Data Factory.
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
ms.openlocfilehash: 2197136b86d0bfbb2de79af6712c953339d46371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442833"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Použití Azure Data Factory k migraci dat z místního serveru Netezza do Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory poskytuje výkonný, robustní a nákladově efektivní mechanismus pro migraci dat se škálováním z místního serveru Netezza k vašemu účtu služby Azure Storage nebo k databázi Azure synapse Analytics (dřív SQL Data Warehouse). 

Tento článek poskytuje následující informace pro inženýry dat a vývojáře:

> [!div class="checklist"]
> * Výkon 
> * Kopírovat odolnost
> * Zabezpečení sítě
> * Architektura řešení vysoké úrovně 
> * Osvědčené postupy implementace  

## <a name="performance"></a>Výkon

Azure Data Factory nabízí architekturu bez serveru, která umožňuje paralelismus na různých úrovních. Pokud jste vývojář, znamená to, že můžete vytvářet kanály pro plné využití šířky pásma sítě i databáze k maximalizaci propustnosti přesunu dat pro vaše prostředí.

![Diagram výkonu](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Předchozí diagram lze interpretovat následujícím způsobem:

- Jedna aktivita kopírování může využít výhod škálovatelných výpočetních prostředků. Když použijete Azure Integration Runtime, můžete pro každou aktivitu kopírování v rámci serveru zadat [až 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) . V místním prostředí Integration runtime (v místním prostředí IR) můžete ručně škálovat počítač nebo škálovat na více počítačů ([až čtyři uzly](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) a jedna aktivita kopírování distribuuje svůj oddíl napříč všemi uzly. 

- Jedna aktivita kopírování čte z a zapisuje do úložiště dat pomocí více vláken. 

- Tok řízení Azure Data Factory může současně spustit více aktivit kopírování. Například je může spustit pomocí příkazu [pro každou smyčku](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Další informace najdete v tématu [Průvodce výkonem a škálovatelností aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Odolnost

V rámci jedné aktivity kopírování Azure Data Factory má vestavěný mechanismus opakování, který umožňuje zpracování určité úrovně přechodných chyb v úložištích dat nebo v podkladové síti.

Při Azure Data Factory aktivity kopírování se při kopírování dat mezi zdrojem a úložištěm dat jímky existují dva způsoby, jak zpracovávat nekompatibilní řádky. Můžete buď přerušit, nebo převzít aktivitu kopírování, nebo pokračovat ve kopírování zbývajících dat vynecháním nekompatibilních datových řádků. Chcete-li zjistit příčinu selhání, můžete protokolovat nekompatibilní řádky ve službě Azure Blob Storage nebo Azure Data Lake Store, opravovat data ve zdroji dat a opakovat aktivitu kopírování.

## <a name="network-security"></a>Zabezpečení sítě 

Ve výchozím nastavení Azure Data Factory přenáší data z místního serveru Netezza do účtu služby Azure Storage nebo do databáze Azure synapse Analytics pomocí šifrovaného připojení přes protokol HTTPS (Hypertext Transfer Protocol Secure). Protokol HTTPS zajišťuje šifrování dat při přenosu a znemožňuje odposlouchávání a útoky prostředníkem.

Případně, pokud nechcete, aby se data přenesla prostřednictvím veřejného Internetu, můžete přispět k vyššímu zabezpečení prostřednictvím přenosu dat prostřednictvím privátního partnerského propojení prostřednictvím trasy Azure Express. 

Další část popisuje, jak dosáhnout vyšší úrovně zabezpečení.

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje dva způsoby, jak migrovat data.

### <a name="migrate-data-over-the-public-internet"></a>Migrace dat přes veřejný Internet

![Migrace dat přes veřejný Internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Předchozí diagram lze interpretovat následujícím způsobem:

- V této architektuře můžete data bezpečně přenášet pomocí protokolu HTTPS prostřednictvím veřejného Internetu.

- Chcete-li dosáhnout této architektury, je nutné nainstalovat Azure Data Factory Integration runtime (v místním prostředí) na počítač s Windows za podnikovou bránou firewall. Ujistěte se, že tento modul runtime integrace má přímý přístup k serveru Netezza. Pokud chcete plně využívat síť a úložiště dat ke kopírování dat, můžete ručně škálovat počítač nebo škálovat kapacitu na více počítačů.

- Pomocí této architektury můžete migrovat data počátečního snímku i rozdílová data.

### <a name="migrate-data-over-a-private-network"></a>Migrace dat přes soukromou síť 

![Migrace dat přes soukromou síť](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Předchozí diagram lze interpretovat následujícím způsobem:

- V této architektuře migrujete data přes privátní partnerský vztah přes Azure Express Route a data nikdy neprochází přes veřejný Internet. 

- K dosažení této architektury je potřeba nainstalovat Azure Data Factory Integration runtime (v místním prostředí) na virtuální počítač s Windows v rámci služby Azure Virtual Network. Pokud chcete plně využívat síť a úložiště dat ke kopírování dat, můžete ručně škálovat virtuální počítač nebo škálovat na více virtuálních počítačů.

- Pomocí této architektury můžete migrovat data počátečního snímku i rozdílová data.

## <a name="implement-best-practices"></a>Implementace osvědčených postupů 

### <a name="manage-authentication-and-credentials"></a>Správa ověřování a přihlašovacích údajů 

- K ověření v Netezza můžete použít ověřování pomocí [rozhraní ODBC prostřednictvím připojovacího řetězce](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 

- Ověření ve službě Azure Blob Storage: 

   - Důrazně doporučujeme používat [pro prostředky Azure spravované identity](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Spravované identity založené na automaticky spravované Azure Data Factory identitě v Azure Active Directory (Azure AD) umožňují konfigurovat kanály bez nutnosti zadávat přihlašovací údaje v definici propojené služby.  

   - Případně můžete provést ověření ve službě Azure Blob Storage pomocí [instančního objektu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sdíleného přístupového podpisu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)nebo [klíče účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 

- Ověření pro Azure Data Lake Storage Gen2: 

   - Důrazně doporučujeme používat [pro prostředky Azure spravované identity](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity).
   
   - Můžete také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) nebo [klíč účtu úložiště](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 

- Ověření ve službě Azure synapse Analytics:

   - Důrazně doporučujeme používat [pro prostředky Azure spravované identity](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity).
   
   - Můžete také použít [instanční objekt](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) nebo [ověřování SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- Pokud nepoužíváte spravované identity pro prostředky Azure, důrazně doporučujeme [ukládat přihlašovací údaje v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) , aby bylo snazší centrálně spravovat a střídat klíče, aniž byste museli upravovat Azure Data Factory propojené služby. Toto je také jedním z [osvědčených postupů pro CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrace dat počátečního snímku 

Pro malé tabulky (to znamená, že tabulky se svazkem menším než 100 GB nebo které je možné migrovat do Azure do dvou hodin), můžete vytvořit každou úlohu kopírování dat na každou tabulku. Pro větší propustnost můžete spustit několik úloh kopírování Azure Data Factory pro souběžné načtení samostatných tabulek. 

Aby bylo možné spouštět paralelní dotazy a kopírovat data podle oddílů, můžete v rámci každé úlohy kopírování spojit i určitou úroveň paralelismu pomocí [ `parallelCopies` nastavení vlastnosti](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) v některé z následujících možností datových oddílů:

- Abychom vám pomohli dosáhnout vyšší efektivity, doporučujeme začít z datového řezu.  Ujistěte se, že hodnota v `parallelCopies` nastavení je menší než celkový počet oddílů datového řezu v tabulce na serveru Netezza.  

- Pokud je svazek každého oddílu pro datové řezy stále velký (například 10 GB nebo větší), doporučujeme přepnout na oddíl dynamického rozsahu. Tato možnost nabízí větší flexibilitu pro definování počtu oddílů a objemu jednotlivých oddílů podle sloupce oddílu, horní meze a dolní meze.

Pro větší tabulky (tj. tabulky se svazkem 100 GB nebo vyšší nebo, které *nelze* migrovat do Azure do dvou hodin), doporučujeme rozdělit data na oddíly vlastními dotazy a pak vytvořit každou kopii v jednom okamžiku kopírováním jednotlivých oddílů. Pro zajištění lepší propustnosti můžete současně spustit více úloh kopírování Azure Data Factory. Pro každý cíl kopírování a úlohy pro načtení jednoho oddílu vlastním dotazem můžete zvýšit propustnost tím, že provedete paralelismus prostřednictvím datového řezu nebo dynamického rozsahu. 

Pokud dojde k selhání jakékoli úlohy kopírování z důvodu přechodného problému sítě nebo úložiště dat, můžete znovu spustit úlohu, která se nezdařila, a znovu načíst konkrétní oddíl z tabulky. Jiné úlohy kopírování, které načítají jiné oddíly, nejsou ovlivněny.

Při načítání dat do databáze Azure synapse Analytics doporučujeme, abyste v rámci úlohy kopírování ve službě Azure Blob Storage povolili základ databáze jako pracovní.

### <a name="migrate-delta-data"></a>Migrace rozdílových dat 

Chcete-li identifikovat nové nebo aktualizované řádky z tabulky, použijte ve schématu sloupec časového razítka nebo přírůstkový klíč. Pak můžete uložit nejnovější hodnotu jako horní mez v externí tabulce a pak ji použít k filtrování rozdílových dat při příštím načtení dat. 

Každá tabulka může použít jiný sloupec meze k identifikaci nových nebo aktualizovaných řádků. Doporučujeme vytvořit tabulku externího ovládacího prvku. V tabulce každý řádek představuje jednu tabulku na serveru Netezza s jeho specifickým názvem sloupce meze a horní hodnotou meze. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Konfigurace prostředí Integration runtime v místním prostředí

Pokud migrujete data ze serveru Netezza do Azure, bez ohledu na to, jestli je server místní za bránou firewall nebo ve virtuálním síťovém prostředí, musíte na počítači s Windows nebo na virtuálním počítači s Windows nainstalovat místně hostovaný IR, což je modul, který se používá k přesunu dat. Při instalaci prostředí IR pro místní hostování doporučujeme následující postup:

- Pro každý počítač s Windows nebo virtuální počítač začněte konfigurací 32 vCPU a 128-GB paměti. V průběhu migrace dat můžete sledovat využití CPU a paměti v počítači IR, abyste viděli, jestli potřebujete ještě víc škálovat počítač pro lepší výkon, nebo snížit kapacitu počítače, aby se ušetřily náklady.

- Horizontální navýšení kapacity můžete také škálovat tak, že přidružíte až čtyři uzly s jedním místně hostovaným IR. Jedna úloha kopírování, která běží na místním prostředí IR, automaticky aplikuje všechny uzly virtuálních počítačů na paralelní kopírování dat. V případě vysoké dostupnosti začněte se čtyřmi uzly virtuálních počítačů, abyste se vyhnuli jednomu bodu selhání během migrace dat.

### <a name="limit-your-partitions"></a>Omezení oddílů

Osvědčeným postupem je vyřídit výkon konceptu (ověření koncepce) s reprezentativní ukázkovou datovou sadou, abyste pro každou aktivitu kopírování mohli určit vhodnou velikost oddílu. Každý oddíl do Azure doporučujeme načíst do dvou hodin.  

Chcete-li zkopírovat tabulku, začněte s jednou aktivitou kopírování s jedním místně hostovaným počítačem IR. Postupně zvyšujte `parallelCopies` nastavení na základě počtu oddílů datového řezu v tabulce. Podívejte se, jestli se celá tabulka dá načíst do Azure do dvou hodin, podle propustnosti, která je výsledkem úlohy kopírování. 

Pokud se do Azure nedá načíst do dvou hodin a kapacita uzlu IR v místním prostředí a úložiště dat se nepoužívá, postupně zvyšujte počet souběžných aktivit kopírování, dokud nedosáhnete limitu sítě nebo šířky pásma úložišť dat. 

Sledujte využití CPU a paměti na místním počítači IR a připravte se na horizontální navýšení kapacity počítače nebo horizontální navýšení kapacity na více počítačů, když zjistíte, že je procesor a paměť plně využité. 

Když narazíte na chyby omezování, jak je uvedeno v Azure Data Factory aktivita kopírování, buď snižte počet souběžnosti nebo `parallelCopies` nastavení v Azure Data Factory, nebo zvažte zvýšení limitu šířky pásma nebo vstupně-výstupních operací za sekundu (IOPS) v síti a úložištích dat. 


### <a name="estimate-your-pricing"></a>Odhad ceny 

Vezměte v úvahu následující kanál, který je vytvořený k migraci dat z místního serveru Netezza do databáze Azure synapse Analytics:

![Cenový kanál](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Pojďme předpokládat, že jsou splněné následující příkazy: 

- Celkový objem dat je 50 terabajtů (TB). 

- Migrujeme data pomocí architektury prvního řešení (Server Netezza je v místním prostředí za bránou firewall).

- Svazek 50-TB je rozdělen do 500 oddílů a každá aktivita kopírování přesune jeden oddíl.

- Každá aktivita kopírování je nakonfigurována s jedním místním prostředím IR na čtyři počítače a dosáhne propustnosti 20 megabajtů za sekundu (MB/s). (V rámci aktivity kopírování `parallelCopies` je nastaveno na 4 a každé vlákno načtení dat z tabulky dosahuje propustnosti 5 MB/s.)

- Souběžnost ForEach je nastavená na 3 a agregovaná propustnost je 60 MB/s.

- V celkovém případě dokončení migrace trvá 243 hodin.

V závislosti na předchozích předpokladech je zde uvedená odhadovaná cena: 

![Tabulka s cenami](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Ceny uvedené v předchozí tabulce jsou hypotetické. Vaše skutečné ceny závisí na skutečné propustnosti ve vašem prostředí. Není zahrnuta cena za počítač se systémem Windows (s nainstalovaným prostředím IR pro místní hostování). 

### <a name="additional-references"></a>Další odkazy

Další informace najdete v následujících článcích a příručkách:

- [Migrace dat z místní databáze relačního datového skladu do Azure pomocí Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Konektor Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Konektor ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Konektor služby Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Konektor Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Konektor Azure synapse Analytics](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Průvodce laděním výkonu aktivity kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Vytvoření a konfigurace místního prostředí Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Vysoce hostované prostředí Integration runtime – HA a škálovatelnost](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Aspekty zabezpečení přesunu dat](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Uložení přihlašovacích údajů v Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Přírůstkové kopírování dat z jedné tabulky](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Přírůstkové kopírování dat z více tabulek](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Stránka s cenami Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Další kroky

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
