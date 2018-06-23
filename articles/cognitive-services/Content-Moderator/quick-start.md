---
title: Azure obsahu moderátora Začínáme | Microsoft Docs
description: Jak začít pracovat s Azure moderátora obsahu.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342759"
---
# <a name="get-started-with-content-moderator"></a>Začínáme s Content Moderatorem

Můžete začít s obsahu moderátora rozhraní API a nástroje zkontrolujte následujícími způsoby:

- [Spustit pomocí nástroje kontrolní](#start-with-the-review-tool) k vytvoření klíče rozhraní API a zkontrolujte týmu. Prozkoumejte nástroj zkontrolujte a zjistěte, jak integrovat pomocí obsahu moderátora rozhraní API.
- [Přihlášení k odběru obsahu moderátorovi](#start-with-the-apis) na portálu Azure. Stále je třeba zaregistrovat online k vytvoření týmu zkontrolujte.
- [Použijte toku konektoru a šablony](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) rezervovat širokou škálu integrace pomocí návrháře snadno použitelné.

Bez ohledu na to možnost zvolíte, najdete v článku [správy pověření](review-tool-user-guide/credentials.md) článek najít přihlašovací údaje rozhraní API.

## <a name="start-with-the-review-tool"></a>Spustit pomocí nástroje Kontrola
[Zaregistrujte si](http://contentmoderator.cognitive.microsoft.com/) na obsahu moderátora kontrolní nástroj pro webové stránky.

![Obsah moderátora domovské stránky](images/homepage.PNG)

### <a name="create-a-review-team"></a>Vytvoření týmu revize
Zadejte název vašeho týmu. Pokud chcete pozvat kolegy, můžete tak učinit zadáním jejich e-mailové adresy.

![Pozvěte člen týmu](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Nahrajte Image nebo zadejte text
Klikněte na tlačítko **zkuste > Image** nebo **zkuste > textu**. Nahrát až pět ukázkové obrázky, nebo zadejte ukázkový text pro přerušování.

![Zkuste bitovou kopii nebo přerušování textu](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Odeslání pro automatizované přerušování
Odešlete obsah pro automatizované přerušování. Zkontrolujte nástroj interně volá přerušování rozhraní API pro vyhledávání obsahu. Po dokončení kontrolu zobrazí zpráva oznamující o výsledcích čekání zkontrolovali.

![Střední soubory](images/submitted.png)

### <a name="review-and-confirm-results"></a>Zkontrolujte a potvrďte výsledky
Zkontrolujte značky moderovaná automaticky, v případě potřeby změňte a odeslat pomocí **Další** tlačítko. Jako obchodní aplikace volá rozhraní API moderátora s příznakem obsahu spuštění služby Řízení front, připraveni mají být zkontrolovány uživatelem lidského zkontrolujte týmy. Prostudování rychle velkých objemů obsahu pomocí tohoto přístupu.

![Kontrola výsledků](images/reviewresults.png)

Naučte se používat všechny [zkontrolujte funkce nástroje](Review-Tool-User-Guide/human-in-the-loop.md) nebo pokračujte v další části Další informace o rozhraní API. Registrace krok přeskočit, protože máte klíč rozhraní API pro vás zřízený v nástroji zkontrolujte jak je znázorněno v [správy pověření](review-tool-user-guide/credentials.md) článku.

### <a name="use-the-apis"></a>Použití rozhraní API

Teď, když jste prozkoumali přerušování obsahu a zkontrolujte prostředí nástroje, zjistěte, jak integrovat moderátora obsah s obchodních aplikací. Použijte následující části Další informace a rychlého pochopení pomocí sady SDK a ukázky.

## <a name="start-with-the-apis"></a>Začněte s rozhraní API

[Přihlášení k odběru obsahu moderátorovi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) na portálu Azure. Začněte s jedním z následujících rozhraní API:

### <a name="image-moderation"></a>Moderování obrázků

Začínat [rozhraní API konzoly](try-image-api.md) nebo použít [rychlý start .NET](image-moderation-quickstart-dotnet.md) pro prohledávání bitové kopie a zjistit potenciální obsah pro dospělé a zájem pomocí značek, spolehlivosti skóre a dalších extrahovat informace.

### <a name="text-moderation"></a>Moderování textu

Začínat [rozhraní API konzoly](try-text-api.md) nebo použít [rychlý start .NET](text-moderation-quickstart-dotnet.md) ke kontrole textového obsahu potenciální testujeme, klasifikace s asistencí počítač nežádoucí text (preview) a identifikovatelné osobní informace o (PII). 


### <a name="video-moderation"></a>Moderování videa

Začínat [rychlý start .NET](video-moderation-api.md) pro prohledávání videa a zjistit potenciální obsah pro dospělé a zájem. 


### <a name="review-apis"></a>Recenze rozhraní API

Začněte zde tak, že zvolíte úlohu zkontrolujte a rozhraní API pracovního postupu.

- [Úlohy API](try-review-api-job.md) kontroluje svůj obsah pomocí přerušování rozhraní API a vygeneruje recenze v nástroji revize. 
- [Zkontrolujte API](try-review-api-review.md) přímo vytvoří bitovou kopii, textové nebo video recenze pro lidské moderátorů bez první kontrolu obsahu. 
- [Pracovního postupu API](try-review-api-workflow.md) vytvoří, aktualizuje a získá informace o vlastní pracovní postupy, které vytvoří tým.

## <a name="next-steps"></a>Další postup

Další informace o obsahu přerušování začínající [bitové kopie přerušování rozhraní API](image-moderation-api.md).
