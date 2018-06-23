---
title: Referenční dokumentace Azure obsahu rozhraní API moderátora | Microsoft Docs
description: Další informace o obsahu přerušování a zkontrolujte rozhraní API pro moderátora obsah.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 94de9b91cc242e8b7e5479cacab8380adac551f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342425"
---
# <a name="content-moderator-api-reference"></a>Referenční informace k rozhraní API Content Moderatoru

Začínáme s Azure obsahu moderátora rozhraní API, následujícím způsobem: (viz také [správy pověření](review-tool-user-guide/credentials.md).)

- Na portálu Azure [přihlášení k odběru obsahu rozhraní API moderátora](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Zaregistrujte si [nástroj pro kontrolu obsahu moderátora](http://contentmoderator.cognitive.microsoft.com/). V tématu [rychlý Start](quick-start.md).

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Nastavit po přerušování pracovní postupy můžete použít následující obsahu moderátora rozhraní API.

| Popis | Referenční informace |
| -------------------- |-------------|
| **Obrázek přerušování rozhraní API**<br /><br />Kontrola bitové kopie a zjistit potenciální obsah pro dospělé a zájem pomocí značek, spolehlivosti skóre a dalších extrahování informací. <br /><br />Tyto informace slouží k publikování, odmítnout nebo zkontrolujte obsah v pracovním postupu po přerušování. <br /><br />| [Obrázek referenční dokumentace rozhraní API přerušování](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "referenční dokumentace rozhraní API přerušování bitové kopie")   |
| **Text přerušování rozhraní API**<br /><br />Kontrola textového obsahu. Jsou vráceny vulgárnost podmínky a identifikovatelné osobní údaje (PII). <br /><br />Tyto informace slouží k publikování, odmítnout nebo zkontrolujte obsah v pracovním postupu po přerušování.<br /><br /> | [Referenční dokumentace rozhraní API přerušování text](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "referenční dokumentace rozhraní API přerušování textu")   |
| **Video přerušování rozhraní API**<br /><br />Kontrola videa a zjistit potenciální obsah pro dospělé a zájem. <br /><br />Tyto informace slouží k publikování, odmítnout nebo zkontrolujte obsah v pracovním postupu po přerušování.<br /><br /> | [Video s přehledem přerušování API](video-moderation-api.md "přehled API přerušování Video")   |
| **Seznam rozhraní API pro správu**<br /><br />Vytvořit a spravovat vlastní seznamy vyloučení nebo zahrnutí obrázky a text. Pokud je povoleno, **obrázek – odpovídat** a **Text – obrazovky** operations provést přibližné shody obsah odeslaný na vaše vlastní seznamy. <br /><br />Pro efektivitu můžete přeskočit krok machine learning na základě přerušování.<br /><br /> | [Seznam referenční dokumentace rozhraní API pro správu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "referenční dokumentace rozhraní API správy seznamu")   |

## <a name="review-api"></a>Kontrola rozhraní API

Rozhraní API zkontrolujte má následující komponenty:

| Popis | Referenční informace |
| -------------------- |-------------|
| **Úlohy**<br /><br /> Zahájit kontrolu a zkontrolujte přerušování pracovní postupy pro bitové kopie a textového obsahu. Úlohu přerušování kontroluje svůj obsah pomocí rozhraní API přerušování bitové kopie a rozhraní API přerušování Text. Přerušování úlohy použijte definovaný a výchozí pracovní postupy pro vygenerování recenze. <br /><br />Po lidského moderátor přečetli automaticky přiřazená značky a data předpovědi a odeslání obsahu přerušování rozhodnutí, odešle rozhraní API zkontrolujte všechny informace, které váš koncový bod rozhraní API.<br /><br /> | [Referenční dokumentace úlohy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "referenční dokumentace úlohy")   |
| **Recenze**<br /><br />Použijte nástroj zkontrolujte přímo vytvoření bitové kopie nebo textu zkontroluje pro lidské moderátorů.<br /><br /> Po lidského moderátor přečetli automaticky přiřazená značky a data předpovědi a odeslání obsahu přerušování rozhodnutí, odešle rozhraní API zkontrolujte všechny informace, které váš koncový bod rozhraní API.<br /><br /> | [Přečtěte si referenční](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "zkontrolujte odkaz")   |
| **Pracovní postupy**<br /><br />Vytvářet, aktualizovat a získat tak podrobné údaje o vlastní pracovní postupy, které vytvoří tým. Pomocí nástroje kontrolní definujete pracovních postupů. <br /> <br />Pracovní postupy obvykle používat obsahu moderátora, ale můžete také použít určité jiná rozhraní API, které jsou k dispozici jako konektory v nástroji revize.<br /><br /> | [Pracovního postupu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "pracovního postupu")   |


