---
title: Limity prostředků Azure SQL Database – spravovaná instance | Dokumentace Microsoftu
description: Tento článek obsahuje přehled limity prostředků Azure SQL Database pro spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: 7ff8405bba39e274c4f9f0cbacb7c295564c877e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303200"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Přehled Azure SQL Database managed instance omezení prostředků

Tento článek obsahuje základní informace o omezení prostředků pro spravovanou instanci Azure SQL Database a poskytuje informace o tom, jak požádat o zvýšení těchto omezení.

> [!NOTE]
> Rozdíly v podporovaných funkcích a T-SQL příkazy naleznete v tématu [rozdíly ve funkcích](sql-database-features.md) a [podpora příkazu T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Omezení prostředků na úrovni instance

Managed instance má vlastnosti a omezení prostředků, které jsou závislé na základní infrastrukturu a architektury. Omezení závisí na úrovni hardwaru generování a služby.

### <a name="hardware-generation-characteristics"></a>Generování vlastností hardwaru

Spravované instance Azure SQL Database je možné nasadit na dvou generacemi hardwaru: Gen4 i Gen5. Generacemi hardwaru mají jiné charakteristiky, které jsou popsány v následující tabulce:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech připojené SSD vCore = 1 PP (fyzických jader) | Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorech rychlé NVMe SSD, vCore = 1 LP (hyper vlákno) |
| Virtuální jádra | 8, 16, 24 virtuálních jader | 8, 16, 24, 32, 40, 64, 80 virtuálních jader |
| Memory (Paměť) | 7 GB na vCore | 5.1 GB na vCore |
| OLTP v paměti maximální paměti | 3 GB na vCore | 2.6 GB na vCore |
| Maximální místo v úložišti instancí (Obecné) |  8 TB | 8 TB |
| Maximální velikost úložiště instance (pro důležité obchodní informace) | 1 TB | 1 TB, 2 TB nebo 4 TB, v závislosti na počtu jader |

### <a name="service-tier-characteristics"></a>Vlastnosti úrovně služeb

Managed instance má dvě úrovně služeb - obecné účely a pro důležité obchodní informace. Tyto úrovně teď poskytují různé možnosti, jak je popsáno v následující tabulce:

| **Funkce** | **Obecné účely** | **Pro důležité obchodní informace** |
| --- | --- | --- |
| Počet virtuálních jader\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Memory (Paměť) | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB - 408 GB (5.1 GB/vCore) | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB - 408 GB (5.1 GB/vCore) |
| Maximální velikost úložiště instancí | 8 TB | Gen4: 1 TB <br/> Gen5: <br/>-1 TB pro 8, 16 virtuálních jader<br/>-2 TB pro 24 virtuálních jader<br/>-4 TB pro 32, 40, 64, 80 virtuálních jader |
| Max. úložiště na databázi | Určuje maximální velikost úložiště na instanci | Určuje maximální velikost úložiště na instanci |
| Maximální počet databází na instanci | 100 | 100 |
| Maximální počet databází na instanci | Až 280 | 32 767 počet souborů v databázi |
| Data/Log IOPS (přibližné) | 500 – 7 500 na soubor<br/>\*[Závisí na velikosti souboru](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 110 tis. (1375/vCore) |
| Propustnost protokolu | 3 MB/s na vCore<br/>Maximální počet 22 MB/s na instanci | 4 MB/s na vCore<br/>Maximální počet 48 MB/s na instanci|
| Propustnost dat (přibližné) | 100 – 250 MB/s na souboru<br/>\*[Závisí na velikosti souboru](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | |
| Vstupně-výstupní latence (přibližné) | 5-10 ms | 1-2 ms |
| Maximální velikost tempDB | 192 - 1,920 GB (24 GB na vCore) | Bez omezení – limitován velikostí úložiště maximální počet instancí |
| Maximální počet relací | 30000 | 30000 |

**Poznámky k**:

- Velikost souboru protokolu a data uživatelů a systémové databáze jsou součástí úložiště velikost instance, která je ve srovnání s maximální limit velikosti úložiště. Použití <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> zobrazení systému k určení součet využitého místa databáze. Protokoly chyb není trvalý a není součástí velikost. Velikost úložiště nejsou součástí zálohy.
- Propustnost a vstupně-výstupních operací také závisí na velikosti stránky, který není omezený explicitně spravované instance.

## <a name="supported-regions"></a>Podporované oblasti

Spravovaná instance je možné vytvořit pouze v [podporované oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Chcete-li vytvořit spravovanou instanci v oblasti, která se aktuálně nepodporuje, můžete [odeslat žádost o podporu prostřednictvím webu Azure portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Typy podporované předplatného

Nasazení spravované instance aktuálně podporuje jenom pro následující typy předplatných:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Poskytovatele cloudových služeb (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> Toto omezení je dočasný. Nové typy předplatného se povolí v budoucnu.

## <a name="regional-resource-limitations"></a>Omezení místních prostředků

Typy podporované předplatného může obsahovat omezený počet prostředků v jedné oblasti. Managed instance má dvě výchozí omezení každou oblast Azure, v závislosti na typu typ předplatného:

- **Limitu podsítí**: Maximální počet podsítí, ve které jsou spravované instance nasazené v jedné oblasti.
- **Omezení počtu instancí**: Maximální počet instancí, které mohou být nasazeny v jedné oblasti.

> [!Note]
> Tato omezení jsou výchozí nastavení a ne technická omezení. Omezení může být vyšší podle potřeby tak, že vytvoříte speciální [žádost o podporu na webu Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) Pokud potřebujete více spravovaných instancí v aktuální oblasti. Jako alternativu můžete vytvořit nové spravované instance v jiné oblasti Azure bez odeslání žádosti o podporu.

Následující tabulka ukazuje výchozí místní omezení pro podporované předplatná:

|Typ odběru| Maximální počet podsítí spravované instance | Maximální počet instancí |Maximální počet GP managed instance *|Maximální počet BC managed instance *|
| :---| :--- | :--- |:--- |:--- |
|Průběžné platby|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|Vývoj/testování s průběžnými platbami|1*|4*|4*|1*|
|Enterprise pro vývoj/testování|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* 1 BC nebo 4 GP instance v jedné podsíti můžete nasadit buď tak, aby celkový počet jednotek"instance" v podsíti nikdy nepřesáhne 4.

** Maximální počet instancí v rámci jedné úrovně služby platí v případě, že neexistují žádné instance v rámci jiné úrovně služby. Pokud budete chtít míchat zásady skupiny a BC instance ve stejné podsíti, použijte jako referenci pro povolené kombinace v následující části. Jednoduché pravidlo celkový počet podsítí nemůže být delší než 3 a celkový počet jednotek instance nemůže být delší než 12.


> [!IMPORTANT]
> Při plánování vašeho nasazení, vezměte v úvahu, že instance kritické obchodní (BC) (z důvodu přidání redundance) obecně využívá 4 x větší kapacitu než instance obecné účely (zásady skupiny). Ano, pro výpočty, 1 GP instance = 1 instance jednotky a 1 BC instance = 4 jednotky instance. Pro zjednodušení analýzy využití pro výchozí omezení, Shrňte instance jednotky ve všech podsítí v oblasti, kde spravované instance se nasazují a porovnávat výsledky s limity instancí jednotky pro typ vašeho předplatného.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Strategie pro nasazení smíšeném instance pro obecné účely a pro důležité obchodní informace

[Smlouvy Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) předplatné může mít kombinací instancí GP a BC. Existují však některá omezení týkající se umístění instance v podsítí.

> [!Note]
> [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) a [Cloud Service Provider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) typy předplatného může mít buď jeden pro důležité obchodní informace nebo až na 4 instance pro obecné účely.

Následující příklady pokrývají nasazené služby se neprázdný podsítěmi a smíšené GP a BC úrovně služeb.

|Počet podsítí|podsíť 1|Podsíť 2|Podsíť 3|
|:---|:---|:---|:---|
|1|1 BC až 8 GP<br>2 BC až 4 GP|neuvedeno| neuvedeno|
|2|BC 0, až 4 GP|BC 1, až 4 GP<br>2 BC, 0 GP|neuvedeno|
|2|1 BC, 0 GP|BC 0, až na 8 GP<br>BC 1, až 4 GP|neuvedeno|
|2|2 BC, 0 GP|BC 0, až 4 GP|neuvedeno|
|3|1 BC, 0 GP|1 BC, 0 GP|BC 0, až 4 GP|
|3|1 BC, 0 GP|BC 0, až 4 GP|BC 0, až 4 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Získání vyšší kvóty pro SQL spravovaná instance

Pokud potřebujete více spravovaných instancí ve vaší aktuální oblastí, odešlete žádost o podporu pro rozšíření kvóty pomocí webu Azure portal.
Chcete-li zahájit proces získávání vyšší kvóty:

1. Otevřít **Nápověda a podpora**a klikněte na tlačítko **nová žádost o podporu**.

   ![Nápověda a podpora](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na kartě základy pro novou žádost o podporu:
   - Pro **typ problému**vyberte **limity služby a předplatného (kvóty)** .
   - V části **Předplatné** vyberte své předplatné.
   - Pro **typ kvóty**vyberte **SQL Database Managed Instance**.
   - Pro **plán podpory**, vyberte plán podpory.

     ![Problém typ kvóty](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klikněte na **Další**.
4. Na kartě problém pro novou žádost o podporu:
   - Pro **závažnost**, vyberte úroveň závažnosti problému.
   - Pro **podrobnosti**, poskytují další informace o svém problému, včetně chybových zpráv.
   - Pro **nahrání souboru**, připojte soubor s dalšími informacemi (až 4 MB).

     ![Podrobnosti o problému](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Neplatný požadavek by měl obsahovat:
     > - Oblasti, které předplatné limit musí být zvýšena
     > - Požadovaný počet instancí na úroveň služby v existující podsítě po kvótu zvýšit (je-li žádné existující podsítě potřeba rozšířit
     > - Požadované číslo nové podsítě a celkový počet instancí na úrovni služby v rámci nové podsítě (Pokud je potřeba nasadit spravované instance v nové podsítě).

5. Klikněte na **Další**.
6. Na kartě kontaktní informace pro novou žádost o podporu zadejte upřednostňovaný způsob kontaktu (e-mail nebo telefon) a kontaktní údaje.
7. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

- Další informace o spravované instance najdete v tématu [co je managed instance?](sql-database-managed-instance.md).
- Informace o cenách najdete v tématu [SQL Database managed instance ceny](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Zjistěte, jak vytvořit první spravované instanci, najdete v článku [úvodní příručky](sql-database-managed-instance-get-started.md).