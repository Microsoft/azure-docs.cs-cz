---
title: Podmínky shody v modulu standardních pravidel pro Azure CDN | Dokumenty společnosti Microsoft
description: Referenční dokumentace pro podmínky shody v modulu standardních pravidel pro síť doručování obsahu Azure (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900176"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Shody podmínek v modulu standardních pravidel pro Azure CDN

V [modulu standardnípravidla](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akce. Tento článek obsahuje podrobný popis podmínek shody, které můžete použít v modulu standardnípravidla pro Azure CDN.

První část pravidla je podmínka shody nebo sada podmínek shody. V modulu standardnípravidla pro Azure CDN každé pravidlo může mít až čtyři podmínky shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které jsou prováděny definované akce. Pokud použijete více podmínek shody, podmínky shody jsou seskupeny pomocí logiky AND.

Podmínku shody můžete například použít k:

- Filtrování požadavků na základě konkrétní IP adresy, země nebo oblasti.
- Filtrujte požadavky podle informací záhlaví.
- Filtrujte požadavky z mobilních nebo stolních zařízení.

## <a name="match-conditions"></a>Podmínky zápasu

Následující podmínky shody jsou k dispozici pro použití v modulu standardní pravidla pro Azure CDN. 

### <a name="device-type"></a>Typ zařízení 

Identifikuje požadavky z mobilního nebo stolního zařízení.  

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, nerovná se | Mobilní zařízení, Stolní počítače

### <a name="http-version"></a>Verze HTTP

Identifikuje požadavky na základě verze http požadavku.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, nerovná se | 2.0, 1.1, 1.0, 0.9, All

### <a name="request-cookies"></a>Žádost o soubory cookie

Identifikuje požadavky na základě informací o souborech cookie v příchozí žádosti.

#### <a name="required-fields"></a>Povinná pole

Název souboru cookie | Operátor | Hodnota souboru cookie | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

#### <a name="key-information"></a>Klíčové informace

- Při zadávání názvu souboru cookie nelze použít\*hodnoty zástupných symbolů (včetně hvězdiček ( )). musíte použít přesný název souboru cookie.
- Pro každou instanci této podmínky shody můžete zadat pouze jeden název souboru cookie.
- Porovnání názvů souborů jsou malá a velká písmena.
- Chcete-li zadat více hodnot souborů cookie, použijte jednu mezeru mezi každou hodnotou souboru cookie. 
- Hodnoty souborů cookie mohou využívat hodnoty zástupných symbolů.
- Pokud nebyla zadána hodnota zástupné znakové hodnoty, splňuje tuto podmínku shody pouze přesná shoda. Například "Hodnota" bude odpovídat "Hodnota", ale ne "Hodnota1". 

### <a name="post-argument"></a>Zaúčtovat argument

Identifikuje požadavky na základě argumentů definovaných pro metodu požadavku POST, která se používá v požadavku. 

#### <a name="required-fields"></a>Povinná pole

Název argumentu | Operátor | Hodnota argumentu | Transformace případu
--------------|----------|----------------|---------------
Řetězec | [Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

### <a name="query-string"></a>Řetězec dotazu

Identifikuje požadavky, které obsahují určitý parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá určitému vzoru. Parametry řetězce dotazu (například **parameter=value**) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle jeho názvu a přijímá jednu nebo více hodnot pro hodnotu parametru.

#### <a name="required-fields"></a>Povinná pole

Operátor | Řetězec dotazu | Transformace případu
---------|--------------|---------------
[Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

### <a name="remote-address"></a>Vzdálená adresa

Identifikuje požadavky na základě umístění nebo IP adresy uchazeče.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|-----------------
Všechny | Není dostupné.
Geografická shoda | Kód země
Shoda IP | IP adresa (oddělená od místa)
Ne žádné | Není dostupné.
Není geo shoda | Kód země
Není ip shoda | IP adresa (oddělená od místa)

#### <a name="key-information"></a>Klíčové informace

- Použijte cidr zápis.
- Chcete-li zadat více adres IP a bloků IP adres, použijte jednu mezeru mezi hodnotami:
  - **Příklad IPv4**: *1.2.3.4 10.20.30.40* odpovídá všem požadavkům, které přicházejí z adresy 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* odpovídá všem požadavkům, které přicházejí z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe bloku IP adres je základní IP adresa následovaná lomítkem a velikostí předpony. Například:
  - **Příklad IPv4**: *5.5.5.64/26* odpovídá všem požadavkům, které přicházejí z adres 5.5.5.64 až 5.5.5.127.
  - **Příklad IPv6**: *1:2:3:/48* odpovídá všem požadavkům, které přicházejí z adres 1:2:3:0:0:0:0: 0 až 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff.ffff.

### <a name="request-body"></a>Text požadavku

Identifikuje požadavky na základě konkrétního textu, který se zobrazí v textu požadavku.

#### <a name="required-fields"></a>Povinná pole

Operátor | Text požadavku | Transformace případu
---------|--------------|---------------
[Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

### <a name="request-header"></a>Hlavička požadavku

Identifikuje požadavky, které používají konkrétní hlavičku v požadavku.

#### <a name="required-fields"></a>Povinná pole

Název hlavičky | Operátor | Hodnota hlavičky | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

### <a name="request-method"></a>Metoda požadavku

Identifikuje požadavky, které používají zadanou metodu požadavku.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, nerovná se | ZÍSKAT, ZVEŘEJNIT, DÁT, ODSTRANIT, HLAVA, MOŽNOSTI, TRASOVAT

#### <a name="key-information"></a>Klíčové informace

- Pouze get request metoda můžete generovat obsah uložený v mezipaměti v Azure CDN. Všechny ostatní metody požadavku jsou proxied prostřednictvím sítě. 

### <a name="request-protocol"></a>Protokol požadavku

Identifikuje požadavky, které používají zadaný protokol.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, nerovná se | HTTP, HTTPS

### <a name="request-url"></a>Adresa URL požadavku

Identifikuje požadavky, které odpovídají zadané adrese URL.

#### <a name="required-fields"></a>Povinná pole

Operátor | Adresa URL požadavku | Transformace případu
---------|-------------|---------------
[Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

#### <a name="key-information"></a>Klíčové informace

- Při použití této podmínky pravidla nezapomeňte zahrnout informace o protokolu. Například: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Přípona souboru URL

Identifikuje požadavky, které obsahují zadanou příponu souboru v názvu souboru v adresě URL žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Linka | Transformace případu
---------|-----------|---------------
[Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

#### <a name="key-information"></a>Klíčové informace

- Pro rozšíření nezahrnejte úvodní období; použijte například *html* místo *.html*.

### <a name="url-file-name"></a>Název souboru ADRESY URL

Identifikuje požadavky, které obsahují zadaný název souboru v adresě URL žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Název souboru | Transformace případu
---------|-----------|---------------
[Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

#### <a name="key-information"></a>Klíčové informace

- Chcete-li zadat více názvů souborů, oddělte každý název souboru jednou mezerou. 

### <a name="url-path"></a>Cesta url

Identifikuje požadavky, které obsahují zadanou cestu v žádající adrese URL.

#### <a name="required-fields"></a>Povinná pole

Operátor | Hodnota | Transformace případu
---------|-------|---------------
[Standardní seznam operátorů](#standard-operator-list) | Řetězec, Int | Žádná transformace, na velká, na malá písmena

#### <a name="key-information"></a>Klíčové informace

- Hodnota názvu souboru může využívat hodnoty zástupných symbolů. Každý vzor názvu souboru se může například skládat z jedné nebo více hvězdiček (*), kde každá hvězdička odpovídá posloupnosti jednoho nebo více znaků.

## <a name="reference-for-rules-engine-match-conditions"></a>Odkaz na podmínky shody motoru pravidla

### <a name="standard-operator-list"></a>Standardní seznam operátorů

Pro pravidla, která přijímají hodnoty ze seznamu standardních operátorů, jsou platné následující operátory:

- Všechny
- Rovná se 
- Contains 
- Začíná na 
- Končí 
- Menší než
- Menší nebo rovno
- Větší než
- Větší než nebo rovno
- Ne žádné
- Neobsahuje
- Ne začíná 
- Nekončí 
- Ne méně než
- Nejméně nebo rovno
- Není větší než
- Ne větší než nebo rovno

Pro číselné operátory jako *Menší než* a Větší než *nebo rovno ,* porovnání použité je založena na délce. V takovém případě by hodnota v podmínce shody měla být celé číslo, které se rovná délce, kterou chcete porovnat. 

## <a name="next-steps"></a>Další kroky

- [Přehled azure cdn](cdn-overview.md)
- [Reference ke stroji pravidel Standard](cdn-standard-rules-engine-reference.md)
- [Akce v modulu standardních pravidel](cdn-standard-rules-engine-actions.md)
- [Vynucení HTTPS s využitím stroje pravidel Standard](cdn-standard-rules-engine.md)
