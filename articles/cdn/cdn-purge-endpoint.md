---
title: Vyprázdnit Azure CDN koncový bod | Microsoft Docs
description: Naučte se vyprázdnit veškerý obsah uložený v mezipaměti z koncového bodu Azure Content Delivery Network. Hraniční uzly dokončí prostředky do mezipaměti, dokud nevyprší jejich doba do provozu.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: cc09c35ba5499c6e911ebd7dd23482ef30f931da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192532"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Vyprázdnit Azure CDN koncový bod
## <a name="overview"></a>Přehled
Azure CDN hraniční uzly budou prostředky ukládat do mezipaměti, dokud nevyprší hodnota TTL (Time to Live) prostředku.  Po vypršení hodnoty TTL prostředku, když klient požaduje prostředek z hraničního uzlu, vytvoří hraniční uzel novou aktualizovanou kopii assetu, která bude obsluhovat požadavek klienta a uloží mezipaměť do úložiště.

Osvědčeným postupem je zajistit, aby vaši uživatelé měli vždycky přístup k nejnovější kopii vašich assetů, aby si mohli každou aktualizaci nastavovat vaše prostředky a publikovat je jako nové adresy URL.  CDN hned načte nové prostředky pro další požadavky klientů.  Někdy možná budete chtít vyprázdnit obsah uložený v mezipaměti ze všech hraničních uzlů a pokaždé, když budou všechny získávat nové aktualizované prostředky.  Důvodem může být aktualizace webové aplikace nebo rychlé aktualizace prostředků, které obsahují nesprávné informace.

> [!TIP]
> Počítejte s tím, že vymazání pouze vymaže obsah uložený v mezipaměti na hraničních serverech CDN.  Jakékoli mezipaměti, jako jsou proxy servery a mezipaměti v místních prohlížečích, můžou pořád uchovávat kopii souboru uloženou v mezipaměti.  Je důležité si pamatovat na to, když nastavíte čas na živé soubory.  Můžete vynutit, aby klient pro příjem dat požádal o nejnovější verzi souboru tím, že mu při každé aktualizaci aktualizuje nebo využije výhod [ukládání řetězců dotazů do mezipaměti](cdn-query-string.md).  
> 
> 

Tento kurz vás provede vymazáním assetů ze všech hraničních uzlů koncového bodu.

## <a name="walkthrough"></a>Názorný postup
1. Na webu [Azure Portal](https://portal.azure.com)přejděte do profilu CDN obsahujícího koncový bod, který chcete vyprázdnit.
2. V okně profilu CDN klikněte na tlačítko vyčistit.
   
    ![Okno profilu CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Otevře se okno Vyčištění.
   
    ![Okno vyprázdnění CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. V okně vyčistit vyberte v rozevíracím seznamu Adresa URL adresu služby, kterou chcete vymazat.
   
    ![Vyprázdnit formulář](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Můžete se také dostat do okna vyprázdnění kliknutím na tlačítko **vyčistit** v okně koncového bodu CDN.  V takovém případě bude pole **Adresa URL** předem vyplněno adresou služby daného konkrétního koncového bodu.
   > 
   > 
4. Vyberte prostředky, které chcete vymazat z hraničních uzlů.  Pokud chcete vymazat všechny prostředky, klikněte na zaškrtávací políčko **Odstranit vše** .  V opačném případě zadejte cestu každého assetu, který chcete vyprázdnit, do textového pole **cesta** . Následující formáty jsou v cestě podporované.
    1. **Vyprázdnění jedné adresy URL**: vyprázdnění jednotlivých assetů zadáním úplné adresy URL s příponou nebo bez přípony souboru, např., `/pictures/strasbourg.png` ; `/pictures/strasbourg`
    2. **Zástupné znaky**: hvězdička ( \* ) se dá použít jako zástupný znak. Vyprázdnit všechny složky, podsložky a soubory v rámci koncového bodu `/*` v cestě nebo vyprázdnit všechny podsložky a soubory v konkrétní složce zadáním složky následovaný `/*` , např. `/pictures/*` .  Všimněte si, že Azure CDN v současné době nepodporuje zástupné znaky vyprázdnění z Akamai. 
    3. **Kořenová doména vyprázdnění**: vyprázdní kořen koncového bodu znakem "/" v cestě.
   
   > [!TIP]
   > Pro vyprázdnění je nutné zadat cesty a musí se jednat o relativní adresu URL, která se vejde do následujícího [regulárního výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Vyprázdnit všechny** a **zástupné znaky** , které **Azure CDN z Akamai** aktuálně nepodporuje.
   > > Vyprázdnění jedné adresy URL `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Řetězec dotazu `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Zástupné znaky vyprázdnění `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` . 
   > 
   > Další textová pole **cesty** se zobrazí po zadání textu, který vám umožní vytvořit seznam více prostředků.  Assety můžete ze seznamu odstranit kliknutím na tlačítko se třemi tečkami (...).
   > 
5. Klikněte na tlačítko **vyčistit** .
   
    ![Tlačítko vyčistit](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Žádosti o vyprázdnění trvat přibližně 10 minut, než se **Azure CDN od Microsoftu**, přibližně 2 minuty s **Azure CDN z Verizon** (Standard a Premium) a přibližně 10 sekund **Azure CDN z Akamai**.  Azure CDN v daném okamžiku na úrovni profilu dosáhl limitu 50 souběžných žádostí o vyprázdnění. 
> 
> 

## <a name="see-also"></a>Viz také
* [Předběžné načtení prostředků v koncovém bodu Azure CDN](cdn-preload-endpoint.md)
* [Odkaz na Azure CDN REST API – vymazání nebo přednačtení koncového bodu](/rest/api/cdn/endpoints)

