---
title: Akcelerace dynamického webu prostřednictvím Azure CDN
description: Azure CDN podporuje optimalizaci dynamické akcelerace webu (DSA) pro soubory s dynamickým obsahem.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778859"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Akcelerace dynamického webu prostřednictvím Azure CDN

S rozpadem sociálních médií, elektronického obchodu a webu přizpůsobeného technologií Hyper-v reálném čase se vygeneruje rychle rostoucí procento obsahu poskytovaného koncovým uživatelům. Uživatelé očekávají rychlé, spolehlivé a přizpůsobené webové prostředí, nezávisle na jeho prohlížeči, umístění, zařízení nebo síti. Ale velice inovace, které tyto prostředí usnadňují, tak, aby se také zpomalily stahování stránek a zajistila kvalita prostředí pro uživatele. 

Funkce Standard Content Delivery Network (CDN) zahrnuje možnost ukládání souborů do mezipaměti blíž k koncovým uživatelům urychlující doručování statických souborů. Nicméně s dynamickými webovými aplikacemi není ukládání obsahu do mezipaměti na hraničních místech možné, protože server vygeneruje obsah v reakci na chování uživatele. Urychlení doručení takového obsahu je složitější než ukládání tradičních hran do mezipaměti a vyžaduje ucelené řešení, které přesně vyladí každý prvek podél celé cesty dat od zahájení do doručení. Díky optimalizaci Azure CDN Dynamic Page Acceleration (DSA) je výkon webových stránek s dynamickým obsahem měřitelnější.

**Azure CDN z Akamai** a **Azure CDN z Verizon** nabízí optimalizaci DSA během vytváření koncového bodu v nabídce **optimalizované pro** . Akcelerace dynamického webu od Microsoftu se nabízí prostřednictvím [služby Azure front-dveří](../frontdoor/front-door-overview.md).

> [!Important]
> Pro **Azure CDN z profilů Akamai** je povoleno změnit optimalizaci koncového bodu CDN po jeho vytvoření.
>   
> U profilů **Azure CDN od Verizonu** nemůžete změnit optimalizaci vytvořeného koncového bodu CDN.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfigurace koncového bodu CDN pro urychlení doručování dynamických souborů

Chcete-li nakonfigurovat koncový bod CDN pro optimalizaci doručování dynamických souborů, můžete buď použít Azure Portal, rozhraní REST API nebo kteroukoli sadu SDK klienta k tomu, aby se mohla provádět stejným krokem programově. 

**Konfigurace koncového bodu CDN pro optimalizaci DSA pomocí Azure Portal:**

1. Na stránce **profil CDN** vyberte **koncový bod** .

   ![Přidání nového koncového bodu CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Otevře se podokno **Přidat koncový bod** .

2. V části **optimalizované pro** vyberte možnost **akcelerace dynamického webu** .

    ![Vytvoření nového koncového bodu CDN pomocí DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. V případě **cesty testu** zadejte platnou cestu k souboru.

    Cesta testu je funkce specifická pro DSA a pro vytvoření je nutná platná cesta. DSA používá pro optimalizaci konfigurace síťového směrování pro CDN na zdrojovém serveru malý soubor *cesty testu* . V případě souboru cesty testu můžete stáhnout a nahrát ukázkový soubor na web nebo použít stávající Asset o velikosti přibližně 10 KB.

4. Zadejte další požadované možnosti koncového bodu (Další informace najdete v tématu [Vytvoření nového koncového bodu CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) a pak vyberte **Přidat** .

   Po vytvoření koncového bodu CDN se aplikují optimalizace DSA pro všechny soubory, které splňují určitá kritéria. 


**Konfigurace existujícího koncového bodu pro DSA (Azure CDN jenom ze profilů Akamai):**

1. Na stránce **profil CDN** vyberte koncový bod, který chcete upravit.

2. V levém podokně vyberte možnost **optimalizace** . 

   Zobrazí se stránka **optimalizace** .

3. V části **optimalizované pro** vyberte možnost **akcelerace dynamického webu** a pak vyberte **Uložit** .

> [!Note]
> DSA další poplatky. Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optimalizace DSA pomocí Azure CDN

Akcelerace dynamického webu na Azure CDN urychluje doručování dynamických prostředků pomocí následujících technik:

-   [Optimalizace tras](#route-optimization)
-   [Optimalizace protokolu TCP](#tcp-optimizations)
-   [Předběžné načtení objektu (Azure CDN jenom z Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptivní komprese obrázků (Azure CDN pouze z Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optimalizace tras

Optimalizace tras je důležitá, protože Internet je dynamické místo, kde provoz a dočasné výpadky neustále mění síťovou topologii. Border Gateway Protocol (BGP) je směrovací protokol internetu, ale může se jednat o rychlejší trasy prostřednictvím serverů PoP (zprostředkujícího bodu přítomnosti). 

Optimalizace tras volí optimální cestu k počátku, takže lokalita bude nepřetržitě přístupná a koncovým uživatelům se doručí dynamický obsah přes nejrychlejší a nejspolehlivější možné směrování. 

Akamai Network využívá techniky ke shromažďování dat v reálném čase a porovnávání různých cest prostřednictvím různých uzlů serveru Akamai a také výchozí trasy protokolu BGP v rámci otevřeného internetu za účelem určení nejrychlejší trasy mezi počátkem a hraniční sítí CDN. Tyto techniky vyloučí body zahlcení Internetu a dlouhé trasy. 

Podobně Verizon síť používá kombinaci DNS s více cestami, pop s vysokou kapacitou a kontroly stavu, aby bylo možné určit nejlepší brány, které budou co nejlépe směrovat data z klienta na počátek.
 
Výsledkem je, že plně dynamický a transakční obsah se rychleji a spolehlivě doručí koncovým uživatelům, a to i v případě, že je nemůžete ukládat do mezipaměti. 

### <a name="tcp-optimizations"></a>Optimalizace protokolu TCP

Protokol TCP (Transmission Control Protocol) je standardem sady protokolů sítě Internet, která slouží k doručování informací mezi aplikacemi v síti IP.  Ve výchozím nastavení se k nastavení připojení TCP vyžaduje několik požadavků na zálohování a omezení, aby se zabránilo zahlcení sítě, což vede k neefektivnímu škálování. **Azure CDN z Akamai** zpracovává tento problém optimalizací ve třech oblastech: 

 - [Odstraňování pomalých spuštění protokolu TCP](#eliminating-tcp-slow-start)
 - [Využití trvalých připojení](#leveraging-persistent-connections)
 - [Vyladění parametrů paketů TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Odstraňování pomalých spuštění protokolu TCP

*Pomalé spuštění* protokolu TCP je algoritmus protokolu TCP, který brání zahlcení sítě tím, že omezuje množství dat odesílaných po síti. Začíná s malým množstvím oken zahlcení mezi odesílatelem a přijímačem až do doby, kdy je dosaženo maxima nebo se zjistí ztráta paketů.

 Jak **Azure CDN z Akamai** a **Azure CDN z profilů Verizon** eliminují protokol TCP pomalu, zahájí následující tři kroky:

1. Monitorování stavu a šířky pásma slouží k měření šířky pásma připojení mezi servery PoP hraniční sítě.
    
2. Metriky se sdílí mezi hraničními servery PoP, aby každý server měl informace o stavech sítě a stavu serveru ostatních bodů POP kolem nich.  
    
3. Hraniční servery CDN dávají předpoklady o některých parametrech přenosu, například o tom, co má optimální velikost okna při komunikaci s dalšími hraničními servery CDN v blízkosti. Tento krok znamená, že počáteční velikost okna zahlcení se dá zvýšit, pokud je stav připojení mezi hraničními servery CDN schopný přenos dat paketů s vyššími nároky.  

#### <a name="leveraging-persistent-connections"></a>Využití trvalých připojení

Pomocí sítě CDN se ke svému původnímu serveru připojí méně jedinečných počítačů přímo v porovnání s uživateli, kteří se připojují přímo k vašemu zdroji. Azure CDN také seskupují požadavky uživatelů, aby bylo možné vytvořit méně připojení se zdrojem.

Jak už jsme uvedli, k navázání připojení TCP se vyžadují několik požadavků na handshake. Trvalá připojení, která jsou implementovaná `Keep-Alive` hlavičkou HTTP, znovu použijí existující připojení TCP pro více požadavků HTTP, aby ušetřily doby odezvy a urychlily doručení. 

**Azure CDN z Verizon** také odesílá pravidelné pakety Keep-Alive přes připojení TCP, aby se zabránilo zavření otevřeného připojení.

#### <a name="tuning-tcp-packet-parameters"></a>Vyladění parametrů paketů TCP

**Azure CDN z Akamai** vyladí parametry, které řídí připojení typu server-server, a snižuje množství dlouhodobých zpátečních cest potřebných k načtení obsahu vloženého v lokalitě pomocí následujících technik:

- Zvýšení počátečního okna zahlcení, aby bylo možné odesílat více paketů bez čekání na potvrzení.
- Snížení počátečního časového limitu opakovaného odeslání tak, aby byla zjištěna ztráta, a rychlejší přenos probíhá rychleji.
- Snížení minimálního a maximálního časového limitu opakovaného přenosu, aby se snížila doba čekání před tím, než dojde ke ztrátě paketů v přenosu.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Předběžné načtení objektu (Azure CDN jenom z Akamai)

Většina webů se skládá ze stránky HTML, která odkazuje na různé další prostředky, jako jsou obrázky a skripty. Když klient požaduje webovou stránku, prohlížeč nejprve stáhne a analyzuje objekt HTML a následně vytvoří další požadavky na propojené prostředky, které jsou vyžadovány k úplnému načtení stránky. 

*Předběžné načtení* je způsob, jak načíst obrázky a skripty vložené do stránky HTML v době, kdy se HTML odešle do prohlížeče, a předtím, než prohlížeč tyto požadavky vyžádá. 

Když je možnost předběžného načtení zapnutá v době, kdy CDN zachovává základní stránku HTML v prohlížeči klienta, CDN analyzuje soubor HTML a provede další požadavky na všechny propojené prostředky a uloží je do mezipaměti. Když klient vytvoří požadavky na propojené prostředky, hraniční Server CDN již má požadované objekty a může je obsluhovat ihned bez odezvy na počátek. Tato optimalizace přináší mezipaměť i obsah, který není možné ukládat do mezipaměti.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptivní komprese obrázků (Azure CDN pouze z Akamai)

Některá zařízení, zejména mobilní zařízení, mají za chvíli pomalejší síťové rychlosti. V těchto scénářích je výhodnější, aby uživatel přijímal menší obrázky na své webové stránce rychleji, a ne tak dlouho, dokud nebude čekat dlouhou dobu pro obrázky s úplným rozlišením.

Tato funkce automaticky monitoruje kvalitu sítě a využívá standardní metody komprese JPEG, pokud jsou rychlosti sítě pomalejší, aby se zlepšila doba doručení.

Adaptivní komprese obrázků | Přípony souborů  
--- | ---  
Komprese JPEG | . jpg,. jpeg,. jpe,. Jig,. jgig,. JGI

## <a name="caching"></a>Ukládání do mezipaměti

V případě DSA je ukládání do mezipaměti ve výchozím nastavení v CDN vypnuté, a to i v případě, že v odpovědi na původce obsahuje `Cache-Control` nebo `Expires` hlavičky. DSA se obvykle používá pro dynamické prostředky, které by neměly být ukládány do mezipaměti, protože jsou pro každého klienta jedinečné. Ukládání do mezipaměti může způsobit přerušení tohoto chování.

Pokud máte web se směsí statických a dynamických prostředků, je nejlepší využít hybridní přístup k dosažení nejlepšího výkonu. 

Pro **Azure CDN Standard od Verizon** a **Azure CDN Standard od profilů Akamai** můžete zapnout ukládání do mezipaměti pro konkrétní koncové body DSA pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md).

Přístup k pravidlům pro ukládání do mezipaměti:

1. Na stránce **profil CDN** v části nastavení vyberte **pravidla ukládání do mezipaměti** .  
    
    ![Tlačítko Pravidla ukládání do mezipaměti CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Otevře se stránka **pravidla pro ukládání do mezipaměti** .

2. Vytvořte globální nebo vlastní pravidlo ukládání do mezipaměti, abyste mohli zapnout ukládání do mezipaměti pro koncový bod DSA. 

Pro **Azure CDN Premium jenom v** profilech Verizon můžete zapnout ukládání do mezipaměti pro konkrétní koncové body DSA pomocí [modulu pravidel](./cdn-verizon-premium-rules-engine.md). Všechna vytvořená pravidla ovlivňují jenom ty koncové body vašeho profilu, které jsou optimalizované pro DSA. 

Přístup ke stroji pravidel:
    
1. Na stránce **profil CDN** vyberte **Spravovat** .  
    
    ![Tlačítko Spravovat profil CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Otevře se portál pro správu CDN.

2. Na portálu pro správu CDN vyberte **a** a pak vyberte **modul pravidel** . 

    ![Modul pravidel pro DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternativně můžete použít dva koncové body CDN: jeden koncový bod optimalizovaný s agentem DSA k dodávání dynamických prostředků a dalšího koncového bodu optimalizovaného pomocí statického typu optimalizace, jako je například obecné doručování webu, pro doručování prostředků do mezipaměti. Upravte adresy URL webových stránek tak, aby se přímo na prostředek naplánovaly na koncovém bodu CDN, který chcete použít. 

Například: `mydynamic.azureedge.net/index.html` je dynamická stránka a je načtena z koncového bodu DSA.  Stránka HTML odkazuje na několik statických prostředků, jako jsou knihovny JavaScriptu nebo obrázky načtené z statického koncového bodu CDN, například `mystatic.azureedge.net/banner.jpg` a `mystatic.azureedge.net/scripts.js` .