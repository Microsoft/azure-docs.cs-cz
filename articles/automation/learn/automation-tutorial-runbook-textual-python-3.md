---
title: Vytvoření Runbooku sady Python 3 (Preview) v Azure Automation
description: Tento článek vás seznámí s vytvořením, otestováním a publikováním jednoduchého Runbooku sady Python 3 (Preview).
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: tutorial
ms.openlocfilehash: cf028722c8c7174aac42f9485e31a599d7713ba3
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734058"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Kurz: vytvoření Runbooku sady Python 3 (Preview)

Tento kurz vás provede vytvořením [sady Runbook 3 v Pythonu](../automation-runbook-types.md#python-runbooks) (Preview) v Azure Automation. Runbooky Pythonu jsou kompilovány v Pythonu 2 a 3. Můžete přímo upravit kód sady Runbook pomocí textového editoru v Azure Portal.

> [!div class="checklist"]
> * Vytvoření jednoduché sady Runbook v Pythonu
> * Testování a publikování Runbooku
> * Spuštění a sledování stavu úlohy Runbooku
> * Aktualizace Runbooku pro spuštění virtuálního počítače Azure s parametry Runbooku

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

- Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Účet Automation](../automation-security-overview.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.

- Virtuální počítač Azure. Během tohoto kurzu zahájíte a zastavíte tento počítač, takže by neměl být produkční virtuální počítač.

- Můžete použít Hybrid Runbook Worker pro Linux nebo Windows ke spouštění sad Runbook 3 v Pythonu.Postupujte podle pokynů pro instalaci systému [Linux](../automation-linux-hrw-install.md)   nebo [Windows](../automation-windows-hrw-install.md)   Hybrid Runbook Worker.Pro Linux Hybrid Worker neexistují žádné zvláštní požadavky. Chcete-li však použít Hybrid Worker Windows pro Runbooky 3 v Pythonu 3, nakonfigurujte počítač hostující službu Runbook Worker na základě toho, jestli podporujete pouze Python 3, nebo pokud je potřeba Python 2 a 3 současně.

   * Pokud máte nainstalované *jenom* Python 2 nebo Python 3, musíte připojit cestu ke složce, ve které je python.exe umístěný do proměnné prostředí **path** . Pokud je například python.exe v instalační cestě `C:\Python` , pak musí být tato cesta připojena k proměnné prostředí **path** .

   * Pokud máte nainstalované Python 2 i Python 3 a chcete spustit oba typy sad Runbook, je nutné nakonfigurovat následující proměnné prostředí:

     * Python 2 – Vytvořte novou proměnnou prostředí s názvem `PYTHON_2_PATH` a zadejte instalační složku. Například pokud je instalační složka `C:\Python27` , pak musí být tato cesta přidána do proměnné.
     
     * Python 3 – vytvořte novou proměnnou prostředí s názvem `PYTHON_3_PATH` a zadejte instalační složku. Například pokud je instalační složka `C:\Python3` , pak musí být tato cesta přidána do proměnné.

## <a name="create-a-new-runbook"></a>Vytvořit nový Runbook

Začnete vytvořením jednoduchého Runbooku, který vypíše text *Hello World*.

1. Na webu Azure Portal otevřete účet Automation.

    Stránka účtu Automation nabízí rychlý přehled prostředků v tomto účtu. Už byste tam měli mít nějaké prostředky. Většina z těchto assetů jsou moduly, které jsou automaticky obsažené v novém účtu Automation. Také potřebujete asset přihlašovacích údajů, který je uvedený v [požadavcích](#prerequisites).

2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.

3. Vyberte **Přidat Runbook** pro vytvoření nové sady Runbook.

4. Dejte Runbooku název **MyFirstRunbook-Python**.

5. Jako **typ Runbooku** vyberte **Python 3** .

6. Vyberte **vytvořit** a vytvořte Runbook a otevřete textový editor.

## <a name="add-code-to-the-runbook"></a>Přidat kód do Runbooku

Nyní přidáte jednoduchý příkaz pro vytištění textu `Hello World` .

```python
print("Hello World!")
```

Vyberte **Save (Uložit** ) a sadu Runbook uložte.

## <a name="test-the-runbook"></a>Otestování runbooku

Před publikováním runbooku, které ho zpřístupní v produkčním prostředí, byste měli runbook otestovat a ujistit se, že funguje správně. Při testování Runbooku se spustí jeho verze konceptu a interaktivně se zobrazí jeho výstup.

1. Vyberte **podokno test** a otevřete tak podokno **test** .

2. Výběrem **Spustit** spusťte test. Měla by to být jediná povolená možnost.

3. Vytvoří se [úloha runbooku](../automation-runbook-execution.md) a její stav se zobrazí.
   Stav úlohy se spustí jako **zařazený do fronty**, což značí, že čekáte na zpřístupnění pracovního procesu Runbooku v cloudu. Změny se **spustí** , když pracovní proces tuto úlohu vyvolá **a pak se spustí,** když se Runbook skutečně spustí.

4. Po dokončení úlohy runbooku se zobrazí jeho výstup. V takovém případě byste měli vidět `Hello World` .

5. Zavřete **testovací** podokno a vraťte se na plátno.

## <a name="publish-and-start-the-runbook"></a>Publikování a spuštění Runbooku

Vytvořený Runbook je stále v režimu konceptu. Musíte ho publikovat, abyste ho mohli spustit v produkčním prostředí. Když publikujete sadu Runbook, přepíšete existující publikovanou verzi verzí konceptu. V tomto případě ještě nemáte publikovanou verzi, protože jste Runbook právě vytvořili.

1. Vyberte **publikovat** a publikujte Runbook a po zobrazení výzvy klikněte na **Ano** .

2. Pokud se posunete doleva, abyste sadu Runbook zobrazili na stránce sady **Runbook** , měli byste vidět **stav vytváření obsahu** **Publikováno**.

3. Posuňte se zpět doprava, abyste viděli podokno **MyFirstRunbook-python3**.

   Možnosti v horní části umožňují spuštění Runbooku, zobrazení Runbooku nebo naplánování jeho spuštění v budoucnu.

4. Vyberte možnost **Start** a poté vyberte možnost **OK** , jakmile se otevře podokno **Spustit Runbook** .

5. Otevře se podokno **úlohy** pro vytvořenou úlohu Runbooku. Toto podokno můžete zavřít, ale nechte otevřené, abyste mohli sledovat průběh úlohy.

6. Stav úlohy se zobrazí v **souhrnu úlohy** a odpovídá stavům, které jste viděli při testování Runbooku.

7. Jakmile se stav Runbooku zobrazí **dokončeno**, vyberte **výstup**. Otevře se podokno **výstup** , kde vidíte `Hello World` .

8. Zavřete podokno **výstup** .

9. Výběrem **všech protokolů** otevřete podokno **datové proudy** pro úlohu Runbooku. Měla by se zobrazit jenom `Hello World` ve výstupním datovém proudu. Toto podokno však může zobrazit další datové proudy pro úlohu Runbooku, jako je například **verbose** a **Chyba**, pokud je sada Runbook zapisuje do nich.

10. Zavřete podokno **streamy** a podokno **úloh** a vraťte se do podokna **MyFirstRunbook-python3** .

11. Vyberte **úlohy** a otevřete stránku **úlohy** pro tuto sadu Runbook. Tato stránka obsahuje seznam všech úloh vytvořených pomocí tohoto Runbooku. Ve výpisu by se měla zobrazit pouze jedna úloha, protože jste ji spustili jenom jednou.

12. Tuto úlohu můžete vybrat, chcete-li otevřít stejné podokno **úloh** , které jste zobrazili při spuštění Runbooku. V tomto podokně se můžete vrátit v čase a zobrazit podrobnosti o všech úlohách, které byly vytvořeny pro konkrétní sadu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Přidání ověřování pro správu prostředků Azure

Runbook jste otestovali a publikovali, ale zatím nedělá nic užitečného. Chcete po něm, aby spravoval prostředky Azure.
Aby bylo možné tento postup provést, musí se skript ověřit pomocí přihlašovacích údajů z vašeho účtu Automation.

> [!NOTE]
> Aby byl účet služby Automation vytvořen jako certifikát spustit jako, musí být vytvořen s funkcí instančního objektu.
> Pokud se Váš účet Automation nevytvořil s instančním objektem, můžete ověřit, jak je popsáno v tématu [ověřování pomocí knihoven pro správu Azure pro Python](/azure/python/python-sdk-azure-authenticate).

1. Kliknutím na **Upravit** v podokně **MyFirstRunbook-python3** otevřete textový editor.

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

Pokud chcete pracovat s virtuálními počítači Azure, vytvořte instanci [klienta Azure COMPUTE pro Python](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

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

Kde `MyResourceGroup` je název skupiny prostředků, která obsahuje virtuální počítač, a `TestVM` je název virtuálního počítače, který chcete spustit.

Otestujte a znovu spusťte Runbook, abyste viděli, že virtuální počítač spouští.

## <a name="use-input-parameters"></a>Použití vstupních parametrů

Sada Runbook aktuálně používá pevně zakódované hodnoty pro názvy skupiny prostředků a virtuálního počítače. Nyní přidáme kód, který získá tyto hodnoty ze vstupních parametrů.

Proměnnou použijete `sys.argv` k získání hodnot parametrů. Do sady Runbook přidejte následující kód hned po ostatních `import` příkazech:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Tím se modul naimportuje `sys` a vytvoří dvě proměnné pro uchování skupiny prostředků a názvů virtuálních počítačů. Všimněte si, že element seznamu argumentů, `sys.argv[0]` , je název skriptu a uživatel ho nezadá.

Nyní můžete upravit poslední dva řádky sady Runbook tak, aby místo použití pevně zakódovaných hodnot používaly hodnoty vstupního parametru:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Když spustíte Runbook v Pythonu, buď z podokna **testu** , nebo jako publikovaný Runbook, můžete zadat hodnoty parametrů na stránce **Spustit Runbook** v části **parametry**.

Po zahájení zadávání hodnoty v prvním poli se zobrazí druhý a tak dále, takže můžete zadat tolik hodnot parametrů, kolik je potřeba.

Hodnoty jsou k dispozici pro skript v `sys.argv` poli, jako v kódu, který jste právě přidali.

Jako hodnotu prvního parametru zadejte název vaší skupiny prostředků a název virtuálního počítače, který se má spustit jako hodnota druhého parametru.

![Zadejte hodnoty parametrů](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Vyberte **OK** a spusťte sadu Runbook. Sada Runbook se spustí a spustí vybraný virtuální počítač.

## <a name="error-handling-in-python"></a>Zpracování chyb v Pythonu

Následující konvence můžete použít také k načtení různých datových proudů z runbooků v Pythonu, včetně upozornění, chyby a ladění datových proudů.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Následující příklad ukazuje tuto konvenci použitou v `try...except` bloku.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Další kroky

- Další informace o typech runbooků, jejich výhodách a omezeních najdete v tématu [Azure Automation typy runbooků](../automation-runbook-types.md).

- Další informace o vývoji pro Azure pomocí Pythonu najdete v tématu [Azure pro vývojáře v Pythonu](/azure/python/).

- Pokud chcete zobrazit ukázkové sady Runbook Python 3, přečtěte si téma [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
