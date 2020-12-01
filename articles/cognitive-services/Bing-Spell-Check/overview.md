---
title: Co je rozhraní API pro kontrolu pravopisu Bingu?
titleSuffix: Azure Cognitive Services
description: Přečtěte si o rozhraní API Bingu pro kontrolu pravopisu, který využívá Machine Learning a statistické překlady počítačů pro kontrolu pravopisu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: be864e04143a381a08dedb5693026f05c42c5421
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349295"
---
# <a name="what-is-the-bing-spell-check-api"></a>Co je rozhraní API pro kontrolu pravopisu Bingu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Bingu pro kontrolu pravopisu umožňuje provádět kontextové gramatiky a kontrolu pravopisu textu. I když většina pravopisných kontrol spoléhá na sady pravidel založených na slovníku, nástroj pro kontrolu pravopisu Bingu využívá strojové učení a statistické překlady strojového překladu, aby poskytoval přesné a kontextové opravy. 

## <a name="features"></a>Funkce

| Funkce | Popis |
|---------|---------|
|Vícenásobné režimy kontroly pravopisu     | Více režimů kontroly pravopisu vám umožní získat opravy zaměřené na gramatiku a/nebo pravopis. |
|Slangem a neformální rozpoznávání jazyka     | Rozpozná běžné výrazy a neformální výrazy používané v textu.         |
|Odlišení mezi podobnými slovy     | Najde správné použití mezi slovy, která má podobný zvuk, ale která se liší v významu (například "viz" a "moře").        |
|Podpora značek, nadpisů a oblíbených použití     | Rozpoznávání nových značek, názvů a dalších oblíbených výrazů, když se objeví |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro kontrolu pravopisu je snadné volat ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat odpovědi JSON. Služba je přístupná pomocí REST API nebo sady Kontrola pravopisu Bingu SDK. 

1. Vytvořte [účet rozhraní API služby Cognitive Services](../cognitive-services-apis-create-account.md) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si vytvořit bezplatný účet. 
2. Odešlete požadavek na rozhraní API Bingu pro vyhledávání na webu.
3. Analyzovat odpověď JSON

## <a name="next-steps"></a>Další kroky

Nejdřív Vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/spell-check/) rozhraní API hledání kontrola pravopisu Bingu, abyste viděli, jak můžete rychle kontrolovat nejrůznější texty.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](../../cognitive-services/cognitive-services-apis-create-account.md). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/free/cognitive-services/).

Můžete také navštívit [stránku centra rozhraní vyhledávání Bingu API](../bing-web-search/overview.md) a prozkoumat další dostupná rozhraní API.