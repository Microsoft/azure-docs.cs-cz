---
title: Funguje jak ukládání do mezipaměti | Dokumentace Microsoftu
description: Ukládání do mezipaměti je proces ukládání dat místně tak, aby budoucí žádosti o data může být více rychlý přístup.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 563c073e781e2a2bee88b4ecdcdc82541c21ec4f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092383"
---
# <a name="how-caching-works"></a>Jak funguje ukládání do mezipaměti

Tento článek obsahuje přehled obecných konceptů, ukládání do mezipaměti a jak [Azure Content Delivery Network (CDN)](cdn-overview.md) používá ukládání do mezipaměti ke zlepšení výkonu. Pokud chcete další informace o tom, jak přizpůsobit chování ukládání do mezipaměti na váš koncový bod CDN, najdete v článku [řízení Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md) a [řízení Azure CDN pomocí řetězců dotazu chování ukládání do mezipaměti](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Úvod k ukládání do mezipaměti

Ukládání do mezipaměti je proces ukládání dat místně tak, aby budoucí žádosti o data může být více rychlý přístup. U většiny běžných typu ukládání do mezipaměti, ukládání do mezipaměti webového prohlížeče, webové nastavení prohlížeče ukládá kopie statická data místně na místní pevný disk. Pomocí ukládání do mezipaměti, může webový prohlížeč Vyhněte se vyvozování vícenásobný na server a místo toho přístup ke stejným datům místně, a ušetřit tak čas i prostředky. Ukládání do mezipaměti je velmi vhodná pro místní správu malé, statická data, jako jsou statické obrázky, šablony stylů CSS soubory a soubory jazyka JavaScript.

Podobně ukládání do mezipaměti používá síť pro doručování obsahu na hraničních serverech umístěný blízko uživatele, aby žádosti o cestách zpět k původu a snížení latence koncového uživatele. Na rozdíl od mezipaměti webového prohlížeče, který se používá pouze pro jednoho uživatele, má CDN sdílené mezipaměti. V CDN sdílené mezipaměti soubor, který si vyžádá jeden uživatel je možný později jinými uživateli, výrazně snižuje počet požadavků na zdrojový server.

Dynamické prostředky, které často mění nebo jsou jedinečné pro jednotlivé uživatele nelze uložit do mezipaměti. Tyto typy prostředků, ale mohou využít výhod dynamického webu (DSA) akcelerace optimalizace v Azure Content Delivery Network pro zlepšení výkonu.

Ukládání do mezipaměti dojít na více úrovních mezi zdrojový server a rozhraní koncového uživatele:

- Webový server: používá sdílené mezipaměti (pro více uživatelů).
- Síť content delivery network: používá sdílené mezipaměti (pro více uživatelů).
- Poskytovatele internetových služeb (ISP): pomocí sdílené mezipaměti (pro více uživatelů).
- Webový prohlížeč: používá soukromé mezipaměti (pro jednoho uživatele).

Každá mezipaměť obvykle spravuje svou vlastní prostředek aktuálnosti a provede ověření, když je soubor zastaralé. Toto chování je definována v protokolu HTTP, ukládání do mezipaměti specifikace, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Aktuálnost prostředků

Prostředek v mezipaměti může být potenciálně neaktuální nebo zastaralý (porovnání s odpovídající prostředek na původním serveru), proto je důležité pro každý použitý mechanizmus ukládání do mezipaměti na ovládací prvek při aktualizaci obsahu. Chcete-li ušetřit čas a šířky pásma, není prostředek v mezipaměti porovnání s verzí na původním serveru pokaždé, když je přístupný. Tak dlouho, dokud prostředek v mezipaměti, se považuje za čerstvé, místo toho se předpokládá, že se její nejnovější verze a se pošle přímo klientovi. Prostředek v mezipaměti se považuje za novou při jeho věk je menší než stáří nebo doby stanovené nastavení mezipaměti. Například když prohlížeč znovu načte webové stránky, se ověří, zda jednotlivé prostředky uložené v mezipaměti na pevném disku dokud a ho načte. Pokud prostředek není aktuální (zastaralé), aktuální kopii je ze serveru načíst.

### <a name="validation"></a>Ověření

Pokud prostředek, se považuje za zastaralý, zdrojový server se výzva k ověření, to znamená, zjistit, zda data v mezipaměti i nadále odpovídá, co je na původním serveru. Pokud soubor byl změněn na původním serveru, mezipaměť aktualizuje svou verzi prostředku. V opačném případě Pokud prostředek je čerstvé, data se doručí přímo z mezipaměti bez ověřování se nejdřív.

### <a name="cdn-caching"></a>Ukládání do mezipaměti CDN

Ukládání do mezipaměti je nedílnou součástí způsobu, jakým CDN funguje zrychlit doručování a snížit zatížení původu pro statické prostředky, jako jsou obrázky, písma a videa. V ukládání do mezipaměti CDN, statické prostředky selektivně ukládají na strategicky umístěných servery, které jsou více místní uživatele a nabízí následující výhody:

- Protože většina webového provozu je statická (pro příklad, obrázky, písma a videa), ukládání do mezipaměti CDN snižuje latence sítě přesunutím obsahu blíže na uživatele, čímž se sníží vzdálenost přenášená data.

- Snižování zátěže práci do sítě CDN, ukládání do mezipaměti můžete snížit síťových přenosů a zatížení na původním serveru. Tím se snižuje náklady a prostředků požadavky na aplikace, i když existuje velký počet uživatelů.

Podobně jako způsob ukládání do mezipaměti je implementováno ve webovém prohlížeči, můžete určit, jak ukládání do mezipaměti se provádí v CDN odesíláním hlaviček direktiv pro mezipaměť. Hlaviček direktiv pro mezipaměť jsou hlavičky protokolu HTTP, které jsou obvykle přidány pomocí zdrojového serveru. I když většina tyto hlavičky byly původně navržená k řešení ukládání do mezipaměti v prohlížečích klienta, je teď také používají všechny zprostředkující mezipaměti, jako je například sítě CDN. 

Dvě záhlaví lze použít k definování mezipaměti čerstvosti: `Cache-Control` a `Expires`. `Cache-Control` aktuálnější a má přednost před `Expires`, pokud obě existovat. Také existují dva typy hlaviček použité k ověření (označované jako validátory): `ETag` a `Last-Modified`. `ETag` aktuálnější a má přednost před `Last-Modified`, pokud obě jsou definovány.  

## <a name="cache-directive-headers"></a>Hlaviček direktiv pro mezipaměť

> [!IMPORTANT]
> Ve výchozím nastavení koncového bodu Azure CDN, která je optimalizovaná pro DSA ignoruje hlaviček direktiv pro mezipaměť a obchází ukládání do mezipaměti. Pro **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profilů, můžete upravit způsob, jakým koncového bodu Azure CDN zpracovává tyto hlavičky pomocí [ukládání do mezipaměti CDN pravidla](cdn-caching-rules.md)povolení ukládání do mezipaměti. Pro **Azure CDN Premium od Verizonu** pouze profily, je použít [stroj pravidel](cdn-rules-engine.md) povolení ukládání do mezipaměti.

Azure CDN podporuje následující hlavičky direktiv pro mezipaměť protokolu HTTP, které definují dobu mezipaměti a sdílení mezipaměti.

**Cache-Control:**
- Počínaje HTTP 1.1 poskytují větší kontrolu nad jejich obsah webového vydavatele a adresu omezení `Expires` záhlaví.
- Přepsání `Expires` záhlaví, pokud ho a `Cache-Control` jsou definovány.
- Pokud se použije v požadavku HTTP od klienta k CDN POP, `Cache-Control` se ignoruje všechny profily Azure CDN, ve výchozím nastavení.
- Při použití v odpovědi HTTP od klienta k CDN POP:
     - **Azure CDN Standard nebo Premium od Verizonu** a **Azure CDN Standard od společnosti Microsoft** podporují všechny `Cache-Control` direktivy.
     - **Azure CDN Standard od Akamai** podporuje pouze následující `Cache-Control` direktivy; všechny další se ignorují:
         - `max-age`: Počet sekund zadaného mezipaměť můžete ukládat obsah. Například, `Cache-Control: max-age=5`. Tato direktiva určuje maximální množství času, které se považuje za čerstvý obsah.
         - `no-cache`: Obsah do mezipaměti, ale ověřit obsah pokaždé, když se před doručením z mezipaměti. Ekvivalentní `Cache-Control: max-age=0`.
         - `no-store`: Nikdy mezipaměť obsahu. Odeberte obsah, pokud byla dříve uložena.

**Vypršení platnosti:**
- Starší verze záhlaví zavedené v HTTP 1.0; podporováno pro zpětnou kompatibilitu.
- Používá čas na základě datum vypršení platnosti se přesnost pro sekundy. 
- Podobně jako `Cache-Control: max-age`.
- Kdy použít `Cache-Control` neexistuje.

**Direktivy pragma:**
   - Není kompilátorem respektovány Azure CDN ve výchozím nastavení.
   - Starší verze záhlaví zavedené v HTTP 1.0; podporováno pro zpětnou kompatibilitu.
   - Použít jako hlavičku požadavku klienta s následující direktiva: `no-cache`. Tato direktiva nastaví server vydat novou verzi prostředku.
   - `Pragma: no-cache` je ekvivalentní `Cache-Control: no-cache`.

## <a name="validators"></a>Validátory

Po zastaralá mezipaměť validátory mezipaměti HTTP slouží k porovnání verze uložené v mezipaměti soubor s verzí na původním serveru. **Azure CDN Standard nebo Premium od Verizonu** podporuje obě `ETag` a `Last-Modified` validátory ve výchozím nastavení, zatímco **Azure CDN Standard od společnosti Microsoft** a **Azure CDN Standard od Akamai** podporuje pouze `Last-Modified` ve výchozím nastavení.

**Značka ETag:**
- **Azure CDN Standard nebo Premium od Verizonu** podporuje `ETag` ve výchozím nastavení, zatímco **Azure CDN Standard od společnosti Microsoft** a **Azure CDN Standard od Akamai** nepodporují.
- `ETag` Definuje řetězec, který je jedinečný pro každý soubor a verze souboru. Například, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Počínaje HTTP 1.1 a je novější než `Last-Modified`. Užitečné, pokud je obtížné určit datum poslední změny.
- Podporuje ověřování silné a slabé ověřování; Azure CDN však podporuje pouze silné ověřování. Silné ověřování, musí být reprezentace dvou prostředků bajt po bajtu identické. 
- Ověřuje soubor, který používá mezipaměť `ETag` odesláním `If-None-Match` záhlaví s jednou nebo více `ETag` validátory v požadavku. Například, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Pokud se shoduje s verzí serveru `ETag` program pro ověření v seznamu, odešle stavovým kódem 304 (Neupraveno) v odpovědi. Pokud verze se liší, server jako odpověď vrátí stavový kód 200 (OK) a aktualizovaného prostředku.

**Poslední změna:**
- Pro **Azure CDN Standard nebo Premium od Verizonu** pouze `Last-Modified` se používá v případě `ETag` není součástí odpovědi HTTP. 
- Určuje datum a čas, který je zdrojový server určil, že poslední změny prostředku. Například, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Mezipaměť ověřuje soubor pomocí `Last-Modified` odesláním `If-Modified-Since` záhlaví s datem a časem v požadavku. Zdrojový server porovnává data se `Last-Modified` záhlaví nejnovější prostředku. Pokud prostředek nebyl změněn od zadaného času, server vrátí stavový kód 304 (Neupraveno) v odpovědi. Pokud prostředek byl změněn, server vrátí stavový kód 200 (OK) a aktualizovaného prostředku.

## <a name="determining-which-files-can-be-cached"></a>Určení souborů, které lze uložit do mezipaměti

Ne všechny prostředky můžete uložit do mezipaměti. V následující tabulce jsou uvedeny prostředky můžete uložit do mezipaměti, na základě typu odpověď HTTP. Nelze uložit do mezipaměti materiálů s odpovědí HTTP, které nesplňují všechny tyto podmínky. Pro **Azure CDN Premium od Verizonu** pouze stroj pravidel můžete přizpůsobit některé z těchto podmínek.

|                   | Azure CDN od společnosti Microsoft          | Azure CDN od Verizonu | Azure CDN od Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Stavové kódy HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Metody HTTP      | GET, HEAD                         | GET                    | GET                          |
| Omezení velikosti souborů  | 300 GB                            | 300 GB                 | -Optimalizace obecného doručování webu: 1,8 GB<br />-Streamování médií optimalizace: 1,8 GB<br />-Optimalizace velkých souborů: 150 GB |

Pro **Azure CDN Standard od společnosti Microsoft** ukládání do mezipaměti pro práci na prostředek, je zdrojový server musí podporovat všechny hlavní a požadavky GET HTTP a hodnoty content-length musí být stejný pro všechny hlavní a GET HTTP odpovědí pro prostředek. Pro požadavek HEAD zdrojový server musí podporovat požadavek HEAD a musí odpovědět s záhlaví stejné, jako by se v minulosti obdržel požadavek GET.

## <a name="default-caching-behavior"></a>Výchozí chování ukládání do mezipaměti

Následující tabulka popisuje výchozí chování pro produkty Azure CDN a jejich optimalizace ukládání do mezipaměti.

|    | Společnosti Microsoft: Obecné doručování webu | Verizon: Obecné doručování webu | Verizon: DSA | Akamai: Obecné doručování webu | Akamai: DSA | Akamai: Stahování velkých souborů | Akamai: Obecné nebo streamování médií videa na Vyžádání |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Případném dalším sdílení dodržovat původu**       | Ano    | Ano   | Ne   | Ano    | Ne   | Ano   | Ano    |
| **Doba trvání mezipaměti CDN** | 2 dny |7 dní | Žádný | 7 dní | Žádný | 1 den | 1 rok |

**Případném dalším sdílení dodržovat původu**: Určuje, zda případném dalším sdílení dodržovat [podporované hlaviček direktiv pro mezipaměť](#http-cache-directive-headers) Pokud ovšem existují v odpovědi HTTP ze zdrojového serveru.

**Doba trvání mezipaměti CDN**: Určuje množství času, u kterého se uloží do mezipaměti prostředek ve službě Azure CDN. Ale pokud **případném dalším sdílení dodržovat původu** Ano a odpovědi HTTP ze zdrojového serveru zahrnuje hlaviček direktiv pro mezipaměť `Expires` nebo `Cache-Control: max-age`, doba trvání hodnota zadaná v hlavičce místo toho používá Azure CDN. 

## <a name="next-steps"></a>Další postup

- Zjistěte, jak přizpůsobit a přepsat výchozí chování v síti CDN prostřednictvím ukládání do mezipaměti pravidla ukládání do mezipaměti, naleznete v tématu [řízení Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md). 
- Zjistěte, jak pomocí řetězců dotazu do řízení chování ukládání do mezipaměti, naleznete v tématu [řízení Azure CDN pomocí řetězců dotazu chování ukládání do mezipaměti](cdn-query-string.md).



