---
title: Řízení ukládání do mezipaměti s řetězci dotazů - úrovně standard chování Azure CDN | Microsoft Docs
description: Azure CDN při ukládání řetězců dotazů ovládací prvky ukládání souborů do mezipaměti při webové žádosti obsahuje řetězec dotazu. Tento článek popisuje řetězec dotazu ukládání do mezipaměti ve standardní produkty Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: fcb4676325066dd6960070d996b1779fb3471dd9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Ovládací prvek Azure CDN ukládání do mezipaměti chování řetězce dotazu - úrovně standard
> [!div class="op_single_selector"]
> * [Úroveň Standard](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
S Azure Content Delivery Network (CDN), můžete řídit, jak jsou soubory uložené v mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. Řetězec dotazu v žádosti o webovou se řetězec dotazu je část požadavku, který se nachází za otazník (?). Řetězec dotazu může obsahovat jeden nebo více páry klíč hodnota, ve kterých pole názvu a hodnoty jsou odděleny znak rovná se (=). Jednotlivé páry klíč hodnota je oddělených ampersandem (&). Například http:\//www.contoso.com/content.mov?field1=value1 & pole2 = value2. Pokud řetězec dotazu požadavku existuje více než jednu dvojici klíč / hodnota, jejich pořadí není důležité. 

> [!IMPORTANT]
> Azure CDN standard a premium produkty poskytovat stejné funkce mezipaměti řetězec dotazu, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Akamai** a **Azure CDN Standard od společnosti Verizon**. Pro dotaz řetězec ukládání do mezipaměti s **Azure CDN Premium od společnosti Verizon**, najdete v části [řízení Azure CDN ukládání do mezipaměti chování řetězce dotazu - úroveň premium](cdn-query-string-premium.md).

K dispozici jsou tři režimy řetězec dotazu:

- **Ignorovat řetězce dotazů**: výchozí režim. V tomto režimu uzlu CDN bodů přítomnosti (POP) předává řetězce dotazu z žadatel na zdrojový server na první požadavek a ukládá do mezipaměti asset. Všechny následné požadavky asset ze serveru POP ignorovat řetězců dotazů do mezipaměti asset vypršení platnosti.

- **Nepoužívat ukládání do mezipaměti pro řetězce dotazů**: V tomto režimu žádostí s řetězci dotazu se neukládají do mezipaměti v uzlu CDN POP. Uzel POP načte asset přímo ze zdrojového serveru a předává je pro žadatele s každou žádostí.

- **Ukládat do mezipaměti každou jedinečnou adresu URL**: V tomto režimu každou žádost s jedinečnou adresou URL, včetně řetězce dotazu, je považován za jedinečný prostředek s vlastní mezipaměti. Například odpověď ze zdrojového serveru pro žádost o example.ashx?q=test1 je uloží do mezipaměti na uzlu POP a vrátit pro následné mezipaměti s stejné řetězec dotazu. Žádost o example.ashx?q=test2 se uloží do mezipaměti jako samostatné asset s vlastní nastavení time to live.
   
    >[!IMPORTANT] 
    > Pokud řetězec dotazu obsahuje parametry, které změní s každou žádostí, jako je například ID relace nebo uživatelské jméno, protože výsledkem bude nízkou poměr přístupů do mezipaměti, nepoužívejte tento režim.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Změna nastavení pro standardní profily CDN ukládání řetězců s dotazy
1. Otevřete profil CDN a potom vyberte koncového bodu CDN, které chcete spravovat.
   
   ![Koncové body profil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. V levém podokně v části nastavení, klikněte na tlačítko **ukládání do mezipaměti pravidla**.
   
    ![Ukládání do mezipaměti CDN tlačítko pravidla](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. V **chování ukládání řetězců s dotazy** seznam, vyberte režim řetězec dotazu a pak klikněte na tlačítko **Uložit**.
   
   ![Řetězec dotazu CDN možnosti ukládání do mezipaměti](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Protože trvá, než se registrace rozšíří v rámci CDN, nemusí být okamžitě viditelné změny nastavení mezipaměti řetězec:
> - Pro **Azure CDN Standard od společnosti Microsoft** profily, šíření obvykle dokončení dobu 10 minut. 
> - Pro **Azure CDN Standard od společnosti Akamai** profily, šíření obvykle dokončení během jedné minuty. 
> - Pro **Azure CDN Standard od společnosti Verizon** a **Azure CDN Premium od společnosti Verizon** profily, šíření obvykle dokončení během 90 minut. 



