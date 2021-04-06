---
title: Nasazení prostředků pomocí Azure Portal
description: K nasazení prostředků do skupiny prostředků ve vašem předplatném použijte Azure Portal a správu prostředků Azure.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028671"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Nasazení prostředků pomocí šablon ARM a Azure Portal

Naučte se používat [Azure Portal](https://portal.azure.com) se [šablonami Azure Resource Manager (šablony ARM)](overview.md) k nasazení prostředků Azure. Další informace o správě prostředků najdete v tématu [Správa prostředků Azure pomocí Azure Portal](../management/manage-resources-portal.md).

Nasazení prostředků Azure pomocí Azure Portal obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků.
- Nasaďte prostředky do skupiny prostředků.

K nasazení prostředků Azure můžete také vytvořit přizpůsobenou šablonu ARM.

Tento článek ukazuje obě metody.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Pokud chcete vytvořit novou skupinu prostředků, vyberte **skupiny prostředků** z [Azure Portal](https://portal.azure.com).

   ![Výběr skupin prostředků](./media/deploy-portal/select-resource-groups.png)

1. V části skupiny prostředků vyberte **Přidat**.

   ![Přidat skupinu prostředků](./media/deploy-portal/add-resource-group.png)

1. Vyberte nebo zadejte následující hodnoty vlastností:

    - **Předplatné**: vyberte předplatné Azure.
    - **Skupina prostředků**: zadejte název skupiny prostředků.
    - **Oblast**: zadejte umístění Azure. V tomto umístění skupina prostředků ukládá metadata o prostředcích. Z důvodu dodržování předpisů možná budete chtít určit, kde se metadata ukládají. Obecně doporučujeme zadat umístění, kde bude většina vašich prostředků. Použití stejného umístění může zjednodušit vaši šablonu.

   ![Nastavení hodnot skupiny](./media/deploy-portal/set-group-properties.png)

1. Vyberte **Zkontrolovat a vytvořit**.
1. Zkontrolujte hodnoty a pak vyberte **vytvořit**.
1. Než uvidíte novou skupinu prostředků v seznamu, vyberte **aktualizovat** .

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření skupiny prostředků můžete z Marketplace nasadit prostředky do skupiny. Marketplace nabízí předdefinovaná řešení pro běžné scénáře.

1. Nasazení spustíte tak, že vyberete **vytvořit prostředek** z [Azure Portal](https://portal.azure.com).

   ![Nový prostředek](./media/deploy-portal/new-resources.png)

1. Najděte typ prostředku, který chcete nasadit. Prostředky jsou uspořádány do kategorií. Pokud nevidíte konkrétní řešení, které byste chtěli nasadit, můžete si ho na Marketplace vyhledat. Na následujícím snímku obrazovky vidíte, že je vybraný server Ubuntu.

   ![Vybrat typ prostředku](./media/deploy-portal/select-resource-type.png)

1. V závislosti na typu vybraného prostředku máte k dispozici kolekci odpovídajících vlastností, které chcete nastavit před nasazením. Pro všechny typy musíte vybrat cílovou skupinu prostředků. Následující obrázek ukazuje, jak vytvořit virtuální počítač se systémem Linux a nasadit ho do skupiny prostředků, kterou jste vytvořili.

   ![Vytvoření skupiny prostředků](./media/deploy-portal/select-existing-group.png)

   Při nasazování prostředků se můžete rozhodnout vytvořit skupinu prostředků. Vyberte **vytvořit novou** a zadejte název skupiny prostředků.

1. Vaše nasazení je zahájeno. Nasazení může trvat několik minut. Některé prostředky vybírají delší dobu než jiné prostředky. Po dokončení nasazení se zobrazí oznámení. Vyberte **Přejít k prostředku** a otevřete ho.

   ![Zobrazit oznámení](./media/deploy-portal/view-notification.png)

1. Po nasazení prostředků můžete přidat další prostředky do skupiny prostředků tak, že vyberete **Přidat**.

   ![Přidat prostředek](./media/deploy-portal/add-resource.png)

I když jste ho nezobrazili, portál k nasazení vybraných prostředků použil šablonu ARM. Šablonu můžete najít v historii nasazení. Další informace najdete v tématu [Export šablony po nasazení](export-template-portal.md#export-template-after-deployment).

## <a name="deploy-resources-from-custom-template"></a>Nasazení prostředků z vlastní šablony

Pokud chcete spustit nasazení, ale ne použít žádnou z šablon na webu Marketplace, můžete vytvořit přizpůsobenou šablonu, která definuje infrastrukturu pro vaše řešení. Další informace o vytváření šablon najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).

> [!NOTE]
> Rozhraní portálu nepodporuje odkazování na [tajný kód z Key Vault](key-vault-parameter.md). Místo toho nasaďte šablonu místně nebo z externího identifikátoru URI pomocí [PowerShellu](deploy-powershell.md) nebo rozhraní příkazového [řádku Azure](deploy-cli.md) .

1. Pokud chcete nasadit přizpůsobenou šablonu prostřednictvím portálu, vyberte **vytvořit prostředek** a vyhledejte **šablonu**. a pak vyberte **template Deployment**.

   ![Hledání v nasazení šablony](./media/deploy-portal/search-template.png)

1. Vyberte **Vytvořit**.
1. Zobrazí se několik možností pro vytvoření šablony:

    - Vytvoření **vlastní šablony v editoru**: Vytvoření vlastní šablony v editoru šablon portálu.
    - **Běžné šablony**: vyberte ze běžných řešení.
    - **Načtení šablony pro rychlý Start GitHubu**: vyberte ze [šablon pro rychlý Start](https://azure.microsoft.com/resources/templates/).

   ![Možnosti zobrazení](./media/deploy-portal/see-options.png)

    Tento kurz poskytuje pokyny pro načtení šablony pro rychlý Start.

1. V části **načíst šablonu pro rychlý Start GitHubu** zadejte nebo vyberte **101-účet úložiště-vytvořit**.

    Máte dvě možnosti:

    - **Vyberte šablonu**: nasaďte šablonu.
    - **Upravit šablonu**: před nasazením upravte šablonu pro rychlý Start.

1. Vyberte **Upravit šablonu** a prozkoumejte editor šablon portálu. Šablona je načtena v editoru. Všimněte si, že existují dva parametry: `storageAccountType` a `location` .

   ![Vytvoření šablony](./media/deploy-portal/show-json.png)

1. Udělejte v šabloně menší změnu. Například aktualizujte `storageAccountName` proměnnou na:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Vyberte **Uložit**. Nyní se zobrazí rozhraní nasazení šablony portálu. Všimněte si dvou parametrů, které jste definovali v šabloně.
1. Zadejte nebo vyberte hodnoty vlastností:

    - **Předplatné**: vyberte předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte název.
    - **Umístění**: vyberte umístění Azure.
    - **Typ účtu úložiště**: použijte výchozí hodnotu.
    - **Umístění**: použijte výchozí hodnotu.
    - **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: Toto políčko zaškrtněte.

1. Vyberte **Koupit**.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zobrazit protokoly auditu, přečtěte si téma [operace auditu s správce prostředků](../management/view-activity-logs.md).
- Informace o řešení chyb při nasazení najdete v tématu [Zobrazení operací nasazení](deployment-history.md).
- Pokud chcete exportovat šablonu z nasazení nebo skupiny prostředků, přečtěte si téma [Export šablon ARM](export-template-portal.md).
- K bezpečnému uvedení služby na více oblastí se podívejte na [Azure Správce nasazení](deployment-manager-overview.md).
