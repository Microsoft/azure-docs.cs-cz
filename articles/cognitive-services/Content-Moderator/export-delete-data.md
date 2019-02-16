---
title: Exportovat nebo odstraňovat data - Content Moderatoru
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak exportovat nebo odstraňovat data v Content Moderatoru.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 15a59bbdc4c93202f8906689100c24ba713ee487
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311252"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportovat nebo odstraňovat data uživatele v Content Moderatoru

Content Moderator shromažďuje data uživatele pro provoz služby, ale zákazníci mají plnou kontrolu nad zobrazení, exportovat a odstranění jejich dat pomocí [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) a [moderování a rozhraní API pro kontrolu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o tom, jak exportovat a odstranit data uživatelů v Content Moderatoru najdete v následující tabulce.

| Data | Operace exportu | Operace odstranění |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | neuvedeno | Odstranit pomocí webu Azure portal (předplatných Azure). Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Bitové kopie pro vlastní porovnávání | Volání [Get image API ID](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Image se ukládají ve formátu otisk proprietární a neexistuje žádný způsob, jak extrahovat skutečné bitové kopie. | Volání [odstranit všechny Image API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Nebo odstranění prostředku Content Moderator pomocí webu Azure portal. |
| Podmínky pro vlastní porovnávání | Licence CAL [všechny podmínky rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Volání [odstranit všechny podmínky rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Nebo odstranění prostředku Content Moderator pomocí webu Azure portal. |
| Značky | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro jednotlivé značky na stránce Nastavení kontroly značky uživatelského rozhraní. Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Recenze | Volání [Get revizi rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Použití **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu.
| Uživatelé | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro každého uživatele v [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](https://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |

