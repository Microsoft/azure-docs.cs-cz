---
title: Řízení ukládání do mezipaměti s řetězci dotazu – úroveň premium chování Azure CDN | Dokumentace Microsoftu
description: Azure CDN řetězec dotazu do mezipaměti ovládací prvky, jak soubory jsou uložené v mezipaměti, když na webový požadavek obsahuje řetězec dotazu Tento článek popisuje řetězec dotazu do mezipaměti v Azure CDN Premium od Verizonu produktu.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2f0a361d53489e22ccc8e41406e5b86b423ea2f6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091398"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Ovládací prvek Azure CDN pomocí řetězců dotazu – úroveň premium chování ukládání do mezipaměti
> [!div class="op_single_selector"]
> * [Úroveň Standard](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
S Azure Content Delivery Network (CDN), můžete řídit, jak jsou soubory uložené v mezipaměti pro webový požadavek, který obsahuje řetězec dotazu. Řetězec dotazu v webový požadavek s řetězcem dotazu, je část žádosti, které je pozdější otazník (?). Řetězec dotazu může obsahovat jeden nebo více párů klíč hodnota, ve kterých název pole a jeho hodnota jsou odděleny znaménkem rovná se (=). Každý pár klíč hodnota je oddělených ampersandem (&). Například http:\//www.contoso.com/content.mov?field1=value1 & pole2 = value2. Pokud existuje více než jeden pár klíč hodnota v řetězci dotazu požadavku, jejich pořadí není důležitá. 

> [!IMPORTANT]
> Produkty CDN úrovně standard a premium poskytují stejné funkce ukládání řetězců dotazů, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní **Azure CDN Premium od Verizonu**. Řetězec dotazu do mezipaměti pomocí standardní produkty Azure CDN, najdete v části [řízení Azure CDN pomocí řetězců dotazu – úroveň standard chování ukládání do mezipaměti](cdn-query-string.md).
>


K dispozici jsou tři režimy řetězec dotazu:

- **Standardní mezipaměti**: výchozí režim. V tomto režimu se uzel CDN point of presence (POP) předá řetězce dotazu z žadatel na zdrojový server na první požadavek a ukládá do mezipaměti assetu. Všechny následné požadavky assetu, které se obsluhují ze serveru POP ignorovat řetězce dotazu do vypršení platnosti mezipaměti asset.

    >[!IMPORTANT] 
    > Pokud token autorizace je povolená pro libovolnou cestu pro tento účet, režimu mezipaměti standard je jediný režim, který lze použít. 

- **no-cache**: V tomto režimu žádostí s řetězci dotazu se neukládají do mezipaměti v CDN POP uzlu. Uzel POP načte asset přímo ze zdrojového serveru a předává je žadateli spolu s každou žádostí.

- **jedinečné mezipaměti**: V tomto režimu se každý požadavek s jedinečnou adresu URL, včetně řetězce dotazu, je považován za jedinečný prostředek s vlastní mezipaměti. Například je odpověď ze zdrojového serveru pro žádost o example.ashx?q=test1 ukládají do mezipaměti na uzlu POP a vrátil pro následné mezipaměti s stejný řetězec dotazu. Žádost o example.ashx?q=test2 je uložené v mezipaměti jako samostatný prostředek s vlastní nastavení time to live.
   
    >[!IMPORTANT] 
    > Tento režim nepoužívejte, pokud řetězec dotazu obsahuje parametry, které se změní při každé žádosti, jako je například ID relace nebo uživatelské jméno, protože výsledkem bude nízký poměr přístupů do mezipaměti.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Změna nastavení pro profily CDN premium ukládání řetězců dotazů
1. Otevřete profil CDN a pak klikněte na tlačítko **spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **HTTP velké** kartu a pak najeďte myší **nastavení mezipaměti** kontextovou nabídku. Klikněte na tlačítko **ukládání do mezipaměti řetězce dotazu**.
   
    Se zobrazují možnosti ukládání do mezipaměti řetězce dotazu.
   
    ![Řetězec dotazu CDN možnosti ukládání do mezipaměti](./media/cdn-query-string-premium/cdn-query-string.png)
3. Vyberte režim řetězec dotazu a pak klikněte na tlačítko **aktualizace**.

> [!IMPORTANT]
> Vzhledem k tomu, že se registrace rozšíří v rámci CDN chvíli trvá, nemusí být okamžitě viditelné změny nastavení mezipaměti řetězců. Šíření obvykle hotové během 10 minut.
 

