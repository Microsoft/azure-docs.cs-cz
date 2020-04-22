---
title: Vytvoření runbooku Pythonu v Azure Automation
description: Kurz, který ukazuje, jak vytvořit, otestovat a publikovat jednoduchý runbook Pythonu.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 116f2544b23ed5f9bc0fabbb945cb5cb2b51af96
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726281"
---
# <a name="tutorial-create-a-python-runbook"></a>Kurz: Vytvoření runbooku pythonu

Tento kurz vás provede vytvořením [runbooku Pythonu](../automation-runbook-types.md#python-runbooks) v Azure Automation. Runbooky pythonu se kompilují v Pythonu 2. Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal.

> [!div class="checklist"]
> * Vytvoření jednoduchého runbooku pythonu
> * Testování a publikování runbooku
> * Spuštění a sledování stavu úlohy runbooku
> * Aktualizace sady Runbook pro spuštění virtuálního počítače Azure s parametry sady Runbook

> [!NOTE]
> Použití webhooku ke spuštění runbooku Pythonu není podporováno.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Účet Automation](../automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
- Virtuální počítač Azure. Tento počítač zastavíte a spustíte, proto to nesmí být produkční virtuální počítač.

## <a name="create-a-new-runbook"></a>Vytvoření nového runbooku

Začnete vytvořením jednoduchého runbooku, který vypíše text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina z těchto assetů jsou moduly, které jsou automaticky obsažené v novém účtu Automation. Také potřebujete asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).

2. Vyberte **Runbooky** v části **Automatizace procesů,** chcete-li otevřít seznam runbooků.

3. Vyberte **Přidat runbook** a vytvořte novou runbook.

4. Pojmenujte runbook **MyFirstRunbook-Python**.

5. Vyberte **Python 2** pro **typ sady Runbook**.

6. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="add-code-to-the-runbook"></a>Přidání kódu do runbooku

Nyní přidáte jednoduchý příkaz pro `Hello World`tisk textu .

```python
print("Hello World!")
```

Chcete-li soubor Runbook uložit, klepněte na tlačítko **Uložit.**

## <a name="test-the-runbook"></a>Otestování runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi Koncept a interaktivně zobrazíte jeho výsledek.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.

2. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.

3. Vytvoří se [úloha runbooku](../automation-runbook-execution.md) a její stav se zobrazí.
   Stav úlohy začíná jako ve frontě, což znamená, že čeká na pracovníka runbooku v cloudu, aby byl k dispozici. Přesune se na Spuštění, když pracovník nároky úlohy a potom Spuštění při spuštění runbooku skutečně spustí.

4. Po dokončení úlohy runbooku se zobrazí jeho výstup. V tomto případě byste `Hello World`měli vidět .

5. Zavřete testovací podokno a vraťte se na plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění runbooku

Vytvořený runbook je stále v režimu konceptu. Musíte publikovat před spuštěním v produkčním prostředí.
Při publikování runbooku přepíšete existující publikovanou verzi konceptovou verzí.
V takovém případě ještě nemáte publikovanou verzi, protože jste právě vytvořili runbook.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.

2. Pokud se posunete **doleva**a zobrazíte runbook na stránce **Runbook,** měl by se zobrazit **stav publikování vytváření** .

3. Přejděte zpět doprava a zobrazte podokno **myfirstrunbook-python**.

   Možnosti v horní části umožňují spustit runbook, zobrazit runbook nebo naplánovat jeho spuštění někdy v budoucnu.

4. Po otevření okna Spustit runbook klikněte na **Start** a potom na **OK.**

5. Pro úlohu runbooku, kterou jste vytvořili, se otevře podokno úlohy. Toto podokno můžete zavřít, ale nechte ho otevřené, abyste mohli sledovat průběh úlohy.

6. Stav úlohy se zobrazí ve **shrnutí úlohy** a odpovídá stavy, které jste viděli při testování runbooku.

7. Jakmile se stav runbooku zobrazí Dokončeno, klikněte na **Výstup**. Otevře se podokno Výstup, `Hello World`ve kterém je vidět .

8. Zavřete podokno Výstup.

9. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Měli byste `Hello World` vidět pouze ve výstupním datovém proudu. Toto podokno však může zobrazit další datové proudy pro úlohu runbooku, jako je například Verbose a Error, pokud jim runbook zapíše.

10. Zavřete podokno Datové proudy a podokno úloh a vraťte se do podokna MyFirstRunbook-Python.

11. Kliknutím na **Úlohy** otevřete stránku Úlohy pro tento runbook. Na této stránce jsou uvedeny všechny úlohy vytvořené tímto souborem runbook. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.

12. Na tuto úlohu můžete kliknout a otevřít podokno Úloha, které jste zobrazili při spuštění runbooku. Toto podokno umožňuje vrátit se v čase a zobrazit podrobnosti o jakékoli úloze, která byla vytvořena pro konkrétní runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Přidání ověřování pro správu prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure.
Chcete-li to provést, skript musí ověřit pomocí pověření z účtu automatizace. Abyste vám pomohli, můžete použít [balíček nástrojů Azure Automation,](https://github.com/azureautomation/azure_automation_utility) který vám usnadní ověřování a interakci s prostředky Azure.

> [!NOTE]
> Účet automatizace musí být vytvořeny s funkcí zaregistrovaný objekt služby pro existovat spustit jako certifikát.
> Pokud váš účet Automation nebyl vytvořen pomocí instančního objektu, můžete se ověřit, jak je popsáno v [části Ověření pomocí knihoven správy Azure pro Python](/azure/python/python-sdk-azure-authenticate).

1. Otevřete textový editor kliknutím na **Upravit** v podokně MyFirstRunbook-Python.

2. Přidejte následující kód k ověření do Azure:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Přidání kódu k vytvoření klienta Python Compute a spuštění virtuálního počítače

Pokud chcete pracovat s virtuálními počítači Azure, vytvořte instanci [klienta Azure Compute pro Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Pomocí výpočetního klienta spusťte virtuální počítač. Přidejte do runbooku následující kód:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Kde `MyResourceGroup` je název skupiny prostředků, která obsahuje `TestVM` virtuální hod a je název virtuálního_

Vyzkoušejte a spusťte znovu runbook, abyste viděli, že spustí virtuální hod.

## <a name="use-input-parameters"></a>Použití vstupních parametrů

Runbook aktuálně používá pevně zakódované hodnoty pro názvy skupiny prostředků a virtuálního počítačů. Nyní přidáme kód, který získá tyto hodnoty ze vstupních parametrů.

Proměnná `sys.argv` slouží k získání hodnot parametrů. Přidejte následující kód do runbooku `import` bezprostředně za ostatní příkazy:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Tím se `sys` modul importuje a vytvoří se dvě proměnné pro názvy skupin y prostředků a virtuálních počítačů. Všimněte si, že prvek `sys.argv[0]`seznamu argumentů , je název skriptu a není zadán uživatelem.

Nyní můžete upravit poslední dva řádky runbooku tak, aby používaly hodnoty vstupních parametrů namísto použití pevně zakódovaných hodnot:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Když spustíte runbook pythonu (buď v podokně Test, nebo jako publikovaný runbook), můžete zadat hodnoty parametrů na stránce Start Runbook v části **Parametry**.

Po zadání hodnoty do prvního pole se zobrazí druhá a tak dále, takže můžete zadat tolik hodnot parametrů podle potřeby.

Hodnoty jsou k dispozici pro `sys.argv` skript v poli jako v kódu, který jste právě přidali.

Zadejte název skupiny prostředků jako hodnotu pro první parametr a název virtuálního počítače, který má být zahájen jako hodnota druhého parametru.

![Zadat hodnoty parametrů](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Klepnutím na **tlačítko OK** spusťte runbook. Runbook spustí a spustí virtuální hod, který jste zadali.

## <a name="error-handling-in-python"></a>Zpracování chyb v Pythonu

Můžete také použít následující konvence k načtení různých datových proudů z runbooků Pythonu, včetně upozornění, chyba a debug datových proudů.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Následující příklad ukazuje tuto konvenci použitou `try...except` v bloku.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure Automation nepodporuje `sys.stderr`.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít pracovat s runbooky prostředí PowerShell, [přečtěte si informace o vytvoření sady Runbook prostředí PowerShell](automation-tutorial-runbook-textual-powershell.md).
- Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Vytvoření grafického seznamu runbooků](automation-tutorial-runbook-graphical.md).
- Pokud chcete začít pracovat s runbooky pracovního postupu Prostředí PowerShell, přečtěte si informace [o vytvoření sady Runbook pracovního postupu PowerShellu](automation-tutorial-runbook-textual.md).
- Další informace o typech runbooků, jejich výhodách a omezeních najdete v [tématu Typy runbooků Azure Automation](../automation-runbook-types.md).
- Další informace o vývoji pro Azure s Pythonem najdete v [tématu Vývojáři Azure pro Python](/azure/python/).
- Ukázkové runbooky pythonu 2 najdete v [tématu GitHub Azure Automation](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
