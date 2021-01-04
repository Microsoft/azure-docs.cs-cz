---
title: Správa balíčků Python 3 v Azure Automation
description: V tomto článku se dozvíte, jak spravovat balíčky python 3 (Preview) v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734297"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Správa balíčků Python 3 (Preview) v Azure Automation

Azure Automation umožňuje spustit sady Python 3 Runbooky (Preview) v Azure a procesy Hybrid Runbook Worker pro Linux. K usnadnění zjednodušení sad Runbook můžete použít balíčky python k importu modulů, které potřebujete. Tento článek popisuje, jak spravovat a používat balíčky python 3 (Preview) v Azure Automation.

## <a name="import-packages"></a>Import balíčků

Ve svém účtu Automation v části **sdílené prostředky** vyberte **balíčky Pythonu** . Vyberte **+ Přidat balíček Pythonu**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Snímek obrazovky se stránkou s balíčky python 3 zobrazuje v levé nabídce balíčky python 3 a přidal se zvýrazněný balíček Python 2.":::

Na stránce **Přidat balíček python** vyberte pro **verzi** Python 3 a vyberte místní balíček, který se má nahrát. Balíček může být soubor **. WHL** nebo **. tar. gz** . Po výběru balíčku vyberte **OK** a nahrajte ho.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Snímek obrazovky s vybraným souborem tar. gz zobrazí stránku přidat balíček python 3.":::

Po importu balíčku se na stránce balíčky Pythonu ve vašem účtu Automation zobrazí na kartě **balíčky python 3 (Preview)** . Pokud potřebujete odebrat balíček, vyberte balíček a klikněte na **Odstranit**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Po importu balíčku se na snímku obrazovky zobrazí stránka s balíčky python 3.":::

## <a name="import-packages-with-dependencies"></a>Importovat balíčky se závislostmi

Azure Automation neřeší závislosti pro balíčky python během procesu importu. Existuje však způsob, jak importovat balíček se všemi jeho závislostmi.

### <a name="manually-download"></a>Ruční stažení

Na počítači se systémem Windows 64 s nainstalovanou [Python 3,8](https://www.python.org/downloads/release/python-380/) a [PIP](https://pip.pypa.io/en/stable/) spusťte následující příkaz ke stažení balíčku a všech jeho závislostí:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Po stažení balíčků je můžete importovat do svého účtu Automation.

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
