---
title: Nasazujte prostředky Azure pomocí webu Azure portal | Dokumentace Microsoftu
description: Nasazení prostředků pomocí Azure Resource Manageru a webu Azure portal.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460324"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Nasazení prostředků pomocí šablon Resource Manageru a portálu Azure Portal

Další informace o použití [webu Azure portal](https://portal.azure.com) s [Azure Resource Manageru](resource-group-overview.md) k nasazení prostředků Azure. Další informace o správě prostředků najdete v tématu [Správa prostředků Azure pomocí webu Azure portal](manage-resources-portal.md).

Nasazení prostředků Azure pomocí webu Azure portal obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků.
- Nasazení prostředků do skupiny prostředků.

Kromě toho můžete taky nasadit šablonu Azure Resource Manageru k vytvoření prostředků Azure.

Tento článek ukazuje obě metody.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Chcete-li vytvořit novou skupinu prostředků, vyberte **skupiny prostředků** z [webu Azure portal](https://portal.azure.com).

   ![Výběr skupin zdrojů](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. V rámci skupiny prostředků, vyberte **přidat**.

   ![Přidat skupinu prostředků](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Vyberte nebo zadejte hodnoty následujících vlastností:

    - **Předplatné**: Vyberte předplatné služby Azure.
    - **Skupina prostředků**: Zadejte název skupiny prostředků.
    - **Oblast**: Zadejte umístění Azure. To je, pokud skupina prostředků ukládá metadata o prostředcích. Kvůli dodržování předpisů můžete určit, kde se tato metadata ukládají. Obecně platí doporučujeme vám, že zadáte umístění, kde se bude nacházet maximální využití vašich prostředků. Pomocí stejného umístění může zjednodušit vaši šablonu.

   ![Nastavte hodnoty skupiny](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Vyberte **Zkontrolovat a vytvořit**.
1. Zkontrolujte hodnoty a pak vyberte **vytvořit**.
1. Vyberte **aktualizovat** předtím, než se zobrazí novou skupinu prostředků v seznamu.

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Když vytvoříte skupinu prostředků, můžete nasadit prostředky do skupiny z webu Marketplace. Na webu Marketplace poskytuje předdefinované řešení pro běžné scénáře.

1. Chcete-li spustit nasazení, vyberte **vytvořit prostředek** z [webu Azure portal](https://portal.azure.com).

   ![Nový prostředek](./media/resource-group-template-deploy-portal/new-resources.png)

1. Najdete typ prostředku, který chcete nasadit. Prostředky jsou uspořádány do kategorií. Pokud nevidíte konkrétní řešení, které chcete nasadit, můžete pro ni Hledat na Marketplace. Následující snímek obrazovky ukazuje, že je vybraný Ubuntu Server.

   ![Vyberte typ prostředku](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. V závislosti na typu vybraného prostředku máte kolekci příslušné vlastnosti a nastavte před nasazením. Pro všechny typy musíte vybrat cílová skupina prostředků. Následující obrázek ukazuje, jak vytvořit virtuální počítač s Linuxem a nasadit ho do skupiny prostředků, kterou jste vytvořili.

   ![Vytvoření skupiny prostředků](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternativně můžete vytvořit skupinu prostředků, při nasazování prostředků. Vyberte **vytvořit nový** a zadejte název skupiny prostředků.

1. Spustí se vaše nasazení. Nasazení může trvat několik minut. Některé prostředky trvat déle než jiné prostředky. Po dokončení nasazení se zobrazí oznámení. Vyberte **přejít k prostředku** otevřít

   ![Zobrazení oznámení](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po nasazení vaše prostředky, můžete přidat další prostředky do skupiny prostředků tak, že vyberete **přidat**.

   ![Přidat prostředek](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Nasazení prostředků z vlastní šablony

Pokud chcete provést nasazení, ale ne použít některý z šablony na webu Marketplace, můžete vytvořit vlastní šablonu, která definuje infrastrukturu pro vaše řešení. Další informace o vytváření šablon najdete v tématu [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).

> [!NOTE]
> Rozhraní portálu nepodporuje odkazování [tajného kódu ze služby Key Vault](resource-manager-keyvault-parameter.md). Místo toho použijte [PowerShell](resource-group-template-deploy.md) nebo [rozhraní příkazového řádku Azure](resource-group-template-deploy-cli.md) k nasazení vaší šablony, místně nebo z externí identifikátor URI.

1. Pokud chcete nasadit vlastní šablony na portálu, vyberte **vytvořit prostředek**, vyhledejte **šablony**. a pak vyberte **nasazení šablony**.

   ![Hledat šablony nasazení](./media/resource-group-template-deploy-portal/search-template.png)

1. Vyberte **Vytvořit**.
1. Uvidíte několik možností pro vytváření šablony:

    - **Vytvořit vlastní šablonu v editoru**: vytvořit šablonu pomocí šablony portálu editoru.  Editor není schopen přidat schéma šablony prostředků.
    - **Běžné šablony**: Existují čtyři běžné templatess pro vytvoření virtuálního počítače s Linuxem, virtuálních počítačů Windows, webové aplikace a Azure SQL database.
    - **Načíst šablonu pro rychlý start Githubu**: použijte existující [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/).

   ![Možnosti zobrazení](./media/resource-group-template-deploy-portal/see-options.png)

    Tento kurz obsahuje instrukce pro načítání šablony pro rychlý start.

1. V části **načíst šablonu pro rychlý start Githubu**, zadejte nebo vyberte **101-storage-account – create**.

    Máte dvě možnosti:

    - **Vybrat šablonu**: nasazení šablony.
    - **Úprava šablony**: Upravit šablonu pro rychlý start před jejím nasazením.

1. Vyberte **úpravy šablony** prozkoumat editoru šablon portálu. Je šablona načtena v editoru. Všimněte si, že existují dva parametry: **storageAccountType** a **umístění**.

   ![Vytvoření šablony](./media/resource-group-template-deploy-portal/show-json.png)

1. Proveďte drobnou změnu do šablony. Například aktualizovat **storageAccountName** proměnné:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Vyberte **Uložit**. Nyní uvidíte rozhraní portálu šablony nasazení. Všimněte si, že tyto dva parametry, které jste definovali v šabloně.
1. Zadejte nebo vyberte hodnoty vlastností:

    - **Předplatné**: Vyberte předplatné služby Azure.
    - **Skupina prostředků**: Vyberte **vytvořit nový** a zadejte název.
    - **Umístění**: Vyberte umístění Azure.
    - **Typ účtu úložiště**: Použijte výchozí hodnotu.
    - **Umístění**: Použijte výchozí hodnotu.
    - **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: Toto políčko zaškrtněte.

1. Vyberte **Koupit**.

## <a name="next-steps"></a>Další postup

- Protokoly auditu najdete v tématu [Audit operací pomocí Resource Manageru](./resource-group-audit.md).
- Řešení chyb nasazení, najdete v článku [zobrazení operací nasazení](./resource-manager-deployment-operations.md).
- Pokud chcete vyexportovat šablonu z nasazení nebo skupinu prostředků, přečtěte si téma [exportovat Azure Resource Manageru šablony](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Bezpečně zavést služby napříč několika oblastmi, najdete v článku [Azure Deployment Manager](./deployment-manager-overview.md).
