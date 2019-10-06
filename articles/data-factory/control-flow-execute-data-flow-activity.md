---
title: Aktivita toku dat v Azure Data Factory | Microsoft Docs
description: Jak spouštět toky dat z kanálu služby Data Factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: makromer
ms.openlocfilehash: 7db410e97046b6d251eb73e754e40eab09a2ee64
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981809"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Aktivita toku dat v Azure Data Factory

Aktivitu toku dat můžete použít k transformaci a přesunutí dat prostřednictvím mapování toků dat. Pokud s toky dat začínáte, přečtěte si téma [mapování toku dat – přehled](concepts-data-flow-overview.md)

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
toku dat | Odkaz na prováděný tok dat | DataFlowReference | Ano
integrationRuntime | Výpočetní prostředí, na kterém se tok dat spouští | IntegrationRuntimeReference | Ano
Příprava. linkedService | Pokud používáte zdroj dat nebo jímku SQL DW, účet úložiště, který se používá pro základní fázování | LinkedServiceReference | Pouze v případě, že tok dat čte nebo zapisuje do SQL datového skladu
Příprava. folderPath | Pokud používáte zdroj dat nebo jímku SQL DW, cesta ke složce v účtu BLOB Storage se používá pro základní fázování. | Řetězec | Pouze v případě, že tok dat čte nebo zapisuje do SQL datového skladu

![Spuštění](media/data-flow/activity-data-flow.png "toku dat spuštění") toku dat

### <a name="data-flow-integration-runtime"></a>Prostředí Integration runtime toku dat

Vyberte, který Integration Runtime se má použít pro spuštění aktivity toku dat. Ve výchozím nastavení Data Factory používat prostředí Azure Integration runtime se čtyřmi jádry a bez TTL (Time to Live). Tento IR má pro výpočetní typ pro obecné účely a běží ve stejné oblasti jako vaše továrna. Můžete vytvářet vlastní prostředí Azure Integration runtime, která definují konkrétní oblasti, výpočetní typ, počty jader a hodnotu TTL pro spuštění aktivity toku dat.

V případě spuštění kanálu je cluster clusterem úloh, který trvá několik minut, než se spustí spuštění. Pokud není zadána hodnota TTL, je při každém spuštění kanálu vyžadován tento čas spuštění. Zadáte-li hodnotu TTL, zůstane aktivní fond clusterů aktivní po dobu zadanou po posledním spuštění, což bude mít za následek kratší dobu spouštění. Například pokud máte hodnotu TTL 60 minut a za každou hodinu spustíte tok dat, fond clusterů zůstane aktivní. Další informace najdete v tématu [prostředí Azure Integration runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration runtime")

> [!NOTE]
> Výběr Integration Runtime v aktivitě toku dat se vztahuje pouze na *aktivované spouštění* vašeho kanálu. Ladění kanálu pomocí datových toků běží v clusteru zadaném v relaci ladění.

### <a name="polybase"></a>PolyBase

Pokud používáte Azure SQL Data Warehouse jako jímku nebo zdroj, musíte zvolit pracovní umístění pro zatížení základní dávky. Základem je hromadné načtení dávkového načítání namísto načítání datových řádků po řádku. Základce výrazně zkracuje dobu načítání do SQL datového skladu.

## <a name="parameterizing-data-flows"></a>Toky dat Parametrizace

### <a name="parameterized-datasets"></a>Parametrizované datové sady

Pokud datový tok používá parametrizované datové sady, nastavte hodnoty parametrů na kartě **Nastavení** .

![Spustit parametry toku dat](media/data-flow/params.png "")

### <a name="parameterized-data-flows"></a>Parametrizované toky dat

Pokud je tok dat parametrizovaný, nastavte dynamické hodnoty parametrů toku dat na kartě **parametry** . K přiřazení hodnot parametrů Dynamic nebo Literal můžete použít jazyk výrazu kanálu ADF (jenom pro řetězcové typy) nebo jazyk výrazu toku dat. Další informace najdete v tématu [parametry toku dat](parameters-data-flow.md).

(media/data-flow/parameter-example.png "Příklad") spuštění ukázkového parametru ![toku dat]

## <a name="pipeline-debug-of-data-flow-activity"></a>Ladění kanálu aktivity toku dat

Chcete-li spustit ladicí kanál spuštěný s aktivitou toku dat, je nutné přepnout na režim ladění toku dat prostřednictvím posuvníku **ladění toku dat** na horním panelu. Režim ladění umožňuje spustit tok dat proti aktivnímu clusteru Spark. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

Tlačítko ![ladit tlačítko ladit](media/data-flow/debugbutton.png "")

Ladicí kanál běží na aktivním ladicím clusteru, nikoli v prostředí Integration runtime zadaném v nastavení aktivity toku dat. Můžete zvolit prostředí ladění COMPUTE při spuštění režimu ladění.

## <a name="monitoring-the-data-flow-activity"></a>Monitorování aktivity toku dat

Aktivita toku dat má speciální prostředí pro monitorování, ve kterém můžete zobrazit oddíly, čas fáze a informace o datových řádcích. Otevřete podokno monitorování pomocí ikony brýlí v části **Akce**. Další informace najdete v tématu [monitorování toků dat](concepts-data-flow-monitoring.md).

## <a name="next-steps"></a>Další kroky

Viz aktivity toku řízení podporované Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
