---
title: Jak používat Azure AD Graph API
description: Rozhraní Graph API služby Azure Active Directory (Azure AD) poskytuje programový přístup ke službě Azure AD prostřednictvím koncových bodů rozhraní REST API pro OData. Aplikace můžete použít Azure AD Graph API k provedení vytvoření, čtení, aktualizace a odstranění (CRUD) operací objekty a daty adresáře.
services: active-directory
documentationcenter: n/a
author: CelesteDG
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: sureshja
ms.custom: aaddev
ms.openlocfilehash: 7b7f2133634a91b828d7e978c9041b12873fa3a1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997212"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Postupy: použití Azure AD Graph API

Rozhraní Graph API služby Azure Active Directory (Azure AD) poskytuje programový přístup ke službě Azure AD prostřednictvím koncových bodů rozhraní REST API pro OData. Aplikace můžete použít Azure AD Graph API k provedení vytvoření, čtení, aktualizace a odstranění (CRUD) operací objekty a daty adresáře. Například můžete použít Azure AD Graph API k vytvoření nového uživatele, zobrazit nebo aktualizovat vlastnosti uživatele, změňte heslo uživatele, zkontrolovat členství ve skupinách pro přístup na základě rolí, zakázat nebo odstranit uživatele. Další informace o funkcích Azure AD Graph API a scénáře aplikací najdete v tématu [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) a [požadavky na Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx).

> [!IMPORTANT]
> Důrazně doporučujeme pro přístup k prostředkům Azure Active Directory použít [Microsoft Graph](https://developer.microsoft.com/graph) místo Azure AD Graph API. Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které může být Azure AD Graph API stále vhodné. Další informace najdete v příspěvku [Microsoft Graph nebo Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogu na webu Office Dev Center.

## <a name="how-to-construct-a-graph-api-url"></a>Jak vytvořit adresu URL rozhraní API Graphu

V rozhraní Graph API pro přístup k datům adresáře a objekty (jinými slovy, prostředky nebo entity), u kterých chcete provádět operace CRUD, můžete použít adresy URL na základě protokolu Open Data (OData). Adresy URL použité v rozhraní Graph API se skládá ze čtyř hlavních částí: root, identifikátor tenanta, cesta k prostředku a možnosti řetězce dotazu služby: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Provést například na následující adrese URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Kořenový adresář**: V Azure AD Graph API, je vždy kořenový adresář https://graph.windows.net.
* **Identifikátor tenanta**: Tato část může být název ověřené domény (registrovaných), v předchozím příkladu contoso.com. Může být ID objektu tenanta nebo "TatoOrganizace" nebo "me" alias. Další informace najdete v tématu [adresování entit a operace v Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Cesta k prostředku**: Tato část adresy URL identifikuje prostředek, který má být interagovat (uživatelů, skupin, konkrétního uživatele, nebo konkrétní skupiny atd.) V předchozím příkladu je nejvyšší úrovně "groups" adresu, kterou zdroj nastaven. Můžete také vyřešit konkrétní entitu, třeba "uživatelé / {ID objektu}" nebo "uživatelé/userPrincipalName".
* **Parametrů dotazu**: otazník (?) odděluje část cesty prostředku z oddílu parametry dotazu. Parametr dotazu "api-version" se vyžaduje u všech požadavků v Azure AD Graph API. Azure AD Graph API podporuje také následující možnosti dotazu OData: **$filter**, **$orderby**, **$expand**, **$top**a **$format**. Následující možnosti dotazu se momentálně nepodporují: **$count**, **$inlinecount**, a **$skip**. Další informace najdete v tématu [podporované dotazy, filtry a možnosti stránkování v Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Verze rozhraní Graph API

Zadejte verzi pro žádost o rozhraní Graph API v parametru dotazu "api-version". Pro verze 1.5 a novější použijte hodnotu číselná verze; verze API-version = 1.6. U starších verzí pomocí řetězec data, která dodržuje formát rrrr-MM-DD; například verze api-version = 2013-11-08. Funkce ve verzi preview použijte řetězec "beta"; například verze api-version = beta. Další informace o rozdílech mezi verzemi rozhraní Graph API najdete v tématu [Správa verzí Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Rozhraní Graph API metadat

Pokud chcete vrátit soubor metadat Azure AD Graph API, přidejte segment "$metadata" po identifikátor tenanta v příkladu adresy URL, na následující adrese URL vrátí metadata pro ukázkové společnosti: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Zadejte tuto adresu URL do adresního řádku webového prohlížeče a zobrazit metadata. Dokument metadat CSDL vrátil popisuje entity a komplexní typy, jejich vlastnosti a funkce a vystavené verzi rozhraní Graph API požadovaná akce. Vynechání parametru verze rozhraní api vrátí metadata pro nejnovější verzi.

## <a name="common-queries"></a>Běžné dotazy

[Azure AD Graph API běžné dotazy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) obsahuje seznam běžných dotazů, které lze použít s Azure AD Graph, včetně dotazů, které lze použít pro přístup k prostředkům nejvyšší úrovně ve vašem adresáři a dotazy k provádění operací ve vašem adresáři.

Například `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` vrátí firemních informací pro directory contoso.com.

Nebo `https://graph.windows.net/contoso.com/users?api-version=1.6` obsahuje seznam všech objektů uživatelů v adresáři contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Pomocí Azure AD Graph Exploreru
Azure AD Graph Explorer pro Azure AD Graph API můžete použít k dotazování na data adresáře při sestavování aplikace.

Na následujícím snímku obrazovky je uvidíte, pokud byste chtěli přejděte na Azure AD Graph Explorer, přihlaste se a zadejte výstup `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` zobrazíte všechny uživatele v adresáři uživatele přihlášeného:

![Průzkumník rozhraní api graph služby Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Načtení Azure AD Graph Explorer**: načtení nástroj, přejděte na [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Klikněte na tlačítko **přihlášení** a přihlaste se pomocí svých přihlašovacích údajů účtu služby Azure AD pro spuštění vašeho tenanta Azure AD Graph Explorer. Pokud spouštíte skript v vašeho vlastního tenanta Azure AD Graph Explorer, vy nebo váš správce musí vyjádřit souhlas při přihlašování. Pokud máte předplatné Office 365, budete mít automaticky tenanta služby Azure AD. Přihlašovací údaje, které používáte k přihlášení do služeb Office 365 jsou ve skutečnosti, účty služby Azure AD, a tyto přihlašovací údaje můžete použít s Azure AD Graph Exploreru.

**Spuštění dotazu**: ke spuštění dotazu, zadejte svůj dotaz v textovém poli požadavku a klikněte na tlačítko **získat** nebo klikněte na tlačítko **zadejte** klíč. Výsledky se zobrazí v poli odpovědi. Například `https://graph.windows.net/myorganization/groups?api-version=1.6` zobrazuje seznam všech objektů skupiny v adresáři přihlášeného uživatele.

Mějte na paměti následující funkce a omezení Azure AD Graph Exploreru:

* Nastaví možnosti automatického dokončování v prostředku. Pokud chcete zobrazit tuto funkci, klepněte na textové pole požadavku (Pokud adresa URL společnosti se zobrazí). Můžete vybrat z rozevíracího seznamu Sada prostředků.
* Historie žádostí.
* Podporuje "me" a "TatoOrganizace" adresování aliasy. Například můžete použít `https://graph.windows.net/me?api-version=1.6` vrátit objekt uživatele přihlášeného uživatele nebo `https://graph.windows.net/myorganization/users?api-version=1.6` vrátí všechny uživatele v adresáři přihlášeného uživatele.
* Podporuje úplné operace CRUD s vlastní adresáře pomocí `POST`, `GET`, `PATCH` a `DELETE`.
* Oddíl hlavičky odpovědi. Tato část umožňuje snadněji řešit problémy, ke kterým dochází při spuštění dotazů.
* Prohlížeč JSON pro odpovědi s možnostmi rozbalení a sbalení.
* Bez podpory pro zobrazování nebo nahráním miniaturu fotografie.

## <a name="using-fiddler-to-write-to-the-directory"></a>Použití aplikace Fiddler k zápisu do adresáře

Pro účely této úvodní příručky můžete použít ladicí program webové aplikace Fiddler na postup provedení zápisu operace adresáře služby Azure AD. Například můžete mít a nahrát profilové fotky uživatele (což není možné pomocí Průzkumníka služby Azure AD Graph). Další informace a nainstalovat Fiddler, najdete v části [ http://www.telerik.com/fiddler ](http://www.telerik.com/fiddler).

V následujícím příkladu použijte ladicí program webové aplikace Fiddler k vytvoření nové skupiny zabezpečení 'MyTestGroup' v adresáři služby Azure AD.

**Získání přístupového tokenu**: přístup k Azure AD Graph, klienti jsou nutné pro úspěšné ověření do služby Azure AD nejprve. Další informace najdete v tématu [scénáře ověřování pro službu Azure AD](authentication-scenarios.md).

**Vytvořte a spusťte dotaz**: proveďte následující kroky:

1. Otevřete Fiddler ladicí program webového a přepněte **Composer** kartu.
2. Protože ale chcete vytvořit novou skupinu zabezpečení, vyberte **příspěvek** jako metoda protokolu HTTP z rozevírací nabídky. Další informace o operacích a oprávnění na objekt skupiny najdete v tématu [skupiny](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) v rámci [Azure AD Graph API Reference k rozhraní REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. V poli vedle **příspěvek**, zadejte následující adresu URL požadavku: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > {Mytenantdomain} je nutné nahradit názvem domény z adresáře Azure AD.

4. Do pole přímo pod rozevírací příspěvek zadejte následující hlavičku protokolu HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Náhradní vaše &lt;váš přístupový token&gt; s přístupovým tokenem pro váš adresář Azure AD.

5. V **text žádosti** pole, zadejte následující kód JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Další informace o vytváření skupin najdete v tématu [vytvořit skupinu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Další informace o Azure AD entity a typy, které jsou vystavené grafu a informace o operacích, které můžete provádět na nich s grafem najdete v tématu [Azure AD Graph API Reference k rozhraní REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Další postup

* Další informace o [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Další informace o [obory oprávnění rozhraní API Graph pro Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)