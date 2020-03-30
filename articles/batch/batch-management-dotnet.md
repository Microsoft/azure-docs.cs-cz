---
title: Správa prostředků účtu pomocí knihovny Správy dávek .NET
description: Vytvořte, odstraňte a upravte prostředky účtu Azure Batch pomocí knihovny Batch Management .NET.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 79916d769ad8a7228aec8db965c29506ccd78ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023680"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Správa dávkových účtů a kvót pomocí klientské knihovny správy dávek pro rozhraní .NET

> [!div class="op_single_selector"]
> * [Portál Azure](batch-account-create-portal.md)
> * [Knihovna Batch Management .NET](batch-management-dotnet.md)
> 
> 

Můžete snížit nároky na údržbu ve vašich aplikacích Azure Batch pomocí [knihovny Batch Management .NET][api_mgmt_net] k automatizaci vytváření, odstraňování, správy klíčů a zjišťování kvót.

* **Vytvořte a odstraňte dávkové účty** v libovolné oblasti. Pokud například jako nezávislý dodavatel softwaru (ISV) poskytujete službu pro své klienty, ve které je každému z nich přiřazen samostatný účet Batch pro účely fakturace, můžete na zákaznický portál přidat možnosti pro vytvoření a odstranění účtu.
* **Načíst a obnovit klíče účtu** programově pro některý z vašich účtů Batch. To vám může pomoci dodržovat zásady zabezpečení, které vynucují pravidelné převrácení nebo vypršení platnosti klíčů účtu. Pokud máte několik účtů Batch v různých oblastech Azure, automatizace tohoto procesu přechodu zvyšuje efektivitu vašeho řešení.
* **Zkontrolujte kvóty účtu** a vezměte zkušební a chybové odhady z určení, které dávkové účty mají jaké limity. Kontrolou kvót účtu před zahájením úloh, vytvářením fondů nebo přidáním výpočetních uzlů můžete proaktivně upravit, kde nebo kdy jsou tyto výpočetní prostředky vytvořeny. Můžete určit, které účty vyžadují zvýšení kvóty před přidělením dalších prostředků v těchto účtech.
* **Kombinujte funkce jiných služeb Azure** pro plnohodnotné prostředí správy – pomocí správy dávek .NET, Azure Active [Directory][aad_about]a Azure [Resource Manager][resman_overview] společně ve stejné aplikaci. Pomocí těchto funkcí a jejich api, můžete poskytnout prostředí ověřování bez tření, možnost vytvářet a odstraňovat skupiny prostředků a možnosti, které jsou popsány výše pro komplexní řešení správy.

> [!NOTE]
> Zatímco tento článek se zaměřuje na programovou správu účtů Batch, klíče a kvóty, můžete provádět mnoho z těchto aktivit pomocí [portálu Azure][azure_portal]. Další informace najdete [v tématu Vytvoření účtu Azure Batch pomocí portálu Azure a](batch-account-create-portal.md) [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Vytvoření a odstranění dávkových účtů
Jak již bylo zmíněno, jednou z primárních funkcí rozhraní API pro správu dávek je vytvoření a odstranění dávkových účtů v oblasti Azure. Chcete-li tak učinit, použijte [BatchManagementClient.Account.CreateAsync][net_create] a [DeleteAsync][net_delete]nebo jejich synchronní protějšky.

Následující fragment kódu vytvoří účet, získá nově vytvořený účet ze služby Batch a odstraní jej. V tomto fragmentu a ostatní v `batchManagementClient` tomto článku je plně inicializované instance [BatchManagementClient][net_mgmt_client].

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
> Aplikace, které používají knihovnu Batch Management .NET a její třídu BatchManagementClient, vyžadují **správce služby** nebo **spolusprávce** přístup k předplatnému, které vlastní účet Batch, který má být spravován. Další informace najdete v části Azure Active Directory a v ukázce kódu [AccountManagement.][acct_mgmt_sample]
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Načíst a znovu vygenerovat klíče účtu
Získejte primární a sekundární klíče účtu z libovolného účtu Batch v rámci předplatného pomocí [ListKeysAsync][net_list_keys]. Tyto klíče můžete znovu vygenerovat pomocí [regenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
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
> Můžete vytvořit zjednodušený pracovní postup připojení pro vaše aplikace pro správu. Nejprve získejte klíč účtu pro účet Batch, který chcete spravovat pomocí [aplikace ListKeysAsync][net_list_keys]. Potom použijte tento klíč při inicializaci batch .NET [knihovny BatchSharedKeyCredentials třídy,][net_sharedkeycred] která se používá při inicializaci [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrola kvót předplatného Azure a dávkového účtu
Předplatná Azure a jednotlivé služby Azure, jako je Batch, mají výchozí kvóty, které omezují počet určitých entit v nich. Výchozí kvóty pro předplatná Azure najdete v [tématu limity předplatného azure a služby, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md). Výchozí kvóty služby Batch najdete v [tématu Kvóty a limity pro službu Azure Batch](batch-quota-limit.md). Pomocí knihovny Správa dávek .NET můžete tyto kvóty zkontrolovat v aplikacích. To umožňuje provádět rozhodnutí o přidělení před přidáním účtů nebo výpočetníprostředky, jako jsou fondy a výpočetní uzly.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrola kvót účtu Batch pro předplatné Azure
Před vytvořením účtu Batch v oblasti můžete zkontrolovat předplatné Azure a zjistit, jestli můžete přidat účet v této oblasti.

V kódu fragment níže nejprve použijeme [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] získat kolekci všech účtů Batch, které jsou v rámci předplatného. Jakmile jsme získali tuto kolekci, zjistíme, kolik účtů jsou v cílové oblasti. Potom použijeme [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] získat kvótu účtu Batch a určit, kolik účtů (pokud existuje) lze vytvořit v této oblasti.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Ve výstřižku `creds` výše je instancí [TokenCloudCredentials][azure_tokencreds]. Příklad vytvoření tohoto objektu najdete v ukázkové části kódu [AccountManagement][acct_mgmt_sample] na GitHubu.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrola dávkového účtu pro kvóty výpočetních prostředků
Před zvýšením výpočetních prostředků v řešení Batch můžete zkontrolovat, zda prostředky, které chcete přidělit, nepřekročí kvóty účtu. Ve výstřižku kódu níže vytiskneme informace `mybatchaccount`o kvótě pro účet Batch s názvem . Ve vlastní aplikaci můžete tyto informace použít k určení, zda účet může zpracovávat další prostředky, které mají být vytvořeny.

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
> Zatímco existují výchozí kvóty pro předplatná azure a služby, mnoho z těchto omezení lze zvýšit vydáním požadavku na [webu Azure Portal][azure_portal]. Například v [tématu kvóty a limity pro službu Azure Batch](batch-quota-limit.md) pokyny ke zvýšení kvóty účtu Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Použití azure ad se správou dávek .NET

Knihovna Batch Management .NET je klientem zprostředkovatele prostředků Azure a používá se společně se [Správcem prostředků Azure][resman_overview] ke správě prostředků účtu programově. Azure AD je potřeba k ověření požadavků provedených prostřednictvím libovolného klienta zprostředkovatele prostředků Azure, včetně knihovny Batch Management .NET, a prostřednictvím [Správce prostředků Azure][resman_overview]. Informace o používání služby Azure AD s knihovnou Batch Management .NET najdete v [tématu Použití služby Azure Active Directory k ověření dávkových řešení](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Ukázkový projekt na GitHubu

Chcete-li zobrazit batch management .NET v akci, podívejte se na ukázkový projekt [AccountManagement][acct_mgmt_sample] na GitHubu. Ukázková aplikace AccountManagement demonstruje následující operace:

1. Získejte token zabezpečení z Azure AD pomocí [ADAL][aad_adal]. Pokud uživatel ještě není přihlášen, zobrazí se výzva k zadání přihlašovacích údajů azure.
2. S token zabezpečení získané z Azure AD, vytvořte [SubscriptionClient][resman_subclient] dotaz Azure pro seznam předplatných přidružených k účtu. Uživatel může vybrat předplatné ze seznamu, pokud obsahuje více než jedno předplatné.
3. Získejte přihlašovací údaje přidružené k vybranému předplatnému.
4. Vytvořte objekt [ResourceManagementClient][resman_client] pomocí pověření.
5. K vytvoření skupiny prostředků použijte objekt [ResourceManagementClient.][resman_client]
6. Pomocí objektu [BatchManagementClient][net_mgmt_client] proveďte několik operací dávkového účtu:
   * Vytvořte dávkový účet v nové skupině prostředků.
   * Získejte nově vytvořený účet ze služby Batch.
   * Vytiskněte klíče účtu pro nový účet.
   * Znovu vygenerovat nový primární klíč pro účet.
   * Vytiskněte informace o kvótě pro účet.
   * Vytiskněte informace o kvótě pro předplatné.
   * Vytisknout všechny účty v rámci předplatného.
   * Odstranit nově vytvořený účet.
7. Odstraňte skupinu prostředků.

Před odstraněním nově vytvořeného účtu batch a skupiny prostředků je můžete zobrazit na [webu Azure Portal][azure_portal]:

Chcete-li ukázkovou aplikaci úspěšně spustit, musíte ji nejprve zaregistrovat u svého klienta Azure AD na webu Azure Portal a udělit oprávnění k rozhraní API Azure Resource Manager. Postupujte podle pokynů uvedených v [řešeních ověřování dávek se službou Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je služba Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrace aplikací s Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
