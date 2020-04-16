---
title: Vytváření aktivačních událostí založených na událostech v Azure Data Factory
description: Zjistěte, jak vytvořit aktivační událost v Azure Data Factory, která spustí kanál v reakci na událost.
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
ms.openlocfilehash: d697fb8afe3e92dfe54eb5d89a2ef59425cb0cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414914"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Vytvoření aktivační události, která spustí kanál v reakci na událost
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje aktivační události založené na událostech, které můžete vytvořit v kanálech datové továrny.

Architektura řízená událostmi (EDA) je běžný vzor integrace dat, který zahrnuje výrobu, detekci, spotřebu a reakci na události. Scénáře integrace dat často vyžadují, aby zákazníci Data Factory aktivovali kanály na základě událostí, jako je příchod nebo odstranění souboru ve vašem účtu Azure Storage. Data Factory je teď integrovaná s [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), který umožňuje aktivovat kanály na události.

Desetiminutový úvod a ukázku této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integrace popsaná v tomto článku závisí na [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Ujistěte se, že vaše předplatné je registrováno u poskytovatele prostředků Event Grid. Další informace najdete v tématu [Zprostředkovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

Tato část ukazuje, jak vytvořit aktivační událost v uživatelském rozhraní Azure Data Factory.

1. Přejít na **autorská plátno**

1. V levém dolním rohu klikněte na tlačítko **Triggers**

1. Klikněte **na + Nový,** který se otevře vytvořit spouštěcí straně nav

1. Vybrat událost typu aktivační **události**

    ![Vytvořit novou aktivační událost](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Vyberte svůj účet úložiště z rozbalovací nabídky předplatného Azure nebo ručně pomocí id prostředku účtu úložiště. Zvolte kontejner, ve kterém chcete, aby k událostem došlo. Výběr kontejneru je volitelný, ale mějte na paměti, že výběr všech kontejnerů může vést k velkému počtu událostí.

   > [!NOTE]
   > Aktivační událost aktuálně podporuje pouze Azure Data Lake Storage Gen2 a účty úložiště pro obecné účely verze 2. Z důvodu omezení Azure Event Grid Azure Data Factory podporuje jenom 500 aktivačních událostí na účet úložiště.

1. **Cesta objektu blob začíná** a cesta **objektu blob končí vlastnostmi** umožňují určit kontejnery, složky a názvy objektů blob, pro které chcete přijímat události. Aktivační událost vyžaduje, aby byla definována alespoň jedna z těchto vlastností. Můžete použít různé vzorky pro obě **cesty blob začíná** a **blob cesta končí vlastnostmi,** jak je znázorněno v příkladech dále v tomto článku.

    * **Cesta objektu blob začíná:** Cesta k objektu blob musí začínat cestou ke složce. Platné hodnoty `2018/` `2018/april/shoes.csv`zahrnují a . Toto pole nelze vybrat, pokud není vybrán kontejner.
    * **Cesta objektu blob končí:** Cesta k objektu blob musí končit názvem nebo příponou souboru. Platné hodnoty `shoes.csv` `.csv`zahrnují a . Název kontejneru a složky jsou volitelné, ale `/blobs/` pokud jsou zadány, musí být odděleny segmentem. Například kontejner s názvem objednávky může `/orders/blobs/2018/april/shoes.csv`mít hodnotu . Chcete-li určit složku v libovolném kontejneru, vynechejte úvodní znak/. Například `april/shoes.csv` spustí událost na libovolný `shoes.csv` soubor s názvem ve složce s názvem "duben" v libovolném kontejneru. 

1. Vyberte, jestli aktivační událost bude reagovat na událost **vytvořená objektem blob,** odstraněná událost **objektu Blob** nebo obojí. V zadaném umístění úložiště každá událost spustí kanály data factory přidružené k aktivační události.

    ![Konfigurace aktivační události](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Vyberte, zda aktivační událost ignoruje objekty BLOB s nulovými bajty.

1. Po konfiguraci aktivační události klikněte na **Další: Náhled dat**. Na této obrazovce se zobrazí existující objekty BLOB odpovídající konfiguraci aktivační události. Ujistěte se, že máte konkrétní filtry. Konfigurace filtrů, které jsou příliš široké, se může shodovat s velkým počtem vytvořených nebo odstraněných souborů a může mít významný dopad na náklady. Po ověření podmínek filtru klepněte na tlačítko **Dokončit**.

    ![Náhled dat aktivační události](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Pokud chcete k této aktivační události připojit kanál, přejděte na plátno kanálu a klikněte na **Přidat aktivační událost** a vyberte **Nový/Upravit**. Když se objeví boční nav, klikněte na **zvolit aktivační událost ...** rozbalovací a vyberte aktivační událost, kterou jste vytvořili. Klikněte na **Další: Náhled dat** pro potvrzení konfigurace je správná a **potom Další** pro ověření náhledu dat je správný.

1. Pokud váš kanál má parametry, můžete je zadat na straně aktivační události spouštěcí. Aktivační událost zachytí cestu ke složce a název `@triggerBody().folderPath` souboru objektu blob do vlastností a `@triggerBody().fileName`. Chcete-li použít hodnoty těchto vlastností v kanálu, je nutné namapovat vlastnosti na parametry kanálu. Po mapování vlastností na parametry, můžete přistupovat k `@pipeline().parameters.parameterName` hodnotám zachycených aktivační událostí prostřednictvím výrazu v celém kanálu. Po dokončení klepněte na **tlačítko Dokončit.**

    ![Mapování vlastností na parametry kanálu](media/how-to-create-event-trigger/event-based-trigger-image4.png)

V předchozím příkladu je aktivační událost nakonfigurována tak, aby se spustila při vytvoření cesty objektu blob končící na .csv v testování událostí složky v ukázkových datech kontejneru. Vlastnosti **folderPath** a **fileName** zachycují umístění nového objektu blob. Například při MoviesDB.csv je přidán do cesty vzorkování `@triggerBody().folderPath` dat/event-testing, má hodnotu `sample-data/event-testing` a `@triggerBody().fileName` má hodnotu `moviesDB.csv`. Tyto hodnoty jsou mapovány v příkladu `sourceFolder` `sourceFile` parametrů kanálu a které `@pipeline().parameters.sourceFolder` lze `@pipeline().parameters.sourceFile` použít v celém kanálu jako a příslušně.

## <a name="json-schema"></a>Schéma JSON

Následující tabulka obsahuje přehled prvků schématu, které souvisejí s aktivačními událostmi založenými na událostech:

| **Prvek JSON** | **Popis** | **Typ** | **Povolené hodnoty** | **Požadováno** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Rozsah** | ID prostředku Správce prostředků Azure účtu úložiště. | Řetězec | ID Správce prostředků Azure | Ano |
| **Události** | Typ událostí, které způsobují spuštění této aktivační události. | Pole    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobOdstraněn | Ano, libovolnou kombinaci těchto hodnot. |
| **blobPathBeginsWith** | Cesta objektu blob musí začínat vzor zadaný pro aktivační událost k požáru. Například `/records/blobs/december/` pouze spustí aktivační událost pro `december` objekty `records` BLOB ve složce pod kontejnerem. | Řetězec   | | Je třeba zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo `blobPathEndsWith`. |
| **blobPathEndsWith** | Cesta objektu blob musí končit vzor zapředpokladu, že aktivační událost k požáru. Například `december/boxes.csv` pouze spustí aktivační událost pro `boxes` objekty BLOB pojmenované ve `december` složce. | Řetězec   | | Je třeba zadat hodnotu alespoň pro jednu z těchto vlastností: `blobPathBeginsWith` nebo `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Bez ohledu na to, zda objekty BLOB s nulovým bajtem spustí spuštění kanálu. Ve výchozím nastavení je tato hodnota nastavena na hodnotu true. | Logická hodnota | true nebo false | Ne |

## <a name="examples-of-event-based-triggers"></a>Příklady aktivačních událostí založených na událostech

Tato část obsahuje příklady nastavení aktivační události založené na událostech.

> [!IMPORTANT]
> Je nutné zahrnout `/blobs/` segment cesty, jak je znázorněno v následujících příkladech, vždy, když zadáte kontejner a složku, kontejner a soubor nebo kontejner, složku a soubor. Pro **blobPathBeginsWith**, uživatelské okno Factory `/blobs/` data automaticky přidá mezi složku a název kontejneru v aktivační události JSON.

| Vlastnost | Příklad | Popis |
|---|---|---|
| **Cesta objektu blob začíná** | `/containername/` | Přijímá události pro všechny objekty blob v kontejneru. |
| **Cesta objektu blob začíná** | `/containername/blobs/foldername/` | Přijímá události pro všechny objekty `containername` BLOB v kontejneru a `foldername` složce. |
| **Cesta objektu blob začíná** | `/containername/blobs/foldername/subfoldername/` | Můžete také odkazovat na podsložku. |
| **Cesta objektu blob začíná** | `/containername/blobs/foldername/file.txt` | Přijímá události pro objekt `file.txt` blob `foldername` pojmenovaný `containername` ve složce pod kontejnerem. |
| **Cesta objektu blob končí** | `file.txt` | Přijímá události pro objekt `file.txt` blob pojmenovaný v libovolné cestě. |
| **Cesta objektu blob končí** | `/containername/blobs/file.txt` | Přijímá události pro objekt `file.txt` blob `containername`s názvem pod kontejnerem . |
| **Cesta objektu blob končí** | `foldername/file.txt` | Přijímá události pro objekt `file.txt` blob pojmenovaný ve `foldername` složce pod libovolným kontejnerem. |

## <a name="next-steps"></a>Další kroky
Podrobné informace o aktivačních událostech naleznete v [tématu Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#trigger-execution).
