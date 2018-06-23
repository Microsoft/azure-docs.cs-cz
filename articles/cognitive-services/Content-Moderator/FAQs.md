---
title: Časté otázky k Azure obsahu moderátora | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se obsahu moderátora.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 7bf6c67bd0a83d9455d14253f4f4b2becafd29ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342435"
---
# <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

#### <a name="what-does-my-content-moderator-subscription-include"></a>Jaké jsou moje obsahu moderátora předplatné zahrnovat?
Vaše předplatné obsahu moderátora zahrnuje přístup k nástroj Kontrola a rozhraní API. Můžete se rozhodnout, zda chcete použít jeden nebo druhý nebo obojí, v závislosti na vašim obchodním potřebám.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Jaká jsou omezení obsahu, který je možné moderovat pomocí tohoto rozhraní API?
Pokud používáte rozhraní API, bitové kopie musí být nejméně 128 pixelů a maximální soubor o velikosti 4 MB volného místa. Text může být maximálně 1 024 znaků. Neexistuje žádné omezení na video doba trvání.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Co se stane, když obsah předaný rozhraní Text API nebo Image API přesáhne velikostní limity?
Text rozhraní API vrátí kód chyby, které informují, že text je delší, než je povoleno. Obrázek rozhraní API také vrátí kód chyby, které informují, že obrázek nesplňuje požadavky na velikost.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Udržet bitové kopie, text nebo videa, které byly odeslány k přerušování?
Obsah je vlastní a nemusí být zachováno Microsoft bez vašeho souhlasu. Společnost Microsoft používá špičkový bezpečnostní opatření k ochraně obsahu.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Lze použít obsahu moderátora na obrazovku pro neplatný podřízené zneužití Image?
Ne. Ale můžou použít kvalifikovaný [PhotoDNA Cloudová služba](https://www.microsoft.com/photodna "cloudové služby Microsoftu PhotoDNA") na obrazovku pro tento typ obsahu.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Až kolik zkontrolujte týmy může uživatel připojení? Může uživatel přepínat mezi týmy?
Uživatel se můžete zapojit do jednoho týmu najednou.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Jaký druh seskupení role jsou podporovány nástrojem zkontrolujte? Jaký je mezi nimi rozdíl?
Nástroje Studio teď umožňuje přiřazení rolí správce a kontrolora. Správce můžete pozvat jiných uživatelů a mít přístup k nastavení konfigurace při kontroloři můžete zkontrolovat výsledky přerušování a značky nebo Odtagujte obsah.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Co je značku? Podporuje nástroj zkontrolujte vlastní značky?
Značky je nebo dvoupísmenným krátký kód, který označuje příznak přerušování, jako je například 'a' pro pro dospělé, "r" pro zájem a tak dále. Správci můžete definovat nové značky pro jejich podnikání, podle potřeby.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Kolik členové týmu může mít v týmu zkontrolujte?
Tým může mít maximálně pět členů, včetně administrátora.
