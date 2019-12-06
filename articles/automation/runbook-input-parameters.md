---
title: Vstupní parametry runbooků
description: Vstupní parametry Runbooku zvyšují flexibilitu runbooků tím, že vám umožní předat data do Runbooku při jeho spuštění. Tento článek popisuje různé scénáře použití vstupních parametrů v sadách Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be7d244f5aa422b2083d35fc56a52318a4379b79
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850223"
---
# <a name="runbook-input-parameters"></a>Vstupní parametry runbooků

Vstupní parametry Runbooku zvyšují flexibilitu runbooků tím, že vám umožní předat data do IT, když se spustí. Parametry umožňují, aby se akce sady Runbook zaměřily na konkrétní scénáře a prostředí. V tomto článku vás provedete různými scénáři, kdy se vstupní parametry používají v sadách Runbook.

## <a name="configure-input-parameters"></a>Konfigurace vstupních parametrů

Vstupní parametry se dají konfigurovat v PowerShellu, pracovním postupu PowerShellu, Pythonu a grafických sadách Runbook. Sada Runbook může mít více parametrů s různými datovými typy nebo žádné parametry vůbec. Vstupní parametry můžou být povinné nebo volitelné a můžete mít výchozí hodnotu pro volitelné parametry. Hodnoty vstupním parametrům pro sadu Runbook přiřadíte, když ji spustíte prostřednictvím jedné z dostupných metod. Mezi tyto metody patří spuštění sady Runbook z Azure Portal, webové služby nebo prostředí PowerShell. Můžete ho také spustit jako podřízený Runbook, který se označuje jako vložený do jiné sady Runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurace vstupních parametrů v sadách PowerShell Runbook

Runbooky pracovních postupů PowerShellu a PowerShellu v Azure Automation podporují vstupní parametry, které jsou definované pomocí následujících atributů:  

| **Vlastnost** | **Popis** |
|:--- |:--- |
| `Type` |Povinná hodnota. Pro hodnotu parametru se očekával datový typ. Jakýkoli typ .NET je platný. |
| `Name` |Povinná hodnota. Název parametru Toto musí být v rámci Runbooku jedinečné a může obsahovat jenom písmena, číslice nebo podtržítka. Musí začínat písmenem. |
| `Mandatory` |Volitelné. Určuje, zda musí být pro parametr zadána hodnota. Pokud tuto hodnotu nastavíte na **\$true**, musí se při spuštění Runbooku zadat hodnota. Pokud nastavíte hodnotu **\$false**, hodnota je volitelná. |
| `Default value` |Volitelné. Určuje hodnotu, která se používá pro parametr, pokud hodnota není předána při spuštění Runbooku. Pro libovolný parametr se dá nastavit výchozí hodnota a automaticky se použije parametr Optional bez ohledu na povinné nastavení. |

Prostředí Windows PowerShell podporuje více atributů vstupních parametrů než těch, které jsou zde uvedeny, jako jsou ověřování, aliasy a sady parametrů. Azure Automation však aktuálně podporuje pouze předchozí vstupní parametry.

Definice parametru ve runbookích pracovního postupu PowerShellu má následující obecný tvar, kde je více parametrů odděleno čárkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Při definování parametrů, pokud nezadáte **povinný** atribut, je ve výchozím nastavení parametr považován za volitelný. Také Pokud nastavíte výchozí hodnotu pro parametr v sadách PowerShell Workflow Runbook, je zpracována PowerShellem jako volitelný parametr bez ohledu na **povinnou** hodnotu atributu.

Jako příklad můžete nakonfigurovat vstupní parametry pro Runbook pracovního postupu PowerShellu, který bude výstupem podrobností o virtuálních počítačích, buď na jednom virtuálním počítači, nebo na všech virtuálních počítačích v rámci skupiny prostředků. Tento Runbook má dva parametry, jak je znázorněno na následujícím snímku obrazovky: název virtuálního počítače a název skupiny prostředků.

![Pracovní postup PowerShellu pro automatizaci](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

V této definici parametru jsou parametry **\$VMName** a **\$resourceGroupName** jednoduché parametry typu String. Ale PowerShell a runbooky pracovních postupů PowerShellu podporují všechny jednoduché typy a komplexní typy, jako je například **Object** nebo **PSCredential** pro vstupní parametry.

Pokud má sada Runbook vstupní parametr typu objektu, použijte páry PowerShellu s (název, hodnota) a předejte hodnotu. Například pokud máte v Runbooku následující parametr:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Pak můžete parametru předat následující hodnotu:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Když nepředáte žádnou hodnotu do volitelného parametru `[String]` typu, který má _výchozí hodnotu_ `\$null`, bude hodnota tohoto parametru _prázdný řetězec_, **nikoli** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurace vstupních parametrů v grafických sadách Runbook

Pro [konfiguraci grafického Runbooku](automation-first-runbook-graphical.md) se vstupními parametry vytvoříme grafický Runbook, který bude výstupem podrobností o virtuálních počítačích, buď na jednom virtuálním počítači, nebo na všech virtuálních počítačích v rámci skupiny prostředků. Konfigurace sady Runbook se skládá ze dvou hlavních aktivit, jak je popsáno níže.

[**Ověřování runbooků pomocí účtu Spustit jako pro Azure**](automation-sec-configure-azure-runas-account.md) pro ověřování pomocí Azure.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) pro získání vlastností virtuálního počítače.

Aktivitu [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) můžete použít k výstupu názvů virtuálních počítačů. Aktivita **Get-AzureRmVm** přijímá dva parametry, **název virtuálního počítače** a **název skupiny prostředků**. Vzhledem k tomu, že tyto parametry mohou při každém spuštění sady Runbook vyžadovat jiné hodnoty, můžete do sady Runbook přidat vstupní parametry. Tady je postup pro přidání vstupních parametrů:

1. V okně **Runbooky** vyberte grafický Runbook a pak klikněte na [**Upravit**](automation-graphical-authoring-intro.md) .
2. V editoru runbooků klikněte na **vstup a výstup** , aby se otevřelo okno pro **vstup a výstup** .

   ![Grafický Runbook služby Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Okno **vstup a výstup** zobrazuje seznam vstupních parametrů, které jsou definovány pro sadu Runbook. V tomto okně můžete buď přidat nový vstupní parametr, nebo upravit konfiguraci existujícího vstupního parametru. Chcete-li přidat nový parametr pro sadu Runbook, klikněte na tlačítko **Přidat vstup** a otevřete okno **vstupní parametr Runbooku** . Tady můžete nakonfigurovat následující parametry:

   | **Vlastnost** | **Popis** |
   |:--- |:--- |
   | `Name` |Povinná hodnota. Název parametru Toto musí být v rámci Runbooku jedinečné a může obsahovat jenom písmena, číslice nebo podtržítka. Musí začínat písmenem. |
   | `Description` |Volitelné. Popis účelu vstupního parametru |
   | `Type` |Volitelné. Datový typ, který se očekává pro hodnotu parametru. Podporované typy parametrů jsou **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime**a **Object**. Pokud není vybraný datový typ, použije se výchozí hodnota **String (řetězec**). |
   | `Mandatory` |Volitelné. Určuje, zda musí být pro parametr zadána hodnota. Zvolíte-li možnost **Ano**, je nutné při spuštění sady Runbook zadat hodnotu. Pokud zvolíte **ne**, hodnota se při spuštění Runbooku nepožaduje a může se nastavit výchozí hodnota. |
   | `Default Value` |Volitelné. Určuje hodnotu, která se používá pro parametr, pokud hodnota není předána při spuštění Runbooku. Výchozí hodnotu lze nastavit pro parametr, který není povinný. Chcete-li nastavit výchozí hodnotu, vyberte možnost **vlastní**. Tato hodnota se používá, pokud se při spuštění Runbooku nezadá jiná hodnota. Pokud nechcete zadat žádnou výchozí hodnotu, vyberte možnost **žádná** . |

    ![Přidat nový vstup](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Vytvořte dva parametry s následujícími vlastnostmi, které jsou používány aktivitou **Get-AzureRmVm** :

   * **Parameter1**
     * Název – VMName
     * Typ – řetězec
     * Povinné – ne
   * **Parameter2**
     * Název – resourceGroupName
     * Typ – řetězec
     * Povinné – ne
     * Výchozí hodnota – vlastní
     * Vlastní výchozí hodnota – \<název skupiny prostředků, která obsahuje virtuální počítače\>

5. Po přidání parametrů klikněte na **OK**. Teď je můžete zobrazit na **stránce vstupní a výstupní**. Znovu klikněte na **OK** a potom klikněte na **Uložit** a **publikovat** Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurace vstupních parametrů v sadách Runbook sady Python

Na rozdíl od PowerShellu, pracovního postupu PowerShellu a grafických runbooků nepřijímá Runbooky v Pythonu pojmenované parametry.
Všechny vstupní parametry jsou analyzovány jako pole hodnot argumentů.
K poli přistupujete tak, že do skriptu Pythonu naimportujete modul `sys` a pak použijete pole `sys.argv`.
Je důležité si uvědomit, že první prvek pole, `sys.argv[0]`, je název skriptu, takže první skutečný vstupní parametr je `sys.argv[1]`.

Příklad použití vstupních parametrů v sadě Runbook Pythonu najdete [v tématu můj první Runbook v Pythonu v Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Přiřazení hodnot k vstupním parametrům v sadách Runbook

Hodnoty do vstupních parametrů v sadách Runbook můžete předat v následujících scénářích:

### <a name="start-a-runbook-and-assign-parameters"></a>Spuštění Runbooku a přiřazení parametrů

Sadu Runbook lze spustit mnoha způsoby: prostřednictvím Azure Portal s webhookem s rutinami prostředí PowerShell s REST API nebo sadou SDK. Níže se podíváme na různé metody spuštění Runbooku a přiřazení parametrů.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Spuštění publikovaného Runbooku pomocí Azure Portal a přiřazení parametrů

Po [Spuštění Runbooku](start-runbooks.md#start-a-runbook-with-the-azure-portal)se otevře okno **Spustit Runbook** a můžete zadat hodnoty pro parametry, které jste vytvořili.

![Začněte používat portál.](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

V popisku pod vstupním polem vidíte atributy, které byly nastaveny pro parametr. Atributy zahrnují povinné nebo volitelné, typ a výchozí hodnotu. V bublině nápovědy vedle názvu parametru můžete zobrazit všechny klíčové informace, které potřebujete k rozhodnutí o vstupních hodnotách parametrů. Tyto informace zahrnují, zda je parametr povinný nebo volitelný. Zahrnuje také typ a výchozí hodnotu (pokud existuje) a další užitečné poznámky.

> [!NOTE]
> Parametry řetězcového typu podporují **prázdné** řetězcové hodnoty.  Zadáním parametru **[EmptyString]** v poli vstupní parametr předá do parametru prázdný řetězec. Také parametry řetězcového typu nepodporují předávání hodnot **null** . Pokud do parametru String nepředáte žádnou hodnotu, PowerShell ho interpretuje jako null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Spuštění publikované sady Runbook pomocí rutin prostředí PowerShell a přiřazení parametrů

* **Rutiny Azure Resource Manager:** Můžete spustit Runbook služby Automation, který se vytvořil ve skupině prostředků, pomocí [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Příklad:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Rutiny modelu nasazení Azure Classic:** Můžete spustit Runbook služby Automation, který byl vytvořen ve výchozí skupině prostředků pomocí [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Příklad:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Když spustíte Runbook pomocí rutin PowerShellu, vytvoří se výchozí parametr **MicrosoftApplicationManagementStartedBy** s hodnotou **PowerShellu**. Tento parametr si můžete zobrazit na stránce s **podrobnostmi o úloze** .  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Spuštění Runbooku pomocí sady SDK a přiřazení parametrů

* **Azure Resource Manager metoda:** Sadu Runbook můžete spustit pomocí sady SDK programovacího jazyka. Níže je fragment C# kódu pro spuštění sady Runbook ve vašem účtu Automation. Veškerý kód můžete zobrazit v našem [úložišti GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Metoda modelu nasazení Azure Classic:** Sadu Runbook můžete spustit pomocí sady SDK programovacího jazyka. Níže je fragment C# kódu pro spuštění sady Runbook ve vašem účtu Automation. Veškerý kód můžete zobrazit v našem [úložišti GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Chcete-li spustit tuto metodu, vytvořte slovník pro uložení parametrů Runbooku, **VMName** a **resourceGroupName**a jejich hodnoty. Poté spusťte sadu Runbook. Níže je fragment C# kódu pro volání metody, která je definována výše.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Spuštění Runbooku pomocí REST API a přiřazení parametrů

Pomocí metody **Put** s následujícím identifikátorem URI žádosti je možné vytvořit úlohu Runbooku a spustit ji pomocí Azure Automation REST API: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


V identifikátoru URI požadavku nahraďte následující parametry:

* **SubscriptionId:** ID vašeho předplatného Azure.  
* **resourceGroupName:** Název skupiny prostředků pro účet Automation.
* **automationAccountName:** Název vašeho účtu Automation, který je hostovaný v zadané cloudové službě.  
* **jobName:** Identifikátor GUID úlohy Identifikátory GUID v PowerShellu se dají vytvořit pomocí **[GUID]:: NewGuid (). ToString ()** příkaz.

Aby bylo možné předat parametry do úlohy Runbooku, použijte text žádosti. Ve formátu JSON má tyto dvě vlastnosti:

* **Název Runbooku:** Požadovanou. Název Runbooku, který má úloha začít.  
* **Parametry Runbooku:** Volitelné. Slovník seznamu parametrů ve formátu (název, hodnota), kde název by měl být typu řetězec a hodnota může být libovolná platná hodnota JSON.

Pokud chcete spustit Runbook **Get-AzureVMTextual** , který byl vytvořen dříve s **VMName** a **resourceGroupName** jako parametry, pro tělo žádosti použijte následující formát JSON.

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

Při úspěšném vytvoření úlohy se vrátí stavový kód HTTP 201. Další informace o hlavičkách odpovědí a textu odpovědi naleznete v článku o [Vytvoření úlohy Runbooku pomocí REST API.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Otestování Runbooku a přiřazení parametrů

Když [otestujete koncept verze Runbooku](automation-testing-runbook.md) pomocí možnosti test, otevře se stránka **test** a můžete nakonfigurovat hodnoty pro parametry, které jste vytvořili.

![Test a přiřazení parametrů](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Připojení plánu k sadě Runbook a přiřazení parametrů

Plán můžete [propojit](automation-schedules.md) s vaší sadou Runbook, aby se Runbook spouštěl v určitou dobu. Vstupní parametry přiřadíte při vytváření plánu a sada Runbook tyto hodnoty používá, když ji spustí plán. Plán nemůžete uložit, dokud nebudou poskytnuty všechny povinné hodnoty parametrů.

![Naplánování a přiřazení parametrů](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Vytvoření Webhooku pro Runbook a přiřazení parametrů

Pro Runbook můžete vytvořit [Webhook](automation-webhooks.md) a nakonfigurovat vstupní parametry Runbooku. Webhook nemůžete uložit, dokud nebudou k dispozici všechny povinné hodnoty parametrů.

![Vytvoření Webhooku a přiřazení parametrů](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Při spuštění sady Runbook pomocí Webhooku se pošle předdefinovaný vstupní parametr **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** spolu se vstupními parametry, které jste definovali. Kliknutím rozbalíte parametr **WebhookData** , kde najdete další podrobnosti.

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Předání objektu JSON do Runbooku

Může být užitečné ukládat data, která chcete předat Runbooku v souboru JSON.
Můžete například vytvořit soubor JSON, který obsahuje všechny parametry, které chcete předat Runbooku. Chcete-li to provést, je nutné převést JSON na řetězec a pak převést řetězec na objekt prostředí PowerShell před jeho předáním do sady Runbook.

V tomto příkladu máte skript PowerShellu, který volá rutinu [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) , která spustí Runbook PowerShellu a předá obsah JSON do Runbooku.
PowerShellový Runbook spustí virtuální počítač Azure a získá parametry pro virtuální počítač z předaného formátu JSON.

### <a name="create-the-json-file"></a>Vytvoření souboru JSON

Do textového souboru zadejte následující test a uložte ho jako `test.json` někam do místního počítače.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Vytvoření Runbooku

Vytvořte v Azure Automation nový PowerShellový Runbook s názvem test-JSON.
Informace o tom, jak vytvořit novou PowerShellovou sadu Runbook, najdete v tématu [můj první powershellový Runbook](automation-first-runbook-textual-powershell.md).

Pro přijetí dat JSON musí sada Runbook převzít objekt jako vstupní parametr.

Sada Runbook pak může použít vlastnosti definované ve formátu JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Uložte a publikujte tuto sadu Runbook ve vašem účtu Automation.

### <a name="call-the-runbook-from-powershell"></a>Volání Runbooku z PowerShellu

Nyní můžete sadu Runbook volat z místního počítače pomocí Azure PowerShell.
Spusťte následující příkazy PowerShellu:

1. Přihlaste se k Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   Budete vyzváni k zadání přihlašovacích údajů Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** je teď alias pro **Connect-AzureRmAccount**. Pokud se při hledání položek knihovny nezobrazí **příkaz Connect-AzureRMAccount**, můžete použít příkaz **Add-AzureRMAccount**nebo můžete aktualizovat moduly v účtu Automation.

1. Získá obsah souboru JSON a převede ho na řetězec:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` je cesta, kam jste uložili soubor JSON.

1. Převést obsah řetězce `$json` na objekt prostředí PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Vytvořte zatřiďovací tabulku pro parametry pro `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Všimněte si, že nastavujete hodnotu `Parameters` k objektu prostředí PowerShell, který obsahuje hodnoty ze souboru JSON.
1. Spuštění runbooku

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Další kroky

* Podrobnosti o různých způsobech, jak spustit sadu Runbook, najdete v tématu [Spuštění Runbooku](automation-starting-a-runbook.md).
* Pokud chcete upravit textový Runbook, přečtěte si téma [úpravy textových runbooků](automation-edit-textual-runbook.md).
* Informace o tom, jak upravit grafický Runbook, najdete v tématu věnovaném [vytváření grafiky v Azure Automation](automation-graphical-authoring-intro.md).
