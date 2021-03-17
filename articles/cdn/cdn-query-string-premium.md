---
title: Řízení chování při ukládání Azure CDN do mezipaměti pomocí řetězců dotazů – úroveň Premium
description: Azure CDN ukládání řetězců dotazů do mezipaměti řídí, jak jsou soubory ukládány do mezipaměti, když webová žádost obsahuje řetězec dotazu. Tento článek popisuje ukládání řetězců dotazů do mezipaměti Azure CDN Premium z produktu Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018592"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Řízení chování při ukládání Azure CDN do mezipaměti pomocí řetězců dotazů – úroveň Premium
> [!div class="op_single_selector"]
> * [Standardní úroveň](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
Pomocí služby Azure Content Delivery Network (CDN) můžete řídit ukládání souborů do mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. V rámci webové žádosti s řetězcem dotazu je řetězec dotazu ta část požadavku, která se vyskytuje po otazníku (?). Řetězec dotazu může obsahovat jednu nebo více párů klíč-hodnota, ve kterých je název pole a jeho hodnota oddělená symbolem rovná se (=). Jednotlivé páry klíč-hodnota jsou oddělené znakem ampersand (&). Například http: \/ /www.contoso.com/content.mov?field1=value1&pole2 = hodnota2. Pokud je v řetězci dotazu požadavku více než jedna dvojice klíč-hodnota, nezáleží na jejich pořadí. 

> [!IMPORTANT]
> Produkty CDN Standard a Premium poskytují stejné funkce ukládání řetězců dotazů, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní **Azure CDN Premium z Verizon**. Pro ukládání řetězců dotazů do mezipaměti s Azure CDN standardními produkty najdete informace v tématu [řízení Azure CDN ukládání do mezipaměti pomocí řetězců dotazů – standardní vrstva](cdn-query-string.md).
>


K dispozici jsou tři režimy řetězce dotazu:

- **standardní – mezipaměť**: výchozí režim. V tomto režimu uzel protokolu CDN (Point-of-Presence) předá řetězce dotazů od žadatele k původnímu serveru při první žádosti a uloží Asset do mezipaměti. Všechny následné požadavky na Asset, které jsou obsluhovány ze serveru POP, ignorují řetězce dotazu do vypršení platnosti prostředku v mezipaměti.

    >[!IMPORTANT] 
    > Pokud je u jakékoli cesty k tomuto účtu povolená autorizace tokenu, je režim standardní mezipaměti jediným režimem, který se dá použít. 

- **no-cache**: v tomto režimu nejsou požadavky na řetězce dotazů ukládány do mezipaměti v uzlu pop CDN. Uzel POP načte prostředek přímo ze zdrojového serveru a předá ho žadateli s každým požadavkem.

- **jedinečná – mezipaměť**: v tomto režimu se každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považuje za jedinečný prostředek s vlastní mezipamětí. Například odpověď ze zdrojového serveru pro požadavek například. ashx? q = test1 se uloží do mezipaměti v uzlu POP a vrátí se pro následné mezipaměti se stejným řetězcem dotazu. Požadavek na příklad. ashx? q = test2 se uloží do mezipaměti jako samostatný prostředek s vlastním nastavením Time to Live.
   
    >[!IMPORTANT] 
    > Tento režim nepoužívejte, pokud řetězec dotazu obsahuje parametry, které se změní u všech požadavků, jako je ID relace nebo uživatelské jméno, protože výsledkem bude nízký poměr přístupů do mezipaměti.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Změna nastavení ukládání řetězců dotazů do mezipaměti pro profily Premium CDN
1. Otevřete profil CDN a pak klikněte na **Spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte myší na **velkou kartu http** a pak najeďte myší na místní nabídku **nastavení mezipaměti** . Klikněte na **dotaz – ukládání řetězců do mezipaměti**.
   
    Zobrazují se možnosti ukládání řetězců dotazů do mezipaměti.
   
    ![Možnosti ukládání řetězců dotazu CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Vyberte režim řetězce dotazu a pak klikněte na **aktualizovat**.

> [!IMPORTANT]
> Vzhledem k tomu, že se registrace do sítě CDN zabere v čase, nemusí být změny nastavení řetězce mezipaměti hned viditelné. Šíření obvykle skončí za 10 minut.
 

