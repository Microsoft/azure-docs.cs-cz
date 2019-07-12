---
title: Podmínky shody stroje pravidel Azure CDN od Verizonu Premium | Dokumentace Microsoftu
description: Referenční dokumentace pro Azure Content Delivery Network od společnosti Verizon Premium pravidla podmínky shody stroje.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593205"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN od Verizonu Premium stroj pravidel splňují podmínky

Tento článek obsahuje podrobný popis dostupných odpovídají podmínkám pro Azure Content Delivery Network (CDN) od společnosti Verizon Premium [stroj pravidel](cdn-verizon-premium-rules-engine.md).

Druhá část pravidla je podmínce shody. Podmínka shody identifikuje konkrétní typy žádostí, pro které se provede sadu funkcí.

Můžete například použít podmínku shody pro:

- Filtrovat žádosti pro obsah v konkrétních místech.
- Filtrovat žádosti vygenerované z konkrétní IP adresu nebo zemi či oblasti.
- Filtrovat žádosti podle informace záhlaví.

## <a name="always-match-condition"></a>Vždy podmínce shody

Podmínka shody vždy platí výchozí sadu funkcí pro všechny požadavky.

Name | Účel
-----|--------
[Vždy](#always) | Výchozí sada funkcí se vztahuje na všechny požadavky.

## <a name="device-match-condition"></a>Podmínka shody zařízení

Podmínka shody zařízení identifikuje žádosti z mobilního zařízení na základě jeho vlastností.  

Name | Účel
-----|--------
[zařízení](#device) | Identifikuje žádosti z mobilního zařízení na základě jeho vlastností.

## <a name="location-match-conditions"></a>Podmínky shody umístění

Podmínky shody umístění identifikaci požadavků v závislosti na umístění žadatele.

Name | Účel
-----|--------
[JAKO číslo](#as-number) | Určuje požadavky, které pocházejí z určité síti.
[Země](#country) | Určuje požadavky, které pocházejí z zadanou zemí nebo oblastí.

## <a name="origin-match-conditions"></a>Podmínky shody původu

Podmínky shody původu identifikaci požadavků, které odkazují na úložiště Content Delivery Network nebo zákazník zdrojový server.

Name | Účel
-----|--------
[CDN Origin](#cdn-origin) | Identifikuje požadavky na obsah uložený v úložišti síť Content Delivery Network.
[Zákazníka](#customer-origin) | Identifikuje požadavky na obsah uložený na původním serveru konkrétního zákazníka.

## <a name="request-match-conditions"></a>Podmínky shody žádosti

Podmínky shody žádost o identifikaci požadavků na základě jejich vlastností.

Name | Účel
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

Name | Účel
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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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
  - Hodnota souboru cookie můžete využít výhod [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Pokud nebyla zadána hodnota zástupného znaku, bude pouze v případě přesné shody splňují tuto podmínku shody. Například zadání "Value" bude odpovídat "Value", ale ne "Hodnota1" nebo "Hodnota2".
  - Použití **ignorování případu** možnost řízení, zda malá a velká písmena porovnání je provedeno proti hodnotě souboru cookie požadavku.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Soubor cookie parametr Regex

Podmínka shody regulárního výrazu parametru souborů Cookie definuje název souboru cookie a hodnotu. Můžete použít [regulární výrazy](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) definovat hodnotu požadovaného souboru cookie.

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

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

- Odepřít přístup (403) funkce: Povolit [funkce odepření přístupu (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) k replikaci povolit nebo blokovat část funkce filtrování podle země.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Zákazníka

Informace o klíči:

- Bez ohledu na to, zda žádost prostřednictvím adresy URL CDN nebo okraj. adresa URL CNAME, který odkazuje na původní název vybraného zákazníka, je splněna podmínka shody zákazníka.
- Konfigurace původu zákazníků, na který odkazuje pravidlo nelze odstranit ze stránky zákazníka. Než se pokusíte odstranit konfiguraci původu zákazníka, ujistěte se, že následující konfigurace není na něj odkazovat:
  - Podmínku shody zákazníka
  - Konfigurace služby edge CNAME
- Nepoužívejte příkaz IF a kombinovat určité podmínky shody. Kombinování podmínku shody zákazníka s podmínku shody původu CDN byste například vytvořit vzor pro shodu, která by mohla nikdy existovat shoda. Z tohoto důvodu nelze kombinovat dvě podmínky shody zákazníka prostřednictvím příkazu a v případě.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

type     | Popis
---------|------------
literál  | Vyberte tuto možnost, chcete-li zabránit většině znaků na zvláštní význam pomocí jejich [literálovou hodnotou](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Zástupný znak | Vyberte tuto možnost, chcete-li využít výhod všech [zástupné znaky] ([zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Vyberte tuto možnost používat [regulární výrazy](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regulární výrazy jsou užitečné pro definování vzor znaků.

#### <a name="wurfl-capabilities"></a>Možnosti WURFL

Funkce WURFL odkazuje na kategorii, která popisuje mobilní zařízení. Vybrané funkce určuje typ mobilního zařízení popis, který se používá k identifikaci požadavků.

Následující tabulka uvádí možnosti WURFL a jejich proměnné pro stroj pravidel.

> [!NOTE]
> V jsou podporovány následující proměnné **upravit hlavička požadavku klienta** a **upravit hlavičku odpovědi klienta** funkce.

Funkce | Proměnná | Popis | Ukázkové hodnoty
-----------|----------|-------------|----------------
Název značky | %{wurfl_cap_brand_name} | Řetězec určující název značky zařízení. | Samsung
Operační systém zařízení | %{wurfl_cap_device_os} | Řetězec, který označuje operační systém nainstalovaný na zařízení. | IOS
Verze operačního systému zařízení | %{wurfl_cap_device_os_version} | Řetězec, který označuje číslo verze operačního systému na zařízení nainstalovaná. | 1.0.1
Duální orientace | %{wurfl_cap_dual_orientation} | Logická hodnota, která určuje, jestli zařízení podporuje duální orientace. | true (pravda)
HTML upřednostňované specifikace DTD | %{wurfl_cap_html_preferred_dtd} | Řetězec, který označuje mobilních zařízení upřednostňované dokumentu typ definice (DTD) pro obsah ve formátu HTML. | None<br/>xhtml_basic<br/>html5
Vkládání obrázků | %{wurfl_cap_image_inlining} | Logická hodnota, která určuje, zda zařízení podporuje Base64 kódovaný bitové kopie. | false
Is Android | %{wurfl_vcap_is_android} | Logická hodnota, která označuje, zda zařízení používá operační systém Android. | true (pravda)
Je s IOSEM | %{wurfl_vcap_is_ios} | Logická hodnota, která označuje, zda zařízení používá iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | Logická hodnota, která určuje, jestli zařízení je inteligentní TV. | false
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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Odkaz domény

Název hostitele přidružený referrer, přes který byl vyžádán obsah Určuje, zda je splněna podmínka odkazující domény.

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých odkazující domény odpovídají podmínce:

- **Odpovídá**: Vyžaduje odkazující název hostitele tak, aby odpovídaly zadanými hodnotami. 
- **Neodpovídá**: Vyžaduje, aby odkazující název hostitele se neshoduje s zadanou hodnotu.

Informace o klíči:

- Zadejte více názvů hostitelů podle omezující každé z nich mezerou.
- Podporuje tato podmínka shody [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Pokud zadaná hodnota neobsahuje hvězdičky, musí být přesná shoda pro název hostitele odkazující server. Například zadání "domena.cz" shodě "www.mydomain.com."
- Použití **ignorování případu** možnost řízení, zda je provedeno porovnání velká a malá písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Regulární výraz hlavičky požadavku

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých regulární výraz záhlaví požadavku odpovídají podmínce.

- **Odpovídá**: Vyžaduje žádost obsahující zadanou hlavičku. Jeho hodnota musí odpovídat vzoru, který je definován v zadaném [regulárního výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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
  - Hodnota hlavičky můžou těžit z výhod [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Použití **ignorování případu** možnost řízení rozlišování porovnání hodnot hlavičky.
  - Pokud hodnota hlavičky k aspoň jednomu ze vzorů zadaný přesně odpovídá, je splněna podmínka tuto shodu.
  - Zadejte více hodnot ve omezující každé z nich mezerou.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
  - Výplň kompletní mezipaměti
  - Interní výchozí Max-Age
  - Platnost vnitřní Max-Age
  - Ignorovat původu No-Cache
  - Interní Nezastaralá Max

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

- Specifikaci cesty adresy URL můžete využít výhod [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

- Hodnota názvu souboru můžete využít výhod [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Například každý vzor názvů souborů se může skládat z jednoho nebo více hvězdičky (*), kde každý hvězdička odpovídá posloupnosti jeden nebo více znaků.

- Pokud nejsou zadané zástupné znaky, bude splňovat pouze v případě přesné shody této podmínce shody.

    Například zadání "presentation.ppt" odpovídá prostředek s názvem "presentation.ppt", ale ne jeden s názvem "prezentace.pptx."

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Regulární výraz cesty adresy URL

Porovná cesta URL požadavku na zadaný [regulárního výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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
     - Adresa URL pro CDN: http://wpc.0001.&lt ; domény&gt; /800001/CustomerOrigin/path/asset.htm
     - Adresa URL CNAME Edge: http:\//&lt;koncový bod&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    
     - Cesta adresy URL (relativní vůči kořenovému adresáři): /800001/CustomerOrigin/path/asset.htm
    
     - Cesta adresy URL (relativní k původu): /path/asset.htm
    
- Určete víc cest URL omezující každé z nich mezerou.

   Příklad: /marketing/asset.* /sales/*.htm

- Řetězce dotazu v adrese URL se ignorují.
    
- Použití **ignorování případu** možnost řízení, zda se provádí porovnání velká a malá písmena.
    
- Nahraďte mezery v cestě adresy URL ": % 20."
    
- Hodnota zadaná pro cestu adresy URL můžete využít výhod [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Každý vzor cesty adresy URL může obsahovat jeden nebo více hvězdičky (*), kde každý hvězdička odpovídá sekvenci jeden nebo více znaků.

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Ukázková konfigurace v následující tabulce se předpokládá, že pokud požadavek odpovídá zadanému vzoru adresy URL je splněna podmínka tuto shodu:

Value                   | Relativní vzhledem k    | Výsledek 
------------------------|----------------|-------
*/test.html */test.php  | Kořenový adresář nebo původu | Tento model je nalezena shoda s požadavky na prostředky s názvem "test.html" nebo "test.php" v jakékoli složce.
/80ABCD/origin/text/*   | Kořenové           | Tento model je nalezena shoda, pokud požadovaný prostředek splňuje následující kritéria: <br />– Musí být umístěny na zákazníka původu názvem "origin". <br />– Relativní cesta musí začínat znakem složku s názvem "text". To znamená, že požadovaný prostředek se může nacházet ve složce "text" nebo jeden z jejích podsložek rekurzivní.
*/css/* */js/*          | Kořenový adresář nebo původu | Tento vzor odpovídá všem CDN nebo Microsoft edge CNAME adresy URL, které obsahují složky šablony stylů css a js.
*.jpg *.gif *.png       | Kořenový adresář nebo původu | Tento vzor odpovídá všem CDN nebo Microsoft edge CNAME adresy URL končící JPG, GIF nebo PNG. Alternativní způsob určení tento model je [rozšíření cesty adresy URL odpovídají podmínce](#url-path-extension).
/ imagí / * / media / *      | Zdroj         | Tento vzor odpovídá CDN nebo Microsoft edge CNAME adresy URL, jehož relativní cesta začínající znakem "Image" nebo "média" složky. <br />– CDN adresa URL: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/images/sales/event1.png<br />-Ukázkový edge CNAME adresy URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

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
- Hodnoty parametrů může zahrnovat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
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

Name  | Hodnota |  Výsledek
------|-------|--------
Uživatel  | Joe   | Tento model je nalezena shoda, pokud je řetězec dotazu pro požadovanou adresu URL "? uživatel = joe."
Uživatel  | *     | Tento model je nalezena shoda, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr uživatele.
Email | Joe\* | Tento model je nalezena shoda, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr e-mailu, která začíná textem "Jan".

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Regulární výraz dotazu adresy URL

Určuje požadavky, které obsahují parametr řetězce dotazu zadaná. Tento parametr je nastaven na hodnotu, která odpovídá zadaným [regulárního výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých regulární výraz dotazu adresy URL odpovídají podmínce.

- **Odpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:

- Jenom přesné shody k zadanému regulárnímu výrazu splňují tuto podmínku shody.
    
- Použití **ignorování případu** možnost řízení rozlišování porovnávání řetězců dotazu.
    
- Pro účely této možnosti se spustí řetězec dotazu k prvnímu znaku za oddělovačem otazník (?) pro řetězec dotazu.
    
- Některé znaky vyžadují kódování URL. Použijte symbol procenta na adresu URL kódování následující znaky:

   Znak | Kódování URL | Value
   ----------|--------------|------
   Kosmické aktivity     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Všimněte si, že musí být uvozena procento symboly.

- Speciální regulárního výrazu dvojitou řídicí znaky (například \^$. +) pro vložení zpětného lomítka v regulárním výrazu.

   Příklad:

   Value | Interpretováno jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shody není kompatibilní s následujícími funkcemi:
   - Výplň kompletní mezipaměti
   - Interní výchozí Max-Age
   - Platnost vnitřní Max-Age
   - Ignorovat původu No-Cache
   - Interní Nezastaralá Max

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Adresa URL dotazu zástupných znaků

Porovná zadané hodnoty pro řetězec dotazu žádosti.

**Shody**/**neodpovídá** možnost určuje splněny podmínky, za kterých zástupné adresy URL dotazu odpovídají podmínce.

- **Odpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá hodnotě zadané zástupný znak.
- **Neodpovídá**: Vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá hodnotě zadané zástupný znak.

Informace o klíči:

- Pro účely této možnosti se spustí řetězec dotazu k prvnímu znaku za oddělovačem otazník (?) pro řetězec dotazu.
- Hodnoty parametrů může zahrnovat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
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

 Name                 | Popis
 ---------------------|------------
user=joe              | Tento model je nalezena shoda, pokud je řetězec dotazu pro požadovanou adresu URL "? uživatel = joe."
\*user=\* \*optout=\* | Tento model je nalezena shoda, když dotaz adresy URL CDN obsahuje uživatele nebo optout parametru.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Další postup

- [Přehled služby Azure Content Delivery Network](cdn-overview.md)
- [Referenční informace ke stroji pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepisování výchozího chování HTTP pomocí stroje pravidel](cdn-verizon-premium-rules-engine.md)
