---
title: Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti | Dokumenty společnosti Microsoft
description: Pravidla ukládání do mezipaměti CDN můžete použít k nastavení nebo úpravě výchozího chování vypršení platnosti mezipaměti jak globálně, tak s podmínkami, jako je cesta URL a přípony souborů.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: 874ec75fb9173b6cee50bf8880510464fa13e9d2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254236"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Řízení chování při ukládání do mezipaměti v Azure CDN s využitím pravidel ukládání do mezipaměti

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici jenom pro **Azure CDN Standard od Verizonu** a Azure CDN Standard z profilů **Akamai.** Pro **profily Azure CDN od Microsoftu** musíte použít [modul standardních pravidel](cdn-standard-rules-engine-reference.md) pro **profily Azure CDN Premium od společnosti Verizon,** musíte použít [modul pravidel Verizon Premium](cdn-rules-engine.md) na portálu **Pro správu** pro podobné funkce.
 
Azure Content Delivery Network (CDN) nabízí dva způsoby, jak řídit, jak jsou vaše soubory ukládány do mezipaměti: 

- Pravidla ukládání do mezipaměti: Tento článek popisuje, jak můžete pomocí pravidel ukládání obsahu do mezipaměti (CDN) nastavit nebo upravit výchozí chování vypršení platnosti mezipaměti globálně i s vlastními podmínkami, jako je například cesta URL a přípona souboru. Azure CDN nabízí dva typy pravidel ukládání do mezipaměti:

   - Globální pravidla ukládání do mezipaměti: Pro každý koncový bod ve vašem profilu můžete nastavit jedno globální pravidlo pro ukládání do mezipaměti, které se bude vztahovat na všechny požadavky na koncový bod. Pokud jsou HTTP hlavičky direktiv pro mezipaměť nastavené, globální pravidlo ukládání do mezipaměti je přepíše.

   - Vlastní pravidla ukládání do mezipaměti: pro každý koncový bod ve svém profilu můžete nastavit jedno nebo více vlastních pravidel ukládání do mezipaměti. Vlastní pravidla ukládání do mezipaměti se shodují s cestami a příponami souborů, zpracovávají se v daném pořadí, a pokud je nastavené globální pravidlo ukládání do mezipaměti, přepíší ho. 

- Ukládání řetězce dotazu do mezipaměti: Můžete upravit, jak Azure CDN zachází ukládání do mezipaměti pro požadavky s řetězci dotazu. Další informace naleznete v [tématu Control Azure CDN caching behavior with query strings](cdn-query-string.md). Pokud soubor nelze uložit do mezipaměti, nemá nastavení ukládání řetězce dotazu do mezipaměti žádný vliv na základě pravidel ukládání do mezipaměti a výchozího chování sítě CDN.

Informace o výchozím chování ukládání do mezipaměti a záhlaví direktivu ukládání do mezipaměti naleznete v tématu [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Přístup k pravidlům ukládání do mezipaměti Azure CDN

1. Otevřete portál Azure, vyberte profil CDN a vyberte koncový bod.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka CDN Pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Nastavení chování ukládání do mezipaměti
Pro globální a vlastní pravidla ukládání do mezipaměti můžete zadat následující nastavení **chování ukládání do mezipaměti:**

- **Obejít mezipaměť**: Neukládat do mezipaměti a ignorovat hlavičky směrnice mezipaměti poskytované původem.

- **Přepsání**: Ignorovat dobu trvání mezipaměti poskytovaná podle počátku; místo toho použijte poskytnutou dobu trvání mezipaměti. Tím se nepřepíše řízení mezipaměti: bez mezipaměti.

- **Set if missing**: Honor origin-provided cache-directive headers, pokud existují; v opačném případě použijte zajišťovnou dobu trvání mezipaměti.

![Globální pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Vlastní pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Doba trvání mezipaměti
Pro globální a vlastní pravidla ukládání do mezipaměti můžete určit dobu trvání vypršení platnosti mezipaměti ve dnech, hodinách, minutách a sekundách:

- Pro **přepsat** a **nastavit, pokud chybí** nastavení chování ukládání do **mezipaměti,** platné doby trvání mezipaměti rozsah mezi 0 sekund a 366 dnů. Pro hodnotu 0 sekund cdn ukládá obsah do mezipaměti, ale musí znovu ověřit každý požadavek s zdrojovým serverem.

- Pro nastavení **mezipaměti Přejezd** je doba trvání mezipaměti automaticky nastavena na 0 sekund a nelze ji změnit.

## <a name="custom-caching-rules-match-conditions"></a>Vlastní pravidla ukládání do mezipaměti odpovídají podmínkám

Pro vlastní pravidla mezipaměti jsou k dispozici dvě podmínky shody:
 
- **Cesta**: Tato podmínka odpovídá cestě k adrese URL s výjimkou názvu\*domény a podporuje zástupný symbol ( ). Například _/myfile.html_, _/my/folder/*_ a _/my/images/*.jpg_. Maximální délka je 260 znaků.

- **Přípona**: Tato podmínka odpovídá příponě souboru požadovaného souboru. Můžete zadat seznam přípon souborů oddělených čárkami, které se budou shodovat. Například _.jpg_, _.mp3_nebo _.png_. Maximální počet rozšíření je 50 a maximální počet znaků na rozšíření je 16. 

## <a name="global-and-custom-rule-processing-order"></a>Globální a vlastní pořadí zpracování pravidel
Globální a vlastní pravidla ukládání do mezipaměti jsou zpracována v následujícím pořadí:

- Globální pravidla ukládání do mezipaměti mají přednost před výchozím chováním ukládání do mezipaměti CDN (nastavení hlavičky direktivy mezipaměti HTTP). 

- Vlastní pravidla ukládání do mezipaměti mají přednost před globálními pravidly ukládání do mezipaměti, kde se použijí. Vlastní pravidla ukládání do mezipaměti jsou zpracovávána v pořadí shora dolů. To znamená, že pokud požadavek odpovídá oběma podmínkám, pravidla v dolní části seznamu mají přednost před pravidly v horní části seznamu. Proto byste měli umístit konkrétnější pravidla nižší v seznamu.

**Příklad**:
- Pravidlo globálního ukládání do mezipaměti: 
   - Chování ukládání do mezipaměti: **Přepsat**
   - Doba vypršení platnosti mezipaměti: 1 den

- Pravidlo vlastního ukládání do mezipaměti #1:
   - Podmínka shody: **Cesta**
   - Hodnota zápasu: _/home/*_
   - Chování ukládání do mezipaměti: **Přepsat**
   - Doba vypršení platnosti mezipaměti: 2 dny

- Pravidlo ukládání do mezipaměti #2:
   - Podmínka shody: **Rozšíření**
   - Hodnota shody: _.html_
   - Chování ukládání do mezipaměti: **Nastavit, pokud chybí**
   - Doba vypršení platnosti mezipaměti: 3 dny

Pokud jsou tato pravidla nastavena, požadavek na _ &lt;název&gt;hostitele koncového bodu_.azureedge.net/home/index.html aktivuje vlastní pravidlo ukládání do mezipaměti #2, která je nastavena **na: Nastavit, pokud chybí,** a 3 dny. Proto pokud soubor *index.html* `Cache-Control` `Expires` má nebo HTTP záhlaví, jsou dodrženy; v opačném případě, pokud tyto hlavičky nejsou nastaveny, soubor je uložen do mezipaměti po dobu 3 dnů.

> [!NOTE] 
> Soubory, které jsou uloženy do mezipaměti před změnou pravidla, zachovávají nastavení doby trvání mezipaměti původu. Chcete-li obnovit dobu trvání mezipaměti, je nutné [soubor vyčistit](cdn-purge-endpoint.md). 
>
> Změny konfigurace Azure CDN může nějakou dobu trvat, než se rozšíří v síti: 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - Pro **Azure CDN Standard od Verizon** profily šíření obvykle dokončí za 10 minut.  
>

## <a name="see-also"></a>Viz také

- [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md)
- [Kurz: Nastavení pravidel ukládání do mezipaměti Azure CDN](cdn-caching-rules-tutorial.md)
