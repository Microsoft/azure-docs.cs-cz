---
title: Azure CDN z podmínek shody stroje Verizon Premium | Microsoft Docs
description: Referenční dokumentace k Azure Content Delivery Network z podmínek shody stroje Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 3bc439e3244be63bff1c54d3230eda17dfb9d88d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745595"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN z podmínek shody modulu pravidel Verizon Premium

V tomto článku jsou uvedené podrobné popisy dostupných podmínek shody pro Azure Content Delivery Network (CDN) z [modulu pravidel](cdn-verizon-premium-rules-engine.md)Verizon Premium.

Druhá část pravidla je podmínka shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které budou provedeny sady funkcí.

Podmínku shody můžete například použít k těmto akcím:

- Filtrovat požadavky na obsah v určitém umístění.
- Vyfiltruje požadavky vygenerované z konkrétní IP adresy nebo země nebo oblasti.
- Vyfiltruje požadavky podle informací v záhlaví.

## <a name="always-match-condition"></a>Podmínka vždy neshody

Podmínka vždycky se vztahuje na všechny požadavky na výchozí sadu funkcí.

Name | Účel
-----|--------
[Vždy](#always) | Použije výchozí sadu funkcí pro všechny požadavky.

## <a name="device-match-condition"></a>Podmínka shody zařízení

Podmínka shody zařízení identifikuje požadavky vytvořené z mobilního zařízení na základě jeho vlastností.  

Name | Účel
-----|--------
[Zařízení](#device) | Identifikuje požadavky vytvořené z mobilního zařízení na základě jeho vlastností.

## <a name="location-match-conditions"></a>Podmínky shody umístění

Podmínky shody umístění identifikují požadavky na základě umístění žadatele.

Name | Účel
-----|--------
[JAKO číslo](#as-number) | Identifikuje požadavky, které pocházejí z konkrétní sítě.
[Země](#country) | Identifikuje požadavky, které pocházejí ze zadaných zemí nebo oblastí.

## <a name="origin-match-conditions"></a>Původní podmínky shody

Původní podmínky shody identifikují požadavky, které odkazují na Content Delivery Network Storage nebo na server původu zákazníka.

Name | Účel
-----|--------
[Zdroj CDN](#cdn-origin) | Identifikuje požadavky na obsah uložený v Content Delivery Network Storage.
[Původ zákazníka](#customer-origin) | Identifikuje požadavky na obsah uložený na konkrétním zákaznickém serveru.

## <a name="request-match-conditions"></a>Podmínky shody žádostí

Podmínky shody požadavků identifikují požadavky na základě jejich vlastností.

Name | Účel
-----|--------
[Client IP Address](#client-ip-address) | Identifikuje požadavky, které pocházejí z konkrétní IP adresy.
[Parametr souboru cookie](#cookie-parameter) | Kontroluje soubory cookie přidružené ke každé žádosti zadané hodnoty.
[Regulární parametr pro soubor cookie](#cookie-parameter-regex) | Kontroluje soubory cookie přidružené ke každé žádosti pro zadaný regulární výraz.
[Záznam CNAME okraje](#edge-cname) | Identifikuje požadavky, které ukazují na konkrétní hraniční záznam CNAME.
[Odkazující doména](#referring-domain) | Identifikuje požadavky, které se odkazovaly ze zadaných názvů hostitelů.
[Literál hlavičky požadavku](#request-header-literal) | Identifikuje požadavky, které obsahují zadanou hlavičku nastavenou na zadanou hodnotu.
[Regulární výraz pro záhlaví požadavku](#request-header-regex) | Identifikuje požadavky, které obsahují zadanou hlavičku nastavenou na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Zástupný znak hlavičky žádosti](#request-header-wildcard) | Identifikuje požadavky, které obsahují zadanou hlavičku nastavenou na hodnotu, která odpovídá zadanému vzoru.
[Request – metoda](#request-method) | Identifikuje požadavky metodou HTTP.
[Schéma žádosti](#request-scheme) | Identifikuje požadavky podle jejich protokolu HTTP.

## <a name="url-match-conditions"></a>Podmínky shody adresy URL

Podmínky shody adresy URL identifikují požadavky na základě jejich adres URL.

Name | Účel
-----|--------
[Adresář cesty URL](#url-path-directory) | Identifikuje požadavky podle jejich relativní cesty.
[Přípona cesty URL](#url-path-extension) | Identifikuje žádosti podle jejich přípony názvu souboru.
[Název cesty URL](#url-path-filename) | Identifikuje požadavky podle názvu souboru.
[Literál cesty URL](#url-path-literal) | Porovná relativní cestu požadavku s určenou hodnotou.
[Regulární výraz cesty URL](#url-path-regex) | Porovná relativní cestu požadavku k určenému regulárnímu výrazu.
[Zástupný znak cesty adresy URL](#url-path-wildcard) | Porovná relativní cestu žádosti se zadaným vzorem.
[Literál dotazu URL](#url-query-literal) | Porovná řetězec dotazu požadavku se zadanou hodnotou.
[Parametr dotazu adresy URL](#url-query-parameter) | Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu nastaveného na hodnotu, která odpovídá zadanému vzoru.
[Regulární výraz pro dotaz na adresu URL](#url-query-regex) | Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu nastaveného na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Zástupný znak URL pro dotaz](#url-query-wildcard) | Porovná zadanou hodnotu s řetězcem dotazu požadavku.

## <a name="reference-for-rules-engine-match-conditions"></a>Reference pro podmínky shody stroje pravidel

---

### <a name="always"></a>Vždy

Podmínka vždycky se vztahuje na všechny požadavky na výchozí sadu funkcí.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>JAKO číslo

AS Number Network je definována číslem autonomního systému (ASN). 

Shoda **neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka pro shodu čísla as:

- **Shody**: vyžaduje, aby ASN klientské sítě odpovídalo jednomu ze zadaných čísla ASN. 
- **Neodpovídá: vyžaduje**, aby ASN klientské sítě neodpovídá žádnému ze zadaných čísla ASN.

Informace o klíči:

- Určete více čísla ASN tak, že každý z nich omezíte na jedno místo. Například 64514 64515 odpovídá žádostem, které přicházejí buď z 64514 nebo 64515.
- Některé požadavky nemusí vracet platné číslo ASN. Otazník (?) bude odpovídat žádostem, u kterých nelze určit platné ASN.
- Zadejte celé číslo ASN pro požadovanou síť. Částečné hodnoty se neshodují.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Zdroj CDN

Podmínka shody se zdrojem CDN je splněna, pokud jsou splněny obě následující podmínky:

- Byl požadován obsah z úložiště CDN.
- Identifikátor URI požadavku používá typ přístupového bodu obsahu (například/000001), který je definován v této podmínce shody:
  - Adresa URL CDN: identifikátor URI žádosti musí obsahovat vybraný přístupový bod obsahu.
  - Adresa URL CNAME Edge: odpovídající konfigurace hraničního záznamu CNAME musí ukazovat na vybraný přístupový bod obsahu.
  
Informace o klíči:

- Přístupový bod obsahu identifikuje službu, která by měla sloužit k požadovanému obsahu.
- Nepoužívejte příkaz a IF ke kombinování určitých podmínek shody. Například kombinování podmínky shody původce CDN s podmínkou shody původce zákazníka vytvořilo vzor shody, který by nikdy neměl odpovídat. Z tohoto důvodu nelze v rámci příkazu a IF kombinovat dvě podmínky shody týkající se původu CDN.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Client IP Address

Možnost **shody neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody IP adresy klienta:

- **Shody**: vyžaduje, aby IP adresa klienta odpovídala jedné ze zadaných IP adres. 
- **Neodpovídá: vyžaduje**, aby IP adresa klienta neodpovídala žádné ze zadaných IP adres. 

Informace o klíči:

- Použijte notaci CIDR.
- Určete více IP adres nebo bloků IP adres tak, že každý z nich oddělujete jedním mezerou. Například:
  - **Příklad protokolu IPv4**: 1.2.3.4 10.20.30.40 odpovídá všem žádostem, které dorazí buď na adresu 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 odpovídá všem žádostem, které přicházejí buď z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe bloku IP adres je základní IP adresa, za kterou následuje lomítko a velikost předpony. Například:
  - **Příklad IPv4**: 5.5.5.64/26 odpovídá všem žádostem, které přicházejí z adres 5.5.5.64 prostřednictvím 5.5.5.127.
  - **Příklad IPv6**: 1:2:3:/48 odpovídá všem žádostem, které přicházejí z adres 1:2:3:0:0:0:0:0 až 1:2: ffff: ffff: ffff: ffff: ffff: FFFF.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parametr souboru cookie

Možnost **shody neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody parametru souboru cookie.

- **Shody**: vyžaduje, aby požadavek obsahoval zadaný soubor cookie s hodnotou, která odpovídá nejméně jedné z hodnot, které jsou definovány v této podmínce shody.
- **Neodpovídá: vyžaduje**, aby požadavek splňoval jedno z následujících kritérií:
  - Neobsahuje zadaný soubor cookie.
  - Obsahuje zadaný soubor cookie, ale jeho hodnota neodpovídá žádné z hodnot, které jsou definovány v této podmínce shody.
  
Informace o klíči:

- Název souboru cookie:
  - Vzhledem k tomu, že se zadáním názvu souboru cookie nepodporují zástupné hodnoty, včetně hvězdiček (*), jsou vhodné pro porovnání jenom přesnou shodu názvů souborů cookie.
  - Pro každou instanci této podmínky shody lze zadat pouze jeden název souboru cookie.
  - Porovnávání názvů souborů cookie rozlišuje velká a malá písmena.
- Hodnota souboru cookie:
  - Určete více hodnot souborů cookie tak, že každý z nich omezíte na jednu mezeru.
  - Hodnota souboru cookie může využít [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Pokud není zadaná zástupná hodnota, bude tato podmínka shody vyhovovat jenom přesná shoda. Například zadání hodnoty "hodnota" bude odpovídat "value", ale nikoli "hodnota1" nebo "hodnota2".
  - Pomocí možnosti **Ignorovat velikost písmen** můžete určit, jestli se má na základě hodnoty souboru cookie žádosti provést porovnání s rozlišováním velkých a malých písmen.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Regulární parametr pro soubor cookie

Podmínka porovnávání regulárního výrazu v parametru cookie definuje název a hodnotu souboru cookie. Můžete použít [regulární výrazy](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) k definování požadované hodnoty souboru cookie.

Možnost **shody neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka pro parametr souboru cookie Regex.

- **Shody**: vyžaduje, aby požadavek obsahoval zadaný soubor cookie s hodnotou, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá: vyžaduje**, aby požadavek splňoval jedno z následujících kritérií:
  - Neobsahuje zadaný soubor cookie.
  - Obsahuje zadaný soubor cookie, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.
  
Informace o klíči:

- Název souboru cookie:
  - Vzhledem k tomu, že se regulární výrazy a zástupné hodnoty, včetně hvězdičky (*), nepodporují, pokud zadáváte název souboru cookie, jsou vhodné pro porovnání jenom přesnou shodu názvů souborů cookie.
  - Pro každou instanci této podmínky shody lze zadat pouze jeden název souboru cookie.
  - Porovnávání názvů souborů cookie rozlišuje velká a malá písmena.
- Hodnota souboru cookie:
  - Hodnota souboru cookie může využít regulární výrazy.
  - Pomocí možnosti **Ignorovat velikost písmen** můžete určit, jestli se má na základě hodnoty souboru cookie žádosti provést porovnání s rozlišováním velkých a malých písmen.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Země

Zemi můžete zadat pomocí jejího kódu země. 

Možnost **shody neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody země:

- **Shody**: vyžaduje, aby požadavek obsahoval zadané hodnoty kódu země. 
- **Neodpovídá: vyžaduje**, aby požadavek neobsahuje zadané hodnoty kódu země.

Informace o klíči:

- Určete více kódů zemí tím, že každý z nich omezíte na jedno místo.
- Zástupné znaky se nepodporují, pokud zadáváte kód země.
- Kódy zemí EU a AP nezahrnují všechny IP adresy v těchto oblastech.
- Některé požadavky nemusí vracet platný kód země. Otazník (?) bude odpovídat žádostem, u kterých nelze určit platný kód země.
- Kódy zemí rozlišují velká a malá písmena.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementace filtrování země pomocí modulu pravidel

Tato podmínka shody vám umožňuje provádět řadu úprav na základě umístění, ze kterého pochází požadavek. Například chování funkce filtrování země lze replikovat prostřednictvím následující konfigurace:

- Shoda se zástupnými znaky cesty URL: nastavte [podmínku shody zástupných znaků cesty URL](#url-path-wildcard) do adresáře, který bude zabezpečený. 
    Připojí hvězdičku ke konci relativní cesty, aby se zajistilo, že bude mít přístup ke všem jeho podřízeným položkám omezení toto pravidlo.

- Shoda země: nastavte podmínku shody země na požadovanou sadu zemí nebo oblastí.
  - Allow: nastavte podmínku shody země na, že se **neshoduje** s tím, že umožňuje přístup k obsahu uloženému v umístění, které je definováno podmínkou pro porovnávání zástupných znaků cesty adresy URL.
  - Blok: nastavte podmínku shody země na **shody** , aby zablokovaly zadané země nebo oblasti přístup k obsahu uloženému v umístění, které je definováno podmínkou pro zástupné znaky cesty adresy URL.

- Funkce odepření přístupu (403): povolení [funkce odepřít přístup (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) pro replikaci části povolení nebo blokování funkce filtrování země.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Původ zákazníka

Informace o klíči:

- Podmínka shody původce zákazníka je splněná bez ohledu na to, jestli je obsah požadován prostřednictvím adresy URL CDN nebo adresy URL hraničního CNAME, který odkazuje na vybraný původ zákazníka.
- Konfiguraci původu zákazníka, na kterou odkazuje pravidlo, nejde odstranit ze stránky zdroje zákazníka. Předtím, než se pokusíte odstranit konfiguraci původu zákazníka, se ujistěte, že tyto konfigurace neodkazují na tyto:
  - Podmínka shody původce zákazníka
  - Konfigurace CNAME Edge
- Nepoužívejte příkaz a IF ke kombinování určitých podmínek shody. Například kombinace podmínky shody původce zákazníka s podmínkou shody se zdrojem CDN by vytvořila vzor shody, který by nikdy neměl odpovídat. Z tohoto důvodu nelze v rámci příkazu AND IF kombinovat dvě podmínky shody původce zákazníka.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Zařízení

Podmínka shody zařízení identifikuje požadavky vytvořené z mobilního zařízení na základě jeho vlastností. Zjišťování mobilních zařízení se dosahuje prostřednictvím [WURFL](http://wurfl.sourceforge.net/). 

Možnost **shody neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody zařízení:

- **Shody**: vyžaduje, aby zařízení žadatele odpovídalo zadané hodnotě. 
- **Neodpovídá: vyžaduje**, aby zařízení žadatele neodpovídalo zadané hodnotě.

Informace o klíči:

- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda zadaná hodnota rozlišuje velká a malá písmena.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

#### <a name="string-type"></a>Typ řetězce

WURFL schopnost obvykle přijímá libovolnou kombinaci čísel, písmen a symbolů. Z důvodu flexibilní povahy této schopnosti je nutné zvolit způsob, jakým je interpretována hodnota přidružená k této podmínce shody. V následující tabulce jsou popsány dostupné sady možností:

Typ     | Popis
---------|------------
Doslovný  | Tuto možnost vyberte, pokud chcete, aby většina znaků nebrala na zvláštní význam pomocí jejich [literálové hodnoty](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Použity | Tuto možnost vyberte, pokud chcete využít všechny [zástupné znaky] ([zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)).
Regulární    | Tuto možnost vyberte, pokud chcete použít [regulární výrazy](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regulární výrazy jsou užitečné pro definování vzoru znaků.

#### <a name="wurfl-capabilities"></a>Možnosti WURFL

WURFL schopnost odkazuje na kategorii, která popisuje mobilní zařízení. Vybraná schopnost určuje typ popisu mobilního zařízení, který se používá k identifikaci požadavků.

V následující tabulce jsou uvedeny možnosti WURFL a jejich proměnné pro modul pravidel.

> [!NOTE]
> Následující proměnné jsou podporovány v **hlavičce upravit žádost klienta** a upravit funkce **hlaviček odpovědi klienta** .

Schopnost | Proměnná | Popis | Ukázkové hodnoty
-----------|----------|-------------|----------------
Název značky | % {wurfl_cap_brand_name} | Řetězec, který označuje název značky zařízení. | Samsung
Operační systém zařízení | % {wurfl_cap_device_os} | Řetězec, který indikuje, že je operační systém nainstalovaný na zařízení. | iOS
Verze operačního systému zařízení | % {wurfl_cap_device_os_version} | Řetězec, který označuje číslo verze operačního systému nainstalovaného v zařízení. | 1.0.1
Dvojitá orientace | % {wurfl_cap_dual_orientation} | Logická hodnota, která označuje, jestli zařízení podporuje dvojí orientaci. | true
Preferovaná deklarace DTD HTML | % {wurfl_cap_html_preferred_dtd} | Řetězec, který označuje upřednostňovanou definici typu dokumentu (DTD) mobilního zařízení pro obsah HTML. | žádné<br/>xhtml_basic<br/>HTML5
Vkládání obrázků | % {wurfl_cap_image_inlining} | Logická hodnota, která označuje, zda zařízení podporuje obrázky kódované v kódování Base64. | false (nepravda)
Je Android | % {wurfl_vcap_is_android} | Logická hodnota, která označuje, jestli zařízení používá operační systém Android. | true
Je IOS | % {wurfl_vcap_is_ios} | Logická hodnota, která indikuje, jestli zařízení používá iOS. | false (nepravda)
Je inteligentní TV | % {wurfl_cap_is_smarttv} | Logická hodnota, která indikuje, jestli je zařízení inteligentní TV. | false (nepravda)
Je smartphone | % {wurfl_vcap_is_smartphone} | Logická hodnota, která indikuje, jestli je zařízení smartphone. | true
Je tablet | % {wurfl_cap_is_tablet} | Logická hodnota, která označuje, zda je zařízení tablet. Tento popis je nezávislý na operačním systému. | true
Je bezdrátové zařízení | % {wurfl_cap_is_wireless_device} | Logická hodnota, která indikuje, jestli se zařízení považuje za bezdrátové zařízení. | true
Název marketingu | % {wurfl_cap_marketing_name} | Řetězec, který označuje název marketingu zařízení. | BlackBerry 8100 Pearl
Mobilní prohlížeč | % {wurfl_cap_mobile_browser} | Řetězec, který označuje prohlížeč, který se používá k vyžádání obsahu ze zařízení. | Chrome
Verze mobilního prohlížeče | % {wurfl_cap_mobile_browser_version} | Řetězec, který označuje verzi prohlížeče, která se používá k vyžádání obsahu ze zařízení. | 31
Název modelu | % {wurfl_cap_model_name} | Řetězec, který označuje název modelu zařízení. | stavu
Progresivní stahování | % {wurfl_cap_progressive_download} | Logická hodnota, která označuje, jestli zařízení podporuje přehrávání zvuku a videa, když se pořád stahuje. | true
Datum vydání | % {wurfl_cap_release_date} | Řetězec, který označuje rok a měsíc, kdy bylo zařízení přidáno do databáze WURFL.<br/><br/>Formátovat`yyyy_mm` | 2013_december
Výška rozlišení | % {wurfl_cap_resolution_height} | Celé číslo, které označuje výšku zařízení v pixelech. | 768
Rozlišení šířky | % {wurfl_cap_resolution_width} | Celé číslo, které označuje šířku zařízení v pixelech. | 1024

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Záznam CNAME okraje

Informace o klíči:

- Seznam dostupných záznamů o hraničních ZÁZNAMech je omezen na okraje CNAME, které byly nakonfigurovány na stránce hraničních záznamů CNAME pro platformu, na které je nakonfigurován modul pravidel.
- Předtím, než se pokusíte odstranit konfiguraci hraničního záznamu CNAME, se ujistěte, že podmínka pro splnění hraničních záznamů CNAME na ni neodkazuje. Konfigurace hraničních záznamů CNAME, které jsou definované v pravidle, se nedají odstranit ze stránky hraničních záznamů CNAME.
- Nepoužívejte příkaz a IF ke kombinování určitých podmínek shody. Například kombinování podmínky záznamu CNAME u okraje s podmínkou shody původce zákazníka vytvoří vzor shody, který by nikdy neměl odpovídat. Z tohoto důvodu není možné v rámci příkazu a IF kombinovat dvě hraniční podmínky CNAME.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Odkazující doména

Název hostitele přidružený k odkazujícímu serveru, prostřednictvím kterého byl požadován obsah, určuje, zda je splněna podmínka odkazující domény.

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna odkazovaná podmínka domény:

- **Shody**: vyžaduje odkazující název hostitele, aby odpovídal zadaným hodnotám. 
- **Neodpovídá: vyžaduje**, aby odkazující název hostitele neodpovídá zadané hodnotě.

Informace o klíči:

- Určete více názvů hostitelů tím, že každý z nich omezíte na jednu mezeru.
- Tato podmínka shody podporuje [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Pokud zadaná hodnota neobsahuje hvězdičku, musí být pro název hostitele odkazující přesnou shodu. Například zadání "mydomain.com" by se neshodovalo s "www.mydomain.com".
- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, jestli se má provést porovnání s rozlišováním velkých a malých písmen.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literál hlavičky požadavku

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka shody s literálem hlavičky požadavku.

- **Shody**: vyžaduje, aby požadavek obsahoval určenou hlavičku. Jeho hodnota musí odpovídat hodnotě, která je definována v této podmínce shody.
- **Neodpovídá: vyžaduje**, aby požadavek splňoval jedno z následujících kritérií:
  - Neobsahuje zadané záhlaví.
  - Obsahuje určenou hlavičku, ale její hodnota neodpovídá hodnotě, která je definována v této podmínce shody.
  
Informace o klíči:

- Při porovnávání názvů hlaviček se nerozlišují malá a velká písmena. Pomocí možnosti **Ignorovat velikost** písmen můžete řídit rozlišování velkých a malých písmen v porovnání hodnot hlaviček.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Regulární výraz pro záhlaví požadavku

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka záhlaví žádosti o porovnání regulárního výrazu.

- **Shody**: vyžaduje, aby požadavek obsahoval určenou hlavičku. Jeho hodnota musí odpovídat vzoru, který je definován v zadaném [regulárním výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Neodpovídá: vyžaduje**, aby požadavek splňoval jedno z následujících kritérií:
  - Neobsahuje zadané záhlaví.
  - Obsahuje určenou hlavičku, ale její hodnota neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:

- Název záhlaví:
  - Porovnávání názvů hlaviček rozlišuje velká a malá písmena.
  - V názvu záhlaví nahraďte mezery řetězcem "%20".
- Hodnota hlavičky:
  - Hodnota záhlaví může využít regulární výrazy.
  - Pomocí možnosti **Ignorovat velikost** písmen můžete řídit rozlišování velkých a malých písmen v porovnání hodnot hlaviček.
  - Podmínka shody je splněna pouze v případě, že hodnota hlavičky přesně odpovídá alespoň jedné ze zadaných vzorů.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Zástupný znak hlavičky žádosti

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka shody se zástupnými znaky v hlavičce požadavku.

- **Shody**: vyžaduje, aby požadavek obsahoval určenou hlavičku. Jeho hodnota musí odpovídat alespoň jedné z hodnot, které jsou definovány v této podmínce shody.
- **Neodpovídá: vyžaduje**, aby požadavek splňoval jedno z následujících kritérií:
  - Neobsahuje zadané záhlaví.
  - Obsahuje určenou hlavičku, ale její hodnota neodpovídá žádné ze zadaných hodnot.
  
Informace o klíči:

- Název záhlaví:
  - Porovnávání názvů hlaviček rozlišuje velká a malá písmena.
  - Mezery v názvu hlavičky by měly být nahrazené řetězcem "%20". Tuto hodnotu můžete také použít k určení mezer v hodnotě hlavičky.
- Hodnota hlavičky:
  - Hodnota hlavičky může využívat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Pomocí možnosti **Ignorovat velikost** písmen můžete řídit rozlišování velkých a malých písmen v porovnání hodnot hlaviček.
  - Tato podmínka shody je splněna, pokud hodnota hlavičky přesně odpovídá alespoň jednomu ze zadaných vzorů.
  - Určete více hodnot tak, že každý z nich omezíte na jednu mezeru.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Request – metoda

Podmínka shody metody žádosti je splněna pouze v případě, že jsou na prostředky vyžádány prostřednictvím vybrané metody žádosti. K dispozici jsou tyto metody žádosti:

- GET
- HEAD
- POST
- NASTAVENÍ
- PUT
- DELETE
- TRACE
- SÍTI

Informace o klíči:

- Ve výchozím nastavení může obsah uložený v mezipaměti v síti generovat jenom metoda GET Request. Všechny ostatní metody žádosti jsou proxy prostřednictvím sítě.
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Schéma žádosti

Podmínka shody schématu žádosti je splněna pouze v případě, že jsou na prostředky vyžádány prostřednictvím vybraného protokolu. Dostupné protokoly jsou:

- HTTP
- HTTPS

Informace o klíči:

- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
  - Vyplnit celou mezipaměť
  - Výchozí interní maximální stáří
  - Vynutit interní maximum – stáří
  - Ignorovat původ – mezipaměť
  - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Adresář cesty URL

Identifikuje požadavek podle jeho relativní cesty, která vylučuje název souboru požadovaného prostředku.

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka shody adresáře cesty adresy URL.

- **Shody**: vyžaduje, aby žádost obsahovala relativní cestu URL s výjimkou názvu souboru, která odpovídá zadanému vzoru adresy URL.
- **Neodpovídá: vyžaduje**, aby žádost obsahovala relativní cestu URL s vyloučením názvu souboru, která neodpovídá zadanému vzoru adresy URL.

Informace o klíči:

- Pomocí možnosti **relativní k** určete, zda se porovnání adresy URL začíná před nebo za přístupovým bodem obsahu. Přístupový bod obsahu je část cesty, která se zobrazí mezi názvem hostitele CDN Verizon a relativní cestou k požadovanému prostředku (například/800001/CustomerOrigin). Identifikuje umístění podle typu serveru (například CDN nebo původ zákazníka) a čísla účtu zákazníka.

   Pro možnost **relativně k** dispozici jsou následující hodnoty:
  - **Root**: označuje, zda se bod porovnání adresy URL začíná přímo za názvem hostitele CDN. 

  Například: http: \/ /WPC.0001. &lt; Doména &gt; / **800001/myorigin/myFolder**/index.htm

  - **Počátek**: označuje, že se bod porovnání adresy URL začíná za přístupovým bodem obsahu (například/000001 nebo/800001/myorigin). Vzhledem k tomu, že se ve \* výchozím nastavení vytvoří název CNAME. azureedge.NET relativní ke zdrojovému adresáři Verizon CDN, Azure CDN uživatelé by měli použít **počáteční** hodnotu. 

  Příklad: https: \/ / &lt; Endpoint &gt; . azureedge.NET/**myFolder**/index.htm 

  Tato adresa URL odkazuje na následující název hostitele CDN Verizon: http: \/ /WPC.0001. &lt; Doména &gt; /800001/myorigin/**myFolder**/index.htm

- Adresa URL hraničního CNAME se před porovnáním adresy URL přepíše na adresu URL CDN.

    Například obě následující adresy URL ukazují na stejný Asset a mají proto stejnou cestu URL.
  - Adresa URL CDN: http: \/ /WPC.0001. &lt; Doména &gt; /800001/CustomerOrigin/Path/Asset.htm
    
  - Adresa URL CNAME Edge: http: \/ / &lt; Endpoint &gt; . azureedge.NET/Path/Asset.htm
    
    Další informace:
  - Vlastní doména: https: \/ /My.domain.com/Path/Asset.htm
    
    - Cesta URL (relativní ke kořenu):/800001/CustomerOrigin/path/
    
    - Cesta URL (relativní ke zdroji):/path/

- Část adresy URL, která se používá pro porovnání adresy URL, končí těsně před názvem souboru požadovaného prostředku. Koncové lomítko je poslední znak v tomto typu cesty.

- Nahraďte všechny mezery v cestě URL vzoru pomocí "%20".

- Každý vzor cesty URL může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička odpovídá sekvenci jednoho nebo více znaků.

- Určete více cest adresy URL ve vzoru tak, že každý z nich oddělujete jedním mezerou.

    Příklad: */Sales/*/marketing/

- Specifikace cesty URL může využívat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda je prováděno porovnání s rozlišováním velkých a malých písmen.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Přípona cesty URL

Identifikuje požadavky podle přípony souboru požadovaného prostředku.

Shoda **neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody přípony cesty URL.

- **Shody**: vyžaduje, aby adresa URL požadavku obsahovala příponu souboru, která přesně odpovídá zadanému vzoru.

   Pokud například zadáte "htm", "htm" assets, ale ne "HTML" assets.  

- **Neodpovídá: vyžaduje**, aby požadavek URL obsahoval příponu souboru, která se neshoduje se zadaným vzorem.

Informace o klíči:

- V poli **hodnota** zadejte přípony souborů, které mají být shodné. Nezahrnovat úvodní období; použijte například htm namísto formátu. htm.

- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda je prováděno porovnání s rozlišováním velkých a malých písmen.

- Určete více přípon souborů tak, že jednotlivá rozšíření oddělujete o jednu mezeru. 

    Příklad: htm HTML

- Například zadání "htm" odpovídá prostředkům "htm", ale nikoli prostředkům "HTML".

#### <a name="sample-scenario"></a>Ukázkový scénář

Následující vzorová konfigurace předpokládá, že se tato podmínka shody splní, když požadavek odpovídá některé ze zadaných rozšíření.

Specifikace hodnoty: ASP HTML aspx php

Tato podmínka shody je splněna, pokud nalezne adresy URL, které končí následujícími příponami:

- . ASP
- . aspx
- . php
- .html,

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Název cesty URL

Identifikuje požadavky podle názvu souboru požadovaného prostředku. Pro účely této podmínky shody se název souboru skládá z názvu požadovaného prostředku, tečky a přípony souboru (například index. html).

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka adresy URL názvu cesty.

- **Shody**: vyžaduje, aby žádost obsahovala název souboru v cestě URL, která odpovídá zadanému vzoru.
- **Neodpovídá: vyžaduje**, aby požadavek obsahoval název souboru v cestě URL, která se neshoduje se zadaným vzorem.

Informace o klíči:

- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda je prováděno porovnání s rozlišováním velkých a malých písmen.

- Chcete-li zadat více přípon souborů, oddělte jednotlivá rozšíření o jednu mezeru.

    Příklad: index. htm index. html

- Nahraďte mezery v názvu souboru hodnotou %20.

- Hodnota názvu souboru může využít [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Například každý vzor názvu souboru může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička odpovídá sekvenci jednoho nebo více znaků.

- Pokud nejsou zadány zástupné znaky, bude tato podmínka shody splněna pouze přesná shoda.

    Například zadání "Presentation. ppt" odpovídá prostředku s názvem Presentation. ppt, ale ne s názvem "Presentation. pptx".

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literál cesty URL

Porovná cestu URL požadavku, včetně názvu souboru, s určenou hodnotou.

Shoda **neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody cesty URL.

- **Shody**: vyžaduje, aby požadavek OBSAHOVAL cestu URL, která odpovídá zadanému vzoru.
- **Neodpovídá: vyžaduje**, aby požadavek OBSAHOVAL cestu URL, která se neshoduje se zadaným vzorem.

Informace o klíči:

- Pomocí možnosti **relativní k** určete, zda se má srovnávací bod URL začínat před nebo za přístupovým bodem obsahu. 

    Pro možnost **relativně k** dispozici jsou následující hodnoty:
  - **Root**: označuje, zda se bod porovnání adresy URL začíná přímo za názvem hostitele CDN.

    Například: http: \/ /WPC.0001. &lt; &gt; / **800001/myorigin/myFolder/index.htm** domény

  - **Počátek**: označuje, že se bod porovnání adresy URL začíná za přístupovým bodem obsahu (například/000001 nebo/800001/myorigin). Vzhledem k tomu, že se ve \* výchozím nastavení vytvoří název CNAME. azureedge.NET relativní ke zdrojovému adresáři Verizon CDN, Azure CDN uživatelé by měli použít **počáteční** hodnotu. 

    Příklad: https: \/ / &lt; Endpoint &gt; . azureedge.NET/**myFolder/index.htm**

  Tato adresa URL odkazuje na následující název hostitele CDN Verizon: http: \/ /WPC.0001. &lt; &gt;**myFolder/index.htm** domény/800001/myorigin/

- Adresa URL hraničního CNAME se před porovnáním adresy URL přepíše na adresu URL CDN.

Například obě následující adresy URL ukazují na stejný Asset a mají proto stejnou cestu adresy URL:

- Adresa URL CDN: http: \/ /WPC.0001. &lt; Doména &gt; /800001/CustomerOrigin/Path/Asset.htm
- Adresa URL CNAME Edge: http: \/ / &lt; Endpoint &gt; . azureedge.NET/Path/Asset.htm

    Další informace:
    
    - Cesta URL (relativní ke kořenu):/800001/CustomerOrigin/path/asset.htm
   
    - Cesta URL (relativní ke zdroji):/path/asset.htm

- Řetězce dotazů v adrese URL jsou ignorovány.
- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda je prováděno porovnání s rozlišováním velkých a malých písmen.
- Hodnota zadaná pro tuto podmínku shody je porovnána s relativní cestou k přesnému požadavku provedenému klientem.

- Pro vyhledání všech požadavků provedených do konkrétního adresáře použijte [adresu URL adresáře](#url-path-directory) nebo podmínku shody se [zástupnými znaky cesty adresy URL](#url-path-wildcard) .

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Regulární výraz cesty URL

Porovná cestu URL požadavku k určenému [regulárnímu výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Shoda **neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka cesty URL regulárního výrazu.

- **Shody**: vyžaduje, aby požadavek OBSAHOVAL cestu URL, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá: vyžaduje**, aby požadavek OBSAHOVAL cestu URL, která neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:

- Adresa URL hraničního CNAME se před porovnáním adresy URL přepíše na adresu URL CDN.

    Například obě adresy URL ukazují na stejný Asset a mají proto stejnou cestu URL.

     - Adresa URL CDN: http: \/ /WPC.0001. &lt; Doména &gt; /800001/CustomerOrigin/Path/Asset.htm

     - Adresa URL CNAME Edge: http: \/ /My.domain.com/Path/Asset.htm

    Další informace:
    
     - Cesta URL:/800001/CustomerOrigin/path/asset.htm

- Řetězce dotazů v adrese URL jsou ignorovány.
    
- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda je prováděno porovnání s rozlišováním velkých a malých písmen.
    
- Mezery v cestě URL by měly být nahrazené řetězcem "%20".

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Zástupný znak cesty adresy URL

Porovná relativní cestu URL požadavku se zadaným zástupným vzorem.

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka shody mezi zástupnými znaky cesty URL.

- **Shody**: vyžaduje, aby požadavek OBSAHOVAL cestu URL, která odpovídá zadanému zástupnému vzoru.
- **Neodpovídá: vyžaduje**, aby požadavek OBSAHOVAL cestu URL, která neodpovídá zadanému vzoru zástupných znaků.

Informace o klíči:

- **Relativní k** možnosti: Tato možnost určuje, zda se má srovnávací bod URL začínat před nebo za přístupovým bodem obsahu.

   Tato možnost může mít následující hodnoty:
     - **Root**: označuje, zda se bod porovnání adresy URL začíná přímo za názvem hostitele CDN.

       Například: http: \/ /WPC.0001. &lt; &gt; / **800001/myorigin/myFolder/index.htm** domény

     - **Počátek**: označuje, že se bod porovnání adresy URL začíná za přístupovým bodem obsahu (například/000001 nebo/800001/myorigin). Vzhledem k tomu, že se ve \* výchozím nastavení vytvoří název CNAME. azureedge.NET relativní ke zdrojovému adresáři Verizon CDN, Azure CDN uživatelé by měli použít **počáteční** hodnotu. 

       Příklad: https: \/ / &lt; Endpoint &gt; . azureedge.NET/**myFolder/index.htm**

     Tato adresa URL odkazuje na následující název hostitele CDN Verizon: http: \/ /WPC.0001. &lt; &gt;**myFolder/index.htm** domény/800001/myorigin/

- Adresa URL hraničního CNAME se před porovnáním adresy URL přepíše na adresu URL CDN.

    Například obě následující adresy URL ukazují na stejný Asset a mají proto stejnou cestu adresy URL:
     - Adresa URL CDN: http://wpc.0001.&lt ;d omain &gt; /800001/CustomerOrigin/Path/Asset.htm
     - Adresa URL CNAME Edge: http: \/ / &lt; Endpoint &gt; . azureedge.NET/Path/Asset.htm
    
    Další informace:
    
     - Cesta URL (relativní ke kořenu):/800001/CustomerOrigin/path/asset.htm
    
     - Cesta URL (relativní ke zdroji):/path/asset.htm
    
- Určete více cest URL tak, že každý z nich oddělujete na jedno místo.

   Příklad:/marketing/Asset. */Sales/*. htm

- Řetězce dotazů v adrese URL jsou ignorovány.
    
- Pomocí možnosti **Ignorovat velikost písmen** můžete určit, zda je prováděno porovnání s rozlišováním velkých a malých písmen.
    
- Nahraďte mezery v cestě URL řetězcem "%20".
    
- Hodnota zadaná pro cestu URL může využívat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Každý vzor cesty URL může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička může odpovídat sekvenci jednoho nebo více znaků.

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Vzorové konfigurace v následující tabulce předpokládají, že se tato podmínka shody splní, když požadavek odpovídá zadanému vzoru adresy URL:

Hodnota                   | Relativní vzhledem k    | Výsledek 
------------------------|----------------|-------
*/test.html */test.php  | Kořen nebo původ | Tento model se shoduje s požadavky na prostředky s názvem test. html nebo test. php v jakékoli složce.
/80ABCD/origin/text/*   | Kořenová složka           | Tento model se spáruje, když požadovaný prostředek splňuje následující kritéria: <br />– Musí se nacházet na zdroji zákazníka s názvem "Origin". <br />-Relativní cesta musí začínat složkou s názvem "text". To znamená, že požadovaný prostředek může být umístěn ve složce "text" nebo v jedné z jeho rekurzivních podsložek.
*/CSS/* */js/*          | Kořen nebo původ | Tento model odpovídá všem adresám URL CDN nebo hraničním záznamům CNAME, které obsahují složku CSS nebo JS.
*. jpg *. gif *. png       | Kořen nebo původ | Tento model odpovídá všem adresám URL CDN nebo hraničních záznamů CNAME končícím na. jpg,. gif nebo. png. Alternativním způsobem, jak tento model zadat, je [Podmínka shody rozšíření cesty URL](#url-path-extension).
/images/* /media/*      | Zdroj         | Tento model odpovídá adresám CNAME CDN nebo Edge, jejichž relativní cesta začíná složkou "image" nebo "médium". <br />-CDN adresa URL: http: \/ /WPC.0001. &lt; &gt;/800001/myorigin/images/Sales/event1.png domény<br />-Ukázka adresa URL CNAME hraničního okraje: http: \/ /CDN.mydomain.com/images/Sales/event1.png

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Literál dotazu URL

Porovná řetězec dotazu požadavku se zadanou hodnotou.

Možnost **shody neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka shody literálu dotazu URL.

- **Shody**: vyžaduje, aby žádost obsahovala řetězec dotazu adresy URL, který odpovídá zadanému řetězci dotazu.
- **Neodpovídá: vyžaduje**, aby požadavek obsahoval řetězec dotazu adresy URL, který se neshoduje se zadaným řetězcem dotazu.

Informace o klíči:

- Pouze přesné shody řetězce dotazu splňují tuto podmínku shody.
    
- Použití možnosti **Ignorovat velikost** písmen pro řízení citlivosti pro porovnávání řetězců dotazů.
    
- Do textu hodnoty řetězce dotazu nezahrnujte úvodní otazník (?).
    
- Některé znaky vyžadují kódování adresy URL. Použijte symbol procenta na adresu URL pro kódování následujících znaků:

   Znak | Kódování adresy URL
   ----------|---------
   Místo     | %20
   &         | %25

- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Vyplnit celou mezipaměť
   - Výchozí interní maximální stáří
   - Vynutit interní maximum – stáří
   - Ignorovat původ – mezipaměť
   - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parametr dotazu adresy URL

Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá zadanému vzoru. Parametry řetězce dotazu (například parametr = hodnota) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle názvu a přijímá jednu nebo více hodnot pro hodnotu parametru. 

Shoda **neodpovídá** / **Does Not Match** parametru určuje podmínky, za kterých je splněna podmínka dotazu adresy URL.

- **Shody**: vyžaduje, aby požadavek obsahoval zadaný parametr s hodnotou, která odpovídá nejméně jedné z hodnot, které jsou definovány v této podmínce shody.
- **Neodpovídá: vyžaduje**, aby požadavek splňoval jedno z následujících kritérií:
  - Neobsahuje zadaný parametr.
  - Obsahuje zadaný parametr, ale jeho hodnota se neshoduje s žádnou z hodnot, které jsou definovány v této podmínce shody.

Tato podmínka shody poskytuje snadný způsob, jak zadat kombinace názvů a hodnot parametrů. Pro větší flexibilitu, pokud odpovídáte parametru řetězce dotazu, zvažte použití podmínky shody se [zástupnými znaky pro dotaz na adresu URL](#url-query-wildcard) .

Informace o klíči:

- Pro každou instanci této podmínky shody lze zadat pouze jeden název parametru dotazu adresy URL.
    
- Vzhledem k tomu, že se hodnoty zástupných znaků nejsou podporované, když je zadaný název parametru, je pro porovnání vhodné jenom přesné názvy parametrů.
- Hodnoty parametrů můžou obsahovat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Každý vzor hodnoty parametru se může skládat z jedné nebo více hvězdiček (*), kde každá hvězdička může odpovídat sekvenci jednoho nebo více znaků.
   - Některé znaky vyžadují kódování adresy URL. Použijte symbol procenta na adresu URL pro kódování následujících znaků:

       Znak | Kódování adresy URL
       ----------|---------
       Místo     | %20
       &         | %25

- Určete více hodnot parametrů řetězce dotazu tak, že každý z nich omezíte na jednu mezeru. Tato podmínka shody je splněna, pokud požadavek obsahuje jednu z určených kombinací název/hodnota.

   - Příklad 1:

     - Konfigurace:

       Hodnota Hodnotab

     - Tato konfigurace odpovídá následujícím parametrům řetězce dotazu:

       Parametr1 = hodnota
    
       Parametr1 = Hodnotab

   - Příklad 2:

     - Konfigurace: 

        Hodnota% 20A hodnoty% s

     - Tato konfigurace odpovídá následujícím parametrům řetězce dotazu:

       Parametr1 = hodnota% 20A

       Parametr1 = hodnota%

- Tato podmínka shody je splněná jenom v případě, že existuje přesná shoda s aspoň jednou zadanou kombinací názvu a hodnoty řetězce dotazu.

   Pokud například použijete konfiguraci v předchozím příkladu, kombinace název parametru/hodnota "parametr1 = ValueAdd" by se nepovažovala za shodu. Pokud však zadáte některou z následujících hodnot, bude odpovídat kombinaci názvu/hodnoty:

   - Hodnota Hodnotab ValueAdd
   - Hodnota ' * Hodnotab

- Použití možnosti **Ignorovat velikost** písmen pro řízení citlivosti pro porovnávání řetězců dotazů.
    
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Vyplnit celou mezipaměť
   - Výchozí interní maximální stáří
   - Vynutit interní maximum – stáří
   - Ignorovat původ – mezipaměť
   - Interní Max – zastaralé

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující příklad ukazuje, jak tato možnost funguje v určitých situacích:

Name  | Hodnota |  Výsledek
------|-------|--------
Uživatel  | Joe   | Tento model odpovídá, pokud je řetězec dotazu pro požadovanou adresu URL "? User = Jan".
Uživatel  | *     | Tento model odpovídá, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr uživatele.
E-mailu | Joe\* | Tento model se spáruje, když řetězec dotazu pro požadovanou adresu URL obsahuje parametr e-mailu, který začíná na Jana.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Regulární výraz pro dotaz na adresu URL

Identifikuje požadavky, které obsahují zadaný parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá zadanému [regulárnímu výrazu](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Shoda **neodpovídá** / **Does Not Match** možnosti určuje podmínky, za kterých je splněna podmínka dotazu URL regulárního výrazu.

- **Shody**: vyžaduje, aby žádost obsahovala řetězec dotazu adresy URL, který odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá: vyžaduje**, aby požadavek obsahoval řetězec dotazu adresy URL, který neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:

- Tuto podmínku shody splňují jenom přesné shody se zadaným regulárním výrazem.
    
- Použití možnosti **Ignorovat velikost** písmen pro řízení citlivosti pro porovnávání řetězců dotazů.
    
- Pro účely této možnosti začíná řetězec dotazu prvním znakem po otazníku (?) pro řetězec dotazu.
    
- Některé znaky vyžadují kódování adresy URL. Použijte symbol procenta na adresu URL pro kódování následujících znaků:

   Znak | Kódování adresy URL | Hodnota
   ----------|--------------|------
   Místo     | %20          | \%20o
   &         | %25          | \%0,25

   Všimněte si, že symboly procentuálních hodnot musí být uvozeny řídicími znaky.

- Speciální znaky regulárního výrazu dvojitého Escape (například \^ $. +) pro vložení zpětného lomítka do regulárního výrazu.

   Například:

   Hodnota | Interpretováno jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Vyplnit celou mezipaměť
   - Výchozí interní maximální stáří
   - Vynutit interní maximum – stáří
   - Ignorovat původ – mezipaměť
   - Interní Max – zastaralé

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Zástupný znak URL pro dotaz

Porovná zadané hodnoty s řetězcem dotazu žádosti.

Možnost **shody** / **neodpovídá** možnosti určuje podmínky, za kterých je splněna podmínka shody zástupného znaku dotazu URL.

- **Shody**: vyžaduje, aby žádost obsahovala řetězec dotazu adresy URL, který odpovídá zadané zástupné hodnotě.
- **Neodpovídá: vyžaduje**, aby požadavek obsahoval řetězec dotazu adresy URL, který neodpovídá zadané zástupné hodnotě.

Informace o klíči:

- Pro účely této možnosti začíná řetězec dotazu prvním znakem po otazníku (?) pro řetězec dotazu.
- Hodnoty parametrů můžou zahrnovat [zástupné hodnoty](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Každý vzor hodnoty parametru se může skládat z jedné nebo více hvězdiček (*), kde každá hvězdička může odpovídat sekvenci jednoho nebo více znaků.
   - Některé znaky vyžadují kódování adresy URL. Použijte symbol procenta na adresu URL pro kódování následujících znaků:

     Znak | Kódování adresy URL
     ----------|---------
     Místo     | %20
     &         | %25

- Určete více hodnot tak, že každý z nich omezíte na jednu mezeru.

   Příklad: *parametr1 = valuea* *Hodnotab* *parametr1 = ValueC&parametr2 = vracející* se

- Tuto podmínku shody splňují jenom přesné shody alespoň s jedním ze zadaných vzorů řetězce dotazu.
    
- Použití možnosti **Ignorovat velikost** písmen pro řízení citlivosti pro porovnávání řetězců dotazů.
    
- Kvůli způsobu, jakým jsou sledována nastavení mezipaměti, je tato podmínka shody nekompatibilní s následujícími funkcemi:
   - Vyplnit celou mezipaměť
   - Výchozí interní maximální stáří
   - Vynutit interní maximum – stáří
   - Ignorovat původ – mezipaměť
   - Interní Max – zastaralé

#### <a name="sample-scenarios"></a>Ukázkové scénáře

Následující příklad ukazuje, jak tato možnost funguje v určitých situacích:

 Name                 | Popis
 ---------------------|------------
uživatel = Jan              | Tento model odpovídá, pokud je řetězec dotazu pro požadovanou adresu URL "? User = Jan".
\*Uživatel = \* \* OptOut =\* | Tento model se spáruje, když dotaz na adresu URL CDN obsahuje buď parametr User nebo OptOut.

[Zpět na začátek](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Další kroky

- [Přehled služby Azure Content Delivery Network](cdn-overview.md)
- [Odkazy na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání výchozího chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
