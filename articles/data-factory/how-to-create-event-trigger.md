---
title: Vytváření aktivačních událostí založených na událostech v Azure Data Factory | Microsoft Docs
description: Naučte se, jak vytvořit Trigger v Azure Data Factory, který v reakci na událost spouští kanál.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: a2c046d4375b891d17d545edd804d0fa1da6ee75
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142355"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Vytvoření triggeru, který spustí kanál v reakci na událost

Tento článek popisuje triggery založené na událostech, které můžete vytvořit v Data Factorych kanálech.

Architektura založená na událostech (EDA) je běžný vzor pro integraci dat, který zahrnuje produkční, detekci, spotřebu a reakci na události. Scénáře integrace dat často Data Factory vyžadují, aby zákazníci mohli aktivovat kanály na základě událostí, jako je například doručení nebo odstranění souboru ve vašem Azure Storagem účtu. Data Factory je teď integrovaná s [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), která umožňuje aktivovat kanály pro událost.

Pokud chcete zobrazit Úvod a ukázku této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integrace popsaná v tomto článku závisí na [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Ujistěte se, že vaše předplatné je zaregistrované u poskytovatele prostředků Event Grid. Další informace najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

V této části se dozvíte, jak vytvořit aktivační událost události v uživatelském rozhraní Azure Data Factory.

1. Přejít na plátno pro **vytváření obsahu**

2. V levém dolním rohu klikněte na tlačítko triggery .

3. Klikněte na **+ Nový** . otevře se navigace vytvořit Trigger na straně.

4. Vybrat **událost** typu triggeru

![Vytvořit novou aktivační proceduru události](media/how-to-create-event-trigger/event-based-trigger-image1.png)

5. V rozevíracím seznamu předplatného Azure vyberte svůj účet úložiště nebo ručně použijte jeho ID prostředku účtu úložiště. Vyberte kontejner, ve kterém chcete události provést. Výběr kontejneru je nepovinný, ale nezapomeňte, že výběr všech kontejnerů může vést k velkému počtu událostí.

   > [!NOTE]
   > Aktivační událost aktuálně podporuje pouze účty úložiště Azure Data Lake Storage Gen2 a obecné účely verze 2. Z důvodu omezení Azure Event Grid Azure Data Factory podporuje maximálně 500 triggerů událostí na účet úložiště.

6. **Cesta k objektu BLOB začíná** a **cesta k objektu BLOB končí** vlastností umožňuje zadat kontejnery, složky a názvy objektů blob, pro které chcete události přijímat. Aktivační událost události vyžaduje, aby byla definována alespoň jedna z těchto vlastností. Můžete použít celou řadu vzorů pro **cestu objektu BLOB** , která začíná a má **cestu k objektu BLOB končící** vlastností, jak je znázorněno v příkladech dále v tomto článku.

    * **Cesta objektu BLOB začíná na:** Cesta objektu BLOB musí začínat cestou složky. Platné hodnoty zahrnují `2018/` a `2018/april/shoes.csv`. Toto pole nelze vybrat, pokud není vybrán kontejner.
    * **Cesta objektu BLOB končí na:** Cesta objektu BLOB musí končit názvem souboru nebo příponou. Platné hodnoty zahrnují `shoes.csv` a `.csv`. Název kontejneru a složky jsou volitelné, ale pokud jsou zadané, musí být odděleny `/blobs/` segmentem. Například kontejner pojmenovaný ' Orders ' může mít hodnotu `/orders/blobs/2018/april/shoes.csv`. Chcete-li určit složku v jakémkoli kontejneru, vynechejte úvodní znak "/". Například `april/shoes.csv` spustí událost pro libovolný soubor s názvem `shoes.csv` ve složce a s názvem ' duben ' v jakémkoli kontejneru. 

7. Vyberte, jestli má aktivační událost reagovat na událost **vytvořeného objektu BLOB** , událost **odstranění objektu BLOB** nebo obojí. V zadaném umístění úložiště spustí každá událost Data Factory kanály přidružené k triggeru.

    ![Konfigurace triggeru události](media/how-to-create-event-trigger/event-based-trigger-image2.png)

8. Po nakonfigurování triggeru klikněte **na další: Náhled**dat. Tato obrazovka zobrazuje existující objekty blob, které odpovídají vaší konfiguraci triggeru události. Ujistěte se, že máte konkrétní filtry. Konfigurace příliš rozsáhlých filtrů může odpovídat velkému počtu vytvořených nebo odstraněných souborů a může významně ovlivnit vaše náklady. Po ověření podmínek filtrování klikněte na **Dokončit**.

    ![Náhled dat triggeru události](media/how-to-create-event-trigger/event-based-trigger-image3.png)

9. Pokud chcete k této aktivační události připojit kanál, přejděte na plátno kanálu a klikněte na **Přidat Trigger** a vyberte **Nový/upravit**. Jakmile se zobrazí boční navigace, klikněte na rozevírací seznam **vybrat aktivační událost...** a vyberte aktivační událost, kterou jste vytvořili. Klikněte **na další: Verze Preview** pro potvrzení konfigurace je správná a pak **u** pole ověřit, jestli je náhled dat správný.

10. Pokud váš kanál obsahuje parametry, můžete je zadat v aktivační události spuštění na straně parametru navigace. Aktivační procedura události zachytí cestu ke složce a název souboru objektu blob do vlastností `@triggerBody().folderPath` a. `@triggerBody().fileName` Chcete-li použít hodnoty těchto vlastností v kanálu, je nutné namapovat vlastnosti na parametry kanálu. Po mapování vlastností na parametry můžete získat přístup k hodnotám zachyceným triggerem prostřednictvím `@pipeline().parameters.parameterName` výrazu v celém kanálu. Až budete hotovi, klikněte na **Dokončit** .

    ![Mapování vlastností na parametry kanálu](media/how-to-create-event-trigger/event-based-trigger-image4.png)

V předchozím příkladu je aktivační událost nakonfigurovaná tak, aby se aktivovala v případě, že se ve složce – testování událostí v kontejnerech ve vzorových událostech vytvoří cesta objektu BLOB končící na. csv. Vlastnosti **FolderPath** a **filename** zachytí umístění nového objektu BLOB. Například když se MoviesDB. csv přidá do cesty Sample-data/Event `@triggerBody().folderPath` -Tests, má `sample-data/event-testing` hodnotu `moviesDB.csv`a `@triggerBody().fileName` má hodnotu. Tyto hodnoty jsou namapovány v `sourceFolder` příkladu na parametry kanálu a `sourceFile` lze je použít v rámci kanálu jako `@pipeline().parameters.sourceFolder` a `@pipeline().parameters.sourceFile` v uvedeném pořadí.

## <a name="json-schema"></a>Schéma JSON

Následující tabulka poskytuje přehled prvků schématu, které souvisejí s triggery založenými na událostech:

| **Element JSON** | **Popis** | **Typ** | **Povolené hodnoty** | **Požadováno** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **oboru** | ID prostředku Azure Resource Manager účtu úložiště. | Řetězec | ID Azure Resource Manager | Ano |
| **událost** | Typ událostí, které způsobují, že se aktivační událost aktivuje. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Ano, libovolná kombinace těchto hodnot. |
| **blobPathBeginsWith** | Cesta objektu BLOB musí začínat vzorem poskytnutým pro aktivaci triggeru. Například `/records/blobs/december/` aktivuje Trigger jenom pro objekty blob `december` ve složce `records` v kontejneru. | Řetězec   | | Je nutné zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo. `blobPathEndsWith` |
| **blobPathEndsWith** | Cesta objektu BLOB musí končit vzorem poskytnutým pro aktivaci triggeru. Například `december/boxes.csv` aktivuje Trigger jenom pro objekty BLOB s názvem `boxes` ve `december` složce. | Řetězec   | | Je nutné zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo. `blobPathEndsWith` |

## <a name="examples-of-event-based-triggers"></a>Příklady triggerů založených na událostech

V této části najdete příklady nastavení triggeru založeného na událostech.

> [!IMPORTANT]
> Je nutné zahrnout `/blobs/` segment cesty, jak je znázorněno v následujících příkladech, kdykoli zadáte kontejner a složku, kontejner a soubor, nebo kontejner, složku a soubor. V případě **blobPathBeginsWith**bude uživatelské rozhraní Data Factory automaticky přidávat `/blobs/` mezi složku a název kontejneru ve formátu JSON triggeru.

| Vlastnost | Příklad | Popis |
|---|---|---|
| **Cesta objektu BLOB začíná na** | `/containername/` | Přijímá události pro libovolný objekt BLOB v kontejneru. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/` | Přijímá události pro všechny objekty BLOB v `containername` kontejneru a `foldername` složce. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/subfoldername/` | Můžete také odkazovat na podsložku. |
| **Cesta objektu BLOB začíná na** | `/containername/blobs/foldername/file.txt` | Přijímá události pro objekt BLOB s `file.txt` názvem `foldername` ve složce `containername` v kontejneru. |
| **Cesta objektu BLOB končí na** | `file.txt` | Přijímá události pro objekt BLOB s `file.txt` názvem v jakékoli cestě. |
| **Cesta objektu BLOB končí na** | `/containername/blobs/file.txt` | Přijímá události pro objekt BLOB s `file.txt` názvem v `containername`kontejneru. |
| **Cesta objektu BLOB končí na** | `foldername/file.txt` | Přijímá události pro objekt BLOB s `file.txt` názvem `foldername` ve složce v jakémkoli kontejneru. |

## <a name="next-steps"></a>Další postup
Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#triggers).
