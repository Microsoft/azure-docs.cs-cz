---
title: Import a export dat v Azure cache pro Redis
description: Naučte se importovat a exportovat data do a z úložiště objektů BLOB s využitím služby Premium Azure cache pro instance Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327504"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Import a export dat v Azure cache pro Redis
Import/Export je mezipaměť Azure pro operaci správy dat Redis, která umožňuje importovat data do mezipaměti Azure pro Redis nebo exportovat data z mezipaměti Azure pro Redis tím, že importuje a exportuje snímek Azure cache for Redis Database (RDB) z mezipaměti Premium do objektu BLOB v účtu Azure Storage.

- **Export** – mezipaměť Azure můžete exportovat pro REDIS snímky RDB do objektu blob stránky.
- **Import** – mezipaměť Azure pro REDIS snímky RDB můžete importovat buď z objektu blob stránky, nebo z objektu blob bloku.

Import/export umožňuje migrovat mezi různými Azure cache pro instance Redis nebo naplnit mezipaměť daty před použitím.

Tento článek obsahuje průvodce pro import a export dat pomocí Azure cache pro Redis a poskytuje odpovědi na nejčastější dotazy.

> [!IMPORTANT]
> Import/Export je k dispozici jenom pro mezipaměti [úrovně Premium](cache-overview.md#service-tiers) .
>
>

## <a name="import"></a>Import
Import se dá použít k převedení Redis kompatibilních souborů RDB z libovolného serveru Redis spuštěného v jakémkoli cloudu nebo prostředí, včetně Redis, které běží na Linux, Windows nebo jakémkoli poskytovateli cloudu, jako je Amazon Web Services a další. Import dat představuje snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu načte služba Azure cache pro Redis soubory RDB z Azure Storage do paměti a pak tyto klíče vloží do mezipaměti.

> [!NOTE]
> Před zahájením operace importu se ujistěte, že se soubor nebo soubory databáze Redis (RDB) nahrály do stránek nebo objektů blob bloku v Azure Storage ve stejné oblasti a předplatném jako instance Azure cache pro Redis. Další informace najdete v tématu [Začínáme se službou Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md). Pokud jste soubor RDB exportovali pomocí funkce [exportu Azure cache for Redis](#export) , váš soubor RDB je už uložený v objektu blob stránky a připravený k importu.
>
>

1. Pokud chcete importovat jeden nebo víc objektů BLOB mezipaměti, [přejděte do mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) v Azure Portal a klikněte na **importovat data** z **nabídky prostředek**.

    ![Import dat](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klikněte na **zvolit objekty blob** a vyberte účet úložiště, který obsahuje data, která chcete importovat.

    ![Zvolit účet úložiště](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klikněte na kontejner obsahující data, která chcete importovat.

    ![Zvolit kontejner](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Vyberte jeden nebo více objektů BLOB pro import kliknutím na oblast nalevo od názvu objektu BLOB a potom klikněte na **Vybrat**.

    ![Zvolit objekty blob](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Kliknutím na **importovat** zahajte proces importu.

   > [!IMPORTANT]
   > Mezipaměť není přístupná pro klienty mezipaměti během procesu importu a veškerá stávající data v mezipaměti budou odstraněna.
   >
   >

    ![Import](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Průběh operace importu můžete monitorovat pomocí oznámení z Azure Portal nebo zobrazením událostí v [protokolu auditu](../azure-resource-manager/management/view-activity-logs.md).

    ![Průběh importu](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Export
Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redis. Tuto funkci můžete použít k přesunu dat z jedné instance Azure cache pro instanci Redis do jiného nebo na jiný server Redis. Během procesu exportu se na virtuálním počítači, který hostuje instanci serveru Azure cache pro Redis, vytvoří dočasný soubor a soubor se nahraje do určeného účtu úložiště. Když se operace exportu dokončí buď se stavem úspěch, nebo neúspěchem, dočasný soubor se odstraní.

1. Pokud chcete exportovat aktuální obsah mezipaměti do úložiště, přejděte do [mezipaměti](cache-configure.md#configure-azure-cache-for-redis-settings) v Azure Portal a klikněte na **exportovat data** z **nabídky prostředků**.

    ![V navigačním podokně pro contoso5premium se zvýrazní možnost exportovat data v seznamu Správa.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klikněte na **zvolit kontejner úložiště** a vyberte požadovaný účet úložiště. Účet úložiště musí být ve stejném předplatném a oblasti jako vaše mezipaměť.

   > [!IMPORTANT]
   > Export funguje s objekty blob stránky, které jsou podporované účty úložiště Classic i Správce prostředků, ale v tuto chvíli nejsou podporované účty úložiště BLOB. Další informace najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).
   >

    ![Účet úložiště](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Zvolte požadovaný kontejner objektů BLOB a klikněte na **Vybrat**. Chcete-li použít nový kontejner, klikněte na tlačítko **Přidat kontejner** a nejprve ho přidejte a vyberte ze seznamu.

    ![V kontejnerech pro contoso55 je možnost + kontejner zvýrazněna. V seznamu je jeden kontejner, cachesaves a je vybraný a zvýrazněný. Možnost výběru je vybrána a zvýrazněna.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Zadejte **předponu názvu objektu BLOB** a kliknutím na **Export** spusťte proces exportu. Předpona názvu objektu BLOB se používá k vytvoření předpony názvů souborů generovaných touto operací exportu.

    ![Export](./media/cache-how-to-import-export-data/cache-export-data.png)

    Průběh operace exportu můžete sledovat pomocí oznámení z Azure Portal nebo zobrazením událostí v [protokolu auditu](../azure-resource-manager/management/view-activity-logs.md).

    ![Export dat dokončen](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Mezipaměti zůstávají k dispozici pro použití během procesu exportu.

## <a name="importexport-faq"></a>Nejčastější dotazy k importu a exportu
Tato část obsahuje nejčastější dotazy týkající se funkce Import/export.

* [Jaké cenové úrovně můžou používat import/export?](#what-pricing-tiers-can-use-importexport)
* [Můžu importovat data z libovolného serveru Redis?](#can-i-import-data-from-any-redis-server)
* [Jaké verze RDB mohu importovat?](#what-rdb-versions-can-i-import)
* [Je moje mezipaměť dostupná během operace importu/exportu?](#is-my-cache-available-during-an-importexport-operation)
* [Můžu použít import/export s clusterem Redis?](#can-i-use-importexport-with-redis-cluster)
* [Jak funguje import/export s nastavením vlastních databází?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Jak se import/export liší od trvalosti Redis?](#how-is-importexport-different-from-redis-persistence)
* [Je možné automatizovat import/export pomocí PowerShellu, rozhraní příkazového řádku nebo jiných klientů pro správu?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Během operace import/export se zobrazila chyba časového limitu. Co to znamená?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Při exportu dat do Azure Blob Storage se stala chyba. Co se přihodilo?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Jaké cenové úrovně můžou používat import/export?
Import/Export je dostupný jenom v cenové úrovni Premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Můžu importovat data z libovolného serveru Redis?
Ano, kromě importování dat exportovaných z mezipaměti Azure pro instance Redis můžete importovat soubory RDB z libovolného serveru Redis spuštěného v jakémkoli cloudu nebo prostředí, jako jsou například Linux, Windows nebo poskytovatelé cloudu, jako je například Amazon Web Services. Provedete to tak, že nahrajete soubor RDB z požadovaného serveru Redis na stránku nebo objekt blob bloku v účtu Azure Storage a pak ho naimportujete do mezipaměti Premium Azure pro instanci Redis. Například můžete chtít exportovat data z produkční mezipaměti a importovat je do mezipaměti používané jako součást přípravného prostředí pro účely testování nebo migrace.

> [!IMPORTANT]
> Aby bylo možné úspěšně importovat data exportovaná ze serverů Redis než Azure cache pro Redis při použití objektu blob stránky, musí být velikost objektu blob stránky zarovnána na hranici 512 bajtů. Vzorový kód pro provedení všech vyžadovaných odsazení v bajtech najdete v tématu věnovaném [nahrání objektu BLOB ukázkové stránky](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Jaké verze RDB mohu importovat?

Azure cache pro Redis podporuje import RDB pomocí sady RDB verze 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Je moje mezipaměť dostupná během operace importu/exportu?
* **Export** – mezipaměti zůstanou dostupné a během operace exportu můžete tuto mezipaměť dál používat.
* **Import** – mezipaměti budou nedostupné, když se spustí operace importu, a po dokončení operace importu budou k dispozici pro použití.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Můžu použít import/export s clusterem Redis?
Ano, a můžete importovat/exportovat mezi Clusterovou mezipamětí a neclusterovanou mezipamětí. Vzhledem k tomu, že cluster Redis [podporuje pouze databázi 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), nejsou importovaná žádná data v jiných databázích než 0. Po importu dat do mezipaměti clusteru se klíče přerozdělí mezi horizontálních oddílů clusteru.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Jak funguje import/export s nastavením vlastních databází?
Některé cenové úrovně mají odlišná [omezení databází](cache-configure.md#databases), takže při importu je potřeba vzít v paměti, pokud jste při vytváření mezipaměti nakonfigurovali vlastní hodnotu pro `databases` nastavení.

* Při importu do cenové úrovně s nižším `databases` limitem, než je vrstva, ze které jste exportovali:
  * Pokud používáte výchozí počet `databases` , který je pro všechny cenové úrovně 16, neztratí se žádná data.
  * Pokud používáte vlastní počet `databases` , který spadá do omezení pro vrstvu, do které importujete, neztratí se žádná data.
  * Pokud vaše exportovaná data obsahují data v databázi, která překračuje limity nové úrovně, data z těchto vyšších databází se neimportují.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Jak se import/export liší od trvalosti Redis?
Mezipaměť Azure pro trvalost Redis umožňuje zachovat data uložená v Redis pro Azure Storage. Při konfiguraci trvalosti Azure cache for Redis uchovává snímek mezipaměti Azure pro Redis v binárním formátu Redis na disk na základě konfigurovatelné četnosti zálohování. Pokud dojde k závažné události, která zakáže primární i mezipaměť repliky, data mezipaměti se automaticky obnoví pomocí nejnovějšího snímku. Další informace najdete v tématu [jak nakonfigurovat Trvalost dat pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-persistence.md).

Import/export umožňuje přenést data do služby Azure cache pro Redis nebo je z ní exportovat. Nekonfiguruje zálohování a obnovování pomocí Redis Persistence.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Je možné automatizovat import/export pomocí PowerShellu, rozhraní příkazového řádku nebo jiných klientů pro správu?
Ano, pokyny pro PowerShell najdete v tématu [Import mezipaměti Azure pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) a [Export mezipaměti Azure pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Během operace import/export se zobrazila chyba časového limitu. Co to znamená?
Pokud zůstanete v okně **importovat data** nebo **exportovat data** déle než 15 minut před zahájením operace, zobrazí se chybová zpráva podobná následujícímu příkladu:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Chcete-li tento problém vyřešit, spusťte operaci importu nebo exportu ještě před 15 minutami.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Při exportu dat do Azure Blob Storage se stala chyba. Co se stalo?
Export funguje pouze s soubory RDB uloženými jako objekty blob stránky. Jiné typy objektů BLOB se momentálně nepodporují, včetně účtů BLOB Storage s horkou a studenou úrovní. Další informace najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis](cache-overview.md#service-tiers)