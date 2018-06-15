---
title: Vyprázdnění koncového bodu Azure CDN | Microsoft Docs
description: Zjistěte, jak k vyprázdnění všech obsah uložený v mezipaměti z koncového bodu Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 262a8f7385ba5f74d21991772599540260a145fc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765162"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Vyprázdnění koncového bodu Azure CDN
## <a name="overview"></a>Přehled
Uzlů Azure CDN hraniční mezipaměti prostředky až do vypršení platnosti assetu time to live (TTL).  Po vypršení platnosti TTL assetu, když klient požádá o prostředku z uzlu edge, hraničního uzlu načte aktualizované novou kopii asset obsluhovat požadavky klientů a úložiště aktualizace mezipaměti.

Doporučujeme, abyste měli jistotu, že uživatelé vždycky získat nejnovější verzi vaše prostředky je vaše prostředky pro jednotlivé aktualizace na verzi a publikovat je jako nové adresy URL.  CDN načte okamžitě nové prostředky pro další požadavky klientů.  V některých případech můžete chtít vymazat obsah uložený v mezipaměti ze všech uzlů okraj a nutí je všechny načíst nové aktualizované prostředky.  Důvodem může být aktualizace webové aplikace, nebo rychle aktualizace prostředky, které obsahují nesprávné informace.

> [!TIP]
> Všimněte si, že vyprazdňování pouze vymaže obsah uložený v mezipaměti na serveru edge CDN.  Všechny podřízené mezipaměti, jako je například proxy servery a místní prohlížeče mezipaměti, může stále obsahující kopii souboru v mezipaměti.  Je důležité pamatovat na to při nastavení souboru time to live.  Můžete vynutit podřízené klienta k žádosti o nejnovější verzi souboru tím, že je jedinečný název pokaždé, když ho aktualizujete, nebo přímým [řetězce dotazu do mezipaměti](cdn-query-string.md).  
> 
> 

Tento kurz vás provede vyprazdňování prostředky ze všech uzlů edge koncového bodu.

## <a name="walkthrough"></a>Názorný postup
1. V [portálu Azure](https://portal.azure.com), vyhledejte profil CDN obsahující koncový bod, které chcete vymazat.
2. V okně profil CDN klikněte na tlačítko vyprázdnění.
   
    ![Okno profil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Otevře se okno vyprázdnění.
   
    ![Okno vyprázdnění CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. V okně vyprázdnění vyberte adresu služby, který si přejete vymazat z rozevíracího seznamu adresy URL.
   
    ![Vyprázdnění formuláře](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Můžete se do okna vyprázdnění kliknutím také získat **mazání** tlačítka v okně koncového bodu CDN.  V takovém případě **URL** pole bude předem vyplněny adresu služby tento konkrétní koncového bodu.
   > 
   > 
4. Vyberte, jaké prostředky, které chcete vymazat z uzlů okraj.  Pokud chcete vymazat všechny prostředky, klikněte **vyprázdnit všechno** zaškrtávací políčko.  Jinak, zadejte cestu každý prostředek, který si přejete vymazat v **cesta** textové pole. Následující formáty, které jsou podporovány v cestě.
    1. **Vyprázdnění jedné adresy URL**: vyprázdnění jednotlivý prostředek zadáním úplnou adresu URL s nebo bez přípony souboru, například`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Zástupné vyprázdnění**: hvězdičky (\*) může být použita jako zástupný znak. Vyprázdnit všechny složky, podsložky a soubory v koncovém bodě s `/*` v cestě nebo vyprázdnění všechny podsložky a soubory v konkrétní složce zadáním složce následuje `/*`, například`/pictures/*`.  Všimněte si, že zástupné vyprázdnění nepodporuje Azure CDN společnosti Akamai aktuálně. 
    3. **Kořenové domény vyprázdnění**: vyprázdnění kořenovém koncový bod s "/" v cestě.
   
   > [!TIP]
   > Cesty pro vyprázdnění musí být zadána a musí být relativní adresa URL, která podle následující [regulární výraz](https://msdn.microsoft.com/library/az24scfc.aspx). **Vyprázdnit všechny** a **zástupné vyprázdnění** nepodporuje **Azure CDN společnosti Akamai** aktuálně.
   > > Vyprázdnění jedné adresy URL `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Řetězec dotazu `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Zástupné vyprázdnění `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Další **cesta** textová pole se zobrazí, když zadáte text, který vám umožní sestavit seznam více prostředků.  Prostředky můžete odstranit ze seznamu kliknutím na tlačítko se třemi tečkami (...).
   > 
5. Klikněte **mazání** tlačítko.
   
    ![Vyprázdnění tlačítko](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Žádosti o vymazání trvat přibližně 2 – 3 minut zpracovat **Azure CDN společnosti Verizon** (standard a premium) a přibližně 7 minut s **Azure CDN společnosti Akamai**.  Limit souběžných 50 mazání požadavky v daném okamžiku na úrovni profilu je Azure CDN. 
> 
> 

## <a name="see-also"></a>Další informace najdete v tématech
* [Předběžné načtení prostředků v koncovém bodu Azure CDN](cdn-preload-endpoint.md)
* [Azure referenční dokumentace rozhraní API REST CDN - mazání nebo předběžné načtení koncový bod](https://msdn.microsoft.com/library/mt634451.aspx)

