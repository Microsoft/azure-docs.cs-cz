---
title: Exportovat nebo odstraňovat data - Content Moderatoru
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak exportovat nebo odstraňovat data v Content Moderatoru.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e671fa9735406ec425b105bb121d3a029ef19dc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227227"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportovat nebo odstraňovat data uživatele v Content Moderatoru

Content Moderator shromažďuje data uživatele pro provoz služby, ale zákazníci mají plnou kontrolu nad zobrazení, exportu a odstranění jejich dat pomocí [uživatelského rozhraní revize](http://contentmoderator.cognitive.microsoft.com/) a [rozhraní API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o tom, jak exportovat a odstranit data uživatelů v Content Moderatoru najdete v následující tabulce.

| Data | Operace exportu | Operace odstranění |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | neuvedeno | Odstranit pomocí webu Azure portal (předplatných Azure). Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Bitové kopie pro vlastní porovnávání | [Získejte ID image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Image se ukládají ve formátu otisk proprietární a neexistuje žádný způsob, jak extrahovat skutečné bitové kopie. | [Odstraňte všechny image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Nebo odstranění prostředku Content Moderator pomocí webu Azure portal. |
| Podmínky pro vlastní porovnávání | [Všechny podmínky](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Odstranit všechny podmínky](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Nebo odstranění prostředku Content Moderator pomocí webu Azure portal. |
| Značky | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro jednotlivé značky na stránce Nastavení kontroly značky uživatelského rozhraní. Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Recenze | [Získání kontroly](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Použití **odstranit Team** tlačítko [uživatelského rozhraní revize](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu.
| Uživatelé | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro každého uživatele v [uživatelského rozhraní revize](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. Nebo použijte **odstranit Team** tlačítko [uživatelského rozhraní revize](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |

