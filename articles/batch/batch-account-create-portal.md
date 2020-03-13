---
title: Vytvoření účtu v Azure Portal-Azure Batch | Microsoft Docs
description: Naučte se vytvořit účet Azure Batch na webu Azure Portal, abyste mohli spouštět velké paralelní úlohy v cloudu.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3723631609a04f6d12abcaac1f9d7733bf3caa01
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247641"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Vytvoření účtu Batch pomocí webu Azure Portal

Naučte se, jak vytvořit účet Azure Batch v [Azure Portal][azure_portal]a jak zvolit vlastnosti účtu, které odpovídají vašemu výpočetnímu scénáři. Zjistěte, kde najít důležité vlastnosti účtu, jako jsou přístupové klíče a adresy URL účtu.

Informace o scénářích a účtech Batch najdete v [přehledu funkcí](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Přihlaste se k webu [Azure Portal][azure_portal].

1. Vyberte **Vytvořit prostředek** > **Compute** > **Služba Batch**.

    ![Batch na webu Marketplace][marketplace_portal]

1. Zadejte nastavení **Nový účet Batch**. Viz následující podrobnosti.

    ![Vytvoření účtu Batch][account_portal]

    a. **Předplatné**: Předplatné, ve kterém chcete účet Batch vytvořit. Pokud máte jenom jedno předplatné, bude ve výchozím nastavení vybrané.

    b. **Skupina prostředků**: Vyberte existující skupinu prostředků vašeho nového účtu Batch, popřípadě si vytvořte novou.

    c. **Název účtu:** Název, který zvolíte, musí být jedinečný v oblasti Azure, ve které se účet vytvoří (viz **Umístění** níže). Název účtu smí obsahovat jenom malá písmena a číslice a musí mít 3 až 24 znaků.

    d. **Umístění**: Oblast Azure, ve které chcete účet Batch vytvořit. Jako možnosti se zobrazí jenom oblasti, které podporuje vaše předplatné a skupina prostředků.

    e. **Účet úložiště**: volitelný účet Azure Storage, který přidružíte k účtu Batch. Pro zajištění nejlepšího výkonu se doporučuje účet úložiště pro obecné účely v2. Všechny možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](batch-api-basics.md#azure-storage-account). Na portálu vyberte existující účet úložiště nebo vytvořte nový.

      ![Vytvořit účet úložiště][storage_account]

    f. **Režim přidělování fondů**: na kartě **Upřesnit** nastavení můžete nastavit režim přidělování fondů jako předplatné **služby Batch** nebo **uživatele**. Ve většině scénářů přijměte výchozí **službu Batch**.

      ![Režim přidělování fondu Batch][pool_allocation]

1. Výběrem možnosti **Vytvořit** vytvořte účet.

## <a name="view-batch-account-properties"></a>Zobrazení vlastností účtu Batch

Po vytvoření účtu ho vyberte pro přístup k jeho nastavením a vlastnostem. Všechna nastavení a vlastnosti účtu jsou přístupná pomocí nabídky vlevo.

![Stránka účtu Batch na webu Azure Portal][account_blade]

* **Název účtu Batch, URL a klíče**: K vývoji aplikace pomocí [rozhraní API služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development) potřebujete URL a klíč účtu pro přístup k prostředkům služby Batch. (Batch podporuje také ověřování pomocí Azure Active Directory.)

    Pokud chcete zobrazit informace pro přístup k účtu Batch, vyberte **Klíče**.

    ![Klíče účtu Batch na webu Azure Portal][account_keys]

* Pokud chcete zobrazit název a klíče účtu úložiště přidruženého k účtu Batch, vyberte **Účet úložiště**.

* Pokud chcete zobrazit kvóty prostředků, které platí pro účet Batch, vyberte **Kvóty**. Podrobnosti najdete v článku o [kvótách a omezeních služby Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Další konfigurace pro režim předplatného uživatele

Pokud zvolíte možnost vytvořit účet Batch v režimu předplatného uživatele, proveďte před vytvořením tohoto účtu následující kroky.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Povolení přístupu k předplatnému pro Azure Batch (jednorázová operace)

Při vytváření prvního účtu Batch v režimu předplatného uživatele musíte zaregistrovat předplatné ve službě Batch. (Pokud jste tento postup již provedli, přejděte k další části.)

1. Přihlaste se k webu [Azure Portal][azure_portal].

1. Vyberte **Všechny služby** > **Předplatná** a vyberte předplatné, které chcete pro účet Batch použít.

1. Na stránce **Předplatné** vyberte **Poskytovatelé prostředků** a vyhledejte **Microsoft.Batch**. Zkontrolujte, že je v předplatném zaregistrovaný poskytovatel prostředků **Microsoft.Batch**. Pokud není zaregistrovaný, vyberte odkaz **Zaregistrovat**.

    ![Registrace poskytovatele Microsoft.Batch][register_provider]

1. Na stránce **předplatné** vyberte **řízení přístupu (IAM)**  > **přiřazení rolí** > **Přidat přiřazení role**.

    ![Řízení přístupu pro předplatné][subscription_access]

1. Na stránce **Přidat přiřazení role** vyberte roli **Přispěvatel** a vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch.

1. Jakmile najdete rozhraní API služby Batch, vyberte ho a pak vyberte **Uložit**.

    ![Přidání oprávnění pro službu Batch][add_permission]

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

V režimu předplatného uživatele je vyžadován trezor klíčů Azure, který patří do stejné skupiny prostředků jako účet Batch, který se má vytvořit. Ověřte, že se skupina prostředků nachází v oblasti, kde je služba Batch [k dispozici](https://azure.microsoft.com/regions/services/) a která podporuje vaše předplatné.

1. V [Azure Portal][azure_portal]vyberte **nové** > **zabezpečení** > **Key Vault**.

1. Na stránce **Vytvořit trezor klíčů** zadejte název pro trezor klíčů a vytvořte skupinu prostředků v oblasti, kterou chcete použít pro účet Batch. Pro zbývající nastavení ponechte výchozí hodnoty a pak vyberte **Vytvořit**.

Při vytváření účtu Batch v režimu předplatného uživatele použijte skupinu prostředků trezoru klíčů. Jako režim přidělování fondů zadejte **předplatné uživatele** , vyberte Trezor klíčů a zaškrtnutím políčka udělte přístup k trezoru klíčů Azure Batch. 

Pokud dáváte přednost ručnímu udělení přístupu k trezoru klíčů, přejděte do části **zásady přístupu** trezoru klíčů a vyberte **Přidat zásady přístupu** a vyhledejte **Microsoft Azure Batch**. Po výběru budete muset nakonfigurovat **tajná oprávnění** pomocí rozevírací nabídky. Azure Batch musí být udělené minimální oprávnění **získat**, **Zobrazit**, **nastavit**a **Odstranit** .

![Tajná oprávnění pro Azure Batch](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> Ujistěte se, že jsou zaškrtnutá políčka **Virtual Machines Azure pro nasazení** a **Azure Resource Manager pro nasazení šablony** v části **zásady přístupu** pro prostředek propojeného **Key Vault** .
> 
> ![povinné zásady přístupu Key Vault](./media/batch-account-create-portal/key-vault-access-policy.png) to není povinné při vytváření účtu Batch v Azure Portal. Ve výchozím nastavení je vybraná možnost.



### <a name="configure-subscription-quotas"></a>Konfigurace kvót předplatného

Ve výchozím nastavení nejsou pro účty Batch předplatného uživatele nastaveny základní kvóty. Základní kvóty je nutné nastavit ručně, protože standardní kvóty Standard Batch se nevztahují na účty v režimu předplatného uživatele.

1. V [Azure Portal][azure_portal]vyberte účet Batch v režimu předplatného uživatele, abyste zobrazili jeho nastavení a vlastnosti.

1. V nabídce vlevo vyberte **kvóty** pro zobrazení a konfiguraci základních kvót přidružených k účtu Batch.

Další informace o hlavních kvótách režimu předplatného uživatele najdete v tématu [kvóty a omezení služby Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Další možnosti správy účtu Batch

K vytváření a správě účtů Batch můžete kromě webu Azure Portal používat také následující nástroje:

* [Rutiny PowerShellu pro účty Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Knihovna Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Další kroky

* Další informace o koncepcích a funkcích služby Batch najdete v článku [Přehled funkcí Batch](batch-api-basics.md). Článek popisuje primární prostředky služby Batch, například fondy, výpočetní uzly a úlohy, a nabízí přehled funkcí služby pro velké výpočetní úlohy.
* Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](quick-run-dotnet.md) nebo [Pythonu](quick-run-python.md). Tyto rychlé starty vás provedou ukázkovou aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a představí vám použití služby Azure Storage k přípravě a načítání souborů úloh.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
