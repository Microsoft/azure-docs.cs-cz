---
title: Azure CDN od Verizon Premium pravidla pravidla režimu zápas podmínky | Dokumenty společnosti Microsoft
description: Referenční dokumentace pro Azure Content Delivery Network od Verizon Premium pravidla pravidla podmínky.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593205"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN od Verizon Premium pravidla pravidla podmínky motoru zápas

Tento článek obsahuje podrobný popis dostupných podmínek shody pro síť azure obsahu doručovací sítě (CDN) z verizon premium [pravidla motoru](cdn-verizon-premium-rules-engine.md).

Druhá část pravidla je podmínka shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které bude provedena sada funkcí.

Podmínku shody můžete například použít k:

- Filtrování požadavků na obsah v určitém umístění.
- Požadavky filtru generované z určité adresy IP nebo země či oblasti.
- Filtrujte požadavky podle informací záhlaví.

## <a name="always-match-condition"></a>Vždy shodovat podmínky

Podmínka Vždy shoda použije výchozí sadu funkcí pro všechny požadavky.

Name (Název) | Účel
-----|--------
[Vždy](#always) | Použije výchozí sadu funkcí pro všechny požadavky.

## <a name="device-match-condition"></a>Podmínka shody zařízení

Podmínka shody zařízení identifikuje požadavky z mobilního zařízení na základě jeho vlastností.  

Name (Název) | Účel
-----|--------
[Zařízení](#device) | Identifikuje požadavky z mobilního zařízení na základě jeho vlastností.

## <a name="location-match-conditions"></a>Podmínky shody umístění

Podmínky shody umístění identifikují požadavky na základě umístění uchazeče.

Name (Název) | Účel
-----|--------
[Číslo AS](#as-number) | Identifikuje požadavky, které pocházejí z určité sítě.
[Země](#country) | Identifikuje požadavky, které pocházejí ze zadaných zemí nebo oblastí.

## <a name="origin-match-conditions"></a>Podmínky shody původu

Podmínky shody Origin identifikují požadavky, které odkazují na úložiště sítě doručování obsahu nebo na server původu zákazníka.

Name (Název) | Účel
-----|--------
[Původ CDN](#cdn-origin) | Identifikuje požadavky na obsah uložený v úložišti sítě doručování obsahu.
[Původ zákazníka](#customer-origin) | Identifikuje požadavky na obsah uložený na konkrétním serveru původu zákazníka.

## <a name="request-match-conditions"></a>Požádat o podmínky shody

Podmínky shody požadavku identifikují požadavky na základě jejich vlastností.

Name (Název) | Účel
-----|--------
[Client IP Address](#client-ip-address) | Identifikuje požadavky, které pocházejí z určité adresy IP.
[Parametr souboru cookie](#cookie-parameter) | Zkontroluje soubory cookie přidružené ke každému požadavku pro zadanou hodnotu.
[Regex parametru souboru cookie](#cookie-parameter-regex) | Zkontroluje soubory cookie přidružené ke každému požadavku pro zadaný regulární výraz.
[Název hrany C](#edge-cname) | Identifikuje požadavky, které odkazují na konkrétní okraj CNAME.
[Odkazující doména](#referring-domain) | Identifikuje požadavky, které byly odkazovány ze zadaných názvů hostitelů.
[Literál hlavičky požadavku](#request-header-literal) | Identifikuje požadavky, které obsahují zadanou sadu hlaviček na zadanou hodnotu.
[Hlavička požadavku Regex](#request-header-regex) | Identifikuje požadavky, které obsahují zadanou sadu hlaviček nastavenou na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Zástupný znak záhlaví požadavku](#request-header-wildcard) | Identifikuje požadavky, které obsahují zadanou sadu hlaviček na hodnotu, která odpovídá zadanému vzoru.
[Request – metoda](#request-method) | Identifikuje požadavky jejich metodou HTTP.
[Schéma žádostí](#request-scheme) | Identifikuje požadavky podle jejich protokolu HTTP.

## <a name="url-match-conditions"></a>Podmínky shody adres URL

Podmínky shody adres URL identifikují požadavky na základě jejich adres URL.

Name (Název) | Účel
-----|--------
[Adresář cesty URL](#url-path-directory) | Identifikuje požadavky podle jejich relativní cesty.
[Rozšíření cesty URL](#url-path-extension) | Identifikuje požadavky podle jejich přípony názvu souboru.
[Název souboru cesty URL](#url-path-filename) | Identifikuje požadavky podle jejich názvu.
[Literál cesty URL](#url-path-literal) | Porovná relativní cestu požadavku k zadané hodnotě.
[Regex cesty URL](#url-path-regex) | Porovná relativní cestu požadavku k zadanému regulárnímu výrazu.
[Zástupný znak cesty URL](#url-path-wildcard) | Porovná relativní cestu požadavku se zadaným vzorkem.
[Literál dotazu URL](#url-query-literal) | Porovná řetězec dotazu požadavku na zadanou hodnotu.
[Parametr dotazu URL](#url-query-parameter) | Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu nastavený na hodnotu, která odpovídá zadanému vzoru.
[Regex dotazu URL](#url-query-regex) | Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu nastavený na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Zástupný znak dotazu URL](#url-query-wildcard) | Porovná zadanou hodnotu s řetězcem dotazu požadavku.

## <a name="reference-for-rules-engine-match-conditions"></a>Odkaz na podmínky shody motoru pravidla

---

### <a name="always"></a>Vždy

Podmínka Vždy shoda použije výchozí sadu funkcí pro všechny požadavky.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Číslo AS

Síť čísel AS je definována svým číslem autonomního systému (ASN). 

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody čísla AS:

- **Shody**: Vyžaduje, aby asn klientské sítě odpovídá jeden ze zadaných ASNs. 
- **Neodpovídá**: Vyžaduje, aby číslo ASN klientské sítě neodpovídalo žádnému ze zadaných seznamů ASN.

Klíčové informace:

- Určete více názvů ASN tak, že každý z nich vymezí jednou mezerou. Například 64514 64515 odpovídá požadavkům, které přicházejí z 64514 nebo 64515.
- Některé požadavky nemusí vrátit platné ASN. Otazník (?) bude odpovídat požadavkům, pro které nebylo možné určit platný odkaz ASN.
- Zadejte celý asn pro požadovanou síť. Částečné hodnoty nebudou spárovány.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Původ CDN

Podmínka shody původu CDN je splněna, pokud jsou splněny obě následující podmínky:

- Byl požadován obsah z úložiště CDN.
- Identifikátor URI požadavku používá typ přístupového bodu obsahu (například /000001), který je definován v této podmínce shody:
  - ADRESA URL CDN: Identifikátor URI požadavku musí obsahovat vybraný přístupový bod obsahu.
  - Adresa URL CNAME okraje: Odpovídající konfigurace CNAME okrajů musí překážet na vybraný přístupový bod obsahu.
  
Klíčové informace:

- Přístupový bod obsahu identifikuje službu, která by měla sloužit požadovaný obsah.
- Nepoužívejte příkaz AND IF ke kombinování určitých podmínek shody. Například kombinace podmínky shody původu CDN s podmínkou shody Původ zákazníka by vytvořila vzor shody, který by nikdy nemohl být spárován. Z tohoto důvodu nelze dvě podmínky shody cdn originu kombinovat prostřednictvím příkazu AND IF.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Client IP Address

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody IP adresy klienta:

- **Shody**: Vyžaduje, aby ip adresa klienta odpovídala jedné ze zadaných adres IP. 
- **Neshoduje se**: Vyžaduje, aby adresa IP klienta neodpovídala žádné zadané adrese IP. 

Klíčové informace:

- Použijte cidr zápis.
- Zadejte více adres IP a/nebo bloků IP adres tak, že každý z nich vymezíte jednou mezerou. Například:
  - **Příklad IPv4**: 1.2.3.4 10.20.30.40 odpovídá všem požadavkům, které přicházejí z adresy 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 odpovídá všem požadavkům, které přicházejí z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe bloku IP adres je základní IP adresa následovaná lomítkem a velikostí předpony. Například:
  - **Příklad IPv4**: 5.5.5.64/26 odpovídá všem požadavkům, které přicházejí z adres 5.5.5.64 až 5.5.5.127.
  - **Příklad IPv6**: 1:2:3:/48 odpovídá všem požadavkům, které přicházejí z adres 1:2:3:0:0:0:0: 0 až 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff.ffff.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parametr souboru cookie

Možnost **Neodpovídá**/**Does Not Match** podmínkám, za kterých je splněna podmínka shody parametru souboru cookie.

- **Shody**: Vyžaduje požadavek na obsahující zadaný soubor cookie s hodnotou, která odpovídá alespoň jedné z hodnot, které jsou definovány v této podmínce shody.
- **Neodpovídá**: Vyžaduje, aby požadavek splňoval některé z následujících kritérií:
  - Neobsahuje zadaný soubor cookie.
  - Obsahuje zadaný soubor cookie, ale jeho hodnota neodpovídá žádné z hodnot, které jsou definovány v této podmínce shody.
  
Klíčové informace:

- Název souboru cookie:
  - Vzhledem k tomu, že hodnoty zástupných symbolů, včetně hvězdiček (*), nejsou při zadávání názvu souboru cookie podporovány, lze pro porovnání popřípadě porovnávat pouze přesné shody názvů souborů cookie.
  - Pro každou instanci této podmínky shody lze zadat pouze jeden název souboru cookie.
  - Porovnání názvů souborů jsou malá a velká písmena.
- Hodnota souboru cookie:
  - Zadejte více hodnot souborů cookie tak, že každou z nich vymezte jednou mezerou.
  - Hodnota souboru cookie může využít [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Pokud nebyla zadána hodnota zástupných symbolů, bude tuto podmínku shody splňovat pouze přesná shoda. Například zadání "Hodnota" bude odpovídat "Hodnota", ale ne "Hodnota1" nebo "Hodnota2."
  - Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda bude provedeno porovnání s rozlišování malých a velkých písmen s hodnotou souboru cookie požadavku.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Regex parametru souboru cookie

Podmínka shody parametru cookie Regex definuje název a hodnotu souboru cookie. K definování požadované hodnoty souboru cookie můžete použít [regulární výrazy.](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)

Možnost **Neodpovídá**/**Does Not Match** podmínkám, za kterých je splněna podmínka shody parametru cookie Regex.

- **Shody**: Vyžaduje požadavek na obsahující zadaný soubor cookie s hodnotou, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá**: Vyžaduje, aby požadavek splňoval některé z následujících kritérií:
  - Neobsahuje zadaný soubor cookie.
  - Obsahuje zadaný soubor cookie, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.
  
Klíčové informace:

- Název souboru cookie:
  - Vzhledem k tomu, že při zadávání názvu souboru cookie nejsou podporovány regulární výrazy a hodnoty zástupných symbolů, včetně hvězdiček (*), lze je porovnat pouze přesnou shodou názvů souborů cookie.
  - Pro každou instanci této podmínky shody lze zadat pouze jeden název souboru cookie.
  - Porovnání názvů souborů jsou malá a velká písmena.
- Hodnota souboru cookie:
  - Hodnota souboru cookie může využívat regulární výrazy.
  - Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda bude provedeno porovnání s rozlišování malých a velkých písmen s hodnotou souboru cookie požadavku.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Země

Zemi můžete zadat prostřednictvím jejího kódu země. 

Možnost **Neodpovídá**/**Does Not Match** podmínkám, za kterých je splněna podmínka shody země:

- **Shody**: Vyžaduje, aby požadavek obsahoval zadané hodnoty kódu země. 
- **Neshoduje se**: Vyžaduje, aby požadavek neobsahoval zadané hodnoty kódu země.

Klíčové informace:

- Zadejte více kódů zemí tak, že každý z nich vymezí tepojednou mezerou.
- Při zadávání kódu země nejsou zástupné znaky podporovány.
- Kódy zemí "EU" a "AP" nezahrnují všechny IP adresy v těchto oblastech.
- Některé požadavky nemusí vrátit platný kód země. Otazník (?) bude odpovídat požadavkům, pro které nebylo možné určit platný kód země.
- Kódy zemí rozlišují malá a velká písmena.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementace filtrování zemí pomocí modulu pravidel

Tato podmínka shody umožňuje provádět velké množství vlastních nastavení na základě umístění, ze kterého požadavek pochází. Například chování funkce filtrování zemí lze replikovat prostřednictvím následující konfigurace:

- Shoda zástupných symbolů cesty URL: Nastavte [podmínku shody zástupných symbolů cesty URL](#url-path-wildcard) do adresáře, který bude zabezpečen. 
    Připojit hvězdičku na konec relativní cesty k zajištění, že přístup ke všem jeho podřízené objekty budou omezeny tímto pravidlem.

- Zápas země: Nastavte podmínku shody země na požadovanou sadu zemí.
  - Povolit: Nastavte podmínku shody země na **Neodpovídá** tak, aby pouze určené země měly přístup k obsahu uloženému v umístění definovaném podmínkou shody se zástupnými kartami cesty URL.
  - Blokovat: Nastavte podmínku shody země na **shodu shody,** aby zadané země nepřistupovaly k obsahu uloženému v umístění definovaném podmínkou shody se zástupnými kartami cesty URL.

- Funkce Odepřít přístup (403): Povolte [funkci Odepřít přístup (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) a replikujte povolit nebo blokovat část funkce Filtrování zemí.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Původ zákazníka

Klíčové informace:

- Podmínka shody Původ zákazníka je splněna bez ohledu na to, zda je obsah požadován prostřednictvím adresy URL CDN nebo hraniční adresy CNAME, která odkazuje na původ vybraného zákazníka.
- Konfiguraci původu zákazníka, na kterou odkazuje pravidlo, nelze odstranit ze stránky Původ zákazníka. Než se pokusíte odstranit konfiguraci původu zákazníka, ujistěte se, že na něj neodkazují následující konfigurace:
  - Podmínka shody Původ zákazníka
  - Konfigurace CNAME hrany
- Nepoužívejte příkaz AND IF ke kombinování určitých podmínek shody. Například kombinace podmínky shody původ zákazníka s podmínkou shody původu CDN by vytvořila vzor shody, který by nikdy nemohl odpovídat. Z tohoto důvodu nelze dvě podmínky shody původu zákazníka kombinovat prostřednictvím příkazu AND IF.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Zařízení

Podmínka shody zařízení identifikuje požadavky z mobilního zařízení na základě jeho vlastností. Detekce mobilních zařízení je dosažena pomocí [WURFL](http://wurfl.sourceforge.net/). 

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody zařízení:

- **Shody**: Vyžaduje, aby zařízení uchazeče odpovídalo zadané hodnotě. 
- **Neodpovídá**: Vyžaduje, aby zařízení uchazeče neodpovídalo zadané hodnotě.

Klíčové informace:

- Pomocí možnosti **Ignorovat malá a velká písmena** určete, zda je zadaná hodnota rozlišována malá a velká písmena.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

#### <a name="string-type"></a>Typ řetězce

Funkce WURFL obvykle přijímá libovolnou kombinaci čísel, písmen a symbolů. Vzhledem k flexibilní povaze této funkce je nutné zvolit způsob interpretace hodnoty přidružené k této podmínce shody. Následující tabulka popisuje dostupnou sadu možností:

Typ     | Popis
---------|------------
Literálu  | Tuto možnost vyberte, chcete-li zabránit tomu, aby většina znaků nabyla zvláštního významu pomocí jejich [literálové hodnoty](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Zástupný znak | Tuto možnost vyberte, chcete-li využít výhod všech [zástupných znaků]([hodnoty zástupných znaků](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Tuto možnost vyberte, chcete-li použít [regulární výrazy](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regulární výrazy jsou užitečné pro definování vzoru znaků.

#### <a name="wurfl-capabilities"></a>Možnosti WURFL

Funkce WURFL odkazuje na kategorii, která popisuje mobilní zařízení. Vybraná funkce určuje typ popisu mobilního zařízení, který se používá k identifikaci požadavků.

V následující tabulce jsou uvedeny možnosti wurfl a jejich proměnné pro modul pravidel.

> [!NOTE]
> Následující proměnné jsou podporovány v **funkcích Změnit hlavičku požadavku klienta** a **Změnit hlavičku klienta.**

Schopnost | Proměnná | Popis | Ukázkové hodnoty
-----------|----------|-------------|----------------
Značka | %{wurfl_cap_brand_name} | Řetězec, který označuje název značky zařízení. | Samsung
Operační zařízení | %{wurfl_cap_device_os} | Řetězec, který označuje operační systém nainstalovaný v zařízení. | iOS
Verze operačního systému zařízení | %{wurfl_cap_device_os_version} | Řetězec, který označuje číslo verze operačního systému nainstalovaného v zařízení. | 1.0.1
Duální orientace | %{wurfl_cap_dual_orientation} | Logická hodnota, která označuje, zda zařízení podporuje duální orientaci. | true
HTML upřednostňovaný DTD | %{wurfl_cap_html_preferred_dtd} | Řetězec, který označuje definici typu dokumentu upřednostňovaného mobilního zařízení (DTD) pro obsah HTML. | Žádná<br/>xhtml_basic<br/>html5
Obrázek Vsazení | %{wurfl_cap_image_inlining} | Logická hodnota, která označuje, zda zařízení podporuje bitové kopie kódované base64. | false (nepravda)
Je Android | %{wurfl_vcap_is_android} | Logická hodnota, která označuje, zda zařízení používá operační systém Android. | true
Je iOS | %{wurfl_vcap_is_ios} | Logická hodnota, která označuje, zda zařízení používá iOS. | false (nepravda)
Je Smart TV | %{wurfl_cap_is_smarttv} | Logická hodnota, která označuje, zda se jedná o inteligentní televizor. | false (nepravda)
Je smartphone | %{wurfl_vcap_is_smartphone} | Logická hodnota, která označuje, zda se jedná o smartphone. | true
Je tablet | %{wurfl_cap_is_tablet} | Logická hodnota, která označuje, zda je zařízení tablet. Tento popis je nezávislý na os. | true
Je bezdrátové zařízení | %{wurfl_cap_is_wireless_device} | Logická hodnota označující, zda je zařízení považováno za bezdrátové zařízení. | true
Marketingový název | %{wurfl_cap_marketing_name} | Řetězec, který označuje marketingový název zařízení. | BlackBerry 8100 Perla
Mobilní prohlížeč | %{wurfl_cap_mobile_browser} | Řetězec, který označuje prohlížeč, který se používá k vyžádání obsahu ze zařízení. | Chrome
Verze mobilního prohlížeče | %{wurfl_cap_mobile_browser_version} | Řetězec, který označuje verzi prohlížeče, který se používá k vyžádání obsahu ze zařízení. | 31
Název modelu | %{wurfl_cap_model_name} | Řetězec, který označuje název modelu zařízení. | s3
Postupné stahování | %{wurfl_cap_progressive_download} | Logická hodnota, která označuje, zda zařízení podporuje přehrávání zvuku a videa, zatímco je stále stahováno. | true
Datum vydání | %{wurfl_cap_release_date} | Řetězec, který označuje rok a měsíc, na kterém bylo zařízení přidáno do databáze WURFL.<br/><br/>Formát:`yyyy_mm` | 2013_december
Výška rozlišení | %{wurfl_cap_resolution_height} | Celé číslo, které označuje výšku zařízení v pixelech. | 768
Šířka rozlišení | %{wurfl_cap_resolution_width} | Celé číslo, které označuje šířku zařízení v pixelech. | 1024

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Název hrany C

Klíčové informace:

- Seznam dostupných okrajových CNAMEs je omezen na ty hraniční CNAMEs, které byly nakonfigurovány na stránce Edge CNAMEs pro platformu, na které je konfigurován modul pravidel.
- Před pokusem o odstranění konfigurace CNAME okrajů se ujistěte, že podmínka shody Edge Cname na něj neodkazuje. Konfigurace CNAME okraje, které byly definovány v pravidle nelze odstranit ze stránky Edge CNAMEs.
- Nepoužívejte příkaz AND IF ke kombinování určitých podmínek shody. Například kombinace podmínky shody Edge Cname s podmínkou shody Původ zákazníka by vytvořila vzor shody, který by nikdy nemohl být spárován. Z tohoto důvodu nelze kombinovat dvě podmínky shody Edge Cname prostřednictvím příkazu AND IF.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Odkazující doména

Název hostitele přidružený k odkazující, jehož prostřednictvím byl požadován obsah, určuje, zda je splněna podmínka odkazující domény.

Možnost **Neodpovídá**/**Does Not Match** hodnotě určuje podmínky, za kterých je splněna podmínka shody odkazující domény:

- **Shody**: Vyžaduje, aby odkazující název hostitele odpovídal zadaným hodnotám. 
- **Neodpovídá**: Vyžaduje, aby odkazující název hostitele neodpovídal zadané hodnotě.

Klíčové informace:

- Zadejte více názvů hostitelů tak, že každý z nich vymezí temnou mezerou.
- Tato podmínka shody podporuje [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Pokud zadaná hodnota neobsahuje hvězdičku, musí se přesně shodovat s názvem hostitele odkazovaného odkazu. Například zadání "mydomain.com" by se neshoduje "www.mydomain.com".
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda bude provedeno porovnání rozlišující malá a velká písmena.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literál hlavičky požadavku

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka literálu hlavičky požadavku.

- **Shody**: Vyžaduje, aby požadavek obsahoval zadanou hlavičku. Jeho hodnota se musí shodovat s hodnotou definovanou v této podmínce shody.
- **Neodpovídá**: Vyžaduje, aby požadavek splňoval některé z následujících kritérií:
  - Neobsahuje zadanou hlavičku.
  - Obsahuje zadanou hlavičku, ale jeho hodnota neodpovídá hodnotě, která je definována v této podmínce shody.
  
Klíčové informace:

- Porovnání názvů záhlaví vždy nerozlišují malá a velká písmena. Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen pro porovnání hodnot záhlaví.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Hlavička požadavku Regex

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody hlavičky požadavku Regex.

- **Shody**: Vyžaduje, aby požadavek obsahoval zadanou hlavičku. Jeho hodnota musí odpovídat vzoru, který je definován v zadaném [regulárním výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Neodpovídá**: Vyžaduje, aby požadavek splňoval některé z následujících kritérií:
  - Neobsahuje zadanou hlavičku.
  - Obsahuje zadanou hlavičku, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.

Klíčové informace:

- Název záhlaví:
  - Porovnání názvů záhlaví nerozlišují malá a velká písmena.
  - Nahraďte mezery v názvu záhlaví %20.
- Hodnota záhlaví:
  - Hodnota záhlaví může využívat regulární výrazy.
  - Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen pro porovnání hodnot záhlaví.
  - Podmínka shody je splněna pouze v případě, že hodnota záhlaví přesně odpovídá alespoň jednomu ze zadaných vzorků.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Zástupný znak záhlaví požadavku

Možnost **Neodpovídá**/**Does Not Match** podmínkám, za kterých je splněna podmínka shody zástupných symbolů záhlaví požadavku.

- **Shody**: Vyžaduje, aby požadavek obsahoval zadanou hlavičku. Jeho hodnota musí odpovídat alespoň jedné z hodnot, které jsou definovány v této podmínce shody.
- **Neodpovídá**: Vyžaduje, aby požadavek splňoval některé z následujících kritérií:
  - Neobsahuje zadanou hlavičku.
  - Obsahuje zadanou hlavičku, ale jeho hodnota neodpovídá žádné zadané hodnoty.
  
Klíčové informace:

- Název záhlaví:
  - Porovnání názvů záhlaví nerozlišují malá a velká písmena.
  - Mezery v názvu záhlaví by měly být nahrazeny %20. Tuto hodnotu můžete také použít k určení mezer v hodnotě záhlaví.
- Hodnota záhlaví:
  - Hodnota záhlaví může využít [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen pro porovnání hodnot záhlaví.
  - Tato podmínka shody je splněna, když hodnota záhlaví přesně odpovídá alespoň jednomu ze zadaných vzorků.
  - Určete více hodnot vymezením každé z nich jednou mezerou.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Request – metoda

Podmínka shody Metody požadavku je splněna pouze v případě, že jsou prostředky požadovány prostřednictvím vybrané metody požadavku. Dostupné metody požadavků jsou:

- GET
- HEAD
- POST
- Možnosti
- PUT
- DELETE
- TRACE
- Připojit

Klíčové informace:

- Ve výchozím nastavení může v síti generovat obsah uložený v mezipaměti pouze metoda požadavku GET. Všechny ostatní metody požadavku jsou proxied prostřednictvím sítě.
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Schéma žádostí

Podmínka shody schématu požadavků je splněna pouze v případě, že jsou prostřednictvím vybraného protokolu požadovány datové zdroje. Dostupné protokoly jsou:

- HTTP
- HTTPS

Klíčové informace:

- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Dokončit výplň mezipaměti
  - Výchozí interní maximální stáří
  - Vynutit vnitřní maximální stáří
  - Ignorovat origin bez mezipaměti
  - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Adresář cesty URL

Identifikuje požadavek podle jeho relativní cesty, která vylučuje název souboru požadovaného datového zdroje.

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody adresáře cesty URL.

- **Shody**: Vyžaduje, aby požadavek obsahoval relativní cestu URL, s výjimkou názvu souboru, který odpovídá zadanému vzoru adresy URL.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval relativní cestu URL s výjimkou názvu souboru, která neodpovídá zadanému vzoru adresy URL.

Klíčové informace:

- Pomocí možnosti **Relativní k** určení, zda se porovnání adres URL spustí před nebo za přístupovým bodem obsahu. Přístupový bod obsahu je část cesty, která se zobrazí mezi verizon cdn název hostitele a relativní cestu k požadovanému prostředku (například /800001/CustomerOrigin). Identifikuje umístění podle typu serveru (například CDN nebo původ zákazníka) a číslo vašeho účtu zákazníka.

   Pro možnost **Relativní k** jsou k dispozici následující hodnoty:
  - **Kořen**: Označuje, že bod porovnání adres URL začíná bezprostředně za názvem hostitele CDN. 

  Například: http:\//wpc.0001. &lt;doména&gt;/**800001/myorigin/myfolder**/index.htm

  - **Původ**: Označuje, že bod porovnání adres URL začíná za přístupovým bodem obsahu (například /000001 nebo /800001/myorigin). Vzhledem \*k tomu, že .azureedge.net CNAME je vytvořen vzhledem k původu adresáře na Verizon CDN hostname ve výchozím nastavení, uživatelé Azure CDN by měl použít hodnotu **Origin.** 

  Příklad: https:\//&lt;koncový&gt;bod .azureedge.net/**myfolder**/index.htm 

  Tato adresa URL odkazuje na následující název\/hostitele Verizon CDN: http: /wpc.0001. &lt;doména&gt;/800001/myorigin/**myfolder**/index.htm

- Okrajová adresa URL CNAME je před porovnáním adres URL přepsána na adresu URL CDN.

    Například obě následující adresy URL odkazují na stejný datový zdroj a proto mají stejnou cestu url.
  - CDN URL:\/http: /wpc.0001. &lt;doména&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL:\//&lt;http: koncový bod&gt;.azureedge.net/path/asset.htm
    
    Další informace:
  - Vlastní doména:\/https: /my.domain.com/path/asset.htm
    
    - Cesta URL (vzhledem ke kořenu): /800001/CustomerOrigin/path/
    
    - Cesta URL (vzhledem k počátku): /path/

- Část adresy URL, která se používá pro porovnání adres URL, končí těsně před názvem souboru požadovaného datového zdroje. Koncové lomítko lomítka je poslední znak v tomto typu cesty.

- Nahraďte všechny mezery ve vzoru cesty URL %20.

- Každý vzor cesty URL může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička odpovídá posloupnosti jednoho nebo více znaků.

- Zadejte ve vzorku více cest URL tak, že každou z nich vymezte jednou mezerou.

    Například: */sales/ */marketing/

- Specifikace cesty URL může využít [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda se provede porovnání rozlišující malá a velká písmena.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Rozšíření cesty URL

Identifikuje požadavky přípony souboru požadovaného datového zdroje.

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody rozšíření cesty URL.

- **Shody**: Vyžaduje, aby adresa URL požadavku obsahovala příponu souboru, která přesně odpovídá zadanému vzoru.

   Pokud například zadáte "htm", budou datové zdroje "htm" spárovány, ale nikoli "html".  

- **Neshoduje se**: Vyžaduje, aby požadavek adresy URL obsahoval příponu souboru, která neodpovídá zadanému vzoru.

Klíčové informace:

- V poli **Hodnota** zadejte přípony souborů, které se mají shodovat. Nezahrnejte úvodní období; použijte například htm místo .htm.

- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda se provede porovnání rozlišující malá a velká písmena.

- Určete více přípon souborů vymezením každé přípony jednou mezerou. 

    Například: htm html

- Například zadání "htm" odpovídá "htm" prostředky, ale ne "html" prostředky.

#### <a name="sample-scenario"></a>Ukázkový scénář

Následující ukázková konfigurace předpokládá, že tato podmínka shody je splněna, pokud požadavek odpovídá jednomu ze zadaných rozšíření.

Specifikace hodnoty: asp aspx php html

Tato podmínka shody je splněna, když najde adresy URL, které končí následujícími rozšířeními:

- Asp
- Aspx
- .php
- .html,

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Název souboru cesty URL

Identifikuje požadavky podle názvu souboru požadovaného datového zdroje. Pro účely této podmínky shody se název souboru skládá z názvu požadovaného datového zdroje, období a přípony souboru (například index.html).

Možnost **Neodpovídá**/**Does Not Match** podmínkám, za kterých je splněna podmínka shody názvu souboru cesty URL.

- **Shody**: Vyžaduje, aby požadavek obsahoval název souboru v cestě adresy URL, který odpovídá zadanému vzoru.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval název souboru v cestě adresy URL, který neodpovídá zadanému vzoru.

Klíčové informace:

- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda se provede porovnání rozlišující malá a velká písmena.

- Chcete-li určit více přípon souborů, oddělte každou příponu jednou mezerou.

    Příklad: index.htm index.html

- Nahraďte mezery v hodnotě názvu souboru hodnotou %20.

- Hodnota názvu souboru může využívat [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Každý vzor názvu souboru se může například skládat z jedné nebo více hvězdiček (*), kde každá hvězdička odpovídá posloupnosti jednoho nebo více znaků.

- Pokud nejsou zadány zástupné znaky, bude tuto podmínku shody splňovat pouze přesná shoda.

    Například zadání "presentation.ppt" odpovídá datovému zdroji s názvem "presentation.ppt", ale ne s názvem "presentation.pptx".

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literál cesty URL

Porovná cestu url požadavku, včetně názvu souboru, na zadanou hodnotu.

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka literálu cesty URL.

- **Shody**: Vyžaduje, aby požadavek obsahoval cestu URL, která odpovídá zadanému vzoru.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval cestu url, která neodpovídá zadanému vzoru.

Klíčové informace:

- Pomocí **možnosti Relativní k** určení, zda bod porovnání adres URL začíná před nebo za přístupovým bodem obsahu. 

    Pro možnost **Relativní k** jsou k dispozici následující hodnoty:
  - **Kořen**: Označuje, že bod porovnání adres URL začíná bezprostředně za názvem hostitele CDN.

    Například: http:\//wpc.0001. &lt;&gt;/**800001/myorigin/myfolder/index.htm** domény

  - **Původ**: Označuje, že bod porovnání adres URL začíná za přístupovým bodem obsahu (například /000001 nebo /800001/myorigin). Vzhledem \*k tomu, že .azureedge.net CNAME je vytvořen vzhledem k původu adresáře na Verizon CDN hostname ve výchozím nastavení, uživatelé Azure CDN by měl použít hodnotu **Origin.** 

    Příklad: https:\//&lt;koncový&gt;bod .azureedge.net/**myfolder/index.htm**

  Tato adresa URL odkazuje na následující název\/hostitele Verizon CDN: http: /wpc.0001. &lt;doména&gt;/800001/myorigin/**myfolder/index.htm**

- Okrajová adresa URL CNAME je před porovnáním adres URL přepsána na adresu URL CDN.

Například obě následující adresy URL odkazují na stejný datový zdroj a proto mají stejnou cestu url:

- CDN URL:\/http: /wpc.0001. &lt;doména&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL:\//&lt;http: koncový bod&gt;.azureedge.net/path/asset.htm

    Další informace:
    
    - Cesta URL (vzhledem ke kořenu): /800001/CustomerOrigin/path/asset.htm
   
    - Cesta URL (vzhledem k počátku): /path/asset.htm

- Řetězce dotazů v adrese URL jsou ignorovány.
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda se provede porovnání rozlišující malá a velká písmena.
- Hodnota zadaná pro tuto podmínku shody je porovnána s relativní cestou přesného požadavku klienta.

- Chcete-li porovnat všechny požadavky na určitý adresář, použijte [adresář cesty URL](#url-path-directory) nebo podmínku shody se [zástupnými znaky cesty URL.](#url-path-wildcard)

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Regex cesty URL

Porovná cestu url požadavku k zadanému [regulárnímu výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody regex cesty URL.

- **Shody**: Vyžaduje, aby požadavek obsahoval cestu URL, která odpovídá zadanému regulárnímu výrazu.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval cestu url, která neodpovídá zadanému regulárnímu výrazu.

Klíčové informace:

- Okrajová adresa URL CNAME je před porovnáním adres URL přepsána na adresu URL CDN.

    Například obě adresy URL odkazují na stejný datový zdroj a proto mají stejnou cestu url.

     - CDN URL:\/http: /wpc.0001. &lt;doména&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL:\/http: /my.domain.com/path/asset.htm

    Další informace:
    
     - Cesta url: /800001/CustomerOrigin/path/asset.htm

- Řetězce dotazů v adrese URL jsou ignorovány.
    
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda se provede porovnání rozlišující malá a velká písmena.
    
- Mezery v cestě url by měly být nahrazeny %20.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Zástupný znak cesty URL

Porovná relativní cestu URL požadavku k zadanému vzorku se zástupnými kódy.

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody zástupný znak cesty URL.

- **Shody**: Vyžaduje, aby požadavek obsahoval cestu url, která odpovídá zadanému vzoru se zástupnými kódy.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval cestu url, která neodpovídá zadanému vzoru se zástupnými symboly.

Klíčové informace:

- **Vzhledem k možnosti:** Tato možnost určuje, zda bod porovnání adres URL začíná před nebo za přístupovým bodem obsahu.

   Tato možnost může mít následující hodnoty:
     - **Kořen**: Označuje, že bod porovnání adres URL začíná bezprostředně za názvem hostitele CDN.

       Například: http:\//wpc.0001. &lt;&gt;/**800001/myorigin/myfolder/index.htm** domény

     - **Původ**: Označuje, že bod porovnání adres URL začíná za přístupovým bodem obsahu (například /000001 nebo /800001/myorigin). Vzhledem \*k tomu, že .azureedge.net CNAME je vytvořen vzhledem k původu adresáře na Verizon CDN hostname ve výchozím nastavení, uživatelé Azure CDN by měl použít hodnotu **Origin.** 

       Příklad: https:\//&lt;koncový&gt;bod .azureedge.net/**myfolder/index.htm**

     Tato adresa URL odkazuje na následující název\/hostitele Verizon CDN: http: /wpc.0001. &lt;doména&gt;/800001/myorigin/**myfolder/index.htm**

- Okrajová adresa URL CNAME je před porovnáním adres URL přepsána na adresu URL CDN.

    Například obě následující adresy URL odkazují na stejný datový zdroj a proto mají stejnou cestu url:
     - ADRESA URL http://wpc.0001.&ltCDN:&gt;;domain /800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL:\//&lt;http: koncový bod&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    
     - Cesta URL (vzhledem ke kořenu): /800001/CustomerOrigin/path/asset.htm
    
     - Cesta URL (vzhledem k počátku): /path/asset.htm
    
- Zadejte více cest URL tak, že každou z nich vymezte jednou mezerou.

   Příklad: /marketing/asset.* /sales/*.htm

- Řetězce dotazů v adrese URL jsou ignorovány.
    
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete určit, zda se provede porovnání rozlišující malá a velká písmena.
    
- Nahraďte mezery v cestě url %20.
    
- Hodnota zadaná pro cestu URL může využívat [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Každý vzor cesty URL může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička může odpovídat posloupnosti jednoho nebo více znaků.

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Ukázkové konfigurace v následující tabulce předpokládají, že tato podmínka shody je splněna, pokud požadavek odpovídá zadanému vzoru adresy URL:

Hodnota                   | Vzhledem k    | Výsledek 
------------------------|----------------|-------
*/test.html */test.php  | Kořen nebo původ | Tento vzor je doplněn požadavky na prostředky s názvem "test.html" nebo "test.php" v libovolné složce.
/80ABCD/původ/text/*   | Kořenová složka           | Tento vzor je porovnán, když požadovaný datový zdroj splňuje následující kritéria: <br />- Musí se nabývat na původu zákazníka zvaném "původ". <br />- Relativní cesta musí začínat stolicí s názvem "text". To znamená, že požadovaný datový zdroj může být umístěn ve složce "text" nebo v jedné z jeho rekurzivních podsložek.
*/css/* */js/*          | Kořen nebo původ | Tento vzor je porovnán se všemi adresy URL CDN nebo edge CNAME, které obsahují složku css nebo js.
*.jpg *.gif *.png       | Kořen nebo původ | Tento vzor je porovnán se všemi adresy URL CDN nebo edge CNAME končícími na .jpg, .gif nebo .png. Alternativní způsob, jak určit tento vzor je s [podmínkou shody rozšíření cesty URL](#url-path-extension).
/obrázky/* /média/*      | Zdroj         | Tento vzor je porovnán s adresy URL CDN nebo edge CNAME, jejichž relativní cesta začíná složkou "obrázky" nebo "média". <br />- CDN URL:\/http: /wpc.0001. &lt;doména&gt;/800001/myorigin/images/sales/event1.png<br />- Ukázka okraje CNAME\/URL: http: /cdn.mydomain.com/images/sales/event1.png

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Literál dotazu URL

Porovná řetězec dotazu požadavku na zadanou hodnotu.

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka literálu url dotazu.

- **Shody**: Vyžaduje, aby požadavek obsahoval řetězec dotazu URL, který odpovídá zadanému řetězci dotazu.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval řetězec dotazu url, který neodpovídá zadanému řetězci dotazu.

Klíčové informace:

- Tuto podmínku shody splňují pouze přesné shody řetězce dotazu.
    
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen porovnání řetězců dotazu.
    
- Nezahrnejte přední otazník (?) do textu hodnoty řetězce dotazu.
    
- Některé znaky vyžadují kódování adresy URL. Pomocí symbolu procenta zakódujte adresu URL následující znaky:

   Znak | Kódování adresy URL
   ----------|---------
   Místo     | %20
   &         | %25

- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Dokončit výplň mezipaměti
   - Výchozí interní maximální stáří
   - Vynutit vnitřní maximální stáří
   - Ignorovat origin bez mezipaměti
   - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parametr dotazu URL

Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá zadanému vzoru. Parametry řetězce dotazu (například parametr=hodnota) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle jeho názvu a přijímá jednu nebo více hodnot pro hodnotu parametru. 

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody parametr dotazu URL.

- **Shody**: Vyžaduje požadavek na obsahující zadaný parametr s hodnotou, která odpovídá alespoň jedné z hodnot, které jsou definovány v této podmínce shody.
- **Neodpovídá**: Vyžaduje, aby požadavek splňoval některé z následujících kritérií:
  - Neobsahuje zadaný parametr.
  - Obsahuje zadaný parametr, ale jeho hodnota neodpovídá žádné z hodnot, které jsou definovány v této podmínce shody.

Tato podmínka shody poskytuje snadný způsob, jak zadat kombinace názvu/hodnoty parametru. Chcete-li zvýšit flexibilitu, pokud porovnáváte parametr řetězce dotazu, zvažte použití podmínky shody [zástupných symbolů dotazu URL.](#url-query-wildcard)

Klíčové informace:

- Pro každou instanci této podmínky shody lze zadat pouze jeden název parametru dotazu URL.
    
- Vzhledem k tomu, že hodnoty zástupných symbolů nejsou podporovány, pokud je zadán název parametru, lze je porovnat pouze přesnou shodou názvů parametrů.
- Hodnota parametru může obsahovat [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Každý vzor hodnoty parametru se může skládat z jedné nebo více hvězdiček (*), kde každá hvězdička může odpovídat posloupnosti jednoho nebo více znaků.
   - Některé znaky vyžadují kódování adresy URL. Pomocí symbolu procenta zakódujte adresu URL následující znaky:

       Znak | Kódování adresy URL
       ----------|---------
       Místo     | %20
       &         | %25

- Zadejte více hodnot parametrů řetězce dotazu tak, že každou z nich vymezte jednou mezerou. Tato podmínka shody je splněna, pokud požadavek obsahuje jednu ze zadaných kombinací názvu a hodnoty.

   - Příklad 1:

     - Konfigurace:

       ValueA ValueB

     - Tato konfigurace odpovídá následujícím parametrům řetězce dotazu:

       Parametr1=HodnotaA
    
       Parametr1=HodnotaB

   - Příklad 2:

     - Konfigurace: 

        Hodnota%20A Hodnota%20B

     - Tato konfigurace odpovídá následujícím parametrům řetězce dotazu:

       Parametr1=Hodnota%20A

       Parametr1=Hodnota%20B

- Tato podmínka shody je splněna pouze v případě, že existuje přesná shoda alespoň s jednou ze zadaných kombinací názvu řetězce dotazu a hodnoty.

   Pokud například použijete konfiguraci v předchozím příkladu, kombinace názvu/hodnoty parametru "Parameter1=ValueAdd" nebude považována za shodu. Pokud však zadáte některou z následujících hodnot, bude odpovídat této kombinaci názvu a hodnoty:

   - ValueA ValueB ValueAdd
   - Hodnota ValueA* HodnotaB

- Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen porovnání řetězců dotazu.
    
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Dokončit výplň mezipaměti
   - Výchozí interní maximální stáří
   - Vynutit vnitřní maximální stáří
   - Ignorovat origin bez mezipaměti
   - Interní maximální zatuchlý

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující příklad ukazuje, jak tato možnost funguje v konkrétních situacích:

Name (Název)  | Hodnota |  Výsledek
------|-------|--------
Uživatel  | Joe   | Tento vzor je porovnán, když je řetězec dotazu pro požadovanou adresu URL "?user=joe."
Uživatel  | *     | Tento vzor je porovnán, když řetězec dotazu pro požadovanou adresu URL obsahuje parametr User.
E-mail | Joe\* | Tento vzor je porovnán, když řetězec dotazu pro požadovanou adresu URL obsahuje parametr E-mailu, který začíná řetězcem "Joe".

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Regex dotazu URL

Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá zadanému [regulárnímu výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody regex dotazu URL.

- **Shody**: Vyžaduje, aby požadavek obsahoval řetězec dotazu URL, který odpovídá zadanému regulárnímu výrazu.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval řetězec dotazu url, který neodpovídá zadanému regulárnímu výrazu.

Klíčové informace:

- Pouze přesné shody se zadaným regulárním výrazem splňují tuto podmínku shody.
    
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen porovnání řetězců dotazu.
    
- Pro účely této možnosti řetězec dotazu začíná prvním znakem za oddělovačem otazníku (?) pro řetězec dotazu.
    
- Některé znaky vyžadují kódování adresy URL. Pomocí symbolu procenta zakódujte adresu URL následující znaky:

   Znak | Kódování adresy URL | Hodnota
   ----------|--------------|------
   Místo     | %20          | \%20
   &         | %25          | \%25

   Všimněte si, že procento symboly musí být uvozena.

- Speciální znaky regulárních výrazů \^s dvojitým escapem (například $.+) pro zahrnutí zpětného lomítka do regulárního výrazu.

   Například:

   Hodnota | Interpretováno jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Dokončit výplň mezipaměti
   - Výchozí interní maximální stáří
   - Vynutit vnitřní maximální stáří
   - Ignorovat origin bez mezipaměti
   - Interní maximální zatuchlý

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Zástupný znak dotazu URL

Porovná zadané hodnoty s řetězcem dotazu požadavku.

Možnost **Neodpovídá**/**Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody zástupný znak url dotazu.

- **Shody**: Vyžaduje, aby požadavek obsahoval řetězec dotazu URL, který odpovídá zadané hodnotě zástupných symbolů.
- **Neshoduje se**: Vyžaduje, aby požadavek obsahoval řetězec dotazu url, který neodpovídá zadané hodnotě zástupných symbolů.

Klíčové informace:

- Pro účely této možnosti řetězec dotazu začíná prvním znakem za oddělovačem otazníku (?) pro řetězec dotazu.
- Hodnoty parametrů mohou obsahovat [hodnoty zástupných symbolů](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Každý vzor hodnoty parametru se může skládat z jedné nebo více hvězdiček (*), kde každá hvězdička může odpovídat posloupnosti jednoho nebo více znaků.
   - Některé znaky vyžadují kódování adresy URL. Pomocí symbolu procenta zakódujte adresu URL následující znaky:

     Znak | Kódování adresy URL
     ----------|---------
     Místo     | %20
     &         | %25

- Určete více hodnot vymezením každé z nich jednou mezerou.

   Příklad: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Pouze přesné shody alespoň jeden ze zadaných vzorů řetězce dotazu splňují tuto podmínku shody.
    
- Pomocí možnosti **Ignorovat malá a velká písmena** můžete řídit rozlišování malých a velkých písmen porovnání řetězců dotazu.
    
- Vzhledem ke způsobu sledování nastavení mezipaměti je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Dokončit výplň mezipaměti
   - Výchozí interní maximální stáří
   - Vynutit vnitřní maximální stáří
   - Ignorovat origin bez mezipaměti
   - Interní maximální zatuchlý

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující příklad ukazuje, jak tato možnost funguje v konkrétních situacích:

 Name (Název)                 | Popis
 ---------------------|------------
user=joe              | Tento vzor je porovnán, když je řetězec dotazu pro požadovanou adresu URL "?user=joe."
\*uživatel\* \*= výúčast =\* | Tento vzor je spárován, pokud dotaz ADRESY URL CDN obsahuje parametr uživatele nebo optout.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Další kroky

- [Přehled sítě doručování obsahu Azure](cdn-overview.md)
- [Odkaz na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy modulu pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce modulu pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání výchozího chování PROTOKOLU HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
