---
title: Správa balíčků Python 2 v Azure Automation
description: V tomto článku se dozvíte, jak spravovat balíčky Python 2 v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0b0fab40eb95fabd4b74e809c9e6cd6f6573f513
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852901"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Správa balíčků Python 2 v Azure Automation

Azure Automation umožňuje spouštění Runbooků Pythonu 2 v Azure a na hybridních pracovních procesech Runbooku pro Linux. K usnadnění zjednodušení sad Runbook můžete použít balíčky python k importu modulů, které potřebujete. Tento článek popisuje, jak spravovat a používat balíčky Pythonu v Azure Automation.

## <a name="import-packages"></a>Import balíčků

Ve svém účtu Automation v části **sdílené prostředky**vyberte **balíčky Python 2** . Klikněte na **+ Přidat balíček Python 2**.

![Přidat balíček Pythonu](media/python-packages/add-python-package.png)

Na stránce přidat balíček Python 2 vyberte místní balíček, který se má nahrát. Balíček může být soubor **. WHL** nebo **. tar. gz** . Až se balíček vybere, klikněte na **OK** a nahrajte ho.

![Přidat balíček Pythonu](media/python-packages/upload-package.png)

Po importu balíčku se zobrazí na stránce balíčky Python 2 ve vašem účtu Automation. Pokud potřebujete odebrat balíček, vyberte balíček a klikněte na **Odstranit**.

![Seznam balíčků](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importovat balíčky se závislostmi

Azure Automation během procesu importu nevyřeší závislosti pro balíčky Pythonu. Existují dva způsoby, jak importovat balíček se všemi jeho závislostmi. K importu balíčků do svého účtu Automation je potřeba použít jenom jeden z následujících kroků.

### <a name="manually-download"></a>Ruční stažení

V počítači se systémem Windows 64, na kterém je nainstalována aplikace [Python 2.7](https://www.python.org/downloads/release/latest/python2) a [PIP](https://pip.pypa.io/en/stable/) , spusťte následující příkaz ke stažení balíčku a všech jeho závislostí:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Po stažení balíčků je můžete importovat do svého účtu Automation.

### <a name="runbook"></a>Runbook

 Pokud chcete sadu Runbook získat, [importujte balíčky Python 2 z PyPI do účtu Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) z Galerie do svého účtu Automation. Ujistěte se, že jsou nastavení spuštění nastavená na **Azure** , a spusťte Runbook pomocí parametrů. Sada Runbook vyžaduje účet Spustit jako, aby mohl účet služby Automation fungovat. U každého parametru se ujistěte, že ho spustíte s přepínačem, jak je vidět v následujícím seznamu a obrázku:

* -s\<subscriptionId\>
* – g\<resourceGroup\>
* -a\<automationAccount\>
* -m\<modulePackage\>

![Seznam balíčků](media/python-packages/import-python-runbook.png)

Sada Runbook umožňuje určit, který balíček se má stáhnout. Například použití `Azure` parametru stáhne všechny moduly Azure a všechny závislosti (přibližně 105).

Až se sada Runbook dokončí, můžete zkontrolovat **balíčky Python 2** v části **sdílené prostředky** v účtu Automation, abyste ověřili, jestli byl balíček správně naimportovaný.

## <a name="use-a-package-in-a-runbook"></a>Použití balíčku v Runbooku

Po importu balíčku ho můžete použít v Runbooku. Následující příklad používá [balíček Azure Automation Utility](https://github.com/azureautomation/azure_automation_utility). Tento balíček usnadňuje používání Pythonu s Azure Automation. Pokud chcete balíček použít, postupujte podle pokynů v úložišti GitHub a přidejte ho do Runbooku. Například můžete použít `from azure_automation_utility import get_automation_runas_credential` k importu funkce pro načtení účtu Spustit jako.

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

## <a name="develop-and-test-runbooks-offline"></a>Vývoj a testování runbooků offline

Chcete-li vyvíjet a testovat Runbooky v Pythonu 2 v režimu offline, můžete použít modul [Azure Automation emulovaných prostředků Pythonu](https://github.com/azureautomation/python_emulated_assets) na GitHubu. Tento modul vám umožní odkazovat na sdílené prostředky, jako jsou přihlašovací údaje, proměnné, připojení a certifikáty.

## <a name="next-steps"></a>Další kroky

Informace o přípravě sady Runbook v Pythonu najdete v tématu [Vytvoření Runbooku v Pythonu](learn/automation-tutorial-runbook-textual-python2.md).
