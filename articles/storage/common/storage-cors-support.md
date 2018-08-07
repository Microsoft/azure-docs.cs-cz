---
title: Podpora prostředků mezi zdroji (CORS) pro sdílení obsahu | Dokumentace Microsoftu
description: Informace o povolení podpory CORS pro služby Microsoft Azure Storage.
services: storage
author: cbrooksmsft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: fd5df50128885f6a96e68c8ad46204bc21d80264
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530849"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Prostředků mezi zdroji (CORS) podporu pro služby Azure Storage pro sdílení obsahu
Od verze 2013-08-15, služby Azure storage podporu sdílení prostředků mezi zdroji (CORS) ke službám Blob, tabulky, fronty a soubor. CORS je funkce protokolu HTTP, která umožňuje webové aplikaci spuštěné v rámci jedné domény pro přístup k prostředkům v jiné doméně. Webové prohlížeče implementují bezpečnostní omezení, označované jako [zásada stejného zdroje](http://www.w3.org/Security/wiki/Same_Origin_Policy) , který chrání webovou stránku před voláním rozhraní API v jiné doméně. CORS nabízí zabezpečený způsob, jak povolit jednu doménu (zdrojová doména) pro volání rozhraní API v jiné doméně. Zobrazit [specifikace CORS](http://www.w3.org/TR/cors/) podrobnosti o CORS.

Můžete nastavit pravidla CORS samostatně pro každou ze služeb úložiště voláním [nastavit vlastnosti služby Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti služby Queue](https://msdn.microsoft.com/library/hh452232.aspx), a [nastavit vlastnosti služby Table](https://msdn.microsoft.com/library/hh452240.aspx). Jakmile jednou nastavíte pravidla CORS pro službu, bude správně autorizované žádost na službu z jiné domény vyhodnotí k určení, zda je povoleno podle pravidel, které jste zadali.

> [!NOTE]
> Všimněte si, že CORS není mechanismus ověřování. Žádné požadavek směřovaný prostředek úložiště při zapnuté CORS musí mít podpis správné ověření, nebo musí být provedena proti prostředek veřejné.
> 
> 

## <a name="understanding-cors-requests"></a>Vysvětlení požadavků CORS
Žádost CORS z zdrojová doména může skládat ze dvou samostatných požadavků:

* Předběžný požadavek, který se dotazuje CORS omezeními službou. Předběžný požadavek není nutná, pokud je metoda žádosti [jednoduchý způsob](http://www.w3.org/TR/cors/), což znamená GET, HEAD nebo POST.
* Skutečné požadavek směřovaný na požadovaný prostředek.

### <a name="preflight-request"></a>Předběžný požadavek
Dotazy předběžný požadavek CORS omezení, které byly vytvořeny pro službu storage vlastník účtu. Webový prohlížeč (nebo jiné uživatelský agent) odešle žádost možnosti, která obsahuje hlavičky požadavku, metody a původu domény. Služba úložiště vyhodnotí odpovídající operace na základě předem nakonfigurované sady pravidel CORS, které určují, které zdrojové domény, požadavek metody a hlavičky požadavku může být specifikovány u aktuálního požadavku proti prostředku úložiště.

Pokud existuje pravidlo CORS, která odpovídá předběžný požadavek CORS je povoleno pro službu, služba jako odpověď vrátí stavový kód 200 (OK) a zahrne požadované hlavičky Access-Control odpovědi.

Pokud CORS není povoleno pro službu nebo nevyhovuje žádné pravidlo CORS předběžný požadavek, odpoví služba stavový kód 403 (zakázáno).

Pokud možnosti žádosti neobsahuje povinné hlavičky CORS (záhlaví původu a Access-Control-Request-Method), bude služba odpovědět stavovým kódem 400 (Chybný požadavek).

Všimněte si, že je předběžný požadavek vyhodnocen na službu (objektu Blob, Queue a Table) a ne na požadovaný prostředek. Vlastník účtu musí povolili CORS jako součást vlastností účtu služby v pořadí pro požadavek úspěšná.

### <a name="actual-request"></a>Skutečnou žádost
Po přijetí předběžný požadavek a odpověď se vrátí, odešle do prohlížeče skutečnou žádost, s prostředkem úložiště. Prohlížeč odmítne skutečnou žádost o okamžitě, pokud je předběžný požadavek zamítnut.

Skutečné žádost se zpracuje jako normální požadavek na službu úložiště. Přítomnost hlavička počátečního označuje, že žádost je žádost CORS a služba zkontroluje odpovídající pravidla CORS. Pokud se najde shoda, záhlaví řízení přístupu přidá do odpovědi a odeslat zpět klientovi. Pokud není nalezena shoda, nebudou zobrazeny hlavičky CORS Access-Control.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Povolení CORS pro služby Azure Storage
Pravidla CORS jsou nastaveny na úrovni služby, budete potřebovat k povolení nebo zákazu sdílení CORS pro každou službu (objektu Blob, fronty a tabulky) samostatně. Ve výchozím nastavení je zakázána CORS pro každou službu. Postup pro povolení CORS, je nutné nastavit vlastnosti příslušnou službu pomocí verze 2013-08-15 nebo novější a přidejte pravidla CORS pro vlastnosti služby. Podrobnosti o tom, jak povolit nebo zakázat CORS pro služby a jak nastavit pravidla CORS, najdete [nastavit vlastnosti služby Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti služby Queue](https://msdn.microsoft.com/library/hh452232.aspx), a [nastavit službu Table Service Vlastnosti](https://msdn.microsoft.com/library/hh452240.aspx).

Tady je ukázka jednoho pravidla CORS, určené pomocí operace nastavit vlastnosti služby:

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

Níže je popsána jednotlivých prvků, součástí pravidlo CORS:

* **AllowedOrigins**: zdrojové domény, které mohou vytvořit požadavek na službu úložiště přes CORS. Zdrojová doména je doména, ze které žádost pochází. Všimněte si, že počátek musí být velká a malá písmena přesně neshoduje s původ, věk uživatele odešle do služby. Můžete také použít zástupný znak "*" Povolit všechny zdrojové domény k podání žádostí o přes CORS. V příkladu výše, domény [ http://www.contoso.com ](http://www.contoso.com) a [ http://www.fabrikam.com ](http://www.fabrikam.com) mohou vytvářet požadavky na službu pomocí CORS.
* **Hodnota AllowedMethods**: metody (akce požadavků HTTP), které může doména zdroje použít pro žádost CORS. V předchozím příkladu jsou povoleny pouze požadavky PUT a GET.
* **AllowedHeaders**: záhlaví požadavku, která doména zdroje může určit v požadavku CORS. V předchozím příkladu jsou povoleny všechny hlavičky metadat od verze x-ms-meta-data, x-ms-meta cíl a x-ms-meta-abc. Všimněte si, že zástupný znak ' *' znamená, že není povoleno žádné začátek záhlaví se zadanou předponou.
* **ExposedHeaders**: hlavičky odpovědi, které mohou být odeslaný v odpovědi na žádost CORS a zpřístupnit v prohlížeči pro vystavitele žádosti. V předchozím příkladu je vystavit libovolné od záhlaví x-ms-meta pokyn prohlížeče.
* **MaxAgeInSeconds**: maximální doba, by měl prohlížeč mezipaměti předběžný možnosti žádosti.

Služby Azure storage podporují zadání záhlaví s předponou pro obě **AllowedHeaders** a **ExposedHeaders** elementy. Povolit kategorii záhlaví, můžete zadat běžnou předponu do této kategorie spadají. Například zadání *x-ms-meta** jako hlavičku předponou vytvoří pravidlo, které budou odpovídat všechny hlavičky, které začínají s x-ms-meta.

Pravidla CORS se vztahují následující omezení:

* Můžete zadat až pět pravidel CORS na službu úložiště (objekt Blob, tabulky a fronty).
* Maximální velikost všech nastavení pravidel CORS v požadavku, s výjimkou značky XML nesmí být delší než 2 KB.
* Délka povolené hlavičky, zveřejněné hlavičky nebo povolený původ by neměl být delší než 256 znaků.
* Povolené hlavičky může být buď zveřejněné hlavičky:
  * Záhlaví literálu, kde je přesné záhlaví je zadat název, například **x-ms-meta zpracovat**. V požadavku je možné zadat maximálně 64 záhlaví literálu.
  * Předponu záhlaví, kterých předponu hlavičky je k dispozici, jako například ** x-ms-meta-data ***. Určení předponu tímto způsobem umožňuje případně zpřístupňuje libovolné záhlaví, který začíná danou předponu. V požadavku je možné zadat maximálně dvě záhlaví označená prefixem.
* Metody (nebo příkazy HTTP), podle **hodnota AllowedMethods** elementu musí odpovídat na metody podporované rozhraní API služby Azure storage. Podporované metody jsou DELETE, GET, HEAD, MERGE, POST, možnosti a PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Principy logika vyhodnocení pravidla CORS
Když služba úložiště obdrží žádost o předběžné nebo skutečné, je vyhodnocen jako žádosti na základě pravidel CORS, kterou jste vytvořili pro službu prostřednictvím odpovídající operace nastavení vlastností služby. Pravidla CORS se vyhodnocují v pořadí, ve kterém byly nastavené v textu požadavku operace nastavení vlastností služby.

Pravidla CORS se vyhodnotí takto:

1. Nejprve zdrojová doména požadavku je porovnávána s domén pro uvedené **AllowedOrigins** elementu. Pokud se zdrojová doména je zahrnuta v seznamu, nebo jsou povoleny všechny domény se zástupným znakem "*", potom pravidla vyhodnocování pokračuje. Pokud zdrojová doména není zahrnutý, požadavek se nezdaří.
2. V dalším kroku – metoda (nebo příkaz HTTP) žádosti je porovnávána s metod uvedených v **hodnota AllowedMethods** elementu. Metoda je zahrnutý v seznamu, pak pokračuje vyhodnocení pravidla; v opačném případě není požadavek splněn.
3. Pokud požadavek odpovídá pravidlu v jeho zdrojová doména a její metoda, vybere se tímto pravidlem ke zpracování požadavku a žádná další pravidla se vyhodnocují. Předtím, než požadavek nezdaří, ale záhlaví v žádosti můžete zadat jsou porovnávána s uvedené v záhlaví **AllowedHeaders** elementu. Pokud hlavičky posílané neshodují povolené hlavičky, požadavek selže.

Protože se zpracovávají v pořadí, v jakém že se vyskytují v textu požadavku, doporučujeme osvědčené postupy zadejte nejvíce omezující pravidla s ohledem na zdroje první v seznamu, tak, aby tyto jsou vyhodnoceny jako první. Zadejte pravidla, které jsou méně omezující – například pravidlo, které umožňuje všechny původy – na konci seznamu.

### <a name="example--cors-rules-evaluation"></a>Příklad: pravidla CORS pro vyhodnocení
Následující příklad ukazuje textu částečné požadavku pro operaci k nastavení pravidel CORS pro služby storage. Naleznete v tématu [nastavit vlastnosti služby Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti služby Queue](https://msdn.microsoft.com/library/hh452232.aspx), a [nastavit vlastnosti tabulky služby](https://msdn.microsoft.com/library/hh452240.aspx) podrobnosti o žádosti o sestavení.

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

Dále je třeba zvážit následující požadavků CORS:

| Žádost |  |  | Odpověď |  |
| --- | --- | --- | --- | --- |
| **– Metoda** |**Počátek** |**Hlavičky žádosti** |**Pravidlo** |**výsledek** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |První pravidlo |Úspěch |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Druhé pravidlo |Úspěch |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Druhé pravidlo |Selhání |

První požadavek odpovídá první pravidlo – doména zdroje odpovídá povolené zdroje, metoda odpovídá povolené metody a záhlaví odpovídá povolené hlavičky – i tak proběhne úspěšně.

Druhou žádost neodpovídá první pravidlo, protože metoda neodpovídá povolené metody. Ji, ale neodpovídá druhé pravidlo tak bude úspěšný.

Třetí požadavek odpovídá druhé pravidlo v jeho zdrojová doména a metodu, takže žádná další pravidla se vyhodnocují. Ale *hlavičky x-ms klienta request-id* není povolena druhé pravidlo tak, že žádost selže, bez ohledu na skutečnost, že sémantika třetí pravidlo by nichž je parametr povoleno ji proběhla úspěšně.

> [!NOTE]
> Přestože tento příklad ukazuje volnější pravidla před více omezující, obecně osvědčeným postupem je nejdřív seznam nejvíce omezující pravidel.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Vysvětlení, jak nastavit měnit hlavičky
*Měnit* záhlaví je standardní hlavičku HTTP/1.1 skládající se z sadu pole hlavičky požadavku, které dokáží prohlížeče nebo uživatel agenta o kritérií, které byly vybrány server ke zpracování požadavku. *Měnit* záhlaví se používá hlavně pro ukládání do mezipaměti podle proxy servery, prohlížečů a CDN, které používají k určení, jak odpověď do mezipaměti. Podrobnosti najdete v tématu specifikace [hlavičce Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Pokud odpověď z požadavku CORS do mezipaměti prohlížeče nebo jiný uživatelský agent, zdrojová doména je uložené v mezipaměti jako povolený původ. Když druhou doménu vydá stejný požadavek pro prostředek úložiště mezipaměti je aktivní, uživatelský agent načte uložené v mezipaměti zdrojová doména. Druhou doménu neodpovídá domény v mezipaměti, takže žádost selže, pokud by jinak úspěšné. V některých případech, Azure Storage, nastaví na hlavičce Vary **původu** dáte pokyn, aby uživatelský agent odeslat další požadavek CORS služby, pokud žádost o domény se liší od počátku uložené v mezipaměti.

Sady Azure Storage *měnit* záhlaví **původu** pro skutečné požadavky GET a HEAD v následujících případech:

* Pokud požadovaný původ přesně odpovídá povolený původ definované pravidlo CORS. Být přesná shoda, pravidlo CORS nemusí obsahovat zástupný znak "*" znak.
* Neexistuje žádné pravidlo odpovídající požadovaný původ, ale je povolená CORS pro službu storage.

V případě, kdy požadavek GET a HEAD odpovídá pravidlo CORS, která umožňuje všechny původy z odpovědi vyplývá, že jsou povolené všechny původy a mezipaměti uživatelského agenta vám umožní následné žádosti z jakékoli zdrojová doména, do mezipaměti je aktivní.

Všimněte si, že požadavky pomocí jiných metod než GET a HEAD, služeb úložiště nenastaví měnit hlavičky, protože odpovědi na tyto metody nejsou uložené v mezipaměti Uživatelští agenti.

Následující tabulka uvádí, jak Azure storage bude reagovat na požadavky GET a HEAD podle výše uvedených případech:

| Žádost | Nastavení účtu a výsledek vyhodnocení pravidla |  |  | Odpověď |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Hlavička počátečního k dispozici na vyžádání** |**Pravidla CORS pro zadaný pro tuto službu** |**Existuje odpovídající pravidlo, která umožňuje všechny origins(*)** |**Existuje odpovídající pravidlo pro původu přesné shody** |**Odpověď obsahuje záhlaví měnit nastavení na původní název** |**Odpověď obsahuje Access-Control-povoleno-Origin: "*"** |**Odpověď obsahuje Access-Control-vystavený-Headers** |
| Ne |Ne |Ne |Ne |Ne |Ne |Ne |
| Ne |Ano |Ne |Ne |Ano |Ne |Ne |
| Ne |Ano |Ano |Ne |Ne |Ano |Ano |
| Ano |Ne |Ne |Ne |Ne |Ne |Ne |
| Ano |Ano |Ne |Ano |Ano |Ne |Ano |
| Ano |Ano |Ne |Ne |Ano |Ne |Ne |
| Ano |Ano |Ano |Ne |Ne |Ano |Ano |

## <a name="billing-for-cors-requests"></a>Fakturace za požadavků CORS
Úspěšně zaregistrovat k předběžné verzi požádá, se účtují, pokud jste povolili CORS pro všechny služby úložiště pro váš účet (voláním [nastavit vlastnosti služby Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti služby Queue](https://msdn.microsoft.com/library/hh452232.aspx), nebo [Nastavovat vlastnosti služby Table](https://msdn.microsoft.com/library/hh452240.aspx)). Chcete-li minimalizovat náklady, zvažte nastavení **MaxAgeInSeconds** element ve vlastní CORS pravidla pro velké hodnoty tak, aby uživatelský agent ukládá do mezipaměti požadavku.

Neúspěšné předběžných požadavků se nebude účtovat.

## <a name="next-steps"></a>Další postup
[Nastavit vlastnosti služby Blob Service](https://msdn.microsoft.com/library/hh452235.aspx)

[Nastavit vlastnosti služby Queue](https://msdn.microsoft.com/library/hh452232.aspx)

[Nastavení vlastností služby tabulky](https://msdn.microsoft.com/library/hh452240.aspx)

[Specifikace sdílení prostředků různého původu W3C](http://www.w3.org/TR/cors/)

