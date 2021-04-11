---
title: Jak funguje ukládání do mezipaměti | Microsoft Docs
description: Ukládání dat do mezipaměti je proces místního ukládání dat, aby bylo možné k budoucím požadavkům na tato data získávat rychleji.
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
ms.openlocfilehash: 7a4688c196551f3ab6b5713d8939f53af161d1e3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505004"
---
# <a name="how-caching-works"></a>Jak funguje ukládání do mezipaměti

Tento článek obsahuje přehled obecných konceptů mezipaměti a způsob, jakým [Azure Content Delivery Network (CDN)](cdn-overview.md) využívá ukládání do mezipaměti pro zlepšení výkonu. Pokud se chcete dozvědět, jak přizpůsobit chování ukládání do mezipaměti u koncového bodu CDN, přečtěte si téma [řízení Azure CDN chování ukládání do mezipaměti s pravidly ukládání do](cdn-caching-rules.md) mezipaměti a [Azure CDN řízení chování při ukládání do mezipaměti pomocí řetězců dotazu](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Úvod do ukládání do mezipaměti

Ukládání dat do mezipaměti je proces místního ukládání dat, aby bylo možné k budoucím požadavkům na tato data získávat rychleji. V rámci nejběžnějšího typu ukládání do mezipaměti webový prohlížeč ukládá do mezipaměti webový prohlížeč kopie statických dat místně na místním pevném disku. Při použití ukládání do mezipaměti může webový prohlížeč zabránit tomu, aby na server prováděl více přenosových cest, a místo toho měl přístup ke stejným datům místně a šetří tak čas a prostředky. Ukládání do mezipaměti je vhodné pro místní správu malých, statických dat, jako jsou statické obrázky, soubory CSS a soubory JavaScriptu.

Podobně je ukládání do mezipaměti používáno sítí Content Delivery Network na hraničních serverech blízko uživatele, aby nedocházelo k tomu, že se požadavky cestují zpátky na počátek a snížila latence koncového uživatele. Na rozdíl od mezipaměti webového prohlížeče, která se používá jenom pro jednoho uživatele, CDN má sdílenou mezipaměť. Ve sdílené mezipaměti CDN může být k souboru, který požaduje jeden uživatel, později přihlášený dalšími uživateli, což významně snižuje počet požadavků na zdrojový server.

Dynamické prostředky, které se často mění nebo jsou jedinečné pro jednotlivé uživatele, nelze ukládat do mezipaměti. Tyto typy prostředků ale můžou využít optimalizaci dynamické akcelerace webu (DSA) na Azure Content Delivery Network pro zvýšení výkonu.

K ukládání do mezipaměti může docházet na více úrovních mezi zdrojovým serverem a koncovým uživatelem:

- Webový server: používá sdílenou mezipaměť (pro více uživatelů).
- Content Delivery Network: používá sdílenou mezipaměť (pro více uživatelů).
- Poskytovatel internetových služeb (ISP): používá sdílenou mezipaměť (pro více uživatelů).
- Webový prohlížeč: používá soukromou mezipaměť (pro jednoho uživatele).

Každá mezipaměť obvykle spravuje svoji vlastní aktuálnost prostředků a provádí ověření, když je soubor zastaralý. Toto chování je definováno ve specifikaci mezipaměti protokolu HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Aktuálnost prostředků

Vzhledem k tomu, že prostředek uložený v mezipaměti může být zastaralý nebo zastaralý (ve srovnání s odpovídajícím prostředkem na zdrojovém serveru), je důležité pro jakýkoliv mechanismus ukládání do mezipaměti, který by měl řídit, kdy se obsah aktualizuje. Pokud chcete ušetřit čas a spotřebu šířky pásma, prostředek uložený v mezipaměti není ve srovnání s verzí na zdrojovém serveru pokaždé, když se k němu přistupoval. Místo toho, pokud je prostředek v mezipaměti považován za čerstvý, předpokládá se, že bude aktuální verze a pošle se přímo klientovi. Prostředek uložený v mezipaměti je považován za čerstvý, pokud je jeho stáří menší než stáří nebo období definované nastavením mezipaměti. Například když prohlížeč znovu načte webovou stránku, ověří, že každý prostředek v mezipaměti na pevném disku je v čerstvém stavu a načte ho. Pokud prostředek není v čerstvém stavu (zastaralý), načte se aktuální kopie ze serveru.

### <a name="validation"></a>Ověřování

Pokud se prostředek považuje za zastaralý, je původní server požádán o jeho ověření. to znamená, zda se data v mezipaměti stále shodují s tím, co se nachází na zdrojovém serveru. Pokud byl soubor změněn na zdrojovém serveru, mezipaměť aktualizuje jeho verzi. V opačném případě platí, že pokud je prostředek nový, data se doručí přímo z mezipaměti bez jejich ověření.

### <a name="cdn-caching"></a>Ukládání do mezipaměti CDN

Ukládání do mezipaměti je nedílnou součástí způsobu, jakým síť CDN pracuje na urychlení doručování a snižování zatížení zdroje pro statické prostředky, jako jsou obrázky, písma a videa. Ve službě CDN Caching jsou statické prostředky selektivně uložené na strategicky umístěných serverech, které jsou pro uživatele více místní, a nabízí následující výhody:

- Vzhledem k tomu, že většina webových přenosů je statická (například obrázky, písma a videa), ukládání do mezipaměti CDN snižuje latenci sítě přesunutím obsahu blíž na uživatele, čímž se zkrátí vzdálenost přenášená daty.

- Při přesměrování práce na síť CDN může ukládání do mezipaměti snížit zatížení sítě a zatížení na zdrojovém serveru. Tím se omezí náklady a požadavky na prostředky pro aplikaci, a to i v případě, že existuje velký počet uživatelů.

Podobně jako při implementaci ukládání do mezipaměti ve webovém prohlížeči můžete řídit, jak se mezipaměť provádí v síti CDN odesláním hlaviček cache-direktivy. Hlavičky cache-direktiv jsou hlavičky protokolu HTTP, které jsou obvykle přidané serverem původu. I když většina z těchto hlaviček byla navržena tak, aby v klientských prohlížečích vyřešila ukládání do mezipaměti, jsou teď používána i všemi zprostředkujícími mezipamětmi, jako je sítě CDN. 

K definování aktuálnosti mezipaměti lze použít dvě hlavičky: `Cache-Control` a `Expires` . `Cache-Control` je větší než aktuální a má přednost před `Expires` , pokud oba existují. K ověřování se používají také dva typy hlaviček (nazývané validátory): `ETag` a `Last-Modified` . `ETag` je větší než aktuální a má přednost před `Last-Modified` , pokud jsou obě definovány.  

## <a name="cache-directive-headers"></a>Hlavičky cache-direktivy

> [!IMPORTANT]
> Ve výchozím nastavení je Azure CDN koncový bod optimalizovaný pro DSA ignorovat hlavičky cache-direktiv a obejít ukládání do mezipaměti. Pro **Azure CDN Standard od Verizon** a **Azure CDN Standard od profilů Akamai** můžete upravit způsob, jakým Azure CDN koncový bod zpracovává tyto hlavičky pomocí [pravidel ukládání do mezipaměti CDN](cdn-caching-rules.md) pro povolení ukládání do mezipaměti. Pro **Azure CDN Premium jenom ze profilů Verizon** použijte [modul pravidel](./cdn-verizon-premium-rules-engine.md) k povolení ukládání do mezipaměti.

Azure CDN podporuje následující hlavičky HTTP cache-direktivy, které definují dobu trvání mezipaměti a sdílení mezipaměti.

**Řízení mezipaměti:**
- Představena v HTTP 1,1, která webovým vydavatelům poskytují větší kontrolu nad jejich obsahem a řeší omezení `Expires` záhlaví.
- Přepíše `Expires` hlavičku, pokud je `Cache-Control` definována a.
- Při použití v požadavku protokolu HTTP z klienta do služby CDN POP `Cache-Control` se ve výchozím nastavení ignorují všechny profily Azure CDN.
- Při použití v odpovědi HTTP z klienta na POP CDN:
     - **Azure CDN Standard/Premium z Verizon** a **Azure CDN Standard od společnosti Microsoft** podporují všechny `Cache-Control` direktivy.
     - **Azure CDN Standard/Premium z Verizon** a **Azure CDN Standard od Microsoftu** dodržuje chování ukládání do mezipaměti pro Cache-Control direktiv v [dokumentu RFC 7234-Hypertext Transfer Protocol (http/1.1): ukládání do mezipaměti (IETF.org)](https://tools.ietf.org/html/rfc7234#section-5.2.2.8).
     - **Azure CDN Standard z Akamai** podporuje pouze následující `Cache-Control` direktivy. všechny ostatní jsou ignorovány:
         - `max-age`: Mezipaměť může ukládat obsah za zadaný počet sekund. Například, `Cache-Control: max-age=5`. Tato direktiva určuje maximální dobu, po kterou je obsah považován za čerstvý.
         - `no-cache`: Obsah ukládat do mezipaměti, ale před jeho odesláním z mezipaměti ověřte jeho obsah. Ekvivalent `Cache-Control: max-age=0` .
         - `no-store`: Nikdy neukládat obsah do mezipaměti. Odeberte obsah, pokud byl dříve uložen.

**Expires**
- Starší verze hlavičky představená v HTTP 1,0; podporováno pro zpětnou kompatibilitu.
- Používá čas vypršení platnosti založený na datu s druhou přesností. 
- Podobně jako `Cache-Control: max-age` .
- Používá se `Cache-Control` , když neexistuje.

**Pragma**
   - Nedodržuje Azure CDN ve výchozím nastavení.
   - Starší verze hlavičky představená v HTTP 1,0; podporováno pro zpětnou kompatibilitu.
   - Používá se jako hlavička žádosti klienta s následující direktivou: `no-cache` . Tato direktiva dává pokyn serveru k doručení nové verze prostředku.
   - `Pragma: no-cache` je ekvivalentem k `Cache-Control: no-cache`.

## <a name="validators"></a>Validátory

Pokud je mezipaměť zastaralá, používají se validátory mezipaměti protokolu HTTP k porovnání verze souboru uloženého v mezipaměti s verzí na zdrojovém serveru. **Azure CDN Standard/Premium z Verizon** `ETag` `Last-Modified` ve výchozím nastavení podporuje i validátory, zatímco **Azure CDN Standard od Microsoftu** a **Azure CDN Standard z Akamai** podporuje jenom standardně `Last-Modified` .

**Značk**
- **Azure CDN Standard/Premium z Verizon** podporuje standardně `ETag` , zatímco **Azure CDN standard od Microsoftu** a **Azure CDN Standard od Akamai** .
- `ETag` definuje řetězec, který je jedinečný pro všechny soubory a verze souboru. Například, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Představená v HTTP 1,1 a je aktuálnější než `Last-Modified` . Užitečné v případě, že je obtížné určit datum poslední změny.
- Podporuje silné ověřování i slabé ověřování; Azure CDN však podporuje pouze silné ověřování. Pro silné ověřování musí být oba reprezentace prostředků shodné s Byte-Byte. 
- Mezipaměť ověřuje soubor, který používá `ETag` odesláním `If-None-Match` hlavičky s jedním nebo více `ETag` validátory v žádosti. Například, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Pokud verze serveru odpovídá `ETag` validátoru na seznamu, pošle v odpovědi stavový kód 304 (nezměněn). Pokud je verze odlišná, server odpoví stavovým kódem 200 (OK) a aktualizovaným prostředkem.

**Poslední změna:**
- Pro **Azure CDN Standard/Premium pouze ze Verizon** `Last-Modified` se používá, pokud není `ETag` součástí odpovědi HTTP. 
- Určuje datum a čas, kdy zdrojový Server určil, že se prostředek naposledy změnil. Například, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Mezipaměť ověří soubor pomocí `Last-Modified` odeslání `If-Modified-Since` hlavičky s datem a časem v žádosti. Zdrojový server porovnává toto datum s `Last-Modified` hlavičkou nejnovějšího prostředku. Pokud se prostředek od zadaného času nezměnil, server vrátí stavový kód 304 (nezměněno) v odpovědi. Pokud byl prostředek změněn, vrátí server stavový kód 200 (OK) a aktualizovaný prostředek.

## <a name="determining-which-files-can-be-cached"></a>Určení souborů, které mohou být uloženy do mezipaměti

Ne všechny prostředky mohou být uloženy do mezipaměti. Následující tabulka ukazuje, jaké prostředky lze ukládat do mezipaměti na základě typu odpovědi HTTP. Prostředky dodávané s odpověďmi HTTP, které nesplňují všechny tyto podmínky, nelze uložit do mezipaměti. Pro **Azure CDN Premium jenom z Verizon** můžete použít modul pravidel k přizpůsobení některých z těchto podmínek.

|                       | Azure CDN od Microsoftu          | Azure CDN z Verizon | Azure CDN z Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **Stavové kódy HTTP** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **Metody HTTP**      | GET, HEAD                         | GET                    | GET                          |
| **Omezení velikosti souborů**  | 300 GB                            | 300 GB                 | – Obecná optimalizace pro doručování webů: 1,8 GB<br />-Optimalizace streamování médií: 1,8 GB<br />– Optimalizace velkých souborů: 150 GB |

Pro **Azure CDN Standard od Microsoft** caching pro práci na prostředku musí zdrojový server podporovat všechny HLAVIČKY a získávat požadavky HTTP a hodnoty Content-Length musí být pro všechny hlavičky stejné a získat odpovědi HTTP na daný Asset. Pro požadavek HEAD musí zdrojový server podporovat požadavek HEAD a musí odpovídat stejným hlavičkám, jako kdyby přijal požadavek GET.

## <a name="default-caching-behavior"></a>Výchozí chování ukládání do mezipaměti

Následující tabulka popisuje výchozí chování při ukládání do mezipaměti pro Azure CDN Products a jejich optimalizace.

|    | Microsoft: obecné webové doručování | Verizon: Obecné doručování webu | Verizon: DSA | Akamai: Obecné doručování webu | Akamai: DSA | Akamai: stahování velkých souborů | Akamai: General nebo VOD Streaming Media |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Dodržovat původ**       | Yes    | Yes   | No   | Yes    | No   | Yes   | Yes    |
| **Doba uložení mezipaměti CDN** | 2 dny |7 dní | Žádné | 7 dní | Žádné | 1 den | 1 rok |

**Dodržovat původ**: Určuje, jestli se mají přijmout podporované hlavičky cache-direktivy, pokud existují v odpovědi HTTP ze zdrojového serveru.

**Doba uložení mezipaměti CDN**: Určuje dobu, po kterou je prostředek uložen v mezipaměti Azure CDN. Pokud je ale možnost **akceptovat původ** nastavená na Ano a odpověď HTTP ze zdrojového serveru zahrnuje hlavičku cache-a `Expires` `Cache-Control: max-age` Azure CDN použije hodnotu trvání určenou hlavičkou. 

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak přizpůsobit a přepsat výchozí chování ukládání do mezipaměti v CDN prostřednictvím pravidel ukládání do mezipaměti, najdete v tématu [řízení Azure CDN chování při ukládání do mezipaměti s pravidly ukládání do](cdn-caching-rules.md)mezipaměti. 
- Informace o tom, jak používat řetězce dotazů k řízení chování ukládání do mezipaměti, najdete v tématu [řízení Azure CDN chování při ukládání do mezipaměti pomocí řetězců dotazu](cdn-query-string.md).
