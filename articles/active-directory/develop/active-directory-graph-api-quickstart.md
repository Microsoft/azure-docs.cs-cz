---
title: Jak používat Graph API Azure AD
description: Služba Azure Active Directory (Azure AD) Graph API poskytuje programový přístup ke službě Azure AD prostřednictvím koncových bodů OData REST API. Aplikace můžou pomocí Graph API Azure AD provádět operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro data a objekty adresáře.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85c3a1953ce34ab6bf60111715d9d8972a4682ba
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853387"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Postup: Použití Graph API v Azure AD

Služba Azure Active Directory (Azure AD) Graph API poskytuje programový přístup ke službě Azure AD prostřednictvím koncových bodů OData REST API. Aplikace můžou pomocí Graph API Azure AD provádět operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro data a objekty adresáře. Můžete například použít Azure AD Graph API k vytvoření nového uživatele, zobrazení nebo aktualizaci vlastností uživatele, změně hesla uživatele, kontrole členství ve skupině pro přístup na základě rolí, zakázání nebo odstranění uživatele. Další informace o funkcích Graph API a scénářích aplikací Azure AD najdete v článku [Azure ad Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) a [požadavky služby Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx).

Tento článek se týká Graph API služby Azure AD. Podobné informace související s Microsoft Graph API najdete v tématu [použití rozhraní api Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

> [!IMPORTANT]
> Důrazně doporučujeme pro přístup k prostředkům Azure Active Directory použít [Microsoft Graph](https://developer.microsoft.com/graph) místo Azure AD Graph API. Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které může být Azure AD Graph API stále vhodné. Další informace najdete v příspěvku [Microsoft Graph nebo Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogu na webu Office Dev Center.

## <a name="how-to-construct-a-graph-api-url"></a>Jak vytvořit adresu URL Graph API

V Graph API můžete pro přístup k datům adresáře a objektům (jinými slovy, prostředkům nebo entitám), u kterých chcete provádět operace CRUD, používat adresy URL založené na protokolu OData (Open data). Adresy URL používané v Graph API se skládají ze čtyř hlavních částí: kořen služby, identifikátor tenanta, cesta prostředku a možnosti řetězce dotazu: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Vezměte v úvahu příklad následující adresy URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Kořen služby**: V Azure AD Graph API je kořenový adresář služby vždycky https://graph.windows.net.
* **Identifikátor tenanta**: V této části se může jednat o ověřený (registrovaný) název domény v předchozím příkladu contoso.com. Může to být také ID objektu tenanta nebo alias "myorganization" nebo "já". Další informace najdete v tématu věnovaném [adresování entit a operací v Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Cesta prostředku**: Tato část adresy URL identifikuje prostředek, se kterým se bude pracovat (uživatelé, skupiny, konkrétní uživatel nebo konkrétní skupina atd.). V předchozím příkladu je to nejvyšší úroveň "skupiny", která tuto sadu prostředků řeší. Můžete také adresovat konkrétní entitu, například "uživatelé/{objectId}" nebo "uživatelé/userPrincipalName".
* **Parametry dotazu**: Otazník (?) odděluje oddíl cesta prostředku od části parametrů dotazu. Parametr dotazu API-Version je vyžadován u všech požadavků ve službě Azure AD Graph API. Azure AD Graph API také podporuje následující možnosti dotazů OData: **$Filter**, **$OrderBy**, **$expand**, **$Top**a **$Format**. Následující možnosti dotazu nejsou aktuálně podporovány: **$Count**, **$inlinecount**a **$Skip**. Další informace najdete v tématu [podporované dotazy, filtry a možnosti stránkování ve službě Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Verze Graph API

V parametru dotazu API-Version zadejte verzi pro požadavek Graph API. Pro verzi 1,5 a novější použijte číselnou hodnotu verze; API-Version = 1.6. V dřívějších verzích použijete řetězec data, který odpovídá formátu RRRR-MM-DD; například API-Version = 2013-11-08. V případě funkcí verze Preview použijte řetězec "beta"; například API-Version = beta. Další informace o rozdílech mezi verzemi Graph API najdete v tématu [Správa verzí Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API metadata

Pokud chcete vrátit soubor metadat Graph API služby Azure AD, přidejte segment "$metadata" za identifikátor tenanta v adrese URL, například následující adresa URL vrátí metadata pro ukázkovou společnost: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Tuto adresu URL můžete zadat v adresním řádku webového prohlížeče, aby se metadata zobrazila. Dokument metadat CSDL vrátil popis entit a komplexních typů, jejich vlastností a funkcí a akcí vystavených ve verzi Graph API, kterou jste požadovali. Vynechání parametru verze API vrátí metadata pro nejnovější verzi.

## <a name="common-queries"></a>Běžné dotazy

[Azure ad Graph API Common dotazuje](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) seznam běžných dotazů, které je možné používat s grafem Azure AD, včetně dotazů, které se dají použít k přístupu k prostředkům nejvyšší úrovně ve vašem adresáři a dotazům k provádění operací ve vašem adresáři.

Například `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` vrátí informace společnosti pro adresář contoso.com.

Nebo `https://graph.windows.net/contoso.com/users?api-version=1.6` vypíše všechny uživatelské objekty v adresáři contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Používání Průzkumníka Azure AD graphu
K dotazování na data adresáře při sestavování aplikace můžete použít Průzkumníka Azure AD graphu pro Graph API Azure AD.

Následující snímek obrazovky je výstup, který vidíte, pokud jste chtěli přejít do Průzkumníka graphu služby Azure AD, přihlásit se a zadat `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` pro zobrazení všech uživatelů v adresáři přihlášeného uživatele:

![Příklad výstupu v Azure AD Graph API Exploreru](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Načíst Průzkumníka graphu služby Azure AD**: Pokud chcete nástroj načíst, přejděte na [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Klikněte na **Přihlásit** se a přihlaste se pomocí přihlašovacích údajů k účtu Azure AD a spusťte Průzkumníka Azure AD graphu s vaším klientem. Pokud spustíte Průzkumníka služby Azure AD Graph pro vašeho vlastního tenanta, budete vy nebo váš správce muset během přihlašování souhlasit. Pokud máte předplatné Office 365, budete mít automaticky tenanta Azure AD. Přihlašovací údaje, které používáte pro přihlášení k Office 365, jsou ve skutečnosti účty Azure AD a tyto přihlašovací údaje můžete použít s Průzkumníkem Azure AD Graph.

**Spusťte dotaz**: Dotaz spustíte tak, že do textového pole žádost zadáte svůj dotaz a kliknete na **získat** nebo kliknete na klávesu **ENTER** . Výsledky se zobrazí v poli odpověď. Například `https://graph.windows.net/myorganization/groups?api-version=1.6` vypíše všechny objekty skupiny v adresáři přihlášeného uživatele.

Všimněte si následujících funkcí a omezení Průzkumníka Azure AD graphu:

* Funkce automatického dokončování v sadách prostředků Tuto funkci zobrazíte tak, že kliknete na textové pole žádosti (kde se zobrazí adresa URL společnosti). Můžete vybrat sadu prostředků z rozevíracího seznamu.
* Historie žádostí
* Podporuje aliasy pro adresování "já" a "myorganization". Můžete například použít `https://graph.windows.net/me?api-version=1.6` k vrácení uživatelského objektu přihlášeného uživatele nebo `https://graph.windows.net/myorganization/users?api-version=1.6` vrácení všech uživatelů v adresáři přihlášeného uživatele.
* Podporuje úplné operace CRUD proti vašemu vlastnímu adresáři `POST`pomocí `GET` `PATCH` , a `DELETE`.
* Část s hlavičkami odpovědi. Tato část se dá použít k řešení problémů, ke kterým dochází při spouštění dotazů.
* Prohlížeč JSON pro odpověď s funkcemi rozbalení a sbalení.
* Žádná podpora pro zobrazení nebo nahrání fotografie miniatur.

## <a name="using-fiddler-to-write-to-the-directory"></a>Zápis do adresáře pomocí Fiddler

Pro účely tohoto průvodce rychlým startem můžete použít webový ladicí program Fiddler k provádění operací zápisu v adresáři Azure AD. Můžete například získat a nahrát fotografii profilu uživatele (což není možné v Průzkumníku Azure AD Graph). Další informace a pokyny k instalaci Fiddler najdete v [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)tématu.

V následujícím příkladu použijete webový ladicí program Fiddler k vytvoření nové skupiny zabezpečení ' MyTestGroup ' v adresáři služby Azure AD.

**Získání přístupového tokenu**: Aby bylo možné získat přístup k Azure AD graphu, musí se nejdřív úspěšně ověřit ve službě Azure AD. Další informace najdete v tématu [scénáře ověřování pro Azure AD](authentication-scenarios.md).

**Sestavit a spustit dotaz**: Proveďte následující kroky:

1. Otevřete webový ladicí program Fiddler a přepněte na kartu skladatele.
2. Vzhledem k tomu, že chcete vytvořit novou skupinu zabezpečení, v rozevírací nabídce vyberte **post** jako metoda HTTP. Další informace o operacích a oprávněních pro objekt skupiny najdete v tématu [Skupina](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) v rámci [odkazu na REST API v grafu Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Do pole vedle **příspěvku**zadejte následující adresu URL žádosti: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > {Mytenantdomain} je nutné nahradit názvem domény vlastního adresáře služby Azure AD.

4. Do pole přímo pod možností po vybírání příspěvku zadejte následující hlavičku protokolu HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Pomocí přístupového tokenu pro váš adresář služby Azure AD Nahraďte svůj přístupový token&gt;. &lt;

5. Do pole **Text žádosti** zadejte následující kód JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Další informace o vytváření skupin najdete v tématu věnovaném [Vytvoření skupiny](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Další informace o entitách a typech služby Azure AD, které jsou zpřístupněny v grafu, a informace o operacích, které lze s grafem provádět, naleznete v tématu [Azure AD Graph REST API Reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Další postup

* Další informace o [Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Další informace o [oborech oprávnění Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
