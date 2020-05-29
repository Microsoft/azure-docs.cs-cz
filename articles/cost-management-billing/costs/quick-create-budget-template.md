---
title: 'Rychlý start: Vytvoření rozpočtu pomocí šablony Azure Resource Manageru'
description: Rychlý start ukazující postup při vytváření rozpočtu pomocí šablony Azure Resource Manageru
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: subject-armqs
ms.openlocfilehash: 88dd1dc99f32d0539718c0f71206176cbfc16eec
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830306"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Rychlý start: Vytvoření rozpočtu pomocí šablony Azure Resource Manageru

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomocí rozpočtů můžete zodpovídat za služby Azure, které využíváte nebo k jejichž odběru jste po určitou dobu přihlášení. Pomůžou vám informovat ostatní o jejich výdajích, aby mohli aktivně spravovat náklady, a umožní vám sledovat, jak se výdaje vyvíjejí v průběhu času. Při překročení vámi vytvořených prahových hodnot rozpočtu se aktivují oznámení. Žádný z vašich prostředků není ovlivněný a vaše spotřeba není zastavena. Rozpočty můžete použít k porovnání a sledování výdajů při analýze nákladů. Tento rychlý start ukazuje postup při vytváření rozpočtu pomocí šablony Azure Resource Manageru.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Šablona Azure Resource Manager podporuje jenom předplatná Azure pro smlouvy Enterprise (EA). Jiné typy předplatného tato šablona nepodporuje.

Chcete-li rozpočty vytvářet a spravovat, musíte mít oprávnění přispěvatele. Můžete vytvořit individuální rozpočty pro předplatná EA a skupiny prostředků. Nemůžete však vytvořit rozpočty pro fakturační účty EA. Pokud máte předplatná Azure EA, musíte mít oprávnění ke čtení, abyste si mohli rozpočty zobrazit.

Pokud chcete rozpočet po vytvoření zobrazit, potřebujete přinejmenším oprávnění ke čtení pro váš účet Azure.

Pokud máte nové předplatné, nemůžete rovnou vytvořit rozpočet ani využívat další funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

Pro rozpočty se podporují následující oprávnění nebo obory Azure pro předplatná podle uživatelů a skupin. Další informace o oborech najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatel služby Cost Management – Může vytvářet, upravovat a odstraňovat vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtenář služby Cost Management – Může zobrazovat rozpočty, ke kterým má oprávnění.

Další informace o přiřazování oprávnění k datům služby Cost Management najdete v tématu [Přiřazení přístupu k datům služby Cost Management](assign-access-acm-data.md).

## <a name="create-a-budget"></a>Vytvoření rozpočtu

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

V této šabloně je definovaný jeden prostředek Azure:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Vytvoření rozpočtu Azure.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří rozpočet.

   [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

   [![Šablona Resource Manageru, vytvoření rozpočtu, portál pro nasazení](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků:** Vyberte **Vytvořit nové**, zadejte jedinečný název pro skupinu prostředků a potom klikněte na **OK**, nebo vyberte existující skupinu prostředků.
    * **Umístění:** Vyberte prosím umístění. Například **USA – střed**.
    * **Název rozpočtu:** Zadejte název pro váš rozpočet. Měl by být v rámci skupiny prostředků jedinečný. Povolují se jen alfanumerické znaky, podtržítka a spojovníky.
    * **Částka:** Zadejte celkové náklady nebo využití, které se má pomocí rozpočtu sledovat.
    * **Kategorie rozpočtu:** Vyberte kategorii rozpočtu – jestli rozpočet sleduje **náklady** nebo **využití**.
    * **Agregační interval:** Zadejte dobu, pro kterou je rozpočet určený. Povolené hodnoty jsou Měsíčně, Čtvrtletně nebo Ročně. Rozpočet se na konci agregačního intervalu resetuje.
    * **Počáteční datum:** Zadejte počáteční datum (první den v měsíci ve formátu RRRR-MM-DD). Počáteční datum by nemělo být ode dneška za více než 3 měsíce. V rámci agregačního intervalu můžete zadat počáteční datum v minulosti.
    * **Koncové datum:** Zadejte koncové datum rozpočtu ve formátu RRRR-MM-DD. Pokud není zadané, jako výchozí koncové datum se nastaví se 10 let od počátečního data.
    * **Operátor:** : Vyberte operátor porovnání. Možné hodnoty jsou EqualTo, GreaterThan a GreaterThanOrEqualTo.
    * **Prahová hodnota:** Zadejte prahovou hodnotu pro oznámení. Oznámení se odešle, když náklady překročí tuto prahovou hodnotu. Je to vždycky procentuální údaj a musí být v rozmezí od 0 do 1000.
    * **Kontaktní e-maily:** Zadejte seznam e-mailových adres, na které se má odeslat oznámení při překročení prahové hodnoty rozpočtu. Očekávaný formát je `["user1@domain.com","user2@domain.com"]`.
    * **Kontaktní role:** Zadejte seznam kontaktních rolí, na které se má odeslat oznámení při překročení prahové hodnoty rozpočtu. Výchozí hodnoty jsou Vlastník, Přispěvatel a Čtenář. Očekávaný formát je `["Owner","Contributor","Reader"]`.
    * **Kontaktní skupiny:** Zadejte seznam skupin akcí, na které se má odeslat oznámení při překročení prahové hodnoty rozpočtu. Je možné zadat pole řetězců. Očekávaný formát je `["Action Group Name1","Action Group Name2"]`. Pokud nechcete používat skupiny akcí, zadejte `[]`.
    * **Filtr prostředků:** Zadejte seznam filtrů pro prostředky. Očekávaný formát je `["Resource Filter Name1","Resource Filter Name2"]`. Pokud nechcete použít filtr, zadejte `[]`. Pokud zadáte filtr prostředků, musíte také zadat hodnoty **filtrů měřičů**.
    * **Filtr měřičů:** Zadejte seznam filtrů pro měřiče. Pro rozpočty kategorie **Využití** je to povinné. Očekávaný formát je `["Meter Filter Name1","Meter Filter Name2"]`. Pokud jste nezadali **filtr prostředků**, zadejte `[]`.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.

3. Vyberte **Koupit**. Po úspěšném nasazení rozpočtu dostanete oznámení:

   ![Šablona Resource Manageru, vytvoření rozpočtu, oznámení portálu nasazení](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

K nasazení šablony se použije Azure Portal. Kromě webu Azure Portal můžete také použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších šablonách nasazení najdete v tématu [Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

K ověření, že se rozpočet vytvořil, můžete použít Azure Portal. Přejděte na **Správa nákladů a fakturace** > vyberte rozsah > **Rozpočty**. Případně můžete ke kontrole rozpočtu použít následující skripty Azure PowerShellu nebo Azure CLI.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už rozpočet nepotřebujete, odstraňte ho pomocí jedné z následujících metod:

### <a name="azure-portal"></a>portál Azure

Přejděte do části **Správa nákladů a fakturace** > vyberte rozsah fakturace > **Rozpočty** > vyberte rozpočet > potom vyberte **Odstranit rozpočet**.

### <a name="command-line"></a>Příkazový řádek

Rozpočet můžete odebrat pomocí rozhraní příkazového řádku Azure CLI nebo Azure PowerShellu.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nasazení rozpočtu Azure. Pokud se chcete o Azure Cost Managementu, fakturaci a Azure Resource Manageru dozvědět víc, pokračujte následujícími články.

- Přehled [správy nákladů a fakturace](../cost-management-billing-overview.md)
- [Vytváření rozpočtů](tutorial-acm-create-budgets.md) na webu Azure Portal
- Další informace o [Azure Resource Manageru](../../azure-resource-manager/management/overview.md)
