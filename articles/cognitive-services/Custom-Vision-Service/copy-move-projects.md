---
title: Kopírování a přesun Custom Vision projektů
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API ExportProject a ImportProject ke kopírování a přesouvání Custom Visionch projektů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 363f0062d316b22019b864972d5a830e4a838b93
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178095"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Kopírování a přesun Custom Visionch projektů

Po vytvoření a školení projektu Custom Vision můžete chtít zkopírovat projekt do jiného prostředku. Například můžete chtít přesunout projekt z vývoje do produkčního prostředí nebo zálohovat projekt na účet v jiné oblasti Azure pro zvýšení zabezpečení dat.

Rozhraní API **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** a **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** tento scénář umožňují, protože byste mohli kopírovat projekty z jednoho Custom Vision účtu do ostatních. V této příručce se dozvíte, jak používat rozhraní REST API s kudrlinkou. K vystavování požadavků můžete použít také službu požadavku HTTP, jako je například post.

## <a name="business-scenarios"></a>Obchodní scénáře

Pokud vaše aplikace nebo firma závisí na použití Custom Vision projektu, doporučujeme zkopírovat model do jiného účtu Custom Vision v jiné oblasti. Pokud dojde k oblastnímu výpadku, máte přístup k projektu v oblasti, kam byl zkopírován.

##  <a name="prerequisites"></a>Požadavky

- Dva prostředky Azure Custom Vision. Pokud je nemáte, můžete přejít na Azure Portal a [vytvořit nový prostředek Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Školicí klíče a adresy URL koncových bodů vašich Custom Visionch prostředků. Tyto hodnoty najdete na kartě **Přehled** prostředku na Azure Portal.
- Vytvořený Custom Vision projekt. Pokyny k tomu, jak to provést, najdete v tématu [sestavení třídění](./getting-started-build-a-classifier.md) .
* [PowerShell verze 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)nebo podobný nástroj příkazového řádku.

## <a name="process-overview"></a>Přehled procesu

Proces kopírování projektu se skládá z následujících kroků:

1. Nejdřív získáte ID projektu ve zdrojovém účtu, který chcete zkopírovat.
1. Pak zavoláte rozhraní API **ExportProject** pomocí ID projektu a školicího klíče vašeho zdrojového účtu. Dostanete dočasný řetězec tokenu.
1. Pak zavoláte rozhraní **ImportProject** API pomocí řetězce tokenu a školicího klíče cílového účtu. Projekt se pak zobrazí v části cílový účet.

## <a name="get-the-project-id"></a>Získat ID projektu

Nejdřív zavolejte **[Getprojects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** , abyste viděli seznam stávajících Custom Vision projektů a jejich ID. Použijte školicí klíč a koncový bod vašeho zdrojového účtu.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Dostanete `200\OK` odpověď se seznamem projektů a jejich metadat v těle. `"id"`Hodnota je řetězec, který se má zkopírovat pro další kroky.

```json
[
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
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Exportovat projekt

Zavolejte **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** pomocí ID projektu a vašeho zdrojového školicího klíče a koncového bodu.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Dostanete `200/OK` odpověď s metadaty k exportovanému projektu a referenčním řetězcem `"token"` . Zkopírujte hodnotu tokenu.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importovat projekt

Zavolejte **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** pomocí cílového školicího klíče a koncového bodu spolu s tokenem reference. Svůj projekt můžete také uvést jako název v novém účtu.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Dostanete `200/OK` odpověď s metadaty o nově importovaném projektu.

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
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak kopírovat a přesouvat projekt mezi Custom Vision prostředky. Dále si Prozkoumejte referenční dokumentaci rozhraní API, abyste viděli, co můžete s Custom Vision dělat.
* [Referenční dokumentace REST API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)