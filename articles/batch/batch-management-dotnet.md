---
title: Správa prostředků účtů pomocí knihovny Batch Management .NET
description: Vytvářejte, odstraňujte a upravujte prostředky účtu Azure Batch pomocí knihovny Batch Management .NET.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896727"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Správa účtů a kvót služby Batch pomocí klientské knihovny pro správu služby Batch pro .NET

Režijní náklady na údržbu v aplikacích Azure Batch můžete snížit pomocí knihovny [.NET Management .NET](/dotnet/api/overview/azure/batch) k automatizaci vytváření, odstraňování, správy klíčů a zjišťování kvót účtů Batch.

- **Vytváření a odstraňování účtů služby Batch** v rámci libovolné oblasti. Pokud například jako nezávislý dodavatel softwaru (ISV) zadáte pro klienty službu, ve které každé z nich pro účely fakturace přiřadíte samostatný účet Batch, můžete přidat možnosti vytváření a mazání účtů na zákaznický portál.
- **Obnovte a obnovujte klíče účtu** programově pro všechny účty Batch. To vám může pomáhat při dodržování zásad zabezpečení, které vynutily pravidelné přecházení nebo vypršení platnosti klíčů účtu. Pokud máte několik účtů Batch v různých oblastech Azure, automatizace tohoto procesu změny zvyšuje efektivitu vašeho řešení.
- **Ověřte kvóty účtu a vyjděte** si zkušební verzi a chybu a uveďte, které účty Batch mají omezení. Kontrolou kvót účtu před zahájením úloh, vytvářením fondů nebo přidáním výpočetních uzlů můžete proaktivně upravit, kde nebo kdy se tyto výpočetní prostředky vytvářejí. Můžete určit, které účty vyžadují zvýšení kvóty před přidělením dalších prostředků v těchto účtech.
- **Kombinování funkcí dalších služeb Azure** pro plnohodnotné prostředí pro správu pomocí služby Batch Management .net, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)a [Azure Resource Manager](../azure-resource-manager/management/overview.md) společně ve stejné aplikaci. Pomocí těchto funkcí a jejich rozhraní API můžete poskytovat bezproblémový způsob ověřování, možnost vytvářet a odstraňovat skupiny prostředků a možnosti, které jsou popsané výše pro komplexní řešení pro správu.

> [!NOTE]
> I když se tento článek zaměřuje na programovou správu účtů, klíčů a kvót služby Batch, můžete také provádět spoustu těchto aktivit [pomocí Azure Portal](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Vytváření a odstraňování účtů Batch

Jednou z primárních funkcí rozhraní API pro správu služby Batch je vytváření a odstraňování [účtů Batch](accounts.md) v oblasti Azure. Provedete to tak, že použijete [BatchManagementClient. Account. CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) a [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync)nebo jejich synchronní protějšky.

Následující fragment kódu vytvoří účet, získá nově vytvořený účet ze služby Batch a pak ho odstraní. V tomto fragmentu kódu a dalších dalších v tomto článku `batchManagementClient` je plně inicializovaná instance [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Aplikace, které používají knihovnu služby Batch Management .NET a její třídu BatchManagementClient, vyžadují, aby správce služeb nebo spolusprávce měl přístup k předplatnému, které vlastní účet Batch ke správě. Další informace naleznete v části Azure Active Directory a v ukázce kódu [službu AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) .

## <a name="retrieve-and-regenerate-account-keys"></a>Načíst a znovu vygenerovat klíče účtu

Získejte primární a sekundární klíč účtu z libovolného účtu Batch v rámci předplatného pomocí [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Tyto klíče můžete znovu vygenerovat pomocí [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Pro aplikace pro správu můžete vytvořit zjednodušený pracovní postup připojení. Nejdřív Získejte klíč účtu pro účet Batch, který chcete spravovat pomocí [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Pak tento klíč použijte při inicializaci třídy [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) knihovny Batch .NET, která se používá při inicializaci [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Zkontroluje předplatné Azure a kvóty účtu Batch.

Předplatná Azure a jednotlivé služby Azure, jako je Batch, mají výchozí kvóty, které omezují počet určitých entit v nich. Výchozí kvóty pro předplatná Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Výchozí kvóty služby Batch najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md). Pomocí knihovny .NET Management .NET Library můžete tyto kvóty ověřit ve svých aplikacích. To vám umožní udělat rozhodnutí o přidělení před přidáním účtů nebo výpočetních prostředků, jako jsou fondy a výpočetní uzly.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Podívejte se na předplatné Azure pro kvóty účtu Batch.

Před vytvořením účtu Batch v oblasti si můžete prohlédnout předplatné Azure a zjistit, jestli jste v této oblasti mohli přidat účet.

V níže uvedeném fragmentu kódu jsme nejdřív použili **ListAsync** k získání kolekce všech účtů Batch, které jsou v rámci předplatného. Až tuto kolekci získáme, určíme, kolik účtů je v cílové oblasti. Pak pomocí **GetQuotasAsync** získáme kvótu účtu Batch a určíte, kolik účtů (pokud existuje) se v této oblasti dá vytvořit.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Ve fragmentu kódu výše `creds` je instance **TokenCredentials**. Příklad vytvoření tohoto objektu naleznete v ukázce kódu [službu AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) na GitHubu.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Zkontroluje účet Batch pro kvóty výpočetních prostředků.

Před zvýšením výpočetních prostředků ve vašem řešení služby Batch můžete zkontrolovat, že prostředky, které chcete přidělit, nepřekročí kvóty účtu. Ve fragmentu kódu níže vytiskněte informace o kvótě pro účet Batch s názvem `mybatchaccount` . Ve vaší vlastní aplikaci můžete tyto informace použít k určení, jestli účet může zpracovávat další prostředky, které se mají vytvořit.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> I když existují výchozí kvóty pro služby a předplatná Azure, mnohé z těchto omezení je možné vyhodnotit [vyžádáním zvýšení kvóty v Azure Portal](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Použití Azure AD s rozhraním Batch Management .NET

Knihovna Batch Management .NET je klientem poskytovatele prostředků Azure, který se používá společně se [Azure Resource Manager](../azure-resource-manager/management/overview.md) ke správě prostředků účtů prostřednictvím kódu programu. Služba Azure AD se vyžaduje k ověřování požadavků provedených prostřednictvím libovolného klienta poskytovatele prostředků Azure, včetně knihovny Batch Management .NET Library a až po Azure Resource Manager. Informace o používání služby Azure AD s knihovnou Batch Management .NET najdete v tématu [použití Azure Active Directory k ověřování dávkových řešení](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Ukázkový projekt na GitHubu

Pokud se chcete podívat na rozhraní Batch Management .NET v akci, podívejte se na vzorový projekt [službu AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) na GitHubu. Ukázková aplikace službu AccountManagement předvádí následující operace:

1. Získejte token zabezpečení ze služby Azure AD pomocí knihovny [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Pokud uživatel ještě není přihlášený, zobrazí se jim výzva k zadání přihlašovacích údajů Azure.
2. Pomocí tokenu zabezpečení získaného z Azure AD vytvořte [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) pro dotaz na Azure, kde najdete seznam předplatných přidružených k účtu. Uživatel může vybrat předplatné ze seznamu, pokud obsahuje více než jedno předplatné.
3. Získá přihlašovací údaje přidružené k vybranému předplatnému.
4. Vytvořte objekt [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) pomocí přihlašovacích údajů.
5. K vytvoření skupiny prostředků použijte objekt [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) .
6. K provedení několika operací účtu Batch použijte objekt [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) :
   - Vytvořte účet Batch v nové skupině prostředků.
   - Získejte nově vytvořený účet ze služby Batch.
   - Vytiskněte klíče účtu pro nový účet.
   - Znovu Vygenerujte nový primární klíč pro účet.
   - Vytiskněte informace o kvótě pro účet.
   - Vytiskněte informace o kvótě pro předplatné.
   - Tisk všech účtů v rámci předplatného.
   - Odstraňte nově vytvořený účet.
7. Odstraňte skupinu prostředků.

Pokud chcete ukázkovou aplikaci úspěšně spustit, musíte ji nejdřív zaregistrovat u svého tenanta Azure AD v Azure Portal a udělit oprávnění Azure Resource Manager API. Postupujte podle kroků uvedených v části [ověřování řešení Batch Management se službou Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](quick-run-dotnet.md) nebo [Pythonu](quick-run-python.md). Tento rychlý Start vás provede ukázkovou aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a to pomocí Azure Storage pro přípravu a načítání souborů úloh. Git PUS
