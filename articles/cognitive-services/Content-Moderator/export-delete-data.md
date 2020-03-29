---
title: Export nebo odstranění uživatelských dat – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Máte plnou kontrolu nad svými daty. Přečtěte si, jak zobrazit, exportovat nebo odstranit data v Moderátorovi obsahu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744785"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Export nebo odstranění uživatelských dat v moderátoru obsahu

Content Moderator shromažďuje uživatelská data pro provoz služby, ale zákazníci mají plnou kontrolu nad zobrazením, exportem a odstraněním dat pomocí [nástroje Revize](https://contentmoderator.cognitive.microsoft.com/) a rozhraní API [pro moderování a kontrolu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o exportu a odstranění uživatelských dat v moderátoru obsahu naleznete v následující tabulce.

| Data | Exportní operace | Odstranit operaci |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | Není dostupné. | Odstraňte pomocí portálu Azure (předplatná Azure). Nebo použijte tlačítko **Odstranit tým** na stránce Nastavení týmu [ui. recenze.](https://contentmoderator.cognitive.microsoft.com/) |
| Obrázky pro vlastní párování | Volání [rozhraní API pro získání ID obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Obrázky jsou uloženy v jednosměrném proprietárním formátu hash a neexistuje žádný způsob, jak extrahovat skutečné obrázky. | Volání [odstranit všechny obrázky rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Nebo odstraňte prostředek Content Moderator pomocí portálu Azure. |
| Podmínky pro vlastní párování | Cal [získat všechny termíny API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Volání [Odstranit všechny termíny ROZHRANÍ API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Nebo odstraňte prostředek Content Moderator pomocí portálu Azure. |
| Značky | Není dostupné. | Na stránce Nastavení značky ui kontroly použijte ikonu **Odstranit,** která je k dispozici pro každou značku. Nebo použijte tlačítko **Odstranit tým** na stránce Nastavení týmu [ui. recenze.](https://contentmoderator.cognitive.microsoft.com/) |
| Recenze | Volání do [rozhraní API pro získání recenze](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Tlačítko **Odstranit tým** použijte na stránce Nastavení týmu [ui. recenze.](https://contentmoderator.cognitive.microsoft.com/)
| Uživatelé | Není dostupné. | Na stránce Nastavení týmu [uživatelského rozhraní](https://contentmoderator.cognitive.microsoft.com/) použijte ikonu **Odstranit,** která je k dispozici pro každého uživatele. Nebo použijte tlačítko **Odstranit tým** na stránce Nastavení týmu [ui. recenze.](https://contentmoderator.cognitive.microsoft.com/) |

