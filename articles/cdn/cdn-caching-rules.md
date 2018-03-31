---
title: Řízení s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti Azure CDN | Microsoft Docs
description: CDN ukládání do mezipaměti pravidla můžete nastavit nebo změnit výchozí chování vypršení platnosti mezipaměti globálně i s podmínkami, jako je například adresa URL a cesta k souboru rozšíření.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 735978a0986b2b16b4f96faca78c06d798915002
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Ovládací prvek Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti

> [!NOTE] 
> Jsou k dispozici pouze pro ukládání do mezipaměti pravidla **Azure CDN společnosti Verizon Standard** a **Azure CDN společnosti Akamai Standard**. Pro **Azure CDN společnosti Verizon Premium**, můžete použít [stroj pravidel Azure CDN](cdn-rules-engine.md) v **spravovat** portálu pro podobné funkce.
 
Azure Content Delivery Network (CDN) nabízí dva způsoby, jak řídit, jak se vaše soubory uložené v mezipaměti: 

- Ukládání do mezipaměti pravidla: Tento článek popisuje, jak můžete použít sítě pro doručování obsahu (CDN) ukládání do mezipaměti pravidla můžete nastavit nebo změnit výchozí chování vypršení platnosti mezipaměti globálně i s vlastní podmínky, jako je například adresa URL a cesta k souboru rozšíření. Azure CDN nabízí dva typy ukládání do mezipaměti pravidla:
   - Ukládání do mezipaměti pravidel globální: jedno pravidlo globální ukládání do mezipaměti pro každý koncový bod můžete nastavit v profilu, který ovlivňuje všechny požadavky na koncový bod. Je globální pravidlo pro ukládání do mezipaměti přepíše jakékoli mezipaměti direktiva hlavičky protokolu HTTP, pokud nastavení.
   - Vlastní pravidla pro ukládání do mezipaměti: můžete nastavit jeden nebo více ukládání vlastní pravidla pro každý koncový bod ve vašem profilu. Ukládání do mezipaměti pravidla shody konkrétní cesty a přípony souborů, jsou zpracovávány v pořadí a přepsat globální pravidlo ukládání do mezipaměti, pokud vlastní nastavení. 

- Ukládání do mezipaměti řetězce dotazu: můžete upravit způsob, jakým Azure CDN zpracovává ukládání do mezipaměti pro žádostí s řetězci dotazu. Informace najdete v tématu [řízení Azure CDN ukládání do mezipaměti chování řetězce dotazu](cdn-query-string.md). Pokud není soubor lze uložit do mezipaměti, řetězec dotazu ukládání do mezipaměti nastavení nemá žádný vliv, založené na ukládání do mezipaměti pravidla a CDN výchozí chování.

Informace o výchozí chování ukládání do mezipaměti a ukládání do mezipaměti direktivy hlavičky najdete v tématu [průběh ukládání do mezipaměti](cdn-how-caching-works.md).

## <a name="tutorial"></a>Kurz

Postup nastavení ukládání do mezipaměti CDN pravidla:

1. Otevřete portál Azure, vyberte profil CDN a pak vyberte koncový bod.
2. V levém podokně v části nastavení, vyberte **ukládání do mezipaměti pravidla**.

   ![Ukládání do mezipaměti CDN tlačítko pravidla](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

3. Vytvořte globální pravidlo ukládání do mezipaměti takto:
   1. V části **globální ukládání do mezipaměti pravidla**, nastavte **chování ukládání řetězců s dotazy** k **ignorovat řetězce dotazů**.
   2. Nastavit **chování ukládání do mezipaměti** k **nastavit, pokud chybí**.
       
   3. Pro **mezipaměti Doba vypršení platnosti**, zadejte 10 v **dní** pole.

       Globální ukládání do mezipaměti pravidlo ovlivní všechny požadavky na koncový bod. Toto pravidlo ctí hlavičky cache – direktiva původu, pokud existují (`Cache-Control` nebo `Expires`); jinak, pokud nejsou zadané, nastaví mezipaměť na 10 dnů. 

     ![Globální pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-global-caching-rules.png)

4. Vytvořte vlastní pravidlo pro ukládání do mezipaměti takto:
    1. V části **vlastní ukládání do mezipaměti pravidla**, nastavte **vyhovují podmínce** k **cesta** a **odpovídají hodnotě** k `/images/*.jpg`.
    2. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte 30 v **dní** pole.
       
       Toto vlastní pravidlo pro ukládání do mezipaměti nastaví dobu trvání mezipaměti 30 dní na žádném `.jpg` bitové kopie souborů v `/images` složky váš koncový bod. Přepíše všechny `Cache-Control` nebo `Expires` hlavičky HTTP, které odesílá na zdrojový server.

    ![Vlastní pravidla ukládání do mezipaměti](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
> [!NOTE] 
> Soubory, které jsou uložené v mezipaměti před změnou pravidlo zachovat jejich nastavení doby trvání mezipaměti původu. Pokud chcete resetovat jejich mezipaměti doby trvání, je nutné [vyprázdnění souboru](cdn-purge-endpoint.md). Pro **Azure CDN společnosti Verizon** koncových bodů, může trvat až 90 minut pro ukládání do mezipaměti pravidla vstoupily v platnost.

## <a name="reference"></a>Referenční informace

### <a name="caching-behavior-settings"></a>Nastavení chování ukládání do mezipaměti
Pro globální a vlastní pravidla ukládání do mezipaměti, můžete zadat následující **chování ukládání do mezipaměti** nastavení:

- **Nepoužívat mezipaměti**: Nezadávejte mezipaměti a ignorovat zadaný počátek mezipaměti direktiva hlavičky.
- **Přepsání**: Ignorovat zadaný počátek hlavičky cache – direktiva; místo toho použijte doba zadaná mezipaměti.
- **Nastavit, pokud chybí**: dodržet zadaný počátek mezipaměti direktiva hlavičky, pokud existují; jinak použijte doba zadaná mezipaměti.

### <a name="cache-expiration-duration"></a>Doba vypršení platnosti mezipaměti
Pro globální a vlastní pravidla ukládání do mezipaměti můžete zadat dobu vypršení uložení do mezipaměti v dny, hodiny, minuty a sekundy:

- Pro **přepsat** a **nastavit, pokud chybí** **chování ukládání do mezipaměti** nastavení, platný mezipaměti doby trvání rozsahu od 0 sekund do 366 dní. Na hodnotu 0 s CDN ukládá do mezipaměti obsah, ale musí znovu ověřit každý požadavek je zdrojový server.
- Pro **vynechat mezipaměti** nastavení, doba uložení do mezipaměti bude automaticky nastavena na 0 sekund a nedá se změnit.

### <a name="custom-caching-rules-match-conditions"></a>Vlastní ukládání do mezipaměti pravidla podmínky shody

Pro vlastní mezipaměti pravidla jsou k dispozici dvě shody podmínky:
 
- **Cesta**: Tento stav odpovídá cestu adresy URL, s výjimkou názvu domény a podporuje zástupný znak (\*). Například `/myfile.html`, `/my/folder/*`, a `/my/images/*.jpg`. Maximální délka je delší než 260 znaků.

- **Rozšíření**: Tento stav odpovídá příponu souboru požadovaného souboru. Můžete zadat seznam přípon souborů oddělený čárkami tak, aby odpovídaly. Například `.jpg`, `.mp3`, nebo `.png`. Maximální počet rozšíření je 50 a maximální počet znaků na jednu rozšíření je 16. 

### <a name="global-and-custom-rule-processing-order"></a>Pořadí zpracování globální a vlastní pravidla
Globální a vlastní ukládání do mezipaměti pravidla se zpracovávají v následujícím pořadí:

- Globální ukládání do mezipaměti pravidla mají přednost před chování ukládání do mezipaměti CDN výchozí (nastavení mezipaměti direktiva záhlaví HTTP). 

- Vlastní pravidla pro ukládání do mezipaměti mají přednost před globální pravidla ukládání do mezipaměti, kde se vztahují. Vlastní pravidla pro ukládání do mezipaměti se zpracovávají v pořadí od shora dolů. To znamená pokud požadavek odpovídá splněny obě podmínky, pravidla v dolní části seznamu mají přednost před pravidly v horní části seznamu. Proto byste měli umístit konkrétnější pravidla nižší v seznamu.

**Příklad**:
- Globální pravidlo pro ukládání do mezipaměti: 
   - Chování ukládání do mezipaměti: **přepsání**
   - Mezipaměti Doba vypršení platnosti: 1 den

- Vlastní ukládání do mezipaměti pravidlo #1:
   - Vyhovují podmínce: **cesta**
   - Hodnota shody: `/home/*`
   - Chování ukládání do mezipaměti: **přepsání**
   - Mezipaměti Doba vypršení platnosti: 2 dny

- Vlastní pravidla #2 ukládání do mezipaměti:
   - Vyhovují podmínce: **rozšíření**
   - Hodnota shody: `.html`
   - Chování ukládání do mezipaměti: **nastavit, pokud chybí**
   - Mezipaměti Doba vypršení platnosti: 3 dny.

Když tato pravidla jsou nastavené, žádost o `<endpoint>.azureedge.net/home/index.html` vlastní ukládání do mezipaměti pravidlo #2, který je nastaven na aktivační události: **nastavit, pokud chybí** a 3 dny. Proto pokud `index.html` soubor má `Cache-Control` nebo `Expires` hlavičky protokolu HTTP, jsou přijmout; jinak, pokud tyto hlavičky nejsou nastaveny, soubor je uložená v mezipaměti pro 3 dny.

