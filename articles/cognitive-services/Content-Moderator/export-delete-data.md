---
title: Export nebo odstranit data v obsahu moderátora - kognitivní služeb Azure | Microsoft Docs
description: Zjistěte, jak k exportu nebo odstranit data v obsahu moderátora.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343695"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Export nebo odstranit data uživatele v obsahu moderátora

Obsahu moderátora shromažďuje data uživatele pro provoz služby, ale zákazníci máte plnou kontrolu nad zobrazení nebo odstranění svá data pomocí [uživatelského rozhraní zkontrolujte](http://contentmoderator.cognitive.microsoft.com/) a [rozhraní API](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o tom, jak exportovat a odstranění dat uživatele v obsahu moderátora najdete v následující tabulce.

| Data | Operace exportu | Operace odstranění |
| ---- | ---------------- | ---------------- |
| Informace o účtu (předplatné klíče) | neuvedeno | Odstranit pomocí portálu Azure (předplatných Azure). Nebo použijte **odstranit Team** tlačítka na [uživatelského rozhraní zkontrolujte](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Bitové kopie pro vlastní párování | [Získání ID obrazů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Image jsou uložené ve formátu otisk proprietární a neexistuje žádný způsob, jak extrahovat skutečné bitové kopie. | [Odstraňte všechny image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Nebo odstranit prostředek obsahu moderátora pomocí portálu Azure. |
| Podmínky pro vlastní párování | [Získat všechny podmínky](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Odstranit všechny podmínky](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Nebo odstranit prostředek obsahu moderátora pomocí portálu Azure. |
| Značky | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro jednotlivé značky na stránce nastavení kontrolní značka uživatelského rozhraní. Nebo použijte **odstranit Team** tlačítka na [uživatelského rozhraní zkontrolujte](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |
| Recenze | [Získat revize](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Použití **odstranit Team** tlačítka na [zkontrolujte uživatelského rozhraní](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu.
| Uživatelé | neuvedeno | Použití **odstranit** ikony, které jsou k dispozici pro každého uživatele v [zkontrolujte uživatelského rozhraní](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. Nebo použijte **odstranit Team** tlačítka na [uživatelského rozhraní zkontrolujte](http://contentmoderator.cognitive.microsoft.com/) stránku nastavení týmu. |

