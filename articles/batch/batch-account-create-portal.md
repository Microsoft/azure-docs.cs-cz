---
title: Vytvoření účtu v Azure Portal
description: Naučte se vytvořit účet Azure Batch na portálu Azure, abyste mohli spouštět velké paralelní úlohy v cloudu.
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711336"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Vytvoření účtu Batch pomocí webu Azure Portal

V tomto tématu se dozvíte, jak vytvořit účet Azure Batch v [Azure Portal](https://portal.azure.com), a to výběrem vlastností účtu, které odpovídají vašemu výpočetnímu scénáři. Naučíte se také, kde najít důležité vlastnosti účtu, jako jsou přístupové klíče a adresy URL účtu.

Základní informace o účtech a scénářích služby Batch najdete v tématu [pracovní postupy a prostředky služby Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit prostředek**a pak vyberte **výpočty** a **Služba Batch**.

1. Zadejte nastavení **Nový účet Batch**. Viz následující podrobnosti.

    ![Vytvoření účtu Batch][account_portal]

    a. **Předplatné**: Předplatné, ve kterém chcete účet Batch vytvořit. Pokud máte jenom jedno předplatné, bude ve výchozím nastavení vybrané.

    b. **Skupina prostředků**: Vyberte existující skupinu prostředků vašeho nového účtu Batch, popřípadě si vytvořte novou.

    c. **Název účtu:** Název, který zvolíte, musí být jedinečný v oblasti Azure, ve které se účet vytvoří (viz **Umístění** níže). Název účtu smí obsahovat jenom malá písmena a číslice a musí mít 3 až 24 znaků.

    d. **Umístění**: Oblast Azure, ve které chcete účet Batch vytvořit. Jako možnosti se zobrazí jenom oblasti, které podporuje vaše předplatné a skupina prostředků.

    e. **Účet úložiště**: volitelný účet Azure Storage, který přidružíte k účtu Batch. Pro zajištění nejlepšího výkonu se doporučuje účet úložiště pro obecné účely v2. Všechny možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](accounts.md#azure-storage-accounts). Na portálu vyberte existující účet úložiště nebo vytvořte nový.

      ![vytvořit účet úložiště][storage_account]

    f. **Režim přidělování fondů**: na kartě **Upřesnit** nastavení můžete nastavit režim přidělování fondů jako předplatné **služby Batch** nebo **uživatele**. Ve většině scénářů přijměte výchozí **službu Batch**.

      ![Režim přidělování fondu Batch][pool_allocation]

1. Výběrem možnosti **Vytvořit** vytvořte účet.

## <a name="view-batch-account-properties"></a>Zobrazení vlastností účtu Batch

Po vytvoření účtu ho vyberte pro přístup k jeho nastavením a vlastnostem. Všechna nastavení a vlastnosti účtu jsou přístupná pomocí nabídky vlevo.

> [!NOTE]
> Název účtu Batch je jeho ID a nedá se změnit. Pokud potřebujete změnit název účtu Batch, budete muset účet odstranit a vytvořit nový s požadovaným názvem.

![Stránka účtu Batch na webu Azure Portal][account_blade]

* **Název účtu Batch, URL a klíče**: K vývoji aplikace pomocí [rozhraní API služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development) potřebujete URL a klíč účtu pro přístup k prostředkům služby Batch. (Batch podporuje také ověřování pomocí Azure Active Directory.)

    Pokud chcete zobrazit informace pro přístup k účtu Batch, vyberte **Klíče**.

    ![Klíče účtu Batch na webu Azure Portal][account_keys]

* Pokud chcete zobrazit název a klíče účtu úložiště přidruženého k účtu Batch, vyberte **Účet úložiště**.

* Pokud chcete zobrazit kvóty prostředků, které platí pro účet Batch, vyberte **Kvóty**. Podrobnosti najdete v článku o [kvótách a omezeních služby Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Další konfigurace pro režim předplatného uživatele

Pokud zvolíte možnost vytvořit účet Batch v režimu předplatného uživatele, proveďte před vytvořením tohoto účtu následující kroky.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Povolení přístupu k předplatnému pro Azure Batch (jednorázová operace)

Při vytváření prvního účtu Batch v režimu předplatného uživatele musíte zaregistrovat předplatné ve službě Batch. (Pokud jste to již provedli, přejděte k další části.)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyberte možnost **všechny**  >  **odběry**služeb a vyberte předplatné, které chcete použít pro účet Batch.

1. Na stránce **Předplatné** vyberte **Poskytovatelé prostředků** a vyhledejte **Microsoft.Batch**. Zkontrolujte, že je v předplatném zaregistrovaný poskytovatel prostředků **Microsoft.Batch**. Pokud není zaregistrovaný, vyberte odkaz **Zaregistrovat**.

    ![Registrace poskytovatele Microsoft.Batch][register_provider]

1. Na stránce **předplatné** vyberte přiřazení rolí **řízení přístupu (IAM)**  >  **Role assignments**  >  **Přidat přiřazení role**.

    ![Řízení přístupu pro předplatné][subscription_access]

1. Na stránce **Přidat přiřazení role** vyberte roli **Přispěvatel** nebo **vlastník** a pak vyhledejte rozhraní API služby Batch. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch.

1. Jakmile najdete rozhraní API služby Batch, vyberte ho a pak vyberte **Uložit**.

    ![Přidání oprávnění pro službu Batch][add_permission]

### <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

V režimu předplatného uživatele se vyžaduje [Azure Key Vault](../key-vault/general/overview.md) . Key Vault musí být ve stejném předplatném a oblasti jako účet Batch, který se má vytvořit. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Nový** > **Zabezpečení** > **Key Vault**.

1. Na stránce **vytvořit Key Vault** zadejte název Key Vault a vytvořte skupinu prostředků v oblasti, kterou chcete pro účet Batch. Pro zbývající nastavení ponechte výchozí hodnoty a pak vyberte **Vytvořit**.

Při vytváření účtu Batch v režimu předplatného uživatele zadejte **předplatné uživatele** jako režim přidělování fondů, vyberte Key Vault a zaškrtnutím políčka udělte Azure Batch přístup k Key Vault.

Pokud dáváte přednost ručnímu udělení přístupu Key Vault, přejděte do části **zásady přístupu** Key Vault, vyberte **Přidat zásady přístupu** a vyhledejte **Microsoft Azure Batch**. Po výběru budete muset nakonfigurovat **tajná oprávnění** pomocí rozevírací nabídky. Azure Batch musí být udělené minimální oprávnění **získat**, **Zobrazit**, **nastavit**a **Odstranit** .

![Tajná oprávnění pro Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Ujistěte se, že jsou zaškrtnutá políčka **Virtual Machines Azure pro nasazení** a **Azure Resource Manager pro nasazení šablony** v části **zásady přístupu** pro prostředek propojeného **Key Vault** .
>
> ![Povinné zásady přístupu Key Vault](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Konfigurace kvót předplatného

Pro účty Batch pro předplatné uživatele je nutné nastavit základní kvóty ručně. U účtů v režimu předplatného uživatele se nevztahují na Batch úrovně Standard základní kvóty.

1. V [Azure Portal](https://portal.azure.com)vyberte účet Batch v režimu předplatného uživatele, abyste zobrazili jeho nastavení a vlastnosti.
1. V nabídce vlevo vyberte **kvóty** pro zobrazení a konfiguraci základních kvót přidružených k účtu Batch.

Další informace o hlavních kvótách režimu předplatného uživatele najdete v tématu [kvóty a limity služby Batch](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Další možnosti správy účtu Batch

K vytváření a správě účtů Batch můžete kromě webu Azure Portal používat také následující nástroje:

* [Rutiny PowerShellu pro účty Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Knihovna Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
* Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](quick-run-dotnet.md) nebo [Pythonu](quick-run-python.md). Tyto rychlé starty vás provedou ukázkovou aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a představí vám použití služby Azure Storage k přípravě a načítání souborů úloh.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
