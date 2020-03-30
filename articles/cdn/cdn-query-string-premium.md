---
title: Řízení chování azure cdn ukládání do mezipaměti pomocí řetězců dotazu – úroveň premium
description: Ukládání řetězce dotazu Azure CDN řídí způsob ukládání souborů do mezipaměti, když webový požadavek obsahuje řetězec dotazu. Tento článek popisuje ukládání řetězce dotazu do mezipaměti v produktu Azure CDN Premium od společnosti Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 365c52840d281c0f48d17aacc358e4cce513e3b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083092"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Řízení chování azure cdn ukládání do mezipaměti pomocí řetězců dotazu – úroveň premium
> [!div class="op_single_selector"]
> * [Úroveň Standard](cdn-query-string.md)
> * [Úroveň Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Přehled
Pomocí sítě pro doručování obsahu Azure (CDN) můžete řídit, jak se soubory upočují pro webový požadavek, který obsahuje řetězec dotazu. Ve webovém požadavku s řetězcem dotazu je řetězec dotazu ta část požadavku, ke které dochází po otazníku (?). Řetězec dotazu může obsahovat jeden nebo více párů klíč-hodnota, ve kterém je název pole a jeho hodnota odděleny znaménkem rovná se (=). Každý pár klíč hodnota je oddělen ampersand (&). Například http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Pokud je více než jeden pár klíč hodnota v řetězci dotazu požadavku, jejich pořadí nezáleží. 

> [!IMPORTANT]
> Standardní a prémiové produkty CDN poskytují stejnou funkci ukládání řetězce dotazu do mezipaměti, ale uživatelské rozhraní se liší. Tento článek popisuje rozhraní azure **cdn premium od společnosti Verizon**. Ukládání řetězců dotazů do mezipaměti se standardními produkty Azure CDN najdete v [tématu Řízení chování mezipaměti Azure CDN pomocí řetězců dotazu – standardní vrstva](cdn-query-string.md).
>


K dispozici jsou tři režimy řetězce dotazu:

- **standardní cache**: Výchozí režim. V tomto režimu uzel CDN point-of-presence (POP) předá řetězce dotazu z osazení na zdrojový server na první požadavek a uloží do mezipaměti prostředek. Všechny následné požadavky na prostředek, které jsou obsluhovány ze serveru POP, ignorují řetězce dotazu, dokud nevyprší platnost datového zdroje uloženého v mezipaměti.

    >[!IMPORTANT] 
    > Pokud je povolena autorizace tokenu pro libovolnou cestu v tomto účtu, režim standardní mezipaměti je jediný režim, který lze použít. 

- **bez mezipaměti**: V tomto režimu nejsou požadavky s řetězci dotazu ukládány do mezipaměti v uzlu CDN POP. Uzel POP načte prostředek přímo z původního serveru a předá jej žadateli s každým požadavkem.

- **jedinečná mezipaměť**: V tomto režimu je každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, považován za jedinečný datový zdroj s vlastní mezipamětí. Například odpověď z původního serveru pro požadavek například.ashx?q=test1 je uložena do mezipaměti v uzlu POP a vrácena pro následné mezipaměti se stejným řetězcem dotazu. Požadavek na example.ashx?q=test2 je uložen do mezipaměti jako samostatný datový zdroj s vlastním nastavením time-to-live.
   
    >[!IMPORTANT] 
    > Tento režim nepoužívejte, pokud řetězec dotazu obsahuje parametry, které se změní při každém požadavku, například ID relace nebo uživatelské jméno, protože to bude mít za následek nízký poměr přístupů do mezipaměti.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Změna nastavení ukládání řetězce dotazu do mezipaměti pro prémiové profily CDN
1. Otevřete profil CDN a klepněte na tlačítko **Spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Velké HTTP** a najeďte na informační nabídku **Nastavení mezipaměti.** Klepněte na **položku Ukládání do mezipaměti řetězce dotazu**.
   
    Zobrazí se možnosti ukládání řetězce dotazu do mezipaměti.
   
    ![Možnosti ukládání řetězce dotazu CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Vyberte režim řetězce dotazu a klepněte na tlačítko **Aktualizovat**.

> [!IMPORTANT]
> Vzhledem k tomu, že trvá nějakou dobu, než se registrace rozšíří prostřednictvím sítě CDN, nemusí být změny nastavení řetězce mezipaměti okamžitě viditelné. Šíření se obvykle dokončí za 10 minut.
 

