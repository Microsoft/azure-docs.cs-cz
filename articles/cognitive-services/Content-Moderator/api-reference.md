---
title: Odkaz na rozhraní API – moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o různých moderování obsahu a recenzování api pro content moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757382"
---
# <a name="content-moderator-api-reference"></a>Odkaz na rozhraní API pro moderátora obsahu

S virtuálními řešeními pro moderátora obsahu Azure můžete začít následujícími způsoby:

- Na webu Azure Portal [se přihlaste k odběru rozhraní Content Moderator API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Viz [Vyzkoušet moderátora obsahu na webu,](quick-start.md) kde se zaregistrujete pomocí [nástroje Kontrola moderátora obsahu](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>Rozhraní API pro moderování

K nastavení pracovních postupů po moderování můžete použít následující api moderátora obsahu.

| Popis | Odkaz |
| -------------------- |-------------|
| **Rozhraní API pro moderování obrázků**<br /><br />Skenujte obrázky a detekujte potenciální obsah pro dospělé a pikantní obsah pomocí značek, skóre spolehlivosti a dalších extrahovaných informací. <br /><br />Tyto informace slouží k publikování, odmítnutí nebo kontrole obsahu v pracovním postupu po moderování. <br /><br />| [Odkaz na rozhraní API moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Odkaz na rozhraní API moderování obrázků")   |
| **Rozhraní API pro moderování textu**<br /><br />Naskenovat textový obsah. Jsou vráceny vulgární výrazy a osobní údaje. <br /><br />Tyto informace slouží k publikování, odmítnutí nebo kontrole obsahu v pracovním postupu po moderování.<br /><br /> | [Odkaz na rozhraní API moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Odkaz na rozhraní API moderování textu")   |
| **Rozhraní API pro moderování videa**<br /><br />Skenujte videa a detekujte potenciální obsah pro dospělé a pikantní obsah. <br /><br />Tyto informace slouží k publikování, odmítnutí nebo kontrole obsahu v pracovním postupu po moderování.<br /><br /> | [Přehled rozhraní API pro moderování videa](video-moderation-api.md "Přehled rozhraní API pro moderování videa")   |
| **Rozhraní API pro správu seznamů**<br /><br />Vytvořte a spravujte vlastní seznamy vylučovacích nebo zahrnutí obrázků a textu. Pokud je tato možnost povolena, operace **Obraz – shoda** a text – **obrazovka** nepřibližují obsah odeslaného obsahu s vašimi vlastními seznamy. <br /><br />Z důvodu efektivity můžete přeskočit krok moderování založené na strojovém učení.<br /><br /> | [Odkaz na rozhraní API pro správu seznamů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Odkaz na rozhraní API pro správu seznamů")   |

## <a name="review-apis"></a>Recenze rozhraní API

Recenzní api mají následující součásti:

| Popis | Odkaz |
| -------------------- |-------------|
| **Úlohy**<br /><br /> Zahajte pracovní postupy pro kontrolu a kontrolu moderování pro obraz i textový obsah. Úloha moderování prohledá váš obsah pomocí rozhraní API pro moderování obrázků a rozhraní API pro moderování textu. Úlohy moderování používají definované a výchozí pracovní postupy ke generování recenzí. <br /><br />Poté, co lidský moderátor zkontroluje automaticky přiřazené značky a data předpovědi a odešle rozhodnutí o moderování obsahu, rozhraní RECENZNÍ ROZHRANÍ API odešle všechny informace do koncového bodu rozhraní API.<br /><br /> | [Odkaz na pracovní pozici](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Odkaz na pracovní pozici")   |
| **Recenze**<br /><br />Pomocí nástroje Revize můžete přímo vytvářet recenze obrázků nebo textu pro lidské moderátory.<br /><br /> Poté, co lidský moderátor zkontroluje automaticky přiřazené značky a data předpovědi a odešle rozhodnutí o moderování obsahu, rozhraní RECENZNÍ ROZHRANÍ API odešle všechny informace do koncového bodu rozhraní API.<br /><br /> | [Odkaz na recenzi](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Odkaz na recenzi")   |
| **Pracovní postupy**<br /><br />Vytvořte, aktualizujte a získejte podrobnosti o vlastních pracovních postupech, které váš tým vytvoří. Pracovní postupy definujete pomocí nástroje Revize. <br /> <br />Pracovní postupy obvykle používají Content Moderator, ale mohou také používat určitá jiná rozhraní API, která jsou k dispozici jako konektory v nástroji revize.<br /><br /> | [Odkaz na pracovní postup](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Odkaz na pracovní postup")   |