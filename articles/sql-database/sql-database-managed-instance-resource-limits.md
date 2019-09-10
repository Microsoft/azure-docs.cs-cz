---
title: Azure SQL Database omezení prostředků – spravovaná instance | Microsoft Docs
description: Tento článek poskytuje přehled Azure SQL Databasech omezení prostředků pro spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 08/27/2019
ms.openlocfilehash: c0bfbbd8b85f0b3eadf468cdd1261f52bff26abe
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813381"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Přehled Azure SQL Database omezení prostředků spravované instance

Tento článek poskytuje přehled omezení prostředků Azure SQL Database spravované instance a poskytuje informace o tom, jak požádat o zvýšení těchto limitů.

> [!NOTE]
> Rozdíly v podporovaných funkcích a příkazech T-SQL najdete v tématu [rozdíly](sql-database-features.md) ve funkcích a [Podpora příkazů t-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Omezení prostředků na úrovni instance

Spravovaná instance má charakteristiky a omezení prostředků, které závisí na základní infrastruktuře a architektuře. Omezení závisí na generaci hardwaru a vrstvě služeb.

### <a name="hardware-generation-characteristics"></a>Charakteristiky generování hardwaru

Azure SQL Database spravovanou instanci lze nasadit na dvě generace hardwaru: COMPUTE GEN4 – a Gen5. Hardwarové generace mají různé charakteristiky, jak je popsáno v následující tabulce:

|   | **COMPUTE GEN4 –** | **Gen5** |
| --- | --- | --- |
| Hardware | Procesory Intel E5-2673 V3 (Haswell) 2,4 GHz, připojené SSD vCore = 1 PP (fyzický jádro) | Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz, Fast NVMe SSD, vCore = 1 LP (Hyper-thread) |
| Počet virtuální jádra | 8, 16, 24 virtuální jádra | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| Maximální velikost paměti (poměr paměti/jádra) | 7 GB na vCore<br/>Přidejte další virtuální jádra, abyste získali více paměti. | 5,1 GB na vCore<br/>Přidejte další virtuální jádra, abyste získali více paměti. |
| Maximální paměť OLTP v paměti | Limit instance: 3 GB na vCore<br/>Omezení databáze:<br/> – 8 – jádro: 8 GB na databázi<br/> – 16 jader: 20 GB na databázi<br/> -24 – jádro: 36 GB na databázi | Limit instance: 2,5 GB na vCore<br/>Omezení databáze:<br/> – 8 – jádro: 13 GB na databázi<br/> – 16 jader: 32 GB na databázi |
| Maximální rezervované úložiště instancí |  Pro obecné účely: 8 TB<br/>Pro důležité obchodní informace: 1TB | Pro obecné účely: 8 TB<br/> V závislosti na počtu jader Pro důležité obchodní informace 1 TB, 2 TB nebo 4 TB. |

> [!IMPORTANT]
> - Probíhá fáze COMPUTE GEN4 – hardwaru. Doporučuje se nasadit nové spravované instance na Gen5 hardware.
> - COMPUTE GEN4 – hardware je v tuto chvíli k dispozici v následujících oblastech: Severní Evropa, Západní Evropa, Východní USA, Střed USA – jih, Střed USA – sever, Západní USA 2, Střed USA, Kanada – střed, Jižní Indie, jihovýchodní Asie a Korea – střed.

### <a name="service-tier-characteristics"></a>Vlastnosti vrstvy služeb

Spravovaná instance má dvě úrovně služeb: Pro obecné účely a Pro důležité obchodní informace. Tyto úrovně poskytují různé možnosti, jak je popsáno v následující tabulce:

| **Funkce** | **Pro obecné účely** | **Pro důležité obchodní informace** |
| --- | --- | --- |
| Počet virtuální jádra\* | COMPUTE GEN4 – 8, 16, 24<br/>Gen5 4, 8, 16, 24, 32, 40, 64, 80 | COMPUTE GEN4 – 8, 16, 24 <br/> Gen5 4, 8, 16, 24, 32, 40, 64, 80 |
| Maximální velikost paměti | COMPUTE GEN4 – 56 GB až 168 GB (7GB/vCore)<br/>Gen5 40,8 GB až 408 GB (5.1 GB/vCore)<br/>Přidejte další virtuální jádra, abyste získali více paměti. | COMPUTE GEN4 – 56 GB až 168 GB (7GB/vCore)<br/>Gen5 40,8 GB až 408 GB (5.1 GB/vCore)<br/>Přidejte další virtuální jádra, abyste získali více paměti. |
| Maximální velikost rezervovaného úložiště instance | – 2 TB pro 4 virtuální jádra (jenom Gen5)<br/>– 8 TB pro jiné velikosti | COMPUTE GEN4 – 1 TB <br/> Gen5 <br/>-1 TB pro 4, 8, 16 virtuální jádra<br/>– 2 TB pro 24 virtuální jádra<br/>-4 TB pro 32, 40, 64, 80 virtuální jádra |
| Maximální velikost databáze | 8 TB | 4 TB |
| Maximální počet databází na instanci | 100 | 100 |
| Maximální počet souborů databáze na instanci | Až 280 | 32 767 souborů na databázi |
| Maximální velikost souboru | 8 TB | 4 TB |
| Data/protokol IOPS (přibližná) | 500 – 7 500 na jeden soubor<br/>\*[Zvětšením velikosti souboru získat více IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 k-110 K (1375/vCore)<br/>Přidáním dalších virtuální jádra získáte lepší výkon v/v. |
| Limit propustnosti zápisu protokolu | 3 MB/s na vCore<br/>Max. 22 MB/s na instanci | 4 MB/s na vCore<br/>Max. 48 MB/s na instanci|
| Propustnost dat (přibližná) | 100 – 250 MB/s na jeden soubor<br/>\*[Zvětšením velikosti souboru získáte lepší vstupně-výstupní operace.](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | Není k dispozici |
| Latence v/v úložiště (přibližná) | 5-10 ms | 1-2 ms |
| Maximální velikost databáze tempDB | 192 – 1 920 GB (24 GB na vCore)<br/>Přidejte další virtuální jádra, abyste získali více místa v databázi TempDB. | Omezeno velikostí úložiště maximální instance. Velikost souboru protokolu TempDB je aktuálně omezená na 24GB/vCore. |
| OLTP v paměti | Nepodporuje se | K dispozici |
| Maximální počet relací | 30000 | 30000 |
| Čitelné repliky | 0 | 1 (zahrnuto do ceny) |
| Ceny a fakturace | vCore, rezervované úložiště  <br/> Neúčtují se za IOPS, úložiště zálohování se ještě neúčtuje. | vCore, rezervované úložiště  <br/> Neúčtují se za IOPS, úložiště zálohování se ještě neúčtuje. | 
| Modely slev | [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Zvýhodněné hybridní využití Azure](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (není k dispozici pro odběry pro vývoj a testování) | [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Zvýhodněné hybridní využití Azure](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (není k dispozici pro odběry pro vývoj a testování)|

> [!NOTE]
> - Velikost dat a souborů protokolu v uživatelských i systémových databázích jsou zahrnuté do velikosti úložiště instance, která je porovnávána s limitem maximální velikosti úložiště. Pomocí systémového zobrazení <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">Sys. master_files</a> určete celkové využité místo podle databází. Protokoly chyb nejsou trvale uložené a nejsou zahrnuté do velikosti. Zálohy nejsou zahrnuté do velikosti úložiště.
> - Propustnost a IOPS závisí také na velikosti stránky, která není explicitně omezená pomocí spravované instance.
> Pomocí skupin s automatickým převzetím služeb při selhání můžete vytvořit další čitelnou repliku v jiné oblasti Azure.

## <a name="supported-regions"></a>Podporované oblasti

Spravované instance lze vytvořit pouze v [podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud chcete vytvořit spravovanou instanci v oblasti, která není aktuálně podporovaná, můžete [Odeslat žádost o podporu prostřednictvím Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Podporované typy předplatného

Spravovaná instance aktuálně podporuje nasazení pouze u následujících typů předplatných:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Poskytovatel cloudových služeb (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Předplatné s měsíčním kreditem Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Omezení regionálních prostředků

Podporované typy předplatného můžou obsahovat omezený počet prostředků na oblast. Spravovaná instance má dvě výchozí omezení pro každou oblast Azure v závislosti na typu typu předplatného:

- **Omezení podsítě**: Maximální počet podsítí, ve kterých jsou spravované instance nasazeny v jedné oblasti.
- **limit Vcore**: Maximální počet virtuální jádra, které se dají nasadit napříč všemi instancemi v jedné oblasti.

> [!Note]
> Tato omezení představují výchozí nastavení a nejedná se o technická omezení. Omezení se dají zvýšit na vyžádání vytvořením speciální [žádosti o podporu v Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) , pokud v aktuální oblasti potřebujete víc spravovaných instancí. Jako alternativu můžete vytvořit nové spravované instance v jiné oblasti Azure bez nutnosti odesílat žádosti o podporu.

Následující tabulka ukazuje **výchozí regionální omezení** pro podporované typy předplatného (pomocí žádosti o podporu popsanou níže můžete rozšířit výchozí omezení):

|Typ odběru| Maximální počet podsítí spravované instance | Maximální počet jednotek vCore * |
| :---| :--- | :--- |
|Průběžné platby|3|320|
|CSP |8 (15 v některých oblastech * *)|960 (1440 v některých oblastech * *)|
|Průběžné platby dle aktuálního využití pro vývoj/testování|3|320|
|Enterprise pro vývoj/testování|3|320|
|EA|8 (15 v některých oblastech * *)|960 (1440 v některých oblastech * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional a MSDN Platforms|2|32|

\*Při plánování nasazení Vezměte v úvahu, že úroveň služby Pro důležité obchodní informace (BC) vyžaduje čtyři (4) větší kapacitu vCore než úroveň služby Pro obecné účely (GP). Příklad: 1 GP vCore = 1 vCore jednotka a 1 BC vCore = 4 jednotky vCore. Abyste zjednodušili analýzu spotřeby proti výchozím omezením, Shrňte jednotky vCore ve všech podsítích v oblasti, ve které se nasazují spravované instance, a porovnejte výsledky s omezeními jednotky instance pro typ předplatného. **Maximální počet Vcore jednotek** se vztahuje na každé předplatné v oblasti. Pro jednotlivé podsítě neplatí žádné omezení s tím rozdílem, že součet všech virtuální jádra nasazených napříč více podsítěmi musí být nižší nebo roven **maximálnímu počtu Vcore jednotek**.

\*\*Větší podsíť a omezení vCore jsou k dispozici v následujících oblastech: Austrálie – východ, Východní USA, Východní USA 2, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Západní Evropa, Západní USA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Získání větší kvóty pro spravovanou instanci SQL

Pokud ve svých současných oblastech potřebujete víc spravovaných instancí, pošlete žádost o podporu, která kvótu rozšiřuje, pomocí Azure Portal.
Postup pro zahájení procesu získání větší kvóty:

1. Otevřete **help + podpora**a klikněte na **Nová žádost o podporu**.

   ![Nápověda a podpora](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na kartě základy nové žádosti o podporu:
   - Jako **typ problému**vyberte **omezení služby a předplatné (kvóty)** .
   - V části **Předplatné** vyberte své předplatné.
   - Jako **typ kvóty**vyberte **SQL Database spravovaná instance**.
   - V případě **plánu podpory**vyberte svůj plán podpory.

     ![Kvóta typu problému](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klikněte na **Další**.
4. Na **kartě problém** u nové žádosti o podporu:
   - V části **závažnost**vyberte úroveň závažnosti problému.
   - **Podrobnosti**získáte zadáním dalších informací o vašem problému, včetně chybových zpráv.
   - Pro **nahrání souboru**připojte soubor s více informacemi (až 4 MB).

     ![Podrobnosti o problému](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Platná žádost by měla zahrnovat:
     > - Oblast, ve které je potřeba zvýšit limit předplatného
     > - Požadovaný počet virtuální jádra, na úroveň služby v existujících podsítích po zvýšení kvóty (Pokud je nutné rozšířit libovolnou existující podsíť.
     > - Požadovaný počet nových podsítí a celkový počet virtuální jádra na úroveň služby v rámci nových podsítí (Pokud potřebujete nasadit spravované instance v nových podsítích).

5. Klikněte na **Další**.
6. Na kartě kontaktní informace u nové žádosti o podporu zadejte upřednostňovanou metodu kontaktu (e-mail nebo telefon) a kontaktní údaje.
7. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

- Další informace o spravované instanci najdete v tématu [co je spravovaná instance?](sql-database-managed-instance.md).
- Informace o cenách najdete v tématu [SQL Database ceny za Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Informace o tom, jak vytvořit první spravovanou instanci, najdete v [příručce pro rychlý Start](sql-database-managed-instance-get-started.md).
