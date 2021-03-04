---
title: Vytváření aktivačních událostí založených na událostech v Azure Data Factory
description: Naučte se, jak vytvořit Trigger v Azure Data Factory, který v reakci na událost spouští kanál.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 7dde05e02421ef8d2ea46fd0d50687ede6e5d884
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727773"
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

1. Přejít na **plátno pro vytváření obsahu**

1. V levém dolním rohu klikněte na tlačítko **triggery** .

1. Klikněte na **+ Nový** . otevře se navigace vytvořit Trigger na straně.

1. Vyberte aktivační **událost typ události úložiště**

    ![Vytvořit novou aktivační událost události úložiště](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. V rozevíracím seznamu předplatného Azure vyberte svůj účet úložiště nebo ručně použijte jeho ID prostředku účtu úložiště. Vyberte kontejner, ve kterém chcete události provést. Výběr kontejneru je nepovinný, ale nezapomeňte, že výběr všech kontejnerů může vést k velkému počtu událostí.

   > [!NOTE]
   > Aktivační událost události úložiště aktuálně podporuje pouze účty úložiště Azure Data Lake Storage Gen2 a obecné účely verze 2. Z důvodu omezení Azure Event Grid Azure Data Factory podporuje maximálně 500 triggerů událostí úložiště na jeden účet úložiště.

   > [!NOTE]
   > Pokud chcete vytvořit a upravit novou aktivační událost události úložiště, účet Azure použitý k přihlášení do Data Factory musí mít alespoň oprávnění *vlastníka* k účtu úložiště. Nevyžadují se žádné další oprávnění: instanční objekt pro Azure Data Factory _nepotřebuje zvláštní_ oprávnění pro účet úložiště ani Event Grid.

1. **Cesta k objektu BLOB začíná** a **cesta k objektu BLOB končí** vlastností umožňuje zadat kontejnery, složky a názvy objektů blob, pro které chcete události přijímat. Aktivační procedura události úložiště vyžaduje, aby byla definovaná aspoň jedna z těchto vlastností. Můžete použít celou řadu vzorů pro **cestu objektu BLOB** , která začíná a má **cestu k objektu BLOB končící** vlastností, jak je znázorněno v příkladech dále v tomto článku.

    * **Cesta objektu BLOB začíná na:** Cesta objektu BLOB musí začínat cestou složky. Platné hodnoty zahrnují `2018/` a `2018/april/shoes.csv` . Toto pole nelze vybrat, pokud není vybrán kontejner.
    * **Cesta objektu BLOB končí na:** Cesta objektu BLOB musí končit názvem souboru nebo příponou. Platné hodnoty zahrnují `shoes.csv` a `.csv` . Název kontejneru a složky jsou volitelné, ale pokud jsou zadané, musí být odděleny `/blobs/` segmentem. Například kontejner pojmenovaný ' Orders ' může mít hodnotu `/orders/blobs/2018/april/shoes.csv` . Chcete-li určit složku v jakémkoli kontejneru, vynechejte úvodní znak "/". Například spustí `april/shoes.csv` událost pro libovolný soubor s názvem `shoes.csv` ve složce a s názvem ' duben ' v jakémkoli kontejneru.
    * Poznámka: cesta objektu BLOB **začíná** na a **končí** na, je to jediné porovnávání vzorů povolené v triggeru události úložiště. Pro typ triggeru se nepodporují jiné typy zástupných znaků.

1. Vyberte, jestli má aktivační událost reagovat na událost **vytvořeného objektu BLOB** , událost **odstranění objektu BLOB** nebo obojí. V zadaném umístění úložiště spustí každá událost Data Factory kanály přidružené k triggeru.

    ![Konfigurace triggeru události úložiště](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Vyberte, jestli aktivační událost ignoruje objekty BLOB s nulovými bajty.

1. Po nakonfigurování triggeru klikněte na **Další: data ve verzi Preview**. Tato obrazovka zobrazuje existující objekty blob, které odpovídají konfiguraci triggeru události úložiště. Ujistěte se, že máte konkrétní filtry. Konfigurace příliš rozsáhlých filtrů může odpovídat velkému počtu vytvořených nebo odstraněných souborů a může významně ovlivnit vaše náklady. Po ověření podmínek filtrování klikněte na **Dokončit**.

    ![Data triggeru události úložiště ve verzi Preview](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Pokud chcete k této aktivační události připojit kanál, přejděte na plátno kanálu a klikněte na **Přidat Trigger** a vyberte **Nový/upravit**. Jakmile se zobrazí boční navigace, klikněte na rozevírací seznam **vybrat aktivační událost...** a vyberte aktivační událost, kterou jste vytvořili. Klikněte na **Další: data Preview** a potvrďte, že je konfigurace správná, a pak **vedle** ověřit, jestli je verze Preview dat správná.

1. Pokud váš kanál obsahuje parametry, můžete je zadat v aktivační události spuštění na straně parametru navigace. Aktivační procedura události úložiště zachytí cestu ke složce a název objektu blob do vlastností `@triggerBody().folderPath` a `@triggerBody().fileName` . Chcete-li použít hodnoty těchto vlastností v kanálu, je nutné namapovat vlastnosti na parametry kanálu. Po mapování vlastností na parametry můžete získat přístup k hodnotám zachyceným triggerem prostřednictvím `@pipeline().parameters.parameterName` výrazu v celém kanálu. Až budete hotovi, klikněte na **Dokončit** .

    ![Mapování vlastností na parametry kanálu](media/how-to-create-event-trigger/event-based-trigger-image4.png)

V předchozím příkladu je aktivační událost nakonfigurovaná tak, aby se aktivovala v případě, že se ve složce – testování událostí v kontejnerech ve vzorových událostech vytvoří cesta objektu BLOB končící na. csv. Vlastnosti **FolderPath** a **filename** zachytí umístění nového objektu BLOB. Například při přidání MoviesDB.csv do cesty Sample-data/testování událostí `@triggerBody().folderPath` má hodnotu `sample-data/event-testing` a `@triggerBody().fileName` má hodnotu `moviesDB.csv` . Tyto hodnoty jsou namapovány v příkladu na parametry kanálu `sourceFolder` a `sourceFile` lze je použít v rámci kanálu jako `@pipeline().parameters.sourceFolder` a v `@pipeline().parameters.sourceFile` uvedeném pořadí.

## <a name="json-schema"></a>Schéma JSON

Následující tabulka poskytuje přehled prvků schématu, které souvisejí s triggery událostí úložiště:

| **Element JSON** | **Popis** | **Typ** | **Povolené hodnoty** | **Povinné** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **oboru** | ID prostředku Azure Resource Manager účtu úložiště. | Řetězec | ID Azure Resource Manager | Ano |
| **událost** | Typ událostí, které způsobují, že se aktivační událost aktivuje. | Pole    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Ano, libovolná kombinace těchto hodnot. |
| **blobPathBeginsWith** | Cesta objektu BLOB musí začínat vzorem poskytnutým pro aktivaci triggeru. Například `/records/blobs/december/` aktivuje Trigger jenom pro objekty blob ve složce v `december` `records` kontejneru. | Řetězec   | | Je nutné zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo `blobPathEndsWith` . |
| **blobPathEndsWith** | Cesta objektu BLOB musí končit vzorem poskytnutým pro aktivaci triggeru. Například `december/boxes.csv` aktivuje Trigger jenom pro objekty BLOB s názvem `boxes` ve `december` složce. | Řetězec   | | Je nutné zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Bez ohledu na to, zda objekty BLOB s nulovým bajtem budou aktivovat spuštění kanálu. Ve výchozím nastavení je tato hodnota nastavena na true (pravda). | Logická hodnota | true nebo false | Ne |

## <a name="examples-of-storage-event-triggers"></a>Příklady triggerů událostí úložiště

V této části najdete příklady nastavení triggeru události úložiště.

> [!IMPORTANT]
> Je nutné zahrnout `/blobs/` segment cesty, jak je znázorněno v následujících příkladech, kdykoli zadáte kontejner a složku, kontejner a soubor, nebo kontejner, složku a soubor. V případě **blobPathBeginsWith** bude uživatelské rozhraní Data Factory automaticky přidávat `/blobs/` mezi složku a název kontejneru ve formátu JSON triggeru.

| Vlastnost | Příklad | Popis |
|---|---|---|
| **Cesta objektu BLOB začíná na** | `/containername/` | Přijímá události pro libovolný objekt BLOB v kontejneru. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/` | Přijímá události pro všechny objekty BLOB v `containername` kontejneru a `foldername` složce. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/subfoldername/` | Můžete také odkazovat na podsložku. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` ve `foldername` složce v `containername` kontejneru. |
| **Cesta objektu BLOB končí na** | `file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` v jakékoli cestě. |
| **Cesta objektu BLOB končí na** | `/containername/blobs/file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` v kontejneru `containername` . |
| **Cesta objektu BLOB končí na** | `foldername/file.txt` | Přijímá události pro objekt BLOB s názvem `file.txt` ve `foldername` složce v jakémkoli kontejneru. |

## <a name="next-steps"></a>Další kroky

Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#trigger-execution).
