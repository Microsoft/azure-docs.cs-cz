---
title: Vytvořit, spravovat a zabezpečit správce a dotaz klíče api Key - Azure Search
description: klíče API Key řídit přístup ke koncovému bodu služby. Klíče správce udělují oprávnění k zápisu. Pro přístup jen pro čtení můžete vytvořit klíče dotazu.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: heidist
ms.openlocfilehash: 64b07d37ce9267681ccfb5de3c7201586bd85b35
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273409"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Vytvoření a správa klíče rozhraní api pro službu Azure Search

Všechny požadavky na služby search nutné jen pro čtení klíče api-key, který byl vygenerován speciálně pro vaši službu. Klíč rozhraní api je jediným mechanismus ověřování přístupu do vašeho koncového bodu služby search a musí obsahovat u každého požadavku. V [REST řešení](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), klíč rozhraní api se obvykle zadává v hlavičce požadavku. V [řešení .NET](search-howto-dotnet-sdk.md#core-scenarios), je často zadaný jako nastavení konfigurace a pak předán jako klíč [pověření](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (klíč správce) nebo [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (klíč dotazu) na [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Klíče jsou vytvořeny pomocí služby search během zřizování služby. Můžete zobrazit a získat hodnoty klíče v [webu Azure portal](https://portal.azure.com).

![Stránka portálu, nastavení klíče oddílu](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Co je klíč rozhraní api

Klíč rozhraní api se řetězec skládá náhodně generované čísel a písmen. Prostřednictvím [oprávnění na základě rolí](search-security-rbac.md), můžete odstranit nebo čtení klíčů, ale nelze nahradit klíč uživatelské heslo nebo použít službu Active Directory jako metodologii primární ověřování pro přístup k operace hledání. 

Dva typy klíče používané pro přístup k vaší vyhledávací služby: admin (čtení a zápis) a dotaz (jen pro čtení).

|Klíč|Popis|Omezení|  
|---------|-----------------|------------|  
|Správa|Uděluje úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat.<br /><br /> Dva klíče správce, označuje jako *primární* a *sekundární* klíče na portálu jsou generovány, pokud je služba se vytvoří a může znovu vygenerovat jednotlivě na vyžádání. Existence dvou klíčů umožňuje nespotřebujete jeden z nich při používání druhý klíč pro přístup ke službě.<br /><br /> Klíče správce se zadávají pouze v hlavičkách žádosti protokolu HTTP. Rozhraní api-key správce nelze umístit v adrese URL.|Maximálně 2 na službu|  
|Dotaz|Uděluje oprávnění jen pro čtení k indexům a dokumentům a obvykle se distribuují klientským aplikacím, které vydávají požadavky hledání.<br /><br /> Na vyžádání se vytvoří klíče dotazu. Můžete je vytvořit ručně na portálu nebo prostřednictvím kódu programu přes [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klíče dotazu se dá nastavit v záhlaví požadavku HTTP pro hledání, návrh nebo operace vyhledávání. Alternativně můžete předat klíč dotazu jako parametr na adrese URL. V závislosti na tom, jak klientské aplikace výrobky zpracovává žádost může být snazší předat klíč jako parametr dotazu:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 na službu|  

 Vizuálně není žádný rozdíl mezi klíč správce a klíč dotazu. Oba klíče jsou řetězce, na které se skládá z 32 náhodně generované alfanumerické znaky. Pokud ztratíte přehled o jaký typ klíče, který je zadán v aplikaci, můžete si [zkontrolujte hodnoty klíče na portálu](https://portal.azure.com) nebo použijte [rozhraní REST API](https://docs.microsoft.com/rest/api/searchmanagement/) vrátí hodnotu a typ klíče.  

> [!NOTE]  
>  Bude považován za špatné bezpečnostních předávat citlivá data `api-key` v identifikátoru URI požadavku. Z tohoto důvodu Azure Search přijímá pouze klíč dotazu jako `api-key` v dotazu řetězec který byste se vyhnout tím, pokud by měl být veřejně dostupný obsah vašeho indexu. Obecně platí, doporučujeme předávání vaše `api-key` jako hlavičku požadavku.  

## <a name="find-existing-keys"></a>Vyhledání existujícího klíče

Můžete získat přístupové klíče na portálu nebo prostřednictvím [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/). Další informace najdete v tématu [spravovat správce a dotaz klíče api Key](search-security-api-keys.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Seznam [vyhledávací služby](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a klikněte na stránce Přehled **nastavení** >**klíče** zobrazíte klíče správce a dotazu.

   ![Stránka portálu, nastavení klíče oddílu](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Vytvoření klíče dotazu

Klíče dotazu se používají pro přístup jen pro čtení na dokumenty v indexu pro operace, které cílí na kolekce dokumentů. Hledání, filtrování a návrhů dotazů jsou všechny operace, které trvat klíč dotazu. Jakákoli jen pro čtení operace, která vrátí systému, dat nebo objektových definice, jako je například stav definice nebo indexeru indexu vyžaduje klíč správce.

Omezení přístupu a operací v klientské aplikace je nezbytné pro ochranu vyhledávání prostředků pro vaši službu. Vždy používejte klíč dotazů místo klíč správce pro jakýkoli dotaz pocházející z klientské aplikace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Seznam [vyhledávací služby](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a klikněte na stránce Přehled **nastavení** >**klíče**.
4. Klikněte na tlačítko **spravovat klíče dotazů**.
5. Použít klíč dotazu již vytvořených pro vaši službu nebo vytvořit až 50 nové klíče dotazu. Výchozí klíč dotazu není s názvem, ale může mít název klíče dotazu další možnosti správy.

   ![Vytvořit nebo použít klíč dotazu](media/search-security-overview/create-query-key.png) 

> [!Note]
> Příklad kódu znázorňující použití klíče dotazu lze nalézt v [dotazování indexu Azure Search v C# ](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

Dva klíče správce se vytvoří pro každou službu tak, aby můžete otočit primární klíč, pomocí sekundárního klíče pro nepřetržitý chod podniků.

1. V **nastavení** >**klíče** stránky, zkopírujte sekundární klíč.
2. Pro všechny aplikace aktualizace nastavení api-key používat sekundární klíč.
3. Znovu vygenerovat primární klíč.
4. Aktualizujte všechny aplikace, aby používaly nový primární klíč.

Pokud jste omylem znovu generovali oba klíče současně, všechny požadavky klientů pomocí těchto klíčů se nezdaří s HTTP 403 Zakázáno. Ale obsah není odstraněn, a můžete nejsou trvale uzamčen. 

Můžete nadále přístup ke službě pomocí portálu nebo vrstva správy ([rozhraní REST API](https://docs.microsoft.com/rest/api/searchmanagement/), [Powershellu](https://docs.microsoft.com/azure/search/search-manage-powershell), nebo Azure Resource Manager). Funkce správy jsou rozhodnou pomocí ID předplatného není službu klíč api-key a proto stále k dispozici i v případě, že se vaše klíče rozhraní api. 

Po vytvoření nového klíče účtů prostřednictvím portálu nebo správu vrstvy přístupu k vašemu obsahu (indexy, indexery, zdroje dat, map synonym) obnovení po nové klíče a zadejte tyto klíče u požadavků.

## <a name="secure-api-keys"></a>Zabezpečení klíče api Key
Zabezpečení klíče je zajištěno omezení přístupu prostřednictvím portálu nebo rozhraní správce prostředků (prostředí PowerShell nebo rozhraní příkazového řádku). Jak je uvedeno, správci předplatného můžete zobrazit a znovu generovat všechny klíče rozhraní api. Jako preventivní opatření zkontrolujte přiřazení rolí pochopit, kdo má přístup ke klíčům správce.

+ Na řídicím panelu služby, klikněte na tlačítko **řízení přístupu (IAM)** a pak **přiřazení rolí** kartě přiřazení rolí pro vaši službu.

Členy z těchto druhů rolí můžete zobrazit a znovu generovat klíče: Vlastník, Přispěvatel, [hledání služby přispěvatelé](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Pro přístup na základě identity výsledků vyhledávání můžete vytvořit filtry zabezpečení mají být odebrány výsledky podle identity, odebírání dokumentů, pro které žadatel by přístup mít neměly. Další informace najdete v tématu [filtry zabezpečení](search-security-trimming-for-azure-search.md) a [zabezpečené službou Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Řízení přístupu na základě role ve službě Azure Search](search-security-rbac.md)
+ [Správa s využitím PowerShellu](search-manage-powershell.md) 
+ [Výkon a optimalizace článku](search-performance-optimization.md)