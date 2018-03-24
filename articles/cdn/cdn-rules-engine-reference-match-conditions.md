---
title: Azure CDN pravidla podmínky shody modul | Microsoft Docs
description: Referenční dokumentace pro Azure Content Delivery Network pravidla shody stav motoru.
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
ms.openlocfilehash: f8dac5469e7160fae93e8251ab7f4195a383f8b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Stroj pravidel Azure CDN splňují podmínky 
Tento článek obsahuje seznam podrobný popis dostupných porovnání podmínky pro Content Delivery Network (CDN) Azure [stroj pravidel](cdn-rules-engine.md).

Druhá část pravidla je podmínka shodu. Stav shody identifikuje konkrétní typy žádostí, pro které se provede sadu funkcí.

Například můžete použít podmínku shodu pro:
- Filtr požadavků na obsah v konkrétních místech.
- Filtrovat žádosti vygenerované z konkrétní IP adresu nebo zemi.
- Filtrovat žádosti podle informace hlavičky.

## <a name="always-match-condition"></a>Vždy odpovídat podmínky

Stav shody vždy výchozí sadu funkcí, platí pro všechny požadavky.

Název | Účel
-----|--------
[Vždy](#always) | Výchozí sadu funkcí, se vztahuje na všechny požadavky.

## <a name="device-match-condition"></a>Stav shody zařízení

Stav shody zařízení identifikuje požadavky z mobilního zařízení podle jeho vlastnosti.  

Název | Účel
-----|--------
[Zařízení](#device) | Identifikuje požadavky z mobilního zařízení podle jeho vlastnosti.

## <a name="location-match-conditions"></a>Podmínky shody umístění

Podmínky shody umístění identifikaci požadavků podle umístění žadatele.

Název | Účel
-----|--------
[JAKO počet](#as-number) | Určuje požadavky, které pocházejí z konkrétní sítě.
[Země](#country) | Určuje požadavky, které pocházejí z zadaný zemích.

## <a name="origin-match-conditions"></a>Podmínky shody počátek

Podmínky shody počátek určete požadavky, které odkazují na Content Delivery Network úložiště nebo na původním serveru zákazníka.

Název | Účel
-----|--------
[Původu CDN](#cdn-origin) | Identifikuje požadavky na obsah uložený v síti pro doručování obsahu úložiště.
[Původ zákazníka](#customer-origin) | Identifikuje požadavky na obsah uložený na původním serveru konkrétního zákazníka.

## <a name="request-match-conditions"></a>Podmínky žádosti o shody

Porovnání podmínky žádosti o identifikaci požadavků na základě jejich vlastností.

Název | Účel
-----|--------
[IP adresa klienta](#client-ip-address) | Určuje požadavky, které pocházejí z konkrétní IP adresu.
[Parametr souboru cookie](#cookie-parameter) | Kontroluje soubory cookie související s každou žádostí pro zadanou hodnotu.
[Soubor cookie parametr Regex](#cookie-parameter-regex) | Kontroluje soubory cookie související s každou žádostí pro určený regulární výraz.
[Hraniční Cname](#edge-cname) | Určuje požadavky, které odkazují na konkrétní edge CNAME.
[Odkazující domény](#referring-domain) | Určuje požadavky, které byly uvedené z názvů zadaného hostitele.
[Literál hlavičky požadavku](#request-header-literal) | Určuje požadavky, které obsahují zadaná hlavička nastavit na zadanou hodnotu.
[Regex hlavičky požadavku](#request-header-regex) | Určuje požadavky, které obsahují zadaná hlavička nastavena na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Zástupný znak hlavičky požadavku](#request-header-wildcard) | Určuje požadavky, které obsahují zadaná hlavička nastavena na hodnotu, která odpovídá zadanému vzoru.
[Request – metoda](#request-method) | Identifikuje požadavky jejich metodou HTTP.
[Schéma požadavku](#request-scheme) | Identifikuje požadavků podle jejich protokolu HTTP.

## <a name="url-match-conditions"></a>Podmínky pro shodu adresy URL

Podmínky shodu adresy URL identifikaci požadavků podle jejich adresy URL.

Název | Účel
-----|--------
[Adresa URL cesta adresáře](#url-path-directory) | Identifikuje požadavků podle jejich relativní cestu.
[Rozšíření cesty adresy URL](#url-path-extension) | Identifikuje požadavků podle přípony názvu souboru.
[Název souboru cestu adresy URL](#url-path-filename) | Identifikuje požadavky podle názvu souboru.
[Literál cestu adresy URL](#url-path-literal) | Porovná relativní cestu požadavku se zadanou hodnotou.
[Regulární výraz cesty adresy URL](#url-path-regex) | Porovná požadavek na relativní cestu k zadanému regulárnímu výrazu.
[Cesta URL zástupný znak](#url-path-wildcard) | Porovná požadavek na relativní cestu k zadanému vzoru.
[Adresa URL dotazu literál](#url-query-literal) | Porovná řetězec dotazu požadavku se zadanou hodnotou.
[Parametr URL dotazu](#url-query-parameter) | Určuje požadavky, které obsahují parametru řetězce dotazu zadaná nastavena na hodnotu, která odpovídá zadanému vzoru.
[Adresa URL dotazu Regex](#url-query-regex) | Určuje požadavky, které obsahují parametru řetězce dotazu zadaná nastavena na hodnotu, která odpovídá zadanému regulárnímu výrazu.
[Adresa URL dotazu zástupný znak](#url-query-wildcard) | Porovná zadanou hodnotu na řetězec dotazu žádosti.


## <a name="reference-for-rules-engine-match-conditions"></a>Referenční dokumentace pro podmínky shody stroj pravidel

---
### <a name="always"></a>Vždy

Stav shody vždy výchozí sadu funkcí, platí pro všechny požadavky.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>JAKO počet 
Číslo AS sítě je definována pomocí čísla autonomního systému (ASN). 

**Odpovídá**/**neodpovídá** možnost určuje podmínky, za kterých číslo AS vyhovují podmínce splníte:
- **Odpovídá**: vyžaduje se, že číslo ASN sítě klienta odpovídá jednomu z zadaného čísla ASN. 
- **Nemá neshodují**: vyžaduje se, že číslo ASN klienta sítě neodpovídá žádnému zadaného čísla ASN.

Informace o klíči:
- Zadejte čísla ASN více omezující každé z nich mezerou. Například 64514 64515 odpovídá požadavků, doručení z 64514 nebo 64515.
- Některé žádosti nemusí vracet platné číslo ASN. Otazník (?) bude shodovat s požadavky, u kterých nebylo možné určit platné číslo ASN.
- Zadejte celé číslo ASN pro požadovanou síť. Částečné hodnoty nebude odpovídat.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>Původu CDN
Je splněna podmínka původu CDN shody, pokud jsou splněny obě následující podmínky:
- Bylo vyžadováno obsah z CDN úložiště.
- Identifikátoru URI požadavku používá typ bodu přístup k obsahu (například /000001), který je definován v tomto stavu shody:
  - Adresa URL CDN: Identifikátoru URI požadavku musí obsahovat bodem vybrané přístup k obsahu.
  - Adresa URL CNAME Edge: Odpovídající konfiguraci hraniční CNAME musí odkazovat bodem vybrané přístup k obsahu.
  
Informace o klíči:
 - Přístup k obsahu bodu identifikuje služba, která by měla sloužit požadovaný obsah.
 - Nepoužívejte příkazu a v případě kombinovat určité podmínky shody. Kombinování podmínku původu CDN shodu s podmínkou shodu zákazníka původu by například vytvořit vzor pro shodu, která může být nikdy shoduje. Z tohoto důvodu nelze kombinovat dvě podmínky shody původu CDN prostřednictvím příkazu a v případě.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>IP adresa klienta
**Odpovídá**/**neodpovídá** možnost určuje podmínky, za kterých IP adresu klienta vyhovují podmínce splníte:
- **Odpovídá**: vyžaduje se, že IP adresa klienta odpovídá některé ze zadaných IP adres. 
- **Nemá neshodují**: vyžaduje se, že IP adresa klienta neodpovídá žádnému ze zadaných IP adres. 

Informace o klíči:
- Pomocí notace CIDR.
- Zadejte více IP adres nebo bloky IP adres podle omezující každé z nich mezerou. Příklad:
  - **Příklad IPv4**: 1.2.3.4 10.20.30.40 odpovídá všechny žádosti, které přicházejí z adresy 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 odpovídá všechny požadavky, které přicházejí z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe pro blok IP adres je základní adresu IP a lomítkem a velikost předponu. Příklad:
  - **Příklad IPv4**: 5.5.5.64/26 odpovídá všechny požadavky, které přicházejí z adresy 5.5.5.64 prostřednictvím 5.5.5.127.
  - **Příklad IPv6**: 1:2:3: / 48 odpovídá všechny požadavky, které přicházejí z adresy 1:2:3:0:0:0:0:0 prostřednictvím 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Parametr souboru cookie
**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých parametr souboru Cookie vyhovují podmínce.
- **Odpovídá**: vyžaduje žádost obsahuje zadaný soubor cookie s hodnotu, která odpovídá alespoň jeden z hodnoty, které jsou definované v tomto stavu shody.
- **Neodpovídá**: vyžaduje se, že splňuje požadavek některý z následujících kritérií:
  - Zadaný soubor cookie neobsahuje.
  - Obsahuje zadaný soubor cookie, ale jeho hodnota neodpovídá žádnému z hodnot, které jsou definované v tomto stavu shody.
  
Informace o klíči:
- Název souboru cookie: 
  - Protože zástupné hodnoty, včetně hvězdičky (*), nejsou podporované, když zadáváte název souboru cookie, jsou způsobilé pro porovnání pouze název odpovídá přesný souboru cookie.
  - Jeden instanci tento stav shody lze zadat pouze název jednoho souboru cookie.
  - Porovnání název souboru cookie nerozlišují malá a velká písmena.
- Hodnota souboru cookie: 
  - Zadejte více hodnot souboru cookie omezující každé z nich mezerou.
  - Hodnota souboru cookie můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values). 
  - Pokud nebyl zadán hodnotu zástupný znak, pouze v případě přesné shody budou splňovat tato podmínka shodu. Například zadání "Value" bude shodovat s "Value", ale ne "Hodnota1" nebo "Value2."
  - Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí před hodnotu souboru cookie žádosti.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Soubor cookie parametr Regex
Stav shody Regex parametr souboru Cookie definuje název souboru cookie a hodnotu. Můžete použít [regulární výrazy](cdn-rules-engine-reference.md#regular-expressions) zadat hodnoty požadované souboru cookie. 

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých Regex parametr souboru Cookie vyhovují podmínce.
- **Odpovídá**: vyžaduje žádost obsahuje zadaný soubor cookie s hodnotou, která odpovídá zadanému regulárnímu výrazu.
- **Neodpovídá**: vyžaduje se, že splňuje požadavek některý z následujících kritérií:
  - Zadaný soubor cookie neobsahuje.
  - Obsahuje zadaný soubor cookie, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.
  
Informace o klíči:
- Název souboru cookie: 
  - Protože regulární výrazy a zástupné hodnoty, včetně hvězdičky (*), nejsou podporované, když zadáváte název souboru cookie, jsou způsobilé pro porovnání pouze název odpovídá přesný souboru cookie.
  - Jeden instanci tento stav shody lze zadat pouze název jednoho souboru cookie.
  - Porovnání název souboru cookie nerozlišují malá a velká písmena.
- Hodnota souboru cookie: 
  - Hodnota souboru cookie můžete využít výhod regulární výrazy.
  - Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí před hodnotu souboru cookie žádosti.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Země
Můžete zadat země přes jeho kód země. 

**Odpovídá**/**neodpovídá** možnost určuje podmínky, za kterých země vyhovují podmínce splníte:
- **Odpovídá**: vyžaduje požadavek obsahovat hodnoty zadané země kódu. 
- **Neodpovídá**: vyžaduje, že žádost neobsahuje hodnoty zadané země kódu.

Informace o klíči:
- Zadejte více kódy zemí omezující každé z nich mezerou.
- Pokud zadáváte kód země, nejsou podporovány zástupné znaky.
- Kódy zemí "EU" a "Asie" není zahrnovat všechny IP adresy v těchto oblastech.
- Některé žádosti nemusí vracet platný kód země. Otazník (?) bude shodovat s požadavky, u kterých nebylo možné určit platný kód země.
- Kódy zemí rozlišují velká a malá písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementace filtrování podle země pomocí stroj pravidel
Tento stav shody umožňuje provádět velkého množství přizpůsobení v závislosti na umístění, ze které žádost pochází. Chování funkce filtrování podle země například je možné replikovat pomocí následující konfigurace:

- Cesta URL zástupný znak shodu: nastavte [cesta URL zástupný znak vyhovují podmínce](#url-path-wildcard) do adresáře, který nebude zabezpečené. 
    Připojte hvězdičku na konec relativní cesta k zajištění, bude možné tímto pravidlem omezený přístup ke všem jeho podřízených položek.

- Shoda země: podmínku země shodu na požadovanou sadu zemích.
   - Povolit: Nastavte země podmínku, která má odpovídat **neodpovídá** pro povolení přístupu zadaný zemích jenom na obsah uložený v umístění definované podmínky shody cesta URL zástupný znak.
   - Blokování: Nastavte země odpovídat podmínku, která má **odpovídá** blokování zadaný zemí v přístupu k obsahu uloženého v umístění definované podmínky shody cesta URL zástupný znak.

- Funkce odepření přístupu (403): Povolení [funkce odepření přístupu (403)](cdn-rules-engine-reference-features.md#deny-access-403) k replikaci povolit nebo blokovat část funkci filtrování podle země.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Původ zákazníka

Informace o klíči: 
- Bez ohledu na to, zda je požadovaný obsah prostřednictvím adresy URL CDN nebo okraj CNAME adresa URL, která odkazuje na počátek vybraného zákazníka. je splněna podmínka shodu počátek zákazníka.
- Konfigurace počátečního zákazníka, která odkazuje pravidlo nelze odstranit ze strany zákazníka původu. Před pokusem o odstranění konfigurace počátek zákazníka, ujistěte se, že následující konfigurace neodkazujte ho:
  - Shoda podmínku počátek zákazníka
  - Konfigurace aplikace edge CNAME
- Nepoužívejte příkazu a v případě kombinovat určité podmínky shody. Kombinování podmínku zákazníka počátek shodu s podmínkou shodu původu CDN by například vytvořit vzor pro shodu, která může být nikdy shoduje. Z tohoto důvodu nelze kombinovat dvě podmínky shody počátek zákazníka prostřednictvím příkazu a v případě.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Zařízení

Stav shody zařízení identifikuje požadavky z mobilního zařízení podle jeho vlastnosti. Mobilní zařízení jsou detekovány prostřednictvím [WURFL](http://wurfl.sourceforge.net/). 

**Odpovídá**/**neodpovídá** možnost určuje podmínky, za kterých zařízení vyhovují podmínce splníte:
- **Odpovídá**: vyžaduje žadatele zařízení tak, aby odpovídaly zadanou hodnotu. 
- **Nemá neshodují**: vyžaduje, aby žadatele zařízení se neshoduje se zadanou hodnotou.

Informace o klíči:

- Použití **ignorovat případ** můžete určit, zda zadaná hodnota je malá a velká písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

#### <a name="string-type"></a>String – typ
Funkce WURFL obvykle přijme libovolnou kombinaci číslic, písmena a symboly. Z důvodu flexibilní povaha této funkci musíte zvolit, jak interpretovat hodnotu přidruženou k tomuto stavu shody. Následující tabulka popisuje dostupná sada možností:

Typ     | Popis
---------|------------
Literál  | Vyberte tuto možnost, chcete-li zabránit většinu znaků s ohledem na zvláštní význam pomocí jejich [literálovou hodnotou](cdn-rules-engine-reference.md#literal-values).
Zástupný znak | Vyberte tuto možnost, pokud chcete využít výhod všech [zástupné znaky] ([zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
regulární výraz    | Vyberte tuto možnost používat [regulární výrazy](cdn-rules-engine-reference.md#regular-expressions). Regulární výrazy jsou užitečné pro definování vzorec znaků.

#### <a name="wurfl-capabilities"></a>Možnosti WURFL
Funkce WURFL odkazuje na kategorii, která popisuje mobilní zařízení. Vybranou možnost určuje typ popisu mobilních zařízení, která se používá k identifikaci požadavků.

Následující tabulka uvádí možnosti WURFL a jejich proměnné pro stroj pravidel.
<br>
> [!NOTE] 
> V jsou podporovány následující proměnné **změnit hlavičky žádosti klienta** a **upravit hlavičku odpovědi klienta** funkce.

Schopnost | Proměnná | Popis | Ukázkové hodnoty
-----------|----------|-------------|----------------
Název značky | %{wurfl_cap_brand_name} | Řetězec, který označuje brand název zařízení. | Samsung
Operačního systému zařízení | %{wurfl_cap_device_os} | Řetězec, který označuje operační systém nainstalovaný na zařízení. | iOS
Verze operačního systému zařízení | %{wurfl_cap_device_os_version} | Řetězec, který označuje číslo verze operačního systému na zařízení nainstalovaná. | 1.0.1
Duální orientace | %{wurfl_cap_dual_orientation} | Logická hodnota, která určuje, zda je zařízení podporuje dva orientace. | true (pravda)
Upřednostňovaný souboru DTD protokolu HTML | %{wurfl_cap_html_preferred_dtd} | Řetězec, který označuje definice typu upřednostňované dokumentu (DTD) mobilních zařízení pro obsah HTML. | Žádné<br/>xhtml_basic<br/>html5
Vložené bitové kopie | %{wurfl_cap_image_inlining} | Logická hodnota, která určuje, zda je zařízení podporuje Base64 kódovaný bitové kopie. | false (nepravda)
Is Android | %{wurfl_vcap_is_android} | Logická hodnota, která označuje, zda zařízení používá operační systém Android. | true (pravda)
IOS | %{wurfl_vcap_is_ios} | Logická hodnota, která označuje, zda zařízení používá iOS. | false (nepravda)
Is Smart TV | %{wurfl_cap_is_smarttv} | Logická hodnota, která určuje, zda zařízení je inteligentní televize. | false (nepravda)
Je Smartphone | %{wurfl_vcap_is_smartphone} | Logická hodnota, která určuje, zda je zařízení smartphone. | true (pravda)
Je Tablet | %{wurfl_cap_is_tablet} | Logická hodnota, která určuje, zda je zařízení tablet. Tento popis je nezávislý na operačního systému. | true (pravda)
Je bezdrátových zařízení | %{wurfl_cap_is_wireless_device} | Logická hodnota, která určuje, jestli zařízení je považováno za bezdrátových zařízení. | true (pravda)
Název marketing | %{wurfl_cap_marketing_name} | Řetězec, který označuje marketing název zařízení. | BlackBerry 8100 Pearl
Prohlížeč pro mobilní zařízení | %{wurfl_cap_mobile_browser} | Řetězec, který označuje prohlížeče, který slouží k vyžádání obsahu ze zařízení. | Chrome
Verze mobilní prohlížeče | %{wurfl_cap_mobile_browser_version} | Řetězec, který určuje verzi prohlížeče, který slouží k vyžádání obsahu ze zařízení. | 31
Název modelu | %{wurfl_cap_model_name} | Řetězec určující název modelu zařízení. | s3
Progresivní stahování | %{wurfl_cap_progressive_download} | Logická hodnota, která určuje, zda zařízení podporuje přehrávání zvuku a videa, zatímco stále probíhá stahování. | true (pravda)
Datum vydání | %{wurfl_cap_release_date} | Řetězec, který označuje za rok a měsíc přidání zařízení WURFL databáze.<br/><br/>Formát: `yyyy_mm` | 2013_december
Výška řešení | %{wurfl_cap_resolution_height} | Celé číslo, které určuje výšku zařízení v pixelech. | 768
Šířka řešení | %{wurfl_cap_resolution_width} | Celé číslo, které označuje zařízení šířku v pixelech. | 1024

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Hraniční Cname
Informace o klíči: 
- Seznam dostupných edge záznamů CNAME je omezen na tyto hraniční záznamů CNAME, které byly nakonfigurovány na stránce Edge záznamů CNAME pro platformu, na kterém je konfigurován stroj pravidel.
- Před dalším pokusem o odstranění konfigurace aplikace edge CNAME, ujistěte se, že podmínku Edge Cname shoda se neodkazuje. Konfigurace Edge CNAME, které byly definovány v pravidle nelze odstranit ze stránky záznamů CNAME okraj. 
- Nepoužívejte příkazu a v případě kombinovat určité podmínky shody. Kombinování podmínku Edge Cname shodu s podmínkou shodu zákazníka původu by například vytvořit vzor pro shodu, která může být nikdy shoduje. Z tohoto důvodu nelze kombinovat dvě podmínky shody Edge Cname prostřednictvím příkazu a v případě.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Odkazující domény
Název hostitele přidružené odkazující server, pomocí kterého byl požadován obsah, určuje, zda je splněna podmínka odkazující domény. 

**Odpovídá**/**neodpovídá** možnost určuje podmínky, za kterých odkazující domény vyhovují podmínce splníte:
- **Odpovídá**: vyžaduje odkazující název hostitele tak, aby odpovídaly zadané hodnoty. 
- **Nemá neshodují**: vyžaduje, aby odkazující název hostitele se neshoduje se zadanou hodnotou.

Informace o klíči:
- Zadejte více názvů hostitelů omezující každé z nich mezerou.
- Tento stav shody podporuje [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
- Pokud zadaná hodnota neobsahuje znak hvězdičky, musí být přesná shoda pro název hostitele odkazující server. Například zadání "domena.cz" nepovede ke shodě "www.mydomain.com."
- Použití **ignorovat případ** možnost na ovládací prvek, zda je provedeno porovnání malá a velká písmena.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Literál hlavičky požadavku
**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých je žádosti hlavičky literálu vyhovují podmínce.
- **Odpovídá**: vyžaduje obsahovat Zadaná hlavička požadavku. Její hodnota musí odpovídat ten, který je definován v tomto stavu shody.
- **Neodpovídá**: vyžaduje se, že splňuje požadavek některý z následujících kritérií:
  - Zadaná hlavička neobsahuje.
  - Zadaná hlavička obsahuje, ale jeho hodnota neodpovídá ten, který je definován v tomto stavu shody.
  
Informace o klíči:
- Porovnání název hlavičky jsou vždy velká a malá písmena. Použití **ignorovat případ** možnost řídit rozlišování porovnání hodnota hlavičky.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Regex hlavičky požadavku
**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých Regex hlavičky požadavku vyhovují podmínce.
- **Odpovídá**: vyžaduje obsahovat Zadaná hlavička požadavku. Její hodnota musí odpovídat vzorku, který je definován v zadané [regulární výraz](cdn-rules-engine-reference.md#regular-expressions).
- **Neodpovídá**: vyžaduje se, že splňuje požadavek některý z následujících kritérií:
  - Zadaná hlavička neobsahuje.
  - Zadaná hlavička obsahuje, ale jeho hodnota neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:
- Název hlavičky: 
  - Porovnání název hlavičky jsou velká a malá písmena.
  - Nahraďte v názvu záhlaví mezery "% 20." 
- Hodnota hlavičky: 
  - Hodnota hlavičky můžete využít výhod regulární výrazy.
  - Použití **ignorovat případ** možnost řídit rozlišování porovnání hodnota hlavičky.
  - Pouze v případě hodnotu hlavičky přesně odpovídá alespoň jedním ze zadaného vzorů je splněna podmínka shodu.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé 

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Zástupný znak hlavičky požadavku
**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých zástupný znak hlavičky požadavku vyhovují podmínce.
- **Odpovídá**: vyžaduje obsahovat Zadaná hlavička požadavku. Její hodnota musí odpovídat aspoň jednu z hodnot, které jsou definované v tomto stavu shody.
- **Neodpovídá**: vyžaduje se, že splňuje požadavek některý z následujících kritérií:
  - Zadaná hlavička neobsahuje.
  - Zadaná hlavička obsahuje, ale jeho hodnota neodpovídá žádnému ze zadaných hodnot.
  
Informace o klíči:
- Název hlavičky: 
  - Porovnání název hlavičky jsou velká a malá písmena.
  - V názvu záhlaví mezery, měl by být nahrazen "% 20." Tuto hodnotu můžete taky zadat prostory v hodnotu hlavičky.
- Hodnota hlavičky: 
  - Hodnota hlavičky můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
  - Použití **ignorovat případ** možnost řídit rozlišování porovnání hodnota hlavičky.
  - Pokud hodnotu hlavičky přesně odpovídá do alespoň jedné zadané vzory splnění této podmínky shody.
  - Zadejte více hodnot omezující každé z nich mezerou.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Request – metoda
Jenom v případě, že prostředky jsou požadovány prostřednictvím metody vybrané požadavku je splněna podmínka shodu metoda žádosti. K dispozici požadavek metody jsou následující:
- GET
- HEAD 
- POST 
- MOŽNOSTI 
- PUT 
- DELETE 
- TRASOVÁNÍ 
- PŘIPOJENÍ 

Informace o klíči:
- Ve výchozím nastavení může generovat jenom metody GET žádost o obsah uložený v mezipaměti v síti. Všechny ostatní metody žádosti jsou směrovány přes proxy server v síti.
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Schéma požadavku
Jenom v případě, že jsou prostředky požadované prostřednictvím vybraný protokol je splněna podmínka shodu schéma požadavku. K dispozici protokoly jsou: 
- HTTP
- HTTPS

Informace o klíči:
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
  - Dokončení výplně mezipaměti
  - Výchozí interní Max-Age
  - Vynutit interní Max-Age
  - Ignorovat počátek No-Cache
  - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>Adresa URL cesta adresáře
Určuje požadavek pomocí relativní cesty, které vylučují názvu souboru požadovaného prostředku.

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých adresáři cesta URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavky tak, aby obsahovala relativní cestu adresy URL, s výjimkou názvu souboru, který odpovídá zadanému vzoru adresy URL.
- **Nemá neshodují**: vyžaduje požadavky tak, aby obsahovala relativní cestu adresy URL, s výjimkou název souboru, který neodpovídá zadanému vzoru adresy URL.

Informace o klíči:
- Použití **relativně k** můžete určit, zda je výsledkem porovnávání URL spustí před nebo po bodem přístup k obsahu. Přístup k obsahu bod je část cesty, která se zobrazí mezi hostitele Verizon CDN a relativní cestu pro požadovaný prostředek (například /800001/CustomerOrigin). Určuje umístění typ serveru (například původu CDN nebo zákazníka) a číslo svého účtu zákazníka.

   Jsou k dispozici pro tyto hodnoty **relativně k** možnost:
   - **Kořenové**: Určuje, že bod porovnání adresa URL začíná přímo po hostitele CDN. 

     Příklad: http:\//wpc.0001.&lt; domény&gt;/**800001/myorigin/Moje_složka**/index.htm

   - **Původ**: Určuje, že bod porovnání URL zahájí po bodem přístup k obsahu (například /000001 nebo/800001/myorigin). Protože \*. azureedge.net CNAME se vytvoří relativně ke zdroji adresáře na hostitele Verizon CDN ve výchozím nastavení, Azure CDN uživatelé by měli použít **původu** hodnotu. 

     Příklad: https:\//&lt;koncový bod&gt;.azureedge.net/**Moje_složka**/index.htm 

     Tato adresa URL odkazuje na následující hostitele Verizon CDN: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/**Moje_složka**/index.htm

- Okraj CNAME adresa URL je přepsaná na adresu URL CDN před porovnání adresy URL.

    Například obě následující adresy URL bodu pro stejný prostředek a proto mít stejnou cestu adresy URL.
    - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
    - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - Cestu adresy URL (relativní vůči kořenovému adresáři): / 800001/CustomerOrigin/cesta /
    
    - Cestu adresy URL (relativní vůči původ): /path/

- Část adresy URL, která se používá k porovnání skončení URL těsně před název souboru požadovaného prostředku. Koncové lomítko je poslední znak v tomto typu cesty.
    
- Nahraďte všechny mezery v vzor adresy URL cesty "% 20."
    
- Každou adresu URL vzorek cesty může obsahovat jeden nebo více hvězdičky (*), kde každý hvězdičky odpovídá pořadí jednoho nebo více znaků.
    
- Zadejte více cest URL ve vzoru omezující každé z nich mezerou.

    Příklad: * /sales/ * /marketing/

- Specifikaci cesty adresy URL můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).

- Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>Rozšíření cesty adresy URL
Identifikuje požadavků podle příponu souboru požadovaného prostředku.

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých rozšíření cesty adresy URL vyhovují podmínce.
- **Odpovídá**: vyžaduje adresu URL požadavku tak, aby obsahovala příponu souboru, který přesně odpovídá zadanému vzoru.

   Například pokud zadáte "htm", "htm" prostředky jsou odpovídající, ale ne "html" prostředky.  

- **Nemá neshodují**: vyžaduje požadavek adresy URL obsahuje příponu souboru, který neodpovídá zadanému vzoru.

Informace o klíči:
- Zadejte přípony souborů tak, aby odpovídaly v **hodnotu** pole. Nebudou obsahovat úvodní tečku; například použijte htm místo .htm.

- Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí.

- Zadejte více přípon souborů omezující každé rozšíření mezerou. 

    Příklad: htm, html

- Například zadání "htm" odpovídá "htm" prostředky, ale ne "html" prostředky.


#### <a name="sample-scenario"></a>Vzorový scénář

Následující ukázková konfigurace předpokládá, pokud požadavek odpovídá jednomu z zadaným rozšířením splnění této podmínky shody.

Hodnota specifikace: asp aspx php html

Splnění této podmínky shody při nalezení adresy URL, které končí s těmito příponami:
- .asp
- .aspx
- .php
- .html

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>Název souboru cestu adresy URL
Identifikuje požadavky podle názvu souboru požadovaného prostředku. Pro účely tento stav shody název souboru se skládá z názvu požadované asset, dobou a přípona souboru (například index.html).

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých Filename cesta URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat název souboru v cestě adresy URL, která odpovídá zadanému vzoru.
- **Nemá neshodují**: vyžaduje požadavek obsahovat název souboru v cestě adresy URL, která neodpovídá zadanému vzoru.

Informace o klíči:
- Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí.

- Pokud chcete zadat více přípon souborů, oddělte jednotlivá rozšíření a jedna mezera.

    Příklad: index.htm index.html

- Nahraďte prostory hodnota názvu souboru "% 20."
    
- Hodnota názvu souboru můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values). Například každý vzor názvů souborů se může skládat z jedné nebo více hvězdičky (*), kde každý hvězdičky odpovídá pořadí jednoho nebo více znaků.
    
- Pokud nejsou zadané zástupné znaky, budou pouze přesnou shodu splňovat tato podmínka shodu.

    Například zadání "presentation.ppt" odpovídá prostředek s názvem "presentation.ppt", ale není jednu s názvem "prezentace.pptx."

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>Literál cestu adresy URL
Porovná cesta URL požadavku, včetně názvu souboru se zadanou hodnotou.

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých literálu cesta URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat cestu adresy URL, která odpovídá zadanému vzoru.
- **Nemá neshodují**: vyžaduje požadavek obsahovat cestu adresy URL, která neodpovídá zadanému vzoru.

Informace o klíči:
- Použití **relativně k** můžete určit, zda je adresa URL porovnání bod začne před nebo po bodem přístup k obsahu. 

    Jsou k dispozici pro tyto hodnoty **relativně k** možnost:
     - **Kořenové**: Určuje, že bod porovnání adresa URL začíná přímo po hostitele CDN.

       Příklad: http:\//wpc.0001.&lt; domény&gt;/**800001/myorigin/myfolder/index.htm**

     - **Původ**: Určuje, že bod porovnání URL zahájí po bodem přístup k obsahu (například /000001 nebo/800001/myorigin). Protože \*. azureedge.net CNAME se vytvoří relativně ke zdroji adresáře na hostitele Verizon CDN ve výchozím nastavení, Azure CDN uživatelé by měli použít **původu** hodnotu. 

       Příklad: https:\//&lt;koncový bod&gt;.azureedge.net/**myfolder/index.htm**

     Tato adresa URL odkazuje na následující hostitele Verizon CDN: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/**myfolder/index.htm**

- Okraj CNAME adresa URL je přepsaná na adresu URL CDN před porovnání adresy URL.

    Například obě následující adresy URL bodu pro stejný prostředek a proto mají stejnou cestu adresy URL:
    - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    
    - Cestu adresy URL (relativní vůči kořenovému adresáři): /800001/CustomerOrigin/path/asset.htm
   
    - Cestu adresy URL (relativní vůči původ): /path/asset.htm

- Řetězce dotazů v adrese URL se ignorují.
- Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí.
- Hodnota zadaná pro tuto podmínku shoda se porovná s relativní cestou přesný požadavku od klienta.

- Vyhledat všechny požadavky vytvořené pro konkrétní adresář, použijte [Directory cesta URL](#url-path-directory) nebo [cesta URL zástupný znak](#url-path-wildcard) vyhovují podmínce.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>Regulární výraz cesty adresy URL
Porovná cesty URL požadavku na zadaný [regulární výraz](cdn-rules-engine-reference.md#regular-expressions).

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých Regex cesta URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat cestu adresy URL, která odpovídá zadanému regulárnímu výrazu.
- **Nemá neshodují**: vyžaduje požadavek obsahovat cestu adresy URL, která neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:
- Okraj CNAME adresa URL je přepsaná na adresu URL CDN před porovnání adresy URL. 
 
    Například obě adresy URL bodu pro stejný prostředek a proto mít stejnou cestu adresy URL.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm
    
    Další informace:
    
     - Cesta adresy URL: /800001/CustomerOrigin/path/asset.htm

- Řetězce dotazů v adrese URL se ignorují.
    
- Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí.
    
- Mezery v cestě adresa URL by měla být nahrazena "% 20."

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>Cesta URL zástupný znak
Porovná požadavku relativní cestu adresy URL ke vzoru zadaný zástupný znak.

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých zástupného cesta URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat cestu adresy URL, který odpovídá vzorku zadaný zástupný znak.
- **Nemá neshodují**: vyžaduje požadavek obsahovat cestu adresy URL, která neodpovídá zadaný zástupný znak – vzor.

Informace o klíči:
- **Vzhledem k** možnost: Tato možnost určuje, zda je adresa URL porovnání bod začne před nebo po bodem přístup k obsahu.

   Tato možnost může mít následující hodnoty:
     - **Kořenové**: Určuje, že bod porovnání adresa URL začíná přímo po hostitele CDN.

       Příklad: http:\//wpc.0001.&lt; domény&gt;/**800001/myorigin/myfolder/index.htm**

     - **Původ**: Určuje, že bod porovnání URL zahájí po bodem přístup k obsahu (například /000001 nebo/800001/myorigin). Protože \*. azureedge.net CNAME se vytvoří relativně ke zdroji adresáře na hostitele Verizon CDN ve výchozím nastavení, Azure CDN uživatelé by měli použít **původu** hodnotu. 

       Příklad: https:\//&lt;koncový bod&gt;.azureedge.net/**myfolder/index.htm**

     Tato adresa URL odkazuje na následující hostitele Verizon CDN: http:\//wpc.0001.&lt; domény&gt;/800001/myorigin/**myfolder/index.htm**

- Okraj CNAME adresa URL je přepsaná na adresu URL CDN před porovnání adresy URL.

    Například obě následující adresy URL bodu pro stejný prostředek a proto mají stejnou cestu adresy URL:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Další informace:
    
     - Cestu adresy URL (relativní vůči kořenovému adresáři): /800001/CustomerOrigin/path/asset.htm
    
     - Cestu adresy URL (relativní vůči původ): /path/asset.htm
    
- Zadejte více cest URL omezující každé z nich mezerou.

   Příklad: /marketing/asset.* /sales/*.htm

- Řetězce dotazů v adrese URL se ignorují.
    
- Použití **ignorovat případ** možnost řídit, jestli malá a velká písmena porovnání se provádí.
    
- Nahraďte mezery v cesty URL s "% 20."
    
- Hodnota zadaná pro cestu adresy URL můžete využít výhod [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values). Každou adresu URL vzorek cesty může obsahovat jeden nebo více hvězdičky (*), kde každý hvězdičky můžete shodují posloupnost jeden nebo více znaků.

#### <a name="sample-scenarios"></a>Vzorové scénáře

Ukázka konfigurace v následující tabulce předpokládá se, pokud požadavek odpovídá zadaný vzor adresy URL splnění této podmínky shody:

Hodnota                   | Vzhledem k    | Výsledek 
------------------------|----------------|-------
*/test.html */test.php  | Kořenový server WSUS nebo počátek | Tento vzor je nalezena shoda s žádostmi o u prostředků s názvem "test.html" nebo "test.php" v libovolné složky.
/ 80ABCD/původ nebo text / *   | Kořen           | Tento vzor je nalezena shoda, pokud požadovaný prostředek splňuje následující kritéria: <br />– Musí být umístěn na zákazníka původu, nazývá "původ." <br />– Relativní cesta musí začínat složku s názvem "text". To znamená, že požadovaný prostředek se může nacházet ve složce "text" nebo jeden z jejích podsložkách rekurzivní.
*/css/* */js/*          | Kořenový server WSUS nebo počátek | Tento vzor má odpovídající všechny CDN nebo Microsoft edge CNAME adresy URL, které obsahují složku šablon stylů css nebo js.
*.jpg *.gif *.png       | Kořenový server WSUS nebo počátek | Tento vzor má odpovídající všechny CDN nebo Microsoft edge CNAME adresy URL konče JPG, GIF nebo PNG. Zadejte tento vzor alternativní způsob je pomocí [rozšíření cesty adresy URL vyhovují podmínce](#url-path-extension).
/ Image / * / media / *      | Zdroj         | Tento vzor má odpovídající CDN nebo Microsoft edge CNAME adresy URL, jehož relativní cesta začíná na "kopie" nebo "média" složku. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>Adresa URL dotazu literál
Porovná řetězec dotazu požadavku se zadanou hodnotou.

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých URL dotazu literálu vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá zadaný řetězec dotazu.
- **Nemá neshodují**: vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá zadaný řetězec dotazu.

Informace o klíči:

- Jenom přesné dotazu řetězec odpovídá splňovat na shodu.
    
- Použití **ignorovat případ** možnost řídit rozlišování porovnávání řetězců dotazů.
    
- Do textu hodnotu řetězce dotazu nezahrnujte úvodní otazník (?).
    
- Některé znaky vyžadují kódování URL. Použít procento symbol na adresu URL kódování následující znaky:

   Znak | Kódování URL
   ----------|---------
   Mezera     | %20
   &         | %25

- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
   - Dokončení výplně mezipaměti
   - Výchozí interní Max-Age
   - Vynutit interní Max-Age
   - Ignorovat počátek No-Cache
   - Interní Max zastaralé

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>Parametr URL dotazu
Určuje požadavky, které obsahují parametr řetězce dotazu zadaná. Tento parametr je nastaven na hodnotu, která odpovídá zadanému vzoru. Parametry řetězce dotazu (například parametr = hodnota) v žádosti o adresu URL určit, zda při splnění této podmínky. Tento stav shody identifikuje parametr řetězce dotazu jeho název a přijímá jeden nebo více hodnot pro hodnotu parametru. 

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých parametr adresy URL dotazu vyhovují podmínce.
- **Odpovídá**: vyžaduje žádost obsahuje zadaný parametr s hodnotou, který odpovídá alespoň jeden z hodnoty, které jsou definované v tomto stavu shody.
- **Neodpovídá**: vyžaduje se, že splňuje požadavek některý z následujících kritérií:
  - Zadaný parametr neobsahuje.
  - Obsahuje zadaný parametr, ale jeho hodnota neodpovídá žádnému z hodnot, které jsou definované v tomto stavu shody.

Tento stav shody poskytuje snadný způsob, jak zadat název/hodnota kombinace parametrů. Větší pružnosti Pokud jsou odpovídající parametr řetězce dotazu, zvažte použití [URL dotazu zástupné](#url-query-wildcard) vyhovují podmínce.

Informace o klíči:
- Jeden instanci tento stav shody lze zadat pouze jednu adresu URL dotazu název parametru.
    
- Protože zástupné hodnoty nejsou podporovány, pokud je zadaný název parametru, jsou způsobilé pro porovnání pouze název odpovídá přesný parametr.
- Hodnoty parametru může obsahovat [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values).
   - Každý vzor hodnota parametru se může skládat z jedné nebo více hvězdičky (*), kde každý hvězdičky můžete shodují posloupnost jeden nebo více znaků.
   - Některé znaky vyžadují kódování URL. Použít procento symbol na adresu URL kódování následující znaky:

       Znak | Kódování URL
       ----------|---------
       Mezera     | %20
       &         | %25

- Zadejte více hodnot parametrů řetězce dotazu omezující každé z nich mezerou. Pokud požadavek obsahuje jeden z kombinace zadaný název hodnota splnění této podmínky shody.

   - Příklad 1:

     - Konfigurace:

       Hodnotu dat b

     - Tato konfigurace se shoduje s následující parametrů řetězce dotazu:

       Parameter1=ValueA
    
       Parameter1 = hodnotu b

   - Příklad 2:

     - Konfigurace: 

        Hodnota % 20A hodnota % 20B

     - Tato konfigurace se shoduje s následující parametrů řetězce dotazu:

       Parametr1 = hodnota % 20A

       Parametr1 = hodnota % 20B

- Splnění této podmínky shody tehdy, když je přesnou shodou alespoň jeden z kombinace názvu a hodnoty řetězce zadaný dotaz.

   Například pokud použijete konfiguraci v předchozím příkladu, parametr název hodnota kombinace "Parameter1 = ValueAdd" by být považovány za shodné. Ale pokud zadejte některou z těchto hodnot, bude shodovat kombinaci názvu a hodnoty:

   - ValueAdd hodnotu b dat
   - Hodnotu dat * b

- Použití **ignorovat případ** možnost řídit rozlišování porovnávání řetězců dotazů.
    
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
   - Dokončení výplně mezipaměti
   - Výchozí interní Max-Age
   - Vynutit interní Max-Age
   - Ignorovat počátek No-Cache
   - Interní Max zastaralé

#### <a name="sample-scenarios"></a>Vzorové scénáře
Následující příklad ukazuje, jak se tato možnost funguje v konkrétních situacích:

Název  | Hodnota |  Výsledek
------|-------|--------
Uživatel  | Jan   | Tento vzor je nalezena shoda, pokud je řetězec dotazu pro požadovanou adresu URL "? uživatele = Jan."
Uživatel  | *     | Tento vzor je nalezena shoda, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr uživatele.
E-mail | Jan\* | Tento vzor je nalezena shoda, pokud řetězec dotazu pro požadovanou adresu URL obsahuje parametr e-mailu, který začíná "Jan".

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>Adresa URL dotazu Regex
Určuje požadavky, které obsahují parametr řetězce dotazu zadaná. Tento parametr je nastaven na hodnotu, která odpovídá zadané [regulární výraz](cdn-rules-engine-reference.md#regular-expressions).

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých Regex dotaz adresy URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá zadanému regulárnímu výrazu.
- **Nemá neshodují**: vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá zadanému regulárnímu výrazu.

Informace o klíči:
- Jenom přesné shody k zadanému regulárnímu výrazu splňují tuto podmínku shodu.
    
- Použití **ignorovat případ** možnost řídit rozlišování porovnávání řetězců dotazů.
    
- Pro účely tuto možnost řetězec dotazu začíná první znak po otazník (?) oddělovač pro řetězec dotazu.
    
- Některé znaky vyžadují kódování URL. Použít procento symbol na adresu URL kódování následující znaky:

   Znak | Kódování URL | Hodnota
   ----------|--------------|------
   Mezera     | %20          | \%20
   &         | %25          | \%25

   Všimněte si, že procento symboly, je nutné uvést.

- Speciální regulární výraz dvojitou řídicí znaky (například \^$. +) Chcete-li regulární výraz obsahuje zpětné lomítko.

   Příklad:

   Hodnota | Interpretovat jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
   - Dokončení výplně mezipaměti
   - Výchozí interní Max-Age
   - Vynutit interní Max-Age
   - Ignorovat počátek No-Cache
   - Interní Max zastaralé


[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>Adresa URL dotazu zástupný znak
Porovná zadané hodnoty proti řetězec dotazu žádosti.

**Odpovídá**/**neodpovídá** možnost určuje, že se splní podmínky, za kterých zástupný dotaz adresy URL vyhovují podmínce.
- **Odpovídá**: vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která odpovídá hodnotě zadané zástupný znak.
- **Nemá neshodují**: vyžaduje požadavek obsahovat řetězec dotazu adresy URL, která neodpovídá hodnotě zadané zástupný znak.

Informace o klíči:
- Pro účely tuto možnost řetězec dotazu začíná první znak po otazník (?) oddělovač pro řetězec dotazu.
- Hodnoty parametrů mohou zahrnovat [zástupné hodnoty](cdn-rules-engine-reference.md#wildcard-values):
   - Každý vzor hodnota parametru se může skládat z jedné nebo více hvězdičky (*), kde každý hvězdičky můžete shodují posloupnost jeden nebo více znaků.
   - Některé znaky vyžadují kódování URL. Použít procento symbol na adresu URL kódování následující znaky:

     Znak | Kódování URL
     ----------|---------
     Mezera     | %20
     &         | %25

- Zadejte více hodnot omezující každé z nich mezerou.

   Příklad: *Parameter1 = dat* *hodnotu b* *Parameter1 = ValueC & parametr2 = ValueD*

- Jenom přesné shody do alespoň jedné vzoru řetězec zadaný dotaz splňují tuto podmínku shodu.
    
- Použití **ignorovat případ** možnost řídit rozlišování porovnávání řetězců dotazů.
    
- Z důvodu způsobem v mezipaměti, které jsou sledovány nastavení tato podmínka shoda není kompatibilní s následující funkce:
   - Dokončení výplně mezipaměti
   - Výchozí interní Max-Age
   - Vynutit interní Max-Age
   - Ignorovat počátek No-Cache
   - Interní Max zastaralé

#### <a name="sample-scenarios"></a>Vzorové scénáře
Následující příklad ukazuje, jak se tato možnost funguje v konkrétních situacích:

 Název                 | Popis
 ---------------------|------------
user=joe              | Tento vzor je nalezena shoda, pokud je řetězec dotazu pro požadovanou adresu URL "? uživatele = Jan."
\*Uživatel =\* \*optout =\* | Tento vzor je nalezena shoda, pokud dotaz adresy URL CDN obsahuje uživatele nebo optout parametr.

[Zpět na začátek](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Další postup
* [Přehled sítě pro doručování obsahu Azure](cdn-overview.md)
* [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
* [Podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkce stroje pravidel](cdn-rules-engine-reference-features.md)
* [Přepsání výchozího nastavení HTTP používá stroj pravidel](cdn-rules-engine.md)

