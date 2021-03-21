---
title: 'Rychlý Start: vytvoření účtu Automation – šablona Azure'
titleSuffix: Azure Automation
description: V tomto rychlém startu se dozvíte, jak vytvořit účet Automation pomocí šablony Azure Resource Manager.
services: automation
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 4d7d4b5af9b86fe5116dc29f6ed1a789cdbf2047
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581091"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Rychlý Start: vytvoření účtu Automation pomocí šablony ARM

Azure Automation zajišťuje cloudovou službu pro automatizaci a konfiguraci, která podporuje konzistentní správu napříč prostředími Azure a mimo Azure. V tomto rychlém startu se dozvíte, jak nasadit šablonu Azure Resource Manager (šablonu ARM), která vytvoří účet Automation. Použití šablony ARM trvá méně kroků v porovnání s jinými metodami nasazení.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Tato ukázková šablona provede následující:

* Automatizuje vytváření Azure Monitorho pracovního prostoru Log Analytics.
* Automatizuje vytváření Azure Automation účtu.
* Propojí účet Automation s pracovním prostorem Log Analytics.
* Přidá do účtu ukázkové Runbooky pro automatizaci.

>[!NOTE]
>Vytvoření účtu Automation spustit jako není podporované, když používáte šablonu ARM. Pokud chcete vytvořit účet Spustit jako ručně z portálu nebo pomocí PowerShellu, přečtěte si téma [Vytvoření účtu Spustit jako](create-run-as-account.md).

Po dokončení těchto kroků je potřeba [nakonfigurovat nastavení diagnostiky](automation-manage-send-joblogs-log-analytics.md) pro váš účet Automation, aby se odesílaly datové proudy úloh Runbooku do propojeného Log Analytics pracovního prostoru.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Verze rozhraní API

Následující tabulka uvádí verzi rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| [Pracovní prostor](/azure/templates/microsoft.operationalinsights/workspaces) | pracovní prostory | 2020-03-01 – Preview |
| [Účet služby Automation](/azure/templates/microsoft.automation/automationaccounts) | automatizace | 2020-01-13 – Preview |
| [Propojené služby v pracovním prostoru](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | pracovní prostory | 2020-03-01 – Preview |

### <a name="before-you-use-the-template"></a>Před použitím šablony

Šablona parametrů JSON je nakonfigurovaná, abyste určili:

* Název pracovního prostoru
* Oblast, ve které se má pracovní prostor vytvořit.
* Název účtu Automation.
* Oblast, ve které se má účet služby Automation vytvořit.

Následující parametry v šabloně jsou nastaveny s výchozí hodnotou pro Log Analytics pracovní prostor:

* Výchozí hodnota *SKU* je cenová úroveň na GB vydaná v cenovém modelu z dubna 2018.
* *Doba uchovávání dat* je nastavena na 30 dní.

>[!WARNING]
>Pokud chcete vytvořit nebo nakonfigurovat pracovní prostor Log Analytics v předplatném, které se zavedlo do cenového modelu z dubna 2018, bude jediná platná Log Analytics cenová úroveň *PerGB2018*.
>

Šablona JSON určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. Šablonu můžete uložit v účtu služby Azure Storage pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Pokud Azure Automation a Azure Monitor začínáte, je důležité, abyste porozuměli následujícím podrobnostem o konfiguraci. Můžou vám zabránit chybám při pokusu o vytvoření, konfiguraci a používání pracovního prostoru Log Analytics propojeného s vaším novým účtem Automation.

* Přečtěte si [Další podrobnosti](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) , abyste plně pochopili možnosti konfigurace pracovního prostoru, jako je režim řízení přístupu, cenová úroveň, uchování a úroveň rezervace kapacity.

* Zkontrolujte [mapování pracovních prostorů](how-to/region-mappings.md) a určete podporované oblasti v inline nebo v souboru parametrů. Pro propojení Log Analyticsho pracovního prostoru a účtu Automation v rámci předplatného se podporují jenom některé oblasti.

* Pokud jste s protokoly Azure Monitor a ještě nemáte nasazený pracovní prostor, měli byste si projít pokyny k [návrhu pracovního prostoru](../azure-monitor/logs/design-logs-deployment.md). Pomůže vám se dozvědět o řízení přístupu a porozumět strategiích implementace návrhu, které doporučujeme pro vaši organizaci.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří účet Azure Automation, pracovní prostor Log Analytics a propojí účet Automation s pracovním prostorem.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Zadejte hodnoty.

3. Dokončení nasazení může trvat několik minut. Výstup je po dokončení podobný následujícímu:

    ![Příklad výsledku po dokončení nasazení](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V Azure Portal otevřete účet Automation, který jste právě vytvořili. 

3. V levém podokně vyberte **Runbooky**. Na stránce sady **Runbook** jsou uvedené tři výukové Runbooky vytvořené s účtem Automation.

    ![Výukové Runbooky vytvořené pomocí účtu Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. V levém podokně vyberte **propojený pracovní prostor**. Na stránce **propojený pracovní prostor** se zobrazuje Log Analytics pracovní prostor, který jste zadali dříve, propojený s vaším účtem Automation.

    ![Účet služby Automation propojený s pracovním prostorem Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, odpojte účet Automation z pracovního prostoru Log Analytics a potom účet Automation a pracovní prostor odstraňte.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Automation, Log Analytics pracovní prostor a propojili je společně.

Pokud se chcete dozvědět víc, přejděte k kurzům Azure Automation.

> [!div class="nextstepaction"]
> [Kurzy Azure Automation](learn/automation-tutorial-runbook-graphical.md)