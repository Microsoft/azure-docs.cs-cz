---
title: Nasazení prostředků pomocí portálu Azure
description: Pomocí portálu Azure Portal a Azure Resource Manage nasazujte prostředky do skupiny prostředků ve vašem předplatném.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153433"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Nasazení prostředků pomocí šablon ARM a portálu Azure

Zjistěte, jak používat [portál Azure s](https://portal.azure.com) [šablonami Azure Resource Manager (ARM)](overview.md) k nasazení prostředků Azure. Další informace o správě prostředků najdete v [tématu Správa prostředků Azure pomocí portálu Azure](../management/manage-resources-portal.md).

Nasazení prostředků Azure pomocí portálu Azure obvykle zahrnuje dva kroky:

- Vytvořte skupinu prostředků.
- Nasaďte prostředky do skupiny prostředků.

Kromě toho můžete také nasadit šablonu ARM k vytvoření prostředků Azure.

Tento článek ukazuje obě metody.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Pokud chcete vytvořit novou skupinu prostředků, vyberte **skupiny prostředků** z [webu Azure Portal](https://portal.azure.com).

   ![Vybrat skupiny prostředků](./media/deploy-portal/select-resource-groups.png)

1. V části Skupiny prostředků vyberte **Přidat**.

   ![Přidat skupinu prostředků](./media/deploy-portal/add-resource-group.png)

1. Vyberte nebo zadejte následující hodnoty vlastností:

    - **Předplatné**: Vyberte předplatné Azure.
    - **Skupina prostředků**: Pojmenujte skupinu prostředků.
    - **Oblast**: Zadejte umístění Azure. Toto je místo, kde skupina prostředků ukládá metadata o prostředcích. Z důvodů dodržování předpisů můžete určit, kde jsou tato metadata uložena. Obecně doporučujeme zadat umístění, kde se bude nabývat většina prostředků. Použití stejného umístění může zjednodušit vaši šablonu.

   ![Nastavení hodnot skupin](./media/deploy-portal/set-group-properties.png)

1. Vyberte **Zkontrolovat a vytvořit**.
1. zkontrolujte hodnoty a pak vyberte **Vytvořit**.
1. Než uvidíte novou skupinu prostředků v seznamu, vyberte **aktualizovat.**

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření skupiny prostředků můžete nasadit prostředky do skupiny z webu Marketplace. Marketplace poskytuje předdefinovaná řešení pro běžné scénáře.

1. Pokud chcete zahájit nasazení, vyberte **Vytvořit prostředek** z webu [Azure Portal](https://portal.azure.com).

   ![Nový zdroj](./media/deploy-portal/new-resources.png)

1. Najděte typ prostředku, který chcete nasadit. Zdroje jsou uspořádány do kategorií. Pokud nevidíte konkrétní řešení, které chcete nasadit, můžete ho vyhledat na Marketplace. Následující snímek obrazovky ukazuje, že je vybrán Server Ubuntu.

   ![Vybrat typ zdroje](./media/deploy-portal/select-resource-type.png)

1. V závislosti na typu vybraného prostředku máte kolekci relevantních vlastností, které chcete nastavit před nasazením. Pro všechny typy je nutné vybrat cílovou skupinu prostředků. Následující obrázek ukazuje, jak vytvořit virtuální počítač Linux a nasadit ho do skupiny prostředků, kterou jste vytvořili.

   ![Vytvoření skupiny prostředků](./media/deploy-portal/select-existing-group.png)

   Případně se můžete rozhodnout vytvořit skupinu prostředků při nasazování prostředků. Vyberte **Vytvořit nový** a pojmenujte skupinu prostředků.

1. Vaše nasazení začíná. Nasazení může trvat několik minut. Některé prostředky trvat déle než jiné prostředky. Po dokončení nasazení se zobrazí oznámení. Vyberte **Přejít na zdroj,** který chcete otevřít.

   ![Zobrazit oznámení](./media/deploy-portal/view-notification.png)

1. Po nasazení prostředků můžete přidat další prostředky do skupiny prostředků výběrem **možnosti Přidat**.

   ![Přidání zdroje](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Nasazení prostředků z vlastní šablony

Pokud chcete spustit nasazení, ale nepoužívat žádnou ze šablon na marketplace, můžete vytvořit vlastní šablonu, která definuje infrastrukturu pro vaše řešení. Další informace o vytváření šablon naleznete [v tématu Principy struktury a syntaxe šablon ARM](template-syntax.md).

> [!NOTE]
> Rozhraní portálu nepodporuje odkazování na [tajný klíč z trezoru klíčů](key-vault-parameter.md). Místo toho použijte [PowerShell](deploy-powershell.md) nebo [Azure CLI](deploy-cli.md) k nasazení šablony místně nebo z externího identifikátoru URI.

1. Pokud chcete nasadit vlastní šablonu prostřednictvím portálu, vyberte **Vytvořit prostředek**, vyhledejte **šablonu**. a pak vyberte **nasazení šablony**.

   ![Nasazení šablony hledání](./media/deploy-portal/search-template.png)

1. Vyberte **Vytvořit**.
1. Zobrazí se několik možností pro vytvoření šablony:

    - **Vytvořte si vlastní šablonu v editoru**: vytvořte šablonu pomocí editoru šablon portálu.  Editor je schopen přidat schéma šablony prostředků.
    - **Běžné šablony**: Existují čtyři běžné šablony pro vytvoření virtuálního počítače s Linuxem, virtuálního počítače s Windows, webové aplikace a databáze Azure SQL.
    - **Načtěte šablonu rychlého startu GitHubu**: použijte existující [šablony rychlého startu](https://azure.microsoft.com/resources/templates/).

   ![Možnosti zobrazení](./media/deploy-portal/see-options.png)

    Tento kurz obsahuje pokyny pro načtení šablony rychlého startu.

1. V části **Načíst šablonu rychlého startu GitHub**uložte nebo vyberte **101-storage-account-create**.

    Máte dvě možnosti:

    - **Vyberte šablonu**: nasaďte šablonu.
    - **Upravit šablonu**: Před nasazením šablonu rychlého startu upravte.

1. Vyberte **Upravit šablonu** a prozkoumejte editor šablon portálu. Šablona je načtena v editoru. Všimněte si, že existují dva parametry: **storageAccountType** a **umístění**.

   ![Vytvoření šablony](./media/deploy-portal/show-json.png)

1. Proveďte menší změnu šablony. Například aktualizujte proměnnou **storageAccountName** na:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Vyberte **Uložit**. Nyní se zobrazí rozhraní pro nasazení šablony portálu. Všimněte si dvou parametrů, které jste definovali v šabloně.
1. Zadejte nebo vyberte hodnoty vlastností:

    - **Předplatné**: Vyberte předplatné Azure.
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte název.
    - **Umístění**: Vyberte umístění Azure.
    - **Typ účtu úložiště**: Použijte výchozí hodnotu.
    - **Umístění**: Použijte výchozí hodnotu.
    - **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: Toto políčko zaškrtněte.

1. Vyberte **Koupit**.

## <a name="next-steps"></a>Další kroky

- Protokoly auditu naleznete v [tématu Audit operace se Správcem prostředků](../management/view-activity-logs.md).
- Informace o řešení chyb nasazení naleznete v [tématu Zobrazení operací nasazení](deployment-history.md).
- Informace o exportu šablony ze skupiny nasazení nebo prostředků naleznete v [tématu Export šablon ARM](export-template-portal.md).
- Pokud chcete bezpečně zavést službu ve více oblastech, přečtěte si informace [o Tom, jak azure deployment manager](deployment-manager-overview.md).
