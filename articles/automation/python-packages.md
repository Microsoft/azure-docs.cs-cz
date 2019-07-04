---
title: Správa balíčků Python 2 ve službě Azure Automation
description: Tento článek popisuje, jak spravovat balíčky Python 2 ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f98b1454ff59eae62bcab7792fd7fd742babfb23
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478215"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Správa balíčků Python 2 ve službě Azure Automation

Azure Automation umožňuje spuštění sad runbook Python 2, v Azure a na Linuxu Hybrid Runbook Worker. Pomoci při zjednodušení sad runbook, můžete balíčky Pythonu pro import modulů, které potřebujete. Tento článek popisuje, jak spravovat a používat balíčky Pythonu ve službě Azure Automation.

## <a name="import-packages"></a>Import balíčků

Ve vašem účtu Automation vyberte **balíčků Python 2** pod **sdílené prostředky**. Klikněte na tlačítko **+ přidat balíček Python 2**.

![Přidat balíček Pythonu](media/python-packages/add-python-package.png)

Na **přidat balíček Python 2** vyberte místní balíček k nahrání. Balíček lze `.whl` souboru nebo `.tar.gz` souboru. Pokud je vybráno, kliknutím na **OK** pro nahrání balíčku.

![Přidat balíček Pythonu](media/python-packages/upload-package.png)

Po naimportování balíčku je uvedená na **balíčků Python 2** stránku ve svém účtu Automation. Pokud je potřeba odebrat balíček, vyberte balíček a zvolte **odstranit** na stránce balíček.

![Seznam balíčků](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importovat balíčky se závislostmi

Azure automation není během procesu importu vyřešení závislostí pro balíčky pythonu. Existují dva způsoby, jak importovat balíček se všemi jeho závislostmi. Pouze jeden z následujících kroků je potřeba použít k importu balíčky do vašeho účtu Automation.

### <a name="manually-download"></a>Ručně stáhnout.

Na Windows 64-bit počítače s [python2.7](https://www.python.org/downloads/release/latest/python2) a [pip](https://pip.pypa.io/en/stable/) nainstalovali, spusťte následující příkaz se stáhnout balíček a jeho závislosti:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Jakmile se stáhnou balíčky, můžete je importovat do účtu automation.

### <a name="runbook"></a>Runbook

Importovat python runbook [balíčků Python 2 importovat z pypi do účtu Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) z Galerie do účtu Automation. Ujistěte se, že parametry spuštění jsou nastaveny **Azure** a spuštění sady runbook s parametry. Sada runbook vyžaduje účet Spustit jako pro účet Automation pro práci. Pro každý parametr Ujistěte se, že spustíte ho s přepínačem jak je znázorněno v následujícím seznamu a bitové kopie:

* -s \<ID předplatného\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Seznam balíčků](media/python-packages/import-python-runbook.png)

Sada runbook umožňuje určit, co balíček ke stažení, třeba `Azure` (čtvrtého parametru) stáhne všechny moduly Azure a všem jeho závislostem, což je přibližně 105.

Po dokončení runbooku můžete zkontrolovat **balíčků Python 2** stránky **sdílené prostředky** ve vašem účtu Automation a ověřte, že balíček byl správně importovány.

## <a name="use-a-package-in-a-runbook"></a>Použití balíčku v sadě runbook

Po importu balíčku, můžete teď v sadě runbook. V následujícím příkladu [ balíček nástroje Azure Automation](https://github.com/azureautomation/azure_automation_utility). Tento balíček usnadňuje použití Pythonu s využitím Azure Automation. Pomocí balíčku, postupujte podle pokynů v úložišti GitHub a přidání do sady runbook pomocí `from azure_automation_utility import get_automation_runas_credential` třeba když chcete importovat funkce pro načtení účet Spustit jako.

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
