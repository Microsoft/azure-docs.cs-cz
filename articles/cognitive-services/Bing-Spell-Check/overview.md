---
title: Co je rozhraní API pro kontrolu pravopisu Bingu?
titleSuffix: Azure Cognitive Services
description: Přečtěte si o rozhraní API Bingu pro kontrolu pravopisu, který využívá Machine Learning a statistické překlady počítačů pro kontrolu pravopisu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 2cb5a7bbe4ef46c2ae0b7a6ad095c56dd21730ad
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500341"
---
# <a name="what-is-the-bing-spell-check-api"></a>Co je rozhraní API pro kontrolu pravopisu Bingu?

Rozhraní API Bingu pro kontrolu pravopisu umožňuje provádět kontextové gramatiky a kontrolu pravopisu textu. I když většina pravopisných kontrol spoléhá na sady pravidel založených na slovníku, nástroj pro kontrolu pravopisu Bingu využívá strojové učení a statistické překlady strojového překladu, aby poskytoval přesné a kontextové opravy. 

## <a name="features"></a>Funkce


|  |  |
|---------|---------|
|Vícenásobné režimy kontroly pravopisu     | Více režimů kontroly pravopisu vám umožní získat opravy zaměřené na gramatiku a/nebo pravopis. |
|Slangem a neformální rozpoznávání jazyka     | Rozpozná běžné výrazy a neformální výrazy používané v textu.         |
|Odlišení mezi podobnými slovy     | Najde správné použití mezi slovy, která má podobný zvuk, ale která se liší v významu (například "viz" a "moře").        |
|Podpora značek, nadpisů a oblíbených použití     | Rozpoznávání nových značek, názvů a dalších oblíbených výrazů, když se objeví |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro kontrolu pravopisu je snadné volat ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat odpovědi JSON. Služba je přístupná pomocí REST API nebo sady Kontrola pravopisu Bingu SDK. 

1. Vytvořte [účet rozhraní API služby Cognitive Services](../cognitive-services-apis-create-account.md) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si vytvořit bezplatný účet. 
2. Odešle požadavek na rozhraní API Bingu pro vyhledávání na webu.
3. Analyzovat odpověď JSON

## <a name="next-steps"></a>Další postup

Nejdřív Vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/spell-check/) rozhraní API hledání kontrola pravopisu Bingu, abyste viděli, jak můžete rychle kontrolovat nejrůznější texty.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](../../cognitive-services/cognitive-services-apis-create-account.md). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
