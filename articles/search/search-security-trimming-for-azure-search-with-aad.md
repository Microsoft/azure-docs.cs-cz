---
title: Filtry zabezpečení pro oříznutí výsledků pomocí služby Active Directory – Azure Search
description: Řízení přístupu k obsahu Azure Search pomocí filtrů zabezpečení a identit Azure Active Directory (AAD).
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 8bcc1dcd1d86c0ca18ed03dc60834884a42a39c9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186531"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtry zabezpečení pro ořezávání výsledků Azure Search pomocí identit služby Active Directory

Tento článek ukazuje, jak používat identity zabezpečení Azure Active Directory (AAD) společně s filtry v Azure Search k vystřihování výsledků hledání na základě členství ve skupinách uživatelů.

Tento článek se zabývá následujícími úkony:
> [!div class="checklist"]
> - Vytvoření skupin AAD a uživatelů
> - Přidružte uživatele ke skupině, kterou jste vytvořili.
> - Ukládat nové skupiny do mezipaměti
> - Rejstřík dokumentů s přidruženými skupinami
> - Vystavení žádosti o vyhledávání pomocí filtru identifikátorů skupin
> 
> [!NOTE]
> Ukázky fragmentů kódu v tomto článku jsou napsané C#v. Úplný zdrojový kód najdete [na GitHubu](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Požadavky

Váš index v Azure Search musí mít [pole zabezpečení](search-security-trimming-for-azure-search.md) pro uložení seznamu identit skupiny, ke kterým má dokument oprávnění ke čtení. Tento případ použití předpokládá, že se jedná o korespondenci mezi zabezpečenou položkou (jako je aplikace školních aplikací jednotlivce) a bezpečnostní pole určující, kdo má přístup k této položce (zaměstnancům pro přijímání).

Musíte mít oprávnění správce AAD, které se v tomto návodu vyžaduje pro vytváření uživatelů, skupin a přidružení v AAD.

Vaše aplikace musí být také zaregistrovaná v AAD, jak je popsáno v následujícím postupu.

### <a name="register-your-application-with-aad"></a>Registrace aplikace pomocí AAD

Tento krok integruje vaši aplikaci s AAD pro účely přijímání přihlášení uživatelů a skupinových účtů. Pokud nejste správcem AAD ve vaší organizaci, možná budete muset [vytvořit nového tenanta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) , abyste mohli provést následující kroky.

1.  > Přejít na aplikaci na [**portál**](https://apps.dev.microsoft.com) >  pro registraci aplikací**Přidat aplikaci**
2. Zadejte název aplikace a pak klikněte na **vytvořit**. 
3. Na stránce Moje aplikace vyberte svou nově registrovanou aplikaci.
4. Na stránce pro registraci aplikace > **platformy** > **Přidat platformu**vyberte **webové rozhraní API**.
5. Pořád na stránce registrace aplikace otevřete > **Microsoft Graph oprávnění** > **Přidat**.
6. V části vybrat oprávnění přidejte následující delegovaná oprávnění a pak klikněte na **OK**:

   + **Adresář. pročtení. vše**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph poskytuje rozhraní API, které umožňuje programový přístup k AAD prostřednictvím REST API. Ukázka kódu pro tento návod používá oprávnění k volání rozhraní Microsoft Graph API pro vytváření skupin, uživatelů a přidružení. Rozhraní API se také používají k ukládání identifikátorů skupin do mezipaměti pro rychlejší filtrování.

## <a name="create-users-and-groups"></a>Vytváření uživatelů a skupin

Pokud přidáváte vyhledávání do zavedené aplikace, můžete v AAD mít existující identifikátory uživatelů a skupin. V takovém případě můžete přeskočit další tři kroky. 

Pokud ale existující uživatele nemáte, můžete k vytvoření objektů zabezpečení použít rozhraní Microsoft Graph API. Následující fragmenty kódu ukazují, jak generovat identifikátory, které se stanou hodnotami dat pro pole zabezpečení v indexu Azure Search. V naší aplikaci pro přijímání hypotetické školy se jedná o identifikátory zabezpečení pro pracovníky, kteří mají přístup.

Členství uživatele a skupiny může být velmi kapalina, zejména ve velkých organizacích. Kód, který sestavuje identity uživatelů a skupin, by měl být často spuštěný dostatečně, aby bylo možné vybírat změny v členství organizace. Podobně, váš Azure Search index vyžaduje podobný plán aktualizace, který odráží aktuální stav povolených uživatelů a prostředků.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Krok 1: Vytvořit [skupinu AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>Krok 2: Vytvořit [uživatele AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
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

### <a name="step-3-associate-user-and-group"></a>Krok 3: Přidružte uživatele a skupinu.
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Ukládání identifikátorů skupin do mezipaměti
Pokud chcete snížit latenci sítě, můžete přidružení skupin uživatelů ukládat do mezipaměti, aby při vydání žádosti o vyhledávání byly skupiny vráceny z mezipaměti a ukládaly do AAD zpětný převod. Pomocí [rozhraní API pro AAD Batch](https://developer.microsoft.com/graph/docs/concepts/json_batching) můžete odeslat jednu žádost HTTP s více uživateli a sestavit mezipaměť.

Microsoft Graph je navržena tak, aby zpracovávala velký objem požadavků. Pokud dojde k zahlcení počtu požadavků, Microsoft Graph požadavek se stavovým kódem HTTP 429. Další informace najdete v tématu [omezení Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexovat dokument s povolenými skupinami

Operace dotazů v Azure Search jsou spouštěny prostřednictvím indexu Azure Search. V tomto kroku operace indexování importuje hledaná data do indexu, včetně identifikátorů použitých jako filtry zabezpečení. 

Azure Search neověřuje identity uživatelů, nebo poskytuje logiku pro zjištění, který obsah má uživatel oprávnění k zobrazení. Případ použití pro ořezávání zabezpečení předpokládá, že zadáváte přidružení mezi citlivý dokument a identifikátor skupiny, který má přístup k tomuto dokumentu, importován do indexu vyhledávání beze změny. 

V hypotetickém příkladu tělo žádosti o vložení na Azure Search indexu zahrnuje Essay školy kandidáta nebo přepis spolu s identifikátorem skupiny, který má oprávnění k zobrazení tohoto obsahu. 

V obecném příkladu použitém v ukázce kódu pro tento návod může akce index vypadat takto:

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

## <a name="issue-a-search-request"></a>Vydat požadavek hledání

Pro účely oříznutí zabezpečení jsou hodnoty v poli zabezpečení v indexu statické hodnoty používané pro zahrnutí nebo vyloučení dokumentů ve výsledcích hledání. Pokud je například identifikátor skupiny pro přístup "A11B22C33D44-E55F66G77-H88I99JKK", všechny dokumenty v Azure Search indexu s tímto identifikátorem v zaznamenaném zabezpečení jsou zahrnuty (nebo vyloučeny) do výsledků hledání odeslaných zpět žadateli.

Pokud chcete filtrovat dokumenty vrácené ve výsledcích hledání na základě skupin uživatele, který požadavek vystavil, Projděte si následující postup.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Načíst identifikátory skupin uživatelů

Pokud skupiny uživatelů ještě nejsou uložené v mezipaměti nebo vypršela platnost mezipaměti, vydejte požadavek [skupin](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) .
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

### <a name="step-2-compose-the-search-request"></a>Krok 2: Vytvoření žádosti o hledání

Za předpokladu, že máte členství uživatele ve skupinách, můžete vystavit požadavek hledání pomocí příslušných hodnot filtru.

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

Odpověď obsahuje filtrovaný seznam dokumentů, který se skládá z těch, které má uživatel oprávnění k zobrazení. V závislosti na tom, jak vytváříte stránku výsledků hledání, můžete chtít zahrnout vizuální pomůcky, které odrážejí filtrovanou sadu výsledků.

## <a name="conclusion"></a>Závěr

V tomto návodu jste zjistili techniky použití přihlášení AAD k filtrování dokumentů v Azure Search výsledky, oříznutí výsledků dokumentů, které neodpovídají filtru zadanému na žádosti.

## <a name="see-also"></a>Viz také:

+ [Řízení přístupu na základě identity pomocí filtrů Azure Search](search-security-trimming-for-azure-search.md)
+ [Filtry v Azure Search](search-filters.md)
+ [Zabezpečení a řízení přístupu k datům v Azure Searchch operacích](search-security-overview.md)
