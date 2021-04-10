---
title: Vytváření aktivačních událostí založených na událostech v Azure Data Factory
description: Naučte se, jak vytvořit Trigger v Azure Data Factory, který v reakci na událost spouští kanál.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: d9012c2bb56b7936b627063be2e9c5b7aa33541e
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962726"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Vytvoření triggeru, který spouští kanál v reakci na událost úložiště

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje triggery událostí úložiště, které můžete vytvořit v Data Factorych kanálech.

Architektura založená na událostech (EDA) je běžný vzor pro integraci dat, který zahrnuje produkční, detekci, spotřebu a reakci na události. Scénáře integrace dat často Data Factory vyžadují, aby zákazníci mohli aktivovat kanály založené na událostech, které se vyskytují v účtu úložiště, jako je třeba doručení nebo odstranění souboru v účtu Azure Blob Storage. Data Factory nativně integruje s [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), což umožňuje aktivovat kanály na těchto událostech.

Pokud chcete zobrazit Úvod a ukázku této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> Integrace popsaná v tomto článku závisí na [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Ujistěte se, že vaše předplatné je zaregistrované u poskytovatele prostředků Event Grid. Další informace najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Musíte být schopni provést akci *Microsoft. EventGrid/eventSubscriptions/**. Tato akce je součástí předdefinované role EventGrid EventSubscription přispěvatele.

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

V této části se dozvíte, jak vytvořit Trigger události úložiště v uživatelském rozhraní Azure Data Factory.

1. Přepněte na kartu **Upravit** , která je zobrazená se symbolem tužky.

1. V nabídce vyberte **aktivační událost** a pak vyberte **Nová/upravit**.

1. Na stránce **Přidat aktivační události** vyberte **zvolit aktivační událost...** a pak vyberte **+ Nová**.

1. Vyberte aktivační **událost typ události úložiště**

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Snímek obrazovky se stránkou autora k vytvoření nového triggeru události úložiště v Data Factory uživatelském rozhraní.":::

1. V rozevíracím seznamu předplatného Azure vyberte svůj účet úložiště nebo ručně použijte jeho ID prostředku účtu úložiště. Vyberte kontejner, ve kterém chcete události provést. Je vyžadován výběr kontejneru, ale je třeba mít na vědomí, že výběr všech kontejnerů může vést k velkému počtu událostí.

   > [!NOTE]
   > Aktivační událost události úložiště aktuálně podporuje pouze účty úložiště Azure Data Lake Storage Gen2 a obecné účely verze 2. Z důvodu omezení Azure Event Grid Azure Data Factory podporuje maximálně 500 triggerů událostí úložiště na jeden účet úložiště. Pokud jste dosáhli limitu, požádejte prosím podporu o doporučení a zvyšte limit při vyhodnocování pomocí Event Grid týmu. 

   > [!NOTE]
   > Pokud chcete vytvořit novou nebo upravit existující Trigger události úložiště, účet Azure použitý k přihlášení do Data Factory a publikování triggeru události úložiště musí mít v účtu úložiště odpovídající oprávnění řízení přístupu na základě role (Azure RBAC). Nevyžadují se žádné další oprávnění: instanční objekt pro Azure Data Factory _nepotřebuje zvláštní_ oprávnění pro účet úložiště ani Event Grid. Další informace o řízení přístupu najdete v části [řízení přístupu na základě rolí](#role-based-access-control) .

1. **Cesta k objektu BLOB začíná** a **cesta k objektu BLOB končí** vlastností umožňuje zadat kontejnery, složky a názvy objektů blob, pro které chcete události přijímat. Aktivační procedura události úložiště vyžaduje, aby byla definovaná aspoň jedna z těchto vlastností. Můžete použít celou řadu vzorů pro **cestu objektu BLOB** , která začíná a má **cestu k objektu BLOB končící** vlastností, jak je znázorněno v příkladech dále v tomto článku.

    * **Cesta objektu BLOB začíná na:** Cesta objektu BLOB musí začínat cestou složky. Platné hodnoty zahrnují `2018/` a `2018/april/shoes.csv` . Toto pole nelze vybrat, pokud není vybrán kontejner.
    * **Cesta objektu BLOB končí na:** Cesta objektu BLOB musí končit názvem souboru nebo příponou. Platné hodnoty zahrnují `shoes.csv` a `.csv` . Názvy kontejnerů a složek, jsou-li zadány, musí být odděleny `/blobs/` segmentem. Například kontejner pojmenovaný ' Orders ' může mít hodnotu `/orders/blobs/2018/april/shoes.csv` . Chcete-li určit složku v jakémkoli kontejneru, vynechejte úvodní znak "/". Například spustí `april/shoes.csv` událost pro libovolný soubor s názvem `shoes.csv` ve složce a s názvem ' duben ' v jakémkoli kontejneru.
    * Všimněte si, že cesta objektu BLOB **začíná** na a **končí** na, což je jediné porovnávání vzorů povolené v triggeru události úložiště. Pro typ triggeru se nepodporují jiné typy zástupných znaků.

1. Vyberte, jestli má aktivační událost reagovat na událost **vytvořeného objektu BLOB** , událost **odstranění objektu BLOB** nebo obojí. V zadaném umístění úložiště spustí každá událost Data Factory kanály přidružené k triggeru.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="Snímek obrazovky se stránkou pro vytváření aktivační události události úložiště":::

1. Vyberte, jestli Trigger ignoruje objekty BLOB s nulovými bajty.

1. Po nakonfigurování triggeru klikněte na **Další: data ve verzi Preview**. Tato obrazovka zobrazuje existující objekty blob, které odpovídají konfiguraci triggeru události úložiště. Ujistěte se, že máte konkrétní filtry. Konfigurace příliš rozsáhlých filtrů může odpovídat velkému počtu vytvořených nebo odstraněných souborů a může významně ovlivnit vaše náklady. Po ověření podmínek filtrování klikněte na **Dokončit**.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Snímek obrazovky se stránkou náhledu triggeru události úložiště":::

1. Pokud chcete k této aktivační události připojit kanál, přejděte na plátno kanálu a klikněte na **Trigger** a vyberte **Nový/upravit**. Jakmile se zobrazí boční navigace, klikněte na rozevírací seznam **vybrat aktivační událost...** a vyberte aktivační událost, kterou jste vytvořili. Klikněte na **Další: data Preview** a potvrďte, že je konfigurace správná, a pak **vedle** ověřit, jestli je verze Preview dat správná.

1. Pokud váš kanál obsahuje parametry, můžete je zadat v aktivační události spuštění na straně parametru navigace. Aktivační procedura události úložiště zachytí cestu ke složce a název objektu blob do vlastností `@triggerBody().folderPath` a `@triggerBody().fileName` . Chcete-li použít hodnoty těchto vlastností v kanálu, je nutné namapovat vlastnosti na parametry kanálu. Po mapování vlastností na parametry můžete získat přístup k hodnotám zachyceným triggerem prostřednictvím `@pipeline().parameters.parameterName` výrazu v celém kanálu. Podrobné vysvětlení najdete v tématu [metadata triggeru odkazů v kanálech](how-to-use-trigger-parameterization.md) .

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Snímek obrazovky s vlastnostmi mapování triggeru událostí úložiště na parametry kanálu":::

   V předchozím příkladu je aktivační událost nakonfigurovaná tak, aby se aktivovala v případě, že se ve složce _– testování událostí_ v kontejnerech ve _vzorových_ událostech vytvoří cesta objektu BLOB končící na. csv. Vlastnosti **FolderPath** a **filename** zachytí umístění nového objektu BLOB. Například při přidání MoviesDB.csv do cesty Sample-data/testování událostí `@triggerBody().folderPath` má hodnotu `sample-data/event-testing` a `@triggerBody().fileName` má hodnotu `moviesDB.csv` . Tyto hodnoty jsou mapovány v příkladu na parametry kanálu `sourceFolder` a `sourceFile` , které lze použít v celém kanálu jako `@pipeline().parameters.sourceFolder` a v `@pipeline().parameters.sourceFile` uvedeném pořadí.

   > [!NOTE]
   > Pokud vytváříte kanál a Trigger ve [službě Azure synapse Analytics](/synapse-analytics), musíte použít `@trigger().outputs.body.fileName` a `@trigger().outputs.body.folderPath` jako parametry. Tyto dvě vlastnosti zaznamenávají informace o objektu BLOB. Místo použití a použijte tyto vlastnosti `@triggerBody().fileName` `@triggerBody().folderPath` .

1. Až budete hotovi, klikněte na **Dokončit** .

## <a name="json-schema"></a>Schéma JSON

Následující tabulka poskytuje přehled prvků schématu, které souvisejí s triggery událostí úložiště:

| **Element JSON** | **Popis** | **Typ** | **Povolené hodnoty** | **Povinné** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **oboru** | ID prostředku Azure Resource Manager účtu úložiště. | Řetězec | ID Azure Resource Manager | Yes |
| **událost** | Typ událostí, které způsobují, že se aktivační událost aktivuje. | Pole    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Ano, libovolná kombinace těchto hodnot. |
| **blobPathBeginsWith** | Cesta objektu BLOB musí začínat vzorem poskytnutým pro aktivaci triggeru. Například `/records/blobs/december/` aktivuje Trigger jenom pro objekty blob ve složce v `december` `records` kontejneru. | Řetězec   | | Zadejte hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo `blobPathEndsWith` . |
| **blobPathEndsWith** | Cesta objektu BLOB musí končit vzorem poskytnutým pro aktivaci triggeru. Například `december/boxes.csv` aktivuje Trigger jenom pro objekty BLOB s názvem `boxes` ve `december` složce. | Řetězec   | | Je nutné zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Bez ohledu na to, zda objekty BLOB s nulovým bajtem budou aktivovat spuštění kanálu. Ve výchozím nastavení je tato hodnota nastavena na true (pravda). | Logická hodnota | true nebo false | No |

## <a name="examples-of-storage-event-triggers"></a>Příklady triggerů událostí úložiště

V této části najdete příklady nastavení triggeru události úložiště.

> [!IMPORTANT]
> Je nutné zahrnout `/blobs/` segment cesty, jak je znázorněno v následujících příkladech, kdykoli zadáte kontejner a složku, kontejner a soubor, nebo kontejner, složku a soubor. V případě **blobPathBeginsWith** bude uživatelské rozhraní Data Factory automaticky přidávat `/blobs/` mezi složku a název kontejneru ve formátu JSON triggeru.

| Vlastnost | Příklad | Description |
|---|---|---|
| **Cesta objektu BLOB začíná na** | `/containername/` | Přijímá události pro libovolný objekt BLOB v kontejneru. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/` | Přijímá události pro všechny objekty BLOB v `containername` kontejneru a `foldername` složce. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/subfoldername/` | Můžete také odkazovat na podsložku. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` ve `foldername` složce v `containername` kontejneru. |
| **Cesta objektu BLOB končí na** | `file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` v jakékoli cestě. |
| **Cesta objektu BLOB končí na** | `/containername/blobs/file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` v kontejneru `containername` . |
| **Cesta objektu BLOB končí na** | `foldername/file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` ve `foldername` složce v jakémkoli kontejneru. |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure Data Factory používá řízení přístupu na základě role Azure (Azure RBAC) k zajištění toho, aby neoprávněný přístup mohl naslouchat, odebírat aktualizace a aktivovat kanály propojené s událostmi objektů blob, jsou čistě zakázané.

* Aby bylo možné úspěšně vytvořit novou nebo aktualizovat existující Trigger události úložiště, účet Azure přihlášený k Data Factory musí mít odpovídající přístup k příslušnému účtu úložiště. V opačném případě se operace nezdařila se _zakázaným přístupem_.
* Data Factory nepotřebuje žádné zvláštní oprávnění k vašemu Event Grid a vy _nemusíte_ přiřazovat speciální oprávnění RBAC pro Data Factory instančního objektu pro tuto operaci.

U triggeru události úložiště funguje kterékoli z následujících nastavení RBAC:

* Role vlastníka účtu úložiště
* Role přispěvatele k účtu úložiště
* Oprávnění _Microsoft. EventGrid/EventSubscriptions/Write_ pro účet úložiště _/Subscriptions/# # # #/resourceGroups/# # # #/Providers/Microsoft.Storage/storageAccounts/storageAccountName_

Abyste porozuměli tomu, jak Azure Data Factory doručí dvě příslibů, Pojďme vzít v úvahu krok a podíváme se na vás zajímá náhled za scénu. Tady jsou pracovní toky vysoké úrovně pro integraci mezi Data Factory, úložištěm a Event Grid.

### <a name="create-a-new-storage-event-trigger"></a>Vytvoří novou aktivační událost události úložiště.

Tento pracovní postup vysoké úrovně popisuje, jak Azure Data Factory komunikuje s Event Grid k vytvoření triggeru události úložiště.

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Pracovní postup vytvoření triggeru události úložiště":::

Dvě poznatelný volání z pracovních toků:

* Azure Data Factory neposkytuje _žádný_ přímý kontakt s účtem úložiště. Požadavek na vytvoření odběru je místo toho přenášen a zpracován Event Grid. Proto Data Factory nepotřebuje žádné oprávnění k účtu úložiště pro tento krok.

* K řízení přístupu a kontrole oprávnění dochází na straně Azure Data Factory. Před tím, než ADF pošle požadavek na přihlášení k odběru události úložiště, zkontroluje oprávnění pro tohoto uživatele. Konkrétně ověří, zda se účet Azure přihlásil a pokusil se vytvořit Trigger události úložiště, který má odpovídající přístup k příslušnému účtu úložiště. Pokud se ověření oprávnění nepovede, vytvoření triggeru se také nepovede.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Trigger události úložiště Data Factory spuštění kanálu

Tyto pracovní toky vysoké úrovně popisují, jak se spouští kanál událostí úložiště pomocí Event Grid

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="Pracovní postup spouštění kanálu událostí úložiště":::

Když přichází do kanálu triggerování událostí v Data Factory, jsou v pracovním postupu tři poznatelnýcí volání:

* Event Grid používá model push, který zprávu pošle co nejdříve, když úložiště tuto zprávu vyřazuje do systému. To se liší od systému zasílání zpráv, jako je například Kafka, kde se používá systém pro vyžádání obsahu.
* Aktivační procedura události v Azure Data Factory slouží jako aktivní naslouchací proces příchozí zprávy a správně aktivuje přidružený kanál.
* Samotný Trigger události úložiště neposkytuje žádný přímý kontakt s účtem úložiště.

  * V takovém případě, pokud máte v kanálu kopii nebo jinou aktivitu ke zpracování dat v účtu úložiště, Data Factory s využitím přihlašovacích údajů uložených v propojené službě přímý kontakt s úložištěm. Ujistěte se, že je správně nastavená propojená služba.
  * Pokud ale v kanálu nebudete mít žádné odkazy na účet úložiště, nemusíte udělovat oprávnění Data Factory přistupovat k účtu úložiště.

## <a name="next-steps"></a>Další kroky

* Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#trigger-execution).
* Informace o tom, jak odkazovat na metadata triggeru v kanálu, najdete v tématu [metadata triggeru odkazů v běhu kanálu](how-to-use-trigger-parameterization.md) .
