---
title: Filtry zabezpečení pro oříznutí výsledky hledání Azure pomocí služby Active Directory identit | Microsoft Docs
description: Řízení přístupu na obsahu Azure Search pomocí filtrů zabezpečení a identity služby Active Directory.
author: revitalbarletz
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: revitalb
ms.openlocfilehash: 7c1723e01c78132169d8975473a0e9f5466a066c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtry zabezpečení pro ořezávání výsledky Azure Search pomocí identit služby Active Directory

Tento článek ukazuje, jak používat identity zabezpečení Azure Active Directory (AAD) společně s filtry ve službě Azure Search oříznout výsledky vyhledávání založené na členství ve skupině uživatelů.

Tento článek se zabývá následujícími úkony:
> [!div class="checklist"]
- Vytváření AAD skupin a uživatelů
- Přiřadit uživatele na skupinu, kterou jste vytvořili
- Nové skupiny do mezipaměti
- Indexování dokumentů s související skupiny
- Vydejte žádost o vyhledávání pomocí filtru identifikátory skupiny

>[!NOTE]
> Ukázka fragmenty kódu v tomto článku jsou napsané v C#. Úplný zdrojový kód najdete [na GitHubu](http://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Požadavky

Musí mít indexu ve službě Azure Search [zabezpečení pole](search-security-trimming-for-azure-search.md) k uložení seznamu identit skupiny, kteří mají přístup pro čtení k dokumentu. Tento případ použití předpokládá souvislosti mezi položkou zabezpečitelné (například k jednotlivcům univerzity, kterou aplikace) a určení, kdo má přístup k této položce (přijetí pracovníky) pole zabezpečení.

Musí mít oprávnění správce AAD, vyžaduje v tomto názorném postupu pro vytváření uživatelů, skupin a přidružení v AAD.

Aplikace musí být také zaregistrováno v AAD, jak je popsáno v následujícím postupu.

### <a name="register-your-application-with-aad"></a>Registrace vaší aplikace v AAD

Tento krok integruje vaše aplikace s AAD za účelem přijetí přihlášení účty uživatelů a skupin. Pokud si nejste správcem AAD ve vaší organizaci, možná budete muset [vytvořit nového klienta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) provést následující kroky.

1. Přejděte na [ **portálu pro registraci aplikace**](https://apps.dev.microsoft.com) >  **sblížené aplikace** > **přidat aplikaci**.
2. Zadejte název pro vaši aplikaci a pak klikněte na **vytvořit**. 
3. Vyberte aplikaci nově zaregistrovaný na stránce Moje aplikace.
4. Na stránce registrace aplikace > **platformy** > **přidejte platformu**, zvolte **webového rozhraní API**.
5. Ještě na stránce registrace aplikace, přejděte na > **Microsoft Graph oprávnění** > **přidat**.
6. Vyberte oprávnění, přidejte následující delegovaná oprávnění a potom klikněte na **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph poskytuje rozhraní API, která umožňuje programový přístup k AAD přes REST API. Ukázka kódu pro tento návod používá oprávnění k volání rozhraní Microsoft Graph API pro vytváření skupin, uživatele a přidružení. Rozhraní API se taky používají k mezipaměti skupiny identifikátory pro rychlejší filtrování.

## <a name="create-users-and-groups"></a>Vytvoření uživatelů a skupin

Pokud přidáváte vyhledávání zavedené aplikace, může být stávajícího uživatele a skupiny v AAD. V takovém případě můžete přeskočit následující tři kroky. 

Ale pokud nemáte stávajících uživatelů, můžete rozhraní Microsoft Graph API k vytvoření objektů zabezpečení. Následující fragmenty kódu ukazují, jak vygenerovat identifikátory, které se stanou hodnoty dat pro zabezpečení pole v indexu Azure Search. V naší aplikaci přijetí hypotetický univerzity, kterou bude identifikátory zabezpečení pracovníků, přijetí.

Může být velmi plynulá práce zvlášť ve velkých organizacích, uživatele a členství ve skupině. Kód, který sestaví identity uživatelů a skupin měli spustit dostatečně často mohla vybrat změny v členství v organizaci. Obdobně indexu Azure Search vyžaduje plán aktualizací podobné tak, aby odrážela aktuální stav povolených uživatelů a prostředků.

### <a name="step-1-create-aad-grouphttpsdevelopermicrosoftcomgraphdocsapi-referencev10apigrouppostgroups"></a>Krok 1: Vytvoření [skupiny AAD](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/group_post_groups) 
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
   
### <a name="step-2-create-aad-userhttpsdevelopermicrosoftcomgraphdocsapi-referencev10apiuserpostusers"></a>Krok 2: Vytvoření [AAD uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_post_users) 
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

### <a name="step-3-associate-user-and-group"></a>Krok 3: Přidružení uživatelů a skupin
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: Mezipaměti identifikátory skupiny
Volitelně můžete snížit latenci sítě, můžete mezipaměti přidružení skupiny uživatelů, aby při vydání žádost o vyhledávání skupin jsou vráceny z mezipaměti, ukládání zvyšuje výkon aad. Můžete použít (AAD Batch API) [https://developer.microsoft.com/graph/docs/concepts/json_batching] k odeslání jedné žádosti Http s více uživateli a sestavení mezipaměti.

Microsoft Graph je určený k řešení k velkému počtu požadavků. Dojde-li čtenáře počet požadavků, Microsoft Graph selže s kódem stavu HTTP 429 žádosti. Další informace najdete v tématu [Microsoft Graph omezení](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>U dokumentu indexu s jejich povolených skupin

Provedení operace dotazů ve službě Azure Search přes indexu Azure Search. V tomto kroku operace indexování importuje prohledávatelná data do indexu, včetně identifikátory používané jako filtry zabezpečení. 

Vyhledávání systému Azure není ověření identity uživatele nebo zadejte logiku pro vytvoření obsah, který má uživatel oprávnění k zobrazení. Případ použití oříznutí zabezpečení se předpokládá, že zadáte přidružení mezi citlivých dokumentů a identifikátor skupiny, které mají přístup k dokumentu, Internet nedotčené importovat do indexu vyhledávání. 

V příkladu hypotetický bude zahrnovat těle žádosti PUT na index Azure Search kompozice univerzity, kterou žadatele nebo přepis společně s identifikátor skupiny mají oprávnění k zobrazení tohoto obsahu. 

Akce indexu obecné příkladu v ukázce kódu v tomto návodu, může vypadat takto:

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

## <a name="issue-a-search-request"></a>Vydat žádost o vyhledávání

Z bezpečnostních důvodů oříznutí hodnoty ve vašem oboru zabezpečení v indexu jsou statické hodnoty používané pro zahrnutí nebo vyloučení dokumenty ve výsledcích hledání. Například pokud je identifikátor skupiny pro přijetí "A11B22C33D44 E55F66G77 H88I99JKK", všechny dokumenty v indexu Azure Search, že tento identifikátor v zabezpečení selhala jsou zahrnuty (nebo vyloučit) ve výsledcích hledání odeslána zpět do žadatel.

Chcete-li filtrovat dokumenty vrácené ve výsledcích hledání na základě skupin vydání žádosti o uživatele, zkontrolujte následující kroky.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: Načíst identifikátory skupiny uživatele

Pokud uživatele skupiny již do mezipaměti neuložily, nebo vypršela platnost mezipaměti, [skupiny](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/directoryobject_getmembergroups) požadavku
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

### <a name="step-2-compose-the-search-request"></a>Krok 2: Tvoří požadavek hledání

Za předpokladu, že máte členství skupiny uživatele, můžete vydat požadavek hledání hodnotami příslušný filtr.

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

Odpověď obsahuje filtrovaný seznam dokumentů, který se skládá z těch, které má uživatel oprávnění k zobrazení. V závislosti na tom, jak vytvořit stránky s výsledky hledání můžete chtít zahrnují vizuální upozornění tak, aby odrážela filtrované výsledné sady.

## <a name="conclusion"></a>Závěr

V tomto návodu jste se dozvěděli techniky pro filtrování dokumenty ve výsledcích hledání Azure, pomocí AAD přihlášení ořezávání výsledky dokumenty, které neodpovídají filtr zadaný v požadavku.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Řízení přístupu na základě identit pomocí filtrů Azure Search](search-security-trimming-for-azure-search.md)
+ [Filtry ve službě Azure Search](search-filters.md)
+ [Řízení přístupu a zabezpečení a data v operacích Azure Search](search-security-overview.md)
