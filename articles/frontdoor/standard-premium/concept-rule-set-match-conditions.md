---
title: Konfigurace podmínek shody sady pravidel pro Azure front-end Standard/Premium
description: Tento článek poskytuje seznam různých podmínek shody, které jsou k dispozici pro sadu pravidel služby Azure front-Premium Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099191"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Podmínky shody sady pravidel služby Azure front-Premium Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

V tomto kurzu se dozvíte, jak vytvořit sadu pravidel s první sadou pravidel v Azure Portal. V sadě Azure front-Premium [Rule](concept-rule-set.md)Standard/Premium se pravidlo skládá z nuly nebo více podmínek shody a akce. Tento článek poskytuje podrobné popisy podmínek shody, které můžete použít v sadě pravidel služby Azure front-Premium Standard/Premium.

První část pravidla je podmínka shody nebo sada podmínek shody. Pravidlo může sestávat z až deseti podmínek shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které jsou provedeny definované akce. Pokud použijete více podmínek shody, podmínky shody jsou seskupeny pomocí logiky a. Pro všechny podmínky shody, které podporují více hodnot (zaznamenáno "mezera"), předpokládá se operátor "nebo".

Podmínku shody můžete například použít k těmto akcím:

* Vyfiltruje požadavky na základě konkrétní IP adresy, země nebo oblasti.
* Vyfiltruje požadavky podle informací v záhlaví.
* Vyfiltruje požadavky z mobilních zařízení nebo stolních zařízení.
* Vyfiltruje požadavky z názvu souboru žádosti a přípony souboru.
* Vyfiltruje požadavky z adresy URL požadavku, protokolu, cesty, řetězce dotazu, parametrů post atd.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Následující podmínky shody jsou k dispozici pro použití v sadě Azure front-in Rules Standard/Premium:

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
Řetězec | [Seznam operátorů](#operator-list) | Řetězec, int | Malá písmena, Velká písmena

## <a name="query-string"></a>Řetězec dotazu

Identifikuje požadavky, které obsahují konkrétní parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá konkrétnímu vzoru. Parametry řetězce dotazu (například **parametr = hodnota**) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle názvu a přijímá jednu nebo více hodnot pro hodnotu parametru.

#### <a name="required-fields"></a>Povinná pole

Operátor | Řetězec dotazu | Transformace případu
---------|--------------|---------------
[Seznam operátorů](#operator-list) | Řetězec, int | Malá písmena, Velká písmena

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

* Použijte notaci CIDR.
* Pro více IP adres a bloků IP adres se používá logika "nebo".
    * **Příklad protokolu IPv4**: Pokud PŘIDÁTE dvě IP adresy *1.2.3.4* a *10.20.30.40*, je podmínka shodná, pokud všechny požadavky přicházejí z adresy 1.2.3.4 nebo 10.20.30.40.
    * **Příklad IPv6**: Pokud PŘIDÁTE dvě IP adresy *1:2:3:4:5:6:7:8* a *10:20:30:40:50:60:70:80*, bude se tato podmínka shodovat, pokud všechny žádosti, které přicházejí z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
* Syntaxe bloku IP adres je základní IP adresa, za kterou následuje lomítko a velikost předpony. Příklad:
    * **Příklad IPv4**: *5.5.5.64/26* odpovídá všem žádostem, které přicházejí z adres 5.5.5.64 prostřednictvím 5.5.5.127.
    * **Příklad IPv6**: *1:2:3:/48* odpovídá všem žádostem, které přicházejí z adres 1:2:3:0:0:0:0:0 až 1:2:3: ffff: ffff: ffff: ffff: FFFF.

## <a name="request-body"></a>Text požadavku

Identifikuje požadavky na základě konkrétního textu, který se zobrazí v těle žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Text požadavku | Transformace případu
---------|--------------|---------------
[Seznam operátorů](#operator-list) | Řetězec, int | Malá písmena, Velká písmena

## <a name="request-header"></a>Hlavička požadavku

Identifikuje požadavky, které v žádosti používají konkrétní hlavičku.

#### <a name="required-fields"></a>Povinná pole

Název hlavičky | Operátor | Hodnota hlavičky | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Seznam operátorů](#operator-list) | Řetězec, int | Malá písmena, Velká písmena

## <a name="request-method"></a>Request – metoda

Identifikuje požadavky, které používají zadanou metodu žádosti.

#### <a name="required-fields"></a>Povinná pole

Operátor | Podporované hodnoty
---------|----------------
Rovná se, není rovno | ZÍSKÁNÍ, VYSTAVENÍ, VLOŽENÍ, ODSTRANĚNÍ, HLAVNÍ, MOŽNOSTI, TRASOVÁNÍ

#### <a name="key-information"></a>Informace o klíči

Jenom metoda GET Request může vygenerovat obsah uložený v mezipaměti v frontách Azure. Všechny ostatní metody žádosti jsou proxy prostřednictvím sítě.

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
[Seznam operátorů](#operator-list) | Řetězec, int | Malá písmena, Velká písmena

#### <a name="key-information"></a>Informace o klíči

Když použijete tuto podmínku pravidla, nezapomeňte zahrnout informace o protokolu. Například: *https://www . \<yourdomain\> . model COM*.

## <a name="request-file-extension"></a>Žádost o příponu souboru

Identifikuje požadavky, které zahrnují zadanou příponu souboru v názvu souboru v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Linka | Transformace případu
---------|-----------|---------------
[Seznam operátorů](#operator-list)  | Řetězec, int | Malá písmena, Velká písmena

#### <a name="key-information"></a>Informace o klíči

V případě rozšíření Nezahrnovat úvodní období; použijte například *HTML* namísto *. html*.

## <a name="request-file-name"></a>Název souboru žádosti

Identifikuje požadavky, které obsahují zadaný název souboru v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Název souboru | Transformace případu
---------|-----------|---------------
[Seznam operátorů](#operator-list)| Řetězec, int | Malá písmena, Velká písmena

## <a name="request-path"></a>Cesta požadavku

Určuje požadavky, které zahrnují zadanou cestu v adrese URL pro vyžádání.

#### <a name="required-fields"></a>Povinná pole

Operátor | Hodnota | Transformace případu
---------|-------|---------------
[Seznam operátorů](#operator-list) | Řetězec, int | Malá písmena, Velká písmena

## <a name="operator-list"></a><a name = "operator-list"></a>Seznam operátorů

Pro pravidla, která přijímají hodnoty ze seznamu standardních operátorů, platí následující operátory:

* Libovolný
* Je rovno
* Contains
* Začíná na
* Končí na
* Menší než
* Je menší než nebo rovno
* Větší než
* Je větší než nebo rovno
* Ne žádné
* Neobsahuje
* Nezačíná na
* Nekončí na
* Není menší než
* Není menší než nebo rovno
* Není větší než
* Není větší než nebo rovno
* Regulární výraz

Pro číselné operátory, které jsou *menší než* a *větší než nebo rovno*, je použití porovnání založeno na délce. Hodnota v podmínce Match by měla být celé číslo, které se rovná délce, kterou chcete porovnat.

## <a name="regular-expression"></a>Regulární výraz

Regulární výraz nepodporuje následující operace:

* Zpětná reference a zachycení dílčích výrazů
* Libovolné kontrolní výrazy s nulovou šířkou
* Reference k podrutinám a rekurzivní vzory
* Podmíněné vzory
* Řízení operací zpětného navrácení
* Direktiva "\c single byte"
* Direktiva pro porovnávání nového řádku \r
* Začátek směrnice pro obnovení shody od \K
* Popisky a vložený kód
* Seskupení Atomic a kvantifikátory possessive

## <a name="next-steps"></a>Další kroky

* Další informace o [sadě pravidel](concept-rule-set.md)
* Přečtěte si, jak [nakonfigurovat svou první sadu pravidel](how-to-configure-rule-set.md).
* Přečtěte si další informace o [akcích sady pravidel](concept-rule-set-actions.md).
