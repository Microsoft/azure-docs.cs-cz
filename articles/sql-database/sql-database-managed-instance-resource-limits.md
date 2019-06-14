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
ms.date: 06/13/2019
ms.openlocfilehash: 15f64c7087ea4d24f271af67b251030a2196fa10
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070378"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Přehled Azure SQL Database managed instance omezení prostředků

Tento článek obsahuje základní informace o omezení prostředků pro spravovanou instanci Azure SQL Database a poskytuje informace o tom, jak požádat o zvýšení těchto omezení.

> [!NOTE]
> Rozdíly v podporovaných funkcích a T-SQL příkazy naleznete v tématu [rozdíly ve funkcích](sql-database-features.md) a [podpora příkazu T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Omezení prostředků na úrovni instance

Managed instance má vlastnosti a omezení prostředků, které jsou závislé na základní infrastrukturu a architektury. Omezení závisí na úrovni hardwaru generování a služby.

### <a name="hardware-generation-characteristics"></a>Generování vlastností hardwaru

Spravované instance Azure SQL Database je možné nasadit na dvou generacemi hardwaru: Gen4 i Gen5. Generacemi hardwaru mají jiné charakteristiky, jak je popsáno v následující tabulce:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech připojené SSD vCore = 1 PP (fyzických jader) | Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorech rychlé NVMe SSD, vCore = 1 LP (hyper vlákno) |
| Virtuální jádra | 8, 16, 24 virtuálních jader | 4, 8, 16, 24, 32, 40, 64, 80 virtuálních jader |
| Paměť (poměr paměti a jader) | 7 GB na vCore | 5.1 GB na vCore |
| OLTP v paměti maximální paměti | Instance omezení: 3 GB na vCore<br/>Omezení databáze:<br/> -8 jádry: 8 GB na databázi<br/> -16 jádry: 20 GB na databázi<br/> -24jádra: 36 GB na databázi | Instance omezení: 2,5 GB na vCore<br/>Omezení databáze:<br/> -8 jádry: 13 GB na databázi<br/> -16 jádry: 32 GB na databázi |
| Maximální místo v úložišti instancí (Obecné) |  8 TB | 8 TB |
| Maximální velikost úložiště instance (pro důležité obchodní informace) | 1 TB | 1 TB, 2 TB nebo 4 TB, v závislosti na počtu jader |

### <a name="service-tier-characteristics"></a>Vlastnosti úrovně služeb

Managed instance má dvě úrovně služby: Obecné účely a pro důležité obchodní informace. Tyto úrovně teď poskytují různé možnosti, jak je popsáno v následující tabulce:

| **Funkce** | **Obecné účely** | **Pro důležité obchodní informace** |
| --- | --- | --- |
| Počet virtuálních jader\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 |
| Memory (Paměť) | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB - 408 GB (5.1 GB/vCore) | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB - 408 GB (5.1 GB/vCore) |
| Maximální velikost úložiště instancí | -2 TB pro 4 virtuální jádra (pouze Gen5)<br/>-8 TB pro ostatní velikosti | Gen4: 1 TB <br/> Gen5: <br/>-1 TB pro 4, 8, 16 virtuálních jader<br/>-2 TB pro 24 virtuálních jader<br/>-4 TB pro 32, 40, 64, 80 virtuálních jader |
| Max. úložiště na databázi | Určuje maximální velikost úložiště na instanci | Určuje maximální velikost úložiště na instanci |
| Maximální počet databází na instanci | 100 | 100 |
| Maximální počet databází na instanci | Až 280 | 32 767 počet souborů v databázi |
| Data/Log IOPS (přibližné) | 500 – 7 500 na soubor<br/>\*[Závisí na velikosti souboru](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 110 tis. (1375/vCore) |
| Propustnost protokolu | 3 MB/s na vCore<br/>Maximální počet 22 MB/s na instanci | 4 MB/s na vCore<br/>Maximální počet 48 MB/s na instanci|
| Propustnost dat (přibližné) | 100 – 250 MB/s na souboru<br/>\*[Závisí na velikosti souboru](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | neuvedeno |
| Vstupně-výstupní latence (přibližné) | 5-10 ms | 1-2 ms |
| Maximální velikost tempDB | 192 - 1,920 GB (24 GB na vCore) | Bez omezení – limitován velikostí úložiště maximální počet instancí |
| Maximální počet relací | 30000 | 30000 |

> [!NOTE]
> - Velikost souboru protokolu a data uživatelů a systémové databáze jsou součástí úložiště velikost instance, která je ve srovnání s maximální limit velikosti úložiště. Použití <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> zobrazení systému k určení součet využitého místa databáze. Protokoly chyb není trvalý a není součástí velikost. Velikost úložiště nejsou součástí zálohy.
> - Propustnost a vstupně-výstupních operací také závisí na velikosti stránky, který není omezený explicitně spravované instance.

## <a name="supported-regions"></a>Podporované oblasti

Spravovaná instance je možné vytvořit pouze v [podporované oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Chcete-li vytvořit spravovanou instanci v oblasti, která se aktuálně nepodporuje, můžete [odeslat žádost o podporu prostřednictvím webu Azure portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Typy podporované předplatného

Nasazení spravované instance aktuálně podporuje jenom pro následující typy předplatných:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Poskytovatele cloudových služeb (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Předplatná s měsíční kredit Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Omezení místních prostředků

Typy podporované předplatného může obsahovat omezený počet prostředků v jedné oblasti. Managed instance má dvě výchozí omezení každou oblast Azure, v závislosti na typu typ předplatného:

- **Limitu podsítí**: Maximální počet podsítí, ve které jsou spravované instance nasazené v jedné oblasti.
- **vCore limit**: Maximální počet virtuálních jader, které je možné nasadit napříč všemi instancemi v jedné oblasti.

> [!Note]
> Tato omezení jsou výchozí nastavení a ne technická omezení. Omezení může být vyšší podle potřeby tak, že vytvoříte speciální [žádost o podporu na webu Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) Pokud potřebujete více spravovaných instancí v aktuální oblasti. Jako alternativu můžete vytvořit nové spravované instance v jiné oblasti Azure bez odeslání žádosti o podporu.

Následující tabulka ukazuje výchozí místní omezení pro podporované předplatná:

|Typ odběru| Maximální počet podsítí spravované instance | Maximální počet jednotek vCore * |
| :---| :--- | :--- |
|Průběžné platby|3|320|
|CSP |8 (15 v některých oblastech **)|960 (v některých oblastech ** 1440)|
|Vývoj/testování s průběžnými platbami|3|320|
|Enterprise pro vývoj/testování|3|320|
|EA|8 (15 v některých oblastech **)|960 (v některých oblastech ** 1440)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional a MSDN Platforms|2|32|

\* Při plánování vašeho nasazení, vezměte v úvahu, že kritické obchodní (BC) virtuální jádro (z důvodu přidání redundance) využívá 4 x větší kapacitu než virtuální jádro pro obecné účely (zásady skupiny). Ano, pro výpočty, 1 virtuální jádro GP = 1 vCore jednotky a 1 BC virtuální jádro = 4 jednotky na vCore. Zjednodušuje analýzy využití pro výchozí omezení, Shrňte jednotky vCore přes všechny podsítě v oblasti, kde spravované instance se nasazují a porovnávat výsledky s limity instancí jednotky pro typ vašeho předplatného. **Maximální počet jednotek vCore** omezení platí pro každé předplatné oblast. Neexistuje žádné omezení na jednotlivé podsítě s tím rozdílem, že součet všech virtuálních jader nasazených na více podsítí musí být menší nebo rovna hodnotě **maximální počet jednotek vCore**.

** Větší podsítě a vCore omezení jsou k dispozici v následujících oblastech: Austrálie – východ, USA – východ, USA – východ 2, Severní Evropa, střed USA – Jih, jihovýchodní Asie, Velká Británie – Jih, západní Evropa, západní USA 2.

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
4. Na **problém kartu** pro novou žádost o podporu:
   - Pro **závažnost**, vyberte úroveň závažnosti problému.
   - Pro **podrobnosti**, poskytují další informace o svém problému, včetně chybových zpráv.
   - Pro **nahrání souboru**, připojte soubor s dalšími informacemi (až 4 MB).

     ![Podrobnosti o problému](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Neplatný požadavek by měl obsahovat:
     > - Oblast, ve které předplatné limit musí být zvýšena.
     > - Požadovaný počet virtuálních jader na úrovni služby v existující podsítě po kvóty zvýšit (je-li žádné existující podsítě je potřeba rozbalit.
     > - Požadované číslo nové podsítě a celkový počet virtuálních jader na úrovni služby v rámci nové podsítě (Pokud je potřeba nasadit spravované instance v nové podsítě).

5. Klikněte na **Další**.
6. Na kartě kontaktní informace pro novou žádost o podporu zadejte upřednostňovaný způsob kontaktu (e-mail nebo telefon) a kontaktní údaje.
7. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

- Další informace o spravované instance najdete v tématu [co je managed instance?](sql-database-managed-instance.md).
- Informace o cenách najdete v tématu [SQL Database managed instance ceny](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Zjistěte, jak vytvořit první spravované instanci, najdete v článku [úvodní příručky](sql-database-managed-instance-get-started.md).
