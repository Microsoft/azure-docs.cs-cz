---
title: Podmínky shody stroje Azure pro pravidla front
description: Tento článek poskytuje seznam různých podmínek shody, které jsou k dispozici pro modul pravidel služby Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1b2b891a0b6b67efef38005d3a4d67eecf41afbd
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531861"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Podmínky shody stroje pravidel služby Azure Front Door

V [modulu pravidel AFD](front-door-rules-engine.md) se pravidlo skládá z nuly nebo více podmínek shody a akce. Tento článek poskytuje podrobné popisy podmínek shody, které můžete použít v modulu pravidel AFD.

První část pravidla je podmínka shody nebo sada podmínek shody. Pravidlo může sestávat z až deseti podmínek shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které jsou provedeny definované akce. Pokud použijete více podmínek shody, podmínky shody jsou seskupeny pomocí logiky a. Pro všechny podmínky shody, které podporují více hodnot (uvedené níže jako "oddělený mezerou"), se předpokládá operátor "nebo".

Podmínku shody můžete například použít k těmto akcím:

- Vyfiltruje požadavky na základě konkrétní IP adresy, země nebo oblasti.
- Vyfiltruje požadavky podle informací v záhlaví.
- Vyfiltruje požadavky z mobilních zařízení nebo stolních zařízení.

K dispozici jsou následující podmínky shody, které je možné použít v modulu pravidel služby Azure front-dveří.  

## <a name="device-type"></a>Typ zařízení 

Identifikuje požadavky vytvořené z mobilního zařízení nebo stolního zařízení.  

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | Mobilní zařízení, Desktop

## <a name="post-argument"></a>Post – argument

Identifikuje požadavky založené na argumentech definovaných pro metodu POST Request, která se používá v žádosti. 

#### <a name="required-fields"></a>Povinná pole

Název argumentu | Operátor | Hodnota argumentu | Transformace případu
--------------|----------|----------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

## <a name="query-string"></a>Řetězec dotazu

Identifikuje požadavky, které obsahují konkrétní parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá konkrétnímu vzoru. Parametry řetězce dotazu (například **parametr = hodnota**) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle názvu a přijímá jednu nebo více hodnot pro hodnotu parametru.

#### <a name="required-fields"></a>Povinná pole

Operátor | Řetězec dotazu | Transformace případu
---------|--------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

## <a name="remote-address"></a>Vzdálená adresa

Identifikuje požadavky na základě umístění nebo IP adresy žadatele.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|-----------------
Geografická shoda | Kód země
Shoda IP adres | IP adresa (oddělená mezerami)
Neshoda geografického umístění | Kód země
Neshoda IP adres | IP adresa (oddělená mezerami)

#### <a name="key-information"></a>Informace o klíči

- Použijte notaci CIDR.
- Pokud chcete zadat víc IP adres a bloků IP adres, použijte jednu mezeru mezi hodnotami:
  - **Příklad protokolu IPv4**: *1.2.3.4 10.20.30.40* odpovídá všem žádostem, které dorazí buď na adresu 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* odpovídá všem žádostem, které přicházejí buď z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe bloku IP adres je základní IP adresa, za kterou následuje lomítko a velikost předpony. Příklad:
  - **Příklad IPv4**: *5.5.5.64/26* odpovídá všem žádostem, které přicházejí z adres 5.5.5.64 prostřednictvím 5.5.5.127.
  - **Příklad IPv6**: *1:2:3:/48* odpovídá všem žádostem, které přicházejí z adres 1:2:3:0:0:0:0:0 až 1:2: ffff: ffff: ffff: ffff: ffff: FFFF.

## <a name="request-body"></a>Text požadavku

Identifikuje požadavky na základě konkrétního textu, který se zobrazí v těle žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Text požadavku | Transformace případu
---------|--------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

## <a name="request-header"></a>Hlavička požadavku

Identifikuje požadavky, které v žádosti používají konkrétní hlavičku.

#### <a name="required-fields"></a>Povinná pole

Název hlavičky | Operátor | Hodnota hlavičky | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

## <a name="request-method"></a>Request – metoda

Identifikuje požadavky, které používají zadanou metodu žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | ZÍSKÁNÍ, VYSTAVENÍ, VLOŽENÍ, ODSTRANĚNÍ, HLAVNÍ, MOŽNOSTI, TRASOVÁNÍ

#### <a name="key-information"></a>Informace o klíči

- Jenom metoda GET Request může vygenerovat obsah uložený v mezipaměti v frontách Azure. Všechny ostatní metody žádosti jsou proxy prostřednictvím sítě. 

## <a name="request-protocol"></a>Protokol žádosti

Určuje požadavky, které používají zadaný protokol.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | HTTP, HTTPS

## <a name="request-url"></a>Adresa URL požadavku

Identifikuje požadavky, které odpovídají zadané adrese URL.

#### <a name="required-fields"></a>Povinná pole

Operátor | Adresa URL požadavku | Transformace případu
---------|-------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

#### <a name="key-information"></a>Informace o klíči

- Když použijete tuto podmínku pravidla, nezapomeňte zahrnout informace o protokolu. Například: * https://www . \<yourdomain\> . model COM*.

## <a name="request-file-extension"></a>Žádost o příponu souboru

Identifikuje požadavky, které zahrnují zadanou příponu souboru v názvu souboru v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Linka | Transformace případu
---------|-----------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

#### <a name="key-information"></a>Informace o klíči

- V případě rozšíření Nezahrnovat úvodní období; použijte například *HTML* namísto *. html*.

## <a name="request-file-name"></a>Název souboru žádosti

Identifikuje požadavky, které obsahují zadaný název souboru v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Název souboru | Transformace případu
---------|-----------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

#### <a name="key-information"></a>Informace o klíči

- Pokud chcete zadat více názvů souborů, oddělte každý název souboru stisknutím klávesy ENTER. 

## <a name="request-path"></a>Cesta požadavku

Určuje požadavky, které zahrnují zadanou cestu v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Hodnota | Transformace případu
---------|-------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Malá písmena, Velká písmena, střih, odebrat prázdné znaky, kódování URL, kódování adresy URL

## <a name="standard-operator-list"></a>Seznam standardních operátorů

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

- Naučte se nastavit [konfiguraci modulu](front-door-tutorial-rules-engine.md)pro první pravidla. 
- Další informace o [akcích modulu pravidel](front-door-rules-engine-actions.md)
- Další informace o [modulu pravidel pro přední dveře Azure](front-door-rules-engine.md)
