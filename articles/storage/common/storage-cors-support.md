---
title: Podpora sdílení zdrojů napříč zdroji (CORS) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak povolit podporu CORS pro služby Microsoft Azure Storage Services.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65147690"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Podpora sdílení prostředků napříč zdroji (CORS) pro služby Azure Storage Services
Počínaje verzí 2013-08-15 podporují služby úložiště Azure sdílení prostředků mezi zdroji (CORS) pro služby blob, table, queue a file. CORS je funkce HTTP, která umožňuje webové aplikaci spuštěné pod jednou doménou přístup k prostředkům v jiné doméně. Webové prohlížeče implementují omezení zabezpečení známé jako [zásady stejného původu,](https://www.w3.org/Security/wiki/Same_Origin_Policy) které brání webové stránce v volání souborů API v jiné doméně. CORS poskytuje bezpečný způsob, jak povolit jedné doméně (původní doméně) volat api v jiné doméně. Podrobnosti o CORS naleznete ve [specifikaci CORS.](https://www.w3.org/TR/cors/)

Pravidla CORS můžete nastavit jednotlivě pro každou službu úložiště voláním [Nastavit vlastnosti služby objektů blob](https://msdn.microsoft.com/library/hh452235.aspx), [Nastavit vlastnosti služby fronty](https://msdn.microsoft.com/library/hh452232.aspx)a Nastavit [vlastnosti služby tabulky](https://msdn.microsoft.com/library/hh452240.aspx). Jakmile nastavíte pravidla CORS pro službu, pak správně autorizovaný požadavek proti službě z jiné domény bude vyhodnocen, aby se zjistilo, zda je povoleno podle pravidel, která jste zadali.

> [!NOTE]
> Všimněte si, že CORS není mechanismus ověřování. Jakýkoli požadavek na prostředek úložiště, pokud je povolen a co je povolen, musí mít buď správný podpis ověřování, nebo musí být proveden proti veřejnému prostředku.
> 
> 

## <a name="understanding-cors-requests"></a>Principy požadavků CORS
Požadavek CORS z původní domény se může skládat ze dvou samostatných požadavků:

* Požadavek kontroly před výstupem, který se dotazuje omezení CORS vynucených službou. Požadavek kontroly před výstupem je povinný, pokud metoda požadavku není [jednoduchou metodou](https://www.w3.org/TR/cors/), což znamená GET, HEAD nebo POST.
* Skutečný požadavek, provedené proti požadovaný prostředek.

### <a name="preflight-request"></a>Požadavek kontroly před výstupem
Požadavek kontroly před výstupem se dotazuje na omezení CORS, která byla vytvořena pro službu úložiště vlastníkem účtu. Webový prohlížeč (nebo jiný uživatelský agent) odešle požadavek OPTIONS, který obsahuje hlavičky požadavku, metodu a původní doménu. Služba úložiště vyhodnotí zamýšlenou operaci na základě předem nakonfigurované sady pravidel CORS, která určují, které původní domény, metody požadavků a hlavičky požadavků mohou být určeny pro skutečný požadavek na prostředek úložiště.

Pokud cors je povolena pro službu a je pravidlo CORS, který odpovídá požadavku kontroly před výstupem, služba odpoví stavový kód 200 (OK) a obsahuje požadované hlavičky řízení přístupu v odpovědi.

Pokud cors není povolena pro službu nebo žádné pravidlo CORS odpovídá požadavku před výstupem, služba odpoví stavovým kódem 403 (Zakázáno).

Pokud požadavek OPTIONS neobsahuje požadované hlavičky CORS (hlavičky Origin a Access-Control-Request-Method), služba odpoví stavovým kódem 400 (Chybný požadavek).

Všimněte si, že požadavek kontroly před výstupem je vyhodnocen proti službě (blob, fronta a tabulka) a nikoli proti požadovanému prostředku. Vlastník účtu musí mít povoleno CORS jako součást vlastností služby účtu, aby požadavek úspěšný.

### <a name="actual-request"></a>Skutečný požadavek
Jakmile je požadavek kontroly před výstupem přijat a odpověď je vrácena, prohlížeč odešle skutečný požadavek proti prostředku úložiště. Prohlížeč odmítne skutečný požadavek okamžitě, pokud je požadavek kontroly před výstupem odmítnut.

Skutečný požadavek je považován za normální požadavek proti službě úložiště. Přítomnost origin záhlaví označuje, že požadavek je požadavek CORS a služba zkontroluje odpovídající pravidla CORS. Pokud je nalezena shoda, hlavičky řízení přístupu jsou přidány do odpovědi a odeslány zpět klientovi. Pokud není nalezena shoda, nejsou vráceny hlavičky řízení přístupu CORS.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Povolení cors pro služby Azure Storage
Pravidla CORS jsou nastavena na úrovni služby, takže je třeba povolit nebo zakázat CORS pro každou službu (objekt blob, fronta a tabulka) samostatně. Ve výchozím nastavení je CORS pro jednotlivé služby zakázáno. Chcete-li povolit CORS, je třeba nastavit příslušné vlastnosti služby pomocí verze 2013-08-15 nebo novější a přidat pravidla CORS do vlastností služby. Podrobnosti o povolení nebo zakázání cors pro službu a nastavení pravidel CORS naleznete v části [Nastavení vlastností služby objektů blob](https://msdn.microsoft.com/library/hh452235.aspx), [Nastavení vlastností služby fronty](https://msdn.microsoft.com/library/hh452232.aspx)a Nastavení [vlastností služby table service](https://msdn.microsoft.com/library/hh452240.aspx).

Zde je ukázka jednoho pravidla CORS určeného pomocí operace Nastavit vlastnosti služby:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Každý prvek obsažený v pravidle CORS je popsán níže:

* **AllowedOrigins**: Původní domény, které jsou povoleny podat žádost proti službě úložiště prostřednictvím CORS. Původní doména je doména, ze které požadavek pochází. Všimněte si, že původ musí být přesně malá a velká písmena shoda s původem, který uživatel věk odešle do služby. Můžete také použít zástupný znak '*' povolit všechny domény původu, aby požadavky prostřednictvím CORS. Ve výše uvedeném příkladu\/domény http:\//www.contoso.com a http: /www.fabrikam.com můžete provádět požadavky proti službě pomocí CORS.
* **AllowedMethods**: Metody (http požadavek slovesa), které původní doména může použít pro požadavek CORS. V příkladu výše jsou povoleny pouze požadavky PUT a GET.
* **AllowedHeaders**: Hlavičky požadavku, které může původní doména zadat v požadavku CORS. Ve výše uvedeném příkladu jsou povoleny všechny hlavičky metadat začínající na x-ms-meta-data, x-ms-meta-target a x-ms-meta-abc. Všimněte si, že zástupný znak *označuje, že je povoleno libovolné záhlaví začínající zadanou předponou.
* **ExposedHeaders**: Hlavičky odpovědí, které mohou být odeslány v odpovědi na požadavek CORS a vystaveny prohlížečem vystaviteli požadavku. Ve výše uvedeném příkladu je prohlížeč instruován vystavit všechny záhlaví začínající x-ms-meta.
* **MaxAgeInSeconds**: Maximální doba, po kterou by měl prohlížeč ukládat požadavek options před výstupem do mezipaměti.

Služba úložiště Azure podporuje určení předponou záhlaví pro **allowedheaders** a **ExposedHeaders prvky.** Chcete-li povolit kategorii záhlaví, můžete zadat společnou předponu pro tuto kategorii. Například zadání *x-ms-meta** jako hlavičky s předponou vytvoří pravidlo, které bude odpovídat všem záhlavím začínajícím x-ms-meta.

Následující omezení platí pro pravidla CORS:

* Můžete zadat až pět pravidel CORS pro službu úložiště (objekt blob, tabulka a fronta).
* Maximální velikost všech nastavení pravidel CORS v požadavku, s výjimkou značek XML, by neměla překročit 2 KB.
* Délka povolené hlavičky, exponované hlavičky nebo povoleného původu by neměla přesáhnout 256 znaků.
* Povolené hlavičky a exponované hlavičky mohou být buď:
  * Literál záhlaví, kde je k dispozici přesný název záhlaví, například **x-ms-meta-processed**. V požadavku může být zadáno maximálně 64 literálových záhlaví.
  * Předpona záhlaví, kde je k dispozici předpona záhlaví, například **x-ms-meta-data***. Zadání předpony tímto způsobem umožňuje nebo zveřejňuje libovolné záhlaví, které začíná danou předponou. V požadavku mohou být zadány maximálně dvě hlavičky s předponou.
* Metody (nebo http slovesa) zadané v **AllowedMethods** element musí odpovídat metody podporované rozhraní API služby úložiště Azure. Podporované metody jsou DELETE, GET, HEAD, MERGE, POST, OPTIONS a PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Principy logiky vyhodnocení pravidel CORS
Když služba úložiště obdrží před letem nebo skutečný požadavek, vyhodnotí tento požadavek na základě pravidel CORS, které jste pro službu vytvořili prostřednictvím příslušné operace Nastavit vlastnosti služby. Pravidla CORS jsou vyhodnocována v pořadí, ve kterém byla nastavena v těle požadavku operace Nastavit vlastnosti služby.

Pravidla CORS jsou vyhodnocována takto:

1. Nejprve původní doména požadavku je kontrolována proti doménám uvedeným pro **allowedorigins** element. Pokud je původní doména zahrnuta do seznamu nebo jsou povoleny všechny domény se zástupným znakem *, pokračuje hodnocení pravidel. Pokud původní doména není zahrnuta, požadavek se nezdaří.
2. Dále metoda (nebo http sloveso) požadavku je kontrolována proti metody uvedené v **AllowedMethods** element. Pokud je metoda zahrnuta do seznamu, pokračuje hodnocení pravidel; pokud ne, požadavek se nezdaří.
3. Pokud požadavek odpovídá pravidlu v původní doméně a jeho metodě, je toto pravidlo vybráno ke zpracování požadavku a nejsou vyhodnocována žádná další pravidla. Před požadavek může být úspěšná, ale všechny hlavičky zadané v požadavku jsou kontrolovány proti záhlaví uvedená v **AllowedHeaders** element. Pokud odeslané hlavičky neodpovídají povoleným záhlavím, požadavek se nezdaří.

Vzhledem k tomu, že pravidla jsou zpracovány v pořadí, ve kterých jsou k dispozici v textu požadavku, doporučené postupy doporučujeme zadat nejvíce omezující pravidla s ohledem na původ nejprve v seznamu tak, aby byly vyhodnoceny jako první. Zadejte pravidla, která jsou méně omezující – například pravidlo povolit všechny počátky – na konci seznamu.

### <a name="example--cors-rules-evaluation"></a>Příklad – vyhodnocení pravidel CORS
Následující příklad ukazuje částečné tělo požadavku pro operaci nastavit pravidla CORS pro služby úložiště. Podrobnosti o sestavení požadavku naleznete v tématech [Nastavení vlastností služby objektů blob](https://msdn.microsoft.com/library/hh452235.aspx), [Nastavení vlastností služby fronty](https://msdn.microsoft.com/library/hh452232.aspx)a Nastavení [vlastností služby table service.](https://msdn.microsoft.com/library/hh452240.aspx)

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Dále zvažte následující požadavky CORS:

| Žádost |  |  | Odpověď |  |
| --- | --- | --- | --- | --- |
| **Metoda** |**Zdroj** |**Hlavičky požadavku** |**Shoda pravidel** |**Výsledek** |
| **Dát** |http:\//www.contoso.com |x-ms-blob-content-type |První pravidlo |Úspěch |
| **Dostat** |http:\//www.contoso.com |x-ms-blob-content-type |Druhé pravidlo |Úspěch |
| **Dostat** |http:\//www.contoso.com |x-ms-client-request-id |Druhé pravidlo |Selhání |

První požadavek odpovídá prvnímu pravidlu – původní doména odpovídá povoleným počátkům, metoda odpovídá povoleným metodám a záhlaví odpovídá povoleným záhlavím – a tak se daří.

Druhý požadavek neodpovídá prvnímu pravidlu, protože metoda neodpovídá povoleným metodám. To však odpovídá druhé pravidlo, tak to uspěje.

Třetí požadavek odpovídá druhému pravidlu v původní doméně a metodě, takže nejsou vyhodnocována žádná další pravidla. *Hlavička x-ms-client-request-id* však není povoleno druhým pravidlem, takže požadavek se nezdaří, a to navzdory skutečnosti, že sémantiku třetího pravidla by bylo povoleno úspěšné.

> [!NOTE]
> I když tento příklad ukazuje méně omezující pravidlo před více omezující, obecně je osvědčeným postupem uvést nejprve nejvíce omezující pravidla.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Principy nastavení záhlaví Vary
Záhlaví *Vary* je standardní hlavička HTTP/1.1, která se skládá ze sady polí hlaviček požadavku, která radí prohlížeči nebo uživatelskému agentovi o kritériích, která server vybral ke zpracování požadavku. Záhlaví *Vary* se používá hlavně pro ukládání do mezipaměti proxy servery proxy, prohlížeče a CDNs, které ji používají k určení, jak by měla být odpověď uložena do mezipaměti. Podrobnosti naleznete v specifikaci [záhlaví Vary](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Když prohlížeč nebo jiný uživatelský agent uloží odpověď z požadavku CORS do mezipaměti, původní doména je uložena do mezipaměti jako povolený původ. Pokud druhá doména vydá stejný požadavek na prostředek úložiště, když je mezipaměť aktivní, načte uživatelský agent původní doménu uloženou v mezipaměti. Druhá doména neodpovídá doméně uložené v mezipaměti, takže požadavek se nezdaří, pokud by jinak byl úspěšný. V některých případech Azure Storage nastaví vary záhlaví **Origin** pokyn agenta uživatele odeslat následné CORS požadavek na službu, když žádající doména se liší od původu uložené v mezipaměti.

Azure Storage nastaví záhlaví *Vary* na **Origin** pro skutečné požadavky GET/HEAD v následujících případech:

* Pokud původ požadavku přesně odpovídá povolenému původu definovanému pravidlem CORS. Chcete-li být přesná shoda, pravidlo CORS nesmí obsahovat zástupný znak * .
* Neexistuje žádné pravidlo odpovídající původ požadavku, ale CORS je povolena pro službu úložiště.

V případě, kdy požadavek GET/HEAD odpovídá pravidlu CORS, které umožňuje všechny počátky, odpověď označuje, že jsou povoleny všechny počátky a mezipaměť agenta uživatele povolí následné požadavky z libovolné původní domény, když je mezipaměť aktivní.

Všimněte si, že pro požadavky pomocí jiných metod než GET/HEAD, služby úložiště nenastaví záhlaví Vary, protože odpovědi na tyto metody nejsou ukládány do mezipaměti uživatelskými agenty.

Následující tabulka uvádí, jak bude úložiště Azure reagovat na požadavky GET/HEAD na základě výše uvedených případů:

| Žádost | Nastavení účtu a výsledek vyhodnocení pravidla |  |  | Odpověď |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Hlavička původu přítomná na vyžádání** |**Pravidla (pravidla) CORS určená pro tuto službu** |**Existuje pravidlo párování, které umožňuje všechny počátky(*)** |**Pro přesnou shodu původu existuje pravidlo párování.** |**Odpověď obsahuje hodnotu Vary nastavenou na Origin.** |**Odpověď zahrnuje přístup-control-allowed-Origin: "*"** |**Odpověď zahrnuje hlavičky Access-Control-Exposed-Headers** |
| Ne |Ne |Ne |Ne |Ne |Ne |Ne |
| Ne |Ano |Ne |Ne |Ano |Ne |Ne |
| Ne |Ano |Ano |Ne |Ne |Ano |Ano |
| Ano |Ne |Ne |Ne |Ne |Ne |Ne |
| Ano |Ano |Ne |Ano |Ano |Ne |Ano |
| Ano |Ano |Ne |Ne |Ano |Ne |Ne |
| Ano |Ano |Ano |Ne |Ne |Ano |Ano |

## <a name="billing-for-cors-requests"></a>Fakturace pro požadavky CORS
Úspěšné požadavky před výstupem se účtují, pokud jste povolili cors pro některou ze služeb úložiště pro váš účet (voláním [Nastavit vlastnosti služby blob](https://msdn.microsoft.com/library/hh452235.aspx), [Nastavit vlastnosti služby fronty](https://msdn.microsoft.com/library/hh452232.aspx)nebo Nastavit [vlastnosti služby table service](https://msdn.microsoft.com/library/hh452240.aspx)). Chcete-li minimalizovat poplatky, zvažte nastavení prvku **MaxAgeInSeconds** v pravidlech CORS na velkou hodnotu tak, aby agent uživatele ukládá požadavek do mezipaměti.

Neúspěšné žádosti o kontrolu před výstupem nebudou účtovány.

## <a name="next-steps"></a>Další kroky
[Nastavení vlastností služby objektů blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Nastavit vlastnosti služby fronty](https://msdn.microsoft.com/library/hh452232.aspx)

[Nastavit vlastnosti služby table service](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C specifikace sdílení zdrojů mezi zdroji](https://www.w3.org/TR/cors/)

