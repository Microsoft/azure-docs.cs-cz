---
title: Ověřování klíčů rozhraní API
titleSuffix: Azure Cognitive Search
description: Klíč rozhraní API řídí příchozí přístup ke koncovému bodu služby. Klíče správce udělují přístup pro zápis. Klíče dotazů lze vytvořit pro přístup jen pro čtení.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 8b2e85744923fb2e7e474e049df1536aebc56f3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536929"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Vytvoření a Správa klíčů rozhraní API pro službu Azure Kognitivní hledání

Všechny požadavky na vyhledávací službu potřebují klíč rozhraní API jen pro čtení, který se vygeneroval speciálně pro vaši službu. Klíč rozhraní API je jediným mechanismem pro ověřování příchozích požadavků do koncového bodu služby Search a je vyžadován při každé žádosti. 

+ V [řešeních REST](search-get-started-rest.md) `api-key` je obvykle zadaný v hlavičce požadavku.

+ V [řešeních .NET](search-howto-dotnet-sdk.md)je klíč často určen jako nastavení konfigurace a pak předán jako [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Klíče rozhraní API můžete zobrazit a spravovat v [Azure Portal](https://portal.azure.com)nebo prostřednictvím [PowerShellu](/powershell/module/az.search), [Azure CLI](/cli/azure/search)nebo [REST API](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Stránka portálu, načíst nastavení, oddíl klíče" border="false":::

## <a name="what-is-an-api-key"></a>Co je klíč rozhraní API?

Klíč rozhraní API je jedinečný řetězec tvořený náhodně generovanými čísly a písmeny, která se předávají při každém požadavku na vyhledávací službu. Služba požadavek přijme, pokud jsou platné samotné žádosti i klíč. 

Pro přístup k vaší vyhledávací službě se používají dva typy klíčů: správce (čtení i zápis) a dotaz (jen pro čtení).

|Klíč|Popis|Omezení|  
|---------|-----------------|------------|  
|Správce|Udělí úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat.<br /><br /> Dva klíče správce, které se v portálu označují jako *primární* a *sekundární* klíče, se generují při vytvoření služby a dají se jednotlivě znovu vygenerovat na vyžádání. Použití dvou klíčů vám umožní přenášet jeden klíč při použití druhého klíče pro pokračování přístupu ke službě.<br /><br /> Klíče správce se zadává jenom v hlavičkách požadavku HTTP. Do adresy URL nelze umístit klíč rozhraní API pro správu.|Maximálně 2 na službu|  
|Dotaz|Uděluje přístup k indexům a dokumentům jen pro čtení a jsou obvykle distribuovány klientským aplikacím, které vydávají požadavky na hledání.<br /><br /> Klíče dotazů se vytvářejí na vyžádání.<br /><br /> Klíče dotazů lze zadat v hlavičce požadavku HTTP pro hledání, návrh nebo operaci vyhledávání. Případně můžete klíč dotazu předat jako parametr na adrese URL. V závislosti na tom, jak vaše klientská aplikace tento požadavek formuluje, může být snazší klíč předat jako parametr dotazu:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 na službu|  

 Vizuálně nerozlišuje klíč správce nebo klíč dotazu. Oba klíče jsou řetězce složené z 32 náhodně generovaných alfanumerických znaků. Pokud ztratíte přehled o tom, jaký typ klíče je zadaný v aplikaci, můžete na [portálu ověřit klíčové hodnoty](https://portal.azure.com).  

> [!NOTE]  
> Považuje se za špatný bezpečnostní postup pro předávání citlivých dat, jako je například `api-key` v identifikátoru URI požadavku. Z tohoto důvodu Azure Kognitivní hledání akceptuje klíč dotazu jenom jako `api-key` v řetězci dotazu a měli byste tomu předejít, pokud by obsah vašeho indexu neměl být veřejně dostupný. Jako obecné pravidlo doporučujeme předat `api-key` jako hlavičku požadavku.  

## <a name="find-existing-keys"></a>Najít existující klíče

Přístupové klíče můžete získat na portálu nebo pomocí [PowerShellu](/powershell/module/az.search), rozhraní příkazového [řádku Azure](/cli/azure/search)nebo [REST API](/rest/api/searchmanagement/).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vypíše [služby vyhledávání](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pro vaše předplatné.
1. Vyberte službu a na stránce Přehled klikněte na možnost klíče **Nastavení**  >  . zobrazí se klíče pro správu a dotazy.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Stránka portálu, nastavení zobrazení, sekce klíče" border="false":::

## <a name="create-query-keys"></a>Vytváření klíčů dotazů

Klíče dotazů jsou používány pro přístup jen pro čtení k dokumentům v rámci indexu pro operace, které cílí na kolekci dokumentů. Dotazy na hledání, filtrování a návrhy jsou všechny operace, které přijímají klíč dotazu. Jakákoli operace jen pro čtení, která vrací systémové data nebo definice objektů, jako je definice indexu nebo stav indexeru, vyžaduje klíč správce.

Omezení přístupu a operací v klientských aplikacích je nezbytné pro zabezpečení prostředků vyhledávání ve vaší službě. Vždy používejte klíč dotazu spíše než klíč správce pro jakýkoli dotaz pocházející z klientské aplikace.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vypíše [služby vyhledávání](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  pro vaše předplatné.
3. Vyberte službu a na stránce Přehled klikněte na **Nastavení**  > **klíče**.
4. Klikněte na **Správa klíčů dotazů**.
5. Použijte klíč dotazu, který jste už vygenerovali pro vaši službu, nebo vytvořte až 50 nových klíčů dotazů. Výchozí klíč dotazu není pojmenován, ale další klíče dotazu lze pojmenovat pro spravovatelnost.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Vytvoření nebo použití klíče dotazu" border="false":::

> [!Note]
> Příklad kódu zobrazujícího použití klíče dotazu najdete v [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

Pro každou službu se vytvoří dva klíče správce, abyste mohli otočit primární klíč pomocí sekundárního klíče pro provozní kontinuitu.

1. Na stránce   > **klíče** nastavení zkopírujte sekundární klíč.
2. U všech aplikací aktualizujte nastavení klíče rozhraní API tak, aby používalo sekundární klíč.
3. Znovu vygenerujte primární klíč.
4. Aktualizujte všechny aplikace tak, aby používaly nový primární klíč.

Pokud současně znovu vygenerujete oba klíče, všechny požadavky klienta používající tyto klíče selžou s protokolem HTTP 403 zakázáno. Obsah se ale neodstraní a trvale nebudete uzamčen. 

Ke službě se dostanete i přes portál nebo prostřednictvím kódu programu. Funkce správy jsou funkční prostřednictvím ID předplatného, které není klíčem rozhraní API služby, a tak tak pořád dostupné i v případě, že klíče rozhraní API nejsou. 

Po vytvoření nových klíčů prostřednictvím portálu nebo vrstvy správy se přístup obnoví na váš obsah (indexy, indexery, zdroje dat, mapy synonym), jakmile budete mít nové klíče a zadáte tyto klíče na požadavky.

## <a name="secure-api-keys"></a>Zabezpečené klíče rozhraní API

Prostřednictvím [oprávnění na základě rolí](search-security-rbac.md)můžete klíče odstranit nebo číst, ale nemůžete nahradit klíč uživatelsky definovaným heslem nebo použít službu Active Directory jako primární metodologii ověřování pro přístup k operacím vyhledávání. 

Zabezpečení klíčů je zajištěno omezením přístupu prostřednictvím portálu nebo rozhraní Správce prostředků (PowerShell nebo rozhraní příkazového řádku). Jak je uvedeno, správci předplatného můžou zobrazit a znovu vygenerovat všechny klíče rozhraní API. Je nutné zkontrolovat přiřazení rolí a pochopit, kdo má přístup k klíčům správce.

+ Na řídicím panelu služby klikněte na možnost **řízení přístupu (IAM)** a pak na kartu **přiřazení rolí** pro zobrazení přiřazení rolí pro vaši službu.

Členové následujících rolí mohou zobrazovat a obnovovat klíče: vlastník, přispěvatel, [Search Service přispěvatelé](../role-based-access-control/built-in-roles.md#search-service-contributor) .

## <a name="see-also"></a>Viz také

+ [Zabezpečení v Azure Kognitivní hledání](search-security-overview.md)
+ [Řízení přístupu na základě role v Azure Kognitivní hledání](search-security-rbac.md)
+ [Správa pomocí prostředí PowerShell](search-manage-powershell.md) 