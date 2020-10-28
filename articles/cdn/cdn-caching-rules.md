---
title: Řízení chování při ukládání Azure CDN do mezipaměti s pravidly pro ukládání do mezipaměti | Microsoft Docs
description: Pravidla ukládání do mezipaměti CDN můžete použít k nastavení nebo úpravě výchozích funkcí vypršení platnosti mezipaměti globálně i s podmínkami, jako je například cesta URL a přípony souborů.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: a5f4f6a6e72b57638688069111071a6e0a035c49
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778961"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Řízení chování při ukládání do mezipaměti v Azure CDN s využitím pravidel ukládání do mezipaměti

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici pouze pro **Azure CDN Standard od Verizon** a **Azure CDN Standard od profilů Akamai** . Pro **Azure CDN z profilů Microsoftu** musíte použít modul [Standard rules](cdn-standard-rules-engine-reference.md) pro **Azure CDN Premium ze profilů Verizon** , musíte použít [modul pravidel Verizon Premium](./cdn-verizon-premium-rules-engine.md) na portálu pro **správu** pro podobné funkce.
 
Azure Content Delivery Network (CDN) nabízí dva způsoby, jak řídit ukládání souborů do mezipaměti: 

- Pravidla ukládání do mezipaměti: Tento článek popisuje, jak můžete použít pravidla pro ukládání do mezipaměti sítě Content Delivery Network (CDN) k nastavení nebo úpravě výchozích funkcí vypršení platnosti mezipaměti globálně i s vlastními podmínkami, jako je například cesta URL a Přípona souboru. Azure CDN nabízí dva typy pravidel ukládání do mezipaměti:

   - Globální pravidla ukládání do mezipaměti: Pro každý koncový bod ve vašem profilu můžete nastavit jedno globální pravidlo pro ukládání do mezipaměti, které se bude vztahovat na všechny požadavky na koncový bod. Pokud jsou HTTP hlavičky direktiv pro mezipaměť nastavené, globální pravidlo ukládání do mezipaměti je přepíše.

   - Vlastní pravidla ukládání do mezipaměti: pro každý koncový bod ve svém profilu můžete nastavit jedno nebo více vlastních pravidel ukládání do mezipaměti. Vlastní pravidla ukládání do mezipaměti se shodují s cestami a příponami souborů, zpracovávají se v daném pořadí, a pokud je nastavené globální pravidlo ukládání do mezipaměti, přepíší ho. 

- Ukládání řetězce dotazu do mezipaměti: můžete upravit způsob, jakým Azure CDN nakládat s ukládáním do mezipaměti pro žádosti s řetězci dotazu. Informace najdete v tématu [Azure CDN řízení chování při ukládání do mezipaměti pomocí řetězců dotazu](cdn-query-string.md). Pokud soubor není možné ukládat do mezipaměti, nemá nastavení ukládání řetězců dotazů žádný účinek, a to na základě pravidel ukládání do mezipaměti a výchozího chování CDN.

Informace o výchozích hlavičkách chování ukládání do mezipaměti a direktivy mezipaměti najdete v tématu [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Přístup k pravidlům pro ukládání Azure CDN do mezipaměti

1. Otevřete Azure Portal, vyberte profil CDN a pak vyberte koncový bod.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti** .

   ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti** .

   ![Stránka CDN Pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Nastavení chování ukládání do mezipaměti
Pro globální a vlastní pravidla ukládání do mezipaměti můžete zadat následující nastavení **chování ukládání do mezipaměti** :

- **Mezipaměť obcházení** : Neukládat do mezipaměti a ignorovat hlavičky zadané v mezipaměti – direktivy.

- **Override** : ignorovat zdroj – poskytnutá doba ukládání do mezipaměti; místo toho použijte zadanou dobu trvání mezipaměti. Tato akce nepřepisuje řízení mezipaměti: no-cache.

- **Nastavit, pokud chybí** : dodržovat hlavičky v mezipaměti – poskytnuté původce, pokud existují; v opačném případě použijte poskytnutou dobu trvání mezipaměti.

![Globální pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Vlastní pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Doba vypršení platnosti mezipaměti
Pro globální a vlastní pravidla ukládání do mezipaměti můžete zadat dobu platnosti mezipaměti ve dnech, hodinách, minutách a sekundách:

- Pro **přepsání** a **nastavení, pokud chybí** nastavení **chování ukládání do mezipaměti** , platí rozsah platných dob trvání mezipaměti v rozmezí 0 sekund až 366 dní. V případě hodnoty 0 sekund bude CDN obsah ukládat do mezipaměti, ale musí znovu ověřit každý požadavek na zdrojovém serveru.

- Pro nastavení **mezipaměť bypass** je doba trvání mezipaměti automaticky nastavena na 0 sekund a nelze ji změnit.

## <a name="custom-caching-rules-match-conditions"></a>Vlastní pravidla ukládání do mezipaměti splňují podmínky

Pro vlastní pravidla mezipaměti jsou k dispozici dvě podmínky shody:
 
- **Cesta** : Tento stav odpovídá cestě k adrese URL s výjimkou názvu domény a podporuje zástupný znak ( \* ). Například _/myfile.html_ , _/My/Folder/*_ a _/My/images/*. jpg_ . Maximální délka je 260 znaků.

- **Přípona** : Tento stav odpovídá příponě souboru požadovaného souboru. Můžete zadat seznam přípon souborů oddělených čárkami, které se mají spárovat. Například _. jpg_ , _. mp3_ nebo _. png_ . Maximální počet rozšíření je 50 a maximální počet znaků na rozšíření je 16. 

## <a name="global-and-custom-rule-processing-order"></a>Pořadí zpracování globálních a vlastních pravidel
Globální a vlastní pravidla ukládání do mezipaměti jsou zpracovávána v následujícím pořadí:

- Globální pravidla ukládání do mezipaměti mají přednost před výchozím chováním ukládání do mezipaměti CDN (nastavení hlavičky mezipaměti protokolu HTTP). 

- Vlastní pravidla ukládání do mezipaměti mají přednost před globálními pravidly ukládání do mezipaměti, kde se vztahují. Vlastní pravidla ukládání do mezipaměti jsou zpracovávána v pořadí shora dolů. To znamená, že pokud požadavek odpovídá oběma podmínkám, pravidla v dolní části seznamu mají přednost před pravidly v horní části seznamu. Proto byste měli v seznamu umístit více specifických pravidel níže.

**Příklad** :
- Globální pravidlo ukládání do mezipaměti: 
   - Chování při ukládání do mezipaměti: **přepsat**
   - Doba vypršení platnosti mezipaměti: 1 den

- Vlastní pravidlo ukládání do mezipaměti #1:
   - Podmínka shody: **cesta**
   - Hodnota shody: _/Home/*_
   - Chování při ukládání do mezipaměti: **přepsat**
   - Doba vypršení platnosti mezipaměti: 2 dny

- Vlastní pravidlo ukládání do mezipaměti #2:
   - Podmínka shody: **rozšíření**
   - Hodnota shody: _. html_
   - Chování při ukládání do mezipaměti: **nastavit, pokud chybí**
   - Doba vypršení platnosti mezipaměti: 3 dny

Když jsou tato pravidla nastavena, požadavek na _&lt; název hostitele &gt; koncového bodu_ . azureedge.NET/Home/index.html aktivuje vlastní pravidlo ukládání do mezipaměti #2, které je nastaveno na: **nastaveno, pokud chybí** a 3 dny. Proto platí, že pokud soubor *index.html* má `Cache-Control` nebo obsahuje `Expires` hlavičky HTTP, budou se respektovat. v opačném případě platí, že pokud nejsou tato záhlaví nastavená, soubor se uloží do mezipaměti 3 dny.

> [!NOTE] 
> Soubory, které jsou uloženy v mezipaměti před změnou pravidla, udržují nastavení doby platnosti mezipaměti původní. Pokud chcete resetovat dobu trvání mezipaměti, musíte [soubor vyprázdnit](cdn-purge-endpoint.md). 
>
> Provedení změn konfigurace Azure CDN může trvat delší dobu, než se bude síť šířit: 
> - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
> - Pro **Azure CDN Standard od profilů Verizon** se šíření obvykle dokončuje za 10 minut.  
>

## <a name="see-also"></a>Viz také

- [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md)
- [Kurz: Nastavení pravidel ukládání do mezipaměti Azure CDN](cdn-caching-rules-tutorial.md)