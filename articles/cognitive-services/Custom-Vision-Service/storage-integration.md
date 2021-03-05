---
title: Integrace úložiště Azure pro oznámení a zálohování modelu
titleSuffix: Azure Cognitive Services
description: Naučte se integrovat službu Azure Storage pro příjem nabízených oznámení při učení nebo exportu modelů Custom Vision. Můžete také uložit zálohu exportovaných modelů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d889eab429b56a9f4e01684e03c67d394d33472b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178010"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integrace úložiště Azure pro oznamování a zálohování

Můžete integrovat svůj Custom Vision projekt s frontou služby Azure Blob Storage a získat nabízená oznámení o aktivitě projektu školení/exportu a záložní kopie publikovaných modelů. Tato funkce je užitečná k tomu, abyste se vyhnuli průběžnému dotazování služby na výsledky v případě, že běží dlouho. Místo toho můžete do pracovního postupu integrovat oznámení fronty úložiště.

V této příručce se dozvíte, jak používat rozhraní REST API s kudrlinkou. K vystavování požadavků můžete použít také službu požadavku HTTP, jako je například post.

> [!NOTE]
> Nabízená oznámení závisí na volitelném parametru _notificationQueueUri_ v rozhraní **CreateProject** API a zálohy modelů vyžadují, abyste také použili volitelný parametr _exportModelContainerUri_ . Tato příručka bude používat pro celou sadu funkcí.

## <a name="prerequisites"></a>Požadavky

- Prostředek Custom Vision v Azure. Pokud ho nemáte, pokračujte na Azure Portal a [vytvořte nový prostředek Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Tato funkce v současné době nepodporuje prostředek služby pro rozpoznávání (vše v jednom klíči).
- Účet Azure Storage s kontejnerem objektů BLOB. Pokud potřebujete s tímto krokem, postupujte podle [cvičení 1 Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) .
* [PowerShell verze 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)nebo podobná aplikace příkazového řádku.

## <a name="set-up-azure-storage-integration"></a>Nastavení integrace služby Azure Storage

V Azure Portal klikněte na prostředek školení Custom Vision, vyberte stránku **Identita** a povolte spravovanou identitu přiřazenou systémem.

V dalším kroku přejdete na prostředek úložiště v Azure Portal. Přejděte na stránku **řízení přístupu (IAM)** a přidejte přiřazení role pro každou funkci integrace:
* Vyberte svůj prostředek školení Custom Vision a přiřaďte roli **Přispěvatel dat objektů BLOB úložiště** , pokud plánujete použít funkci zálohování modelu. 
* Pak vyberte svůj prostředek školení Custom Vision a přiřaďte **přispěvatele dat fronty úložiště** , pokud chcete používat funkci fronty oznámení.

> [!div class="mx-imgBorder"]
> ![Stránka pro přidání přiřazení role účtu úložiště](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Získat adresy URL pro integraci

V dalším kroku získáte adresy URL, které umožní vašemu Custom Vision prostředkům přístup k těmto koncovým bodům.

Pro adresu URL integrace fronty oznámení klikněte na stránku **fronty** svého účtu úložiště, přidejte novou frontu a uložte její adresu URL do dočasného umístění.

> [!div class="mx-imgBorder"]
> ![Stránka fronty úložiště Azure](./media/storage-integration/queue-url.png) 

V poli Adresa URL integrace zálohování modelu otevřete stránku **kontejnery** svého účtu úložiště a vytvořte nový kontejner. Pak ho vyberte a pak přejít na stránku **vlastností** . Zkopírujte adresu URL do dočasného umístění.
 
> [!div class="mx-imgBorder"]
> ![Stránka vlastností kontejneru úložiště Azure](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integrace Custom Visionho projektu

Teď, když máte integrační adresy URL, můžete vytvořit nový Custom Vision projekt, který integruje funkce Azure Storage. Můžete také aktualizovat existující projekt a přidat funkce.

### <a name="create-new-project"></a>Vytvoření nového projektu

Při volání rozhraní API [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) přidejte volitelné parametry _exportModelContainerUri_ a _notificationQueueUri_. Přiřaďte hodnoty URL, které jste získali v předchozí části. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Pokud obdržíte `200/OK` odpověď, znamená to, že se adresy URL úspěšně nastavily. V odpovědi JSON by se měly zobrazit i vaše hodnoty URL:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Aktualizovat existující projekt

Chcete-li aktualizovat existující projekt pomocí integrace funkcí služby Azure Storage, zavolejte rozhraní API [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) pomocí ID projektu, který chcete aktualizovat. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Nastavte text žádosti ( `body` ) na následující formát JSON a vyplňte příslušné hodnoty pro _ExportModelContainerUri_ a _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Pokud obdržíte `200/OK` odpověď, znamená to, že se adresy URL úspěšně nastavily.

## <a name="verify-the-connection"></a>Ověření připojení 

Vaše volání rozhraní API v předchozí části by už mělo aktivovat nové informace v účtu úložiště Azure. 

V určeném kontejneru by měl být testovací objekt BLOB ve složce **CustomVision-TestPermission** . Tento objekt BLOB bude existovat jenom dočasně.

Ve vaší frontě oznámení by se měla zobrazit zkušební oznámení v následujícím formátu:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Získat oznámení o událostech

Až budete připraveni, zavolejte rozhraní [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API na svůj projekt a proveďte běžnou výukovou operaci.

V oznamovací frontě úložiště obdržíte oznámení po dokončení školení:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`Pole může být buď `"TrainingCompleted"` nebo `"TrainingFailed"` . Toto `"iterationId"` pole je ID trained model.

## <a name="get-model-export-backups"></a>Získat zálohy exportu modelu

Až budete připraveni, zavolejte rozhraní [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API, které vyexportuje trained model do určené platformy.

V určeném kontejneru úložiště se zobrazí záložní kopie exportovaného modelu. Název objektu BLOB bude mít tento formát:

```
{projectId} - {iterationId}.{platformType}
```

Až se export dokončí, obdržíte také oznámení ve vaší frontě. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`Pole může být buď `"ExportCompleted"` nebo `"ExportFailed"` . `"modelUri"`Pole bude obsahovat adresu URL záložního modelu uloženého ve vašem kontejneru za předpokladu, že jste na začátku integrovaná oznámení fronty. Pokud jste to neudělali, `"modelUri"` pole zobrazí adresu URL SAS pro objekt BLOB modelu Custom Vision.

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak kopírovat a přesouvat projekt mezi Custom Vision prostředky. Dále si Prozkoumejte referenční dokumentaci rozhraní API, abyste viděli, co můžete s Custom Vision dělat.
* [REST API Referenční dokumentace (školení)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API Referenční dokumentace (předpověď)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)
