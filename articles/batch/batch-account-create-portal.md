---
title: Vytvoření účtu Batch na webu Azure Portal | Dokumentace Microsoftu
description: Naučte se vytvořit účet Azure Batch na webu Azure Portal, abyste mohli spouštět velké paralelní úlohy v cloudu.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b1c8c981c0b1c3b599d2dd737b680390a52888eb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Vytvoření účtu Batch pomocí webu Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Knihovna Batch Management .NET](batch-management-dotnet.md)
>
>

Naučte se vytvořit účet Azure Batch na webu [Azure Portal][azure_portal] a zvolit vlastnosti účtu odpovídající výpočetnímu scénáři. Zjistěte, kde najít důležité vlastnosti účtu, jako jsou přístupové klíče a adresy URL účtu.

Informace o scénářích a účtech Batch najdete v [přehledu funkcí](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Přihlaste se na web [Azure Portal][azure_portal].
2. Klikněte na **Vytvořit prostředek** a na Marketplace vyhledejte **Služba Batch**.

    ![Batch na webu Marketplace][marketplace_portal]
3. Vyberte **Služba Batch**, klikněte na **Vytvořit** a zadejte nastavení **Nový účet Batch**. Viz následující podrobnosti.

    ![Vytvoření účtu Batch][account_portal]

    a. **Název účtu:** Název, který zvolíte, musí být jedinečný v oblasti Azure, ve které se účet vytvoří (viz **Umístění** níže). Název účtu může obsahovat jenom malá písmena nebo čísla a musí být dlouhý 3 až 24 znaků.

    b. **Předplatné**: Předplatné, ve kterém chcete účet Batch vytvořit. Pokud máte jenom jedno předplatné, bude ve výchozím nastavení vybrané.

    c. **Režim přidělování fondů:** Pokud se zobrazí toto nastavení, přijměte výchozí hodnotu **Služba Batch**.

    c. **Skupina prostředků**: Vyberte existující skupinu prostředků vašeho nového účtu Batch, popřípadě si vytvořte novou.

    d. **Umístění**: Oblast Azure, ve které chcete účet Batch vytvořit. Jako možnosti se zobrazí jenom oblasti, které podporuje vaše předplatné a skupina prostředků.

    e. **Účet úložiště** (volitelné): Účet Azure Storage, který přidružíte k účtu Batch. Toto nastavení se doporučuje pro většinu účtů Batch. Další informace najdete v části [Propojený účet Azure Storage](#linked-azure-storage-account) dále v tomto článku.

4. Kliknutím na **Vytvořit** vytvořte účet.



## <a name="view-batch-account-properties"></a>Zobrazení vlastností účtu Batch
Po vytvoření účtu na něj klikněte pro přístup k jeho nastavením a vlastnostem. Všechna nastavení a vlastnosti účtu jsou přístupná pomocí nabídky vlevo.

![Stránka účtu Batch na webu Azure Portal][account_blade]

* **Adresa URL účtu Batch:** Při vývoji aplikace pomocí [rozhraní API služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development) potřebujete pro přístup k prostředkům Batch adresu URL účtu. Adresa URL účtu Batch má následující formát:

    `https://<account_name>.<region>.batch.azure.com`

![Adresa URL účtu Batch na portálu][account_url]

* **Přístupové klíče:** Pro ověření přístupu k účtu Batch z vaší aplikace můžete použít přístupový klíč účtu. (Batch podporuje také ověřování pomocí Azure Active Directory.)

    Pokud chcete zobrazit ne znovu vytvořit přístupové klíče, vyberte **Klíče**.

    ![Klíče účtu Batch na webu Azure Portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Propojený účet Azure Storage

S účtem Batch můžete propojit účet Azure Storage, což je užitečné pro řadu scénářů. Funkce [balíčků aplikací](batch-application-packages.md) služby Batch využívá úložiště objektů blob v Azure, stejně jako knihovna [.NET Batch File Conventions](batch-task-output.md). Tyto volitelné funkce vám pomohou při nasazení aplikací spouštěných vašimi úkoly Batch a při zachování jimi vytvářených dat.

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](batch-api-basics.md#azure-storage-account).

![Vytváření účtu úložiště][storage_account]

> [!NOTE]
> Při obnovování přístupových klíčů v propojeném účtu úložiště buďte velmi opatrní. Obnovte vždycky jenom jeden klíč účtu úložiště a na stránce propojeného účtu úložiště klikněte na **Synchronizovat klíče**. Počkejte 5 minut, aby se klíče rozšířily do výpočetních uzlů ve fondech, a potom v případě potřeby znovu vygenerujte a synchronizujte další klíč. Pokud byste obnovili (znovu vygenerovali) oba klíče najednou, výpočetní uzly by nedokázaly synchronizovat ani jeden klíč a vy byste ztratili přístup k účtu úložiště.
>
>

![Obnovování klíčů účtu úložiště][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>Další konfigurace pro režim předplatného uživatele

Pokud zvolíte možnost vytvořit účet Batch v režimu předplatného uživatele, proveďte před vytvořením tohoto účtu následující kroky.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Povolení přístupu k předplatnému pro Azure Batch (jednorázová operace)
Při vytváření prvního účtu Batch v režimu předplatného uživatele musíte zaregistrovat předplatné ve službě Batch. (Pokud jste tento postup již provedli, přejděte k další části.)

1. Přihlaste se na web [Azure Portal][azure_portal].

2. Klikněte na **Další služby** > **Předplatné** a klikněte na předplatné, které chcete pro účet Batch použít.

3. Na stránce **Předplatné** klikněte na **Řízení přístupu (IAM)** > **Přidat**.

    ![Řízení přístupu pro předplatné][subscription_access]

4. Na stránce **Přidejte oprávnění** vyberte roli **Přispěvatel** a vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch. 

5. Jakmile najdete rozhraní API služby Batch, vyberte ho a klikněte na **Uložit**.

    ![Přidání oprávnění pro službu Batch][add_permission]

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů
V režimu předplatného uživatele je vyžadován trezor klíčů Azure, který patří do stejné skupiny prostředků jako účet Batch, který se má vytvořit. Ověřte, že se skupina prostředků nachází v oblasti, kde je služba Batch [k dispozici](https://azure.microsoft.com/regions/services/) a která podporuje vaše předplatné.

1. Na webu [Azure Portal][azure_portal] klikněte na **Nový** > **Zabezpečení + identita** > **Trezor klíčů**.

2. Na stránce **Vytvořit trezor klíčů** zadejte název pro trezor klíčů a vytvořte skupinu prostředků v oblasti, kterou chcete použít pro účet Batch. Pro zbývající nastavení ponechejte výchozí hodnoty a pak klikněte na **Vytvořit**.




## <a name="batch-service-quotas-and-limits"></a>Kvóty a omezení služby Batch
Podobně jako pro předplatné a další služby Azure, i pro účty Batch platí určité [kvóty a omezení](batch-quota-limit.md). Aktuální kvóty pro účet Batch se zobrazí v části **Kvóty**.

![Kvóty účtu Batch na webu Azure Portal][quotas]



Mnohé z těchto kvót je navíc možné zvýšit požadavkem na bezplatnou podporu odeslaným pomocí webu Azure Portal. Podrobnosti o vyžádání zvýšení kvóty viz [Kvóty a omezení pro službu Azure Batch](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Další možnosti správy účtu Batch
Kromě webu Azure Portal můžete účty Batch vytvářet a spravovat následujícími způsoby:

* [Rutiny PowerShellu pro účty Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Knihovna Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Další kroky
* Další informace o koncepcích a funkcích služby Batch najdete v článku [Přehled funkcí Batch](batch-api-basics.md). Článek popisuje primární prostředky služby Batch, například fondy, výpočetní uzly a úlohy, a nabízí přehled funkcí služby pro velké výpočetní úlohy.
* Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](batch-dotnet-get-started.md) nebo [Pythonu](batch-python-tutorial.md). Tyto úvodní články vás provedou funkční aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a představí vám použití služby Azure Storage k přípravě a načítání souborů úloh.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Obnovování klíčů účtu úložiště"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

