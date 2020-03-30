---
title: Řízení chování mezipaměti Azure CDN pomocí řetězců dotazu – standardní úroveň
description: Ukládání řetězce dotazu Azure CDN řídí způsob ukládání souborů do mezipaměti, když webový požadavek obsahuje řetězec dotazu. Tento článek popisuje ukládání řetězce dotazu do mezipaměti ve standardních produktech Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 6471241527dd9b594eaaca20ebc75cacb27f8f72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083034"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Řízení chování mezipaměti Azure CDN pomocí řetězců dotazu – standardní úroveň
> [!div class="op_single_selector"]
> * [Úroveň Standard](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
Pomocí sítě pro doručování obsahu Azure (CDN) můžete řídit, jak se soubory upočují pro webový požadavek, který obsahuje řetězec dotazu. Ve webovém požadavku s řetězcem dotazu je řetězec dotazu ta část požadavku, ke které dochází po otazníku (?). Řetězec dotazu může obsahovat jeden nebo více párů klíč-hodnota, ve kterém je název pole a jeho hodnota odděleny znaménkem rovná se (=). Každý pár klíč hodnota je oddělen ampersand (&). Například http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Pokud je více než jeden pár klíč hodnota v řetězci dotazu požadavku, jejich pořadí nezáleží. 

> [!IMPORTANT]
> Standardní a prémiové produkty Azure CDN poskytují stejnou funkci ukládání řetězce dotazu do mezipaměti, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Akamai** a **Azure CDN Standard od Verizonu**. Ukládání řetězců dotazů do mezipaměti s **Azure CDN Premium od společnosti Verizon**najdete v [tématu Řízení chování mezipaměti Azure CDN pomocí řetězců dotazu – úroveň premium](cdn-query-string-premium.md).

K dispozici jsou tři režimy řetězce dotazu:

- **Ignorovat řetězce dotazu**: Výchozí režim. V tomto režimu uzel CDN point-of-presence (POP) předá řetězce dotazu z osazení na zdrojový server na první požadavek a uloží do mezipaměti prostředek. Všechny následné požadavky na prostředek, které jsou obsluhovány z pop ignorovat řetězce dotazu, dokud platnost prostředku uloženého v mezipaměti vyprší.

- **Obejít ukládání do mezipaměti pro řetězce dotazu**: V tomto režimu nejsou požadavky s řetězci dotazu ukládány do mezipaměti v uzlu CDN POP. Uzel POP načte prostředek přímo z původního serveru a předá jej žadateli s každým požadavkem.

- **Mezi každou jedinečnou adresu URL:** V tomto režimu je každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považován za jedinečný datový zdroj s vlastní mezipamětí. Například odpověď z původního serveru pro požadavek například.ashx?q=test1 je uložena do mezipaměti v uzlu POP a vrácena pro následné mezipaměti se stejným řetězcem dotazu. Požadavek na example.ashx?q=test2 je uložen do mezipaměti jako samostatný datový zdroj s vlastním nastavením time-to-live.
   
    >[!IMPORTANT] 
    > Tento režim nepoužívejte, pokud řetězec dotazu obsahuje parametry, které se změní při každém požadavku, například ID relace nebo uživatelské jméno, protože to bude mít za následek nízký poměr přístupů do mezipaměti.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Změna nastavení ukládání řetězce dotazu do mezipaměti pro standardní profily CDN
1. Otevřete profil CDN a vyberte koncový bod CDN, který chcete spravovat.
   
   ![Koncové body profilu CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. V levém podokně v části Nastavení klepněte na **položku Pravidla ukládání do mezipaměti**.
   
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. V seznamu **chování ukládání řetězců dotazu** vyberte režim řetězce dotazu a klepněte na tlačítko **Uložit**.
   
   ![Možnosti ukládání řetězce dotazu CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Vzhledem k tomu, že registrace trvá nějakou dobu, než se registrace rozšíří prostřednictvím azure CDN, změny nastavení řetězce mezipaměti nemusí být okamžitě viditelné:
> - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 



