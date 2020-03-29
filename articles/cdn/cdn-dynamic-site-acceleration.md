---
title: Dynamická akcelerace webu přes Azure CDN
description: Azure CDN podporuje optimalizaci dynamické akcelerace webu (DSA) pro soubory s dynamickým obsahem.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 08e705d3c3623d4d02ccaea609eb0555aa1c8e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593926"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamická akcelerace webu přes Azure CDN

S explozí sociálních médií, elektronického obchodu a hyperpersonalizovaného webu se rychle rostoucí procento obsahu, který slouží koncovým uživatelům, generuje v reálném čase. Uživatelé očekávají rychlé, spolehlivé a personalizované webové prostředí nezávislé na prohlížeči, poloze, zařízení nebo síti. Nicméně, velmi inovace, které činí tyto zkušenosti tak poutavé, také zpomalují stahování stránek a ohrožují kvalitu zkušeností spotřebitelů. 

Standardní funkce sítě pro doručování obsahu (CDN) zahrnuje možnost ukládat soubory do mezipaměti blíže koncovým uživatelům, aby se urychlilo doručování statických souborů. U dynamických webových aplikací však ukládání obsahu do mezipaměti v okrajových umístěních není možné, protože server generuje obsah v reakci na chování uživatelů. Urychlení doručování takového obsahu je složitější než tradiční ukládání okrajů do mezipaměti a vyžaduje komplexní řešení, které jemně vyladí každý prvek po celé datové cestě od počátku až po doručení. Díky optimalizaci dynamické akcelerace webu Azure CDN (DSA) se měřitelně zlepší výkon webových stránek s dynamickým obsahem.

**Azure CDN od Akamai** a **Azure CDN od Verizonu** nabízejí optimalizaci DSA prostřednictvím nabídky **Optimalizované pro** vytváření koncových bodů. Dynamické zrychlení webu od Microsoftu je nabízeno prostřednictvím [služby Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> Pro **Azure CDN z akamai** profily můžete změnit optimalizaci koncového bodu CDN po jeho vytvoření.
>   
> U profilů **Azure CDN od Verizonu** nemůžete změnit optimalizaci vytvořeného koncového bodu CDN.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfigurace koncového bodu CDN pro urychlení doručování dynamických souborů

Chcete-li nakonfigurovat koncový bod CDN pro optimalizaci doručování dynamických souborů, můžete použít portál Azure, rozhraní API REST nebo libovolné klientské sady SDK k provádění stejné věci programově. 

**Konfigurace koncového bodu CDN pro optimalizaci DSA pomocí portálu Azure:**

1. Na stránce **profilu CDN** vyberte **Koncový bod**.

   ![Přidání nového koncového bodu CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Otevře se podokno **Přidat koncový bod**.

2. V části **Optimalizováno pro**vyberte **možnost Dynamické zrychlení webu**.

    ![Vytvoření nového koncového bodu CDN pomocí DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Do **sondy zadejte**platnou cestu k souboru.

    Cesta sondy je funkce specifická pro DSA a pro vytvoření je vyžadována platná cesta. DSA používá malý soubor *cesty sondy* umístěné na zdrojovém serveru k optimalizaci konfigurace síťového směrování pro CDN. Pro soubor cesty sondy můžete stáhnout a nahrát ukázkový soubor na web nebo použít existující datový zdroj na vašem původu, který má velikost přibližně 10 kB.

4. Zadejte další požadované možnosti koncového bodu (další informace najdete v [tématu Vytvoření nového koncového bodu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) a vyberte **Přidat**.

   Po vytvoření koncového bodu CDN použije optimalizace DSA pro všechny soubory, které odpovídají určitým kritériím. 


**Konfigurace existujícího koncového bodu pro DSA (jenom Azure CDN z profilů Akamai):**

1. Na stránce **profilu CDN** vyberte koncový bod, který chcete upravit.

2. V levém podokně vyberte **Optimalizace**. 

   Zobrazí se stránka **Optimalizace.**

3. V části **Optimalizováno pro**vyberte **Dynamické zrychlení webu**a pak vyberte **Uložit**.

> [!Note]
> DSA účtuje příplatky. Další informace naleznete v [tématu Content Delivery Network pricing](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optimalizace DSA pomocí Azure CDN

Dynamická akcelerace webu v síti Azure CDN urychluje doručování dynamických datových zdrojů pomocí následujících technik:

-   [Optimalizace trasy](#route-optimization)
-   [Optimalizace protokolu TCP](#tcp-optimizations)
-   [Předběžné načtení objektu (azure cdn z Akamai pouze)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptivní komprese obrázků (Azure CDN pouze z Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optimalizace trasy

Optimalizace trasy je důležitá, protože Internet je dynamické místo, kde provoz a dočasné výpadky neustále mění topologii sítě. Protokol BGP (Border Gateway Protocol) je směrovací protokol Internetu, ale mohou existovat rychlejší trasy prostřednictvím zprostředkujících serverů Point of Presence (PoP). 

Optimalizace trasy zvolí nejoptimálnější cestu k počátku tak, aby byl web nepřetržitě přístupný a dynamický obsah byl koncovým uživatelům doručován nejrychlejší a nejspolehlivější možnou cestou. 

Síť Akamai používá techniky ke shromažďování dat v reálném čase a porovnávání různých cest přes různé uzly na serveru Akamai, stejně jako výchozí trasu Protokolu BGP přes otevřený Internet k určení nejrychlejší trasy mezi původem a okrajem CDN. Tyto techniky se vyhýbají internetovým dopravním bodům a dlouhým trasem. 

Podobně síť Verizon používá kombinaci Anycast DNS, vysoké kapacity podpory POP a kontroly stavu, k určení nejlepší brány pro nejlepší směrování dat z klienta do původu.
 
Výsledkem je, že plně dynamický a transakční obsah je dodáván rychleji a spolehlivěji koncovým uživatelům, i když je neskladovatelný. 

### <a name="tcp-optimizations"></a>Optimalizace protokolu TCP

Protokol TCP (Transmission Control Protocol) je standardem sady internetových protokolů, která slouží k poskytování informací mezi aplikacemi v síti IP.  Ve výchozím nastavení je k nastavení připojení TCP vyžadováno několik požadavků tam a zpět a také omezení, aby se zabránilo přetížení sítě, což vede k neefektivnosti ve velkém měřítku. **Azure CDN z Akamai** zpracovává tento problém optimalizací ve třech oblastech: 

 - [Eliminace pomalého startu protokolu TCP](#eliminating-tcp-slow-start)
 - [Využití trvalých připojení](#leveraging-persistent-connections)
 - [Optimalizace parametrů paketu TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminace pomalého startu protokolu TCP

Pomalé *spuštění* protokolu TCP je algoritmus protokolu TCP, který zabraňuje zahlcení sítě omezením množství dat odeslaných po síti. Začíná malými velikostmi zahlcení mezi odesílatelem a příjemcem, dokud není dosaženo maxima nebo není zjištěna ztráta paketů.

 Oba **Azure CDN z Akamai** a **Azure CDN od Verizon** profily eliminovat TCP pomalé spuštění s následujícími třemi kroky:

1. Monitorování stavu a šířky pásma se používá k měření šířky pásma připojení mezi hraničními servery PoP.
    
2. Metriky jsou sdíleny mezi hraničními servery PoP, takže každý server si je vědom stavu sítě a stavu serveru ostatních popů kolem nich.  
    
3. Hraniční servery CDN předpokládají některé parametry přenosu, například jaká by měla být optimální velikost okna při komunikaci s ostatními hraničními servery CDN v jeho blízkosti. Tento krok znamená, že počáteční velikost okna přetížení může být zvýšena, pokud je stav připojení mezi hraničními servery CDN schopen vyšších přenosů dat paketů.  

#### <a name="leveraging-persistent-connections"></a>Využití trvalých připojení

Pomocí sítě CDN se k vašemu původu přímo připojuje méně jedinečných počítačů ve srovnání s uživateli, kteří se připojují přímo k vašemu původu. Azure CDN také fondy požadavky uživatelů společně navázat méně připojení s původem.

Jak již bylo zmíněno, několik požadavků handshake jsou nutné k vytvoření připojení TCP. Trvalá připojení, která `Keep-Alive` jsou implementována hlavičkou PROTOKOLU HTTP, znovu používají existující připojení TCP pro více požadavků PROTOKOLU HTTP, aby se ušetřily doby odezvy a urychlilo doručování. 

**Azure CDN od společnosti Verizon** také odesílá pravidelné pakety keep-alive přes připojení TCP, aby se zabránilo zavření otevřeného připojení.

#### <a name="tuning-tcp-packet-parameters"></a>Optimalizace parametrů paketu TCP

**Azure CDN od Akamai** vyladí parametry, které řídí připojení mezi servery a snižuje množství dálkových zpátečních cest potřebných k načtení obsahu vloženého do webu pomocí následujících technik:

- Zvýšení počáteční hodovací okno tak, aby bylo možné odeslat více paketů bez čekání na potvrzení.
- Snížení počáteční ho retransmit time out tak, aby byla zjištěna ztráta a k opakovanému přenosu dochází rychleji.
- Snížení minimální a maximální časový limit opakovaného přenosu, aby se zkrátila čekací doba před zapředpokladem, že pakety byly ztraceny v přenosu.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Předběžné načtení objektu (azure cdn z Akamai pouze)

Většina webových stránek se skládá ze stránky HTML, která odkazuje na různé další zdroje, jako jsou obrázky a skripty. Obvykle, když klient požaduje webovou stránku, prohlížeč nejprve stáhne a analyzuje objekt HTML a potom provede další požadavky na propojené datové zdroje, které jsou nutné k úplnému načtení stránky. 

*Předběžné načtení* je technika pro načtení obrázků a skriptů vložených do stránky HTML, zatímco HTML je obsluhován prohlížeči, a ještě předtím, než prohlížeč dokonce tyto požadavky na objekt. 

Při zapnuté možnosti předběžného načtení v době, kdy cdn slouží základní stránce HTML do prohlížeče klienta, cdn analyzuje soubor HTML a další požadavky na všechny propojené prostředky a uloží jej do mezipaměti. Když klient provede požadavky na propojené datové zdroje, server EDGE CDN již má požadované objekty a může je okamžitě obsluhovat bez odezvy na původ. Tato optimalizace je výhodami obsahu, který lze uložit do mezipaměti, i obsahu, který lze ukládat do mezipaměti.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptivní komprese obrázků (Azure CDN pouze z Akamai)

Některá zařízení, zejména mobilní, se čas od času setkávají s pomalejšími rychlostmi sítě. V těchto scénářích je výhodnější pro uživatele přijímat menší obrázky na svých webových stránkách rychleji, než čekat dlouhou dobu na obrázky v plném rozlišení.

Tato funkce automaticky monitoruje kvalitu sítě a využívá standardní metody komprese JPEG, když jsou rychlosti sítě pomalejší, aby se zlepšila doba dodání.

Adaptivní komprese obrazu | Přípony souborů  
--- | ---  
Komprese JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Ukládání do mezipaměti

S DSA ukládání do mezipaměti je ve výchozím nastavení vypnuto `Cache-Control` `Expires` na CDN, i když počátek obsahuje nebo záhlaví v odpovědi. DSA se obvykle používá pro dynamické prostředky, které by neměly být uloženy do mezipaměti, protože jsou jedinečné pro každého klienta. Ukládání do mezipaměti může toto chování přerušit.

Pokud máte webové stránky se směsí statických a dynamických aktiv, je nejlepší přijmout hybridní přístup, abyste dosáhli nejlepšího výkonu. 

Pro **Azure CDN Standard od Verizonu** a Azure CDN Standard z profilů **Akamai** můžete zapnout ukládání do mezipaměti pro konkrétní koncové body DSA pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md).

Přístup k pravidlům ukládání do mezipaměti:

1. Na stránce **profilu CDN** vyberte v části nastavení **pravidla ukládání do mezipaměti**.  
    
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Otevře se stránka **Pravidla ukládání do mezipaměti.**

2. Vytvořte globální nebo vlastní pravidlo ukládání do mezipaměti a zapněte ukládání do mezipaměti pro koncový bod DSA. 

Pro **Azure CDN Premium od Verizon** profily pouze pomocí modulu [pravidel](cdn-rules-engine.md). Všechna pravidla, která jsou vytvořena ovlivnit pouze ty koncové body profilu, které jsou optimalizovány pro DSA. 

Přístup k modulu pravidel:
    
1. Na stránce **profilu CDN** vyberte **Spravovat**.  
    
    ![Tlačítko pro správu profilu CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Otevře se portál pro správu CDN.

2. Na portálu pro správu CDN vyberte **ADN**a pak vyberte **Modul pravidel**. 

    ![Pravidla motorpro DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternativně můžete použít dva koncové body CDN: jeden koncový bod optimalizovaný pomocí DSA k doručování dynamických datových zdrojů a jiný koncový bod optimalizovaný statickým optimalizačním typem, jako je například obecné doručování webu, pro doručování prostředků s možností mezipaměti. Upravte adresy URL webových stránek tak, aby se přímo propojili s datovým zdrojem v koncovém bodě CDN, který chcete použít. 

Například: `mydynamic.azureedge.net/index.html` je dynamická stránka a je načtenz koncového bodu DSA.Stránka html odkazuje na více statických datových zdrojů, jako jsou knihovny JavaScript `mystatic.azureedge.net/banner.jpg` nebo `mystatic.azureedge.net/scripts.js`obrázky načtené ze statického koncového bodu CDN, například a . 



