---
title: Reference k rozhraní API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Seznamte se s různými moderováními obsahu a přečtěte si rozhraní API pro Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72757382"
---
# <a name="content-moderator-api-reference"></a>Reference k rozhraní API Content Moderator

Rozhraní API pro Azure Content Moderator můžete začít následujícími způsoby:

- V Azure Portal [Přihlaste se k odběru rozhraní Content moderator API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Pokud se chcete zaregistrovat pomocí [Nástroje pro kontrolu Content moderator](https://contentmoderator.cognitive.microsoft.com/), přečtěte si téma [vyzkoušet Content moderator na webu](quick-start.md) .

## <a name="moderation-apis"></a>Rozhraní API pro moderování

Pomocí následujících rozhraní API Content Moderator můžete nastavit pracovní postupy po moderování.

| Popis | Odkaz |
| -------------------- |-------------|
| **Rozhraní API pro moderování obrázků**<br /><br />Skenování imagí a detekce potenciálního dospělého a pikantní obsahu pomocí značek, hodnocení spolehlivosti a dalších extrahovaných informací. <br /><br />Tyto informace slouží k publikování, zamítnutí nebo kontrole obsahu v pracovním postupu po moderování. <br /><br />| [Reference k rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Reference k rozhraní API pro moderování obrázků")   |
| **Rozhraní API pro moderování textu**<br /><br />Naskenujte textový obsah. Jsou vraceny výrazy vulgárních výrazů a osobní údaje. <br /><br />Tyto informace slouží k publikování, zamítnutí nebo kontrole obsahu v pracovním postupu po moderování.<br /><br /> | [Reference k rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Reference k rozhraní API pro moderování textu")   |
| **Rozhraní API pro moderování videa**<br /><br />Skenování videí a detekce potenciálního dospělého a pikantní obsahu <br /><br />Tyto informace slouží k publikování, zamítnutí nebo kontrole obsahu v pracovním postupu po moderování.<br /><br /> | [Přehled rozhraní API pro moderování videa](video-moderation-api.md "Přehled rozhraní API pro moderování videa")   |
| **Rozhraní API pro správu seznamu**<br /><br />Vytvářejte a spravujte vlastní seznamy vyloučení a zahrnutí obrázků a textu. Pokud je povoleno, operace se **shodou obrázků** a **textových obrazovek** nepřibližné shody s odeslaným obsahem a vlastními seznamy. <br /><br />V případě efektivity můžete přeskočit krok na základě machine learningu.<br /><br /> | [Reference k rozhraní API pro správu seznamu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Reference k rozhraní API pro správu seznamu")   |

## <a name="review-apis"></a>Recenze rozhraní API

Rozhraní API pro revize mají následující komponenty:

| Popis | Odkaz |
| -------------------- |-------------|
| **Úlohy**<br /><br /> Zahájit kontrolu a zkontrolovat pracovní postupy moderování pro obsah obrázku i textu. Úloha moderování prohledává váš obsah pomocí rozhraní API pro moderování obrázků a rozhraní API pro moderování textu. Úlohy moderování používají pro generování recenzí definované a výchozí pracovní postupy. <br /><br />Po kontrole lidského moderátora se automaticky přiřadí značky a předpověď data a odešlou rozhodnutí o moderování obsahu, rozhraní API pro revize odešle všechny informace do koncového bodu rozhraní API.<br /><br /> | [Odkaz na úlohu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Odkaz na úlohu")   |
| **Recenze**<br /><br />Pomocí nástroje pro kontrolu můžete přímo vytvořit obrázky nebo revize textu pro Moderátoři pro lidské moderování.<br /><br /> Po kontrole lidského moderátora se automaticky přiřadí značky a předpověď data a odešlou rozhodnutí o moderování obsahu, rozhraní API pro revize odešle všechny informace do koncového bodu rozhraní API.<br /><br /> | [Odkaz na kontrolu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Odkaz na kontrolu")   |
| **Pracovní postupy**<br /><br />Vytvářejte, aktualizujte a Získejte podrobné informace o vlastních pracovních postupech, které tým vytvoří. Pracovní postupy definujete pomocí nástroje pro kontrolu. <br /> <br />Pracovní postupy obvykle používají Content Moderator, ale můžou používat i určitá další rozhraní API, která jsou k dispozici jako konektory v nástroji pro revize.<br /><br /> | [Referenční dokumentace pracovního postupu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Referenční dokumentace pracovního postupu")   |