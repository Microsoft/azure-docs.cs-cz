---
title: Konfigurace vstupních parametrů Runbooku v Azure Automation
description: Tento článek obsahuje informace o tom, jak nakonfigurovat vstupní parametry Runbooku, které umožňují předávat data do sady Runbook při jejím spuštění.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 73e4dbb24b4e7c0c651f7d082c75b0f4a17158b5
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890877"
---
# <a name="configure-runbook-input-parameters"></a>Vstupní parametry runbooku

Vstupní parametry Runbooku zvyšují flexibilitu sady Runbook tím, že umožňují, aby se data předala do IT, když se spustí. Tyto parametry umožňují cílit na akce sady Runbook pro konkrétní scénáře a prostředí. Tento článek popisuje konfiguraci a použití vstupních parametrů v sadách Runbook.

Můžete nakonfigurovat vstupní parametry pro PowerShell, PowerShellový pracovní postup, grafické a Python Runbooky. Sada Runbook může mít více parametrů s různými datovými typy nebo žádné parametry vůbec. Vstupní parametry můžou být povinné nebo volitelné a můžete použít výchozí hodnoty pro volitelné parametry.

Přiřadíte hodnoty vstupním parametrům pro sadu Runbook při jejím spuštění. Runbook můžete spustit z Azure Portal, webové služby nebo PowerShellu. Můžete ho také spustit jako podřízený Runbook, který se označuje jako vložený do jiné sady Runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurace vstupních parametrů v sadách PowerShell Runbook

Runbooky pracovních postupů PowerShellu a PowerShellu v Azure Automation podporují vstupní parametry, které jsou definované prostřednictvím následujících vlastností. 

| **Vlastnost** | **Popis** |
|:--- |:--- |
| Typ |Povinná hodnota. Pro hodnotu parametru se očekával datový typ. Jakýkoli typ .NET je platný. |
| Name |Povinná hodnota. Název parametru Tento název musí být v rámci Runbooku jedinečný, musí začínat písmenem a může obsahovat jenom písmena, číslice nebo podtržítka. |
| Povinné |Nepovinný parametr. Logická hodnota určující, zda parametr vyžaduje hodnotu. Pokud nastavíte hodnotu true, musí se při spuštění Runbooku zadat hodnota. Pokud nastavíte hodnotu false, hodnota je nepovinná. Pokud nezadáte hodnotu `Mandatory` vlastnosti, PowerShell posuzuje vstupní parametr ve výchozím nastavení jako volitelný. |
| Výchozí hodnota |Nepovinný parametr. Hodnota, která se používá pro parametr, pokud není žádná vstupní hodnota předána při spuštění Runbooku. Sada Runbook může nastavit výchozí hodnotu pro libovolný parametr. |

Prostředí Windows PowerShell podporuje více atributů vstupních parametrů než výše uvedené, například ověřování, aliasy a sady parametrů. Azure Automation však aktuálně podporuje pouze uvedené vlastnosti vstupního parametru.

Jako příklad si podívejme na definici parametru v Runbooku pracovního postupu PowerShellu. Tato definice má následující obecný tvar, kde je více parametrů odděleno čárkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Teď nakonfigurujeme vstupní parametry pro Runbook pracovního postupu PowerShellu, který bude výstupem podrobností o virtuálních počítačích, buď k jednomu virtuálnímu počítači, nebo k virtuálním počítačům v rámci skupiny prostředků. Tento Runbook má dva parametry, jak je znázorněno na následujícím snímku obrazovky: název virtuálního počítače ( `VMName` ) a název skupiny prostředků ( `resourceGroupName` ).

![Pracovní postup PowerShellu pro automatizaci](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

V této definici parametru jsou vstupní parametry jednoduché parametry typu String.

Všimněte si, že Runbooky PowerShellu a PowerShellového pracovního postupu podporují všechny jednoduché typy a komplexní typy, například `Object` nebo `PSCredential` pro vstupní parametry. Pokud má sada Runbook vstupní parametr objektu, je nutné použít hodnotu hash prostředí PowerShell s páry název-hodnota k předání hodnoty. V sadě Runbook například máte následující parametr.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

V takovém případě můžete parametru předat následující hodnotu.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Pokud nepředáte hodnotu volitelného řetězcového parametru s výchozí hodnotou null, hodnota parametru je prázdný řetězec místo hodnoty null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurace vstupních parametrů v grafických sadách Runbook

Pro ilustraci konfigurace vstupních parametrů grafického Runbooku vytvoříme sadu Runbook, která bude výstupem podrobností o virtuálních počítačích, buď na jednom virtuálním počítači, nebo na všech virtuálních počítačích v rámci skupiny prostředků. Podrobnosti najdete v tématu [můj první grafický Runbook](./learn/automation-tutorial-runbook-graphical.md).

Grafický Runbook používá tyto hlavní aktivity sady Runbook:

* Konfigurace účtu Spustit jako pro Azure pro ověřování pomocí Azure 
* Definice rutiny [Get-AzVM](/powershell/module/az.compute/get-azvm) pro získání vlastností virtuálního počítače
* Použití aktivity [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) k výstupu názvů virtuálních počítačů. 

`Get-AzVM`Aktivita definuje dva vstupy, název virtuálního počítače a název skupiny prostředků. Vzhledem k tomu, že tyto názvy mohou být při spuštění sady Runbook odlišné, je nutné přidat do Runbooku vstupní parametry, abyste tyto vstupy přijímali. Informace najdete v tématu věnovaném [vytváření grafik v Azure Automation](automation-graphical-authoring-intro.md).

Při konfiguraci vstupních parametrů postupujte podle těchto kroků.

1. Vyberte grafický Runbook ze stránky Runbooky a pak klikněte na **Upravit**.
2. V grafickém editoru klikněte na tlačítko **vstup a výstup a** pak **Přidat vstup** . otevře se podokno vstupní parametr Runbooku.

   ![Grafický Runbook služby Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Ovládací prvek vstup a výstup zobrazuje seznam vstupních parametrů, které jsou definovány pro sadu Runbook. Zde můžete buď přidat nový vstupní parametr, nebo upravit konfiguraci existujícího vstupního parametru. Chcete-li přidat nový parametr pro sadu Runbook, klikněte na tlačítko **Přidat vstup** a otevřete okno **vstupní parametr Runbooku** , kde můžete nakonfigurovat parametry pomocí vlastností definovaných při [vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).

    ![Přidat nový vstup](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Vytvořte dva parametry s následujícími vlastnostmi, které má `Get-AzVM` aktivita používat, a pak klikněte na **OK**.

   * Parametr 1:
        * **Název**  --  **VMName**
        * **Typ** – řetězec
        * **Povinný**  --  **Ne**

   * Parametr 2:
        * **Název**  --  **resourceGroupName**
        * **Typ** – řetězec
        * **Povinný**  --  **Ne**
        * **Výchozí hodnota**  --  **Vlastní**
        * Vlastní výchozí hodnota – název skupiny prostředků, která obsahuje virtuální počítače

5. Zobrazení parametrů v ovládacím prvku vstup a výstup. 
6. Znovu klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit**.
7. Kliknutím na **publikovat** publikujte Runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurace vstupních parametrů v sadách Runbook sady Python

Na rozdíl od PowerShellu, pracovního postupu PowerShellu a grafických runbooků nepřijímá Runbooky v Pythonu pojmenované parametry. Editor runbooků provede analýzu všech vstupních parametrů jako pole hodnot argumentů. K poli se dostanete tak `sys` , že do skriptu Pythonu naimportujete modul a pak ho použijete `sys.argv` . Je důležité si uvědomit, že první prvek pole `sys.argv[0]` je název skriptu. Proto první skutečný vstupní parametr je `sys.argv[1]` .

Příklad použití vstupních parametrů v sadě Runbook Pythonu najdete [v tématu můj první Runbook v Pythonu v Azure Automation](./learn/automation-tutorial-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Přiřazení hodnot k vstupním parametrům v sadách Runbook

Tato část popisuje několik způsobů, jak předat hodnoty do vstupních parametrů v sadách Runbook. Hodnoty parametrů můžete přiřadit v těchto případech:

* [Spuštění runbooku](#start-a-runbook-and-assign-parameters)
* [Testování runbooku](#test-a-runbook-and-assign-parameters)
* [Propojit plán pro sadu Runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Vytvoření webhooku pro runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Spuštění Runbooku a přiřazení parametrů

Sadu Runbook lze spustit mnoha způsoby: prostřednictvím Azure Portal s webhookem s rutinami prostředí PowerShell s REST API nebo sadou SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Spuštění publikované sady Runbook pomocí Azure Portal a přiřazení parametrů

Po [Spuštění Runbooku](start-runbooks.md#start-a-runbook-with-the-azure-portal) v Azure Portal se otevře okno **Spustit Runbook** a můžete zadat hodnoty pro parametry, které jste vytvořili.

![Začněte používat portál.](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

V popisku pod vstupním polem můžete zobrazit vlastnosti, které byly nastaveny k definování atributů parametrů, například povinné nebo volitelné, typ, výchozí hodnota. V bublině nápovědy vedle názvu parametru jsou také definovány klíčové informace potřebné k rozhodnutí o vstupních hodnotách parametrů. 

> [!NOTE]
> Řetězcové parametry podporují prázdné hodnoty typu String. Zadáním `[EmptyString]` do pole vstupní parametr předá do parametru prázdný řetězec. Parametry řetězce nepodporují také hodnotu null. Pokud do parametru řetězce nepředáte žádnou hodnotu, PowerShell ho interpretuje jako null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Spuštění publikované sady Runbook pomocí rutin prostředí PowerShell a přiřazení parametrů

* **Rutiny Azure Resource Manager:** Můžete spustit Runbook služby Automation, který se vytvořil ve skupině prostředků, pomocí [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Rutiny modelu nasazení Azure Classic:** Můžete spustit Runbook služby Automation, který byl vytvořen ve výchozí skupině prostředků pomocí [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Když spustíte Runbook pomocí rutin PowerShellu, vytvoří se výchozí parametr `MicrosoftApplicationManagementStartedBy` s hodnotou `PowerShell` . Tento parametr můžete zobrazit v podokně podrobností úlohy.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Spuštění Runbooku pomocí sady SDK a přiřazení parametrů

* **Azure Resource Manager metoda:** Sadu Runbook můžete spustit pomocí sady SDK programovacího jazyka. Níže je fragment kódu jazyka C# pro spuštění sady Runbook ve vašem účtu Automation. Veškerý kód můžete zobrazit v našem [úložišti GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Metoda modelu nasazení Azure Classic:** Sadu Runbook můžete spustit pomocí sady SDK programovacího jazyka. Níže je fragment kódu jazyka C# pro spuštění sady Runbook ve vašem účtu Automation. Veškerý kód můžete zobrazit v našem [úložišti GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Chcete-li spustit tuto metodu, vytvořte slovník pro uložení parametrů Runbooku `VMName` a  `resourceGroupName` a jejich hodnoty. Poté spusťte sadu Runbook. Níže je fragment kódu jazyka C# pro volání metody, která je definována výše.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Spuštění Runbooku pomocí REST API a přiřazení parametrů

Můžete vytvořit a spustit úlohu Runbooku pomocí Azure Automation REST API pomocí `PUT` metody s následujícím identifikátorem URI požadavku: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

V identifikátoru URI požadavku nahraďte následující parametry:

* `subscriptionId`: ID vašeho předplatného Azure.  
* `resourceGroupName`: Název skupiny prostředků pro účet Automation.
* `automationAccountName`: Název účtu Automation, který je hostovaný v zadané cloudové službě.  
* `jobName`: Identifikátor GUID pro úlohu. Identifikátory GUID v PowerShellu se dají vytvořit pomocí `[GUID]::NewGuid().ToString()*` .

Chcete-li předat parametry do úlohy Runbooku, použijte text žádosti. Vybírá následující informace, které jsou k dispozici ve formátu JSON:

* Název Runbooku: povinné. Název Runbooku, který má úloha začít.  
* Parametry Runbooku: volitelné. Slovník seznamu parametrů ve formátu (název, hodnota), kde název je typu řetězec a hodnota, může být libovolná platná hodnota JSON.

Pokud chcete spustit sadu Runbook **Get-AzureVMTextual** vytvořenou dříve s `VMName` `resourceGroupName` parametrem a jako parametry, použijte pro tělo žádosti následující formát JSON.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Při úspěšném vytvoření úlohy se vrátí stavový kód HTTP 201. Další informace o hlavičkách odpovědí a textu odpovědi najdete v tématu [Vytvoření úlohy Runbooku pomocí REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Otestování Runbooku a přiřazení parametrů

Když [otestujete koncept verze Runbooku](./manage-runbooks.md) pomocí možnosti test, otevře se stránka Test. Pomocí této stránky můžete konfigurovat hodnoty pro parametry, které jste vytvořili.

![Test a přiřazení parametrů](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Připojení plánu k sadě Runbook a přiřazení parametrů

Plán můžete [propojit](./shared-resources/schedules.md) s vaší sadou Runbook, aby se Runbook spouštěl v určitou dobu. Vstupní parametry přiřadíte při vytváření plánu a sada Runbook tyto hodnoty používá, když ji spustí plán. Plán nemůžete uložit, dokud nebudou poskytnuty všechny povinné hodnoty parametrů.

![Naplánování a přiřazení parametrů](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Vytvoření Webhooku pro Runbook a přiřazení parametrů

Pro Runbook můžete vytvořit [Webhook](automation-webhooks.md) a nakonfigurovat vstupní parametry Runbooku. Webhook nemůžete uložit, dokud nebudou k dispozici všechny povinné hodnoty parametrů.

![Vytvoření Webhooku a přiřazení parametrů](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Při spuštění sady Runbook pomocí Webhooku se odešle předdefinovaný vstupní parametr `[WebhookData](automation-webhooks.md)` spolu se vstupními parametry, které definujete. 

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Předání objektu JSON do Runbooku

Může být užitečné ukládat data, která chcete předat Runbooku v souboru JSON. Můžete například vytvořit soubor JSON, který obsahuje všechny parametry, které chcete předat Runbooku. Chcete-li to provést, je nutné převést kód JSON na řetězec a poté převést řetězec na objekt prostředí PowerShell před předáním do sady Runbook.

V této části se používá příklad, ve kterém skript PowerShellu volá rutinu [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) , která spustí Runbook PowerShellu a předá obsah souboru JSON do Runbooku. PowerShellový Runbook spustí virtuální počítač Azure načtením parametrů pro virtuální počítač z objektu JSON.

### <a name="create-the-json-file"></a>Vytvoření souboru JSON

Zadejte následující kód do textového souboru a uložte ho jako **test.jsna** někde na místním počítači.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Vytvoření Runbooku

Vytvořte nový PowerShellový Runbook s názvem **test-JSON** v Azure Automation. Podívejte se na [můj první Runbook PowerShellu](./learn/automation-tutorial-runbook-textual-powershell.md).

Pro přijetí dat JSON musí sada Runbook převzít objekt jako vstupní parametr. Sada Runbook pak může použít vlastnosti definované v souboru JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Uložte a publikujte tuto sadu Runbook ve vašem účtu Automation.

### <a name="call-the-runbook-from-powershell"></a>Volání Runbooku z PowerShellu

Nyní můžete sadu Runbook volat z místního počítače pomocí Azure PowerShell. 

1. Přihlaste se k Azure, jak je znázorněno níže. Potom budete vyzváni k zadání přihlašovacích údajů Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Pro PowerShellové Runbooky `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro `Connect-AzAccount` . Všimněte si, že tyto aliasy nejsou k dispozici pro grafické Runbooky. Grafická sada Runbook může používat pouze `Connect-AzAccount` sebe sama.

1. Získá obsah uloženého souboru JSON a převede ho na řetězec. `JsonPath` Určuje cestu, kam jste uložili soubor JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Převeďte obsah řetězce `$json` na objekt prostředí PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Vytvořte zatřiďovací tabulku pro parametry pro `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Všimněte si, že nastavujete hodnotu `Parameters` objektu prostředí PowerShell, který obsahuje hodnoty ze souboru JSON.
1. Spusťte Runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Další kroky

* Pokud chcete připravit textový Runbook, přečtěte si téma [Úprava textových runbooků v Azure Automation](automation-edit-textual-runbook.md).
* Informace o přípravě grafického Runbooku najdete [v tématu vytváření grafických runbooků v Azure Automation](automation-graphical-authoring-intro.md).
