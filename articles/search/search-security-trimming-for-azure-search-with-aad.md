---
title: Filtry zabezpečení pro oříznutí výsledků pomocí služby Active Directory
titleSuffix: Azure Cognitive Search
description: Naučte se implementovat oprávnění zabezpečení na úrovni dokumentu pro Azure Kognitivní hledání výsledky hledání pomocí filtrů zabezpečení a identit Azure Active Directory (AD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629506"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtry zabezpečení pro oříznutí výsledků Kognitivní hledání Azure pomocí identit služby Active Directory

Tento článek ukazuje, jak používat bezpečnostní identity Azure Active Directory (AD) společně s filtry ve službě Azure Kognitivní hledání k vystřihování výsledků hledání na základě členství ve skupinách uživatelů.

Tento článek se zabývá následujícími úkony:
> [!div class="checklist"]
> - Vytváření skupin a uživatelů Azure AD
> - Přidružte uživatele ke skupině, kterou jste vytvořili.
> - Ukládat nové skupiny do mezipaměti
> - Rejstřík dokumentů s přidruženými skupinami
> - Vystavení žádosti o vyhledávání pomocí filtru identifikátorů skupin
> 
> [!NOTE]
> Ukázky fragmentů kódu v tomto článku jsou napsané v jazyce C#. Úplný zdrojový kód najdete [na GitHubu](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Požadavky

Váš index v Azure Kognitivní hledání musí mít [pole zabezpečení](search-security-trimming-for-azure-search.md) pro uložení seznamu identit skupin, které mají k dokumentu přístup pro čtení. Tento případ použití předpokládá, že se jedná o korespondenci mezi zabezpečenou položkou (jako je aplikace školních aplikací jednotlivce) a bezpečnostní pole určující, kdo má přístup k této položce (zaměstnancům pro přijímání).

Musíte mít oprávnění správce Azure AD, které se vyžaduje v tomto návodu pro vytváření uživatelů, skupin a přidružení. 

Vaše aplikace musí být také zaregistrovaná ve službě Azure AD jako aplikace pro více tenantů, jak je popsáno v následujícím postupu.

### <a name="register-your-application-with-azure-active-directory"></a>Zaregistrujte svoji aplikaci pomocí Azure Active Directory

Tento krok integruje vaši aplikaci se službou Azure AD za účelem přijetí přihlašovacích účtů uživatelů a skupin. Pokud nejste správcem tenanta ve vaší organizaci, možná budete muset [vytvořit nového tenanta](../active-directory/develop/quickstart-create-new-tenant.md) , abyste mohli provést následující kroky.

1. V [Azure Portal](https://portal.azure.com)vyhledejte prostředek Azure Active Directory pro vaše předplatné.

1. Na levé straně v části **Spravovat** vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.

1. Dejte registraci název, třeba název, který se podobá názvu vyhledávací aplikace. Vyberte **Zaregistrovat**.

1. Po vytvoření registrace aplikace zkopírujte ID aplikace. Tento řetězec budete muset zadat do aplikace.

   Pokud procházíte [DotNetHowToSecurityTrimmingem](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), vložte tuto hodnotu do souboru **app.config** .

   Opakujte pro ID tenanta.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID aplikace v části Essentials":::

1. Na levé straně vyberte **oprávnění rozhraní API** a pak vyberte **Přidat oprávnění**. 

1. Vyberte **Microsoft Graph** a pak vyberte **delegovaná oprávnění**.

1. Vyhledejte a pak přidejte následující delegovaná oprávnění:

   - **Adresář. pročtení. vše**
   - **Group.ReadWrite.All**
   - **User. pročtení. All**

Microsoft Graph poskytuje rozhraní API, které umožňuje programový přístup k Azure AD prostřednictvím REST API. Ukázka kódu pro tento návod používá oprávnění k volání rozhraní Microsoft Graph API pro vytváření skupin, uživatelů a přidružení. Rozhraní API se také používají k ukládání identifikátorů skupin do mezipaměti pro rychlejší filtrování.

## <a name="create-users-and-groups"></a>Vytváření uživatelů a skupin

Pokud přidáváte hledání do vytvořené aplikace, můžete mít v Azure AD existující identifikátory uživatelů a skupin. V takovém případě můžete přeskočit další tři kroky. 

Pokud ale existující uživatele nemáte, můžete k vytvoření objektů zabezpečení použít rozhraní Microsoft Graph API. Následující fragmenty kódu ukazují, jak generovat identifikátory, které se stanou hodnotami dat pro pole zabezpečení v indexu Azure Kognitivní hledání. V naší aplikaci pro přijímání hypotetické školy se jedná o identifikátory zabezpečení pro pracovníky, kteří mají přístup.

Členství uživatele a skupiny může být velmi kapalina, zejména ve velkých organizacích. Kód, který sestavuje identity uživatelů a skupin, by měl být často spuštěný dostatečně, aby bylo možné vybírat změny v členství organizace. Stejně tak váš index služby Azure Kognitivní hledání vyžaduje podobný plán aktualizace, který odráží aktuální stav povolených uživatelů a prostředků.

### <a name="step-1-create-group"></a>Krok 1: [Vytvoření skupiny](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Krok 2: [Vytvoření uživatele](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Krok 3: přidružení uživatele a skupiny

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Krok 4: ukládání identifikátorů skupin do mezipaměti

Pokud chcete snížit latenci sítě, můžete přidružení uživatelů do skupin ukládat do mezipaměti, aby při vydání žádosti o vyhledávání byly skupiny vracené z mezipaměti a ukládaly zpět do Azure AD. Pomocí [rozhraní API služby Batch pro Azure AD](/graph/json-batching) můžete odeslat jednu žádost HTTP s více uživateli a sestavit mezipaměť.

Microsoft Graph je navržena tak, aby zpracovávala velký objem požadavků. Pokud dojde k zahlcení počtu požadavků, Microsoft Graph požadavek se stavovým kódem HTTP 429. Další informace najdete v tématu [omezení Microsoft Graph](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexovat dokument s povolenými skupinami

Operace dotazů v Azure Kognitivní hledání se provádějí přes index Azure Kognitivní hledání. V tomto kroku operace indexování importuje hledaná data do indexu, včetně identifikátorů použitých jako filtry zabezpečení. 

Azure Kognitivní hledání neověřuje identity uživatelů ani neposkytuje logiku pro zjištění, který obsah má uživatel oprávnění k zobrazení. Případ použití pro ořezávání zabezpečení předpokládá, že zadáváte přidružení mezi citlivý dokument a identifikátor skupiny, který má přístup k tomuto dokumentu, importován do indexu vyhledávání beze změny. 

V hypotetickém příkladu tělo žádosti o vložení na indexu služby Azure Kognitivní hledání by zahrnovalo essay školního typu nebo přepisu spolu s identifikátorem skupiny, který má oprávnění k zobrazení tohoto obsahu. 

V obecném příkladu použitém v ukázce kódu pro tento návod může akce index vypadat takto:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Vydat požadavek hledání

Pro účely oříznutí zabezpečení jsou hodnoty v poli zabezpečení v indexu statické hodnoty používané pro zahrnutí nebo vyloučení dokumentů ve výsledcích hledání. Pokud je například identifikátor skupiny pro přístup "A11B22C33D44-E55F66G77-H88I99JKK", všechny dokumenty v indexu služby Azure Kognitivní hledání s tímto identifikátorem v zaznamenaném zabezpečení jsou zahrnuty (nebo vyloučeny) ve výsledcích hledání odeslaných zpět žadateli.

Pokud chcete filtrovat dokumenty vrácené ve výsledcích hledání na základě skupin uživatele, který požadavek vystavil, Projděte si následující postup.

### <a name="step-1-retrieve-users-group-identifiers"></a>Krok 1: načtení identifikátorů skupin uživatelů

Pokud se skupiny uživatelů ještě neuložily do mezipaměti nebo vypršela její platnost, vydejte požadavek [skupin](/graph/api/directoryobject-getmembergroups) .

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Krok 2: vytvoření žádosti o hledání

Za předpokladu, že máte členství uživatele ve skupinách, můžete vystavit požadavek hledání pomocí příslušných hodnot filtru.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Krok 3: zpracování výsledků

Odpověď obsahuje filtrovaný seznam dokumentů, který se skládá z těch, které má uživatel oprávnění k zobrazení. V závislosti na tom, jak vytváříte stránku výsledků hledání, můžete chtít zahrnout vizuální pomůcky, které odrážejí filtrovanou sadu výsledků.

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se dozvěděli o vzorech pro používání přihlášení Azure AD k filtrování dokumentů v Azure Kognitivní hledání výsledky, oříznutí výsledků dokumentů, které neodpovídají filtru zadanému na žádosti. Alternativní vzor, který může být jednodušší nebo přecházet další funkce zabezpečení, najdete na následujících odkazech.

- [Filtry zabezpečení pro oříznutí výsledků](search-security-trimming-for-azure-search.md)
- [Zabezpečení v Azure Kognitivní hledání](search-security-overview.md)