---
title: Exportovat nebo odstraňovat data - Content Moderatoru
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak exportovat nebo odstraňovat data v Content Moderatoru.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: 5b6485a4918e86908372287ddc44338be54d309c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245796"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportovat nebo odstraňovat data uživatele v Content Moderatoru

Content Moderator shromažďuje data uživatele pro provoz služby, ale zákazníci mají plnou kontrolu nad zobrazení, exportu a odstranění jejich dat pomocí [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) a [rozhraní API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o tom, jak exportovat a odstranit data uživatelů v Content Moderatoru najdete v následující tabulce.

| Data | Operace exportu | Operace odstranění |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | neuvedeno | Odstranit pomocí webu Azure portal (předplatných Azure). Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Bitové kopie pro vlastní porovnávání | [Získejte ID image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Image se ukládají ve formátu otisk proprietární a neexistuje žádný způsob, jak extrahovat skutečné bitové kopie. | [Odstraňte všechny image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Nebo odstranění prostředku Content Moderator pomocí webu Azure portal. |
| Podmínky pro vlastní porovnávání | [Všechny podmínky](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Odstranit všechny podmínky](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Nebo odstranění prostředku Content Moderator pomocí webu Azure portal. |
| Značky | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro jednotlivé značky na stránce Nastavení kontroly značky uživatelského rozhraní. Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Recenze | [Získání kontroly](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Použití **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu.
| Uživatelé | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro každého uživatele v [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |

