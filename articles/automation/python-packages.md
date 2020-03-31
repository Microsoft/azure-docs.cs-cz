---
title: Správa balíčků Pythonu 2 v Azure Automation
description: Tento článek popisuje, jak spravovat balíčky Pythonu 2 v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417647"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Správa balíčků Pythonu 2 v Azure Automation

Azure Automation umožňuje spouštět runbooky Pythonu 2 v Azure a na Linuxu hybridní runbook pracovníků. Chcete-li pomoci při zjednodušení runbooků, můžete použít balíčky Pythonu k importu modulů, které potřebujete. Tento článek popisuje, jak spravovat a používat balíčky Pythonu v Azure Automation.

## <a name="import-packages"></a>Import balíčků

V účtu Automation vyberte **balíčky Pythonu 2** v části **Sdílené prostředky**. Klikněte **na + Přidat balíček pythonu 2**.

![Přidat balíček Pythonu](media/python-packages/add-python-package.png)

Na stránce **Přidat balíček v Pythonu 2** vyberte místní balíček, který chcete nahrát. Balíček může `.whl` být `.tar.gz` soubor nebo soubor. Když je tato možnost vybrána, kliknutím na **OK** balíček nahrajete.

![Přidat balíček Pythonu](media/python-packages/upload-package.png)

Po importu balíčku je balíček uveden na stránce **balíčků Pythonu 2** ve vašem účtu Automation. Pokud potřebujete balíček odebrat, vyberte balíček a na stránce balíčku zvolte **Odstranit.**

![Seznam balíčků](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Import balíčků se závislostmi

Azure automation neřeší závislosti pro balíčky pythonu během procesu importu. Existují dva způsoby importu balíčku se všemi jeho závislostmi. K importu balíčků do účtu automation je třeba použít pouze jeden z následujících kroků.

### <a name="manually-download"></a>Ruční stažení

Na 64bitovém počítači se systémem Windows s nainstalovaným [jazykem Python2.7](https://www.python.org/downloads/release/latest/python2) a [pip](https://pip.pypa.io/en/stable/) spusťte následující příkaz ke stažení balíčku a všech jeho závislostí:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Po stažení balíčků je můžete importovat do svého účtu pro automatizaci.

### <a name="runbook"></a>Runbook

Importujte [balíčky Pythonu Import Pythonu 2 z pypi v Pythonu do účtu Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) z galerie do svého účtu Automation. Ujistěte se, že nastavení spuštění jsou nastaveny na **Azure** a spusťte runbook s parametry. Runbook vyžaduje spustit jako účet pro automatizační účet pracovat. Pro každý parametr se ujistěte, že jej spustíte přepínačem, jak je vidět v následujícím seznamu a obrázku:

* -s \<subscriptionId\>
* -g \<skupina zdrojů\>
* -a \<automationAccount\>
* -m \<modulBalíček\>

![Seznam balíčků](media/python-packages/import-python-runbook.png)

Runbook umožňuje určit, jaký balíček ke `Azure` stažení, například (čtvrtý parametr) stáhne všechny moduly Azure a všechny jeho závislosti, což je asi 105.

Po dokončení sady Runbook můžete zkontrolovat stránku **balíčků Pythonu 2** v části **Sdílené prostředky** ve vašem účtu automation a ověřit, že byl balíček importován správně.

## <a name="use-a-package-in-a-runbook"></a>Použití balíčku v runbooku

Po importu balíčku ho teď můžete použít v runbooku. Následující příklad používá [balíček nástrojů Azure Automation](https://github.com/azureautomation/azure_automation_utility). Tento balíček usnadňuje používání Pythonu s Azure Automation. Chcete-li použít balíček, postupujte podle pokynů v úložišti GitHub a přidejte jej do sady Runbook pomocí `from azure_automation_utility import get_automation_runas_credential` například k importu funkce pro načtení účtu RunAs.

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

K vývoji a testování runbooků pythonu 2 offline můžete použít modul [emulovaných datových zdrojů Pythonu Azure Automation](https://github.com/azureautomation/python_emulated_assets) na GitHubu. Tento modul umožňuje odkazovat na sdílené prostředky, jako jsou pověření, proměnné, připojení a certifikáty.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít s runbooky pythonu 2, najdete [v tématu Můj první runbook v Pythonu 2](automation-first-runbook-textual-python2.md)
