---
title: Jak používat rozhraní API azure ad grafu
description: Rozhraní API grafu Azure Active Directory (Azure AD) poskytuje programový přístup k Azure AD prostřednictvím koncových bodů rozhraní API OData REST. Aplikace můžou pomocí rozhraní Azure AD Graph API provádět operace vytváření, čtení, aktualizace a odstraňování (CRUD) na datech adresáře a objektech.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9fd5fa943468924c289587285fe7986a73c21dba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161337"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Postup: Použití rozhraní API azure ad grafu

> [!IMPORTANT]
> Důrazně doporučujeme používat [Microsoft Graph](https://developer.microsoft.com/graph) místo rozhraní Azure AD Graph API pro přístup k prostředkům Azure Active Directory (Azure AD). Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které rozhraní API Azure AD Graph může být stále vhodné; Další informace najdete v [tématu Microsoft Graph nebo Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogu a [migrace aplikací Azure AD Graph do Microsoft Graphu](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Rozhraní API azure ad grafu poskytuje programový přístup k Azure AD prostřednictvím koncových bodů rozhraní API OData REST. Aplikace můžou pomocí rozhraní Azure AD Graph API provádět operace vytváření, čtení, aktualizace a odstraňování (CRUD) na datech adresáře a objektech. Pomocí rozhraní API azure ad graphu můžete například vytvořit nového uživatele, zobrazit nebo aktualizovat vlastnosti uživatele, změnit heslo uživatele, zkontrolovat členství ve skupině pro přístup založený na rolích, zakázat nebo odstranit uživatele. Další informace o funkcích rozhraní API rozhraní Azure AD Graph a scénářích aplikací najdete v [tématu Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) a [předpoklady rozhraní API Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx). Rozhraní API Azure AD Graph funguje jenom s pracovními nebo školními účty.Azure AD Graph API only works with work or school/organization accounts.

Tento článek se vztahuje na rozhraní API Azure AD Graph. Podobné informace související s rozhraním Microsoft Graph API najdete [v tématu Použití rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Jak vytvořit adresu URL rozhraní API grafu

V rozhraní Graph API můžete pro přístup k datům adresáře a objektům (jinými slovy, prostředkům nebo entitám), proti kterým chcete provádět operace CRUD, používat adresy URL založené na protokolu Open Data (OData). Adresy URL používané v rozhraní Graph API se skládají ze čtyř hlavních částí: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`kořenové služby, identifikátor u klienta, cesta k prostředku a možnosti řetězce dotazu: . Vezměme si příklad `https://graph.windows.net/contoso.com/groups?api-version=1.6`následující adresy URL: .

* **Kořenslužby**: V rozhraní API azure ad graphu je kořenová služba vždy https://graph.windows.net.
* **Identifikátor klienta**: Tato část může být ověřený (registrovaný) název domény, v předchozím příkladu contoso.com. Může se také jedná o ID objektu klienta nebo alias "myorganization" nebo "me". Další informace naleznete [v tématu Adresování entit a operací v rozhraní API azure ad graph .](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)
* **Cesta k prostředku**: Tato část adresy URL identifikuje prostředek, se kterým má být interagonováno (uživatelé, skupiny, konkrétní uživatel nebo určitá skupina atd.) Ve výše uvedeném příkladu je nejvyšší úroveň "skupiny" k řešení této sady prostředků. Můžete také řešit určitou entitu, například "users/{objectId}" nebo "users/userPrincipalName".
* **Parametry dotazu**: Otazník (?) odděluje část cesty k prostředku od části parametrů dotazu. Parametr dotazu "verze rozhraní api" je povinný pro všechny požadavky v rozhraní API Azure AD Graph. Rozhraní Azure AD Graph API také podporuje následující možnosti dotazu OData: **$filter**, **$orderby**, **$expand**, **$top**a **$format**. V současné době nejsou podporovány následující možnosti dotazu: **$count**, **$inlinecount**a **$skip**. Další informace naleznete v [tématu Podporované dotazy, filtry a možnosti stránkování v rozhraní API grafu Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Verze rozhraní API pro grafy

V parametru dotazu "api-version" zadáte verzi požadavku rozhraní API pro graf. Pro verzi 1.5 a novější použijete číselnou hodnotu verze; api-version=1.6. Pro starší verze použijete řetězec kalendářních dat, který se drží formátu YYYY-MM-DD; například api-version=2013-11-08. Pro funkce náhledu použijte řetězec "beta"; například api-version=beta. Další informace o rozdílech mezi verzemi rozhraní API rozhraní Graph najdete v [tématu Správa verzí rozhraní API rozhraní Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadata rozhraní API grafu

Chcete-li vrátit soubor metadat rozhraní API rozhraní Azure AD Graph, přidejte segment "$metadata" za identifikátor klienta `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`v adrese URL Například následující adresa URL vrátí metadata pro ukázkovou společnost: . Tato adresa URL můžete zadat do adresního řádku webového prohlížeče a zobrazit metadata. Vrácený dokument metadat CSDL popisuje entity a složité typy, jejich vlastnosti a funkce a akce vystavené požadovanou verzí rozhraní GRAPH API. Vynechání parametru verze rozhraní api vrátí metadata pro nejnovější verzi.

## <a name="common-queries"></a>Běžné dotazy

[Azure AD Graph API běžné dotazy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) uvádí běžné dotazy, které lze použít s Azure AD Graph, včetně dotazů, které lze použít pro přístup k prostředkům nejvyšší úrovně ve vašem adresáři a dotazy k provádění operací ve vašem adresáři.

Například `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` vrátí informace o společnosti pro contoso.com adresáře.

Nebo `https://graph.windows.net/contoso.com/users?api-version=1.6` zobrazí seznam všech objektů uživatelů v adresáři contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Použití Průzkumníka grafů Azure AD
Azure AD Graph Explorer pro rozhraní API Azure AD Graph api k dotazování na data adresáře při vytváření aplikace.

Následující snímek obrazovky je výstup, který byste viděli, pokud byste měli přejít `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` do Průzkumníka azure ad graphu, přihlásit se a zadat pro zobrazení všech uživatelů v adresáři přihlášeného uživatele:

![Příklad výstupu v Průzkumníku rozhraní API grafu Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Načtení Průzkumníka grafů Azure AD**: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)Chcete-li nástroj načíst, přejděte na . Klikněte na **Přihlásit** se a přihlásit se pomocí přihlašovacích údajů účtu Azure AD a spusťte Průzkumníka Azure AD Graph proti svému tenantovi. Pokud spustíte Azure AD Graph Explorer proti vlastnímu tenantovi, vy nebo váš správce musíte souhlasit během přihlášení. Pokud máte předplatné Office 365, máte automaticky klienta Azure AD. Přihlašovací údaje, které používáte k přihlášení k Office 365, jsou ve skutečnosti účty Azure AD a tato pověření můžete použít pomocí Průzkumníka Azure AD Graph.

**Spuštění dotazu**: Chcete-li spustit dotaz, zadejte dotaz do textového pole požadavku a klepněte na tlačítko **GET** nebo klikněte na **klávesu Enter.** Výsledky se zobrazí v poli odezvy. Zobrazí například `https://graph.windows.net/myorganization/groups?api-version=1.6` seznam všech objektů skupiny v adresáři přihlášeného uživatele.

Všimněte si následujících funkcí a omezení Průzkumníka azure ad graphu:

* Funkce automatického dokončování u sad prostředků. Chcete-li tuto funkci zobrazit, klikněte na textové pole požadavku (kde se zobrazí adresa URL společnosti). Sadu prostředků můžete vybrat z rozevíracího seznamu.
* Žádost o historii.
* Podporuje "já" a "myorganization" adresování aliasy. Můžete například vrátit `https://graph.windows.net/me?api-version=1.6` objekt uživatele přihlášeného uživatele nebo `https://graph.windows.net/myorganization/users?api-version=1.6` vrátit všechny uživatele v adresáři přihlášeného uživatele.
* Podporuje úplné operace CRUD proti `POST` `GET`vlastnímu adresáři pomocí aplikace , `PATCH` a `DELETE`.
* Oddíl záhlaví odpovědí. Tuto část lze použít k řešení problémů, ke kterým dochází při spouštění dotazů.
* Prohlížeč JSON pro odpověď s možností rozbalení a sbalení.
* Žádná podpora pro zobrazení nebo nahrávání miniatury.

## <a name="using-fiddler-to-write-to-the-directory"></a>Použití Fiddler psát do adresáře

Pro účely tohoto průvodce úvodním startem můžete použít ladicí program Fiddler Web k procvičování provádění operací zápisu proti adresáři Azure AD. Například můžete získat a nahrát profilovou fotku uživatele (což není možné s Průzkumníkem Azure AD Graph). Další informace a instalaci Fiddler, naleznete v tématu [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

V níže uvedeném příkladu použijete ukázku Fiddler web k vytvoření nové skupiny zabezpečení MyTestGroup v adresáři Azure AD.

**Získat přístupový token**: Pro přístup k Azure AD Graph, klienti jsou povinni úspěšně ověřit do služby Azure AD jako první. Další informace najdete v [tématu scénáře ověřování pro Azure AD](authentication-scenarios.md).

**Vytvoření a spuštění dotazu**: Proveďte následující kroky:

1. Otevřete webový ladicí program Fiddler a přepněte na kartu **Composer.**
2. Vzhledem k tomu, že chcete vytvořit novou skupinu zabezpečení, vyberte z rozbalovací nabídky možnost **Účtovat** jako metodu HTTP. Další informace o operacích a oprávněních k objektu skupiny najdete v tématu [Skupina](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) v rámci [odkazu rozhraní API rozhraní REST grafu Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Do pole vedle pole **Zaúčtovat**zadejte `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`následující adresu URL požadavku: .
   
   > [!NOTE]
   > Musíte nahradit {mytenantdomain} názvem domény vlastního adresáře Azure AD.

4. Do pole přímo pod přitahujte příkaz Y inas pod příkazem Zavřít hlavičku HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > &lt;Nahraďte svůj&gt; přístupový token přístupovým tokenem pro adresář Azure AD.

5. Do pole **Tělo požadavku** zadejte následující json:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Další informace o vytváření skupin naleznete v [tématu Vytvoření skupiny](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Další informace o entitách a typech Azure AD, které jsou vystaveny graphu a informace o operacích, které lze provádět na nich pomocí grafu, najdete v [tématu Azure AD Graph REST API odkaz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Další kroky

* Další informace o [rozhraní API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Další informace o [oborech oprávnění rozhraní AZURE AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
