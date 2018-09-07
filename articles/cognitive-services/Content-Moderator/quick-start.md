---
title: Azure Content Moderator Začínáme | Dokumentace Microsoftu
description: Jak začít pracovat s Content Moderatorem Azure.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: 39727b4d97ade67b854fe525afad565451cc3d77
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024507"
---
# <a name="get-started-with-content-moderator"></a>Začínáme s Content Moderatorem

Asi začít pracovat s Content Moderatorem následujícími způsoby:

- [Začněte s nástroj pro recenze](#start-with-the-review-tool) získat klíč rozhraní API a vytvořit tým kontroly. Výhodou je, že můžete použít klíč rozhraní API pro volání rozhraní API pro moderování pro kontrolu obsahu a revizi rozhraní API pro generování kontroly, bez dalších kroků.
- [Předplatit Content Moderator](#start-with-the-apis) v Azure a získat klíč rozhraní API. Podívejte se [reference k rozhraní API](api-reference.md) a [sady SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Nevyhnete se zaregistrujte online a vytvořit tým kontroly.
- [Pomocí konektoru toku a šablon](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) rezervovat širokou škálu integrace s snadno použít návrháře.

Bez ohledu na to možnost zvolíte, najdete v článku [Správa přihlašovacích údajů](review-tool-user-guide/credentials.md) článku najít svoje přihlašovací údaje rozhraní API.

## <a name="start-with-the-review-tool"></a>Začněte s nástroj pro recenze
[Zaregistrujte](http://contentmoderator.cognitive.microsoft.com/) na webové stránce Content Moderator kontroly nástroje.

![Content Moderator domovské stránky](images/homepage.PNG)

### <a name="create-a-review-team"></a>Vytvořit tým kontroly
Zadejte název vašeho týmu. Pokud chcete pozvat své kolegy, provést zadáním jejich e-mailové adresy.

![Pozvání členů týmu](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Nahrávání obrázků nebo zadejte text
Klikněte na tlačítko **zkuste > obrázku** nebo **zkuste > Text**. Nahrát až o pěti ukázkové obrázky nebo můžete zadat text v ukázce pro moderování.

![Zkuste použít obraz nebo moderování textu](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Pro automatizované moderování odeslat
Odešlete obsah pro automatizované moderování. Nástroj pro recenze interně volá rozhraní API kontrolovat obsah pro moderování. Po dokončení skenování se zobrazí zpráva o čekání na kontrolu výsledků.

![Střední soubory](images/submitted.png)

### <a name="review-and-confirm-results"></a>Zkontrolujte a potvrďte výsledky
Zkontrolujte značky který automaticky, změňte v případě potřeby a odeslat pomocí **Další** tlačítko. Jak obchodní aplikace volá Moderator API, označený obsahu spuštění služby Řízení front, jste připravení začít prověřena týmy recenze prováděné lidmi. Můžete rychle zkontrolovat velké objemy obsahu pomocí tohoto přístupu.

![Kontrola výsledků](images/reviewresults.png)

Zjistěte, jak používat všechny [zkontrolujte funkce nástroje](Review-Tool-User-Guide/human-in-the-loop.md) nebo pokračovat v další části a další informace o rozhraní API. Registrace krok přeskočte, protože máte klíč rozhraní API, které jsou zřízené pro vás v nástroj pro recenze, jak je znázorněno [Správa přihlašovacích údajů](review-tool-user-guide/credentials.md) článku.

### <a name="use-the-apis"></a>Použití rozhraní API

Zjistěte, jak integrovat Content Moderator s podnikovými aplikacemi. Podívejte se [reference k rozhraní API](api-reference.md) a [sady SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Přihlášení odběru na portálu Azure portal

[Předplatit Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) na webu Azure Portal. Začněte s jedním z následujících rozhraní API:

### <a name="image-moderation"></a>Moderování obrázků

Začněte [rozhraní API konzoly](try-image-api.md) nebo použijte [rychlý úvod k .NET](image-moderation-quickstart-dotnet.md) prohledávat obrázky a detekovat potenciální obsah pro dospělé nebo pikantního pomocí značek, skóre spolehlivosti a další extrahovat informace.

### <a name="text-moderation"></a>Moderování textu

Začněte s [rozhraní API konzoly](try-text-api.md) nebo použijte [rychlý úvod k .NET](text-moderation-quickstart-dotnet.md) kontrolovat textový obsah pro potenciální vulgární výrazy, klasifikace s podporou počítače nežádoucího textu (preview) a identifikovatelné osobní údaje informace o (PII). 


### <a name="video-moderation"></a>Moderování videa

Začněte [rychlý úvod k .NET](video-moderation-api.md) kontrolovat videa a detekovat potenciální obsah pro dospělé nebo pikantního. 


### <a name="review-apis"></a>Recenze rozhraní API

Výběrem úlohy, kontrola a rozhraní API pracovního postupu, začněte tady.

- [Úlohy API](try-review-api-job.md) kontroluje váš obsah s použitím rozhraní API pro moderování a generuje revize v nástroj pro recenze. 
- [Revizi rozhraní API](try-review-api-review.md) přímo vytvoří image, text nebo video revize pro lidské moderátory bez první kontrolu obsahu. 
- [Pracovního postupu API](try-review-api-workflow.md) vytvoří, aktualizuje a načte podrobnosti o vlastních pracovních postupů, které váš tým vytvoří.

## <a name="next-steps"></a>Další postup

Podívejte se [reference k rozhraní API](api-reference.md) a [sady SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Rychlé zprovoznění svoji integraci s [ukázky sady .NET SDK](sdk-and-samples.md#net-sdk-samples), [ukázky rozhraní REST API v jazyce C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) a [kurzy](sdk-and-samples.md#tutorials).
