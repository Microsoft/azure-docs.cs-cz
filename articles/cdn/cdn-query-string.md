---
title: Řízení ukládání do mezipaměti s řetězci dotazu – úroveň standard chování Azure CDN | Dokumentace Microsoftu
description: Azure CDN řetězec dotazu do mezipaměti ovládací prvky, jak soubory jsou uložené v mezipaměti, když na webový požadavek obsahuje řetězec dotazu Tento článek popisuje řetězec dotazu do mezipaměti v Azure CDN standard produktů.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: f0dab3dc81c626e3e7f8c79b4142e5eb4f2a1276
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093795"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Ovládací prvek Azure CDN pomocí řetězců dotazu – úroveň standard chování ukládání do mezipaměti
> [!div class="op_single_selector"]
> * [Úroveň Standard](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
S Azure Content Delivery Network (CDN), můžete řídit, jak jsou soubory uložené v mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. Řetězec dotazu v webový požadavek s řetězcem dotazu, je část žádosti, které je pozdější otazník (?). Řetězec dotazu může obsahovat jeden nebo více párů klíč hodnota, ve kterých název pole a jeho hodnota jsou odděleny znaménkem rovná se (=). Každý pár klíč hodnota je oddělených ampersandem (&). Například http:\//www.contoso.com/content.mov?field1=value1 & pole2 = value2. Pokud existuje více než jeden pár klíč hodnota v řetězci dotazu požadavku, jejich pořadí není důležitá. 

> [!IMPORTANT]
> Azure CDN standard a premium produkty poskytují stejné funkce ukládání řetězců dotazů, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai** a **Azure CDN Standard od Verizonu**. Do mezipaměti řetězce dotazu s **Azure CDN Premium od Verizonu**, naleznete v tématu [řízení Azure CDN pomocí řetězců dotazu – úroveň premium chování ukládání do mezipaměti](cdn-query-string-premium.md).

K dispozici jsou tři režimy řetězec dotazu:

- **Ignorovat řetězce dotazů**: výchozí režim. V tomto režimu se uzel CDN point of presence (POP) předá řetězce dotazu z žadatel na zdrojový server na první požadavek a ukládá do mezipaměti assetu. Všechny následné požadavky assetu, které se obsluhují z POP ignorovat řetězce dotazu do vypršení platnosti mezipaměti asset.

- **Nepoužívat ukládání do mezipaměti pro řetězce dotazu**: V tomto režimu žádostí s řetězci dotazu se neukládají do mezipaměti v CDN POP uzlu. Uzel POP načte asset přímo ze zdrojového serveru a předává je žadateli spolu s každou žádostí.

- **Ukládat do mezipaměti každou jedinečnou adresu URL**: V tomto režimu se každý požadavek s jedinečnou adresu URL, včetně řetězce dotazu, je považován za jedinečný prostředek s vlastní mezipaměti. Například je odpověď ze zdrojového serveru pro žádost o example.ashx?q=test1 ukládají do mezipaměti na uzlu POP a vrátil pro následné mezipaměti s stejný řetězec dotazu. Žádost o example.ashx?q=test2 je uložené v mezipaměti jako samostatný prostředek s vlastní nastavení time to live.
   
    >[!IMPORTANT] 
    > Tento režim nepoužívejte, pokud řetězec dotazu obsahuje parametry, které se změní při každé žádosti, jako je například ID relace nebo uživatelské jméno, protože výsledkem bude nízký poměr přístupů do mezipaměti.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Změna nastavení pro profily standard CDN ukládání řetězců dotazů
1. Otevřete profil CDN a pak vyberte koncový bod CDN, které chcete spravovat.
   
   ![Koncové body profilu CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. V levém podokně v části nastavení klikněte na tlačítko **pravidla ukládání do mezipaměti**.
   
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. V **chování ukládání řetězců dotazů** seznamu, vyberte režim řetězec dotazu a pak klikněte na tlačítko **Uložit**.
   
   ![Řetězec dotazu CDN možnosti ukládání do mezipaměti](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Protože se registrace rozšíří do Azure CDN nějakou dobu trvá, nemusí být okamžitě viditelné změny nastavení mezipaměti řetězec:
> - Šíření profilů **Azure CDN Standard od Microsoftu** trvá většinou 10 minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 



