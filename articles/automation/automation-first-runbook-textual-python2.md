---
title: Můj první runbook Pythonu ve službě Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého runbooku Python.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 13bb12c2c624bfd50933b624a28145172f521747
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427674"
---
# <a name="my-first-python-runbook"></a>Můj první runbook Pythonu

> [!div class="op_single_selector"]
> - [Grafický](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Tento kurz vás provede vytvořením [Python runbook](automation-runbook-types.md#python-runbooks) ve službě Azure Automation. Začnete s jednoduchým runbookem pro testování a publikování. Potom runbook upravíte, aby skutečně spravoval prostředky Azure, v tomto případě virtuální počítač Azure. Nakonec provedete runbook robustnější přidáním parametrů runbooku.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
- Virtuální počítač Azure. Tento počítač zastavíte a spustíte, proto to nesmí být produkční virtuální počítač.

## <a name="create-a-new-runbook"></a>Vytvořit nový runbook

Začněte vytvořením jednoduchého runbooku, který zobrazí text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina z těchto assetů jsou moduly, které jsou automaticky obsažené v novém účtu Automation. Také potřebujete asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).<br>

1. V části **SPRÁVA PROCESŮ** vyberte **Runbooky** a otevřete seznam runbooků.
1. Vyberte **+ přidat runbook** k vytvoření nové sady runbook.
1. Dejte runbooku název *MyFirstRunbook-Python*.
1. V takovém případě se chystáte vytvořit [Python runbook](automation-runbook-types.md#python-runbooks) proto vyberte **Python 2** pro **typ Runbooku**.
1. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="add-code-to-the-runbook"></a>Přidání kódu do runbooku

Nyní přidejte tomu jednoduchý příkaz Tisk textu "Hello World":

```python
print("Hello World!")
```

Klikněte na tlačítko **Uložit** pro sadu runbook uložte.

## <a name="test-the-runbook"></a>Otestování runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi **Koncept** a interaktivně zobrazíte jeho výsledek.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
1. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
1. Vytvoří se [úloha runbooku](automation-runbook-execution.md) a její stav se zobrazí.
   Počáteční stav úlohy bude *Zařazeno ve frontě*. To označuje, že čekáte na zpřístupnění pracovního procesu runbooku v cloudu. Přejde do *počáteční* když pracovní proces úlohu a potom *systémem* když sada runbook skutečně spustí, změní.
1. Po dokončení úlohy runbooku se zobrazí jeho výstup. V takovém případě byste měli vidět *Hello World*.
1. Zavřete testovací podokno a vraťte se na plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění sady runbook

Vytvořený runbook je stále v režimu konceptu. Je potřeba publikovat před spuštěním v produkčním prostředí.
Když runbook publikujete, přepíšete stávající publikované verze s verzi konceptu.
V takovém případě publikovanou verzi ještě nemáte protože jste runbook teprve vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Pokud se posunete doleva, abyste runbook viděli v **sady Runbook** podokně nyní zobrazuje **stav vytváření** z **publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-Python**.
   Možnosti v horní části nám umožňují spuštění runbooku, zobrazení runbooku, naplánování jeho spuštění někdy v budoucnu nebo vytvoření [webhooku](automation-webhooks.md), který umožní spuštění prostřednictvím volání protokolu HTTP.
1. Chcete spouštět sadu runbook, proto klikněte na **Start** a potom klikněte na tlačítko **Ok** po otevření okna spuštění Runbooku.
1. Podokno úlohy je otevřen pro úlohy runbooku, který jste vytvořili. v tomto podokně můžete zavřít, ale v takovém případě vám ho můžete nechat otevřený mohli sledovat průběh úlohy.
1. Stav úlohy se zobrazuje v **Souhrn úlohy** a odpovídá stavům, že jste viděli při testování runbooku.
1. Když se jako stav runbooku zobrazí *Dokončeno*, klikněte na **Výstup**. Otevře se podokno výstup a zobrazí se váš *Hello World*.
1. Zavřete podokno Výstup.
1. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Ve výstupním datovém proudu byste měli vidět jenom text *Hello World*, ale můžou se zobrazit i jiné datové proudy z úlohy runbooku, například Podrobný nebo Chyba, pokud do nich runbook zapisuje.
1. Zavřete podokno datové proudy a podokno úloha a vraťte se do podokna MyFirstRunbook-Python.
1. Kliknutím na **Úlohy** otevřete podokno Úlohy, které patří k tomuto runbooku. Vypíšou se všechny úlohy, které tento runbook vytvořil. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.
1. Na tuto úlohu můžete kliknout a otevřít podokno Úloha, které jste zobrazili při spuštění runbooku. Pomocí této možnosti se můžete vrátit v čase a zobrazit si podrobnosti libovolné úlohy, která byla pro konkrétní runbook vytvořena.

## <a name="add-authentication-to-manage-azure-resources"></a>Přidání ověřování ke správě prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure.
Ke správě prostředků Azure, tento skript má k ověření pomocí přihlašovacích údajů z vašeho účtu Automation. Abychom vám pomohli, můžete použít [balíček nástroje Azure Automation](https://github.com/azureautomation/azure_automation_utility) usnadnit k ověření a interakce s prostředky Azure.

> [!NOTE]
> Účet Automation musí být vytvořen s hlavní funkce služby existovat certifikát spustit jako.
> Pokud váš účet automation nebyl vytvořen pomocí instančního objektu, můžete se ověřit pomocí metody popsané v [ověřování pomocí knihovny správy Azure pro Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Kliknutím otevřete textový editor **upravit** v podokně MyFirstRunbook-Python.

2. Přidejte následující kód pro ověření do Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
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
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Přidejte kód k vytvoření klienta služby Python Compute a spuštění virtuálního počítače

Pro práci s virtuálními počítači Azure, vytvořte instanci [Azure Compute klienta pro Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Spuštění virtuálního počítače pomocí klienta výpočetní prostředky. Přidejte následující kód do sady runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Kde _MyResourceGroup_ je název skupiny prostředků, která obsahuje virtuální počítač, a _TestVM_ je název virtuálního počítače, které chcete spustit.

Testování a spuštění sady runbook znovu, abyste viděli, že začne tento virtuální počítač.

## <a name="use-input-parameters"></a>Používání vstupních parametrů

Sada runbook aktuálně používá pevně definovaných hodnot pro názvy skupiny prostředků a virtuální počítač.
Nyní Pojďme přidat kód, který načte tyto hodnoty ze vstupních parametrů.

Můžete použít `sys.argv` proměnné k získání hodnot parametrů.
Přidejte následující kód do runbooku ihned po druhé `import` příkazy:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Tento postup importuje `sys` modulu a vytvoří dvě proměnné pro uložení názvu skupiny prostředků a virtuální počítač.
Všimněte si, že element seznamu argumentů `sys.argv[0]`, je název skriptu a není uživatelský vstup.

Nyní můžete upravit poslední dva řádky, runbook použije parametr vstupní hodnoty místo pevně definovaných hodnot:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Při spuštění runbooku v Pythonu (buď na **testovací** stránky nebo jako publikované sady runbook), můžete zadat hodnoty pro parametry v **spustit Runbook** stránce v části **parametry** .

Po spuštění zadáním hodnoty v prvním poli sekundy se zobrazí a tak dále, tak, že podle potřeby můžete zadat libovolný počet hodnot parametrů.

Hodnoty budou dostupné do skriptu jako `sys.argv` pole jako v kódu, kterou jste právě přidali.

Zadejte název vaší skupiny prostředků jako hodnota pro první parametr a název virtuálního počítače spustit jako hodnotu druhého parametru.

![Zadejte hodnoty parametrů](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klikněte na tlačítko **OK** pro spuštění sady runbook. Sada runbook spuštěna a spouští se virtuální počítač, který jste zadali.

## <a name="next-steps"></a>Další postup

- První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
- První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
- První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
- Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
- Další informace o vývoji pro Azure pomocí Pythonu najdete v tématu [Azure pro vývojáře v Pythonu](https://docs.microsoft.com/python/azure/?view=azure-python)
- Pokud chcete zobrazit ukázkové sady runbook Python 2, najdete v článku [Azure Automation na Githubu](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
