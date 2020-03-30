---
title: Vytvoření runtime integrace Azure v Azure Data Factory
description: Zjistěte, jak vytvořit runtime integrace Azure v Azure Data Factory, který se používá ke kopírování dat a odesílání transformačních aktivit.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260758"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak vytvořit a nakonfigurovat prostředí Azure Integration Runtime
Integrační běh (IR) je výpočetní infrastruktura používaná Azure Data Factory k poskytování možností integrace dat v různých síťových prostředích. Další informace o infračerveném zápisu naleznete [v tématu Integration runtime](concepts-integration-runtime.md).

Azure IR poskytuje plně spravované výpočetní prostředky pro nativně provádět přesun y dat a odesílání aktivit transformace dat do výpočetních služeb, jako je HDInsight. Hostuje se v prostředí Azure a podporuje připojení k prostředkům v prostředí veřejné sítě s veřejnými přístupnými koncovými body.

Tento dokument představuje, jak můžete vytvořit a nakonfigurovat Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Výchozí azure ir
Ve výchozím nastavení má každá továrna na data v back-endu Azure IR, který podporuje operace v úložišti cloudových dat a výpočetních službách ve veřejné síti. Umístění tohoto Azure IR je automatické řešení. Pokud vlastnost **connectVia** není zadána v definici propojené služby, použije se výchozí Azure IR. Stačí explicitně vytvořit Azure IR, pokud chcete explicitně definovat umístění infračerveného počítače, nebo pokud chcete prakticky seskupit spuštění aktivity na různých iRs pro účely správy. 

## <a name="create-azure-ir"></a>Vytvoření Azure IR
Integrační runtime lze vytvořit pomocí rutiny **Set-AzDataFactoryV2IntegrationRuntime** PowerShell. Chcete-li vytvořit Azure IR, zadejte název, umístění a zadejte příkaz. Tady je ukázkový příkaz pro vytvoření Azure IR s umístěním nastaveným na "Západní Evropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pro Azure IR musí být typ nastaven na **Spravované**. Není nutné zadat podrobnosti o výpočetních prostředcích, protože je plně spravovaná elasticky v cloudu. Zadejte podrobnosti o výpočetních výkonech, jako je velikost uzlu a počet uzlů, pokud chcete vytvořit Azure-SSIS IR. Další informace naleznete v [tématu Vytvoření a konfigurace služby Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Můžete nakonfigurovat existující Azure IR změnit jeho umístění pomocí Set-AzDataFactoryV2IntegrationRuntime PowerShell rutina. Další informace o umístění Azure IR najdete [v tématu Úvod do integrace runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Použití azure ir

Po vytvoření Azure IR, můžete odkazovat v definici propojené služby. Níže je ukázka toho, jak můžete odkazovat na runtime integrace Azure vytvořené výše z propojené služby Azure Storage:  

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
Následující články o vytvoření dalších typů integračních runrunů:

- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)
 
