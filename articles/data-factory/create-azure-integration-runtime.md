---
title: Vytvoření prostředí Azure Integration runtime v Azure Data Factory
description: Naučte se vytvářet prostředí Azure Integration runtime v Azure Data Factory, které se používá ke kopírování dat a odesílání transformačních aktivit.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 9615dc358b1c5bed0e48c07c2571ccce05fcdf2e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637203"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak vytvořit a nakonfigurovat Azure Integration Runtime
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) je výpočetní infrastruktura, kterou používá Azure Data Factory k tomu, aby poskytovala možnosti integrace dat napříč různými síťovými prostředími. Další informace o technologii IR naleznete v tématu [Integration runtime](concepts-integration-runtime.md).

Azure IR poskytuje plně spravované výpočetní prostředky k nativně provádění přesunu dat a odesílání aktivit transformace dat do výpočetních služeb, jako je HDInsight. Hostuje se v prostředí Azure a podporuje připojení k prostředkům ve veřejném síťovém prostředí s veřejnými dostupnými koncovými body.

Tento dokument popisuje, jak můžete vytvořit a nakonfigurovat Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Výchozí Azure IR
Ve výchozím nastavení má každá datová továrna Azure IR v back-endu, která podporuje operace v cloudových úložištích dat a výpočetní služby ve veřejné síti. Umístění tohoto Azure IR je automaticky vyřešeno. Pokud v definici propojené služby není zadaná vlastnost **connectVia** , použije se výchozí Azure IR. Musíte explicitně vytvořit Azure IR, když chcete explicitně definovat umístění IR, nebo pokud chcete pro účely správy prakticky seskupit provádění aktivit na jiném finančním úřadu. 

## <a name="create-azure-ir"></a>Vytvořit Azure IR

Chcete-li vytvořit a nastavit Azure IR, můžete použít následující postupy.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Vytvoření Azure IR přes Azure PowerShell
Integration Runtime lze vytvořit pomocí rutiny prostředí PowerShell **set-AzDataFactoryV2IntegrationRuntime** . Chcete-li vytvořit Azure IR, zadejte název, umístění a typ příkazu. Tady je ukázkový příkaz pro vytvoření Azure IR s umístěním nastaveným na "Západní Evropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pro Azure IR musí být typ nastavený na **spravované** . Nemusíte zadávat podrobné informace o výpočetním prostředí, protože je plně spravovaná elastická v cloudu. Určete výpočetní údaje, jako je velikost uzlu a počet uzlů, když chcete vytvořit Azure-SSIS IR. Další informace najdete v tématu [Vytvoření a konfigurace Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Existující Azure IR můžete nakonfigurovat tak, aby změnila umístění pomocí rutiny Set-AzDataFactoryV2IntegrationRuntime PowerShellu. Další informace o umístění Azure IR najdete v tématu [Úvod do prostředí Integration runtime](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Vytvoření Azure IR prostřednictvím uživatelského rozhraní Azure Data Factory
Pomocí následujících kroků můžete vytvořit Azure IR pomocí uživatelského rozhraní Azure Data Factory.

1. Na stránce **Začínáme** v uživatelském rozhraní Azure Data Factory vyberte [kartu spravovat](./author-management-hub.md) z levého podokna.

   ![Tlačítko Správa domovské stránky](media/doc-common-process/get-started-page-manage-button.png)

1. V levém podokně vyberte **modul runtime integrace** a pak vyberte **+ Nový** .

   ![Vytvoření prostředí Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na stránce **instalace prostředí Integration runtime** vyberte **Azure,** v místním prostředí a pak vyberte **pokračovat** . 

1. Na následující stránce vyberte **Azure** a vytvořte Azure IR a pak vyberte **pokračovat** .
   ![Vytvoření prostředí Integration Runtime](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Zadejte název pro Azure IR a vyberte **vytvořit** .
   ![Vytvoření Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Po dokončení vytváření se zobrazí místní oznámení. Na stránce **Integration runtime** se ujistěte, že se v seznamu zobrazuje nově vytvořený IR.

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
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)
