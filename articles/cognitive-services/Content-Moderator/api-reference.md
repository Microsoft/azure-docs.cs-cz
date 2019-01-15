---
title: Reference k rozhraní API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Další informace o různých moderování obsahu a projděte si rozhraní API pro Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: reference
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4a64b2f7ce54d5ace26ad18c0ca1a9facb66245e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266956"
---
# <a name="content-moderator-api-reference"></a>Referenční informace k rozhraní API Content Moderatoru

Asi začít pracovat s Azure Content Moderator API následujícími způsoby: (Viz také [Správa přihlašovacích údajů](review-tool-user-guide/credentials.md).)

- Na webu Azure Portal [předplatit rozhraní API Content Moderatoru](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Zaregistrovat [nástroj pro kontrolu Content Moderator](https://contentmoderator.cognitive.microsoft.com/). Zobrazit [zkuste Content Moderator na webu](quick-start.md).

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Nastavení pracovního postupu po moderování můžete použít následující rozhraní API Content Moderatoru.

| Popis | Referenční informace |
| -------------------- |-------------|
| **Rozhraní API pro moderování obrázků**<br /><br />Kontrola imagí a detekovat potenciální obsah pro dospělé nebo pikantního pomocí značky, skóre spolehlivosti a dalších extrahovaných informací. <br /><br />Tyto informace slouží k publikování, zamítnutí nebo zkontrolovat obsah pracovního postupu po moderování. <br /><br />| [Reference k rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "reference k rozhraní API pro moderování obrázků")   |
| **Rozhraní API pro moderování textu**<br /><br />Prohledejte obsah textu. Podmínky vulgárních výrazů a identifikovatelné osobní údaje (PII) jsou vráceny. <br /><br />Tyto informace slouží k publikování, zamítnutí nebo zkontrolovat obsah pracovního postupu po moderování.<br /><br /> | [Reference k rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "reference k rozhraní API pro moderování textu")   |
| **Moderování videa rozhraní API**<br /><br />Kontrola videa a detekovat potenciální obsah pro dospělé nebo pikantního. <br /><br />Tyto informace slouží k publikování, zamítnutí nebo zkontrolovat obsah pracovního postupu po moderování.<br /><br /> | [Přehled rozhraní Video API pro moderování](video-moderation-api.md "přehled rozhraní API pro moderování videa")   |
| **Seznam rozhraní API pro správu**<br /><br />Vytvářejte a spravujte vlastní vyloučení nebo zahrnutí seznamy obrázků a textu. Pokud je povoleno, **obrázek – odpovídat** a **Text – obrazovky** provést operace přibližné shody obsah odeslaný základě vlastních seznamů. <br /><br />Účinnost můžete přeskočit krok počítače založené na učení moderování.<br /><br /> | [Referenční dokumentace rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "referenční dokumentace rozhraní API pro správu seznamu")   |

## <a name="review-api"></a>Revize rozhraní API

Rozhraní API pro kontrolu má následující komponenty:

| Popis | Referenční informace |
| -------------------- |-------------|
| **Úlohy**<br /><br /> Zahájení kontroly a kontrola moderování pracovních postupů pro obsah obrázků a textu. Moderování úlohy kontroluje váš obsah s použitím rozhraní API pro moderování obrázků a rozhraní API pro moderování textu. Moderování úlohy použijte definovanou a výchozí pracovní postupy pro generování kontroly. <br /><br />Po lidské moderátory zkontroluje automaticky přiřazená značky a data předpovědí a odeslání moderování obsahu rozhodnutí, odešle rozhraní API pro kontrolu všechny informace do vašeho koncového bodu rozhraní API.<br /><br /> | [Referenční dokumentace úlohy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "referenční dokumentace úlohy")   |
| **Revize**<br /><br />Pomocí nástroje pro recenze přímo vytvořit image nebo text kontroly pro lidské moderátory.<br /><br /> Po lidské moderátory zkontroluje automaticky přiřazená značky a data předpovědí a odeslání moderování obsahu rozhodnutí, odešle rozhraní API pro kontrolu všechny informace do vašeho koncového bodu rozhraní API.<br /><br /> | [Projděte si referenční](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "zkontrolujte odkaz")   |
| **Pracovní postupy**<br /><br />Vytvořit, aktualizovat a získat podrobné informace o vlastních pracovních postupů, které váš tým vytvoří. Můžete definovat pracovní postupy pomocí nástroje pro recenze. <br /> <br />Pracovní postupy, obvykle pomocí Content Moderatoru, ale můžete také použít některé další rozhraní API, která jsou k dispozici jako konektory v nástroj pro recenze.<br /><br /> | [Odkaz na pracovní postup](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "odkaz na pracovní postup")   |


