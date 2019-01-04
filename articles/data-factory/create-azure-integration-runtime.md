---
title: Vytvoření prostředí Azure integration runtime ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prostředí Azure integration runtime ve službě Azure Data Factory, který slouží ke kopírování dat a odeslání aktivit transformace.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: f9dfb2dde4c49d9ca167b0f4ea6af28bd1db6872
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013583"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Vytvoření a konfigurace prostředí Azure Integration Runtime
Integration Runtime (IR) je výpočetní infrastruktura, službou Azure Data Factory používá k poskytování možnosti integrace dat v různých síťových prostředích. Další informace o prostředí IR najdete v tématu [prostředí Integration runtime](concepts-integration-runtime.md).

Prostředí IR v Azure nabízí plně spravovanou výpočetní nativně provádět pohyb a rozesílání dat aktivity transformace dat do výpočetních služeb, jako je HDInsight. Je hostován v prostředí Azure a podporuje připojení k prostředkům ve veřejné síti prostředí s veřejně přístupnými koncovými body.

Toto téma představuje, jak můžete vytvořit a nakonfigurovat prostředí Azure Integration Runtime. 

## <a name="default-azure-ir"></a>Výchozí prostředí IR v Azure
Každá datová Továrna má ve výchozím nastavení prostředí Azure IR v back-endu, který podporuje operace na cloudové úložiště dat a výpočetním službám ve veřejné síti. Umístění tohoto prostředí Azure IR je automaticky vyřešit. Pokud **connectVia** není zadána vlastnost v definici propojené služby, výchozí prostředí Azure IR se používá. Potřebujete jenom explicitně vytvořit prostředí Azure IR, když chcete explicitně definovat umístění prostředí IR, nebo pokud byste chtěli prakticky skupině spuštění aktivity v různých IRs pro účely správy. 

## <a name="create-azure-ir"></a>Vytvoření prostředí IR v Azure
Prostředí Integration Runtime je možné vytvořit **Set-AzureRmDataFactoryV2IntegrationRuntime** rutiny Powershellu. Pokud chcete vytvořit prostředí Azure IR, zadejte název, umístění a typ příkazu. Tady je ukázka příkazu k vytvoření prostředí Azure IR nastavena na "V oblasti západní Evropa" umístění:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pro prostředí Azure IR typ musí být nastavený **spravované**. Není nutné zadat podrobnosti výpočetní prostředky, protože to je plně spravovaná Elasticky v cloudu. Zadejte výpočetní podrobnosti, jako je velikost uzlu a uzel počítat, když chcete vytvořit prostředí Azure-SSIS IR. Další informace najdete v tématu [vytvořit a nakonfigurovat prostředí Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Můžete nakonfigurovat stávající prostředí Azure IR do jeho umístění pomocí rutiny Set-AzureRmDataFactoryV2IntegrationRuntime Powershellu. Další informace o umístění prostředí Azure IR najdete v tématu [Úvod do prostředí integration runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Použití prostředí IR v Azure

Po vytvoření prostředí Azure IR, můžete na něj mohli odkazovat v definici propojené služby. Níže je příklad použití prostředí Azure Integration Runtime vytvořili výše z propojená služba Azure Storage:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Další postup
O tom, jak vytvořit další typy prostředí integration runtime naleznete v následujících článcích:

- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md)
 
