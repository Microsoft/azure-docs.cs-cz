---
title: Podmínky shody v modulu Standard rules pro Azure CDN | Microsoft Docs
description: Referenční dokumentace pro podmínky shody v modulu Standard rules pro Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "81259918"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Podmínky shody v modulu Standard rules pro Azure CDN

V [modulu Standard Rules](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akci. Tento článek poskytuje podrobné popisy podmínek shody, které můžete použít v modulu Standard rules pro Azure CDN.

První část pravidla je podmínka shody nebo sada podmínek shody. V modulu Standard rules pro Azure CDN může mít každé pravidlo až čtyři podmínky shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které jsou provedeny definované akce. Pokud použijete více podmínek shody, podmínky shody jsou seskupeny pomocí logiky a.

Podmínku shody můžete například použít k těmto akcím:

- Vyfiltruje požadavky na základě konkrétní IP adresy, země nebo oblasti.
- Vyfiltruje požadavky podle informací v záhlaví.
- Vyfiltruje požadavky z mobilních zařízení nebo stolních zařízení.

## <a name="match-conditions"></a>Podmínky shody

Následující podmínky shody jsou k dispozici pro použití v modulu Standard rules pro Azure CDN. 

### <a name="device-type"></a>Typ zařízení 

Identifikuje požadavky vytvořené z mobilního zařízení nebo stolního zařízení.  

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | Mobilní zařízení, Desktop

### <a name="http-version"></a>Verze protokolu HTTP

Identifikuje požadavky na základě verze HTTP žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | 2,0, 1,1, 1,0, 0,9, vše

### <a name="request-cookies"></a>Soubory cookie požadavků

Identifikuje požadavky na základě informací o souborech cookie v příchozím požadavku.

#### <a name="required-fields"></a>Povinná pole

Název souboru cookie | Operátor | Hodnota souboru cookie | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

#### <a name="key-information"></a>Informace o klíči

- Pokud zadáte název souboru cookie, nemůžete použít zástupné znaky (včetně hvězdiček ( \* )). je nutné použít přesný název souboru cookie.
- Pro každou instanci této podmínky shody můžete zadat jenom jeden název souboru cookie.
- Porovnávání názvů souborů cookie rozlišuje velká a malá písmena.
- Chcete-li zadat více hodnot souborů cookie, použijte jednu mezeru mezi každou hodnotou souboru cookie. 
- Hodnoty souboru cookie můžou využívat zástupné hodnoty.
- Pokud není zadaná zástupná hodnota, splňuje tato podmínka shody jenom přesnou shodu. Například hodnota "value" bude odpovídat hodnotě "value", ale ne "hodnota1". 

### <a name="post-argument"></a>Post – argument

Identifikuje požadavky založené na argumentech definovaných pro metodu POST Request, která se používá v žádosti. 

#### <a name="required-fields"></a>Povinná pole

Název argumentu | Operátor | Hodnota argumentu | Transformace případu
--------------|----------|----------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="query-string"></a>Řetězec dotazu

Identifikuje požadavky, které obsahují konkrétní parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá konkrétnímu vzoru. Parametry řetězce dotazu (například **parametr = hodnota**) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle názvu a přijímá jednu nebo více hodnot pro hodnotu parametru.

#### <a name="required-fields"></a>Povinná pole

Operátor | Řetězec dotazu | Transformace případu
---------|--------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="remote-address"></a>Vzdálená adresa

Identifikuje požadavky na základě umístění nebo IP adresy žadatele.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|-----------------
Všechny | –
Geografická shoda | Kód země
Shoda IP adres | IP adresa (oddělená mezerami)
Ne žádné | –
Neshoda geografického umístění | Kód země
Neshoda IP adres | IP adresa (oddělená mezerami)

#### <a name="key-information"></a>Informace o klíči

- Použijte notaci CIDR.
- Pokud chcete zadat víc IP adres a bloků IP adres, použijte jednu mezeru mezi hodnotami:
  - **Příklad protokolu IPv4**: *1.2.3.4 10.20.30.40* odpovídá všem žádostem, které dorazí buď na adresu 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* odpovídá všem žádostem, které přicházejí buď z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe bloku IP adres je základní IP adresa, za kterou následuje lomítko a velikost předpony. Například:
  - **Příklad IPv4**: *5.5.5.64/26* odpovídá všem žádostem, které přicházejí z adres 5.5.5.64 prostřednictvím 5.5.5.127.
  - **Příklad IPv6**: *1:2:3:/48* odpovídá všem žádostem, které přicházejí z adres 1:2:3:0:0:0:0:0 až 1:2: ffff: ffff: ffff: ffff: ffff: FFFF.

### <a name="request-body"></a>Text požadavku

Identifikuje požadavky na základě konkrétního textu, který se zobrazí v těle žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Text požadavku | Transformace případu
---------|--------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="request-header"></a>Hlavička požadavku

Identifikuje požadavky, které v žádosti používají konkrétní hlavičku.

#### <a name="required-fields"></a>Povinná pole

Název hlavičky | Operátor | Hodnota hlavičky | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="request-method"></a>Request – metoda

Identifikuje požadavky, které používají zadanou metodu žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | ZÍSKÁNÍ, VYSTAVENÍ, VLOŽENÍ, ODSTRANĚNÍ, HLAVNÍ, MOŽNOSTI, TRASOVÁNÍ

#### <a name="key-information"></a>Informace o klíči

- Obsah uložený v mezipaměti v Azure CDN může generovat jenom metoda GET Request. Všechny ostatní metody žádosti jsou proxy prostřednictvím sítě. 

### <a name="request-protocol"></a>Protokol žádosti

Určuje požadavky, které používají zadaný protokol.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | HTTP, HTTPS

### <a name="request-url"></a>Adresa URL požadavku

Identifikuje požadavky, které odpovídají zadané adrese URL.

#### <a name="required-fields"></a>Povinná pole

Operátor | Adresa URL požadavku | Transformace případu
---------|-------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

#### <a name="key-information"></a>Informace o klíči

- Když použijete tuto podmínku pravidla, nezapomeňte zahrnout informace o protokolu. Například: *https://www . \<yourdomain\> . model COM*.

### <a name="url-file-extension"></a>Přípona souboru adresy URL

Identifikuje požadavky, které zahrnují zadanou příponu souboru v názvu souboru v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Linka | Transformace případu
---------|-----------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

#### <a name="key-information"></a>Informace o klíči

- V případě rozšíření Nezahrnovat úvodní období; použijte například *HTML* namísto *. html*.

### <a name="url-file-name"></a>Název souboru URL

Identifikuje požadavky, které obsahují zadaný název souboru v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Název souboru | Transformace případu
---------|-----------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

#### <a name="key-information"></a>Informace o klíči

- Chcete-li zadat více názvů souborů, oddělte každý název souboru jediným mezerou. 

### <a name="url-path"></a>Cesta URL

Určuje požadavky, které zahrnují zadanou cestu v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Hodnota | Transformace případu
---------|-------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

#### <a name="key-information"></a>Informace o klíči

- Hodnota názvu souboru může využít zástupné hodnoty. Například každý vzor názvu souboru může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička odpovídá sekvenci jednoho nebo více znaků.

## <a name="reference-for-rules-engine-match-conditions"></a>Reference pro podmínky shody stroje pravidel

### <a name="standard-operator-list"></a>Seznam standardních operátorů

Pro pravidla, která přijímají hodnoty ze seznamu standardních operátorů, platí následující operátory:

- Všechny
- Je rovno 
- Contains 
- Začíná na 
- Končí na 
- Menší než
- Je menší než nebo rovno
- Větší než
- Je větší než nebo rovno
- Ne žádné
- Neobsahuje
- Nezačíná na 
- Nekončí na 
- Není menší než
- Není menší než nebo rovno
- Není větší než
- Není větší než nebo rovno

Pro číselné operátory, které jsou *menší než* a *větší než nebo rovno*, je použití porovnání založeno na délce. V tomto případě by hodnota v podmínce shody měla být celé číslo, které se rovná délce, kterou chcete porovnat. 

## <a name="next-steps"></a>Další kroky

- [Přehled Azure CDN](cdn-overview.md)
- [Reference ke stroji pravidel Standard](cdn-standard-rules-engine-reference.md)
- [Akce v modulu Standard Rules](cdn-standard-rules-engine-actions.md)
- [Vynucení HTTPS s využitím stroje pravidel Standard](cdn-standard-rules-engine.md)
