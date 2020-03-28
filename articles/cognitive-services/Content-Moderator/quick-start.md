---
title: 'Úvodní příručka: Vyzkoušejte moderátora obsahu na webu – Moderátor obsahu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete nástroj online Content Moderator Review k testování základních funkcí moderátora obsahu, aniž byste museli psát jakýkoli kód.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203547"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Úvodní příručka: Vyzkoušejte moderátora obsahu na webu

V tomto rychlém startu použijete online nástroj Content Moderator Review k testování základních funkcí moderátora obsahu, aniž byste museli psát jakýkoli kód. Pokud chcete tuto službu do aplikace integrovat rychleji, podívejte se na další rychlé starty v části [Další kroky.](#next-steps)

## <a name="prerequisites"></a>Požadavky

- Webový prohlížeč

## <a name="set-up-the-review-tool"></a>Nastavení nástroje pro kontrolu
Nástroj Content Moderator Review je webový nástroj, který umožňuje lidským recenzentům pomáhat kognitivním službám při rozhodování. V této příručce provedete krátký proces nastavení nástroje pro kontrolu, abyste viděli, jak funguje služba Content Moderator. Přejděte na web [nástroje Kontrola moderátora obsahu](https://contentmoderator.cognitive.microsoft.com/) a zaregistrujte se.

![Domovská stránka moderátora obsahu](images/homepage.PNG)

## <a name="create-a-review-team"></a>Vytvoření revizního týmu

Dále vytvořte revizní tým. V pracovním scénáři to bude skupina lidí, kteří budou ručně kontrolovat rozhodnutí o moderování služby. Chcete-li vytvořit tým, budete muset vybrat **oblast**a zadat **název týmu** a **ID týmu**. Pokud chcete pozvat kolegy do týmu, můžete tak učinit zadáním jejich e-mailové adresy zde.

> [!NOTE]
> **Název týmu** je popisný název pro váš revizní tým. Toto je název zobrazený na webu Azure Portal. **ID týmu** je to, co se používá k identifikaci týmu recenze programově.

> [!div class="mx-imgBorder"]
> ![Pozvat člena týmu](images/create-team.png)

Pokud se rozhodnete šifrovat data pomocí klíče spravovaného zákazníkem (CMK), budete vyzváni k **zadání ID prostředku** pro váš prostředek Content Moderator v cenové úrovni E0. Prostředek, který zadáte, musí být nový. 

> [!div class="mx-imgBorder"]
> ![Pozvat člena týmu pomocí CMK](images/create-team-cmk.png)

Pokud se pokusíte znovu použít zdroj Moderátor obsahu, zobrazí se toto upozornění: 

> [!div class="mx-imgBorder"]
> ![Selhání CMK](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Nahrání ukázkového obsahu

Nyní jste připraveni nahrát ukázkový obsah. Vyberte **Vyzkoušet > obrázek**, **Zkuste > text**nebo > **video**.

![Vyzkoušet moderování obrazu nebo textu](images/tryimagesortext.png)

Odešlete svůj obsah k moderování. Interně nástroj pro kontrolu zavolá nastavení API pro moderování pro skenování obsahu. Po dokončení skenování se zobrazí zpráva s informací, že na vaši recenzi čekají výsledky.

![Moderování souborů](images/submitted.png)

## <a name="review-moderation-tags"></a>Kontrola značek moderování

Zkontrolujte použité značky moderování. Můžete zjistit, které značky byly použity na váš obsah a jaké bylo skóre v jednotlivých kategoriích. Další informace o tom, co označují různé značky obsahu, najdete v tématech Moderování [obrázků](image-moderation-api.md), [Textu](text-moderation-api.md)a [videa.](video-moderation-api.md)

![Kontrola výsledků](images/reviewresults_text.png)

V projektu můžete vy nebo váš tým recenzentů tyto značky podle potřeby změnit nebo přidat další značky. Tyto změny odešlete pomocí tlačítka **Další.** Jak vaše obchodní aplikace volá rozhraní API moderátora, označený obsah se zde zařadí do fronty a bude připraven ke kontrole týmy pro lidské recenze. Pomocí tohoto přístupu můžete rychle zkontrolovat velké objemy obsahu.

V tomto okamžiku jste použili nástroj Kontrola moderátora obsahu, abyste viděli příklady toho, co může služba Content Moderator dělat. Dále můžete buď získat další informace o nástroji pro kontrolu a o tom, jak ho integrovat do softwarového projektu pomocí recenzovaných api, nebo můžete přeskočit na část [Další kroky,](#next-steps) kde se dozvíte, jak používat samotná nastavení API pro moderování ve vaší aplikaci.

## <a name="learn-more-about-the-review-tool"></a>Další informace o nástroji pro kontrolu

Další informace o tom, jak používat nástroj Kontrola moderátora obsahu, najdete v průvodci [nástrojem revize](Review-Tool-User-Guide/human-in-the-loop.md) a v rozhraních API nástroje Revize, kde se dozvíte, jak doladit zkušenosti s lidskými recenzemi:
- Rozhraní [JOB API](try-review-api-job.md) prohledá váš obsah pomocí rozhraní API pro moderování a vygeneruje recenze v nástroji pro kontrolu. 
- Rozhraní [Review API](try-review-api-review.md) přímo vytváří recenze obrázků, textu nebo videí pro lidské moderátory bez předchozího skenování obsahu. 
- Rozhraní [API pracovního postupu](try-review-api-workflow.md) vytváří, aktualizuje a získává podrobnosti o vlastních pracovních postupech, které vytvoří váš tým.

Nebo pokračujte dalšími kroky, abyste mohli začít používat nastavení API moderování ve vašem kódu.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat samotná nastavení API pro moderování ve vaší aplikaci.
- Implementujte moderování obrazu. Pomocí [konzoly rozhraní API](try-image-api.md) nebo podle rychlého spuštění sady [.NET SDK](dotnet-sdk-quickstart.md) můžete skenovat obrázky a zjišťovat potenciální obsah pro dospělé a pikantní pomocí značek, skóre spolehlivosti a dalších extrahovaných informací.
- Implementujte moderování textu. Pomocí [konzoly rozhraní API](try-text-api.md) nebo pomocí [rychlého startu sady .NET SDK](dotnet-sdk-quickstart.md) můžete skenovat textový obsah, zda neobsahuje vulgární výrazy, klasifikaci nežádoucího textu (preview) s pomocí počítače a osobní data.
- Implementujte moderování videa. Sledujte [návod, jak c#](video-moderation-api.md) pro C# naskenovat videa a odhalit potenciální obsah pro dospělé a pikantní. 
