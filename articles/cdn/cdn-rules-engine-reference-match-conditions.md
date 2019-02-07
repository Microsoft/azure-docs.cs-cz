---
title: Podmínky shody stroje pravidel Azure CDN | Dokumentace Microsoftu
description: Referenční dokumentace pro Azure Content Delivery Network pravidla podmínky shody stroje.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 44182d686548fa5b6363a87be0ce7851829e20ab
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820552"
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN stroj pravidel splňují podmínky 
Tento článek obsahuje podrobný popis dostupných odpovídají podmínkám pro sítě Azure Content Delivery Network (CDN) [stroj pravidel](cdn-rules-engine.md).

Druhá část pravidla je podmínce shody. Podmínka shody identifikuje konkrétní typy žádostí, pro které se provede sadu funkcí.

Můžete například použít podmínku shody pro:
- Filtrovat žádosti pro obsah v konkrétních místech.
- Filtrovat žádosti vygenerované z konkrétní IP adresu nebo zemi.
- Filtrovat žádosti podle informace záhlaví.

## <a name="always-match-condition"></a>Vždy podmínce shody

Podmínka shody vždy platí výchozí sadu funkcí pro všechny požadavky.

Název | Účel
-----|--------
[Vždy](#always) | Výchozí sada funkcí se vztahuje na všechny požadavky.

## <a name="device-match-condition"></a>Podmínka shody zařízení

Podmínka shody zařízení identifikuje žádosti z mobilního zařízení na základě jeho vlastností.  

Název | Účel
-----|--------
[zařízení](#device) | Identifikuje žádosti z mobilního zařízení na základě jeho vlastností.

## <a name="location-match-conditions"></a>Podmínky shody umístění

Podmínky shody umístění identifikaci požadavků v závislosti na umístění žadatele.

Název | Účel
-----|--------
[JAKO číslo](#as-number) | Určuje požadavky, které pocházejí z určité síti.
[Země](#country) | Určuje požadavky, které pocházejí ze zadaného zemí.

## <a name="origin-match-conditions"></a>Podmínky shody původu

Podmínky shody původu identifikaci požadavků, které odkazují na úložiště Content Delivery Network nebo zákazník zdrojový server.

Název | Účel
-----|--------
[CDN Origin](#cdn-origin) | Identifikuje požadavky na obsah uložený v úložišti síť Content Delivery Network.
[Zákazníka](#customer-origin) | Identifikuje požadavky na obsah uložený na původním serveru konkrétního zákazníka.

## <a name="request-match-conditions"></a>Podmínky shody žádosti

Podmínky shody žádost o identifikaci požadavků na základě jejich vlastností.

Název | Účel
-----|--------
[IP adresa klienta](#client-ip-address) | Určuje požadavky, které pocházejí z konkrétní IP adresy.
[Parametr souboru cookie](#cookie-parameter) | Ověří soubory cookie související s každou žádostí pro zadanou hodnotu.
[Soubor cookie parametr Regex](#cookie-parameter-regex) | Ověří soubory cookie související s každou žádostí pro zadaný regulární výraz.
[Edge Cname](#edge-cname) | Identifikuje požadavky, které odkazují na konkrétní edge CNAME.
[Odkaz domény](#referring-domain) | Určuje požadavky, které byly postoupeny ze zadaného hostitele.
[Literál hlavičky požadavku](#request-header-literal) | Určuje požadavky, které obsahují zadanou hlavičku nastavit na zadanou hodnotu.
[Regulární výraz hlavičky požadavku](#request-header-regex) | Určuje požadavky, které obsahují zadanou hlavičku nastavena na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Zástupný znak hlavičky požadavku](#request-header-wildcard) | Určuje požadavky, které obsahují zadanou hlavičku nastavena na hodnotu, která odpovídá zadanému vzoru.
[Request – metoda](#request-method) | Identifikuje požadavky podle jejich metody HTTP.
[Schéma požadavku](#request-scheme) | Identifikuje požadavky podle jejich protokolu HTTP.

## <a name="url-match-conditions"></a>Podmínky shody adresy URL

Podmínky shody URL identifikaci požadavků podle jejich adresy URL.

Název | Účel
-----|--------
[Adresář cesty URL](#url-path-directory) | Identifikuje požadavky podle jejich relativní cesty.
[Rozšíření cesty adresy URL](#url-path-extension) | Identifikuje požadavky podle jejich přípony názvu souboru.
[Adresa URL cesta název_souboru](#url-path-filename) | Identifikuje požadavky podle názvu souboru.
[Literál cestu adresy URL](#url-path-literal) | Porovná relativní cestu požadavku se zadanou hodnotou.
[Regulární výraz cesty adresy URL](#url-path-regex) | Porovná požadavek na relativní cestu k zadanému regulárnímu výrazu.
[Cesta adresy URL zástupný znak](#url-path-wildcard) | Porovná požadavek na relativní cestu k zadanému vzoru.
[Adresa URL dotazu literál](#url-query-literal) | Porovná řetězec dotazu požadavku se zadanou hodnotou.
[Parametr dotazu adresy URL](#url-query-parameter) | Určuje požadavky, které obsahují zadaný dotaz řetězec parametrem nastaveným na hodnotu, která odpovídá zadanému vzoru.
[Regulární výraz dotazu adresy URL](#url-query-regex) | Určuje požadavky, které obsahují zadaný dotaz řetězec parametrem nastaveným na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Adresa URL dotazu zástupných znaků](#url-query-wildcard) | Porovná zadané hodnoty s řetězci dotazu požadavku.


## <a name="reference-for-rules-engine-match-conditions"></a>Referenční informace pro podmínky shody stroje pravidel

---
### <a name="always"></a>Vždy

Podmínka shody vždy platí výchozí sadu funkcí pro všechny požadavky.

[Zpět na začátek](#main)

</br>

---
### <a name="as-number"></a>JAKO číslo 
Číslo sítě je definován pomocí čísla autonomního systému (ASN). 

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých číslo AS odpovídají podmínce:
- **Odpovídá**: Vyžaduje se, že číslo ASN síť klienta odpovídá jednomu z zadaného čísla ASN. 
- **Neodpovídá**: Vyžaduje se, že číslo ASN síť klienta neodpovídá žádnému zadaného čísla ASN.

Informace o klíči:
- Zadejte čísla ASN více omezující každé z nich mezerou. Například 64514 64515 odpovídá požadavků, které přicházejí z 64514 nebo 64515.
- Některé žádosti nemusí vrátit platný číslo ASN. Otazník (?) bude odpovídat požadavků, u kterých nebylo možné určit platné číslo ASN.
- Zadejte celé číslo ASN pro požadovanou síť. Částečné hodnoty nebudou odpovídat.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="cdn-origin"></a>CDN Origin
Pokud jsou splněny obě následující podmínky, je splněna podmínka shody původu CDN:
- Obsah z CDN úložiště byla vyžádána.
- Identifikátor URI požadavku na základě typu obsahu přístupový bod (například /000001), který je definován v tomto stavu shody:
  - ADRESA URL PRO CDN: Identifikátor URI požadavku musí obsahovat vybraného obsahu přístupový bod.
  - Edge CNAME URL: Odpovídající konfigurace CNAME okraj musí odkazovat na vybrané obsahu přístupový bod.
  
Informace o klíči:
 - Bod přístup k obsahu se identifikuje služba, která by měla sloužit požadovaný obsah.
 - Nepoužívejte příkaz IF a kombinovat určité podmínky shody. Kombinování podmínku CDN Origin shody s podmínku shody zákazníka byste například vytvořit vzor pro shodu, která by mohla nikdy existovat shoda. Z tohoto důvodu nelze kombinovat dvě podmínky shody původu CDN prostřednictvím příkazu a v případě.

[Zpět na začátek](#main)

</br>

---
### <a name="client-ip-address"></a>IP adresa klienta
**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých IP adresa klienta odpovídají podmínce:
- **Odpovídá**: Vyžaduje se, že IP adresa klienta odpovídá některé ze zadaných IP adres. 
- **Neodpovídá**: Vyžaduje, aby IP adresa klienta neodpovídá žádnému ze zadaných IP adres. 

Informace o klíči:
- Pomocí zápisu CIDR.
- Zadejte několik IP adres nebo bloky IP adres omezující každé z nich mezerou. Příklad:
  - **Příklad IPv4**: 1.2.3.4 10.20.30.40 odpovídá všechny žádosti, které přicházejí z adresa 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 odpovídá všechny žádosti, které přicházejí z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe pro blok IP adres je základní adresa následována lomítkem a velikost předpony. Příklad:
  - **Příklad IPv4**: 5.5.5.64/26 odpovídá všechny žádosti, které přicházejí z adresy 5.5.5.64 prostřednictvím 5.5.5.127.
  - **Příklad IPv6**: 1:2:3: / 48 odpovídá všechny žádosti, které přicházejí z adresy 1:2:3:0:0:0:0:0 prostřednictvím 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="cookie-parameter"></a>Parametr souboru cookie
**Shody**/**neodpovídá** možnost určuje podmínky, za kterých parametr souboru Cookie odpovídat podmínka je splněna.
- **Odpovídá**: Vyžaduje žádost obsahující zadaný soubor cookie s hodnotou, která odpovídá alespoň jedné z hodnot, které jsou definovány v tomto stavu shody.
- **Neodpovídá**: Vyžaduje se, že splňuje požadavek na některý z následujících kritérií:
  - Neobsahuje zadaný soubor cookie.
  - Obsahuje zadaného souboru cookie, ale její hodnota neodpovídá žádnému z hodnot, které jsou definovány v tomto stavu shody.
  
Informace o klíči:
- Název souboru cookie: 
  - Hodnoty zástupných znaků, včetně hvězdičky (*), nejsou podporovány, pokud zadáváte název souboru cookie, jsou způsobilé pro porovnání pouze název odpovídá přesně souboru cookie.
  - Na jednu instanci této podmínce shody lze zadat pouze název jednoho souboru cookie.
  - Soubor cookie název porovnávání rozlišuje velká a malá písmena.
- Hodnota souboru cookie: 
  - Určete více hodnoty souboru cookie omezující každé z nich mezerou.
  - Hodnota souboru cookie můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values). 
  - Pokud nebyla zadána hodnota zástupného znaku, bude pouze v případě přesné shody splňují tuto podmínku shody. Například zadání "Value" bude odpovídat "Value", ale ne "Hodnota1" nebo "Hodnota2".
  - Použití **ignorování případu** možnost řízení, zda malá a velká písmena porovnání je provedeno proti hodnotě souboru cookie požadavku.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="cookie-parameter-regex"></a>Soubor cookie parametr Regex
Podmínka shody regulárního výrazu parametru souborů Cookie definuje název souboru cookie a hodnotu. Můžete použít [regulární výrazy](cdn-rules-engine-reference.md#regular-expressions) definovat hodnotu požadovaného souboru cookie. 

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých regulárního výrazu parametru souboru Cookie odpovídají podmínce.
- **Odpovídá**: Vyžaduje žádost obsahující zadaný soubor cookie s hodnotou, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá**: Vyžaduje se, že splňuje požadavek na některý z následujících kritérií:
  - Neobsahuje zadaný soubor cookie.
  - Obsahuje zadaného souboru cookie, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.
  
Informace o klíči:
- Název souboru cookie: 
  - Protože regulární výrazy a zástupné hodnoty, včetně hvězdičky (*), nejsou podporovány, pokud zadáváte název souboru cookie, jsou způsobilé pro porovnání pouze název odpovídá přesně souboru cookie.
  - Na jednu instanci této podmínce shody lze zadat pouze název jednoho souboru cookie.
  - Soubor cookie název porovnávání rozlišuje velká a malá písmena.
- Hodnota souboru cookie: 
  - Hodnota souboru cookie můžete využít regulárních výrazů.
  - Použití **ignorování případu** možnost řízení, zda malá a velká písmena porovnání je provedeno proti hodnotě souboru cookie požadavku.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

--- 
### <a name="country"></a>Země
Můžete zadat prostřednictvím jeho směrové číslo země. 

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých země odpovídají podmínce:
- **Odpovídá**: Vyžaduje požadavku tak, aby obsahovala kód hodnoty zadanou zemi. 
- **Neodpovídá**: Vyžaduje se, že žádost neobsahuje hodnoty kód země zadaná.

Informace o klíči:
- Zadejte více kódy zemí omezující každé z nich mezerou.
- Zástupné znaky nejsou podporovány, pokud zadáváte kód země.
- Kódy zemí "EU" a "Asie a Tichomoří" není zahrnovat všechny IP adresy v těchto oblastech.
- Některé žádosti nemusí vrátit platný kód země. Otazník (?) bude odpovídat požadavků, u kterých nebylo možné určit platný kód země.
- Kódy zemí rozlišují malá a velká písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementace filtrování podle země s použitím stroje pravidel
Tato podmínka shody umožňuje provádět velké množství vlastního nastavení v závislosti na umístění, ze které žádost pochází. Například je možné replikovat chování funkce filtrování podle země prostřednictvím následující konfiguraci:

- Cesta adresy URL zástupný znak shody: Nastavte [cesta URL zástupný znak odpovídají podmínce](#url-path-wildcard) k adresáři, který bude zabezpečené. 
    Připojte hvězdičku na konec relativní cesta k zajištění, že budou mít přístup ke všem jeho podřízených prvků omezené tímto pravidlem.

- Shoda země: Nastavení země podmínky shody k požadované sadě zemích.
   - Povolte: Podmínku shody země na **neodpovídá** povolit pouze zadanou zemí přístup k obsahu uloženému v umístění definovaném podmínce shody cesta URL zástupný znak.
   - Blok: Podmínku shody země na **shody** blokování zadanou zemí v přístupu k obsahu uloženému v umístění definovaném podmínce shody cesta URL zástupný znak.

- Odepřít přístup (403) funkce: Povolit [funkce odepření přístupu (403)](cdn-rules-engine-reference-features.md#deny-access-403) k replikaci povolit nebo blokovat část funkce filtrování podle země.

[Zpět na začátek](#main)

</br>

---
### <a name="customer-origin"></a>Zákazníka

Informace o klíči: 
- Bez ohledu na to, zda žádost prostřednictvím adresy URL CDN nebo okraj. adresa URL CNAME, který odkazuje na původní název vybraného zákazníka, je splněna podmínka shody zákazníka.
- Konfigurace původu zákazníků, na který odkazuje pravidlo nelze odstranit ze stránky zákazníka. Než se pokusíte odstranit konfiguraci původu zákazníka, ujistěte se, že následující konfigurace není na něj odkazovat:
  - Podmínku shody zákazníka
  - Konfigurace služby edge CNAME
- Nepoužívejte příkaz IF a kombinovat určité podmínky shody. Kombinování podmínku shody zákazníka s podmínku shody původu CDN byste například vytvořit vzor pro shodu, která by mohla nikdy existovat shoda. Z tohoto důvodu nelze kombinovat dvě podmínky shody zákazníka prostřednictvím příkazu a v případě.

[Zpět na začátek](#main)

</br>

---
### <a name="device"></a>Zařízení

Podmínka shody zařízení identifikuje žádosti z mobilního zařízení na základě jeho vlastností. Rozpoznávání mobilních zařízení můžete dosáhnout [WURFL](http://wurfl.sourceforge.net/). 

**Shody**/**neodpovídá** možnost určuje podmínky, za kterých zařízení odpovídat podmínka je splněna:
- **Odpovídá**: Vyžaduje, aby zařízení žadatele odpovídají zadané hodnotě. 
- **Neodpovídá**: Vyžaduje, aby žadatele zařízení neodpovídá zadanou hodnotu.

Informace o klíči:

- Použití **ignorování případu** možnost určit, zda zadaná hodnota je velká a malá písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

#### <a name="string-type"></a>String – typ
Funkce WURFL obvykle přijímá libovolnou kombinaci čísla, písmena a symboly. Z důvodu flexibilní povaze tuto funkci musíte zvolit způsob interpretace hodnotu přidruženou k tomuto stavu shody. Následující tabulka popisuje dostupné sady možností:

Type     | Popis
---------|------------
literál  | Vyberte tuto možnost, chcete-li zabránit většině znaků na zvláštní význam pomocí jejich [literálovou hodnotou](cdn-rules-engine-reference.md#literal-values).
Zástupný znak | Vyberte tuto možnost, chcete-li využít výhod všech [zástupné znaky] ([zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
Regex    | Vyberte tuto možnost používat [regulární výrazy](cdn-rules-engine-reference.md#regular-expressions). Regulární výrazy jsou užitečné pro definování vzor znaků.

#### <a name="wurfl-capabilities"></a>Možnosti WURFL
Funkce WURFL odkazuje na kategorii, která popisuje mobilní zařízení. Vybrané funkce určuje typ mobilního zařízení popis, který se používá k identifikaci požadavků.

Následující tabulka uvádí možnosti WURFL a jejich proměnné pro stroj pravidel.
<br>
> [!NOTE] 
> V jsou podporovány následující proměnné **upravit hlavička požadavku klienta** a **upravit hlavičku odpovědi klienta** funkce.

Schopnost | Proměnná | Popis | Ukázkové hodnoty
-----------|----------|-------------|----------------
Název značky | %{wurfl_cap_brand_name} | Řetězec určující název značky zařízení. | Samsung
Operační systém zařízení | %{wurfl_cap_device_os} | Řetězec, který označuje operační systém nainstalovaný na zařízení. | iOS
Verze operačního systému zařízení | %{wurfl_cap_device_os_version} | Řetězec, který označuje číslo verze operačního systému na zařízení nainstalovaná. | 1.0.1
Duální orientace | %{wurfl_cap_dual_orientation} | Logická hodnota, která určuje, jestli zařízení podporuje duální orientace. | true (pravda)
HTML upřednostňované specifikace DTD | %{wurfl_cap_html_preferred_dtd} | Řetězec, který označuje mobilních zařízení upřednostňované dokumentu typ definice (DTD) pro obsah ve formátu HTML. | žádný<br/>xhtml_basic<br/>html5
Vkládání obrázků | %{wurfl_cap_image_inlining} | Logická hodnota, která určuje, zda zařízení podporuje Base64 kódovaný bitové kopie. | false (nepravda)
Is Android | %{wurfl_vcap_is_android} | Logická hodnota, která označuje, zda zařízení používá operační systém Android. | true (pravda)
Je s IOSEM | %{wurfl_vcap_is_ios} | Logická hodnota, která označuje, zda zařízení používá iOS. | false (nepravda)
Is Smart TV | %{wurfl_cap_is_smarttv} | Logická hodnota, která určuje, jestli zařízení je inteligentní TV. | false (nepravda)
Je chytrém telefonu | %{wurfl_vcap_is_smartphone} | Logická hodnota určující, zda je zařízení smartphonu. | true (pravda)
Je Tablet | %{wurfl_cap_is_tablet} | Logická hodnota určující, zda je zařízení tabletu. Tento popis je nezávislé na operační systém. | true (pravda)
Je bezdrátových zařízení | %{wurfl_cap_is_wireless_device} | Logická hodnota, která určuje, jestli se zařízení považuje za bezdrátových zařízení. | true (pravda)
Název marketing | %{wurfl_cap_marketing_name} | Řetězec, který označuje marketingové název zařízení. | BlackBerry 8100 Pearl
Mobilní prohlížeče | %{wurfl_cap_mobile_browser} | Řetězec, který označuje prohlížeč, který slouží k vyžádání obsahu ze zařízení. | Chrome
Verze mobilního prohlížeče | %{wurfl_cap_mobile_browser_version} | Řetězec označující verzi prohlížeče, který slouží k vyžádání obsahu ze zařízení. | 31
Název modelu | %{wurfl_cap_model_name} | Řetězec určující název modelu zařízení. | s3
Progresivní stahování | %{wurfl_cap_progressive_download} | Logická hodnota, která určuje, jestli zařízení podporuje přehrávání zvuku a videa, zatímco se stále stahují. | true (pravda)
Datum vydání | %{wurfl_cap_release_date} | Řetězec, který označuje roku a měsíce přidávání zařízení WURFL databáze.<br/><br/>Formát: `yyyy_mm` | 2013_december
Výška řešení | %{wurfl_cap_resolution_height} | Celé číslo, které označuje, zařízení výšku v pixelech. | 768
Šířka řešení | %{wurfl_cap_resolution_width} | Celé číslo, které označuje, zařízení šířka v pixelech. | 1024

[Zpět na začátek](#main)

</br>

---
### <a name="edge-cname"></a>Edge Cname
Informace o klíči: 
- Seznam dostupných edge záznamů CNAME je omezen na tyto záznamy CNAME edge, které jsou nakonfigurované na stránce záznamy CNAME Edge pro platformu, na kterém je nakonfigurovaná stroj pravidel.
- Než se pokusíte odstranit konfiguraci CNAME služby edge, ujistěte se, že podmínku shody Edge Cname se neodkazuje. Konfigurace CNAME Edge, které byly definovány v pravidle nelze odstranit ze stránky Edge záznamů CNAME. 
- Nepoužívejte příkaz IF a kombinovat určité podmínky shody. Kombinování Edge Cname shoda podmínku, zákazníka shoda by například vytvořit vzor pro shodu, která by mohla nikdy existovat shoda. Z tohoto důvodu nelze kombinovat dvě podmínky shody Edge Cname prostřednictvím příkazu a v případě.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="referring-domain"></a>Odkaz domény
Název hostitele přidružený referrer, přes který byl vyžádán obsah Určuje, zda je splněna podmínka odkazující domény. 

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých odkazující domény odpovídají podmínce:
- **Odpovídá**: Vyžaduje odkazující název hostitele tak, aby odpovídaly zadanými hodnotami. 
- **Neodpovídá**: Vyžaduje, aby odkazující název hostitele se neshoduje s zadanou hodnotu.

Informace o klíči:
- Zadejte více názvů hostitelů podle omezující každé z nich mezerou.
- Podporuje tato podmínka shody [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
- Pokud zadaná hodnota neobsahuje hvězdičky, musí být přesná shoda pro název hostitele odkazující server. Například zadání "domena.cz" shodě "www.mydomain.com."
- Použití **ignorování případu** možnost řízení, zda je provedeno porovnání velká a malá písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---  
### <a name="request-header-literal"></a>Literál hlavičky požadavku
**Shody**/**neodpovídá** možnost určuje podmínky, za kterých žádost o záhlaví literálu odpovídat podmínka je splněna.
- **Odpovídá**: Vyžaduje žádost obsahující zadanou hlavičku. Její hodnota musí odpovídat objektu, který je definován v tomto stavu shody.
- **Neodpovídá**: Vyžaduje se, že splňuje požadavek na některý z následujících kritérií:
  - Zadaná hlavička neobsahuje.
  - Obsahuje zadanou hlavičku, ale její hodnota není shodný s identifikátorem, který je definován v tomto stavu shody.
  
Informace o klíči:
- Porovnání názvů záhlaví jsou vždy velká a malá písmena. Použití **ignorování případu** možnost řízení rozlišování porovnání hodnot hlavičky.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---  
### <a name="request-header-regex"></a>Regulární výraz hlavičky požadavku
**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých regulární výraz záhlaví požadavku odpovídají podmínce.
- **Odpovídá**: Vyžaduje žádost obsahující zadanou hlavičku. Jeho hodnota musí odpovídat vzoru, který je definován v zadaném [regulárního výrazu](cdn-rules-engine-reference.md#regular-expressions).
- **Neodpovídá**: Vyžaduje se, že splňuje požadavek na některý z následujících kritérií:
  - Zadaná hlavička neobsahuje.
  - Obsahuje zadanou hlavičku, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:
- Název hlavičky: 
  - Záhlaví název porovnávání rozlišuje velká a malá písmena.
  - Nahraďte mezerami v názvu záhlaví "% 20." 
- Hodnota hlavičky: 
  - Hodnota hlavičky můžete využít regulárních výrazů.
  - Použití **ignorování případu** možnost řízení rozlišování porovnání hodnot hlavičky.
  - Pouze v případě, že alespoň jednomu ze vzorů zadaná hodnota hlavičky přesně odpovídá je splněna podmínka shody.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max 

[Zpět na začátek](#main)

</br>

---
### <a name="request-header-wildcard"></a>Zástupný znak hlavičky požadavku
**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých zástupný znak záhlaví požadavku odpovídají podmínce.
- **Odpovídá**: Vyžaduje žádost obsahující zadanou hlavičku. Jeho hodnota musí odpovídat aspoň jedna z hodnot, které jsou definovány v tomto stavu shody.
- **Neodpovídá**: Vyžaduje se, že splňuje požadavek na některý z následujících kritérií:
  - Zadaná hlavička neobsahuje.
  - Obsahuje zadanou hlavičku, ale jeho hodnota se neshoduje s některou ze zadaných hodnot.
  
Informace o klíči:
- Název hlavičky: 
  - Záhlaví název porovnávání rozlišuje velká a malá písmena.
  - Mezerami v názvu záhlaví by měla být nahrazena "% 20." Tato hodnota také můžete určit mezery v hodnotu hlavičky.
- Hodnota hlavičky: 
  - Hodnota hlavičky můžou těžit z výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
  - Použití **ignorování případu** možnost řízení rozlišování porovnání hodnot hlavičky.
  - Pokud hodnota hlavičky k aspoň jednomu ze vzorů zadaný přesně odpovídá, je splněna podmínka tuto shodu.
  - Zadejte více hodnot ve omezující každé z nich mezerou.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="request-method"></a>Request – metoda
Pouze v případě, že prostředky jsou požadovány prostřednictvím metody vybrané žádosti je splněna podmínka shody metoda žádosti. K dispozici žádosti metody jsou následující:
- GET
- HLAVNÍ 
- POST 
- MOŽNOSTI 
- PUT 
- DELETE 
- TRACE 
- PŘIPOJENÍ 

Informace o klíči:
- Ve výchozím nastavení můžete vygenerovat jenom metody GET žádosti o obsah uložený v mezipaměti v síti. Všechny ostatní metody požadavku jsou směrovány přes proxy server prostřednictvím sítě.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="request-scheme"></a>Schéma požadavku
Pouze v případě, že prostředky jsou požadovány prostřednictvím vybraný protokol je splněna podmínka shody schéma požadavku. K dispozici protokoly jsou: 
- HTTP
- HTTPS

Informace o klíči:
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="url-path-directory"></a>Adresář cesty URL
Určuje požadavek pomocí relativní cesty, které vylučují název souboru požadovaného assetu.

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých adresář cesty URL odpovídají podmínce.
- **Odpovídá**: Vyžaduje požadavek obsahovat relativní cestu adresy URL, s výjimkou názvu souboru, který odpovídá zadanému vzoru adresy URL.
- **Neodpovídá**: Vyžaduje požadavek obsahovat relativní cestu adresy URL, s výjimkou názvu souboru, který neodpovídá zadanému vzoru adresy URL.

Informace o klíči:
- Použití **vzhledem ke** možnost určit, zda adresa URL porovnání spustí před nebo po obsahu přístupový bod. Přístup k obsahu bod je část cesty, který se zobrazí mezi název hostitele Verizon CDN a relativní cesta k požadované assetu (například /800001/CustomerOrigin). Určuje umístění typu serveru (například původu CDN nebo zákazníka) a číslo účtu zákazníka.

   Jsou k dispozici pro následující hodnoty **vzhledem ke** možnost:
   - **Kořenové**: Označuje, že začne porovnání bodu adresy URL přímo za název hostitele CDN. 

     Příklad: http:\//wpc.0001.&lt; domény&gt;/**800001/myorigin/Moje_složka**/index.htm

   - **Počátek**: Označuje, že porovnání bodu adresy URL začne za obsahu přístupový bod (například myorigin /000001 nebo/800001 /). Protože \*. azureedge.net CNAME je relativní vzhledem k adresáři origin na název hostitele Verizon CDN ve výchozím nastavení vytvořen, Azure CDN uživatelé by měli použít **původu** hodnotu. 

     Například: https:\//&lt;koncový bod&gt;.azureedge.net/**Moje_složka**/index.htm 

     Tato adresa URL odkazuje na následující název hostitele Verizon CDN: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/**Moje_složka**/index.htm

- Okraj CNAME adresa URL je přepsán na adresu URL CDN před porovnání adresy URL.

    Například obě následující adresy URL přejděte na stejný prostředek a proto mají stejnou cestu URL.
    - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
    - Adresa URL CNAME Edge: http:\//&lt;koncový bod&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    - Vlastní doménu: https:\//my.domain.com/path/asset.htm
    
    - Cesta adresy URL (relativní vůči kořenovému adresáři): / 800001/CustomerOrigin/path /
    
    - Cesta adresy URL (relativní k původu): /path/

- Část adresy URL, který se používá pro porovnání končí URL těsně před název souboru požadovaného assetu. Koncové lomítko je posledním znakem v tento druh cesty.
    
- Nahraďte všechny mezery v vzor cesty URL ": % 20."
    
- Každý vzor cesty adresy URL může obsahovat jeden nebo více hvězdičky (*), kde každý hvězdička odpovídá posloupnosti jeden nebo více znaků.
    
- Určete víc cest URL ve vzoru tak omezující každé z nich mezerou.

    Příklad: * /sales/ * /marketing/

- Specifikaci cesty adresy URL můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).

- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.

[Zpět na začátek](#main)

</br>

---
### <a name="url-path-extension"></a>Rozšíření cesty adresy URL
Identifikuje požadavky podle přípony souboru požadovaného assetu.

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých rozšíření cesty adresy URL odpovídají podmínce.
- **Odpovídá**: Vyžaduje adresu URL požadavku obsahuje příponu souboru, který přesně odpovídá zadanému vzoru.

   Například pokud chcete zadat "htm", "htm" prostředky budou odpovídat, ale ne "html" prostředky.  

- **Neodpovídá**: Vyžaduje adresu URL požadavku obsahuje příponu souboru, který neodpovídá zadanému vzoru.

Informace o klíči:
- Zadejte přípony souborů tak, aby odpovídaly v **hodnotu** pole. Nezahrnujte první pozici tečku; například místo .htm použijte htm.

- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.

- Zadejte více přípon souborů omezující každé rozšíření mezerou. 

    Příklad: htm, html

- Například zadání "htm" odpovídá "htm" prostředky, ale ne "html" prostředky.


#### <a name="sample-scenario"></a>Ukázkový scénář

Následující ukázkové konfiguraci předpokládá, že pokud požadavek odpovídá jednomu z zadané rozšíření je splněna podmínka tuto shodu.

Hodnota specifikace: asp aspx php html

Pokud se najde adresy URL, které končí s těmito příponami splnění této podmínky shody:
- .asp
- .aspx
- .php
- .html

[Zpět na začátek](#main)

</br>

---
### <a name="url-path-filename"></a>Adresa URL cesta název_souboru
Identifikuje požadavky podle názvu souboru požadovaného assetu. Pro účely této podmínce shody názvu souboru se skládá z názvu požadované asset, tečku a přípona souboru (například index.html).

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých cesta název_souboru URL odpovídají podmínce.
- **Odpovídá**: Vyžaduje požadavek obsahovat název souboru v cestě adresy URL, která odpovídá zadanému vzoru.
- **Neodpovídá**: Vyžaduje požadavek obsahovat název souboru v cestě adresy URL, která neodpovídá zadanému vzoru.

Informace o klíči:
- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.

- Pokud chcete zadat více přípon souborů, oddělte každé rozšíření jedna mezera.

    Příklad: index.htm index.html

- Nahraďte prostory hodnota názvu souboru "% 20."
    
- Hodnota názvu souboru můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values). Například každý vzor názvů souborů se může skládat z jednoho nebo více hvězdičky (*), kde každý hvězdička odpovídá posloupnosti jeden nebo více znaků.
    
- Pokud nejsou zadané zástupné znaky, bude splňovat pouze v případě přesné shody této podmínce shody.

    Například zadání "presentation.ppt" odpovídá prostředek s názvem "presentation.ppt", ale ne jeden s názvem "prezentace.pptx."

[Zpět na začátek](#main)

</br>

---
### <a name="url-path-literal"></a>Literál cestu adresy URL
Porovná cesta URL požadavku, včetně názvu souboru, se zadanou hodnotou.

**Shody**/**neodpovídá** možnost určuje podmínky, za kterých literálu cesta URL odpovídají podmínka je splněna.
- **Odpovídá**: Vyžaduje požadavek obsahovat cestu adresy URL, která odpovídá zadanému vzoru.
- **Neodpovídá**: Vyžaduje požadavek obsahovat cestu adresy URL, která neodpovídá zadanému vzoru.

Informace o klíči:
- Použití **vzhledem ke** možnost určit, zda bod porovnání adresa URL začíná před nebo po obsahu přístupový bod. 

    Jsou k dispozici pro následující hodnoty **vzhledem ke** možnost:
     - **Kořenové**: Označuje, že začne porovnání bodu adresy URL přímo za název hostitele CDN.

       Příklad: http:\//wpc.0001.&lt; domény&gt;/**800001/myorigin/myfolder/index.htm**

     - **Počátek**: Označuje, že porovnání bodu adresy URL začne za obsahu přístupový bod (například myorigin /000001 nebo/800001 /). Protože \*. azureedge.net CNAME je relativní vzhledem k adresáři origin na název hostitele Verizon CDN ve výchozím nastavení vytvořen, Azure CDN uživatelé by měli použít **původu** hodnotu. 

       Například: https:\//&lt;koncový bod&gt;.azureedge.net/**myfolder/index.htm**

     Tato adresa URL odkazuje na následující název hostitele Verizon CDN: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/**myfolder/index.htm**

- Okraj CNAME adresa URL je přepsán na adresu URL CDN před porovnání adresy URL.

    Například obě následující adresy URL přejděte na stejný prostředek a proto mají stejnou cestu URL:
    - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    - Adresa URL CNAME Edge: http:\//&lt;koncový bod&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    
    - Cesta adresy URL (relativní vůči kořenovému adresáři): /800001/CustomerOrigin/path/asset.htm
   
    - Cesta adresy URL (relativní k původu): /path/asset.htm

- Řetězce dotazu v adrese URL se ignorují.
- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.
- Hodnota zadaná pro tuto podmínku shody se porovná relativní cesta přesné žádost klienta.

- Vyhledat všechny požadavky vytvořené pro konkrétní adresář, použijte [adresář cesty URL](#url-path-directory) nebo [cesta URL zástupný znak](#url-path-wildcard) odpovídají podmínce.

[Zpět na začátek](#main)

</br>

---
### <a name="url-path-regex"></a>Regulární výraz cesty adresy URL
Porovná cesta URL požadavku na zadaný [regulárního výrazu](cdn-rules-engine-reference.md#regular-expressions).

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých regulární výraz cesty adresy URL odpovídají podmínce.
- **Odpovídá**: Vyžaduje požadavek obsahovat cestu adresy URL, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá**: Vyžaduje požadavek obsahovat cestu adresy URL, která neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:
- Okraj CNAME adresa URL je přepsán na adresu URL CDN před porovnání adresy URL. 
 
    Například obě adresy URL přejděte na stejný prostředek a proto mají stejnou cestu URL.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm
    
    Další informace:
    
     - Cesta adresy URL: /800001/CustomerOrigin/path/asset.htm

- Řetězce dotazu v adrese URL se ignorují.
    
- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.
    
- Mezery v cestě adresy URL by měla být nahrazena "% 20."

[Zpět na začátek](#main)

</br>

---
### <a name="url-path-wildcard"></a>Cesta adresy URL zástupný znak
Porovná relativní cesta URL požadavku na zadané zástupných.

**Shody**/**neodpovídá** možnost určuje podmínky, za kterých cesta URL zástupný znak odpovídá podmínka je splněna.
- **Odpovídá**: Požadavek obsahuje cestu adresy URL, která odpovídá zadaným zástupných vyžaduje.
- **Neodpovídá**: Požadavek obsahuje cestu adresy URL, který neodpovídá zadané zástupných vyžaduje.

Informace o klíči:
- **Vzhledem k** možnost: Tato možnost určuje, zda bod adresy URL porovnání zahájí před nebo po obsahu přístupový bod.

   Tato možnost může mít následující hodnoty:
     - **Kořenové**: Označuje, že začne porovnání bodu adresy URL přímo za název hostitele CDN.

       Příklad: http:\//wpc.0001.&lt; domény&gt;/**800001/myorigin/myfolder/index.htm**

     - **Počátek**: Označuje, že porovnání bodu adresy URL začne za obsahu přístupový bod (například myorigin /000001 nebo/800001 /). Protože \*. azureedge.net CNAME je relativní vzhledem k adresáři origin na název hostitele Verizon CDN ve výchozím nastavení vytvořen, Azure CDN uživatelé by měli použít **původu** hodnotu. 

       Například: https:\//&lt;koncový bod&gt;.azureedge.net/**myfolder/index.htm**

     Tato adresa URL odkazuje na následující název hostitele Verizon CDN: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/**myfolder/index.htm**

- Okraj CNAME adresa URL je přepsán na adresu URL CDN před porovnání adresy URL.

    Například obě následující adresy URL přejděte na stejný prostředek a proto mají stejnou cestu URL:
     - Adresa URL pro CDN: http://wpc.0001.&lt; domény&gt;/800001/CustomerOrigin/path/asset.htm
     - Adresa URL CNAME Edge: http:\//&lt;koncový bod&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    
     - Cesta adresy URL (relativní vůči kořenovému adresáři): /800001/CustomerOrigin/path/asset.htm
    
     - Cesta adresy URL (relativní k původu): /path/asset.htm
    
- Určete víc cest URL omezující každé z nich mezerou.

   Příklad: /marketing/asset.* /sales/*.htm

- Řetězce dotazu v adrese URL se ignorují.
    
- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.
    
- Nahraďte mezery v cestě adresy URL ": % 20."
    
- Hodnota zadaná pro cestu adresy URL můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values). Každý vzor cesty adresy URL může obsahovat jeden nebo více hvězdičky (*), kde každý hvězdička odpovídá sekvenci jeden nebo více znaků.

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Ukázková konfigurace v následující tabulce se předpokládá, že pokud požadavek odpovídá zadanému vzoru adresy URL je splněna podmínka tuto shodu:

Hodnota                   | Relativní vzhledem k    | Výsledek 
------------------------|----------------|-------
*/test.html */test.php  | Kořenový adresář nebo původu | Tento model je nalezena shoda s požadavky na prostředky s názvem "test.html" nebo "test.php" v jakékoli složce.
/80ABCD/origin/text/*   | Hlavní stránka           | Tento model je nalezena shoda, pokud požadovaný prostředek splňuje následující kritéria: <br />– Musí být umístěny na zákazníka původu názvem "origin". <br />– Relativní cesta musí začínat znakem složku s názvem "text". To znamená, že požadovaný prostředek se může nacházet ve složce "text" nebo jeden z jejích podsložek rekurzivní.
*/css/* */js/*          | Kořenový adresář nebo původu | Tento vzor odpovídá všem CDN nebo Microsoft edge CNAME adresy URL, které obsahují složky šablony stylů css a js.
*.jpg *.gif *.png       | Kořenový adresář nebo původu | Tento vzor odpovídá všem CDN nebo Microsoft edge CNAME adresy URL končící JPG, GIF nebo PNG. Alternativní způsob určení tento model je [rozšíření cesty adresy URL odpovídají podmínce](#url-path-extension).
/ imagí / * / media / *      | Zdroj         | Tento vzor odpovídá CDN nebo Microsoft edge CNAME adresy URL, jehož relativní cesta začínající znakem "Image" nebo "média" složky. <br />– CDN adresa URL: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/images/sales/event1.png<br />-Ukázkový edge CNAME adresy URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Zpět na začátek](#main)

</br>

---
### <a name="url-query-literal"></a>Adresa URL dotazu literál
Porovná řetězec dotazu požadavku se zadanou hodnotou.

**Shody**/**neodpovídá** možnost určuje podmínky, za kterých literál dotazu adresy URL odpovídají podmínka je splněna.
- **Odpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá zadaný řetězec dotazu.
- **Neodpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá řetězci zadanému dotazu.

Informace o klíči:

- Pouze odpovídá řetězci přesný dotaz splňují tuto podmínku shody.
    
- Použití **ignorování případu** možnost řízení rozlišování porovnávání řetězců dotazu.
    
- V textu hodnotu řetězce dotazu neobsahují nejlepší otazník (?).
    
- Některé znaky vyžadují kódování URL. Použijte symbol procenta na adresu URL kódování následující znaky:

   Znak | Kódování URL
   ----------|---------
   Kosmické aktivity     | %20
   &         | %25

- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
   - Výplň kompletní mezipaměti
   - Interní výchozí Max-Age
   - Platnost vnitřní Max-Age
   - Ignorovat původu No-Cache
   - Interní Nezastaralá Max

[Zpět na začátek](#main)

</br>

---
### <a name="url-query-parameter"></a>Parametr dotazu adresy URL
Určuje požadavky, které obsahují parametr řetězce dotazu zadaná. Tento parametr je nastaven na hodnotu, která odpovídá zadanému vzoru. Parametry řetězce dotazu (například parametr = hodnota) v žádosti o adresy URL určete, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametru řetězce dotazu podle názvu a přijímá jednu nebo více hodnot pro hodnotu parametru. 

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých parametru dotazu URL odpovídají podmínce.
- **Odpovídá**: Vyžaduje žádost obsahující zadaný parametr s hodnotou, která odpovídá alespoň jedné z hodnot, které jsou definovány v tomto stavu shody.
- **Neodpovídá**: Vyžaduje se, že splňuje požadavek na některý z následujících kritérií:
  - Zadaný parametr neobsahuje.
  - Obsahuje zadaný parametr, ale její hodnota neodpovídá žádnému z hodnot, které jsou definovány v tomto stavu shody.

Tato podmínka shody poskytuje snadný způsob, jak určit kombinace názvu a hodnoty parametrů. V zájmu větší flexibility Pokud jsou odpovídající parametru řetězce dotazu, zvažte použití [zástupné adresy URL dotazu](#url-query-wildcard) odpovídají podmínce.

Informace o klíči:
- Na jednu instanci této podmínce shody se dá nastavit jenom jedné adresy URL dotazu název parametru.
    
- Protože zástupné hodnoty nejsou podporovány, pokud je zadaný název parametru, pouze název odpovídá přesně parametru jsou způsobilé pro porovnání.
- Hodnoty parametrů může zahrnovat [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
   - Každý vzor hodnota parametru se může skládat z jednoho nebo více hvězdičky (*), kde každý hvězdička odpovídá sekvenci jeden nebo více znaků.
   - Některé znaky vyžadují kódování URL. Použijte symbol procenta na adresu URL kódování následující znaky:

       Znak | Kódování URL
       ----------|---------
       Kosmické aktivity     | %20
       &         | %25

- Zadejte více hodnot parametrů řetězce dotazu podle omezující každé z nich mezerou. Pokud požadavek obsahuje jeden z kombinace zadaný název/hodnota, je splněna podmínka tuto shodu.

   - Příklad 1:

     - Konfigurace:

       Hodnotaa Hodnotab

     - Tato konfigurace se shoduje s následující parametry řetězce dotazu:

       Parametr1 = Hodnotaa
    
       Parametr1 = Hodnotab

   - Příklad 2:

     - Konfigurace: 

        Hodnota % 20A % hodnotu 20B

     - Tato konfigurace se shoduje s následující parametry řetězce dotazu:

       Parametr1 = hodnota % 20A

       Parametr1 = hodnota % 20B

- Splnění této podmínky shodu pouze v případě, že přesnou shodu na alespoň jeden z kombinace názvu a hodnoty řetězec zadaný dotaz.

   Například pokud použijete konfiguraci v předchozím příkladu, parametr název/hodnota kombinaci "parametr1 = ValueAdd" by být považovány za shodné. Nicméně, pokud chcete zadat jednu z následujících hodnot, budou se shodovat s daná kombinace názvu a hodnoty:

   - ValueA ValueB ValueAdd
   - Hodnotab Hodnotaa *

- Použití **ignorování případu** možnost řízení rozlišování porovnávání řetězců dotazu.
    
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
   - Výplň kompletní mezipaměti
   - Interní výchozí Max-Age
   - Platnost vnitřní Max-Age
   - Ignorovat původu No-Cache
   - Interní Nezastaralá Max

#### <a name="sample-scenarios"></a>Ukázkové scénáře
Následující příklad ukazuje, jak se tato možnost funguje v konkrétních situacích:

Název  | Hodnota |  Výsledek
------|-------|--------
Uživatel  | Joe   | Tento model je nalezena shoda, pokud je řetězec dotazu pro požadovanou adresu URL "? uživatel = joe."
Uživatel  | *     | Tento model je nalezena shoda, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr uživatele.
Email | Joe\* | Tento model je nalezena shoda, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr e-mailu, která začíná textem "Jan".

[Zpět na začátek](#main)

</br>

---
### <a name="url-query-regex"></a>Regulární výraz dotazu adresy URL
Určuje požadavky, které obsahují parametr řetězce dotazu zadaná. Tento parametr je nastaven na hodnotu, která odpovídá zadaným [regulárního výrazu](cdn-rules-engine-reference.md#regular-expressions).

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých regulární výraz dotazu adresy URL odpovídají podmínce.
- **Odpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:
- Jenom přesné shody k zadanému regulárnímu výrazu splňují tuto podmínku shody.
    
- Použití **ignorování případu** možnost řízení rozlišování porovnávání řetězců dotazu.
    
- Pro účely této možnosti se spustí řetězec dotazu k prvnímu znaku za oddělovačem otazník (?) pro řetězec dotazu.
    
- Některé znaky vyžadují kódování URL. Použijte symbol procenta na adresu URL kódování následující znaky:

   Znak | Kódování URL | Hodnota
   ----------|--------------|------
   Kosmické aktivity     | %20          | \%20
   &         | %25          | \%25

   Všimněte si, že musí být uvozena procento symboly.

- Speciální regulárního výrazu dvojitou řídicí znaky (například \^$. +) pro vložení zpětného lomítka v regulárním výrazu.

   Příklad:

   Hodnota | Interpretováno jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
   - Výplň kompletní mezipaměti
   - Interní výchozí Max-Age
   - Platnost vnitřní Max-Age
   - Ignorovat původu No-Cache
   - Interní Nezastaralá Max


[Zpět na začátek](#main)

</br>

---
### <a name="url-query-wildcard"></a>Adresa URL dotazu zástupných znaků
Porovná zadané hodnoty pro řetězec dotazu žádosti.

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých zástupné adresy URL dotazu odpovídají podmínce.
- **Odpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá hodnotě zadané zástupný znak.
- **Neodpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá hodnotě zadané zástupný znak.

Informace o klíči:
- Pro účely této možnosti se spustí řetězec dotazu k prvnímu znaku za oddělovačem otazník (?) pro řetězec dotazu.
- Hodnoty parametrů může zahrnovat [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values):
   - Každý vzor hodnota parametru se může skládat z jednoho nebo více hvězdičky (*), kde každý hvězdička odpovídá sekvenci jeden nebo více znaků.
   - Některé znaky vyžadují kódování URL. Použijte symbol procenta na adresu URL kódování následující znaky:

     Znak | Kódování URL
     ----------|---------
     Kosmické aktivity     | %20
     &         | %25

- Zadejte více hodnot ve omezující každé z nich mezerou.

   Příklad: *Parametr1 = Hodnotaa* *Hodnotab* *parametr1 = ValueC & parametr2 = Vážíme si toho*

- Jenom přesné shody k aspoň jednomu ze vzorů řetězec zadaný dotaz splňují tuto podmínku shody.
    
- Použití **ignorování případu** možnost řízení rozlišování porovnávání řetězců dotazu.
    
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
   - Výplň kompletní mezipaměti
   - Interní výchozí Max-Age
   - Platnost vnitřní Max-Age
   - Ignorovat původu No-Cache
   - Interní Nezastaralá Max

#### <a name="sample-scenarios"></a>Ukázkové scénáře
Následující příklad ukazuje, jak se tato možnost funguje v konkrétních situacích:

 Název                 | Popis
 ---------------------|------------
user=joe              | Tento model je nalezena shoda, pokud je řetězec dotazu pro požadovanou adresu URL "? uživatel = joe."
\*user=\* \*optout=\* | Tento model je nalezena shoda, když dotaz adresy URL CDN obsahuje uživatele nebo optout parametru.

[Zpět na začátek](#main)

</br>

## <a name="next-steps"></a>Další postup
* [Přehled služby Azure Content Delivery Network](cdn-overview.md)
* [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
* [Podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkce stroje pravidel](cdn-rules-engine-reference-features.md)
* [Přepisování výchozího chování HTTP pomocí stroje pravidel](cdn-rules-engine.md)

