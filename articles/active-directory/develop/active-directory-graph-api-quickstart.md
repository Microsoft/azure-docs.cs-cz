---
title: Rychlý úvodní kurz pro Azure AD Graph API | Microsoft Docs
description: Azure Active Directory Graph API zajišťují programový přístup ke službě Azure AD prostřednictvím koncové body OData REST API. Aplikace můžete použít Azure AD Graph API k provedení vytvářet, číst, aktualizovat a odstraňovat operace na data adresáře a objekty.
services: active-directory
documentationcenter: n/a
author: mtillman
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: d4e06c7332c12ed26afbf71d1b307c6a6fb2a55b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156870"
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Rychlý úvodní kurz pro Azure AD Graph API
Rozhraní Graph API Azure Active Directory (AD) zajišťují programový přístup ke službě Azure AD prostřednictvím koncové body OData REST API. Aplikace můžete použít Azure AD Graph API k provedení vytvářet, číst, aktualizovat a odstraňovat operace na data adresáře a objekty. Například můžete použít Azure AD Graph API k vytvoření nového uživatele, zobrazit nebo aktualizovat vlastnosti uživatele, změnit heslo uživatele, zkontrolovat členství ve skupinách pro přístup na základě rolí, zakázat nebo odstranit uživatele. Další informace o aplikaci scénáře a funkce Azure AD Graph API najdete v tématu [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) a [Azure AD Graph API požadavky](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Důrazně doporučujeme pro přístup k prostředkům Azure Active Directory použít [Microsoft Graph](https://developer.microsoft.com/graph) místo Azure AD Graph API. Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které může být Azure AD Graph API stále vhodné. Další informace najdete v příspěvku [Microsoft Graph nebo Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogu na webu Office Dev Center.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Jak vytvořit adresu URL rozhraní API grafu
V rozhraní Graph API přístup k datům adresáře a objekty (jinými slovy, prostředky nebo entity), u kterých chcete provádět operace CRUD, můžete použít adresy URL založené na protokolu (OData Open Data). Adresy URL používá v rozhraní Graph API obsahovat čtyři hlavní části: služby root, identifikátor klienta, cesta prostředku a možnosti řetězec dotazu: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Provést jako příklad následující adresu URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Služba kořenové**: V Azure AD Graph API, kořenový adresář je vždy https://graph.windows.net.
* **Identifikátor klienta**: v této části může být název ověřené domény (registrovaný), v předchozím příkladu contoso.com. Lze ji ID objektu klienta nebo "TatoOrganizace" nebo "ME." alias. Další informace najdete v tématu [adresování entity a operace v Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Cesta prostředku**: Tato část adresy URL identifikuje prostředek na serveru (uživatelé, skupiny, určitého uživatele, nebo konkrétní skupiny atd.) V předchozím příkladu je nejvyšší úrovně "skupiny" adresu, která prostředek nastaven. Můžete také vyřešit konkrétní entitu, například "uživatelé / {objectId}" nebo "uživatelé nebo userPrincipalName".
* **Parametrů dotazu**: otazník (?) odděluje část cesty prostředku z části parametry dotazu. Parametr dotazu "api-version" je požadován u všech požadavků v Azure AD Graph API. Azure AD Graph API také podporuje následující možnosti dotazu OData: **$filter**, **$orderby**, **$expand**, **$top**a **$format**. Následující možnosti dotazu nejsou aktuálně podporovány: **$count**, **$inlinecount**, a **$skip**. Další informace najdete v tématu [podporované dotazy, filtrů a možností stránkování v Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Verze rozhraní Graph API
Verze pro žádost o rozhraní Graph API určíte v parametru dotazu "api-version". Pro verze 1.5 a novější použijte hodnotu numerické verze; rozhraní API-version = 1.6. U starších verzí použijte datum řetězec, který dodržuje formát rrrr-MM-DD; například rozhraní api-version = 2013. 11 08. Pro funkce verze preview použijte řetězec "beta"; například rozhraní api-version = beta. Další informace o rozdílech mezi verzemi rozhraní Graph API najdete v tématu [Správa verzí Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graf metadat rozhraní API
Vrátí soubor metadat rozhraní Azure AD Graph API, přidejte segment "$metadata" po identifikátor klienta v adrese URL pro příkladu, následující adresu URL vrátí metadata pro ukázkové společnosti: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Můžete zadat tuto adresu URL do adresního řádku webového prohlížeče zobrazíte metadata. Dokument metadat CSDL vrátil popisuje entity a komplexní typy, jejich vlastnosti a funkce a vystavené verzi rozhraní Graph API požadovaná akce. Vynechání parametru verze rozhraní api vrátí metadata pro nejnovější verzi.

## <a name="common-queries"></a>Běžné dotazy
[Azure AD Graph API běžné dotazy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) jsou uvedeny běžné dotazy, které lze použít s Azure AD Graph, včetně dotazy, které lze použít pro přístup k prostředkům nejvyšší úrovně ve vašem adresáři a k provádění operací ve vašem adresáři.

Například `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` vrátí společnosti informace pro adresář contoso.com.

Nebo `https://graph.windows.net/contoso.com/users?api-version=1.6` jsou uvedené všechny uživatelské objekty v adresáři contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Pomocí Průzkumníka Azure AD Graph
Průzkumník Azure AD Graph pro Azure AD Graph API můžete zadávat dotazy na data adresáře, jak sestavit aplikaci.

Na následujícím snímku obrazovky je výstup, zobrazí se, pokud byste chtěli přejděte do Průzkumníka Azure AD Graph, přihlaste se a zadejte `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` zobrazíte všechny uživatele v adresáři přihlášeného uživatele:

![Azure AD graph api explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Načíst Průzkumník Azure AD Graph**: Chcete-li načíst nástroj, přejděte na [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Klikněte na tlačítko **přihlášení** a přihlaste se pomocí svých přihlašovacích údajů účtu Azure AD ke spouštění vašeho klienta Azure AD Graph Explorer. Pokud spustíte Průzkumníka Azure AD Graph proti vlastního klienta, vy nebo váš správce musí vyjádřit souhlas. během přihlášení. Pokud máte předplatné služeb Office 365, máte automaticky klient služby Azure AD. Přihlašovací údaje, které používáte k přihlášení do služeb Office 365 jsou ve skutečnosti účty Azure AD, a pomocí Azure AD Graph Průzkumníka můžete použít tyto přihlašovací údaje.

**Spuštění dotazu**: ke spuštění dotazu, zadejte dotaz do textového pole žádost a klikněte na tlačítko **získat** nebo klikněte na tlačítko **zadejte** klíč. Výsledky jsou zobrazeny v poli odpovědi. Například `https://graph.windows.net/myorganization/groups?api-version=1.6` zobrazí seznam všech objektů skupiny v adresáři přihlášeného uživatele.

Poznámka: následující vlastnosti a omezení v Azure AD Graph Exploreru:

* Nastaví možnost automatického dokončování na prostředek. Pokud chcete zobrazit tuto funkci, klikněte na textového pole požadavku (kde adresa URL společnosti se zobrazí). Můžete vybrat prostředek nastavit z rozevíracího seznamu.
* Historie žádostí.
* Podporuje "mě" a "TatoOrganizace" adresování aliasy. Například můžete použít `https://graph.windows.net/me?api-version=1.6` vrátit objekt uživatele přihlášeného uživatele nebo `https://graph.windows.net/myorganization/users?api-version=1.6` vrátit všechny uživatele v adresáři přihlášeného uživatele.
* Podporuje úplné operace CRUD před použitím vlastního directory `POST`, `GET`, `PATCH` a `DELETE`.
* Oddíl hlavičky odpovědi. V této části můžete použít k řešení potíží, ke kterým dochází při spuštění dotazů.
* Prohlížečem JSON pro odpověď s rozbalení a sbalení možnosti.
* Žádná podpora pro zobrazení nebo odesílání miniaturu fotografie.

## <a name="using-fiddler-to-write-to-the-directory"></a>Pomocí Fiddler k zápisu do adresáře
Pro účely tohoto průvodce rychlý start můžete použít aplikaci Fiddler ladicí program Web na postup provádění zápisu operace u svého adresáře Azure AD. Například můžete získat a uložit fotografii profilu uživatele, (což není možné s Azure AD Graph Explorer). Další informace a nainstalovat aplikaci Fiddler, najdete v části [ http://www.telerik.com/fiddler ](http://www.telerik.com/fiddler).

V následujícím příkladu použijete webovou aplikaci Fiddler ladicí program vytvořit novou skupinu zabezpečení, MyTestGroup' v adresáři služby Azure AD.

**Získat přístupový token**: přístup k Azure AD Graph, jsou klienti vyžadovaný k úspěšně nejprve ověření Azure AD. Další informace najdete v tématu [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md).

**Napište a spuštění dotazu**: pomocí následujících kroků:

1. Otevřete webovou aplikaci Fiddler ladicího programu a přepnout **autora** kartě.
2. Vzhledem k tomu, že chcete vytvořit novou skupinu zabezpečení, vyberte **Post** jako metodu protokolu HTTP v rozevírací nabídce. Další informace o operacích a oprávnění na objekt skupiny najdete v tématu [skupiny](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) v rámci [Azure AD Graph REST API – referenční informace](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. V poli vedle **Post**, zadejte následující adresu URL požadavku: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Nahraďte {mytenantdomain} s názvem domény adresáře Azure AD.
   > 
   > 
4. Do pole přímo pod rozevírací Post zadejte následující hlavičku protokolu HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > SUBSTITUTE vaše &lt;přístupový token&gt; s tímto tokenem přístupu pro váš adresář Azure AD.
   > 
   > 
5. V **text žádosti** pole, zadejte následujícím kódu JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Další informace o vytváření skupin najdete v tématu [vytvořit skupinu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Další informace o Azure AD entity a typy, které jsou vystavené grafu a informace o operacích, které lze provést na nich grafu naleznete v tématu [Azure AD Graph REST API – referenční informace](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Další postup
* Další informace o [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Další informace o [Azure AD Graph API oprávnění oborů](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

