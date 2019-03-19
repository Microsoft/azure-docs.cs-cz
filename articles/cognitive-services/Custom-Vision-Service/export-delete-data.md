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
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 4c395a062b1132710f888cc5a315529db082a805
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850022"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Export nebo odstranění údajů uživatele ve službě Custom Vision

Shromažďuje data uživatele pro provoz služby Custom Vision, ale zákazníci mají plnou kontrolu nad zobrazení, exportu a odstranění dat s využitím Custom Vision [školení API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Informace o exportu a odstranění údajů uživatele ve službě Custom Vision, najdete v následující tabulce.

| Data | Operace exportu | Operace odstranění |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraňte pomocí webu Azure portal (předplatných Azure). Nebo pomocí tlačítka "Odstranit svůj účet" na stránce nastavení CustomVision.ai (předplatné účet Microsoft) | 
| Podrobnosti iterace | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Podrobnosti výkonu iterace | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Seznam iterací | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty a podrobnosti o projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) a [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrázek značky | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) a [gettags slouží k načtení](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Image | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (obsahuje identifikátor uri pro stažení bitové kopie) a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (obsahuje identifikátor uri pro stažení bitové kopie) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportované modelů | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraní při odstranění účtu |
