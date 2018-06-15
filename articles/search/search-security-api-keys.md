---
title: Vytvářet, spravovat a zabezpečit správce a dotaz klíče api Key pro službu Azure Search | Microsoft Docs
description: klíče API Key řízení přístupu ke koncovému bodu služby. Klíče správce udělení oprávnění k zápisu. Klíče dotazu lze vytvořit pro přístup jen pro čtení.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 4215795b7cd2a25427a3ce9b3cde16bfc69cb009
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185939"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Vytvářet a spravovat klíče api Key pro službu Azure Search

Všechny žádosti o službu vyhledávání potřebovat rozhraní api klíč, který byl vygenerován speciálně pro vaši službu. Tento klíč rozhraní api se jedná o jedinou mechanismus pro ověřování přístupu do vašeho koncového bodu služby vyhledávání. 

Klíč rozhraní api je řetězec tvořený náhodně generované číslic a písmen. Prostřednictvím [oprávnění na základě rolí](search-security-rbac.md), můžete odstranit nebo čtení klíčů, ale nelze nahradit klíč uživatelské heslo nebo použít služby Active Directory jako primární ověřování metody pro přístup k operace hledání. 

Dva typy klíčů se používají k přístupu ke službě vyhledávání: správce (pro čtení a zápis) a dotazů (jen pro čtení).

|Klíč|Popis|Omezení|  
|---------|-----------------|------------|  
|Správa|Uděluje úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat.<br /><br /> Dva klíče správce, označuje jako *primární* a *sekundární* klíče na portálu, jsou generovány, pokud služba je vytvořena a můžete znovu vygeneruje jednotlivě na vyžádání. Máte dva klíče můžete vrátit přes jeden klíč při použití druhý klíč pro nepřetržitý přístup ke službě.<br /><br /> Správce klíčů jsou určené jenom v hlavičkách žádosti protokolu HTTP. Rozhraní api-key správce nelze umístit v adrese URL.|Maximálně 2 na služby|  
|Dotaz|Uděluje přístup jen pro čtení k indexům a dokumentům a obvykle se distribuují klientským aplikacím, které vydávají požadavky hledání.<br /><br /> Klíče dotazu jsou vytvořeny na vyžádání. Můžete je vytvořit ručně na portálu nebo prostřednictvím kódu programu prostřednictvím [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klíče dotazu lze zadat v hlavičce požadavku HTTP pro vyhledávání, návrh nebo operace vyhledávání. Alternativně můžete předat klíč dotazu jako parametr na adresu URL. V závislosti na tom, jak klientské aplikace výrobky zpracovává požadavek může být snazší předat klíč jako parametr dotazu:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 pro službu|  

 Vizuální není žádný rozdíl mezi klíč správce a klíč dotazu. Oba klíče se, řetězce tvořený 32 náhodně generované alfanumerické znaky. Pokud ztratíte zaznamenávat, jaký typ klíče je zadán v aplikaci, můžete [zkontrolujte hodnoty klíče na portálu](https://portal.azure.com) nebo použít [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) vrátí hodnotu a typ klíče.  

> [!NOTE]  
>  Postup nízký zabezpečení k předávání citlivých dat, jako považuje `api-key` v identifikátoru URI požadavku. Z tohoto důvodu Azure Search přijímá pouze klíč dotazu jako `api-key` v dotazu řetězec a vy byste neměli tak, pokud obsah indexu by měly být veřejně dostupné. Obecně platí, doporučujeme předávání vaší `api-key` jako hlavičku požadavku.  

## <a name="find-api-keys-for-your-service"></a>Najít klíče api Key služby

Můžete získat přístupové klíče na portálu nebo prostřednictvím [REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/). Další informace najdete v tématu [spravovat správce a dotaz klíče api Key](search-security-api-keys.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Seznam [vyhledávací služby](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
3. Vyberte službu a na stránce služby najít **nastavení** >**klíče** zobrazíte klíče správce a dotazu.

![Stránky portálu, nastavení klíče oddílu](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

Jsou vytvořeny dva klíče správce pro každou službu, aby bylo možné výměny primární klíč, pomocí sekundární klíč pro nepřetržitý přístup.

Pokud jste znovu vygenerovat primární a sekundární klíče ve stejnou dobu, bude všechny aplikace, která používá buď klíč pro přístup k operací služby již mít přístup ke službě.

1. V **nastavení** >**klíče** stránky, zkopírujte sekundární klíč.
2. Pro všechny aplikace aktualizujte nastavení klíč rozhraní api používat sekundární klíč.
3. Obnovit primární klíč.
4. Aktualizujte všechny aplikace na používání nové primární klíče.

## <a name="secure-api-keys"></a>Zabezpečovací klíče rozhraní api
Zabezpečení klíčů je zajištěno omezení přístupu prostřednictvím portálu nebo rozhraní správce prostředků (prostředí PowerShell nebo rozhraní příkazového řádku). Jak jsme uvedli, správci předplatného můžete zobrazit a obnovit všechny klíče rozhraní api. Z důvodu zkontrolujte přiřazení rolí pochopit, kdo má přístup ke klíčům správce.

+ V řídicím panelu služby, klikněte na **přístup k ovládacímu prvku (IAM)** zobrazíte přiřazení rolí pro vaši službu.

Můžete zobrazit a znovu vygenerovat klíče členy následujících rolí: vlastník, Přispěvatel, [přispěvatelé služby vyhledávání](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Pro přístup na základě identity prostřednictvím výsledky hledání můžete vytvořit filtry zabezpečení oříznout výsledky identita, odebírání dokumentů, pro které žadatel by neměly mít přístup. Další informace najdete v tématu [filtrů zabezpečení](search-security-trimming-for-azure-search.md) a [zabezpečeného se službou Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Řízení přístupu na základě role ve službě Azure Search](search-security-rbac.md)
+ [Správa s využitím PowerShellu](search-manage-powershell.md) 
+ [Výkon a optimalizace článku](search-performance-optimization.md)