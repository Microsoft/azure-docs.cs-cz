---
title: Správa balíčků Python 3 v Azure Automation
description: V tomto článku se dozvíte, jak spravovat balíčky python 3 (Preview) v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122030"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Správa balíčků Python 3 (Preview) v Azure Automation

Azure Automation umožňuje spustit sady Python 3 Runbooky (Preview) v Azure a procesy Hybrid Runbook Worker pro Linux. K usnadnění zjednodušení sad Runbook můžete použít balíčky python k importu modulů, které potřebujete. Postup importu jednoho balíčku najdete v tématu [Import balíčku](#import-a-package). Pokud chcete importovat balíček s více balíčky, přečtěte si téma [Import balíčku se závislostmi](#import-a-package-with-dependencies). Tento článek popisuje, jak spravovat a používat balíčky python 3 (Preview) v Azure Automation.

## <a name="import-a-package"></a>Importovat balíček

Ve svém účtu Automation v části **sdílené prostředky** vyberte **balíčky Pythonu** . Vyberte **+ Přidat balíček Pythonu**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Snímek obrazovky se stránkou s balíčky python 3 zobrazuje v levé nabídce balíčky python 3 a přidal se zvýrazněný balíček Python 2.":::

Na stránce **Přidat balíček python** vyberte pro verzi **Python 3** a vyberte místní balíček, který se má nahrát. Balíček může být soubor **. WHL** nebo **. tar. gz** . Po výběru balíčku vyberte **OK** a nahrajte ho.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Snímek obrazovky s vybraným souborem tar. gz zobrazí stránku přidat balíček python 3.":::

Po importu balíčku se na stránce balíčky Pythonu ve vašem účtu Automation zobrazí na kartě **balíčky python 3 (Preview)** . Pokud potřebujete odebrat balíček, vyberte balíček a klikněte na **Odstranit**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Po importu balíčku se na snímku obrazovky zobrazí stránka s balíčky python 3.":::

### <a name="import-a-package-with-dependencies"></a>Import balíčku se závislostmi

Balíček python 3 a jeho závislosti můžete importovat importem následujícího skriptu Pythonu do sady Runbook Python 3 a potom ho spustit.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Import skriptu do sady Runbook
Informace o importu Runbooku najdete v tématu [Import Runbooku z Azure Portal](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Zkopírujte soubor z GitHubu do úložiště, ke kterému má portál přístup, než spustíte import.

Ve výchozím nastavení se na stránce **importovat Runbook naformátuje** název sady Runbook tak, aby odpovídal názvu skriptu. Pokud máte přístup k poli, můžete název změnit. **Typ Runbooku** může být ve výchozím nastavení **Pythonu 2**. Pokud tomu tak je, nezapomeňte ji změnit na **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Snímek obrazovky se zobrazí na stránce pro import sady Runbook Python 3.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Spouští se Runbook pro Import balíčku a závislostí.

Po vytvoření a publikování Runbooku ho spusťte a naimportujte balíček. Podrobnosti o spuštění Runbooku najdete [v tématu Spuštění Runbooku v Azure Automation](start-runbooks.md) .

Skript ( `import_py3package_from_pypi.py` ) vyžaduje následující parametry.

| Parametr | Popis |
|---------------|-----------------|
|subscription_id | ID předplatného účtu Automation |
| resource_group | Název skupiny prostředků, ve které je definovaný účet Automation |
| automation_account | Název účtu Automation |
| module_name | Název modulu, ze kterého se má importovat `pypi.org` |

Další informace o použití parametrů v sadách Runbook naleznete v tématu [work with Runbook Parameters](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Použití balíčku v Runbooku

Po importu balíčku ho můžete použít v Runbooku. Přidejte následující kód pro výpis všech skupin prostředků v rámci předplatného Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Další kroky

Informace o přípravě sady Runbook v Pythonu najdete v tématu [Vytvoření Runbooku v Pythonu](learn/automation-tutorial-runbook-textual-python-3.md).
