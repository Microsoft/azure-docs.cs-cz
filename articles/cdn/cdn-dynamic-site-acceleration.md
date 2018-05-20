---
title: Akcelerace dynamických webů prostřednictvím Azure CDN
description: Azure CDN podporuje dynamických webů (DSA) acceleration optimalizace pro soubory s dynamickým obsahem.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: rli; v-deasim
ms.openlocfilehash: 4c0a68fd7b6cdf96bb495f6b447299bdbc5772f7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Akcelerace dynamických webů prostřednictvím Azure CDN

S prudký nárůst počtu sociálních médií, elektronické obchodování a webové technologie hyper přizpůsobené rychle roste procento obsah obsluhuje koncovým uživatelům se generuje v reálném čase. Uživatelé očekávají, že rychlé, spolehlivé a přizpůsobené webové prostředí, nezávisle na jejich prohlížeče, umístění, zařízení nebo sítě. Však velmi inovací, které tyto činnosti, takže zapojení také zpomalit stránce stahování a ohrozit kvalitu prostředí pro uživatele. 

Standardní doručování obsahu (CDN) sítě schopností zahrnuje možnost souborů z mezipaměti blíže koncovým uživatelům pro urychlení doručení statických souborů. Však s dynamické webové aplikace, ukládání do mezipaměti tohoto obsahu v hraniční umístění není možné, protože server vygeneruje obsah v reakci na chování uživatele. Urychlení doručování takový obsah je mnohem složitější než tradiční edge ukládání do mezipaměti a vyžaduje-komplexní řešení, která přesné vyladění každý prvek v cestě celého datového od zahájení k doručení. S optimalizací (DSA) acceleration dynamických webů Azure CDN měřitelný výkon vyšší výkon webových stránek s dynamickým obsahem.

**Azure CDN společnosti Akamai** a **Azure CDN společnosti Verizon** nabízejí DSA optimalizace prostřednictvím **optimalizované pro** nabídky během vytvoření koncového bodu.

> [!Important]
> Pro **Azure CDN společnosti Akamai** profily mají být povoleno změnit optimalizace koncový bod CDN po jeho vytvoření.
>   
> Pro **Azure CDN společnosti Verizon** profily, nelze změnit optimalizace koncový bod CDN po jeho vytvoření.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Konfigurace koncového bodu CDN pro urychlení doručování dynamické soubory

Ke konfiguraci koncového bodu CDN k optimalizaci doručování dynamické soubory, můžete použít buď na portálu Azure, rozhraní REST API nebo některou z klienta sady SDK pro stejnou věc udělat prostřednictvím kódu programu. 

**Konfigurace koncového bodu CDN pro optimalizaci DSA pomocí portálu Azure:**

1. V **profil CDN** vyberte **koncový bod**.

   ![Přidat nový koncový bod CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Otevře se podokno **Přidat koncový bod**.

2. V části **optimalizované pro**, vyberte **dynamických webů akcelerace**.

    ![Vytvořte nový koncový bod CDN s DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Pro **testu cesta**, zadejte platnou cestu k souboru.

    Test cesta je funkce specifická pro agenta DSA, a je požadována pro vytvoření platná cesta. DSA používá malá *testu cesta* soubor umístěn na původním serveru za účelem optimalizace směrování konfigurace sítě pro CDN. Cesta k souboru testu můžete stáhnout a odesílání ukázkový soubor do vaší lokality nebo použijte existující prostředek na vaše původ, který je velikost asi 10 KB.

4. Zadejte další požadované koncový bod možnosti (Další informace najdete v tématu [vytvořte nový koncový bod CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), pak vyberte **přidat**.

   Po vytvoření koncového bodu CDN, bude se vztahovat DSA optimalizace pro všechny soubory, které splňují určitá kritéria. 


**Koncový bod existující konfigurace pro agenta DSA (Azure CDN společnosti Akamai profily pouze):**

1. V **profil CDN** vyberte koncového bodu, kterou chcete upravit.

2. V levém podokně vyberte **optimalizace**. 

   **Optimalizace** se zobrazí stránka.

3. V části **optimalizované pro**, vyberte **dynamických webů akcelerace**, pak vyberte **Uložit**.

> [!Note]
> DSA budou vám účtovány další poplatky. Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optimalizace DSA pomocí Azure CDN

Dynamické akcelerace lokality na Azure CDN urychluje doručení dynamické prostředků pomocí následujících postupů:

-   [Optimalizace směrování](#route-optimization)
-   [Optimalizace TCP](#tcp-optimizations)
-   [Předběžné načtení objektu (Azure CDN společnosti Akamai pouze)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Komprese Adaptivní bitové kopie (Azure CDN společnosti Akamai pouze)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optimalizace směrování

Optimalizace směrování je důležité, protože Internetu je dynamické místo, kde provozu a dočasně výpadků se neustále mění síťové topologie. Protokol BGP (Border Gateway) je protokol směrování v Internetu, ale může být rychlejší trasy prostřednictvím zprostředkující serverů bodů přítomnosti (PoP). 

Optimalizace směrování rozhodne, že optimální cesta k počátku tak, aby lokalita se nepřetržitě dostupné a dynamický obsah doručuje koncovým uživatelům prostřednictvím nejrychlejší a nejspolehlivější trasy možné. 

Akamai síť používá techniky ke shromažďování dat v reálném čase a porovnání různé cesty prostřednictvím různých uzlů v Akamai serveru, jakož i výchozí trasa protokolu BGP přes otevřete Internet k určení nejrychlejší trasy mezi zdroji a hraniční CDN. Tyto postupy vyhnout internetové body zahlcení a dlouho trasy. 

Podobně sítě Verizon se používá v kombinaci libovolného vysílání DNS, vysoké kapacity podpora bodů POP a kontroly stavu určit nejlepší brány a nejlepší data trasy od klienta k počátku.
 
V důsledku toho plně dynamické a transakční obsah doručuje rychleji a spolehlivěji koncovým uživatelům, i když se uncacheable. 

### <a name="tcp-optimizations"></a>Optimalizace TCP

Protokol TCP (Transmission Control) je standardní sada protokolů Internet slouží k poskytování informací mezi aplikací v síti IP.  Ve výchozím nastavení je potřeba nastavit připojení TCP, stejně jako omezení předejdete congestions sítě, které umožňuje zvýšit efektivitu škálované za následek několik požadavků a zpět. **Azure CDN společnosti Akamai** zpracovává tento problém pomocí optimalizace do tří oblastí: 

 - [Odstranění TCP pomalé spuštění](#eliminating-tcp-slow-start)
 - [Využívání trvalé připojení](#leveraging-persistent-connections)
 - [Ladění parametry paketu protokolu TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Odstranění TCP pomalé spuštění

TCP *zpomalit spuštění* je algoritmus, protokolu TCP, která zabraňuje zahlcení sítě omezením množství dat posílaných prostřednictvím sítě. Začíná velikosti okna malé zahlcení mezi odesílatele a příjemce až do dosažení maximální nebo je zjištěna ztráta paketů.

 Obě **Azure CDN společnosti Akamai** a **Azure CDN společnosti Verizon** profily eliminovat TCP pomalé začněte s následující tři kroky:

1. Šířka pásma sledování stavu a se používá k měření šířky pásma připojení mezi servery edge PoP.
    
2. Metriky jsou sdílené mezi servery PoP edge tak, aby každý server si je vědoma síťových podmínkách a stavu serveru dalších bodů POP je obcházet.  
    
3. Servery edge CDN Zkontrolujte předpoklady o některé parametry přenosu, například co optimální velikost okna musí být při komunikaci s ostatními servery edge CDN v jeho blízkosti. Tento krok znamená, že velikost okna počáteční zahlcení může být zvýšena, pokud stav připojení mezi servery edge CDN umožňuje přenosech souborů vyšší paketů.  

#### <a name="leveraging-persistent-connections"></a>Využívání trvalé připojení

Pomocí název CDN, menší počet jedinečných počítačů připojení k serveru počátek přímo ve srovnání s uživateli připojovat přímo k do zdrojového umístění. Azure CDN fondu také požadavky uživatelů společně ke zřízení méně spojení s počátek.

Jak jsme uvedli několik požadavků metody handshake vyžadovaných k navázání připojení TCP. Trvalé připojení, které jsou implementované `Keep-Alive` hlavičky protokolu HTTP, opakované použití existující připojení TCP pro víc požadavků HTTP uložte časy odezvy a urychlit doručení. 

**Azure CDN společnosti Verizon** také odesílá pravidelné udržovací pakety přes připojení TCP, aby se zabránilo otevřené připojení z dochází k uzavření.

#### <a name="tuning-tcp-packet-parameters"></a>Ladění parametry paketu protokolu TCP

**Azure CDN společnosti Akamai** vyladí parametry, které řídí připojení k serveru a snižuje množství dálková odezev potřebnou k načtení obsahu vložených v této lokalitě pomocí následujících postupů:

- Zvýšit počáteční zahlcení okna tak, aby pakety další můžete odeslat bez čekání na potvrzení.
- Časový limit opakování počáteční přenosu snížení tak, že se detekuje ztrátu a opakování přenosu dojde rychleji.
- Snížení opakovaného přenosu minimální a maximální časový limit zkrátit čas čekání před za předpokladu, že byly ztrátě paketů při přenosu.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Předběžné načtení objektu (Azure CDN společnosti Akamai pouze)

Většina webů obsahovat stránku HTML, který odkazuje na různé prostředky, jako obrázků a skriptů. Obvykle když klient požádá o webovou stránku, prohlížeč nejprve stáhne a analyzuje objekt HTML a potom provede další žádosti o propojené prostředky, které jsou nutné k plně načtení stránky. 

*Předběžné načtení* je technika načíst obrázků a skriptů vložených na stránce HTML během obsluhovaného kódu HTML do prohlížeče, a před i odesílá požadavky těchto objektů. 

S parametrem předběžné načtení zapnutý v době, kdy CDN slouží základní stránky HTML do prohlížeče klienta CDN analyzuje soubor HTML a další požadavky pro všechny propojené prostředky a uložte ho do své mezipaměti. Když klient zadává požadavky pro propojené prostředky, CDN edge server už má požadovaný objekty a můžete je sloužit bez odezvy na počátek. Tato optimalizace výhody lze uložit do mezipaměti a neurčené obsahu.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Komprese Adaptivní bitové kopie (Azure CDN společnosti Akamai pouze)

Některá zařízení, zejména mobilní ty prostředí nižší rychlost sítě čas od času. V těchto scénářích platí je více vhodné pro rychleji přijímat menší Image jejich webové stránce místo čekání na dlouhou dobu pro bitové kopie úplné řešení.

Tato funkce automaticky monitoruje sítě kvality a využívá standardní metody komprese JPEG při rychlost sítě jsou pomalejší ke zlepšení čas doručení.

Komprese Adaptivní bitové kopie | Přípony souborů  
--- | ---  
Komprese JPEG | JPG, JPEG, JPE, .jig, .jgig, .jgi

## <a name="caching"></a>Ukládání do mezipaměti

S DSA, ukládání do mezipaměti je ve výchozím nastavení vypnuté na CDN, i když počátek zahrnuje `Cache-Control` nebo `Expires` hlavičky v odpovědi. DSA se obvykle používá pro dynamické prostředky, které by neměly uložené v mezipaměti, protože jsou jedinečné pro každého klienta. Ukládání do mezipaměti může dojít k narušení toto chování.

Pokud máte web se smíšenými statické a dynamické prostředky, je nejlepší postup hybridní získat nejlepší výkon. 

Pro **Azure CDN Standard od společnosti Verizon** a **Azure CDN Standard od společnosti Akamai** profilů, můžete zapnout pomocí ukládání do mezipaměti pro specifické koncové body DSA [ukládání do mezipaměti pravidla](cdn-caching-rules.md).

Pro přístup k ukládání do mezipaměti pravidla:

1. Z **profil CDN** stránky v části nastavení, vyberte **ukládání do mezipaměti pravidla**.  
    
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **Ukládání do mezipaměti pravidla** otevře se stránka.

2. Vytvořte globální nebo vlastní ukládání do mezipaměti pravidlo pro zapnutí ukládání do mezipaměti pro koncový bod služby DSA. 

Pro **Azure CDN Premium od společnosti Verizon** pouze profily, zapnete pomocí ukládání do mezipaměti pro specifické koncové body DSA [stroj pravidel](cdn-rules-engine.md). Všechna pravidla, které jsou vytvořené ovlivňují jenom těchto koncových bodů vašeho profilu, které jsou optimalizované pro DSA. 

Pro přístup k stroj pravidel:
    
1. Z **profil CDN** vyberte **spravovat**.  
    
    ![Tlačítko Spravovat profil CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Otevře se na portálu pro správu CDN.

2. Z portálu pro správu CDN vyberte **ADN**, pak vyberte **stroj pravidel**. 

    ![Stroj pravidel pro agenta DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternativně můžete použít dva koncové body CDN: optimalizovaný jeden koncový bod se DSA k poskytování dynamické prostředky a jiným koncovým bodem optimalizovaný se statickou optimalizace typu, například obecné webové přenosy k doručení Uložitelný prostředky. Upravte adresám URL webové stránky na přímý odkaz na prostředek na koncového bodu CDN, které chcete používat. 

Příklad: `mydynamic.azureedge.net/index.html` dynamické stránky a je načíst z DSA koncového bodu.  Stránky html odkazuje na více statických prostředků, například knihoven jazyka JavaScript nebo bitové kopie, které jsou načítány ze statické koncového bodu CDN, jako například `mystatic.azureedge.net/banner.jpg` a `mystatic.azureedge.net/scripts.js`. 



