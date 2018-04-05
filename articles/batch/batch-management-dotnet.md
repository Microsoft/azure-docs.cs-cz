---
title: Správa prostředků účtu Batch pomocí klientské knihovny pro platformu .NET – Azure | Microsoft Docs
description: Vytvořit, odstranit a upravit prostředků účtu Azure Batch pomocí knihovny Batch Management .NET.
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
ms.openlocfilehash: 7f2d0b261a991dc02b670488c14392697ea984ec
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Spravovat účty Batch a kvóty pomocí klientské knihovny správy Batch pro .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Knihovna Batch Management .NET](batch-management-dotnet.md)
> 
> 

Můžete snížit údržby režie v aplikacích Azure Batch pomocí [rozhraní Batch Management .NET] [ api_mgmt_net] knihovna k automatizaci vytváření účtu Batch, odstranění, správu klíčů a kvóty zjišťování.

* **Vytvářet a odstraňovat účty Batch** v libovolné oblasti. Pokud například jako nezávislý dodavatel softwaru (ISV) z nich poskytovat služby pro klienty ve kterých každý je přiřazen samostatný účet Batch pro účely fakturace, můžete přidat možnosti vytváření a odstraňování účtu na portál zákazníka.
* **Získat a obnovit klíče účtu** prostřednictvím kódu programu pro všechny účty Batch. To vám může pomoct souladu se zásadami zabezpečení, které vynucují pravidelné výměny nebo vypršení platnosti klíče účtu. Pokud máte několik účty Batch v různých oblastech Azure, automatizaci procesu tato změna se zvyšuje efektivita vaše řešení.
* **Zkontrolujte účet kvóty** a určení, které účty Batch mají jaké limity průběhu zkušební verze a chyby. Kontrolou kvóty vašeho účtu před spuštěním úlohy vytváření fondů nebo přidání výpočetní uzly, můžete upravit proaktivně kde nebo když tyto výpočetní prostředky jsou vytvořeny. Můžete určit, které účty vyžadují, že kvóta zvyšuje před přiděluje další prostředky v těchto účtů.
* **Kombinování funkcí jinými službami Azure** pro správu plnohodnotné prostředí – pomocí rozhraní Batch Management .NET [Azure Active Directory][aad_about]a [Azure Správce prostředků] [ resman_overview] společně ve stejné aplikaci. Pomocí těchto funkcí a jejich rozhraní API mohou poskytnout prostředí hladký ověřování, umožňuje vytvářet a odstraňovat skupiny prostředků a možnosti, které jsou popsané výše začátku do konce řešení pro správu.

> [!NOTE]
> Když tento článek se zaměřuje na programové správy účtů Batch, klíče a kvóty, je mnoho tyto aktivity provést pomocí [portál Azure][azure_portal]. Další informace najdete v tématu [vytvoření účtu Azure Batch pomocí portálu Azure](batch-account-create-portal.md) a [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Vytvářet a odstraňovat účty Batch
Jak je uvedeno, jeden z primární funkce rozhraní API pro správu Batch je vytvářet a odstraňovat účty Batch v oblasti Azure. Chcete-li to provést, použijte [BatchManagementClient.Account.CreateAsync] [ net_create] a [DeleteAsync][net_delete], nebo jejich synchronní protějšky.

Následující fragment kódu vytvoří účet, nově vytvořený účet získá od služby Batch a odstraní ji. V tento fragment kódu a jiné v tomto článku `batchManagementClient` je plně inicializovaném instanci [BatchManagementClient][net_mgmt_client].

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
> Vyžadují aplikace, které používají knihovnu rozhraní Batch Management .NET a její třída BatchManagementClient **Správce služeb** nebo **spolusprávce** přístupu k odběru, který vlastní účet Batch, který chcete spravovat. Další informace najdete v tématu [Azure Active Directory](#azure-active-directory) části a [AccountManagement] [ acct_mgmt_sample] ukázka kódu.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Získat a obnovit klíče účtu
Získat účet primární a sekundární klíče z jakékoli účtu Batch v rámci vašeho předplatného pomocí [ListKeysAsync][net_list_keys]. Tyto klíče můžete vygenerovat pomocí [RegenerateKeyAsync][net_regenerate_keys].

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
> Zjednodušená připojení pracovního postupu můžete vytvořit pro správu aplikací. Nejdřív získat klíč účtu pro účet Batch, které chcete spravovat pomocí [ListKeysAsync][net_list_keys]. Poté použijte tento klíč při inicializaci knihovny Batch .NET [BatchSharedKeyCredentials] [ net_sharedkeycred] třídy, která se používá při inicializaci [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Zkontrolujte předplatné a kvóty účtu Batch
Předplatná Azure a jednotlivé služby Azure, jako jsou všechny služby Batch mají výchozí kvóty, které omezí počet určitých entit v nich. Výchozí kvóty pro předplatná Azure, najdete v části [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md). Výchozí kvóty služby Batch, najdete v části [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md). Pomocí knihovny Batch Management .NET můžete zkontrolovat těchto kvót ve svých aplikacích. Můžete provést rozhodnutí o přidělení, než přidáte účty nebo výpočetní prostředky, jako jsou fondy a výpočetní uzly.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Zkontrolujte předplatné Azure pro kvóty účtu Batch
Před vytvořením účtu Batch v oblasti, můžete zkontrolovat vašeho předplatného Azure, pokud chcete zobrazit, zda budete moci přidat účet v této oblasti.

V následujícím fragmentu kódu, nejprve používáme [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] získat kolekce všechny účty Batch, které jsou v rámci předplatného. Po získání jsme tuto kolekci jsme určit, kolik účty jsou v cílové oblasti. Využijeme [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] získat kvóta účtu Batch a určit, kolik účty (pokud existuje) můžete vytvořit v této oblasti.

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

Ve výše uvedeném fragmentu `creds` je instance [TokenCloudCredentials][azure_tokencreds]. Příklad vytvoření tohoto objektu najdete v sekci [AccountManagement] [ acct_mgmt_sample] ukázka kódu na Githubu.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Zkontrolujte účet Batch u výpočetních prostředků kvóty
Před zvýšením výpočetní prostředky ve vašem řešení Batch, můžete zkontrolovat, aby prostředky, které chcete přidělit nesmí překročit kvóty účtu. V následujícím fragmentu kódu, jsme tisknout informace o kvótě pro dávkový účet s názvem `mybatchaccount`. Ve vaší vlastní aplikaci můžete tyto informace použít k určení, zda účet dokáže zpracovat další prostředky, který se má vytvořit.

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
> Existuje výchozí kvóty pro předplatná Azure a služby, řada těchto mezních hodnot mohou být vyvolány po vydání požadavku v [portál Azure][azure_portal]. Například v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) pokyny pro zvýšení vaší kvóty účtu Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Používat Azure AD s Batch Management .NET

Knihovně rozhraní Batch Management .NET je klient poskytovatel prostředků Azure a je použít v kombinaci s [Azure Resource Manager] [ resman_overview] ke správě prostředků účtu prostřednictvím kódu programu. Azure AD je vyžadovaný k ověření požadavky prostřednictvím všechny klienty poskytovatele prostředků Azure, včetně knihovny Batch Management .NET a prostřednictvím [Azure Resource Manager][resman_overview]. Informace o používání služby Azure AD pomocí knihovny Batch Management .NET najdete v tématu [pomocí Azure Active Directory k ověření řešení Batch](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Ukázkový projekt na Githubu

Pokud chcete zobrazit rozhraní Batch Management .NET v akci, podívejte se [AccountManagment] [ acct_mgmt_sample] ukázkového projektu na Githubu. Ukázkovou aplikaci AccountManagment ukazuje následující operace:

1. Získat token zabezpečení ze služby Azure AD pomocí [ADAL][aad_adal]. Pokud již není přihlášený uživatel, bude vyzván k zadání přihlašovacích údajů Azure.
2. Pomocí tokenu zabezpečení získaného z Azure AD, vytvářet [SubscriptionClient] [ resman_subclient] dotazu Azure seznam předplatných přidružené k účtu. Uživatel může vybrat odběr, v seznamu, pokud obsahuje více než jedno předplatné.
3. Získání přihlašovacích údajů, které jsou přidružené k vybranému předplatnému.
4. Vytvoření [ResourceManagementClient] [ resman_client] objekt pomocí přihlašovacích údajů.
5. Použití [ResourceManagementClient] [ resman_client] objekt, který chcete vytvořit skupinu prostředků.
6. Použití [BatchManagementClient] [ net_mgmt_client] objekt pro provedení několik operací účtu Batch:
   * Vytvořte účet Batch do nové skupiny prostředků.
   * Nově vytvořený účet získáte ze služby Batch.
   * Tisk klíče účtu pro nový účet.
   * Znovu vygenerujte nový primární klíč pro účet.
   * Tisk – informace o kvótě pro účet.
   * Tisk – informace o kvótě pro předplatné.
   * Tisk – všechny účty v rámci předplatného.
   * Nově vytvořený účet odstraňte.
7. Odstraňte skupinu prostředků.

Před odstraněním nově vytvořenou skupinu účtů a prostředků Batch, lze je zobrazit v [portál Azure][azure_portal]:

K úspěšnému spuštění ukázkové aplikace, musíte nejprve zaregistrovat ji pomocí vašeho klienta Azure AD na portálu Azure a udělte oprávnění na rozhraní API služby Azure Resource Manager. Postupujte podle kroků uvedených v [řešení pro správu Batch ověření pomocí služby Active Directory](batch-aad-auth-management.md).


[aad_about]: ../active-directory/active-directory-whatis.md "Co je Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénáře ověřování pro Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrace aplikací s Azure Active Directory"
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
