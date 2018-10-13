---
title: Vytvoření triggery založené na události ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit trigger v Azure Data Factory, která běží v reakci na událost kanálu.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: douglasl
ms.openlocfilehash: 20ee69654a6b19365c9b7c46e1fa11e102168365
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309343"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Vytvoření aktivační události, která běží v reakci na událost kanálu

Tento článek popisuje triggery založené na události, vytvořené v kanálů Data Factory.

Architektura založená na událostech (EDA) je běžný vzor integrace dat, která zahrnuje produkčního prostředí, detekce, spotřebě a reakce na události. Scénáře integrace dat často vyžadují zákazníci služby Data Factory pro aktivaci kanály na základě událostí. Data Factory je teď integrovaná s [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), který umožní aktivaci kanály pro událost.

Pro zavedení 10 minut a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integrace popsaných v tomto článku, závisí na [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Ujistěte se, že vaše předplatné je zaregistrované u poskytovatele prostředků služby Event Grid. Další informace najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/resource-manager-supported-services.md#portal).

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

### <a name="create-a-new-event-trigger"></a>Vytvořit novou aktivační událost

Typické události je určení souboru nebo odstranění souboru, ve vašem účtu úložiště Azure. Můžete vytvořit trigger, který odpovídá této události ve vašem kanálu služby Data Factory.

> [!NOTE]
> Tato integrace podporuje pouze účty úložiště verze 2 (pro obecné účely).

![Vytvořit novou aktivační proceduru události](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Konfigurace aktivační události

S **cesta objektu Blob začíná** a **cesta objektu Blob končí** vlastnosti, můžete zadat, kontejnery, složky a názvy objektů blob, pro které chcete přijímat události. Můžete použít různé vzorce pro obě **začíná cesta objektu Blob** a **končí cesta objektu Blob** vlastnosti, jak ukazují příklady dále v tomto článku. Nejméně jednu z těchto vlastností je povinný.

![Konfigurace aktivační události](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Vyberte typ aktivační události

Jakmile dorazí soubor v umístění úložiště a vytvořit odpovídající objekt blob, tato událost aktivuje a spuštění kanálu služby Data Factory. Můžete vytvořit aktivační událost, která bude reagovat na události vytvoření objektu blob, události odstranění objektů blob nebo obou událostí v kanálů Data Factory.

![Typ aktivační události vyberte jako událost](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mapování vlastnosti aktivační události na parametry kanálu

Jakmile se spustí aktivační procedura událostí pro konkrétní objekt blob, událost zaznamená název složky a cesta k souboru objektu blob do vlastností `@triggerBody().folderPath` a `@triggerBody().fileName`. V kanálu používat hodnoty těchto vlastností, je nutné mapovat vlastnosti k parametrům kanálu. Po mapování vlastnosti na parametry, můžete přistupovat k hodnotám nezachytává aktivační události prostřednictvím `@pipeline().parameters.parameterName` výrazu v rámci kanálu.

![Mapování vlastností pro parametry kanálu](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Například v předchozím snímku obrazovky. aktivační událost konfigurován tak, aby se aktivují, když cestu objektu blob s koncovkou `.csv` se vytvoří v účtu úložiště. V důsledku toho při objekt blob se `.csv` rozšíření se vytvoří kdekoli v účtu úložiště `folderPath` a `fileName` vlastnosti zachycení umístění nový objekt blob. Například `@triggerBody().folderPath` má hodnotu jako `/containername/foldername/nestedfoldername` a `@triggerBody().fileName` má hodnotu jako `filename.csv`. Tyto hodnoty jsou namapovány v příkladu parametry kanálu `sourceFolder` a `sourceFile`. Můžete je použít v celém kanálu `@pipeline().parameters.sourceFolder` a `@pipeline().parameters.sourceFile` v uvedeném pořadí.

## <a name="json-schema"></a>Schéma JSON

Následující tabulka obsahuje přehled elementů schématu souvisejících s triggery založené na události:

| **JSON Element** | **Popis** | **Typ** | **Povolené hodnoty** | **Vyžaduje** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Obor** | Azure Resource Manageru ID prostředku účtu úložiště. | Řetězec | ID Azure Resource Manageru | Ano |
| **Události** | Typ události, které způsobují tento aby trigger provedl akci. | Pole    | Microsoft.Storage.BlobCreated Microsoft.Storage.BlobDeleted | Ano, libovolnou kombinaci. |
| **blobPathBeginsWith** | Cesta objektu blob musí začínat vzor pro aby trigger provedl akci k dispozici. Například "/ záznamy/objekty BLOB nebo prosince /" se pouze aktivoval aktivační událost pro objekty BLOB ve složce dne v kontejneru záznamy. | Řetězec   | | Musí být zadaná aspoň jednu z těchto vlastností: blobPathBeginsWith blobPathEndsWith. |
| **blobPathEndsWith** | Cesta objektu blob musí končit vzor pro aby trigger provedl akci k dispozici. Například 'december/boxes.csv' bude pouze aktivoval aktivační událost pro objekty BLOB s názvem pole v složku dne. | Řetězec   | | Musí být zadaná aspoň jednu z těchto vlastností: blobPathBeginsWith blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Příklady triggery založené na události

Tato část obsahuje příklady nastavení založeného na událostech aktivační události.

-   **Cesta k objektu BLOB začíná**("/ containername /") – přijímá události pro všechny objekty blob v kontejneru.
-   **Cesta k objektu BLOB začíná**("/ containername/objektů BLOB/foldername") – přijímá události pro všechny objekty BLOB v kontejneru containername a název_složky složky. Je také možné odkazovat podsložku; například "/ containername/objektů BLOB/název_složky/subfoldername /".
-   **Cesta k objektu BLOB začíná**("/ containername/blobs/foldername/file.txt") – přijímá události pro objekt blob s názvem soubor.txt ve složce název_složky v kontejneru containername.
-   **Cesta k objektu BLOB končí**("soubor.txt") – přijme události pro objekt blob s názvem soubor.txt v jakékoli cestě.
-   **Cesta k objektu BLOB končí**("/ containername/blobs/file.txt") – přijímá události pro objekt blob s názvem soubor.txt v kontejneru containername.
-   **Cesta k objektu BLOB končí**("foldername/file.txt") – přijme události pro objekt blob s názvem soubor.txt v název_složky složce v kontejneru.

> [!NOTE]
> Je nutné zahrnout `/blobs/` segment cesty pokaždé, když zadáte kontejner a složku, kontejner a složku souboru nebo kontejneru a soubor.

## <a name="next-steps"></a>Další postup
Podrobné informace o aktivačních událostech najdete v tématu [spouštění kanálů a triggery](concepts-pipeline-execution-triggers.md#triggers).
