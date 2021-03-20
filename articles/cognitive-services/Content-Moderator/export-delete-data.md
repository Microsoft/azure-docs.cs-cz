---
title: Export nebo odstranění uživatelských dat – Content Moderator
titleSuffix: Azure Cognitive Services
description: Máte plnou kontrolu nad daty. Naučte se, jak zobrazit, exportovat nebo odstranit data v Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 9f74fdc9cd30e1dfbd4df6c94842a9dccb435ef4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913649"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Export nebo odstranění uživatelských dat v Content Moderator

Content Moderator shromažďuje data uživatelů za účelem provozu služby, ale zákazníci mají plnou kontrolu nad tím, že budou data zobrazovat, exportovat a odstraňovat pomocí [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) a [rozhraní API pro moderování a kontrolu](./api-reference.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o tom, jak exportovat a odstranit data uživatelů v Content Moderator, najdete v následující tabulce.

| Data | Operace exportu | Odstranit operaci |
| ---- | ---------------- | ---------------- |
| Informace o účtu (klíče předplatného) | – | Odstraňte pomocí Azure Portal (předplatná Azure). Nebo použijte tlačítko **Odstranit tým** na stránce zkontrolovat nastavení týmu [uživatelského rozhraní](https://contentmoderator.cognitive.microsoft.com/) . |
| Obrázky pro vlastní porovnání | Volejte [rozhraní API získat ID imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Obrázky jsou uloženy v jednosměrné proprietární formě algoritmu hash a neexistuje žádný způsob, jak extrahovat samotné image. | Zavolejte [rozhraní API pro odstranění všech imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Nebo odstraňte prostředek Content Moderator pomocí Azure Portal. |
| Výrazy pro vlastní porovnání | Licence CAL pro [rozhraní get all terms](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Zavolejte [rozhraní API odstranit všechny výrazy](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Nebo odstraňte prostředek Content Moderator pomocí Azure Portal. |
| Značky | – | Použijte ikonu **Odstranit** dostupnou pro každou značku na stránce zkontrolovat nastavení značky uživatelského rozhraní. Nebo použijte tlačítko **Odstranit tým** na stránce zkontrolovat nastavení týmu [uživatelského rozhraní](https://contentmoderator.cognitive.microsoft.com/) . |
| Recenze | Volání [rozhraní get View API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Použijte tlačítko **Odstranit tým** na stránce zkontrolovat nastavení týmu [uživatelského rozhraní](https://contentmoderator.cognitive.microsoft.com/) .
| Uživatelé | – | Použijte ikonu **Odstranit** dostupnou pro každého uživatele na stránce zkontrolovat nastavení týmu [uživatelského rozhraní](https://contentmoderator.cognitive.microsoft.com/) . Nebo použijte tlačítko **Odstranit tým** na stránce zkontrolovat nastavení týmu [uživatelského rozhraní](https://contentmoderator.cognitive.microsoft.com/) . |