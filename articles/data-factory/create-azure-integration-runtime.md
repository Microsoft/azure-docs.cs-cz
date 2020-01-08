---
title: Vytvoření prostředí Azure Integration runtime v Azure Data Factory
description: Naučte se vytvářet prostředí Azure Integration runtime v Azure Data Factory, které se používá ke kopírování dat a odesílání transformačních aktivit.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440298"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak vytvořit a nakonfigurovat Azure Integration Runtime
Integration Runtime (IR) je výpočetní infrastruktura, kterou používá Azure Data Factory k tomu, aby poskytovala možnosti integrace dat napříč různými síťovými prostředími. Další informace o technologii IR naleznete v tématu [Integration runtime](concepts-integration-runtime.md).

Azure IR poskytuje plně spravované výpočetní prostředky k nativně provádění přesunu dat a odesílání aktivit transformace dat do výpočetních služeb, jako je HDInsight. Hostuje se v prostředí Azure a podporuje připojení k prostředkům ve veřejném síťovém prostředí s veřejnými dostupnými koncovými body.

Tento dokument popisuje, jak můžete vytvořit a nakonfigurovat Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Výchozí Azure IR
Ve výchozím nastavení má každá datová továrna Azure IR v back-endu, která podporuje operace v cloudových úložištích dat a výpočetní služby ve veřejné síti. Umístění tohoto Azure IR je automaticky vyřešeno. Pokud v definici propojené služby není zadaná vlastnost **connectVia** , použije se výchozí Azure IR. Musíte explicitně vytvořit Azure IR, když chcete explicitně definovat umístění IR, nebo pokud chcete pro účely správy prakticky seskupit provádění aktivit na jiném finančním úřadu. 

## <a name="create-azure-ir"></a>Vytvořit Azure IR
Integration Runtime lze vytvořit pomocí rutiny prostředí PowerShell **set-AzDataFactoryV2IntegrationRuntime** . Chcete-li vytvořit Azure IR, zadejte název, umístění a typ příkazu. Tady je ukázkový příkaz pro vytvoření Azure IR s umístěním nastaveným na "Západní Evropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pro Azure IR musí být typ nastavený na **spravované**. Nemusíte zadávat podrobné informace o výpočetním prostředí, protože je plně spravovaná elastická v cloudu. Určete výpočetní údaje, jako je velikost uzlu a počet uzlů, když chcete vytvořit Azure-SSIS IR. Další informace najdete v tématu [Vytvoření a konfigurace Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Existující Azure IR můžete nakonfigurovat tak, aby změnila umístění pomocí rutiny Set-AzDataFactoryV2IntegrationRuntime prostředí PowerShell. Další informace o umístění Azure IR najdete v tématu [Úvod do prostředí Integration runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Použít Azure IR

Po vytvoření Azure IR můžete na něj odkazovat v definici propojené služby. Níže je ukázka, jak můžete odkazovat na Azure Integration Runtime vytvořené výše z propojené služby Azure Storage:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Další kroky
V následujících článcích najdete informace o tom, jak vytvořit další typy prostředí Integration Runtime:

- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS Integration runtime](create-azure-ssis-integration-runtime.md)
 
