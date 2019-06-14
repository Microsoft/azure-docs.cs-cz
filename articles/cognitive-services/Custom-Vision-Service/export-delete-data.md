---
title: Exportovat nebo odstraňovat data – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak exportovat nebo odstraňovat data ve službě Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: e662e61a9df45cf3d57d5698337a26b7b8fc55a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605477"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Export nebo odstranění údajů uživatele ve službě Custom Vision

Shromažďuje data uživatele pro provoz služby Custom Vision, ale zákazníci mají plnou kontrolu nad zobrazení, exportu a odstranění dat s využitím Custom Vision [školení API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Informace o exportu a odstranění údajů uživatele ve službě Custom Vision, najdete v následující tabulce.

| Data | Operace exportu | Operace DELETE |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraňte pomocí webu Azure portal (předplatných Azure). Nebo pomocí tlačítka "Odstranit svůj účet" na stránce nastavení CustomVision.ai (předplatné účet Microsoft) | 
| Podrobnosti iterace | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti výkonu iterace | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Seznam iterací | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty a podrobnosti o projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) a [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrázek značky | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) a [gettags slouží k načtení](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrázky | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (obsahuje identifikátor uri pro stažení bitové kopie) a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (obsahuje identifikátor uri pro stažení bitové kopie) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportované modelů | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraní při odstranění účtu |
