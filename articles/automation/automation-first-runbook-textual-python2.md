---
title: Můj první Runbook v Pythonu v Azure Automation
description: Kurz vás provede vytvořením, otestováním a publikováním jednoduchého Runbooku v Pythonu.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 65cd59933fa31d870a507cbe80b454934c9008d0
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265106"
---
# <a name="my-first-python-runbook"></a>Můj první Runbook sady Python

> [!div class="op_single_selector"]
> - [Grafický](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Pracovní postup PowerShellu](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Tento kurz vás provede vytvořením [Runbooku](automation-runbook-types.md#python-runbooks) v Pythonu v Azure Automation. Začnete s jednoduchým runbookm, který otestujete a publikujete. Potom runbook upravíte, aby skutečně spravoval prostředky Azure, v tomto případě virtuální počítač Azure. Nakonec uděláte robustnější sadu Runbook přidáním parametrů Runbooku.

> [!NOTE]
> Použití Webhooku ke spuštění Runbooku v Pythonu se nepodporuje.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Účet Automation](automation-offering-get-started.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
- Virtuální počítač Azure. Tento počítač zastavíte a spustíte, proto to nesmí být produkční virtuální počítač.

## <a name="create-a-new-runbook"></a>Vytvořit nový Runbook

Začnete vytvořením jednoduchého Runbooku, který vypíše text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina z těchto assetů jsou moduly, které jsou automaticky obsažené v novém účtu Automation. Také potřebujete asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).<br>

1. V části **SPRÁVA PROCESŮ** vyberte **Runbooky** a otevřete seznam runbooků.
1. Vyberte **+ Přidat Runbook** pro vytvoření nové sady Runbook.
1. Dejte Runbooku název *MyFirstRunbook-Python*.
1. V tomto případě vytvoříte [Runbook sady Python](automation-runbook-types.md#python-runbooks) , takže vyberte **Python 2** pro **typ Runbooku**.
1. Kliknutím na **Vytvořit** vytvoříte runbook a otevřete textový editor.

## <a name="add-code-to-the-runbook"></a>Přidat kód do Runbooku

Nyní přidáte jednoduchý příkaz pro vytištění textu "Hello World":

```python
print("Hello World!")
```

Kliknutím na **Save (Uložit** ) sadu Runbook uložte.

## <a name="test-the-runbook"></a>Otestování runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Když runbook testujete, spustíte jeho  verzi **Koncept** a interaktivně zobrazíte jeho výsledek.

1. Kliknutím na **Testovací podokno** otevřete testovací podokno.
1. Kliknutím na **Spustit** spustíte test. Měla by to být jediná povolená možnost.
1. Vytvoří se [úloha runbooku](automation-runbook-execution.md) a její stav se zobrazí.
   Počáteční stav úlohy bude *Zařazeno ve frontě*. To označuje, že čekáte na zpřístupnění pracovního procesu runbooku v cloudu. Přesune se ke *spuštění* , když pracovní proces tuto úlohu vyvolá a pak se spustí, když se Runbook skutečně spustí.
1. Po dokončení úlohy runbooku se zobrazí jeho výstup. V takovém případě byste měli vidět *Hello World*.
1. Zavřete testovací podokno a vraťte se na plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění Runbooku

Vytvořený Runbook je stále v režimu konceptu. Musíte ho publikovat, abyste ho mohli spustit v produkčním prostředí.
Když publikujete sadu Runbook, přepíšete existující publikovanou verzi verzí konceptu.
V tomto případě ještě nemáte publikovanou verzi, protože jste Runbook právě vytvořili.

1. Kliknutím na **Publikovat** runbook publikujte a po zobrazení výzvy klikněte na **Ano**.
1. Pokud se posunete doleva, abyste sadu Runbook zobrazili v podokně sady **Runbook** nyní, zobrazí se **stav vytváření obsahu** **Publikováno**.
1. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-Python**.
   Možnosti v horní části nám umožňují spuštění Runbooku, zobrazení Runbooku nebo naplánování jeho spuštění někdy v budoucnu.
2. Chcete spustit sadu Runbook, klikněte na tlačítko **Start** a poté klikněte na tlačítko **OK** , jakmile se otevře okno spustit Runbook.
3. Otevře se podokno úlohy pro vytvořenou úlohu Runbooku. Toto podokno můžete zavřít, ale v tomto případě ho necháte otevřené, abyste mohli sledovat průběh úlohy.
1. Stav úlohy se zobrazí v **souhrnu úlohy** a odpovídá stavům, které jste viděli při testování Runbooku.
2. Když se jako stav runbooku zobrazí *Dokončeno*, klikněte na **Výstup**. Otevře se podokno výstup a uvidíte své *Hello World*.
3. Zavřete podokno Výstup.
4. Klikněte na **Všechny protokoly** a otevřete podokno Datové proudy, které patří k úloze runbooku. Ve výstupním datovém proudu byste měli vidět jenom text *Hello World*, ale můžou se zobrazit i jiné datové proudy z úlohy runbooku, například Podrobný nebo Chyba, pokud do nich runbook zapisuje.
5. Zavřete podokno streamy a podokno úloh a vraťte se do podokna MyFirstRunbook-Python.
6. Kliknutím na **Úlohy** otevřete podokno Úlohy, které patří k tomuto runbooku. Vypíšou se všechny úlohy, které tento runbook vytvořil. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.
7. Na tuto úlohu můžete kliknout a otevřít podokno Úloha, které jste zobrazili při spuštění runbooku. Pomocí této možnosti se můžete vrátit v čase a zobrazit si podrobnosti libovolné úlohy, která byla pro konkrétní runbook vytvořena.

## <a name="add-authentication-to-manage-azure-resources"></a>Přidání ověřování pro správu prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure.
Ke správě prostředků Azure se musí skript ověřit pomocí přihlašovacích údajů z vašeho účtu Automation. K usnadnění ověřování a interakci s prostředky Azure můžete použít [balíček Azure Automation Utility](https://github.com/azureautomation/azure_automation_utility) .

> [!NOTE]
> Aby byl účet služby Automation vytvořen jako certifikát spustit jako, musí být vytvořen s funkcí instančního objektu.
> Pokud se Váš účet Automation nevytvořil s instančním objektem, můžete ověřit pomocí metody popsané v tématu [ověřování pomocí knihoven pro správu Azure pro Python](/azure/python/python-sdk-azure-authenticate).

1. Kliknutím na **Upravit** v podokně MyFirstRunbook-Python otevřete textový editor.

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Přidejte kód pro vytvoření klienta Python COMPUTE a spusťte virtuální počítač.

Pokud chcete pracovat s virtuálními počítači Azure, vytvořte instanci [klienta Azure COMPUTE pro Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Pomocí výpočetního klienta spusťte virtuální počítač. Do sady Runbook přidejte následující kód:

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

Kde _MyResourceGroup_ je název skupiny prostředků, která obsahuje virtuální počítač, a _TestVM_ je název virtuálního počítače, který chcete spustit.

Otestujte a znovu spusťte Runbook, abyste viděli, že virtuální počítač spouští.

## <a name="use-input-parameters"></a>Použití vstupních parametrů

Sada Runbook aktuálně používá pevně zakódované hodnoty pro názvy skupiny prostředků a virtuálního počítače.
Nyní přidáme kód, který získá tyto hodnoty ze vstupních parametrů.

`sys.argv` Proměnnou použijete k získání hodnot parametrů.
Do sady Runbook přidejte následující kód hned po ostatních `import` příkazech:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Tím se `sys` modul naimportuje a vytvoří dvě proměnné pro uchování skupiny prostředků a názvů virtuálních počítačů.
Všimněte si, že element seznamu argumentů, `sys.argv[0]`, je název skriptu a uživatel ho nezadá.

Nyní můžete upravit poslední dva řádky sady Runbook tak, aby místo použití pevně zakódovaných hodnot používaly hodnoty vstupního parametru:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Po spuštění sady Python Runbook (na stránce **test** nebo jako publikovaného Runbooku) můžete zadat hodnoty parametrů na stránce **Spustit Runbook** v části **parametry**.

Po zahájení zadání hodnoty v prvním poli se zobrazí druhý a tak dále, takže můžete zadat tolik hodnot parametrů, kolik je potřeba.

Hodnoty jsou k dispozici pro skript jako `sys.argv` pole jako v kódu, který jste právě přidali.

Jako hodnotu prvního parametru zadejte název vaší skupiny prostředků a název virtuálního počítače, který se má spustit jako hodnota druhého parametru.

![Zadejte hodnoty parametrů](media/automation-first-runbook-textual-python/runbook-python-params.png)

Spusťte sadu Runbook kliknutím na tlačítko **OK** . Sada Runbook se spustí a spustí vybraný virtuální počítač.

## <a name="next-steps"></a>Další kroky

- První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
- První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
- První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
- Další informace o typech runbooků, jejich výhodách a omezeních najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
- Další informace o vývoji pro Azure pomocí Pythonu najdete v tématu [Azure pro vývojáře](/azure/python/) v Pythonu.
- Pokud chcete zobrazit ukázkové sady Runbook Python 2, přečtěte si téma [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python) .
