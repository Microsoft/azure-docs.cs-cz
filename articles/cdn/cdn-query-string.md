---
title: Řízení chování při ukládání Azure CDN do mezipaměti pomocí řetězců dotazů – úroveň Standard
description: Azure CDN ukládání řetězců dotazů do mezipaměti řídí, jak jsou soubory ukládány do mezipaměti, když webová žádost obsahuje řetězec dotazu. Tento článek popisuje ukládání řetězců dotazů do mezipaměti v Azure CDNch standardních produktech.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018575"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Řízení chování při ukládání Azure CDN do mezipaměti pomocí řetězců dotazů – úroveň Standard
> [!div class="op_single_selector"]
> * [Standardní úroveň](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
Pomocí služby Azure Content Delivery Network (CDN) můžete řídit ukládání souborů do mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. V rámci webové žádosti s řetězcem dotazu je řetězec dotazu ta část požadavku, která se vyskytuje po otazníku (?). Řetězec dotazu může obsahovat jednu nebo více párů klíč-hodnota, ve kterých je název pole a jeho hodnota oddělená symbolem rovná se (=). Jednotlivé páry klíč-hodnota jsou oddělené znakem ampersand (&). Například http: \/ /www.contoso.com/content.mov?field1=value1&pole2 = hodnota2. Pokud je v řetězci dotazu požadavku více než jedna dvojice klíč-hodnota, nezáleží na jejich pořadí. 

> [!IMPORTANT]
> Produkty Azure CDN Standard a Premium poskytují stejné funkce pro ukládání řetězců dotazů do mezipaměti, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Akamai** a **Azure CDN Standard od Verizon**. Pro ukládání řetězců dotazů do mezipaměti s **Azure CDN Premium z Verizon** najdete informace v tématu [řízení Azure CDN ukládání do mezipaměti pomocí řetězců dotazů – úroveň Premium](cdn-query-string-premium.md).

K dispozici jsou tři režimy řetězce dotazu:

- **Ignorovat řetězce dotazů**: výchozí režim. V tomto režimu uzel protokolu CDN (Point-of-Presence) předá řetězce dotazů od žadatele k původnímu serveru při první žádosti a uloží Asset do mezipaměti. Všechny následné požadavky na Asset, které jsou obsluhovány z POP, ignorují řetězce dotazu do vypršení platnosti prostředku v mezipaměti.

- **Nepoužívat ukládání do mezipaměti pro řetězce dotazů**: v tomto režimu nejsou požadavky na řetězce dotazů ukládány do mezipaměti v uzlu pop CDN. Uzel POP načte prostředek přímo ze zdrojového serveru a předá ho žadateli s každým požadavkem.

- **Ukládat do mezipaměti každou jedinečnou adresu URL**: v tomto režimu se každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považuje za jedinečný prostředek s vlastní mezipamětí. Například odpověď ze zdrojového serveru pro požadavek například. ashx? q = test1 se uloží do mezipaměti v uzlu POP a vrátí se pro následné mezipaměti se stejným řetězcem dotazu. Požadavek na příklad. ashx? q = test2 se uloží do mezipaměti jako samostatný prostředek s vlastním nastavením Time to Live.
   
    >[!IMPORTANT] 
    > Tento režim nepoužívejte, pokud řetězec dotazu obsahuje parametry, které se změní u všech požadavků, jako je ID relace nebo uživatelské jméno, protože výsledkem bude nízký poměr přístupů do mezipaměti.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Změna nastavení mezipaměti řetězce dotazu pro standardní profily CDN
1. Otevřete profil CDN a pak vyberte koncový bod CDN, který chcete spravovat.
   
   ![Koncové body profilu CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. V levém podokně v části Nastavení klikněte na **pravidla ukládání do mezipaměti**.
   
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. V seznamu **chování mezipaměti řetězce dotazu** vyberte režim řetězce dotazu a pak klikněte na **Uložit**.
   
   ![Možnosti ukládání řetězců dotazu CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Vzhledem k tomu, že se registrace do Azure CDN zabere v čase, nemusí být změny nastavení řetězce v mezipaměti hned viditelné:
> - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 



