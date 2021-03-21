---
title: Vytvoření účtu v Azure Portal
description: Naučte se vytvořit účet Azure Batch na portálu Azure, abyste mohli spouštět velké paralelní úlohy v cloudu.
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703660"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Vytvoření účtu Batch pomocí webu Azure Portal

V tomto tématu se dozvíte, jak vytvořit [účet Azure Batch](accounts.md) v [Azure Portal](https://portal.azure.com), a to výběrem vlastností účtu, které odpovídají vašemu výpočetnímu scénáři. Naučíte se také, kde najít důležité vlastnosti účtu, jako jsou přístupové klíče a adresy URL účtu.

Základní informace o účtech a scénářích služby Batch najdete v tématu [pracovní postupy a prostředky služby Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Z domovské stránky vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **Služba Batch**. Z výsledků vyberte **Služba Batch** a pak vyberte **vytvořit**.

1. Zadejte následující podrobnosti.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Snímek obrazovky s novou obrazovkou účtu Batch":::

    a. **Předplatné**: Předplatné, ve kterém chcete účet Batch vytvořit. Pokud máte jenom jedno předplatné, bude ve výchozím nastavení vybrané.

    b. **Skupina prostředků**: Vyberte existující skupinu prostředků vašeho nového účtu Batch, popřípadě si vytvořte novou.

    c. **Název účtu:** Název, který zvolíte, musí být jedinečný v oblasti Azure, ve které se účet vytvoří (viz **Umístění** níže). Název účtu smí obsahovat jenom malá písmena a číslice a musí mít 3 až 24 znaků.

    d. **Umístění**: Oblast Azure, ve které chcete účet Batch vytvořit. Jako možnosti se zobrazí jenom oblasti, které podporuje vaše předplatné a skupina prostředků.

    e. **Účet úložiště**: volitelný [účet Azure Storage](accounts.md#azure-storage-accounts) , který přidružíte k účtu Batch. Můžete vybrat existující účet úložiště nebo vytvořit nový. Pro zajištění nejlepšího výkonu se doporučuje účet úložiště pro obecné účely v2.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Snímek obrazovky s možnostmi při vytváření účtu úložiště":::

1. V případě potřeby vyberte **Upřesnit** a zadejte **typ identity**, **přístup k veřejné síti** nebo **režim přidělování fondů**. Ve většině scénářů jsou výchozí možnosti přesné.

1. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit** a vytvořte účet.

## <a name="view-batch-account-properties"></a>Zobrazení vlastností účtu Batch

Po vytvoření účtu ho vyberte pro přístup k jeho nastavením a vlastnostem. Všechna nastavení a vlastnosti účtu jsou přístupná pomocí nabídky vlevo.

> [!NOTE]
> Název účtu Batch je jeho ID a nedá se změnit. Pokud potřebujete změnit název účtu Batch, budete muset účet odstranit a vytvořit nový s požadovaným názvem.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Snímek obrazovky se stránkou účtu Batch v Azure Portal.":::

Při vývoji aplikace pomocí [rozhraní API služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development)budete potřebovat adresu URL účtu a klíč pro přístup k prostředkům služby Batch. (Batch také podporuje ověřování Azure Active Directory.) Pokud chcete zobrazit informace o přístupu k účtu Batch, vyberte **klíče**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Snímek obrazovky klíčů účtu Batch v Azure Portal.":::

Pokud chcete zobrazit název a klíče účtu úložiště přidruženého k účtu Batch, vyberte **Účet úložiště**.

Pokud chcete zobrazit [kvóty prostředků](batch-quota-limit.md) , které se vztahují k účtu Batch, vyberte **kvóty**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Další konfigurace pro režim předplatného uživatele

Pokud zvolíte možnost vytvořit účet Batch v režimu předplatného uživatele, proveďte před vytvořením tohoto účtu následující kroky.

> [!IMPORTANT]
> Uživatel, který vytváří účet Batch v režimu předplatného uživatele, musí mít přiřazenou roli přispěvatel nebo vlastník pro předplatné, ve kterém se účet Batch vytvoří.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Povolení přístupu k předplatnému pro Azure Batch (jednorázová operace)

Při vytváření prvního účtu Batch v režimu předplatného uživatele musíte zaregistrovat předplatné ve službě Batch. (Pokud jste to již provedli, přejděte k další části.)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte možnost **všechny**  >  **odběry** služeb a vyberte předplatné, které chcete použít pro účet Batch.

1. Na stránce **Předplatné** vyberte **Poskytovatelé prostředků** a vyhledejte **Microsoft.Batch**. Zkontrolujte, že je v předplatném zaregistrovaný poskytovatel prostředků **Microsoft.Batch**. Pokud není, vyberte odkaz **Registrovat** v horní části obrazovky.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Snímek obrazovky zobrazující poskytovatele prostředků Microsoft.Batch":::

1. Vraťte se na stránku **odběr** a potom vyberte přiřazení rolí **řízení přístupu (IAM)**  >    >  **Přidat**  >  **přiřazení role přidat**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Snímek obrazovky se stránkou přiřazení rolí pro předplatné":::

1. Na stránce **Přidat přiřazení role** vyberte roli **Přispěvatel** nebo **vlastník** a pak vyhledejte rozhraní API služby Batch. Vyhledejte rozhraní API **Microsoft Azure Batch** nebo **MicrosoftAzureBatch** . (**ddbf3205-c6bd-46ae-8127-60eb93363864** je ID aplikace pro rozhraní API služby Batch.)

1. Jakmile najdete rozhraní API služby Batch, vyberte ho a pak vyberte **Uložit**.

### <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

V režimu předplatného uživatele se vyžaduje [Azure Key Vault](../key-vault/general/overview.md) . Key Vault musí být ve stejném předplatném a oblasti jako účet Batch, který se má vytvořit.

1. Z domovské stránky [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **Key Vault**. Z výsledků vyberte **Key Vault** a pak vyberte **vytvořit**.
1. Na stránce **vytvořit Trezor klíčů** zadejte název pro Key Vault a vytvořte novou skupinu prostředků ve stejné oblasti, kterou chcete pro účet Batch. Pro zbývající nastavení ponechte výchozí hodnoty a pak vyberte **Vytvořit**.

Při vytváření účtu Batch v režimu předplatného uživatele zadejte **předplatné uživatele** jako režim přidělování fondů, vyberte Key Vault, který jste vytvořili, a zaškrtnutím políčka udělte Azure Batch přístup k Key Vault.

Pokud dáváte přednost ručnímu udělení přístupu Key Vault, přejděte do části **zásady přístupu** Key Vault a vyberte **Přidat zásady přístupu**. Vyberte odkaz vedle pole **Vybrat objekt zabezpečení** a vyhledejte **Microsoft Azure Batch** (ID aplikace **ddbf3205-c6bd-46ae-8127-60eb93363864**). Vyberte tento objekt zabezpečení a pak nakonfigurujte **tajná oprávnění** pomocí rozevírací nabídky. Azure Batch musí být udělené minimální oprávnění **získat**, **Zobrazit**, **nastavit** a **Odstranit** . U [trezorů klíčů s povoleným obnovitelném odstraněním](../key-vault/general/soft-delete-overview.md)musí být také udělené oprávnění **obnovit** Azure Batch.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Snímek obrazovky s vybranými tajnými oprávněními pro Azure Batch":::

Vyberte **Přidat** a ujistěte se, že jsou pro prostředek propojeného **Key Vault** zaškrtnutá políčka **Virtual Machines Azure pro nasazení** a **Azure Resource Manager pro nasazení šablony** . Vyberte **Uložit** a potvrďte provedené změny.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Snímek obrazovky s obrazovkou zásad přístupu":::

### <a name="configure-subscription-quotas"></a>Konfigurace kvót předplatného

Pro účty Batch pro předplatné uživatele je nutné nastavit [základní kvóty](batch-quota-limit.md) ručně. Kvóty Batch úrovně Standard Core se nevztahují na účty v režimu předplatného uživatele a používají se a vynutily [kvóty v předplatném](../azure-resource-manager/management/azure-subscription-service-limits.md) pro regionální výpočetní jádra, výpočetní jádra pro jednotlivé řady a další prostředky.

1. V [Azure Portal](https://portal.azure.com)vyberte účet Batch v režimu předplatného uživatele, abyste zobrazili jeho nastavení a vlastnosti.
1. V nabídce vlevo vyberte **kvóty** pro zobrazení a konfiguraci základních kvót přidružených k účtu Batch.

## <a name="other-batch-account-management-options"></a>Další možnosti správy účtu Batch

K vytváření a správě účtů Batch můžete kromě webu Azure Portal používat také následující nástroje:

- [Rutiny PowerShellu pro účty Batch](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Knihovna Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](quick-run-dotnet.md) nebo [Pythonu](quick-run-python.md). Tento rychlý Start vás provede ukázkovou aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a to pomocí Azure Storage pro přípravu a načítání souborů úloh.