---
title: Vytváření, správa a zabezpečení klíčů rozhraní api pro správce a dotaz
titleSuffix: Azure Cognitive Search
description: Klíč rozhraní api řídí přístup ke koncovému bodu služby. Klíče správce udělují přístup pro zápis. Klíče dotazu lze vytvořit pro přístup jen pro čtení.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794374"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Vytvoření a správa klíčů api pro službu Azure Cognitive Search

Všechny požadavky na vyhledávací službu potřebují klíč api jen pro čtení, který byl vygenerován speciálně pro vaši službu. Klíč rozhraní api je jediným mechanismem pro ověřování přístupu ke koncovému bodu vyhledávací služby a musí být zahrnuta na každém požadavku. V [řešení rest](search-get-started-postman.md)klíč api je obvykle zadán v hlavičce požadavku. V [řešeních .NET](search-howto-dotnet-sdk.md#core-scenarios)je klíč často určen jako nastavení konfigurace a poté předán jako [pověření](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (klíč správce) nebo [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (klíč dotazu) na [programu SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Klíče jsou vytvořeny pomocí vyhledávací služby během zřizování služeb. Hodnoty klíče můžete zobrazit a získat na [webu Azure Portal](https://portal.azure.com).

![Stránka portálu, Nastavení, Klíče](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Co je api-key

Klíč rozhraní api je řetězec složený z náhodně generovaných čísel a písmen. Pomocí [oprávnění založených na rolích](search-security-rbac.md)můžete klíče odstranit nebo číst, ale nemůžete klíč nahradit uživatelem definovaným heslem nebo použít službu Active Directory jako primární metodiku ověřování pro přístup k vyhledávacím operacím. 

Pro přístup k vyhledávací službě se používají dva typy klíčů: admin (read-write) a query (jen pro čtení).

|Klíč|Popis|Omezení|  
|---------|-----------------|------------|  
|Správce|Uděluje úplná práva všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat.<br /><br /> Dva klíče správce, označované jako *primární* a *sekundární* klíče na portálu, jsou generovány při vytvoření služby a lze je jednotlivě obnovit na vyžádání. S dvěma klíči umožňuje převrátit jeden klíč při použití druhého klíče pro trvalý přístup ke službě.<br /><br /> Klíče správce jsou určeny pouze v hlavičkách požadavků HTTP. Do adresy URL nelze umístit klíč rozhraní API správce.|Maximálně 2 na službu|  
|Dotaz|Uděluje přístup jen pro čtení indexy a dokumenty a jsou obvykle distribuovány do klientských aplikací, které vydávají požadavky na vyhledávání.<br /><br /> Klávesy dotazu jsou vytvořeny na vyžádání. Můžete je vytvořit ručně na portálu nebo programově prostřednictvím [rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klíče dotazu lze zadat v hlavičce požadavku HTTP pro vyhledávání, návrh nebo vyhledávací operaci. Případně můžete předat klíč dotazu jako parametr na adrese URL. V závislosti na tom, jak klientská aplikace formuluje požadavek, může být jednodušší předat klíč jako parametr dotazu:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 na službu|  

 Vizuálně není rozdíl mezi klíčem správce nebo klíčem dotazu. Oba klíče jsou řetězce složené z 32 náhodně generovaných alfanumerických znaků. Pokud ztratíte přehled o tom, jaký typ klíče je zadán ve vaší aplikaci, můžete [zkontrolovat hodnoty klíče na portálu](https://portal.azure.com) nebo pomocí rozhraní REST [API](https://docs.microsoft.com/rest/api/searchmanagement/) vrátit hodnotu a typ klíče.  

> [!NOTE]  
>  Je považován za špatnou praxi zabezpečení předat `api-key` citlivá data, jako je například v požadavku URI. Z tohoto důvodu Azure Cognitive Search přijímá pouze `api-key` klíč dotazu jako v řetězci dotazu a měli byste se tomu vyhnout, pokud obsah indexu by měl být veřejně dostupný. Obecně platí, že doporučujeme `api-key` předat jako hlavičku požadavku.  

## <a name="find-existing-keys"></a>Vyhledání existujících klíčů

Přístupové klíče můžete získat na portálu nebo prostřednictvím [rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/). Další informace naleznete v [tématu Správa klíčů rozhraní API správce a dotazu](search-security-api-keys.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Seznam [vyhledávacích služeb](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na **Nastavení** >**kláves** a zobrazte klíče správce a dotazu.

   ![Stránka portálu, Nastavení, Klíče](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Vytvoření klíčů dotazu

Klávesy dotazu se používají pro přístup jen pro čtení k dokumentům v rámci indexu pro operace zaměřené na kolekci dokumentů. Vyhledávací, filtrované a návrhové dotazy jsou všechny operace, které berou klíč dotazu. Každá operace jen pro čtení, která vrací systémová data nebo definice objektů, jako je například definice indexu nebo stav indexeru, vyžaduje klíč správce.

Omezení přístupu a provozu v klientských aplikacích je nezbytné pro zabezpečení prostředků vyhledávání ve vaší službě. Pro jakýkoli dotaz pocházející z klientské aplikace vždy používejte klíč dotazu, nikoli klíč správce.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Seznam [vyhledávacích služeb](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na **nastavení** >**kláves**.
4. Klepněte na **tlačítko Spravovat klávesy dotazu**.
5. Použijte klíč dotazu, který je již vygenerovaný pro vaši službu, nebo vytvořte až 50 nových klíčů dotazu. Výchozí klíč dotazu není pojmenován, ale další klíče dotazu lze pojmenovat pro správu.

   ![Vytvoření nebo použití klíče dotazu](media/search-security-overview/create-query-key.png) 

> [!Note]
> Příklad kódu zobrazující využití klíče dotazu najdete v [dotazu a Indexu azure kognitivní vyhledávání v C#](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Obnovit klíče správce

Pro každou službu jsou vytvořeny dva klíče správce, takže můžete otočit primární klíč pomocí sekundárního klíče pro kontinuitu podnikání.

1. Na stránce**Klíče** **nastavení** >zkopírujte sekundární klíč.
2. Pro všechny aplikace aktualizujte nastavení klíče rozhraní api a použijte sekundární klíč.
3. Znovu vygenerovat primární klíč.
4. Aktualizujte všechny aplikace tak, aby používaly nový primární klíč.

Pokud neúmyslně znovu vygenerujete oba klíče současně, všechny požadavky klientů pomocí těchto klíčů se nezdaří s HTTP 403 Zakázáno. Obsah však není odstraněn a nejste trvale uzamčeni. 

Ke službě můžete stále přistupovat prostřednictvím portálu nebo vrstvy pro správu[(ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)nebo Správce prostředků Azure). Funkce správy jsou operativní prostřednictvím ID předplatného není klíč rozhraní api služby, a proto stále k dispozici i v případě, že vaše klíče api nejsou. 

Po vytvoření nových klíčů prostřednictvím portálu nebo vrstvy správy se přístup k obsahu (indexy, indexery, zdroje dat, mapy synonym), jakmile budete mít nové klíče a poskytnete tyto klíče na základě požadavků.

## <a name="secure-api-keys"></a>Zabezpečené klíče api
Zabezpečení klíčů je zajištěno omezením přístupu prostřednictvím portálu nebo rozhraní Správce prostředků (PowerShell nebo rozhraní příkazového řádku). Jak již bylo uvedeno, správci předplatného můžete zobrazit a obnovit všechny klíče rozhraní api. Jako preventivní opatření zkontrolujte přiřazení rolí, abyste pochopili, kdo má přístup ke klíčům správce.

+ Na řídicím panelu služby klikněte na **ovládací prvek Přístupu (IAM)** a potom na kartě **Přiřazení rolí** a zobrazte přiřazení rolí pro vaši službu.

Členové následujících rolí mohou zobrazit a obnovit klíče: Vlastník, Přispěvatel, [Přispěvatelé vyhledávacích služeb](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Pro přístup založený na identitě přes výsledky hledání můžete vytvořit filtry zabezpečení pro oříznutí výsledků podle identity a odebrání dokumentů, pro které by žadatel neměl mít přístup. Další informace naleznete v [tématu Security filters](search-security-trimming-for-azure-search.md) and [Secure with Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Viz také

+ [Řízení přístupu na základě rolí v Azure Cognitive Search](search-security-rbac.md)
+ [Správa pomocí prostředí PowerShell](search-manage-powershell.md) 
+ [Výkon a optimalizace článku](search-performance-optimization.md)