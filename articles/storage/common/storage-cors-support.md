---
title: Podpora (CORS) pro sdílení prostředků různého původu | Microsoft Docs
description: Zjistěte, jak povolit podporu CORS pro služby Microsoft Azure Storage.
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "23873972"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Podpora služby Azure Storage (CORS) pro sdílení prostředků různého původu
Počínaje verzí 2013-08-15, podporu služby Azure storage sdílení prostředků různých původů (CORS) pro služby objektů Blob, tabulky, fronty a souboru. CORS je funkce protokolu HTTP, která umožňuje webové aplikace spuštěna v rámci jednoho domény přístup k prostředkům v jiné doméně. Webové prohlížeče implementovat omezení zabezpečení, označuje jako [stejného původu zásad](http://www.w3.org/Security/wiki/Same_Origin_Policy) která brání webové stránky z volání rozhraní API v jiné doméně. CORS poskytuje zabezpečení způsob, jak povolit jednu doménu (doménu původu) k volání rozhraní API v jiné doméně. Najdete v článku [specifikace CORS](http://www.w3.org/TR/cors/) podrobnosti o CORS.

Můžete nastavit pravidla CORS samostatně pro každou službu úložiště, voláním [nastavit vlastnosti služby objektů Blob](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti fronty služby](https://msdn.microsoft.com/library/hh452232.aspx), a [nastavit vlastnosti služby tabulky](https://msdn.microsoft.com/library/hh452240.aspx). Jakmile jednou nastavíte pravidla CORS pro službu, pak správně ověřené požadavek na službu z jiné domény vyhodnotí k určení, zda je povoleno podle pravidel, který jste zadali.

> [!NOTE]
> Všimněte si, že CORS není ověřovací mechanismus. Všechny požadavek směřovaný na prostředků úložiště, pokud je povoleno CORS musí mít buď správné ověření podpisu, nebo musí být provedeny pro prostředek veřejné.
> 
> 

## <a name="understanding-cors-requests"></a>Pochopení požadavků CORS
Požadavek CORS z počátku domény může obsahovat dva samostatné požadavky:

* Předběžný požadavek, který se dotazuje CORS omezení vynucená službou. Předběžný požadavek není nutná, pokud je metoda žádosti [jednoduše](http://www.w3.org/TR/cors/), což znamená, GET, HEAD nebo POST.
* Skutečný požadavek směřovaný na požadovaný prostředek.

### <a name="preflight-request"></a>Předběžný požadavek
Dotazy předběžný požadavek CORS omezení, které byly vytvořeny pro službu úložiště podle vlastníka účtu. Webový prohlížeč (nebo jiné uživatelský agent) odešle požadavek na možnosti, která zahrnuje hlavičky požadavku, metoda a původu domény. Služba úložiště vyhodnotí určený operaci na základě sady předem nakonfigurovaných pravidel CORS, která určit, které zdrojové domény, žádost metody a hlavičky požadavku může být na požadavek skutečné u prostředků úložiště.

Pokud existuje pravidlo CORS, která odpovídá předběžný požadavek CORS je povolen pro službu, službu odpoví se stavovým kódem 200 (OK) a obsahuje požadované hlavičky Access-Control v odpovědi.

Pokud žádné pravidlo CORS odpovídá předběžný požadavek nebo CORS není povolen pro službu, službu bude odpovídat se stavovým kódem 403 (zakázáno).

Pokud možnosti žádost neobsahuje požadované hlavičky CORS (hlavičky původ a Access-Control-Request-Method), služba bude odpovídat se stavovým kódem 400 (Chybný požadavek).

Všimněte si, že předběžný požadavek vyhodnotí proti služby (Blob, fronty a tabulky) a ne požadovaný prostředek. Vlastníka účtu musí mít povolena CORS v rámci vlastnosti účtu služby v pořadí k úspěšné žádosti.

### <a name="actual-request"></a>Skutečné požadavku
Jakmile je přijímán předběžný požadavek a odpověď se vrátí, bude skutečné žádost u prostředků úložiště odeslat prohlížeče. V prohlížeči se zakáže skutečnou okamžitě požadavku, pokud předběžný požadavek byl odmítnut.

Skutečný požadavek považován za normální požadavek na službu úložiště. Přítomnost hlavičky počátku označuje, že požadavek je požadavek CORS a služba zkontroluje odpovídající pravidla CORS. Pokud je nalezena shoda, hlavičky Access-Control se přidá do odpovědi a odesílají zpět do klienta. Pokud není nalezena shoda, nebudou zobrazeny hlavičky CORS Access-Control.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Povolení CORS pro služby Azure Storage
Pravidla CORS jsou nastavené na úrovni služby, takže budete muset povolit nebo zakázat CORS pro každou službu (objekt Blob, fronty a tabulky) samostatně. Ve výchozím nastavení je zakázané CORS pro každou službu. K povolení sdílení CORS, budete muset nastavit vlastnosti příslušnou službu pomocí verze 2013-08-15 nebo novější a přidejte pravidla CORS do vlastnosti služby. Podrobnosti o tom, jak povolit nebo zakázat CORS pro služby a jak nastavit pravidla CORS, naleznete v [nastavit vlastnosti služby objektů Blob](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti fronty služby](https://msdn.microsoft.com/library/hh452232.aspx), a [nastavit služby Table Vlastnosti](https://msdn.microsoft.com/library/hh452240.aspx).

Zde je ukázka jednoho pravidla CORS, zadaný prostřednictvím operace nastavit vlastnosti služby:

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

Každý prvek uvedené v pravidle CORS je popsán dále:

* **AllowedOrigins**: zdrojové domény, které jsou povoleny vytvořte žádost na službě úložiště prostřednictvím CORS. Doménu původu je doména, ze které žádost pochází. Všimněte si, že počátek musí být velká a malá písmena přesně shodovat s původ, který stáří uživatel odešle do služby. Můžete také použít zástupný znak ' *' povolit všechny zdrojové domény provádět požadavky pomocí CORS. V příkladu výše, domén [ http://www.contoso.com ](http://www.contoso.com) a [ http://www.fabrikam.com ](http://www.fabrikam.com) provádět požadavků na službu pomocí CORS.
* **AllowedMethods**: metody (příkazy požadavku HTTP), které doménu původu může použít pro požadavek CORS. V předchozím příkladu jsou povoleny pouze požadavky PUT a GET.
* **AllowedHeaders**: hlavičky žádosti, které může zadejte doménu původu na požadavek CORS. V předchozím příkladu jsou povolené všechny hlavičky metadata počínaje x-ms-meta-data, x-ms-meta cíl a x-ms-meta-abc. Všimněte si, že zástupný znak ' *' označuje, že je povolit všechny hlavičky začíná zadanou předponou.
* **ExposedHeaders**: hlavičky odpovědi, které může odeslaný v odpovědi na požadavek CORS a zveřejněny v prohlížeči Issuer požadavku. V příkladu nahoře je v prohlížeči pokyn ke zveřejnění počínaje x-ms-meta všechny hlavičky.
* **MaxAgeInSeconds**: maximální velikost čas, že prohlížeče by měl mezipaměti možnosti předběžných požadavků.

Podpora služby Azure storage zadání předponou hlavičky i **AllowedHeaders** a **ExposedHeaders** elementy. Povolit kategorii hlaviček, můžete zadat předponu běžné do této kategorie spadají. Například zadání *x-ms-meta** jako hlavičku předponou Určuje pravidlo, které bude odpovídat všechny hlavičky, které začínají s x-ms-meta.

Na pravidla CORS platí následující omezení:

* Můžete zadat až pět pravidla CORS pro službu úložiště (objekt Blob, Table a Queue).
* Maximální velikost všech pravidla nastavení CORS v požadavku, s výjimkou značky XML nesmí být delší než 2 KB.
* Délka povolené hlavičky, vystaveného hlavičce nebo povolený původ nesmí překročit 256 znaků.
* Povolené hlavičky a zveřejněné záhlaví může být buď:
  * Literál hlavičky, kde je přesný záhlaví je zadaný název, například **x-ms-meta zpracovat**. Je možné zadat maximálně 64 literálu hlavičky v požadavku.
  * Předponu hlavičky, kde předponu hlavičky, která je k dispozici, jako například ** x-ms-meta-data ***. Zadání předpony tímto způsobem umožňuje nebo zpřístupní všechny hlavičky, který začíná danou předponu. Je možné zadat maximálně dvě předponou hlavičky v požadavku.
* Metody (nebo příkazy HTTP), zadaný v **AllowedMethods** element musí odpovídat metod podporovaných službou Azure storage rozhraní API. Jsou podporované metody odstranění, GET, HEAD, SLOUČENÍ, POST, možnosti a PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Principy logiku vyhodnocení pravidla CORS
Když služba úložiště obdrží žádost o předběžný nebo skutečné, vyhodnotí tohoto požadavku na základě pravidel CORS, které jste vytvořili pro službu pomocí příslušné operace nastavit vlastnosti služby. Pravidla CORS jsou vyhodnocovány v pořadí, ve které byly nastavené v těle žádosti operace nastavit vlastnosti služby.

CORS pravidla se vyhodnocují následujícím způsobem:

1. Nejprve domén uvedené pro zkontrolován doménu původu požadavku **AllowedOrigins** element. Pokud doménu původu je uveden v seznamu, nebo jsou povoleny všechny domény se zástupným znakem ' *', pak bude pokračovat vyhodnocení pravidla. Pokud doménu původu neuvedete, požadavek se nezdaří.
2. V dalším kroku – metoda (nebo příkaz HTTP) žádosti je zkontrolován metody uvedené v **AllowedMethods** element. Metoda je uveden v seznamu, potom pokračuje pravidla vyhodnocení; Pokud ne, pak požadavek selže.
3. Pokud požadavek odpovídá pravidla v jeho zdrojovou doménu a jeho metodou, je proces, který se vyhodnotí žádost a žádná další pravidla vybrán daného pravidla. Předtím, než mohlo být úspěšné žádosti, ale kontroluje všechny hlavičky zadaný v požadavku vůči hlavičky uvedené v **AllowedHeaders** element. Pokud hlavičky odeslané neshodují povolené hlavičky, žádost skončí s chybou.

Vzhledem k tomu, že se zpracovávají v pořadí, v jakém že se nachází v textu požadavku, doporučujeme osvědčené postupy zadat nejvíc omezující pravidla s ohledem na zdroje první v seznamu, tak, aby tyto se vyhodnocují jako první. Zadejte pravidla, která jsou méně omezující – například pravidla povolit všechny původy – na konci tohoto seznamu.

### <a name="example--cors-rules-evaluation"></a>Příklad – CORS pravidel vyhodnocení
Následující příklad ukazuje obsah částečné žádosti operace nastavit pravidla CORS pro služby úložiště. V tématu [nastavit vlastnosti služby objektů Blob](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti fronty služby](https://msdn.microsoft.com/library/hh452232.aspx), a [nastavit vlastnosti služby Table](https://msdn.microsoft.com/library/hh452240.aspx) informace o vytváření žádosti.

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
| **– Metoda** |**Origin** |**Hlavičky požadavku** |**Pravidla shody** |**výsledek** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |První pravidlo |Úspěch |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Druhé pravidlo |Úspěch |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Druhé pravidlo |Selhání |

První požadavek odpovídá první pravidlo – doménu původu odpovídá povolené zdroje, metoda odpovídá povolené metody a hlavičky odpovídá povolené hlavičky – a proto je úspěšné.

Druhý žádosti neodpovídá první pravidlo, protože metoda neodpovídá povolené metody. Pravidlo druhého, je však, shodovala, takže operace proběhne úspěšně.

Třetí požadavek odpovídá druhé pravidlo v jeho zdrojovou doménu a metodu, takže žádná další pravidla se vyhodnocují. Ale *hlavičky x-ms-client-request-id* není povolena pravidlo druhého, takže požadavek selže, bez ohledu na skutečnost, že sémantika třetí pravidlo by mít povoleno ho proběhla úspěšně.

> [!NOTE]
> I když tento příklad ukazuje méně přísná pravidla před více omezující jeden, obecně osvědčeným postupem je nejdřív seznam nejvíc omezující pravidel.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Pochopení, jak nastavit měnit hlavičky
*Měnit* záhlaví je standardní hlavičku HTTP/1.1 skládající se z sadu pole hlavičky požadavku, které poradit agenta prohlížeče nebo uživatele o kritériích zvolená serverem pro zpracování požadavku. *Měnit* záhlaví se většinou používá pro ukládání do mezipaměti podle proxy, prohlížečů a sítím CDN, které ho použít k určení, jak odpověď do mezipaměti. Podrobnosti najdete v tématu specifikace [měnit hlavičky](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Pokud odpověď z požadavek CORS do mezipaměti prohlížeče nebo jiné uživatelského agenta, doménu původu se uloží do mezipaměti jako povolený původ. Když druhé domény vydá stejnou žádost o prostředek úložiště při mezipaměti je aktivní, uživatelský agent načte doménu původu v mezipaměti. Druhá doména neodpovídá domény v mezipaměti, takže požadavek selže, když by jinak úspěšné. V některých případech, Azure Storage, nastaví hlavičku měnit na **původu** dáte pokyn, aby uživatelský agent odeslat další požadavek CORS služby, pokud žádajícího domény se liší od počátku uložené v mezipaměti.

Azure Storage nastaví *měnit* hlavičky k **původu** skutečné požadavky GET nebo HEAD v následujících případech:

* Když je požadovaný původ přesně odpovídá povolený původ definované pravidlem CORS. Jako přesnou shodu pravidla CORS nesmí obsahovat zástupný znak ' * ' znak.
* Neexistuje žádné pravidlo odpovídající požadovaný původ, ale je povolená CORS pro služby úložiště.

V případě, kdy požadavek GET nebo HEAD odpovídá pravidla CORS, která umožňuje všechny původy z odpovědi vyplývá, že povolené jsou všechny zdroje a mezipaměti agenta uživatele umožní následné žádosti z domén počátek při mezipaměti je aktivní.

Všimněte si, že pro požadavky na použití jiných metod než GET nebo HEAD, služby storage nenastaví měnit hlavičky vzhledem k tomu, že se neukládají do mezipaměti odpovědi na tyto metody Uživatelští agenti.

Následující tabulka uvádí, jak Azure storage bude reagovat na požadavky GET nebo HEAD podle výše uvedených případech:

| Žádost | Nastavení účtu a výsledek vyhodnocení pravidla |  |  | Odpověď |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Původ hlavičky v požadavku** |**CORS pravidel zadaný pro tuto službu** |**Existuje odpovídající pravidlo, která umožňuje všechny origins(*)** |**Existuje odpovídající pravidlo pro shodu přesný počátek** |**Odpověď obsahuje záhlaví měnit nastavení pro počátek** |**Odpověď obsahuje Access-Control-povolené-Origin: "*"** |**Odpověď obsahuje Access-Control-zveřejněné-Headers** |
| Ne |Ne |Ne |Ne |Ne |Ne |Ne |
| Ne |Ano |Ne |Ne |Ano |Ne |Ne |
| Ne |Ano |Ano |Ne |Ne |Ano |Ano |
| Ano |Ne |Ne |Ne |Ne |Ne |Ne |
| Ano |Ano |Ne |Ano |Ano |Ne |Ano |
| Ano |Ano |Ne |Ne |Ano |Ne |Ne |
| Ano |Ano |Ano |Ne |Ne |Ano |Ano |

## <a name="billing-for-cors-requests"></a>Fakturace požadavků CORS
Kontrola před výstupem úspěšné požadavky se účtují, pokud jste povolili CORS pro všechny služby storage pro váš účet (voláním [nastavit vlastnosti služby objektů Blob](https://msdn.microsoft.com/library/hh452235.aspx), [nastavit vlastnosti fronty služby](https://msdn.microsoft.com/library/hh452232.aspx), nebo [Nastavit vlastnosti služby Table](https://msdn.microsoft.com/library/hh452240.aspx)). Chcete-li minimalizovat náklady, zvažte nastavení **MaxAgeInSeconds** element ve vaší CORS pravidla velké hodnoty tak, aby uživatelský agent ukládá do mezipaměti žádosti.

Neúspěšná předběžných požadavků, nebude platit.

## <a name="next-steps"></a>Další postup
[Nastavit vlastnosti služby objektů Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Nastavit vlastnosti fronty služby](https://msdn.microsoft.com/library/hh452232.aspx)

[Nastavit vlastnosti služby Table](https://msdn.microsoft.com/library/hh452240.aspx)

[Specifikace pro sdílení prostředků různého původu W3C](http://www.w3.org/TR/cors/)

