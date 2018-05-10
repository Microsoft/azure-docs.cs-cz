---
title: Průběh ukládání do mezipaměti | Microsoft Docs
description: Ukládání do mezipaměti je procesu ukládání dat místně, tak, aby budoucí požadavky, data se dají zpřístupnit rychleji.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: v-deasim
ms.openlocfilehash: bb0824995972b49febdb1695e41f45fbd0966cd1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-caching-works"></a>Jak funguje ukládání do mezipaměti

Tento článek obsahuje přehled obecné koncepty ukládání do mezipaměti a jak [Azure Content Delivery Network (CDN)](cdn-overview.md) používá ukládání do mezipaměti ke zlepšení výkonu. Pokud vás zajímají další informace o tom, jak přizpůsobit chování ukládání do mezipaměti na koncový bod CDN, najdete v části [Azure CDN ovládacího prvku s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md) a [řízení Azure CDN ukládání do mezipaměti chování řetězce dotazu](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Úvod k ukládání do mezipaměti

Ukládání do mezipaměti je procesu ukládání dat místně, tak, aby budoucí požadavky, data se dají zpřístupnit rychleji. U většiny běžných typu ukládání do mezipaměti, ukládání do mezipaměti webového prohlížeče, webové nastavení prohlížeče ukládá kopie statických dat místně na místní pevný disk. Pomocí ukládání do mezipaměti webového prohlížeče můžete Vyvarujte se vícenásobný serveru a místo toho přistupují ke stejným datům místně, a ušetřit tak čas a prostředky. Ukládání do mezipaměti je vhodné pro místní správu malé, statické data, jako jsou statické obrázky, soubory šablon stylů CSS a JavaScript soubory.

Podobně ukládání do mezipaměti je používán síti pro doručování obsahu na serverech edge blízko uživatele předejdete požadavky cestách zpět na počátku a sníží se latence koncového uživatele. Na rozdíl od mezipaměti webového prohlížeče, který se používá pouze pro jednoho uživatele, má CDN sdílené mezipaměti. CDN sdílené mezipaměti soubor, který se požaduje jeden uživatel je přístupná později jinými uživateli, což značně snižuje počet požadavků na zdrojový server.

Nelze uložit do mezipaměti dynamické prostředky, které se často mění nebo jsou jedinečné pro jednotlivé uživatele. Těchto typů prostředků, ale můžete využít výhod optimalizace (DSA) acceleration dynamických webů na Azure Content Delivery Network pro zlepšení výkonu.

Ukládání do mezipaměti se může objevit na více úrovních mezi serverem počátečního a koncového uživatele:

- Webový server: používá sdílené mezipaměti (pro více uživatelů).
- Sítě pro doručování obsahu: používá sdílené mezipaměti (pro více uživatelů).
- Poskytovatele internetových služeb (ISP): používá sdílené mezipaměti (pro více uživatelů).
- Webové prohlížeče: používá privátní mezipaměti (pro jednoho uživatele).

Každá mezipaměť obvykle spravuje vlastní aktuálnosti prostředků a provádí ověření v případě, že soubor je zastaralá. Toto chování je definována v HTTP ukládání do mezipaměti specifikace, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Aktuálnosti prostředků

Protože v mezipaměti prostředek může být potenciálně zastaralé nebo zastaralé (ve srovnání se odpovídající prostředek na původním serveru), je důležité pro žádné ukládání do mezipaměti mechanismus řízení při aktualizaci obsahu. Uložit čas a šířky pásma, není v mezipaměti prostředků pokaždé, když se k porovnání s verzí na původním serveru. Tak dlouho, dokud prostředek v mezipaměti se považuje za nový, místo toho se předpokládá, že se jeho nejaktuálnější verzi a je zasílán přímo na klienta. Prostředek v mezipaměti se považuje za čerstvé při jeho stáří je menší než stáří nebo období definované nastavení mezipaměti. Například při prohlížeče znovu načte na webové stránce, se ověří, že všechny uložené v mezipaměti prostředky na pevném disku je nový a ho načte. Pokud prostředek není novou (zastaralé), aktuální kopie je načíst ze serveru.

### <a name="validation"></a>Ověření

Pokud prostředek se považuje za zastaralý, na zdrojový server se zobrazí výzva k ověření, který je, určují, zda data v mezipaměti stále odpovídá, co je na původním serveru. Pokud soubor byl upraven na původním serveru, aktualizuje mezipaměť jeho verzi prostředku. Jinak Pokud prostředek je nový, data doručuje přímo z mezipaměti bez ověřování se nejdřív.

### <a name="cdn-caching"></a>Ukládání do mezipaměti CDN

Ukládání do mezipaměti je nedílnou součástí s používáním název CDN k urychlení doručení a snížit zatížení původu pro statické prostředky, například bitové kopie, písem a videa. Při ukládání do mezipaměti CDN, statické prostředky jsou selektivní uložené na strategicky umístěných servery, které jsou více místní uživatele a nabízí následující výhody:

- Protože většina webových přenosů se o statickou (například obrázky, písem a videa), ukládání do mezipaměti CDN snižuje latence sítě přesunutím obsahu blíže uživateli, proto snižuje vzdálenost, která přenáší data.

- Snižování zátěže, aby název CDN, ukládání do mezipaměti můžete snížit síťových přenosů a zatížení na původním serveru. Tím se snižuje náklady a prostředky požadavky aplikace, i když existuje velký počet uživatelů.

Podobně jako způsob implementace ukládání do mezipaměti ve webovém prohlížeči, můžete řídit způsob ukládání do mezipaměti se provádí v název CDN odesláním mezipaměti direktiva hlavičky. Direktiva mezipaměti hlavičky jsou hlavičky protokolu HTTP, které jsou obvykle přidávány na zdrojový server. I když většina tyto hlavičky byly původně navrženy pro adres ukládání do mezipaměti v prohlížečích klienta, je nyní také používají všechny zprostředkující mezipaměti, jako je například sítím CDN. 

Dvě hlavičky lze použít k definování mezipaměti aktuálnosti: `Cache-Control` a `Expires`. `Cache-Control` aktuálnější a má přednost před `Expires`, pokud obě neexistuje. Existují také dva typy hlaviček použít pro ověření (nazývané validátory): `ETag` a `Last-Modified`. `ETag` aktuálnější a má přednost před `Last-Modified`, pokud obě jsou definovány.  

## <a name="cache-directive-headers"></a>Hlavičky Cache – direktiva

> [!IMPORTANT]
> Ve výchozím nastavení koncový bod Azure CDN, který je optimalizovaná pro DSA ignoruje mezipaměti direktiva hlavičky a obchází ukládání do mezipaměti. Pro **Azure CDN Standard od společnosti Verizon** a **Azure CDN Standard od společnosti Akamai** profilů, můžete upravit způsob, jakým koncového bodu Azure CDN zpracovává tyto hlavičky pomocí [ukládání do mezipaměti CDN pravidla](cdn-caching-rules.md)povolení ukládání do mezipaměti. Pro **Azure CDN Premium od společnosti Verizon** používají pouze profily [stroj pravidel](cdn-rules-engine.md) povolení ukládání do mezipaměti.

Azure CDN podporuje následující hlavičky cache – direktiva protokolu HTTP, které definují sdílení mezipaměti a doba trvání mezipaměti.

**Cache-Control:**
- Počínaje HTTP 1.1 umožnit webové vydavatelů větší kontrolu nad obsah a vyřešit omezení `Expires` záhlaví.
- Přepsání `Expires` záhlaví, pokud ho a `Cache-Control` jsou definovány.
- Při použití v požadavku HTTP od klienta k CDN POP, `Cache-Control` je ignorován v všechny profily Azure CDN, ve výchozím nastavení.
- Při použití v odpovědi HTTP od klienta k CDN POP:
     - **Azure CDN Standard nebo Premium od společnosti Verizon** a **Azure CDN Standard od společnosti Microsoft** podporovat všechny `Cache-Control` direktivy.
     - **Azure CDN Standard od Akamai** podporuje pouze následující `Cache-Control` direktivy; všechny další se ignorují:
         - `max-age`: Mezipaměti můžete ukládat obsah počtu sekund zadaného. Například, `Cache-Control: max-age=5`. Tato direktiva určuje maximální množství času, který obsah se považuje za čerstvý.
         - `no-cache`: Uloží obsah do mezipaměti, ale ověřit obsah pokaždé, když před jeho doručení z mezipaměti. Ekvivalentní `Cache-Control: max-age=0`.
         - `no-store`: Nikdy mezipaměti obsahu. Odebrání obsahu, pokud byl dříve uložené.

**Vypršení platnosti:**
- Starší verze záhlaví zavedené v protokolu HTTP 1.0; podporované pro zpětné kompatibility.
- Čas vypršení platnosti na základě data používá s přesnost pro sekundy. 
- Podobně jako `Cache-Control: max-age`.
- Použít, když `Cache-Control` neexistuje.

**Direktiva pragma:**
   - Ve výchozím nastavení není dodržení pomocí Azure CDN.
   - Starší verze záhlaví zavedené v protokolu HTTP 1.0; podporované pro zpětné kompatibility.
   - Použít jako hlavičku požadavku klienta následující direktivou: `no-cache`. Tato direktiva nastaví serveru k poskytování novou verzi prostředku.
   - `Pragma: no-cache` je ekvivalentní `Cache-Control: no-cache`.

## <a name="validators"></a>Validátory

Pokud mezipaměť je zastaralý, validátory mezipaměti HTTP používají k porovnání uložené v mezipaměti verzi souboru s verzí na původním serveru. **Azure CDN Standard nebo Premium od společnosti Verizon** podporuje obě `ETag` a `Last-Modified` validátory ve výchozím nastavení, zatímco **Azure CDN Standard od společnosti Microsoft** a **Azure CDN Standard od společnosti Akamai** podporuje pouze `Last-Modified` ve výchozím nastavení.

**Značka ETag:**
- **Azure CDN Standard nebo Premium od společnosti Verizon** podporuje `ETag` ve výchozím nastavení, zatímco **Azure CDN Standard od společnosti Microsoft** a **Azure CDN Standard od společnosti Akamai** nepodporují.
- `ETag` Určuje řetězec, který je jedinečný pro každý soubor a verzi souboru. Například, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Počínaje HTTP 1.1 a aktuálnější než `Last-Modified`. To užitečné, pokud je obtížné určit datum poslední změny.
- Podporuje silné ověřování a slabé ověření; Azure CDN však podporuje pouze silné ověřování. Silné ověřování, dva prostředků reprezentace musí být bajt pro bajtu identické. 
- Mezipaměť ověří soubor, který používá `ETag` odesláním `If-None-Match` záhlaví s jedním nebo více `ETag` validátory v požadavku. Například, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Pokud odpovídá verzi serveru `ETag` validátoru v seznamu, odešle stavový kód 304 (upraveno) v odpovědi. Pokud je různé verze, odpoví server se stavovým kódem 200 (OK) a aktualizovaný prostředek.

**Poslední úpravy:**
- Pro **Azure CDN Standard nebo Premium od společnosti Verizon** pouze `Last-Modified` se používá v případě `ETag` není součástí odpověď HTTP. 
- Určuje datum a čas, který na zdrojový server bylo zjištěno, že bylo naposledy změněno prostředku. Například, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Mezipaměť ověří souboru pomocí `Last-Modified` odesláním `If-Modified-Since` záhlaví s datem a časem v požadavku. Zdrojový server porovná datum s `Last-Modified` záhlaví nejnovější prostředku. Pokud od zadané doby nebylo změněno prostředku, server vrátí stavový kód 304 (upraveno) v odpovědi. Pokud byl upraven prostředku, server vrátí stav 200 (OK) a aktualizovaný prostředek kódu.

## <a name="determining-which-files-can-be-cached"></a>Určení, které soubory do mezipaměti

Ne všechny prostředky do mezipaměti. Následující tabulka uvádí, jaké prostředky do mezipaměti, na základě typu odpovědi HTTP. Nelze uložit do mezipaměti zdrojů informací s odpovědí HTTP, které nesplňují všechny tyto podmínky. Pro **Azure CDN Premium od společnosti Verizon** pouze stroj pravidel můžete přizpůsobit některé z těchto podmínek.

|                   | Azure CDN společnosti Microsoft          | Azure CDN společnosti Verizon | Azure CDN společnosti Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Stavové kódy HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Metody HTTP      | GET, HEAD                         | GET                    | GET                          |
| Omezení velikosti souborů  | 300 GB                            | 300 GB                 | -Obecné webové doručení optimalizace: 1,8 GB<br />-Streamování médií optimalizace: 1,8 GB<br />-Optimalizace velkých souborů: 150 GB |

Pro **Azure CDN Standard od společnosti Microsoft** ukládání do mezipaměti, pro práci na prostředku, na zdrojový server musí podporovat všechny HLAVIČKY a požadavky GET HTTP a obsah délka hodnoty musí být stejné pro všechny HLAVIČKY a získat HTTP odpovědi pro asset. Pro žádost HEAD na zdrojový server musí podporovat žádost HEAD a musí odpovědět s hlavičkách stejné, jako kdyby měl obdržel požadavek GET.

## <a name="default-caching-behavior"></a>Výchozí chování ukládání do mezipaměti

Následující tabulka popisuje výchozí chování pro produkty Azure CDN a jejich optimalizace ukládání do mezipaměti.

|    | Společnosti Microsoft: Doručení obecné web | Verizon: Obecné webové doručení | Verizon: DSA | Akamai: Doručení obecné webové | Akamai: DSA | Akamai: Stažení velkých souborů | Akamai: Obecné nebo datové proudy media VOD |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Dodržet počátek**       | Ano    | Ano   | Ne   | Ano    | Ne   | Ano   | Ano    |
| **Doba trvání mezipaměti CDN** | 2 dny |7 dní | Žádný | 7 dní | Žádný | 1 den | 1 rok |

**Respektovat počátek**: Určuje, zda respektovat [podporované hlavičky cache – direktiva](#http-cache-directive-headers) Pokud existují v odpovědi HTTP ze zdrojového serveru.

**Doba trvání mezipaměti CDN**: Určuje množství času, pro které se uloží do mezipaměti prostředek na Azure CDN. Ale pokud **respektovat počátek** Ano a odpovědi HTTP ze zdrojového serveru zahrnuje hlavičku cache – direktiva `Expires` nebo `Cache-Control: max-age`, doba trvání hodnota zadaná v hlavičce místo toho používá Azure CDN. 

## <a name="next-steps"></a>Další postup

- Zjistěte, jak přizpůsobit a přepsat výchozí chování v CDN prostřednictvím ukládání do mezipaměti pravidla ukládání do mezipaměti, najdete v tématu [Azure CDN ovládacího prvku s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md). 
- Další informace o použití řetězce dotazu pro řízení chování ukládání do mezipaměti, najdete v tématu [řízení Azure CDN ukládání do mezipaměti chování řetězce dotazu](cdn-query-string.md).



