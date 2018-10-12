---
title: Akcelerace dynamického webu přes Azure CDN
description: Azure CDN podporuje optimalizace (DSA) akcelerace dynamického webu pro soubory s dynamickým obsahem.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: magattus
ms.openlocfilehash: 4fa681e800197ea241ba1c6cf2180ba04b6e565b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092575"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Akcelerace dynamického webu přes Azure CDN

S rozbalení sociálních médií, elektronické obchodování a web hyper individuální prudce rostoucí procento poskytnutý koncovým uživatelům obsah generují v reálném čase. Uživatelé očekávají, že rychlé, spolehlivé a přizpůsobené webové prostředí, nezávisle na jejich prohlížeče, místa, zařízení nebo sítě. Ale velmi inovace, které tyto zkušenosti tak poutavější také zpomalit stránku soubory ke stažení a ohrozit kvalitní uživatelské prostředí. 

Možnost Standard content delivery network (CDN) umožňuje soubory do mezipaměti blíž uživatelům, zrychlit doručování statických souborů. Nicméně s dynamické webové aplikace, ukládání do mezipaměti tohoto obsahu v okrajových umístěních není možné, protože server obsahu vygeneruje v reakci na chování uživatelů. Zrychlení doručování takový obsah je složitější než tradiční edge ukládání do mezipaměti a vyžaduje-ucelené řešení, která přesné vyladění každý prvek na cestě všechna data od zahájení až po dodání. S Azure CDN dynamického webu (DSA) akcelerace optimalizací zlepšení života na zemi lepší výkon webových stránek s dynamickým obsahem.

**Azure CDN od Akamai** a **Azure CDN od Verizonu** obě sady nabízejí DSA optimalizace prostřednictvím **optimalizovaná pro** nabídky během vytváření koncového bodu.

> [!Important]
> Pro **Azure CDN od Akamai** profily mají být povoleno po jejím vytvoření změnit optimalizace koncového bodu CDN.
>   
> U profilů **Azure CDN od Verizonu** nemůžete změnit optimalizaci vytvořeného koncového bodu CDN.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfigurace koncového bodu CDN pro zrychlení doručování dynamického souborů

Konfigurace koncového bodu CDN pro optimalizaci doručení dynamické soubory, můžete použít buď na portálu Azure portal, rozhraní REST API nebo některý z klientské sady SDK pro to samé udělá programově. 

**Konfigurace koncového bodu CDN pro optimalizaci algoritmu DSA pomocí webu Azure portal:**

1. V **profil CDN** stránce **koncový bod**.

   ![Přidat nový koncový bod CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Otevře se podokno **Přidat koncový bod**.

2. V části **optimalizovaná pro**vyberte **akcelerace dynamického webu**.

    ![Vytvořit nový koncový bod CDN s DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Pro **cesta testu paměti**, zadejte platnou cestu k souboru.

    Cesta testu paměti je funkce specifická pro DSA a je vyžadováno pro vytvoření platná cesta. DSA používá malé *cesta testu paměti* soubor umístěn na původním serveru optimalizovat směrování konfigurace sítě pro síť CDN. Cesta souboru testu můžete stáhnout a nahrajte ukázkový soubor na web nebo použijte existující prostředek na váš původní název, který je velikost přibližně 10 KB.

4. Zadejte další požadované koncový bod možnosti (Další informace najdete v tématu [vytvořit nový koncový bod CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) a pak vyberte **přidat**.

   Po vytvoření koncového bodu CDN se vztahuje DSA optimalizace pro všechny soubory, které splňují určitá kritéria. 


**Ke konfiguraci existující koncový bod pro DSA (Azure CDN od Akamai profily pouze):**

1. V **profil CDN** vyberte koncový bod, kterou chcete upravit.

2. V levém podokně vyberte **optimalizace**. 

   **Optimalizace** se zobrazí stránka.

3. V části **optimalizovaná pro**vyberte **akcelerace dynamického webu**a pak vyberte **Uložit**.

> [!Note]
> DSA s sebou nese náklady účtovat další poplatky. Další informace najdete v tématu [ceny Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optimalizace DSA pomocí Azure CDN

Akcelerace dynamického webu ve službě Azure CDN zrychluje doručování dynamického prostředky pomocí následujících postupů:

-   [Optimalizace směrování](#route-optimization)
-   [Optimalizace TCP](#tcp-optimizations)
-   [Předběžné načtení objektů (Azure CDN od Akamai jenom)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptivní komprese obrázků (Azure CDN od Akamai jenom)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optimalizace směrování

Optimalizace směrování je důležité, protože je dynamické místo, kde provoz a dočasně výpadky jsou neustále mění se topologie sítě Internet. Protokolu BGP (Border Gateway) je protokol směrování Internetu, ale může být rychlejší trasy přes zprostředkující servery Point of Presence (PoP). 

Optimalizace směrování zvolí, že optimální cesta k počátku tak, aby lokality nepřetržitě dostupné a dynamický obsah se doručí do koncových uživatelů prostřednictvím nejrychlejší a nejspolehlivější trasy, je to možné. 

Akamai sítě využívá i metody pro shromažďování dat v reálném čase a porovnat různé cesty různé uzly v Akamai serveru, jakož i výchozí trasa protokolu BGP přes otevřený Internet k určení nejrychlejší trasy mezi zdroji a na hraničních zařízeních CDN. Tyto postupy vyhnout Internet zahlcení body a dlouhá trasy. 

Podobně sítě Verizonu se používá v kombinaci Anycast DNS, velkokapacitní podporují bodů POP a kontroly stavu, určit nejlepší brány na nejlepší data trasy z klienta k počátku.
 
V důsledku toho plně dynamického a transakční obsah se doručí rychleji a spolehlivěji koncovým uživatelům, i když je uncacheable. 

### <a name="tcp-optimizations"></a>Optimalizace TCP

Protokolu TCP (Transmission Control) je standardní sadu protokolů sítě Internet, který se používá k předávání informací mezi aplikacemi v síti IP.  Ve výchozím nastavení jsou požadovány k nastavení připojení protokolu TCP, jakož i omezení sítě congestions, vedlo nedostatečnou ve velkém měřítku, aby několik požadavků a zpět. **Azure CDN od Akamai** zpracovává tento problém pomocí optimalizace ve třech oblastech: 

 - [Odstranění TCP pomalého začátku](#eliminating-tcp-slow-start)
 - [Využití trvalého připojení](#leveraging-persistent-connections)
 - [Parametry paketu ladění protokolu TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Odstranění TCP pomalého začátku

TCP *zpomalit spuštění* je algoritmus, který brání zahlcení sítě tím, že omezíte množství dat posílaných prostřednictvím sítě protokolu TCP. Začíná velikosti okna malé přetížení mezi odesílatelem a příjemcem až do dosažení maximální nebo ztráta paketů se detekuje.

 Obě **Azure CDN od Akamai** a **Azure CDN od Verizonu** profily eliminovat TCP pomalé začínat následující tři kroky:

1. Šířka pásma monitorování stavu a se používá k měření šířky pásma připojení mezi servery edge PoP.
    
2. Metriky jsou sdílené mezi hraniční server PoP servery tak, aby každý server je seznámen stavy sítě a serveru stav dalších bodů POP, co s nimi souvisí.  
    
3. Krajních serverů CDN vytvářet předpoklady o některé parametry předávání, například jaké optimální velikost okna by měly být při komunikaci s další krajních serverů CDN v její blízkosti. Tento krok znamená, že počáteční zahlcení velikost okna je možné zvýšit, pokud je stav připojení mezi krajních serverů CDN dokáže vyšší přenosy dat paketů.  

#### <a name="leveraging-persistent-connections"></a>Využití trvalého připojení

Používání sítě CDN, menší počet jedinečných počítačů připojit k serveru původu přímo ve srovnání s uživatelé, kteří se připojují přímo na váš původní název. Azure CDN také fondu uživatelských požadavků současně ke zřízení menší počet spojení s původu.

Jak už jsme zmínili je potřeba několik požadavků na handshake navázání připojení TCP. Trvalá připojení, které jsou implementované `Keep-Alive` hlavičky protokolu HTTP, opakované použití existující připojení TCP více požadavků protokolu HTTP k uložení doby odezvy a zrychlit doručování. 

**Azure CDN od Verizonu** rovněž odesílá pakety pravidelné keep-alive přes připojení TCP, aby se zabránilo otevření připojení z zavírá.

#### <a name="tuning-tcp-packet-parameters"></a>Parametry paketu ladění protokolu TCP

**Azure CDN od Akamai** vylaďuje parametry, které určují připojení k serveru a snižuje počet zpátečních cest dálkovou přepravu potřebný k načtení obsah vložený v lokalitě pomocí následujících postupů:

- V okně počáteční zahlcení zvýšit, tak, aby další pakety odesílat bez čekání na potvrzení.
- Časový limit opakování počáteční snížení tak, že se detekuje ztrátu a Opakovaný přenos zpráv vyvolá rychleji.
- Snížení vypršení časového limitu opakování minimální a maximální doba čekání před za předpokladu, že byly ztrátě paketů při přenosu.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Předběžné načtení objektů (Azure CDN od Akamai jenom)

Většina webů se skládají z stránku HTML, který odkazuje na různé prostředky jako obrázků a skriptů. Obvykle když klient požádá o webovou stránku, prohlížeč nejprve stáhne a analyzuje objekt HTML a dalších požadavků umožňuje propojené prostředky, které jsou nutné k plně načtení stránky. 

*Předběžné načtení* je technika k načtení obrázků a skriptů vložený na stránce HTML zatímco kód HTML je předán do prohlížeče a před prohlížeč umožňuje i požadavky těchto objektů. 

S parametrem předběžné načtení povoleno v době, kdy CDN slouží základní stránce HTML do prohlížeče klienta CDN analyzuje soubor HTML a další požadavky pro všechny propojené prostředky a uložit do mezipaměti. Pokud klient odešle požadavky pro propojené prostředky, CDN edge server už má požadované objekty a může je sloužit bez odezvy zdroji. Tato optimalizace další výhody, možné ukládat do mezipaměti a neurčené obsah.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptivní komprese obrázků (Azure CDN od Akamai jenom)

Některá zařízení, zejména mobilní ty, které jsou občas prostředí nižší rychlostí sítě. V těchto scénářích platí je vhodné více rychleji přijímat menší imagí ve své webové stránce místo čekání na dlouhou dobu pro obrázky v plném rozlišení.

Tato funkce automaticky sleduje kvalitu sítě a využívá standardní metody komprese JPEG při rychlostech sítě jsou pomalejší zlepšit dobu dodání.

Adaptivní komprese obrázků | Přípony souborů  
--- | ---  
Komprese JPEG | JPG, JPEG, JPE, .jig, .jgig, .jgi

## <a name="caching"></a>Ukládání do mezipaměti

S DSA, ukládání do mezipaměti je ve výchozím nastavení vypnuta v síti CDN, i v případě, že zahrnuje původ `Cache-Control` nebo `Expires` hlaviček v odpovědi. DSA se obvykle používá pro dynamické prostředky, které by neměly uložit do mezipaměti, protože jsou jedinečné pro jednotlivé klienty. Ukládání do mezipaměti může dojít k narušení toto chování.

Pokud máte web s využitím kombinace statické a dynamické prostředky, je vhodné provést s hybridním přístupem k co nejlepšího výkonu. 

Pro **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profilů, můžete zapnout pomocí ukládání do mezipaměti pro konkrétní koncové body DSA [pravidla ukládání do mezipaměti](cdn-caching-rules.md).

Pro přístup k pravidla ukládání do mezipaměti:

1. Z **profil CDN** stránky v části Nastavení vyberte **pravidla ukládání do mezipaměti**.  
    
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **Pravidla ukládání do mezipaměti** otevře se stránka.

2. Vytvoření globální nebo vlastní pravidla ukládání do mezipaměti chcete zapnout ukládání do mezipaměti pro vaše koncové body DSA. 

Pro **Azure CDN Premium od Verizonu** pouze profily, posunete na pomocí ukládání do mezipaměti pro konkrétní koncové body DSA [stroj pravidel](cdn-rules-engine.md). Všechna pravidla, která se vytvářejí ovlivňují jenom ty koncové body vašeho profilu, které jsou optimalizovány pro DSA. 

Pro přístup k stroj pravidel:
    
1. Z **profil CDN** stránce **spravovat**.  
    
    ![Tlačítko Spravovat profil CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Otevře se na portálu pro správu CDN.

2. Z portálu pro správu CDN vyberte **ADN**a pak vyberte **stroj pravidel**. 

    ![Stroj pravidel pro DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternativně můžete použít dva koncové body CDN: optimalizace jeden koncový bod s DSA poskytovat dynamické prostředky a jiný koncový bod optimalizované typu statické optimalizace, jako je například obecné doručování webu, k prostředkům doručování možné ukládat do mezipaměti. Upravte vaší adresy URL webové stránky na přímý odkaz na prostředek na koncový bod CDN, které chcete použít. 

Příklad: `mydynamic.azureedge.net/index.html` je dynamické stránky a je načteno z koncového bodu DSA.  Na stránce html odkazuje více statické prostředky, jako jsou knihovny jazyka JavaScript nebo bitové kopie, které jsou načteny ze statické koncový bod CDN, jako například `mystatic.azureedge.net/banner.jpg` a `mystatic.azureedge.net/scripts.js`. 



