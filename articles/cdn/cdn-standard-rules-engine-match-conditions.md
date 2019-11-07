---
title: Azure CDN z podmínek shody stroje Microsoft Standard Rules | Microsoft Docs
description: Referenční dokumentace pro Azure Content Delivery Network od podmínek shody stroje Microsoft Standard Rules.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615959"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Azure CDN z podmínek shody stroje Microsoft Standard Rules

V tomto článku jsou uvedené podrobné popisy dostupných podmínek shody pro Azure Content Delivery Network (CDN) od stroje Microsoft [Standard Rules](cdn-standard-rules-engine.md).

První část pravidla je množina podmínek shody. Každé pravidlo může mít až 4 podmínky shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které budou provedeny akce definované v pravidle. Pokud použijete více podmínek shody, budou seskupeny pomocí logiky a.

Podmínku shody můžete například použít k těmto akcím:

- Vyfiltruje požadavky vygenerované z konkrétní IP adresy nebo země nebo oblasti.
- Vyfiltruje požadavky podle informací v záhlaví.
- Vyfiltruje požadavky z mobilních nebo stolních zařízení.

## <a name="match-conditions"></a>Podmínky shody

Pro použití jsou k dispozici následující podmínky shody. 

### <a name="device-type"></a>Typ zařízení 

Podmínka shody typů zařízení identifikuje požadavky vytvořené z mobilního nebo stolního zařízení na základě jeho vlastností.  

**Povinná pole**

Operátor | Podporovaná hodnota
---------|----------------
Rovná se, není rovno | Mobilní zařízení, Desktop


### <a name="http-version"></a>Verze protokolu HTTP

Podmínka shody verze HTTP identifikuje požadavky na základě verze protokolu HTTP, se kterou požadavek dorazí.

**Povinná pole**

Operátor | Podporovaná hodnota
---------|----------------
Rovná se, není rovno | 2,0, 1,1, 1,0, 0,9, vše


### <a name="request-cookies"></a>Soubory cookie požadavků

Podmínka shody souborů cookie žádosti identifikuje požadavky na základě informací o souborech cookie v příchozím požadavku.

**Povinná pole**

Název souboru cookie | Operátor | Hodnota souboru cookie | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

Informace o klíči
- Zástupné hodnoty, včetně hvězdičky (*), nejsou při zadávání názvu souboru cookie podporované, pro porovnání jsou způsobilé jenom přesné názvy souborů cookie.
- Pro každou instanci této podmínky shody lze zadat pouze jeden název souboru cookie.
- Porovnávání názvů souborů cookie rozlišuje velká a malá písmena.
- Určete více hodnot souborů cookie tak, že každý z nich omezíte na jednu mezeru. 
- Hodnoty souboru cookie můžou využívat zástupné hodnoty.
- Pokud není zadaná zástupná hodnota, bude tato podmínka shody vyhovovat jenom přesná shoda. Například zadání hodnoty "hodnota" bude odpovídat hodnotě "value", ale ne "hodnota1". 

### <a name="post-argument"></a>Post – argument

**Povinná pole**

Název argumentu | Operátor | Hodnota argumentu | Transformace případu
--------------|----------|----------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="query-string"></a>Řetězec dotazu

Podmínky shody řetězce dotazu označují požadavky obsahující zadaný parametr řetězce dotazu. Tento parametr je nastaven na hodnotu, která odpovídá zadanému vzoru. Parametry řetězce dotazu (například parametr = hodnota) v adrese URL požadavku určují, zda je tato podmínka splněna. Tato podmínka shody identifikuje parametr řetězce dotazu podle názvu a přijímá jednu nebo více hodnot pro hodnotu parametru.

**Povinná pole**

Operátor | Řetězec dotazu | Transformace případu
---------|--------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="remote-address"></a>Vzdálená adresa

Podmínka shody vzdálených adres identifikuje požadavky na základě umístění žadatele nebo IP adresy.

**Povinná pole**

Operátor | Podporované hodnoty
---------|-----------------
Všechny | Není dostupné.
Geografická shoda | Kódy zemí
Shoda IP adres | IP adresy (oddělené místo)
Ne žádné | Není dostupné.
Neshoda geografického umístění | Kódy zemí
Neshoda IP adres | IP adresy (oddělené místo)

Informace o klíči:

- Použijte notaci CIDR.
- Určete více IP adres nebo bloků IP adres tak, že každý z nich oddělujete jedním mezerou. Příklad:
  - **Příklad protokolu IPv4**: 1.2.3.4 10.20.30.40 odpovídá všem žádostem, které dorazí buď na adresu 1.2.3.4 nebo 10.20.30.40.
  - **Příklad IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 odpovídá všem žádostem, které přicházejí buď z adresy 1:2:3:4:5:6:7:8 nebo 10:20:30:40:50:60:70:80.
- Syntaxe bloku IP adres je základní IP adresa, za kterou následuje lomítko a velikost předpony. Příklad:
  - **Příklad IPv4**: 5.5.5.64/26 odpovídá všem žádostem, které přicházejí z adres 5.5.5.64 prostřednictvím 5.5.5.127.
  - **Příklad IPv6**: 1:2:3:/48 odpovídá všem žádostem, které přicházejí z adres 1:2:3:0:0:0:0:0 až 1:2: ffff: ffff: ffff: ffff: ffff: FFFF.

### <a name="request-body"></a>Text žádosti

**Povinná pole**

Operátor | Text žádosti | Transformace případu
---------|--------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="request-header"></a>Hlavička žádosti

**Povinná pole**
Název záhlaví | Operátor | Hodnota hlavičky | Transformace případu
------------|----------|--------------|---------------
Řetězec | [Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

### <a name="request-method"></a>Request – metoda

**Povinná pole**

Operátor | Podporovaná hodnota
---------|----------------
Rovná se, není rovno | ZÍSKÁNÍ, VYSTAVENÍ, VLOŽENÍ, ODSTRANĚNÍ, HLAVNÍ, MOŽNOSTI, TRASOVÁNÍ

Informace o klíči:

- Obsah uložený v mezipaměti v CDN může generovat jenom metoda GET Request. Všechny ostatní metody žádosti jsou proxy prostřednictvím sítě. 

### <a name="request-protocol"></a>Protokol žádosti

**Povinná pole**

Operátor | Podporovaná hodnota
---------|----------------
Rovná se, není rovno | HTTP, HTTPS

### <a name="request-url"></a>Adresa URL požadavku

**Povinná pole**

Operátor | Adresa URL požadavku | Transformace případu
---------|-------------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

Informace o klíči:

- Při zadávání adresy URL žádosti se ujistěte, že jste zahrnuli informace o protokolu. Například "https://www. [yourdomain]. com "

### <a name="url-file-extension"></a>Přípona souboru adresy URL

**Povinná pole**

Operátor | Linka | Transformace případu
---------|-----------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

Informace o klíči:

- V případě rozšíření Nezahrnovat úvodní období; použijte například HTML namísto. html.

### <a name="url-file-name"></a>Název souboru URL

**Povinná pole**

Operátor | Název souboru | Transformace případu
---------|-----------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

Informace o klíči:

- Chcete-li zadat více názvů souborů, oddělte každý název souboru jediným mezerou. 

### <a name="url-path"></a>Cesta adresy URL

**Povinná pole**

Operátor | Hodnota | Transformace případu
---------|-------|---------------
[Seznam standardních operátorů](#standard-operator-list) | Řetězec, int | Žádná transformace, pro velká a malá písmena

Informace o klíči:

- Hodnota názvu souboru může využít zástupné hodnoty. Například každý vzor názvu souboru může obsahovat jednu nebo více hvězdiček (*), kde každá hvězdička odpovídá sekvenci jednoho nebo více znaků.

## <a name="reference-for-rules-engine-match-conditions"></a>Reference pro podmínky shody stroje pravidel

### <a name="standard-operator-list"></a>Seznam standardních operátorů

Pro pravidla, která obsahují seznam standardních operátorů, platí následující operátory:

- Všechny
- Rovná se 
- Contains 
- Začíná na 
- Končí na 
- Méně než
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

Pro číselné operátory, jako je "menší než" nebo "větší než nebo rovno", bude porovnání použito na základě délky. V takovém případě by hodnota v podmínce shody měla být celé číslo, které se rovná délce, kterou byste chtěli porovnat. 

---

[Zpět na začátek](#match-conditions)

</br>

## <a name="next-steps"></a>Další kroky

- [Přehled služby Azure Content Delivery Network](cdn-overview.md)
- [Referenční informace ke stroji pravidel](cdn-standard-rules-engine-reference.md)
- [Akce modulu pravidel](cdn-standard-rules-engine-actions.md)
- [Vynutilit HTTPS pomocí modulu Standard Rules](cdn-standard-rules-engine.md)
