---
title: Vyprázdnění koncového bodu Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak chcete vymazat všechny uložené v mezipaměti obsahu z koncového bodu Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: magattus
ms.openlocfilehash: a3777533fc967e1974b99375496dd3777fa9fb3a
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093846"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Vyprázdnění koncového bodu Azure CDN
## <a name="overview"></a>Přehled
Azure hraniční uzly CDN ukládá prostředky do vypršení platnosti prostředku time to live (TTL).  Po vypršení platnosti TTL prostředku, když klient požádá o prostředek z hraničního uzlu, na hraničním uzlu se načtou novou aktualizovanou kopii prostředku obsluhovat žádosti klienta a úložiště aktualizovat mezipaměť.

Osvědčeným postupem zajistit, aby vaši uživatelé vždy získat nejnovější verzi vaše prostředky se vaše prostředky pro každou aktualizaci na verzi a publikujte je jako nové adresy URL.  CDN okamžitě budou načítat nové prostředky pro další požadavky klienta.  Někdy můžete chtít Vyprázdnit obsah uložený v mezipaměti ze všech hraničních uzlů a vynuťte jejich všechny k načtení nové materiály aktualizované.  To může být způsobeno aktualizace do vaší webové aplikace, a rychle aktualizace prostředky, které obsahují nesprávné informace.

> [!TIP]
> Všimněte si, že vyprazdňování pouze vymaže obsah uložený v mezipaměti v krajních serverů CDN.  Všechny podřízené mezipaměti, jako je například proxy servery a mezipamětí místního prohlížeče, stále se udržují v mezipaměti kopii souboru.  Je důležité si pamatovat, když nastavíte souboru time-to-live.  Můžete vynutit příjem dat klienta do žádosti o nejnovější verzi souboru s tím, že je jedinečný název, pokaždé, když ho aktualizujete nebo s využitím [řetězce dotazu do mezipaměti](cdn-query-string.md).  
> 
> 

Tento kurz vás provede vyprázdnění prostředků z koncového bodu všech hraničních uzlů.

## <a name="walkthrough"></a>Názorný postup
1. V [webu Azure Portal](https://portal.azure.com), přejděte do profilu CDN obsahujícího koncový bod chcete vyprázdnit.
2. Z okna profil CDN klikněte na tlačítko Vymazat.
   
    ![Okno profilu CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Otevře se okno vymazání.
   
    ![Okno vyprázdnění CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. V okně vymazání vyberte adresu služby, které chcete vymazat z rozevíracího seznamu adresy URL.
   
    ![Vyprázdnit formuláře](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Můžete také získáte do okna pro vymazání kliknutím **vyprázdnit** tlačítka v okně koncového bodu CDN.  V takovém případě **URL** pole budou předem vyplněná adresa služby této konkrétní koncového bodu.
   > 
   > 
4. Vyberte prostředky, které chcete vyprázdnit z hraničních uzlů.  Pokud chcete vymazat všechny prostředky, klikněte na tlačítko **vyprázdnit vše** zaškrtávací políčko.  V opačném případě zadejte cestu k každý prostředek, který chcete vyprázdnit v **cesta** textového pole. Následující formáty jsou podporovány v cestě.
    1. **Vyprázdnění jedné adresy URL**: vymazání jednotlivý prostředek tak, že zadáte úplnou adresu URL s nebo bez přípony souboru, například`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Zástupné vyprázdnění**: hvězdičky (\*) může sloužit jako zástupný znak. Vyprázdnit všechny složky, podsložky a soubory v koncovém bodě s `/*` v cestě nebo vymazat všechny podsložky a soubory v rámci konkrétní složky tak, že zadáte složce následovaný `/*`, například`/pictures/*`.  Všimněte si, že zástupné vyprázdnění se momentálně nepodporuje Azure CDN od Akamai. 
    3. **Kořenové domény vyprázdnění**: vyprázdnit kořenový koncový bod s "/" v cestě.
   
   > [!TIP]
   > Cesty pro vyprázdnění se musí zadat a musí být relativní adresa URL, který se vejde následující [regulárního výrazu](https://msdn.microsoft.com/library/az24scfc.aspx). **Odstraní se všechny** a **zástupné vyprázdnění** nepodporuje **Azure CDN od Akamai** aktuálně.
   > > Vyprázdnění jedné adresy URL `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Řetězec dotazu `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Zástupné vyprázdnění `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Další **cesta** textová pole se zobrazí, když zadáte text, který umožňuje vytvořit seznam více prostředků.  Prostředky můžete odstranit ze seznamu klikněte na tlačítko se třemi tečkami (...).
   > 
5. Klikněte na tlačítko **vyprázdnit** tlačítko.
   
    ![Tlačítko Vymazat](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Žádosti o vymazání trvat přibližně 2 až 3 minuty zpracování s **Azure CDN od Verizonu** (standard a premium) a přibližně 7 minut s **Azure CDN od Akamai**.  Azure CDN může obsahovat maximálně 50 souběžných vyprázdnit požadavky v daném okamžiku na úrovni profilu. 
> 
> 

## <a name="see-also"></a>Další informace najdete v tématech
* [Předběžné načtení prostředků v koncovém bodu Azure CDN](cdn-preload-endpoint.md)
* [Reference rozhraní REST API služby CDN Azure – mazání nebo předběžné načtení koncový bod](https://msdn.microsoft.com/library/mt634451.aspx)

