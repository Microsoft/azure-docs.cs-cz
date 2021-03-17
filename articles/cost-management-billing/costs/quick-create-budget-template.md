---
title: 'Rychlý start: Vytvoření rozpočtu pomocí šablony Azure Resource Manageru'
description: Rychlý start ukazující postup při vytváření rozpočtu pomocí šablony Azure Resource Manageru
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 7d93bd757a39247302a6bc09009a1a814425c32f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745371"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Rychlý start: Vytvoření rozpočtu s využitím šablony ARM

Rozpočty ve službě Cost Management pomáhají plánovat a řídit odpovědnost v organizaci. Pomocí rozpočtů můžete zodpovídat za služby Azure, které využíváte nebo k jejichž odběru jste po určitou dobu přihlášení. Pomůžou vám informovat ostatní o jejich výdajích, aby mohli aktivně spravovat náklady, a umožní vám sledovat, jak se výdaje vyvíjejí v průběhu času. Při překročení vámi vytvořených prahových hodnot rozpočtu se aktivují oznámení. Žádný z vašich prostředků není ovlivněný a vaše spotřeba není zastavena. Rozpočty můžete použít k porovnání a sledování výdajů při analýze nákladů. Tento rychlý start ukazuje postup při vytváření rozpočtu pomocí šablony Azure Resource Manageru (šablona ARM).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure** . Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud máte nové předplatné, nemůžete rovnou vytvořit rozpočet ani využívat další funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

Rozpočty se podporují pro následující typy rozsahů a účtů Azure:

- Rozsahy řízení přístupu na základě role Azure (Azure RBAC)
    - Skupiny pro správu
    - Předplatné
- Rozsahy smlouvy Enterprise
    - Fakturační účet
    - Oddělení
    - Registrační účet
- Jednotlivé smlouvy
    - Fakturační účet
- Rozsahy smluv se zákazníky Microsoftu
    - Fakturační účet
    - Fakturační profil
    - Oddíl faktury
    - Zákazník
- Rozsahy AWS
    - Externí účet
    - Externí předplatné

Pokud chcete zobrazit rozpočty, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure.

Pokud máte předplatná Azure EA, musíte mít oprávnění ke čtení, abyste si mohli rozpočty zobrazit. Chcete-li rozpočty vytvářet a spravovat, musíte mít oprávnění přispěvatele.

Pro rozpočty se podporují následující oprávnění nebo obory Azure pro předplatná podle uživatelů a skupin. Další informace o oborech najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

- Vlastník – Může vytvářet, upravovat a odstraňovat rozpočty u předplatných.
- Přispěvatel a přispěvatel služby Cost Management – Může vytvářet, upravovat a odstraňovat vlastní rozpočty. Může měnit částky rozpočtu pro rozpočty, které vytvářejí jiní uživatelé.
- Čtenář a čtenář služby Cost Management – Může zobrazovat rozpočty, ke kterým má oprávnění.

Další informace o přiřazování oprávnění k datům služby Cost Management najdete v tématu [Přiřazení přístupu k datům služby Cost Management](assign-access-acm-data.md).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

V této šabloně je definovaný jeden prostředek Azure:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Vytvoření rozpočtu Azure.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří rozpočet.

   [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Šablona Resource Manageru, vytvoření rozpočtu, portál pro nasazení" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků:** V případě potřeby vyberte existující skupinu prostředků nebo **vytvořte novou** .
    * **Oblast:** Vyberte oblast Azure. Například **USA – střed** .
    * **Název rozpočtu:** Zadejte název pro váš rozpočet. Měl by být v rámci skupiny prostředků jedinečný. Povolují se jen alfanumerické znaky, podtržítka a spojovníky.
    * **Částka:** Zadejte celkovou výši nákladů, které se mají pomocí rozpočtu sledovat.
    * **Agregační interval:** Zadejte dobu, pro kterou je rozpočet určený. Povolené hodnoty jsou Měsíčně, Čtvrtletně nebo Ročně. Rozpočet se na konci agregačního intervalu resetuje.
    * **Počáteční datum:** Zadejte počáteční datum (první den v měsíci ve formátu RRRR-MM-DD). Počáteční datum by nemělo být ode dneška za více než 3 měsíce. V rámci agregačního intervalu můžete zadat počáteční datum v minulosti.
    * **Koncové datum:** Zadejte koncové datum rozpočtu ve formátu RRRR-MM-DD. 
    * **První prahová hodnota:** Zadejte prahovou hodnotu pro první oznámení. Oznámení se odešle, když náklady překročí tuto prahovou hodnotu. Je to vždycky procentuální údaj a musí být v rozmezí od 0 do 1000.
    * **Druhá prahová hodnota:** Zadejte prahovou hodnotu pro druhé oznámení. Oznámení se odešle, když náklady překročí tuto prahovou hodnotu. Je to vždycky procentuální údaj a musí být v rozmezí od 0 do 1000.
    * **Kontaktní role:** Zadejte seznam kontaktních rolí, na které se má odeslat oznámení při překročení prahové hodnoty rozpočtu. Výchozí hodnoty jsou Vlastník, Přispěvatel a Čtenář. Očekávaný formát je `["Owner","Contributor","Reader"]`.
    * **Kontaktní e-maily:** Zadejte seznam e-mailových adres, na které se má odeslat oznámení při překročení prahové hodnoty rozpočtu. Očekávaný formát je `["user1@domain.com","user2@domain.com"]`.
    * **Kontaktní skupiny:** Zadejte seznam ID prostředků skupin akcí (ve formě kompletních identifikátorů URI prostředků), na které se má odeslat oznámení při překročení prahové hodnoty rozpočtu. Je možné zadat pole řetězců. Očekávaný formát je `["action group resource ID1","action group resource ID2"]`. Pokud nechcete používat skupiny akcí, zadejte `[]`.
    * **Hodnoty filtru skupin prostředků:** Zadejte seznam názvů skupin prostředků pro filtrování. Očekávaný formát je `["Resource Group Name1","Resource Group Name2"]`. Pokud nechcete použít filtr, zadejte `[]`. 
    * **Hodnoty filtru kategorií měřičů:** Zadejte seznam enter kategorií měřičů služeb Azure. Očekávaný formát je `["Meter Category1","Meter Category2"]`. Pokud nechcete použít filtr, zadejte `[]`.
   
3. V závislosti na typu předplatného Azure proveďte jednu z následujících akcí:
   - Vyberte **Zkontrolovat a vytvořit** .
   - Přečtěte si Podmínky a ujednání, vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a potom vyberte **Koupit** .

4. Pokud jste vybrali **Zkontrolovat a vytvořit** , vaše šablona se ověří. Vyberte **Vytvořit** .  

   ![Šablona Resource Manageru, vytvoření rozpočtu, oznámení portálu nasazení](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

K nasazení šablony se použije Azure Portal. Kromě webu Azure Portal můžete také použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších šablonách nasazení najdete v tématu [Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

K ověření, že se rozpočet vytvořil, můžete použít Azure Portal. Přejděte na **Správa nákladů a fakturace** > vyberte rozsah > **Rozpočty** . Případně můžete ke kontrole rozpočtu použít následující skripty Azure PowerShellu nebo Azure CLI.

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

Přejděte do části **Správa nákladů a fakturace** > vyberte rozsah fakturace > **Rozpočty** > vyberte rozpočet > potom vyberte **Odstranit rozpočet** .

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
