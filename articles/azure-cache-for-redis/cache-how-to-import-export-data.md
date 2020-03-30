---
title: Import a export dat v azure mezipaměti pro Redis
description: Zjistěte, jak importovat a exportovat data do úložiště objektů blob a z nich pomocí prémiových instancí Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278087"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Import a export dat v azure mezipaměti pro Redis
Import a export je operace správy dat Azure Cache for Redis, která umožňuje importovat data do mezipaměti Azure pro Redis nebo exportovat data z Azure Cache for Redis importováním a exportováním snímku databáze Azure Cache for Redis (RDB) z mezipaměti premium do objektu blob v účtu úložiště Azure.

- **Export** – můžete exportovat azure cache pro Redis RDB snímky do objektu blob stránky.
- **Import** – můžete importovat azure cache pro Redis RDB snímky z objektu blob stránky nebo bloku blob.

Import nebo export umožňuje migrovat mezi různými instancemi Azure Cache for Redis nebo naplnit mezipaměť daty před použitím.

Tento článek obsahuje průvodce pro import a export dat pomocí Azure Cache pro Redis a poskytuje odpovědi na běžně kladené otázky.

> [!IMPORTANT]
> Import a export je k dispozici pouze pro mezipaměti [úrovně premium.](cache-premium-tier-intro.md)
>
>

## <a name="import"></a>Import
Import lze použít k přenesení souborů RDB kompatibilních s redisem z libovolného serveru Redis spuštěného v jakémkoli cloudu nebo prostředí, včetně redisu běžícího na Linuxu, Windows nebo libovolného poskytovatele cloudu, jako jsou Amazon Web Services a další. Import dat je snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu Azure Cache for Redis načte soubory RDB z úložiště Azure do paměti a pak vloží klíče do mezipaměti.

> [!NOTE]
> Před zahájením operace importu se ujistěte, že se soubor nebo soubory databáze Redis (RDB) nahrají do objektů BLOB stránky nebo bloku v úložišti Azure, ve stejné oblasti a předplatné jako instance Azure Cache for Redis. Další informace najdete [v tématu Začínáme s úložištěm objektů blob Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Pokud jste exportovali soubor RDB pomocí funkce [Azure Cache for Redis Export,](#export) soubor RDB je už uložený v objektu blob stránky a je připravený k importu.
>
>

1. Pokud chcete importovat jeden nebo více exportovaných objektů BLOB mezipaměti, [přejděte do mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) na webu Azure Portal a klikněte na **Importovat data** z **nabídky Prostředky**.

    ![Import dat](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klikněte na **Vybrat objekty blob** a vyberte účet úložiště, který obsahuje data, která chcete importovat.

    ![Zvolte účet úložiště](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klikněte na kontejner, který obsahuje data, která chcete importovat.

    ![Zvolte kontejner](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Vyberte jeden nebo více objektů BLOB, které chcete importovat, kliknutím na oblast nalevo od názvu objektu blob a potom klepněte na **tlačítko Vybrat**.

    ![Výběr objektů BLOB](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Chcete-li zahájit proces importu, klepněte na tlačítko **Importovat.**

   > [!IMPORTANT]
   > Mezipaměť není přístupná klientům mezipaměti během procesu importu a všechna existující data v mezipaměti jsou odstraněna.
   >
   >

    ![Import](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Průběh operace importu můžete sledovat podle oznámení z portálu Azure nebo zobrazením událostí v [protokolu auditu](../azure-resource-manager/management/view-activity-logs.md).

    ![Průběh importu](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Export
Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redisem. Pomocí této funkce můžete přesunout data z jedné mezipaměti Azure pro instanci Redis na jiný nebo na jiný server Redis. Během procesu exportu se na virtuálním počítači, který je hostitelem instance serveru Azure Cache for Redis, vytvoří dočasný soubor a soubor se nahraje do určeného účtu úložiště. Po dokončení operace exportu se stavem úspěchu nebo neúspěchu je dočasný soubor odstraněn.

1. Pokud chcete exportovat aktuální obsah mezipaměti do úložiště, [přejděte do mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) na webu Azure Portal a klikněte na **Exportovat data** z **nabídky Prostředky**.

    ![Zvolte kontejner úložiště](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klikněte na **Zvolit kontejner úložiště** a vyberte požadovaný účet úložiště. Účet úložiště musí být ve stejném předplatném a oblasti jako vaše mezipaměť.

   > [!IMPORTANT]
   > Export funguje s objekty BLOB stránky, které jsou podporovány klasickými účty úložiště a účty správce prostředků, ale v současné době nejsou podporovány účty úložiště objektů Blob. Další informace najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).
   >

    ![Účet úložiště](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Zvolte požadovanou kontejner objektů blob a klepněte na **tlačítko Vybrat**. Pokud chcete použít nový kontejner, klikněte nejdřív na **Přidat kontejner** a pak ho vyberte ze seznamu.

    ![Zvolte kontejner úložiště](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Zadejte **předponu názvu objektu Blob** a klepnutím na **export** spusťte proces exportu. Předpona názvu objektu blob se používá k předponě názvů souborů generovaných touto operací exportu.

    ![Export](./media/cache-how-to-import-export-data/cache-export-data.png)

    Průběh operace exportu můžete sledovat podle oznámení z portálu Azure nebo zobrazením událostí v [protokolu auditu](../azure-resource-manager/management/view-activity-logs.md).

    ![Export dat dokončen](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Mezipaměti zůstávají k dispozici pro použití během procesu exportu.

## <a name="importexport-faq"></a>Nejčastější dotazy k importu a exportu
Tato část obsahuje nejčastější dotazy týkající se funkce Import/Export.

* [Jaké cenové úrovně mohou používat import nebo export?](#what-pricing-tiers-can-use-importexport)
* [Mohu importovat data z libovolného serveru Redis?](#can-i-import-data-from-any-redis-server)
* [Jaké verze RDB mohu importovat?](#what-rdb-versions-can-i-import)
* [Je moje mezipaměť dostupná během operace importu a exportu?](#is-my-cache-available-during-an-importexport-operation)
* [Mohu použít import nebo export s clusterem Redis?](#can-i-use-importexport-with-redis-cluster)
* [Jak funguje import nebo export s nastavením vlastních databází?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Jak se import/export liší od trvalosti Redisu?](#how-is-importexport-different-from-redis-persistence)
* [Můžu automatizovat import a export pomocí PowerShellu, CLI nebo jiných klientů pro správu?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Během operace importu a exportu se zobrazila chyba časového ochazu. Co to znamená?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Při exportu dat do úložiště objektů blob Azure došlo k chybě. Co se přihodilo?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Jaké cenové úrovně mohou používat import nebo export?
Import/export je k dispozici pouze v cenové úrovni premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Mohu importovat data z libovolného serveru Redis?
Ano, kromě importu dat exportovaných z Azure Cache for Redis instance, můžete importovat rdb soubory z libovolného serveru Redis spuštěného v libovolném cloudu nebo prostředí, jako je Linux, Windows nebo poskytovatelé cloudu, jako jsou Amazon Web Services. Chcete-li to provést, nahrajte soubor RDB z požadovaného serveru Redis na stránku nebo blokový objekt blob v účtu úložiště Azure a pak jej importujte do prémiové instance Azure Cache for Redis. Můžete například exportovat data z produkční mezipaměti a importovat je do mezipaměti používané jako součást pracovního prostředí pro testování nebo migraci.

> [!IMPORTANT]
> Chcete-li úspěšně importovat data exportovaná z jiných serverů Redis než Azure Cache for Redis při použití objektu blob stránky, musí být velikost objektu blob stránky zarovnána na hranici 512 bajtů. Ukázkový kód k provedení požadovaného odsazení bajtů naleznete v [tématu Ukázkové nahrání objektu blob stránky](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Jaké verze RDB mohu importovat?

Azure Cache for Redis podporuje import RDB až přes RDB verze 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Je moje mezipaměť dostupná během operace importu a exportu?
* **Export** - Mezipaměti zůstanou k dispozici a během operace exportu můžete dál používat mezipaměť.
* **Import** - Mezipaměti budou při spuštění operace importu nedostupné a budou k dispozici pro použití po dokončení operace importu.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Mohu použít import nebo export s clusterem Redis?
Ano a můžete importovat nebo exportovat mezi clusterovnou mezipamětí a neclusterovenou mezipamětí. Vzhledem k tomu, že cluster Redis [podporuje pouze databázi 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), nebudou importována žádná data v jiných databázích než 0. Při importu dat clusterované mezipaměti jsou klíče dále distribuovány mezi úlomky clusteru.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Jak funguje import nebo export s nastavením vlastních databází?
Některé cenové úrovně mají [různá omezení databází](cache-configure.md#databases), takže při importu jsou některé `databases` důležité informace, pokud jste nakonfigurovali vlastní hodnotu pro nastavení během vytváření mezipaměti.

* Při importu do cenové úrovně `databases` s nižším limitem, než je úroveň, ze které jste exportovali:
  * Pokud používáte výchozí číslo `databases`, což je 16 pro všechny cenové úrovně, dojde ke ztrátě žádných dat.
  * Pokud používáte vlastní počet, který spadá do limitů pro úroveň, do které importujete, dojde ke ztrátě žádných `databases` dat.
  * Pokud exportovaná data obsahovala data v databázi, která překračuje limity nové vrstvy, data z těchto vyšších databází se neimportují.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Jak se import/export liší od trvalosti Redisu?
Azure Cache for Redis trvalá umožňuje uchovávat data uložená v Redis u Azure Storage. Při nakonfigurované trvalosti Azure Cache for Redis zachová snímek azure cache pro Redis v binárním formátu Redis na disk na základě konfigurovatelné frekvence zálohování. Pokud dojde ke katastrofické události, která zakáže primární i replikovou mezipaměť, data mezipaměti se automaticky obnoví pomocí nejnovějšího snímku. Další informace naleznete v tématu [Jak nakonfigurovat trvalost dat pro premium Azure Cache pro Redis](cache-how-to-premium-persistence.md).

Import nebo export umožňuje přenést data do nebo exportovat z Azure Cache pro Redis. Nenakonfiguruje zálohování a obnovení pomocí trvalosti Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Můžu automatizovat import a export pomocí PowerShellu, CLI nebo jiných klientů pro správu?
Ano, pokyny k PowerShellu najdete [v tématu Import mezipaměti Azure pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) a [Exportazure cache pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Během operace importu a exportu se zobrazila chyba časového ochazu. Co to znamená?
Pokud zůstanete v okně **Import dat** nebo **Export dat** déle než 15 minut před zahájením operace, zobrazí se chyba s chybovou zprávou podobnou následujícímu příkladu:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Chcete-li tento problém vyřešit, zahajte operaci importu nebo exportu před uplynutím 15 minut.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Při exportu dat do úložiště objektů blob Azure došlo k chybě. Co se stalo?
Export funguje pouze se soubory RDB uloženými jako objekty BLOB stránky. Jiné typy objektů blob nejsou aktuálně podporované, včetně účtů úložiště objektů Blob s horkými a chladnými vrstvami. Další informace najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak používat více funkcí prémiové mezipaměti.

* [Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md)
