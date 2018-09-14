---
title: Exportovat nebo odstraňovat data v Content Moderator – Azure Cognitive Services | Dokumentace Microsoftu
description: Zjistěte, jak exportovat nebo odstraňovat data v Content Moderatoru.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: fb496837560fe28f1a2e53d8c4ca67e23ada8f64
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576795"
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

