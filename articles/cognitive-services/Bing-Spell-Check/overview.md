---
title: Co je rozhraní API pro kontrolu pravopisu Bingu?
titleSuffix: Azure Cognitive Services
description: Přečtěte si o rozhraní API pro kontrolu pravopisu Bingu, které používá strojové učení a statistický strojový překlad pro kontrolu kontextových pravopisů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 5586704e098fc568c714e779c2eed44aa3d1df9c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382995"
---
# <a name="what-is-the-bing-spell-check-api"></a>Co je rozhraní API pro kontrolu pravopisu Bingu?

Rozhraní API pro kontrolu pravopisu bingu umožňuje provádět kontextovou gramatiku a kontrolu pravopisu u textu. Zatímco většina kontrolorů pravopisu spoléhá na sady pravidel založené na slovníku, kontrola pravopisu Bing využívá strojové učení a statistický strojový překlad k poskytování přesných a kontextových oprav. 

## <a name="features"></a>Funkce


|  |  |
|---------|---------|
|Více režimů kontroly pravopisu     | Více režimů kontroly pravopisu umožňuje získat opravy zaměřené na gramatiku nebo pravopis. |
|Slang a neformální rozpoznávání jazyků     | Rozpoznat běžné výrazy a neformální termíny používané v textu.         |
|Rozlišovat mezi podobnými slovy     | Najděte správné použití mezi slovy, která zní podobně, ale liší se významem (například "viz" a "moře")        |
|Podpora značky, titulu a oblíbeného použití     | Rozpoznat nové značky, tituly a další populární výrazy, jak se objeví |

## <a name="workflow"></a>Pracovní postup

Rozhraní API kontroly pravopisu Bingu lze snadno volat z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat odpovědi JSON. Služba je přístupná pomocí rozhraní REST API nebo sad SDK pro kontrolu pravopisu Bingu. 

1. Vytvořte [účet rozhraní API služby Cognitive Services](../cognitive-services-apis-create-account.md) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si vytvořit bezplatný účet. 
2. Odešlete požadavek na rozhraní API Bingu pro vyhledávání na webu.
3. Analyzovat odpověď JSON

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte [interaktivní ukázku rozhraní](https://azure.microsoft.com/services/cognitive-services/spell-check/) API pro vyhledávání pravopisu bingu a zjistěte, jak můžete rychle zkontrolovat různé texty.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](../../cognitive-services/cognitive-services-apis-create-account.md). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Můžete také navštívit [stránku centra rozhraní API pro vyhledávání Binga](../bing-web-search/search-the-web.md) a prozkoumat další dostupná rozhraní API.