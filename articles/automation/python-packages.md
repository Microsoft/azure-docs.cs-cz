---
title: Správa balíčků Python 2 ve službě Azure Automation
description: Tento článek popisuje, jak spravovat balíčky Python 2 ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5974a8e622ca0969b2a7b5ee9500766ac95398c9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986291"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Správa balíčků Python 2 ve službě Azure Automation

Azure Automation umožňuje spuštění sad runbook Python 2, v Azure a na Linuxu Hybrid Runbook Worker. Pomoci při zjednodušení sad runbook, můžete balíčky Pythonu pro import modulů, které potřebujete. Tento článek popisuje, jak spravovat a používat balíčky Pythonu ve službě Azure Automation.

## <a name="import-packages"></a>Importovat balíčky

Ve vašem účtu Automation vyberte **balíčků Python 2** pod **sdílené prostředky**. Klikněte na tlačítko **+ přidat balíček Python 2**.

![Přidat balíček Pythonu](media/python-packages/add-python-package.png)

Na **přidat balíček Python 2** vyberte místní balíček k nahrání. Balíček lze `.whl` souboru nebo `.tar.gz` souboru. Pokud je vybráno, kliknutím na **OK** pro nahrání balíčku.

![Přidat balíček Pythonu](media/python-packages/upload-package.png)

Po naimportování balíčku je uvedená na **balíčků Python 2** stránku ve svém účtu Automation. Pokud je potřeba odebrat balíček, vyberte balíček a zvolte **odstranit** na stránce balíček.

![Seznam balíčků](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Použití balíčku v sadě runbook

Jakmile naimportujete balíček, můžete teď v sadě runbook. V následujícím příkladu [ balíček nástroje Azure Automation](https://github.com/azureautomation/azure_automation_utility). Tento balíček usnadňuje použití Pythonu s využitím Azure Automation. Pomocí balíčku, postupujte podle pokynů v úložišti GitHub a přidání do sady runbook pomocí `from azure_automation_utility import get_automation_runas_credential` třeba když chcete importovat funkce pro načtení účet Spustit jako.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Vývoj a testování sad runbook v režimu offline

Můžete vyvíjet a testovat svoje runbooky Python 2 do offline režimu, můžete použít [python Azure Automation emulován prostředky](https://github.com/azureautomation/python_emulated_assets) modulu na Githubu. Tento modul umožňuje odkazovat na sdílené prostředky, například přihlašovací údaje, proměnné, připojení a certifikáty.

## <a name="next-steps"></a>Další postup

Začínáme se sadami runbook Python 2, najdete v článku [Můj první runbook Python 2](automation-first-runbook-textual-python2.md)