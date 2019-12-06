---
title: Správa balíčků Python 2 v Azure Automation
description: Tento článek popisuje, jak spravovat balíčky Python 2 v Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f20865b92df2a197410f209cf921c5e573723286
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850189"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Správa balíčků Python 2 v Azure Automation

Azure Automation umožňuje spouštění Runbooků Pythonu 2 v Azure a na hybridních pracovních procesech Runbooku pro Linux. K usnadnění zjednodušení sad Runbook můžete použít balíčky python k importu modulů, které potřebujete. Tento článek popisuje, jak spravovat a používat balíčky Pythonu v Azure Automation.

## <a name="import-packages"></a>Import balíčků

Ve svém účtu Automation v části **sdílené prostředky**vyberte **balíčky Python 2** . Klikněte na **+ Přidat balíček Python 2**.

![Přidat balíček Pythonu](media/python-packages/add-python-package.png)

Na stránce **Přidat balíček Python 2** vyberte místní balíček, který se má nahrát. Balíček může být soubor `.whl` nebo `.tar.gz`. Pokud je tato možnost vybrána, kliknutím na tlačítko **OK** balíček nahrajte.

![Přidat balíček Pythonu](media/python-packages/upload-package.png)

Po importu balíčku se zobrazí na stránce **balíčky Python 2** ve vašem účtu Automation. Pokud potřebujete odebrat balíček, vyberte balíček a na stránce balíček zvolte **Odstranit** .

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

Importujte balíčky Pythonu pro [Import do Azure Automation účtu](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) z Galerie do účtu Automation. Ujistěte se, že jsou nastavení spuštění nastavená na **Azure** , a spusťte Runbook pomocí parametrů. Sada Runbook vyžaduje účet Spustit jako, aby mohl účet služby Automation fungovat. U každého parametru se ujistěte, že ho spustíte s přepínačem, jak je vidět v následujícím seznamu a obrázku:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Seznam balíčků](media/python-packages/import-python-runbook.png)

Sada Runbook umožňuje určit, který balíček se má stáhnout, například `Azure` (čtvrtý parametr) stáhne všechny moduly Azure a všechny jeho závislosti, což je přibližně 105.

Po dokončení sady Runbook můžete zkontrolovat stránku s **balíčky Python 2** v části **sdílené prostředky** ve vašem účtu Automation a ověřit tak, že byl balíček správně importován.

## <a name="use-a-package-in-a-runbook"></a>Použití balíčku v Runbooku

Po importu balíčku ho teď můžete použít v Runbooku. Následující příklad používá [balíček Azure Automation Utility](https://github.com/azureautomation/azure_automation_utility). Tento balíček usnadňuje používání Pythonu s Azure Automation. Chcete-li použít balíček, postupujte podle pokynů v úložišti GitHub a přidejte jej do sady Runbook pomocí `from azure_automation_utility import get_automation_runas_credential` například pro import funkce pro načtení účtu RunAs.

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

Chcete-li začít s Runbooky Python 2, přečtěte si [můj první Runbook sady Python 2](automation-first-runbook-textual-python2.md) .
