---
title: Vytvoření modulu integrace se službou Azure runtime v Azure Data Factory | Microsoft Docs
description: Naučte se vytvořit modul runtime integrace se službou Azure v Azure Data Factory, který slouží ke kopírování dat a odesílání aktivit transformace.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: cb1a263c0a33a291a44e7c60b3c032d7f9dc16a3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054071"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak vytvořit a nakonfigurovat Runtime integrace Azure
Integrační modul Runtime (IR) je na výpočetní infrastruktuře používá k zajištění funkce integrace dat různých prostředích sítě Azure Data Factory. Další informace o IR najdete v tématu [integrace runtime](concepts-integration-runtime.md).

Azure IR poskytuje plně spravovaná výpočetní nativně provést přesun a odesílání dat aktivit transformace dat na výpočetní služby stejně jako HDInsight. Je hostovaná v prostředí Azure a podporuje připojení k prostředkům ve veřejné síti prostředí s veřejné koncové body přístupné.

Tento dokument uvádí, jak můžete vytvořit a nakonfigurovat Runtime integrace Azure. 

## <a name="default-azure-ir"></a>Výchozí Azure reakcí na Incidenty
Ve výchozím nastavení má každý objekt pro vytváření dat Reakcí Azure v back-end, který podporuje operace v cloudu ukládá data a výpočetní služby ve veřejné síti. Umístění této IR Azure je automaticky vyřešit. Pokud **connectVia** vlastnost není zadaný v definici propojené služby, výchozí hodnota se používá Azure reakcí na Incidenty. Potřebujete jenom explicitně vytvořit Reakcí Azure, když chcete explicitně definujte umístění reakcí na Incidenty, nebo pokud byste chtěli prakticky skupiny spuštěních aktivity na jiné finanční úřad za účelem správy. 

## <a name="create-azure-ir"></a>Vytvoření Azure reakcí na Incidenty
Integrace Runtime lze vytvořit pomocí **Set-AzureRmDataFactoryV2IntegrationRuntime** rutiny prostředí PowerShell. Chcete-li vytvořit Reakcí Azure, zadáte název, umístění a typ k příkazu. Zde je ukázka příkazu k vytvoření služby Azure IR umístění nastavena na "Západní Evropa":

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pro Azure Reakcí typ musí být nastavený na **spravované**. Není nutné zadávat výpočetní podrobnosti, protože ho je plně spravovaná pružně v cloudu. Zadejte výpočetní podrobnosti jako velikost uzlu a uzel count, když chcete vytvořit Azure SSIS infračerveného signálu. Další informace najdete v tématu [vytvořit a nakonfigurovat Azure SSIS IR](create-azure-ssis-integration-runtime.md).

Můžete nakonfigurovat existující IR Azure změnit jeho umístění pomocí rutiny Set-AzureRmDataFactoryV2IntegrationRuntime prostředí PowerShell. Další informace o umístění IR Azure najdete v tématu [Úvod do integrace runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Použití Azure reakcí na Incidenty

Po vytvoření služby Azure IR něj mohli odkazovat v definice propojené služby. Níže je ukázka, jak můžete odkazovat modulu Runtime integrace Azure vytvořili výše z propojené služby Azure Storage:  

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
O tom, jak vytvořit jiné typy integrační moduly runtime naleznete v následujících článcích:

- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření modulu runtime integrace Azure SSIS](create-azure-ssis-integration-runtime.md)
 
