---
title: Vytváření, Správa a zabezpečení správců a dotazů – klíče rozhraní API
titleSuffix: Azure Cognitive Search
description: Klíč rozhraní API ovládá přístup ke koncovému bodu služby. Klíče správce udělují přístup pro zápis. Klíče dotazů lze vytvořit pro přístup jen pro čtení.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 5935bc3f59585b19fc3b45bdfd567bb1f9404234
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675584"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Vytváření a Správa klíčů rozhraní API pro službu Azure Kognitivní hledání

Všechny požadavky na vyhledávací službu potřebují jen pro čtení `api-key` , které byly vygenerovány speciálně pro vaši službu. `api-key`Je jediným mechanismem pro ověření přístupu ke koncovému bodu služby Search a musí se zahrnout do každého požadavku. 

+ V [řešeních REST](search-get-started-postman.md)se klíč rozhraní API obvykle určuje v hlavičce požadavku.

+ V [řešeních .NET](search-howto-dotnet-sdk.md)je klíč často určen jako nastavení konfigurace a pak předán jako [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Při zřizování služby se pomocí vyhledávací služby vytvoří klíče. Můžete zobrazit a získat klíčové hodnoty v [Azure Portal](https://portal.azure.com).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Stránka portálu, načíst nastavení, oddíl klíče" border="false":::

## <a name="what-is-an-api-key"></a>Co je klíč rozhraní API

Klíč rozhraní API je řetězec tvořený náhodně generovanými čísly a písmeny. Prostřednictvím [oprávnění na základě rolí](search-security-rbac.md)můžete klíče odstranit nebo číst, ale nemůžete nahradit klíč uživatelsky definovaným heslem nebo použít službu Active Directory jako primární metodologii ověřování pro přístup k operacím vyhledávání. 

Pro přístup k vaší vyhledávací službě se používají dva typy klíčů: správce (čtení i zápis) a dotaz (jen pro čtení).

|Klíč|Popis|Omezení|  
|---------|-----------------|------------|  
|Správce|Udělí úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat.<br /><br /> Dva klíče správce, které se v portálu označují jako *primární* a *sekundární* klíče, se generují při vytvoření služby a dají se jednotlivě znovu vygenerovat na vyžádání. Použití dvou klíčů vám umožní přenášet jeden klíč při použití druhého klíče pro pokračování přístupu ke službě.<br /><br /> Klíče správce se zadává jenom v hlavičkách požadavku HTTP. Do adresy URL nemůžete umístit klíč rozhraní API pro správu.|Maximálně 2 na službu|  
|Dotaz|Uděluje přístup k indexům a dokumentům jen pro čtení a jsou obvykle distribuovány klientským aplikacím, které vydávají požadavky na hledání.<br /><br /> Klíče dotazů se vytvářejí na vyžádání. Můžete je vytvořit ručně na portálu nebo programově prostřednictvím [REST API pro správu](/rest/api/searchmanagement/).<br /><br /> Klíče dotazů lze zadat v hlavičce požadavku HTTP pro hledání, návrh nebo operaci vyhledávání. Případně můžete klíč dotazu předat jako parametr na adrese URL. V závislosti na tom, jak vaše klientská aplikace tento požadavek formuluje, může být snazší klíč předat jako parametr dotazu:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 na službu|  

 Vizuálně nerozlišuje klíč správce nebo klíč dotazu. Oba klíče jsou řetězce složené z 32 náhodně generovaných alfanumerických znaků. Pokud ztratíte přehled o tom, jaký typ klíče je zadán v aplikaci, můžete [zjistit hodnoty klíčů na portálu](https://portal.azure.com) nebo použít [REST API](/rest/api/searchmanagement/) k vrácení hodnoty a typu klíče.  

> [!NOTE]  
>  Považuje se za špatný bezpečnostní postup pro předávání citlivých dat, jako je například `api-key` v identifikátoru URI požadavku. Z tohoto důvodu Azure Kognitivní hledání akceptuje klíč dotazu jenom jako `api-key` v řetězci dotazu a měli byste tomu předejít, pokud by obsah vašeho indexu neměl být veřejně dostupný. Jako obecné pravidlo doporučujeme předat `api-key` jako hlavičku požadavku.  

## <a name="find-existing-keys"></a>Najít existující klíče

Přístupové klíče můžete získat na portálu nebo prostřednictvím [REST API pro správu](/rest/api/searchmanagement/). Další informace najdete v tématu [Správa klíčů rozhraní API pro správu a dotazy](search-security-api-keys.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vypíše [služby vyhledávání](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na možnost klíče **Nastavení**  > **Keys** . zobrazí se klíče pro správu a dotazy.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Stránka portálu, načíst nastavení, oddíl klíče" border="false":::

## <a name="create-query-keys"></a>Vytváření klíčů dotazů

Klíče dotazů jsou používány pro přístup jen pro čtení k dokumentům v rámci indexu pro operace, které cílí na kolekci dokumentů. Dotazy na hledání, filtrování a návrhy jsou všechny operace, které přijímají klíč dotazu. Jakákoli operace jen pro čtení, která vrací systémové data nebo definice objektů, jako je definice indexu nebo stav indexeru, vyžaduje klíč správce.

Omezení přístupu a operací v klientských aplikacích je nezbytné pro zabezpečení prostředků vyhledávání ve vaší službě. Vždy používejte klíč dotazu spíše než klíč správce pro jakýkoli dotaz pocházející z klientské aplikace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vypíše [služby vyhledávání](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na **Nastavení**  > **klíče** .
4. Klikněte na **Správa klíčů dotazů** .
5. Použijte klíč dotazu, který jste už vygenerovali pro vaši službu, nebo vytvořte až 50 nových klíčů dotazů. Výchozí klíč dotazu není pojmenován, ale další klíče dotazu lze pojmenovat pro spravovatelnost.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Stránka portálu, načíst nastavení, oddíl klíče" border="false":::

> [!Note]
> Příklad kódu, který ukazuje použití klíče dotazu, najdete v [dotazování indexu služby Azure kognitivní hledání v jazyce C#](./search-get-started-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

Pro každou službu se vytvoří dva klíče správce, abyste mohli otočit primární klíč pomocí sekundárního klíče pro provozní kontinuitu.

1. Na stránce **Settings**  > **klíče** nastavení zkopírujte sekundární klíč.
2. U všech aplikací aktualizujte nastavení klíče rozhraní API tak, aby používalo sekundární klíč.
3. Znovu vygenerujte primární klíč.
4. Aktualizujte všechny aplikace tak, aby používaly nový primární klíč.

Pokud současně znovu vygenerujete oba klíče, všechny požadavky klienta používající tyto klíče selžou s protokolem HTTP 403 zakázáno. Obsah se ale neodstraní a trvale nebudete uzamčen. 

Ke službě můžete přistupovat i přes portál nebo vrstvu správy ([REST API](/rest/api/searchmanagement/), [PowerShell](./search-manage-powershell.md)nebo Azure Resource Manager). Funkce správy jsou fungující prostřednictvím ID předplatného, které není klíčem rozhraní API služby, a tak tak pořád dostupné i v případě, že klíče API-nejsou. 

Po vytvoření nových klíčů prostřednictvím portálu nebo vrstvy správy se přístup obnoví na váš obsah (indexy, indexery, zdroje dat, mapy synonym), jakmile budete mít nové klíče a zadáte tyto klíče na požadavky.

## <a name="secure-api-keys"></a>Secure API – klíče
Zabezpečení klíčů je zajištěno omezením přístupu prostřednictvím portálu nebo rozhraní Správce prostředků (PowerShell nebo rozhraní příkazového řádku). Jak je uvedeno, správci předplatného můžou zobrazit a znovu vygenerovat všechny klíče API-Key. Je nutné zkontrolovat přiřazení rolí a pochopit, kdo má přístup k klíčům správce.

+ Na řídicím panelu služby klikněte na možnost **řízení přístupu (IAM)** a pak na kartu **přiřazení rolí** pro zobrazení přiřazení rolí pro vaši službu.

Členové následujících rolí mohou zobrazovat a obnovovat klíče: vlastník, přispěvatel, [Search Service přispěvatelé](../role-based-access-control/built-in-roles.md#search-service-contributor) .

> [!Note]
> Pro přístup na základě identity přes výsledky hledání můžete vytvořit filtry zabezpečení pro oříznutí výsledků podle identity a odebrání dokumentů, ke kterým by žadatel neměl mít přístup. Další informace najdete v tématech [filtry zabezpečení](search-security-trimming-for-azure-search.md) a zabezpečení [pomocí služby Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Viz také

+ [Řízení přístupu na základě role v Azure Kognitivní hledání](search-security-rbac.md)
+ [Správa pomocí prostředí PowerShell](search-manage-powershell.md) 
+ [Článek o výkonu a optimalizaci](search-performance-optimization.md)