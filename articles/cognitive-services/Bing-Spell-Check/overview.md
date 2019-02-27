---
title: Co je rozhraní API pro kontrolu pravopisu Bingu?
titlesuffix: Azure Cognitive Services
description: Další informace o Bingu pravopisu zkontrolujte API pro kontrolu pravopisu kontextové využívá strojové učení a statistické strojového překladu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 22f75efb3cb4baa645030e7ad64072674de662ed
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889947"
---
# <a name="what-is-the-bing-spell-check-api"></a>Co je rozhraní API pro kontrolu pravopisu Bingu?

Rozhraní API Bingu pro kontrolu pravopisu zkontrolujte umožňuje provádět kontextové gramatiky a na text kontrolu pravopisu. Zatímco většina pravopisu využívají sady pravidel na základě slovníku, kontrola pravopisu Bingu využívá strojové učení a statistické strojový překlad poskytnout přesné a kontextové opravy. 

## <a name="features"></a>Funkce


|  |  |
|---------|---------|
|Více režimů pro kontrolu pravopisu     | Více režimů pro kontrolu pravopisu umožňují získat opravy, zaměřuje na gramatika a/nebo kontrolu pravopisu. |
|Slangu a neformálních rozpoznávání     | Rozpoznávání běžných výrazech a neoficiální termíny používané v textu.         |
|Rozlišovat mezi podobné slova     | Najít správné použití mezi slovy této zvuk podobné, ale liší se v význam (např. "najdete v tématu" a "mořská")        |
|Značky, název a podporu oblíbených využití     | Rozpoznávání nových značek, názvy a dalších oblíbených výrazů od samého počátku |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro kontrolu pravopisu zkontrolujte je snadné je zavolat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsovat odpovědi JSON. Tato služba je přístupný pomocí rozhraní REST API nebo SDK kontrola pravopisu Bingu. 

1. Vytvořte [účet rozhraní API služby Cognitive Services](../cognitive-services-apis-create-account.md) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete vytvořit bezplatný účet. 
2. Odeslat požadavek na rozhraní API webové vyhledávání Bingu.
3. Analyzovat odpověď JSON

## <a name="next-steps"></a>Další postup

Nejprve zkuste API pro vyhledávání kontrola pravopisu Bingu [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/spell-check/) zobrazíte, jak rychle zkontrolovat širokou škálu texty.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](../../cognitive-services/cognitive-services-apis-create-account.md). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
