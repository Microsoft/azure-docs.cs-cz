---
title: Import a Export dat ve službě Azure Redis Cache | Microsoft Docs
description: Postup importu a exportu dat do a z úložiště objektů blob s vaší instancí Azure Redis Cache premium
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: wesmc
ms.openlocfilehash: db488f759752880a47a78dfeec13b14f65bd503c
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910083"
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Import a Export dat ve službě Azure Redis Cache
Import a Export je operace Azure Redis Cache dat správy, který umožňuje importovat data do Azure Redis Cache nebo exportovat data z Azure Redis Cache pomocí import a Export snímku databáze Redis Cache (RDB) z mezipaměti premium do objektu blob v Azure Účet úložiště. 

- **Export** -vaše Azure Redis Cache RDB snímky můžete exportovat do objektů Blob stránky.
- **Import** -vaší RDB mezipaměti Redis snímky můžete importovat z objekt Blob stránky nebo objekt Blob bloku.

Import a Export umožňuje migraci mezi různými instancemi Azure Redis Cache nebo naplnění mezipaměti s daty před použitím.

Tento článek obsahuje Průvodce pro import a export dat pomocí Azure Redis Cache a odpovědi na nejčastější dotazy.

> [!IMPORTANT]
> Import a Export je ve verzi preview a je dostupná jenom pro [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti.
>
>

## <a name="import"></a>Import
Import umožňuje přinést si Redis kompatibilní RDB soubory z jakéhokoli Redis serveru se systémem v libovolném cloudu nebo prostředí, včetně Redis systémem Linux, Windows nebo kteréhokoli poskytovatele cloudových služeb jako Amazon Web Services a dalších. Import dat je snadný způsob, jak vytvořit mezipaměti s předem vyplněná data. Během procesu importu Azure Redis Cache načte RDB soubory z úložiště Azure do paměti a vloží klíčů do mezipaměti.

> [!NOTE]
> Před zahájením operace importu se ujistěte, že Redis databáze (RDB) soubor nebo soubory jsou odeslány do stránky nebo blok objektů BLOB v úložišti Azure, ve stejné oblasti a předplatné jako instance služby Azure Redis Cache. Další informace najdete v tématu [Začínáme s Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Pokud jste exportovali pomocí souboru RDB [Azure Redis Cache exportovat](#export) funkce RDB soubor je již uložen do objektu BLOB stránky a je připravený pro import.
>
>

1. Chcete-li importovat jeden nebo více objektů BLOB exportovaný mezipaměti, [přejděte do mezipaměti](cache-configure.md#configure-redis-cache-settings) na portálu Azure a klikněte na **importovat data** z **prostředků nabídky**.

    ![Import dat][cache-import-data]
2. Klikněte na tlačítko **zvolte Blob(s)** a vyberte účet úložiště, který obsahuje data určená k importu.

    ![Výběr účtu úložiště][cache-import-choose-storage-account]
3. Klikněte na kontejner, který obsahuje data určená k importu.

    ![Vyberte kontejner][cache-import-choose-container]
4. Vyberte jeden nebo více objektů BLOB k importu klepnutím na plochu nalevo od názvu objektu blob a potom klikněte na **vyberte**.

    ![Vyberte objekty BLOB][cache-import-choose-blobs]
5. Klikněte na tlačítko **importovat** zahájíte proces importu.

   > [!IMPORTANT]
   > Mezipaměť je přístupný pro klienty mezipaměti není během procesu importu a všechna existující data v mezipaměti se odstraní.
   >
   >

    ![Import][cache-import-blobs]

    Pomocí následujících oznámení z portálu Azure nebo zobrazením událostí v můžete sledovat průběh operace importu [protokol auditování](../azure-resource-manager/resource-group-audit.md).

    ![Probíhá import][cache-import-data-import-complete]

## <a name="export"></a>Export
Export umožňuje exportovat data uložená ve službě Azure Redis Cache k Redis kompatibilní soubory RDB. Tato funkce slouží pro přesun dat z jedné instance Azure Redis Cache do jiné nebo jinému serveru Redis. Během procesu exportu ve virtuálním počítači, který je hostitelem instance serveru Azure Redis Cache je vytvořit dočasný soubor a soubor je odeslán k účtu úložiště určený. Po dokončení operace exportu se stavem úspěch nebo selhání dočasný soubor bude odstraněn.

1. Exportovat aktuální obsah mezipaměti do úložiště, [přejděte do mezipaměti](cache-configure.md#configure-redis-cache-settings) na portálu Azure a klikněte na **Export dat** z **prostředků nabídky**.

    ![Vyberte kontejner úložiště][cache-export-data-choose-storage-container]
2. Klikněte na tlačítko **vyberte kontejner úložiště** a vyberte požadovaný účet úložiště. Účet úložiště musí být ve stejném předplatném, oblasti jako vaše mezipaměť.

   > [!IMPORTANT]
   > Export funguje s objekty BLOB stránky, které podporují classic i Resource Manager účty úložiště, ale nepodporuje [účty úložiště Blob](../storage/common/storage-account-options.md#blob-storage-accounts) v tuto chvíli.
   >
   >

    ![Účet úložiště][cache-export-data-choose-account]
3. Vyberte kontejner objektů blob požadované a klikněte na **vyberte**. Chcete-li použít nový kontejner, klikněte na tlačítko **přidat kontejner** nejprve přidat a vyberte ho ze seznamu.

    ![Vyberte kontejner úložiště][cache-export-data-container]
4. Zadejte **předponu názvu objektu Blob** a klikněte na tlačítko **exportovat** ke spuštění procesu exportu. Předpona názvu objektu blob se používá jako předpona názvy soubory generované této operace exportu.

    ![Export][cache-export-data]

    Pomocí následujících oznámení z portálu Azure nebo zobrazením událostí v můžete sledovat průběh operace exportu [protokol auditování](../azure-resource-manager/resource-group-audit.md).

    ![Exportovat data dokončení][cache-export-data-export-complete]

    Mezipamětí zůstávají dostupné k použití během procesu exportu.

## <a name="importexport-faq"></a>Nejčastější dotazy k importu a exportu
Tento oddíl obsahuje nejčastější dotazy týkající se funkce importu a exportu.

* [Jaké cenové úrovně můžete použít k importu a exportu?](#what-pricing-tiers-can-use-importexport)
* [Můžete importovat data z jakéhokoli serveru Redis?](#can-i-import-data-from-any-redis-server)
* [Jaké verze RDB můžete importovat?](#what-rdb-versions-can-i-import)
* [Je k dispozici Moje mezipaměti během operace importu a exportu?](#is-my-cache-available-during-an-importexport-operation)
* [Můžete použít s clusteru Redis importu a exportu?](#can-i-use-importexport-with-redis-cluster)
* [Jak funguje importu a exportu s vlastní databáze nastavení?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Jak se liší od trvalosti Redis importu a exportu?](#how-is-importexport-different-from-redis-persistence)
* [Můžete automatizovat Import a Export pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné správy klientů?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Zobrazila vypršení časového limitu během Moje operace importu a exportu. Co to znamená?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Chyba se zobrazí chybové při exportu svá data do úložiště objektů Blob Azure. Co se přihodilo?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Jaké cenové úrovně můžete použít k importu a exportu?
Import a Export je k dispozici pouze v cenová úroveň premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Můžete importovat data z jakéhokoli serveru Redis?
Ano, kromě import data exportovaná z instance služby Azure Redis Cache, můžete importovat soubory RDB z jakéhokoli serveru Redis v libovolném cloudu nebo prostředí, například Linux, systém Windows spuštěný, nebo cloudu poskytovatelé, například Amazon Web Services. K tomuto účelu nahrát soubor RDB z požadovaného serveru Redis do stránky nebo blok objektů blob v účtu úložiště Azure a následně ho naimportovat do instance služby Azure Redis Cache premium. Například můžete chtít exportovat data z mezipaměti produkční a importujte ho do mezipaměti, používá jako součást pracovní prostředí pro testování nebo pro migraci.

> [!IMPORTANT]
> Chcete-li úspěšně importovat data exportovaná z Redis servery než Azure Redis Cache, při použití objektů blob stránky, musí být zarovnána velikost stránky objektu blob na hranici 512 bajtů. Ukázkový kód k provedení všech požadovaných bajtů odsazení, najdete v části [ukázkové stránky blogu nahrávání](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Jaké verze RDB můžete importovat?

Azure Redis Cache podporuje RDB import až prostřednictvím RDB verze 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Je k dispozici Moje mezipaměti během operace importu a exportu?
* **Export** – mezipamětí zůstanou dostupné, a můžete nadále používat vaše mezipaměť během operace exportu.
* **Import** – mezipaměti k dispozici, když se spustí operace importu a bude k dispozici pro použití při dokončení operace importu.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Můžete použít s clusteru Redis importu a exportu?
Ano, a můžete můžete importu a exportu mezi Clusterové mezipaměti a mezipaměti vypojené z clusteru. Od clusteru Redis [jen podporuje databáze 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), není-li importovat všechna data v databázích než 0. Při importu dat Clusterové mezipaměti, klíče se předistribuují mezi horizontálních oddílů clusteru.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Jak funguje importu a exportu s vlastní databáze nastavení?
Některé cenové úrovně mají různé [databáze omezení](cache-configure.md#databases), takže existují některé aspekty při importu, pokud jste nakonfigurovali vlastní hodnotu `databases` nastavení během vytváření mezipaměti.

* Při importu do cenovou úroveň s nižší `databases` limit než vrstvy, ze kterého jste exportovali:
  * Pokud používáte výchozí počet `databases`, což je 16 pro všechny cenové úrovně, dojde ke ztrátě žádná data.
  * Pokud používáte vlastní počet `databases` že patří do limity pro vrstvu, do které importujete, dojde ke ztrátě žádná data.
  * Exportovaná data obsažená data v databázi, která překračuje omezení nové vrstvy, není k importu dat z těchto vyšší databází.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Jak se liší od trvalosti Redis importu a exportu?
Trvalost Azure Redis Cache vám umožňuje zachovat data uložená v Redis do služby Azure Storage. Pokud je nakonfigurovaná trvalost, trvá Azure Redis Cache snímek mezipaměti Redis v binárním formátu Redis na disk, na základě konfigurovat četnosti zálohování. Pokud dojde k závažné události, zakazující primární server a repliky mezipaměti, je obnoven data v mezipaměti automaticky pomocí poslední snímek. Další informace najdete v tématu [postup konfigurace trvalosti dat pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-persistence.md).

Import / Export můžete přenést data do nebo exportovat z Azure Redis Cache. Není konfigurace zálohování a obnovení pomocí trvalosti Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Můžete automatizovat Import a Export pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné správy klientů?
Ano, pro prostředí PowerShell pokyny naleznete v části [import mezipaměti Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) a [Export mezipaměti Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Zobrazila vypršení časového limitu během Moje operace importu a exportu. Co to znamená?
Pokud zůstanou na **importovat data** nebo **exportovat data** okno delší než 15 minut před zahájením operace, obdržíte chybu s chybovou zprávou podobně jako v následujícím příkladu:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Chcete-li tento problém vyřešili, spusťte import nebo export fungování předtím, než 15 minut uplynul.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Chyba se zobrazí chybové při exportu svá data do úložiště objektů Blob Azure. Co se přihodilo?
Export pracuje pouze s RDB soubory uložené jako objekty BLOB stránky. Jiné typy objektů blob nejsou aktuálně podporovány, včetně účty úložiště blob s horká a studená vrstvami. Další informace najdete v tématu [účty úložiště Blob](../storage/common/storage-account-options.md#blob-storage-accounts).

## <a name="next-steps"></a>Další postup
Naučte se používat další funkce mezipaměti premium.

* [Úvod do Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
