---
title: Filtry zabezpečení pro oříznutí výsledků pomocí služby Active Directory
titleSuffix: Azure Cognitive Search
description: Řízení přístupu k obsahu Azure Cognitive Search pomocí filtrů zabezpečení a identit Azure Active Directory (AAD).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794307"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtry zabezpečení pro oříznutí výsledků Azure Cognitive Search pomocí identit služby Active Directory

Tento článek ukazuje, jak používat identity zabezpečení Azure Active Directory (AAD) spolu s filtry v Azure Cognitive Search pro oříznutí výsledků hledání na základě členství ve skupině uživatelů.

Tento článek se zabývá následujícími úkony:
> [!div class="checklist"]
> - Vytvoření skupin a uživatelů a uživatelů add
> - Přidružení uživatele k vytvořené skupině
> - Ukládat do mezipaměti nové skupiny
> - Indexovat dokumenty s přidruženými skupinami
> - Vydat požadavek na hledání s filtrem identifikátorů skupiny
> 
> [!NOTE]
> Ukázkové fragmenty kódu v tomto článku jsou napsány v c#. Úplný zdrojový kód najdete [na GitHubu](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Požadavky

Váš index v Azure Cognitive Search musí mít [pole zabezpečení](search-security-trimming-for-azure-search.md) pro uložení seznamu identit skupiny, které mají přístup pro čtení do dokumentu. Tento případ použití předpokládá individuální korespondenci mezi zabezpečenou položkou (například přihláškou na vysokou školu) a bezpečnostním polem určujícím, kdo má k této položce přístup (personál pro přijetí).

Pro vytváření uživatelů, skupin a přidružení ve službě AAD musíte mít oprávnění správce služby AAD, která jsou vyžadována v tomto návodu.

Vaše přihláška musí být také zaregistrována u služby AAD, jak je popsáno v následujícím postupu.

### <a name="register-your-application-with-aad"></a>Zaregistrujte svou přihlášku u AAD

Tento krok integruje vaši aplikaci s AAD za účelem přijetí přihlášení uživatelských a skupinových účtů. Pokud nejste správcem AAD ve vaší organizaci, možná budete muset [vytvořit nového klienta,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) abyste provedli následující kroky.

1. Přejděte do >  **konvergované aplikace** >  [**Portál pro registraci aplikací**](https://apps.dev.microsoft.com)**Přidejte aplikaci**.
2. Zadejte název aplikace a klepněte na **tlačítko Vytvořit**. 
3. Na stránce Moje aplikace vyberte nově registrovanou aplikaci.
4. Na stránce registrace aplikace > **platformy** > **Přidat platformu**zvolte **webové rozhraní API**.
5. Stále na stránce registrace aplikace, přejděte na > **Microsoft Graph Oprávnění** > **Přidat**.
6. V části Vybrat oprávnění přidejte následující delegovaná oprávnění a klepněte na **tlačítko OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **Uživatel.ReadWrite.All**

Microsoft Graph poskytuje rozhraní API, které umožňuje programový přístup k AAD prostřednictvím rozhraní REST API. Ukázka kódu pro tento návod používá oprávnění k volání rozhraní Microsoft Graph API pro vytváření skupin, uživatelů a přidružení. Api se také používají k ukládání identifikátorů skupiny do mezipaměti pro rychlejší filtrování.

## <a name="create-users-and-groups"></a>Vytváření uživatelů a skupin

Pokud přidáváte vyhledávání do zavedené aplikace, můžete mít existující identifikátory uživatelů a skupin v aad. V takovém případě můžete přeskočit další tři kroky. 

Pokud však nemáte stávající uživatele, můžete k vytvoření objektů zabezpečení použít rozhraní API aplikace Microsoft Graph. Následující fragmenty kódu ukazují, jak generovat identifikátory, které se stanou datovými hodnotami pro pole zabezpečení v indexu Azure Cognitive Search. V naší hypotetické žádosti o přijetí na vysokou školu by to byly bezpečnostní identifikátory pro přijímací personál.

Členství uživatelů a skupin může být velmi plynulé, zejména ve velkých organizacích. Kód, který vytváří identity uživatelů a skupin, by měl být spuštěn dostatečně často, aby bylo třeba vyzvednout změny členství v organizaci. Podobně váš index Azure Cognitive Search vyžaduje podobný plán aktualizací, aby odrážel aktuální stav povolených uživatelů a prostředků.

### <a name="step-1-create-aad-group"></a>Krok 1: Vytvoření [skupiny AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>Krok 2: Vytvoření [uživatele add](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Krok 3: Přidružení uživatele a skupiny
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Ukládat identifikátory skupin do mezipaměti
Volitelně můžete snížit latenci sítě, můžete ukládat do mezipaměti přidružení skupin uživatelů tak, aby při vydání požadavku na vyhledávání byly skupiny vráceny z mezipaměti a ukládají se do ad. Dávkové [rozhraní API AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching) můžete použít k odeslání jednoho požadavku Http s více uživateli a vytvoření mezipaměti.

Microsoft Graph je navržen tak, aby zpracovávat velký objem požadavků. Pokud dojde k obrovskému počtu požadavků, microsoft graph nezdaří požadavek se stavovým kódem HTTP 429. Další informace naleznete v [tématu Omezení aplikace Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexovat dokument s povolenými skupinami

Operace dotazů v Azure Cognitive Search se spouštějí přes index Azure Cognitive Search. V tomto kroku operace indexování importuje prohledávatelná data do indexu, včetně identifikátorů používaných jako filtry zabezpečení. 

Azure Cognitive Search neověřuje identity uživatelů ani neposkytuje logiku pro určení obsahu, k jehož zobrazení má uživatel oprávnění. Případ použití pro oříznutí zabezpečení předpokládá, že zadáte přidružení mezi citlivým dokumentem a identifikátorem skupiny, který má přístup k tomuto dokumentu, importované ho do indexu hledání. 

V hypotetickém příkladu by tělo žádosti PUT na indexu Azure Cognitive Search zahrnovalo esej nebo přepis uchazeče na vysokou školu spolu s identifikátorem skupiny, který má oprávnění k zobrazení tohoto obsahu. 

V obecném příkladu použitém v ukázce kódu pro tento návod může akce indexu vypadat takto:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Vydat žádost o hledání

Pro účely oříznutí zabezpečení jsou hodnoty v poli zabezpečení v indexu statické hodnoty používané pro zahrnutí nebo vyloučení dokumentů ve výsledcích hledání. Například pokud identifikátor skupiny pro přijetí je "A11B22C33D44-E55F66G77-H88I99JKK", všechny dokumenty v indexu Azure Cognitive Search, které mají tento identifikátor v zabezpečení soubor jsou zahrnuty (nebo vyloučeny) ve výsledcích hledání odeslaných zpět žadateli.

Chcete-li filtrovat dokumenty vrácené ve výsledcích hledání na základě skupin uživatele, který žádost vydal, přečtěte si následující kroky.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Načtení identifikátorů skupiny uživatele

Pokud skupiny uživatele ještě nebyly uloženy do mezipaměti nebo vypršela platnost mezipaměti, vydejte žádost o [skupiny](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Krok 2: Sestavte požadavek na hledání

Za předpokladu, že máte členství ve skupinách uživatele, můžete žádost o hledání vydat s příslušnými hodnotami filtru.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Krok 3: Zpracování výsledků

Odpověď obsahuje filtrovaný seznam dokumentů, který se skládá z těch, ke kterým má uživatel oprávnění k zobrazení. V závislosti na tom, jak vytvoříte stránku s výsledky hledání, můžete chtít zahrnout vizuální podněty tak, aby odrážely filtrovanou sadu výsledků.

## <a name="conclusion"></a>Závěr

V tomto návodu jste se naučili techniky pro použití přihlášení AAD k filtrování dokumentů ve výsledcích Azure Cognitive Search, oříznutí výsledků dokumentů, které neodpovídají filtru k dispozici na žádost.

## <a name="see-also"></a>Viz také

+ [Řízení přístupu založené na identitě pomocí filtrů Azure Cognitive Search](search-security-trimming-for-azure-search.md)
+ [Filtry v Azure Cognitive Search](search-filters.md)
+ [Zabezpečení dat a řízení přístupu v operacích Azure Cognitive Search](search-security-overview.md)
