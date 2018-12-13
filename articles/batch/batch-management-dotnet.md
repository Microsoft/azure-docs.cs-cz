---
title: Správa prostředků účtu služby Batch pomocí klientské knihovny pro .NET – Azure | Dokumentace Microsoftu
description: Vytvářet, odstraňovat a upravovat prostředků účtu služby Azure Batch pomocí knihovny Batch Management .NET.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd511a8a9d17bf79f1237bbfe2dc2ddbc5ec1b2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867422"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Správa účtů služby Batch a kvót pomocí klientské knihovny Batch Management pro rozhraní .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Knihovna Batch Management .NET](batch-management-dotnet.md)
> 
> 

Můžete snížit údržby režii v aplikacích Azure Batch s využitím [rozhraní Batch Management .NET] [ api_mgmt_net] knihovny k automatizaci vytváření účtu Batch, odstranění, správu klíčů a kvóty zjišťování.

* **Vytvářet a odstraňovat účty Batch** v libovolné oblasti. Pokud například jako nezávislý výrobce softwaru (ISV) z nich poskytovat službu pro klienty, v nichž každý je přiřazena samostatný účet Batch pro účely fakturace, můžete přidat možnosti vytváření a odstraňování účtu na zákaznický portál.
* **Načíst a znovu vygenerovat klíče účtu** prostřednictvím kódu programu pro všechny účty Batch. To může pomoct zajistit soulad se zásadami zabezpečení, které vynucují při přechodu myší pravidelných nebo vypršení platnosti klíče účtu. Až budete mít několik účtů služby Batch v různých oblastech Azure, automatizace procesu tato změna zvyšuje efektivitu vašeho řešení.
* **Zkontrolujte kvóty účtu** a najisto zkušební verze a chyba stanovení, které účty Batch mají jaká omezení. Kontrolou vaší kvóty účtu před spuštěním úlohy vytváření fondů nebo přidávání výpočetních uzlů, kde můžete proaktivně upravit nebo když tyto výpočetní prostředky vytvoří. Můžete určit, které účty vyžadují, že se kvóta zvýší před přiděluje další prostředky v těchto účtů.
* **Kombinování funkcí dalších služeb Azure** plně funkční management možnosti--pomocí rozhraní Batch Management .NET [Azure Active Directory][aad_about]a [Azure Resource Manager] [ resman_overview] společně v jedné aplikaci. Pomocí těchto funkcí a jejich rozhraní API můžete poskytovat bezproblémové ověřování, možnost vytvářet a odstraňovat skupiny prostředků a možnosti, které jsou popsány výše začátku do konce řešení pro správu.

> [!NOTE]
> Přestože tento článek se zaměřuje na programové správě účtů Batch, klíčů a kvóty, můžete mnohé z těchto aktivit provádět pomocí [webu Azure portal][azure_portal]. Další informace najdete v tématu [vytvořit účet Azure Batch pomocí webu Azure portal](batch-account-create-portal.md) a [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Vytvářet a odstraňovat účty Batch
Jak už bylo zmíněno, mezi primární funkce rozhraní API pro správu služby Batch je vytvářet a odstraňovat účty Batch v oblasti Azure. Chcete-li tak učinit, použijte [BatchManagementClient.Account.CreateAsync] [ net_create] a [DeleteAsync][net_delete], nebo jejich protějšky synchronní.

Následující fragment kódu vytvoří účet, získá nově vytvořený účet ze služby Batch a potom ji odstraní. V tomto fragmentu kódu a další v tomto článku `batchManagementClient` je plně inicializován instancí [BatchManagementClient][net_mgmt_client].

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
> Vyžadují aplikace, které používají knihovny Batch Management .NET a jeho třída BatchManagementClient **Správce služeb** nebo **spolusprávce** s přístupem k předplatnému, který vlastní služby Batch účet pro správu. Další informace najdete v tématu [Azure Active Directory](#azure-active-directory) oddílu a [AccountManagement] [ acct_mgmt_sample] vzorový kód.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Načíst a znovu vygenerovat klíče účtu
Získat účet primární a sekundární klíče z jakékoli účtu Batch v rámci předplatného pomocí [ListKeysAsync][net_list_keys]. Můžete obnovit tyto klíče s použitím [RegenerateKeyAsync][net_regenerate_keys].

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
> Zjednodušené připojení pracovního postupu můžete vytvořit pro vaše aplikace pro správu. Nejprve získat klíče účtu služby pro účet Batch, kterou chcete spravovat pomocí [ListKeysAsync][net_list_keys]. Potom tento klíč použít při inicializaci knihovny Batch .NET [BatchSharedKeyCredentials] [ net_sharedkeycred] třídu, která se používá při inicializaci [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Zkontrolujte předplatné a kvóty účtu Batch
Předplatná Azure a jednotlivých služeb Azure, jako jsou dávkové všechny mají výchozí kvóty, které omezují počet určité entity v nich. Výchozí kvóty pro předplatná Azure, najdete v části [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md). Výchozí kvóty služby Batch, najdete v části [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md). Pomocí knihovny Batch Management .NET můžete zkontrolovat těchto kvót ve svých aplikacích. To umožňuje rozhodování přidělení předtím, než přidáte účty nebo výpočetní prostředky, jako jsou fondy a výpočetních uzlů.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Zkontrolujte předplatné Azure pro kvóty účtu Batch
Před vytvořením účtu Batch v oblasti, můžete zkontrolovat vaše předplatné Azure, pokud chcete zobrazit, jestli budete moct přidat účet v této oblasti.

Následující fragment kódu nejprve používáme [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] k získání kolekce všechny účty služby Batch, které jsou v rámci předplatného. Po získání jsme tuto kolekci nám určit, kolik účty jsou v cílové oblasti. Potom pomocí [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] získání kvóty účtu Batch a určit, kolik účtů (pokud existuje) je možné vytvořit v této oblasti.

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

V tomto fragmentu kódu výše `creds` je instance [TokenCloudCredentials][azure_tokencreds]. Příklad vytvoření tohoto objektu najdete v tématu [AccountManagement] [ acct_mgmt_sample] ukázku kódu na Githubu.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Zkontrolujte pro výpočetní prostředek kvóty účtu Batch
Před zvýšením výpočetních prostředků ve vašem řešení Batch, můžete zkontrolovat, aby prostředky, které chcete přidělit nesmí překročit kvóty účtu. V následujícím fragmentu kódu jsme tisknout informace o kvótách pro účet Batch s názvem `mybatchaccount`. Ve své aplikaci můžete tyto informace použít k určení, zda účet dokáže zpracovat další prostředky, který se má vytvořit.

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
> I když existují výchozí kvóty pro předplatných a služeb Azure, mnoho z těchto omezení můžete navýšit tak, že vydání požadavku na [webu Azure portal][azure_portal]. Viz například [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) pokyny na zvýšení kvóty vašeho účtu Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Pomocí služby Azure AD službou Batch Management .NET

Knihovna Batch Management .NET je klient poskytovatele prostředků Azure a používá se společně s [Azure Resource Manageru] [ resman_overview] ke správě prostředků účtů prostřednictvím kódu programu. Azure AD je potřeba ověřit požadavky provedené přes všechny klienty poskytovatele prostředků Azure, včetně knihovny Batch Management .NET, prostřednictvím [Azure Resource Manageru][resman_overview]. Informace o používání služby Azure AD pomocí knihovny Batch Management .NET najdete v tématu [pomocí Azure Active Directory k ověření řešení Batch](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Ukázkový projekt na Githubu

K rozhraní Batch Management .NET můžete vidět v akci, podívejte se [AccountManagement] [ acct_mgmt_sample] ukázkového projektu na Githubu. Ukázková aplikace AccountManagement demonstruje následující operace:

1. Získání tokenu zabezpečení ze služby Azure AD s použitím [ADAL][aad_adal]. Pokud již není přihlášený uživatel, zobrazí se výzva k zadání přihlašovacích údajů Azure.
2. S tokenem zabezpečení získaných ze služby Azure AD, vytvořte [SubscriptionClient] [ resman_subclient] dotazu seznam předplatných, které jsou přidružené k účtu Azure. Uživatel lze ze seznamu vyberte předplatné, pokud obsahuje více než jedno předplatné.
3. Získáte přihlašovací údaje přidružené k vybrané předplatné.
4. Vytvoření [ResourceManagementClient] [ resman_client] objektu pomocí přihlašovacích údajů.
5. Použití [ResourceManagementClient] [ resman_client] objekt k vytvoření skupiny prostředků.
6. Použití [BatchManagementClient] [ net_mgmt_client] objekt pro provedení operace několik účtů Batch:
   * Vytvoření účtu Batch v nové skupině prostředků.
   * Nově vytvořený účet získáte ze služby Batch.
   * Tisk klíčů účtu pro nový účet.
   * Znovu vygenerovat nový primární klíč pro účet.
   * Vytiskne informace o kvótách pro účet.
   * Vytiskne informace o kvótách pro předplatné.
   * Vytiskne všechny účty v rámci předplatného.
   * Odstraňte nově vytvořený účet.
7. Odstraníte skupinu prostředků.

Před odstraněním nově vytvořenou skupinu účtů a prostředků služby Batch, můžete zobrazit v [webu Azure portal][azure_portal]:

K úspěšnému spuštění ukázkové aplikace, musí nejdřív zaregistrovat tenanta Azure AD na webu Azure Portal a udělení oprávnění pro rozhraní API Azure Resource Manageru. Postupujte podle kroků uvedených v [řešení pro správu ověřování služby Batch pomocí Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scénáře ověřování pro službu Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrace aplikací s Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
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
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
