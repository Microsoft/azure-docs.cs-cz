---
title: Vytváření, Správa a zabezpečení správce a rozhraní API pro dotazování klíčů – Azure Search
description: API-Keys řídí přístup ke koncovému bodu služby. Klíče správce udělují přístup pro zápis. Klíče dotazů lze vytvořit pro přístup jen pro čtení.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: d3880ed367ebe33e04f37b139927b75e3d01b178
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855784"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Vytváření a Správa klíčů rozhraní API pro službu Azure Search

Všechny požadavky na vyhledávací službu potřebují klíč rozhraní API jen pro čtení, který se vygeneroval speciálně pro vaši službu. Klíč rozhraní API je jediným mechanismem pro ověřování přístupu ke koncovému bodu vyhledávací služby a musí být součástí každého požadavku. V [řešeních REST](search-get-started-postman.md)je klíč rozhraní API obvykle určený v hlavičce požadavku. V [řešeních .NET](search-howto-dotnet-sdk.md#core-scenarios)se klíč často určuje jako konfigurační nastavení a pak se předává jako [přihlašovací údaje](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (klíč správce) nebo [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (klíč dotazu) na [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Při zřizování služby se pomocí vyhledávací služby vytvoří klíče. Můžete zobrazit a získat klíčové hodnoty v [Azure Portal](https://portal.azure.com).

![Stránka portálu, nastavení, sekce klíče](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Co je klíč rozhraní API

Klíč rozhraní API je řetězec tvořený náhodně generovanými čísly a písmeny. Prostřednictvím [oprávnění na základě rolí](search-security-rbac.md)můžete klíče odstranit nebo číst, ale nemůžete nahradit klíč uživatelsky definovaným heslem nebo použít službu Active Directory jako primární metodologii ověřování pro přístup k operacím vyhledávání. 

Pro přístup k vaší vyhledávací službě se používají dva typy klíčů: správce (čtení i zápis) a dotaz (jen pro čtení).

|Klíč|Popis|Omezení|  
|---------|-----------------|------------|  
|Správce|Udělí úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat.<br /><br /> Dva klíče správce, které se v portálu označují jako *primární* a *sekundární* klíče, se generují při vytvoření služby a dají se jednotlivě znovu vygenerovat na vyžádání. Použití dvou klíčů vám umožní přenášet jeden klíč při použití druhého klíče pro pokračování přístupu ke službě.<br /><br /> Klíče správce se zadává jenom v hlavičkách požadavku HTTP. Do adresy URL nemůžete umístit klíč rozhraní API pro správu.|Maximálně 2 na službu|  
|Dotaz|Uděluje přístup k indexům a dokumentům jen pro čtení a jsou obvykle distribuovány klientským aplikacím, které vydávají požadavky na hledání.<br /><br /> Klíče dotazů se vytvářejí na vyžádání. Můžete je vytvořit ručně na portálu nebo programově prostřednictvím [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klíče dotazů lze zadat v hlavičce požadavku HTTP pro hledání, návrh nebo operaci vyhledávání. Případně můžete klíč dotazu předat jako parametr na adrese URL. V závislosti na tom, jak vaše klientská aplikace tento požadavek formuluje, může být snazší klíč předat jako parametr dotazu:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 na službu|  

 Vizuálně nerozlišuje klíč správce nebo klíč dotazu. Oba klíče jsou řetězce složené z 32 náhodně generovaných alfanumerických znaků. Pokud ztratíte přehled o tom, jaký typ klíče je zadán v aplikaci, můžete [zjistit hodnoty klíčů na portálu](https://portal.azure.com) nebo použít [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) k vrácení hodnoty a typu klíče.  

> [!NOTE]  
>  Považuje se za špatný bezpečnostní postup pro předávání citlivých dat, jako `api-key` je například v identifikátoru URI požadavku. Z tohoto důvodu Azure Search akceptuje klíč dotazu jenom jako `api-key` v řetězci dotazu a měli byste se vyhnout tomu, pokud by obsah vašeho indexu neměl být veřejně dostupný. Jako obecné pravidlo doporučujeme předat `api-key` jako hlavičku požadavku.  

## <a name="find-existing-keys"></a>Najít existující klíče

Přístupové klíče můžete získat na portálu nebo prostřednictvím [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/). Další informace najdete v tématu [Správa klíčů rozhraní API pro správu a dotazy](search-security-api-keys.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vypíše [služby vyhledávání](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na možnost**klíče** **Nastavení** >. zobrazí se klíče pro správu a dotazy.

   ![Stránka portálu, nastavení, sekce klíče](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Vytváření klíčů dotazů

Klíče dotazů jsou používány pro přístup jen pro čtení k dokumentům v rámci indexu pro operace, které cílí na kolekci dokumentů. Dotazy na hledání, filtrování a návrhy jsou všechny operace, které přijímají klíč dotazu. Jakákoli operace jen pro čtení, která vrací systémové data nebo definice objektů, jako je definice indexu nebo stav indexeru, vyžaduje klíč správce.

Omezení přístupu a operací v klientských aplikacích je nezbytné pro zabezpečení prostředků vyhledávání ve vaší službě. Vždy používejte klíč dotazu spíše než klíč správce pro jakýkoli dotaz pocházející z klientské aplikace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vypíše [služby vyhledávání](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na **Nastavení** >**klíče**.
4. Klikněte na **Správa klíčů dotazů**.
5. Použijte klíč dotazu, který jste už vygenerovali pro vaši službu, nebo vytvořte až 50 nových klíčů dotazů. Výchozí klíč dotazu není pojmenován, ale další klíče dotazu lze pojmenovat pro spravovatelnost.

   ![Vytvoření nebo použití klíče dotazu](media/search-security-overview/create-query-key.png) 

> [!Note]
> Příklad kódu zobrazujícího použití klíče dotazu lze nalézt v [dotazu na Azure Search index v C# ](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

Pro každou službu se vytvoří dva klíče správce, abyste mohli otočit primární klíč pomocí sekundárního klíče pro provozní kontinuitu.

1. Na stránce**klíče** **Nastavení** >zkopírujte sekundární klíč.
2. U všech aplikací aktualizujte nastavení klíče rozhraní API tak, aby používalo sekundární klíč.
3. Znovu vygenerujte primární klíč.
4. Aktualizujte všechny aplikace tak, aby používaly nový primární klíč.

Pokud současně znovu vygenerujete oba klíče, všechny požadavky klienta používající tyto klíče selžou s protokolem HTTP 403 zakázáno. Obsah se ale neodstraní a trvale nebudete uzamčen. 

Ke službě můžete přistupovat i přes portál nebo vrstvu správy ([REST API](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)nebo Azure Resource Manager). Funkce správy jsou fungující prostřednictvím ID předplatného, které není klíčem rozhraní API služby, a tak tak pořád dostupné i v případě, že klíče API-nejsou. 

Po vytvoření nových klíčů prostřednictvím portálu nebo vrstvy správy se přístup obnoví na váš obsah (indexy, indexery, zdroje dat, mapy synonym), jakmile budete mít nové klíče a zadáte tyto klíče na požadavky.

## <a name="secure-api-keys"></a>Secure API – klíče
Zabezpečení klíčů je zajištěno omezením přístupu prostřednictvím portálu nebo rozhraní Správce prostředků (PowerShell nebo rozhraní příkazového řádku). Jak je uvedeno, správci předplatného můžou zobrazit a znovu vygenerovat všechny klíče API-Key. Je nutné zkontrolovat přiřazení rolí a pochopit, kdo má přístup k klíčům správce.

+ Na řídicím panelu služby klikněte na možnost **řízení přístupu (IAM)** a pak na kartu **přiřazení rolí** pro zobrazení přiřazení rolí pro vaši službu.

Členové následujících rolí mohou zobrazit a znovu vygenerovat klíče: Vlastník, přispěvatel, [Search Service přispěvatelé](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Pro přístup na základě identity přes výsledky hledání můžete vytvořit filtry zabezpečení pro oříznutí výsledků podle identity a odebrání dokumentů, ke kterým by žadatel neměl mít přístup. Další informace najdete v tématech [filtry zabezpečení](search-security-trimming-for-azure-search.md) a zabezpečení [pomocí služby Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Viz také:

+ [Řízení přístupu na základě role v Azure Search](search-security-rbac.md)
+ [Správa s využitím PowerShellu](search-manage-powershell.md) 
+ [Článek o výkonu a optimalizaci](search-performance-optimization.md)