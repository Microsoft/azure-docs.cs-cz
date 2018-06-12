---
title: Řízení ukládání do mezipaměti řetězce dotazu - úroveň premium chování Azure CDN | Microsoft Docs
description: Azure CDN při ukládání řetězců dotazů ovládací prvky ukládání souborů do mezipaměti při webové žádosti obsahuje řetězec dotazu. Tento článek popisuje řetězec dotazu ukládání do mezipaměti v Azure CDN Premium od Verizon produktu.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 3777689af72f580645826a1ca4e31ca84bd65dab
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260830"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Ovládací prvek Azure CDN ukládání do mezipaměti chování řetězce dotazu - úrovni premium
> [!div class="op_single_selector"]
> * [Úroveň Standard](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
S Azure Content Delivery Network (CDN), můžete řídit, jak jsou soubory uložené v mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. Řetězec dotazu v žádosti o webovou se řetězec dotazu je část požadavku, který se nachází za otazník (?). Řetězec dotazu může obsahovat jeden nebo více páry klíč hodnota, ve kterých pole názvu a hodnoty jsou odděleny znak rovná se (=). Jednotlivé páry klíč hodnota je oddělených ampersandem (&). Například http:\//www.contoso.com/content.mov?field1=value1 & pole2 = value2. Pokud řetězec dotazu požadavku existuje více než jednu dvojici klíč / hodnota, jejich pořadí není důležité. 

> [!IMPORTANT]
> Produktů CDN standard a premium poskytují stejné funkce mezipaměti řetězec dotazu, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní pro **Azure CDN Premium od společnosti Verizon**. Řetězec dotazu ukládání do mezipaměti s produkty Azure CDN standard, najdete v části [řízení Azure CDN ukládání do mezipaměti chování řetězce dotazu - úrovně standard](cdn-query-string.md).
>


K dispozici jsou tři režimy řetězec dotazu:

- **Standardní mezipaměti**: výchozí režim. V tomto režimu uzlu CDN bodů přítomnosti (POP) předává řetězce dotazu z žadatel na zdrojový server na první požadavek a ukládá do mezipaměti asset. Všechny následné požadavky asset ze serveru POP ignorovat řetězců dotazů do mezipaměti asset vypršení platnosti.

    >[!IMPORTANT] 
    > Pokud je token autorizace je povoleno pro jakoukoli cestu na tento účet, režimu mezipaměti standard je jediný režim, který lze použít. 

- **Ne mezipaměti**: V tomto režimu žádostí s řetězci dotazu se neukládají do mezipaměti v uzlu CDN POP. Uzel POP načte asset přímo ze zdrojového serveru a předává je pro žadatele s každou žádostí.

- **Jedinečný mezipaměti**: V tomto režimu každou žádost s jedinečnou adresou URL, včetně řetězce dotazu, je považován za jedinečný prostředek s vlastní mezipaměti. Například odpověď ze zdrojového serveru pro žádost o example.ashx?q=test1 je uloží do mezipaměti na uzlu POP a vrátit pro následné mezipaměti s stejné řetězec dotazu. Žádost o example.ashx?q=test2 se uloží do mezipaměti jako samostatné asset s vlastní nastavení time to live.
   
    >[!IMPORTANT] 
    > Pokud řetězec dotazu obsahuje parametry, které změní s každou žádostí, jako je například ID relace nebo uživatelské jméno, protože výsledkem bude nízkou poměr přístupů do mezipaměti, nepoužívejte tento režim.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Změna nastavení pro profily CDN premium ukládání řetězců s dotazy
1. Otevřete profil CDN a pak klikněte na **spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **HTTP velké** a potom přejděte myší **nastavení mezipaměti** plovoucím panelem nabídky. Klikněte na tlačítko **ukládání do mezipaměti řetězce dotazu**.
   
    Zobrazí se možnosti ukládání do mezipaměti řetězce dotazu.
   
    ![Řetězec dotazu CDN možnosti ukládání do mezipaměti](./media/cdn-query-string-premium/cdn-query-string.png)
3. Vyberte režim řetězec dotazu a pak klikněte na **aktualizace**.

> [!IMPORTANT]
> Protože trvá, než se registrace rozšíří v rámci CDN, změny nastavení mezipaměti řetězec nemusí být okamžitě viditelné. Šíření obvykle dokončí za 10 minut.
 

