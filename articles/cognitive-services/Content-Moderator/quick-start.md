---
title: 'Rychlý start: Vyzkoušejte si Content Moderator na web – Content Moderator'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete online nástroje pro recenze Content Moderator, k otestování základní funkce Content Moderator bez nutnosti psát jakýkoli kód.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 52c69aee4a8fdf8ad08590113c95ff3de3b0883b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205793"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Rychlý start: Vyzkoušejte si Content Moderator na webu

V tomto rychlém startu použijete online nástroje pro recenze Content Moderator, k otestování základní funkce Content Moderator bez nutnosti psát jakýkoli kód. Pokud chcete integrovat tuto službu do aplikace rychleji, viz další rychlé starty v [další kroky](#next-steps) oddílu.

## <a name="prerequisites"></a>Požadavky

- Webový prohlížeč

## <a name="set-up-the-review-tool"></a>Nastavení nástroje pro recenze
Nástroj pro recenze Content Moderator je webový nástroj, který umožňuje lidské revidující na podporu služby cognitive Services v rozhodování. V tomto průvodci budou procházet krátký proces nastavení nástroje pro recenze tak, abyste viděli, jak funguje služba Content Moderatoru. Přejděte [nástroj pro kontrolu Content Moderator](https://contentmoderator.cognitive.microsoft.com/) webu a zaregistrovat.

![Content Moderator domovské stránky](images/homepage.PNG)

## <a name="create-a-review-team"></a>Vytvořit tým kontroly

Dále vytvořte tým kontroly. Ve scénáři pracovní bude skupina uživatelů, kteří budou ručně zkontrolovat služby moderování rozhodnutí. Teď stačí vytvořit název týmu. Pokud chcete, aby vaši kolegové mohli týmu, provést zadáním jejich e-mailové adresy.

![Pozvání členů týmu](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Nahrajte ukázkový obsah

Nyní jste připraveni nahrát ukázkový obsah. Vyberte **zkuste > obrázku**, **zkuste > Text**, nebo **zkuste > Video**.

![Zkuste použít obraz nebo moderování textu](images/tryimagesortext.png)

Svůj obsah odešlete k moderování. Nástroj pro recenze interně, zavolá rozhraní API kontrolovat obsah pro moderování. Po dokončení skenování zobrazí zpráva oznamující, že jsou výsledky čekání na kontrolu.

![Střední soubory](images/submitted.png)

## <a name="review-moderation-tags"></a>Zkontrolujte moderování značky

Projděte si značky použité pro moderování. Můžete zobrazit značky, které byly použity k vašemu obsahu a skóre byla v každé kategorii. Najdete v článku [Image](image-moderation-api.md), [Text](text-moderation-api.md), a [Video](video-moderation-api.md) označení moderování témata pro další informace o klíčových jiný obsah.

![Kontrola výsledků](images/reviewresults_text.png)

V projektu můžete vy nebo váš tým kontroly změnit tyto značky nebo přidat další značky, podle potřeby. Bude odesílat tyto změny se **Další** tlačítko. Obchodní aplikace volá rozhraní API moderátora, bude obsah označený fronty tady, jste připravení začít prověřena týmy recenze prováděné lidmi. Můžete rychle zkontrolovat velké objemy obsahu pomocí tohoto přístupu.

V tomto okamžiku jste použili nástroj pro recenze Content Moderator příklady co dokáže služba Content Moderatoru. V dalším kroku můžete buď Další informace o nástroje pro recenze a jak integrovat do softwarovém projektu pomocí rozhraní API, přečtěte si, nebo můžete přejít k [další kroky](#next-steps) části a zjistěte, jak použít rozhraní API pro moderování sami ve vaší aplikaci.

## <a name="learn-more-about-the-review-tool"></a>Další informace o nástroje pro recenze

Další informace o tom, jak používat nástroj zkontrolujte Content Moderator, podívejte se na [nástroj pro recenze](Review-Tool-User-Guide/human-in-the-loop.md) průvodce a nástroj pro recenze rozhraní API pro informace o optimalizaci prostředí pro recenze prováděné lidmi naleznete v tématu:
- [Úlohy API](try-review-api-job.md) kontroluje váš obsah s použitím rozhraní API pro moderování a generuje revize v nástroj pro recenze. 
- [Revizi rozhraní API](try-review-api-review.md) přímo vytvoří image, text nebo video revize pro lidské moderátory bez první kontrolu obsahu. 
- [Pracovního postupu API](try-review-api-workflow.md) vytvoří, aktualizuje a načte podrobnosti o vlastních pracovních postupů, které váš tým vytvoří.

Nebo můžete pokračovat s dalšími kroky, abyste mohli začít používat rozhraní API pro moderování ve vašem kódu.

## <a name="next-steps"></a>Další postup

Další informace o použití rozhraní API pro moderování sami ve vaší aplikaci.
- Implementace moderování obrázků. Použití [rozhraní API konzoly](try-image-api.md) nebo [ C# rychlý Start](image-moderation-quickstart-dotnet.md) prohledávat obrázky a detekovat potenciální obsah pro dospělé nebo pikantního pomocí značek, skóre spolehlivosti a další extrahovat informace.
- Implementace moderování textu. Použít [rozhraní API konzoly](try-text-api.md) nebo použijte [ C# rychlý Start](text-moderation-quickstart-dotnet.md) kontrolovat textový obsah pro potenciální vulgární výrazy, klasifikace s podporou počítače nežádoucího textu (preview) a identifikovatelné osobní údaje informace o (PII). 
- Implementace moderování videa. Postupujte podle [Průvodce postupy moderování videa pro C# ](video-moderation-api.md) kontrolovat videa a detekovat potenciální obsah pro dospělé nebo pikantního. 
