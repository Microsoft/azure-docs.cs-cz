---
title: Použití Azure Portal k nasazení prostředků Azure | Microsoft Docs
description: K nasazení prostředků do skupiny prostředků ve vašem předplatném použijte Azure Portal a správu prostředků Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: 249afcaad85d9031e0972d4fcfc185b5ff890f65
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390349"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Nasazení prostředků pomocí šablon Resource Manageru a portálu Azure Portal

Naučte se používat [Azure Portal](https://portal.azure.com) s [Azure Resource Manager](resource-group-overview.md) k nasazení prostředků Azure. Další informace o správě prostředků najdete v tématu [Správa prostředků Azure pomocí Azure Portal](manage-resources-portal.md).

Nasazení prostředků Azure pomocí Azure Portal obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků.
- Nasaďte prostředky do skupiny prostředků.

Kromě toho můžete k vytvoření prostředků Azure nasadit taky šablonu Azure Resource Manager.

Tento článek ukazuje obě metody.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Pokud chcete vytvořit novou skupinu prostředků, vyberte **skupiny prostředků** z [Azure Portal](https://portal.azure.com).

   ![Výběr skupin prostředků](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. V části skupiny prostředků vyberte **Přidat**.

   ![Přidat skupinu prostředků](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Vyberte nebo zadejte následující hodnoty vlastností:

    - **Předplatné:** Vyberte předplatné Azure.
    - **Skupina prostředků**: zadejte název skupiny prostředků.
    - **Oblast**: zadejte umístění Azure. V takovém případě skupina prostředků ukládá metadata o prostředcích. Z důvodu dodržování předpisů možná budete chtít určit, kde se metadata ukládají. Obecně doporučujeme, abyste zadali umístění, kde se bude nacházet většina vašich prostředků. Použití stejného umístění může zjednodušit vaši šablonu.

   ![Nastavení hodnot skupiny](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Vyberte **Zkontrolovat a vytvořit**.
1. Zkontrolujte hodnoty a pak vyberte **vytvořit**.
1. Než uvidíte novou skupinu prostředků v seznamu, vyberte **aktualizovat** .

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření skupiny prostředků můžete z Marketplace nasadit prostředky do skupiny. Marketplace nabízí předdefinovaná řešení pro běžné scénáře.

1. Nasazení spustíte tak, že vyberete **vytvořit prostředek** z [Azure Portal](https://portal.azure.com).

   ![Nový prostředek](./media/resource-group-template-deploy-portal/new-resources.png)

1. Najděte typ prostředku, který chcete nasadit. Prostředky jsou uspořádány do kategorií. Pokud nevidíte konkrétní řešení, které byste chtěli nasadit, můžete si ho na Marketplace vyhledat. Na následujícím snímku obrazovky vidíte, že je vybraný server Ubuntu.

   ![Vybrat typ prostředku](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. V závislosti na typu vybraného prostředku máte k dispozici kolekci odpovídajících vlastností, které chcete nastavit před nasazením. Pro všechny typy musíte vybrat cílovou skupinu prostředků. Následující obrázek ukazuje, jak vytvořit virtuální počítač se systémem Linux a nasadit ho do skupiny prostředků, kterou jste vytvořili.

   ![Vytvoření skupiny prostředků](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Případně se můžete rozhodnout vytvořit skupinu prostředků při nasazení prostředků. Vyberte **vytvořit novou** a zadejte název skupiny prostředků.

1. Vaše nasazení je zahájeno. Nasazení může trvat několik minut. Některé prostředky vybírají delší dobu než jiné prostředky. Po dokončení nasazení se zobrazí oznámení. Vyberte **Přejít k prostředku** a otevřete ho.

   ![Zobrazit oznámení](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po nasazení prostředků můžete přidat další prostředky do skupiny prostředků tak, že vyberete **Přidat**.

   ![Přidat prostředek](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Nasazení prostředků z vlastní šablony

Pokud chcete spustit nasazení, ale ne použít žádnou z šablon na webu Marketplace, můžete vytvořit přizpůsobenou šablonu, která definuje infrastrukturu pro vaše řešení. Další informace o vytváření šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> Rozhraní portálu nepodporuje odkazování na [tajný kód z Key Vault](resource-manager-keyvault-parameter.md). Místo toho nasaďte šablonu místně nebo z externího identifikátoru URI pomocí [PowerShellu](resource-group-template-deploy.md) nebo rozhraní příkazového [řádku Azure](resource-group-template-deploy-cli.md) .

1. Pokud chcete nasadit přizpůsobenou šablonu prostřednictvím portálu, vyberte **vytvořit prostředek**a vyhledejte **šablonu**. a pak vyberte **template Deployment**.

   ![Hledání v nasazení šablony](./media/resource-group-template-deploy-portal/search-template.png)

1. Vyberte **Create** (Vytvořit).
1. Zobrazí se několik možností pro vytvoření šablony:

    - Vytvoření **vlastní šablony v editoru**: Vytvořte šablonu pomocí editoru šablon portálu.  Editor je schopný přidat schéma šablony prostředků.
    - **Běžné šablony**: Existují čtyři společné šablony pro vytvoření virtuálního počítače se systémem Linux, virtuálního počítače s Windows, webové aplikace a databáze SQL Azure.
    - **Načtení šablony pro rychlý Start GitHubu**: použijte existující [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/).

   ![Možnosti zobrazení](./media/resource-group-template-deploy-portal/see-options.png)

    Tento kurz poskytuje pokyny pro načtení šablony pro rychlý Start.

1. V části **načíst šablonu pro rychlý Start GitHubu**zadejte nebo vyberte **101-účet úložiště-vytvořit**.

    Máte dvě možnosti:

    - **Vyberte šablonu**: nasaďte šablonu.
    - **Upravit šablonu**: před nasazením upravte šablonu pro rychlý Start.

1. Vyberte **Upravit šablonu** a prozkoumejte editor šablon portálu. Šablona je načtena v editoru. Všimněte si, že existují dva parametry: **storageAccountType** a **Location**.

   ![Vytvoření šablony](./media/resource-group-template-deploy-portal/show-json.png)

1. Udělejte v šabloně menší změnu. Například aktualizujte proměnnou **storageAccountName** na:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Vyberte **Save** (Uložit). Nyní se zobrazí rozhraní nasazení šablony portálu. Všimněte si dvou parametrů, které jste definovali v šabloně.
1. Zadejte nebo vyberte hodnoty vlastností:

    - **Předplatné:** Vyberte předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte název.
    - **Umístění**: vyberte umístění Azure.
    - **Typ účtu úložiště**: použijte výchozí hodnotu.
    - **Umístění**: použijte výchozí hodnotu.
    - **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: Toto políčko zaškrtněte.

1. Vyberte **Koupit**.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zobrazit protokoly auditu, přečtěte si téma [operace auditu s správce prostředků](./resource-group-audit.md).
- Informace o řešení chyb při nasazení najdete v tématu [Zobrazení operací nasazení](./resource-manager-deployment-operations.md).
- Pokud chcete exportovat šablonu z nasazení nebo skupiny prostředků, přečtěte si téma [export Azure Resource Manager šablon](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- K bezpečnému uvedení služby na více oblastí se podívejte na [Azure Deployment Manager](./deployment-manager-overview.md).
