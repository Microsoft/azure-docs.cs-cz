---
title: Jak funguje ukládání do mezipaměti | Dokumenty společnosti Microsoft
description: Ukládání do mezipaměti je proces ukládání dat místně tak, aby budoucí požadavky pro tato data lze přistupovat rychleji.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: d0c438aee7f56e96feb7167fad718fd9519a9f76
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253709"
---
# <a name="how-caching-works"></a>Jak funguje ukládání do mezipaměti

Tento článek obsahuje přehled obecných konceptů ukládání do mezipaměti a jak [síť pro doručování obsahu Azure (CDN)](cdn-overview.md) používá ukládání do mezipaměti ke zlepšení výkonu. Pokud se chcete dozvědět o tom, jak přizpůsobit chování ukládání do mezipaměti v koncovém bodě CDN, přečtěte [si část Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](cdn-caching-rules.md) a Řízení chování [mezipaměti Azure CDN pomocí řetězců dotazu](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Úvod do ukládání do mezipaměti

Ukládání do mezipaměti je proces ukládání dat místně tak, aby budoucí požadavky pro tato data lze přistupovat rychleji. V nejběžnějším typu ukládání do mezipaměti, ukládání webového prohlížeče do mezipaměti, webový prohlížeč ukládá kopie statických dat místně na místním pevném disku. Pomocí ukládání do mezipaměti se webový prohlížeč může vyhnout provádění více kruhových cest na server a místo toho přistupovat ke stejným datům místně, čímž šetří čas a prostředky. Ukládání do mezipaměti je vhodné pro místní správu malých statických dat, jako jsou statické obrázky, soubory CSS a soubory JavaScript.

Podobně ukládání do mezipaměti používá síť pro doručování obsahu na hraničních serverech v blízkosti uživatele, aby se zabránilo požadavkům, které cestují zpět do původu a snižují latenci koncového uživatele. Na rozdíl od mezipaměti webového prohlížeče, která se používá pouze pro jednoho uživatele, má CDN sdílenou mezipaměť. Ve sdílené mezipaměti CDN může být soubor požadovaný jedním uživatelem později přístupný ostatním uživatelům, což výrazně snižuje počet požadavků na zdrojový server.

Dynamické prostředky, které se často mění nebo jsou jedinečné pro jednotlivé uživatele, nelze uložit do mezipaměti. Tyto typy prostředků však můžete využít dynamické akcelerace webu (DSA) optimalizace v síti doručování obsahu Azure pro zlepšení výkonu.

Ukládání do mezipaměti může probíhat na více úrovních mezi zdrojovým serverem a koncovým uživatelem:

- Webový server: Používá sdílenou mezipaměť (pro více uživatelů).
- Síť pro doručování obsahu: Používá sdílenou mezipaměť (pro více uživatelů).
- Poskytovatel služeb Internetu (ISP): Používá sdílenou mezipaměť (pro více uživatelů).
- Webový prohlížeč: Používá privátní mezipaměť (pro jednoho uživatele).

Každá mezipaměť obvykle spravuje vlastní aktuálnost prostředků a provádí ověření, když je soubor zastaralý. Toto chování je definováno ve specifikaci ukládání do mezipaměti PROTOKOLU [HTTP RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Čerstvost zdrojů

Vzhledem k tomu, že prostředek uložený v mezipaměti může být potenciálně zastaralý nebo zastaralý (ve srovnání s odpovídajícím prostředkem na zdrojovém serveru), je důležité, aby jakýkoli mechanismus ukládání do mezipaměti řídil při aktualizaci obsahu. Chcete-li ušetřit čas a spotřebu šířky pásma, prostředek uložený v mezipaměti není porovnán s verzí na zdrojovém serveru při každém přístupu. Místo toho, dokud prostředek uložený v mezipaměti je považován za čerstvé, předpokládá se, že je nejaktuálnější verze a je odeslána přímo klientovi. Prostředek uložený v mezipaměti je považován za čerstvý, pokud je jeho stáří menší než stáří nebo období definované nastavením mezipaměti. Pokud například prohlížeč znovu načte webovou stránku, ověří, zda je každý prostředek uložený v mezipaměti na pevném disku čerstvý, a načte jej. Pokud prostředek není aktuální (zastaralé), aktuální kopie je načten ze serveru.

### <a name="validation"></a>Ověřování

Pokud je prostředek považován za zastaralý, je zdrojový server požádán o jeho ověření, to znamená, zda data v mezipaměti stále odpovídají tomu, co je na zdrojovém serveru. Pokud byl soubor změněn na zdrojovém serveru, mezipaměť aktualizuje svou verzi prostředku. V opačném případě, pokud je prostředek čerstvý, data jsou doručována přímo z mezipaměti bez ověření.

### <a name="cdn-caching"></a>Ukládání do mezipaměti CDN

Ukládání do mezipaměti je nedílnou součástí fungování sítě CDN, která urychluje doručování a snižuje počáteční zatížení statických datových zdrojů, jako jsou obrázky, písma a videa. V ukládání do mezipaměti CDN jsou statické prostředky selektivně uloženy na strategicky umístěných serverech, které jsou pro uživatele více místní a nabízejí následující výhody:

- Vzhledem k tomu, že většina webového provozu je statická (například obrázky, písma a videa), ukládání do mezipaměti CDN snižuje latenci sítě přesunutím obsahu blíže k uživateli, čímž se snižuje vzdálenost, kterou data ubíjejí.

- Převedením práce na cdn může ukládání do mezipaměti snížit zatížení serveru původu. Tím se sníží náklady a požadavky na prostředky pro aplikaci, i když existuje velký počet uživatelů.

Podobně jako ukládání do mezipaměti je implementováno ve webovém prohlížeči, můžete řídit, jak se provádí ukládání do mezipaměti v CDN odesláním hlavičky cache direktivy. Hlavičky direktivy mezipaměti jsou hlavičky HTTP, které jsou obvykle přidány zdrojovým serverem. Ačkoli většina těchto záhlaví byla původně navržena tak, aby řešila ukládání do mezipaměti v klientských prohlížečích, jsou nyní také používány všemi mezilehlými mezipamětmi, například sítěmi CDN. 

Dvě záhlaví lze použít k definování `Cache-Control` čerstvosti mezipaměti: a `Expires`. `Cache-Control`je aktuálnější a má `Expires`přednost před , pokud existují obě. Existují také dva typy hlaviček používaných pro ověření `ETag` `Last-Modified`(nazývané validátory): a . `ETag`je aktuálnější a má `Last-Modified`přednost před , pokud jsou definovány obě.  

## <a name="cache-directive-headers"></a>Záhlaví direktivy mezipaměti

> [!IMPORTANT]
> Ve výchozím nastavení koncový bod Azure CDN, který je optimalizovaný pro DSA ignoruje hlavičky direktivy mezipaměti a obchází ukládání do mezipaměti. U **Azure CDN Standard od Verizonu** a Azure CDN Standard z profilů **Akamai** můžete upravit, jak koncový bod Azure CDN zachází s těmito hlavičkami pomocí [pravidel ukládání do mezipaměti CDN,](cdn-caching-rules.md) která umožňují ukládání do mezipaměti. Pro **Azure CDN Premium od Verizon** profily pouze pomocí [modulu pravidel](cdn-rules-engine.md) povolit ukládání do mezipaměti.

Azure CDN podporuje následující hlavičky direktivy mezipaměti HTTP, které definují dobu trvání mezipaměti a sdílení mezipaměti.

**Řízení mezipaměti:**
- Zavedeno v protokolu HTTP 1.1, aby weboví vydavatelé měli větší `Expires` kontrolu nad svým obsahem a řešili omezení záhlaví.
- Přepíše `Expires` záhlaví, pokud je `Cache-Control` definováno i je definováno.
- Při použití v požadavku HTTP z klienta `Cache-Control` na CDN POP, je ignorována všechny profily Azure CDN, ve výchozím nastavení.
- Při použití v odpovědi HTTP z klienta na CDN POP:
     - **Azure CDN Standard/Premium od Verizonu** a Azure `Cache-Control` **CDN Standard od Microsoftu** podporují všechny direktivy.
     - **Azure CDN Standard od Akamai** podporuje pouze následující `Cache-Control` direktivy; všechny ostatní jsou ignorovány:
         - `max-age`: Mezipaměť může ukládat obsah po zadaný počet sekund. Například, `Cache-Control: max-age=5`. Tato směrnice stanoví maximální dobu, po kterou je obsah považován za čerstvý.
         - `no-cache`: Ukládat obsah do mezipaměti, ale před doručením obsahu z mezipaměti jej ověřte pokaždé. Odpovídá `Cache-Control: max-age=0`.
         - `no-store`: Nikdy neukládat obsah do mezipaměti. Odeberte obsah, pokud byl dříve uložen.

**Vyprší:**
- Starší záhlaví zavedené v protokolu HTTP 1.0; podporována pro zpětnou kompatibilitu.
- Používá čas vypršení platnosti na základě data s druhou přesností. 
- Podobně `Cache-Control: max-age`jako .
- Používá `Cache-Control` se, když neexistuje.

**Pragma:**
   - Není ve výchozím nastavení oceněna službou Azure CDN.
   - Starší záhlaví zavedené v protokolu HTTP 1.0; podporována pro zpětnou kompatibilitu.
   - Používá se jako hlavička požadavku `no-cache`klienta s následující direktivou: . Tato směrnice dává serveru pokyn k dodání nové verze prostředku.
   - `Pragma: no-cache`je ekvivalentní `Cache-Control: no-cache`.

## <a name="validators"></a>Validátory

Pokud je mezipaměť zastaralá, validátory mezipaměti HTTP se používají k porovnání verze souboru uložené v mezipaměti s verzí na zdrojovém serveru. **Azure CDN Standard/Premium od Verizonu** podporuje ve výchozím nastavení `ETag` i `Last-Modified` validátory, zatímco Azure **CDN Standard od Microsoftu** a Azure **CDN Standard od Akamai** podporuje jenom `Last-Modified` ve výchozím nastavení.

**Etag:**
- **Azure CDN Standard/Premium od Verizonu** podporuje `ETag` ve výchozím nastavení, zatímco **Azure CDN Standard od Microsoftu** a Azure **CDN Standard od Akamai** ne.
- `ETag`definuje řetězec, který je jedinečný pro každý soubor a verzi souboru. Například, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Zavedeno v PROTOKOLU HTTP 1.1 `Last-Modified`a je aktuálnější než . Užitečné při poslední změně datum je obtížné určit.
- Podporuje silné ověřování i slabé ověřování; Azure CDN však podporuje pouze silné ověření. Pro silné ověření musí být dvě reprezentace prostředků identické bajt pro bajt. 
- Mezipaměť ověří soubor, `ETag` který `If-None-Match` se používá odesláním záhlaví s jedním nebo více `ETag` validátory v požadavku. Například, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Pokud verze serveru odpovídá `ETag` validátoru v seznamu, odešle stavový kód 304 (Nezměněno) v jeho odpovědi. Pokud je verze odlišná, server odpoví stavovým kódem 200 (OK) a aktualizovaným zdrojem.

**Poslední změna:**
- Pro **Azure CDN Standard/Premium od Verizonu** se používá, `Last-Modified` pokud `ETag` není součástí odpovědi HTTP. 
- Určuje datum a čas, kdy zdrojový server určil, že prostředek byl naposledy změněn. Například, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Mezipaměť ověří soubor `Last-Modified` pomocí `If-Modified-Since` odesláním záhlaví s datem a časem v požadavku. Zdrojový server porovná toto `Last-Modified` datum s hlavičkou nejnovějšího prostředku. Pokud prostředek nebyl změněn od zadaného času, server vrátí stavový kód 304 (Nezměněno) ve své odpovědi. Pokud byl prostředek změněn, server vrátí stavový kód 200 (OK) a aktualizovaný prostředek.

## <a name="determining-which-files-can-be-cached"></a>Určení souborů, které mohou být uloženy do mezipaměti

Ne všechny prostředky lze uložit do mezipaměti. V následující tabulce je uvedeno, jaké prostředky lze uložit do mezipaměti na základě typu odpovědi HTTP. Prostředky dodané s odpověďmi HTTP, které nesplňují všechny tyto podmínky, nelze uložit do mezipaměti. Pro **Azure CDN Premium od Verizonu** můžete použít modul pravidel k přizpůsobení některých z těchto podmínek.

|                   | Azure CDN od Microsoftu          | Azure CDN od společnosti Verizon | Azure CDN od Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Stavové kódy HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Metody HTTP      | GET, HLAVA                         | GET                    | GET                          |
| Omezení velikosti souboru  | 300 GB                            | 300 GB                 | - Obecná optimalizace doručování webových stránek: 1,8 GB<br />- Optimalizace streamování médií: 1,8 GB<br />- Optimalizace velkých souborů: 150 GB |

Aby **azure cdn standard od Microsoftu** ukládání do mezipaměti pracovat na prostředek, zdrojový server musí podporovat všechny požadavky HEAD a GET HTTP a hodnoty délky obsahu musí být stejné pro všechny odpovědi HEAD a ZÍSKAT HTTP pro prostředek. Pro požadavek HEAD musí zdrojový server podporovat požadavek HEAD a musí reagovat stejnými hlavičkami, jako by obdržel požadavek GET.

## <a name="default-caching-behavior"></a>Výchozí chování ukládání do mezipaměti

Následující tabulka popisuje výchozí chování ukládání do mezipaměti pro produkty Azure CDN a jejich optimalizace.

|    | Microsoft: Obecné doručování na webu | Verizon: Obecné doručování webových stránek | Verizon: DSA | Akamai: Obecné doručování webových stránek | Akamai: DSA | Akamai: Stahování velkých souborů | Akamai: obecné nebo VOD streamování médií |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Původ cti**       | Ano    | Ano   | Ne   | Ano    | Ne   | Ano   | Ano    |
| **Doba trvání mezipaměti CDN** | 2 dny |7 dní | Žádný | 7 dní | Žádný | 1 den | 1 rok |

**Původ cti**: Určuje, zda mají být dodržet podporované hlavičky direktivy mezipaměti, pokud existují v odpovědi HTTP z původního serveru.

**Doba trvání mezipaměti CDN**: Určuje dobu, po kterou je prostředek uložen do mezipaměti v azure cdn. Pokud je však **původ cti** ano a odpověď HTTP z `Expires` původního serveru obsahuje hlavičku direktivy mezipaměti nebo `Cache-Control: max-age`, Azure CDN místo toho použije hodnotu doby trvání určenou hlavičkou. 

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak přizpůsobit a přepsat výchozí chování ukládání do mezipaměti na CDN prostřednictvím pravidel ukládání do mezipaměti, naleznete [v tématu Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](cdn-caching-rules.md). 
- Informace o použití řetězců dotazu k řízení chování ukládání do mezipaměti naleznete v [tématu Řízení chování mezipaměti Azure CDN pomocí řetězců dotazu](cdn-query-string.md).



