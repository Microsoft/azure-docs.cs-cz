---
title: Vstupní parametry runbooku
description: Vstupní parametry Runbooku zvýšíte flexibilitu sad runbook, neboť umožňuje předat data do sady runbook, kdy byla spuštěna. Tento článek popisuje různé scénáře použití vstupních parametrů v sadách runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ef0dfafce33837a80ec00ba58e6be99784e652a2
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418537"
---
# <a name="runbook-input-parameters"></a>Vstupní parametry runbooku

Vstupní parametry Runbooku zvýšíte flexibilitu sad runbook, neboť umožňuje předá jí data při spuštění. Povolit parametry sady runbook akce, jež mají být určené pro konkrétní scénáře a prostředí. V tomto článku si projdete různé scénáře použití vstupních parametrů v sadách runbook.

## <a name="configure-input-parameters"></a>Vstupní parametry

Vstupní parametry lze konfigurovat v prostředí PowerShell, pracovní postup prostředí PowerShell, Python a grafické runbooky. Sada runbook může mít více parametrů s různými datovými typy nebo žádné parametry vůbec. Vstupní parametry můžou být povinný nebo volitelný a může mít výchozí hodnotu pro volitelné parametry. Můžete přiřadit hodnoty vstupní parametry pro sady runbook při spuštění prostřednictvím některého z dostupných metod. Tyto metody patří, spuštění runbooku z portálu Azure, webové služby nebo prostředí PowerShell. Můžete také spustit jako podřízené sady runbook, která je volána vložená v jiné sady runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurace vstupních parametrů v sadách runbook Powershellu

Sady runbook Powershellu a pracovní postup prostředí PowerShell ve službě Azure Automation se podporují vstupní parametry, které jsou definovány prostřednictvím následující atributy:  

| **Vlastnost** | **Popis** |
|:--- |:--- |
| Type |Povinná hodnota. Datový typ, očekávání pro hodnotu parametru. Libovolný typ rozhraní .NET je platný. |
| Název |Povinná hodnota. Název parametru Toto musí být jedinečný v rámci sady runbook a může obsahovat jenom písmena, číslice nebo znaky podtržení. Musí začínat písmenem. |
| Povinné |Volitelné. Určuje, zda je nutné zadat hodnotu pro parametr. Pokud nastavíte  **\$true**, pak je nutné zadat hodnotu při spuštění runbooku. Pokud nastavíte  **\$false**, pak hodnota je volitelná. |
| Výchozí hodnota |Volitelné. Určuje hodnotu, která se používá pro parametr, pokud není hodnota předávané při spuštění runbooku. Výchozí hodnota je možné nastavit pro libovolný parametr a bude automaticky nastavit parametr volitelný bez ohledu na to povinné nastavení. |

Prostředí Windows PowerShell podporuje další atributy vstupních parametrů, než jsou zde uvedeny, jako je ověření, aliasy, a nastaví parametr. Azure Automation, ale aktuálně podporuje pouze předchozí vstupní parametry.

Definici parametru v runboocích pracovního postupu Powershellu má následující obecné formulář, kde více parametrů je odděleno čárkami.

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
> Když definujete parametry, pokud nechcete zadat **povinné** atribut, pak ve výchozím nastavení, tento parametr se považuje za nepovinné. Navíc pokud nastavíte výchozí hodnotu pro parametr v runboocích pracovních postupů Powershellu, je považován za powershellu volitelný parametr, bez ohledu **povinné** hodnotu atributu.

Jako příklad nakonfigurujeme vstupní parametry runbooku pracovního postupu Powershellu, který zobrazí podrobnosti o virtuálních počítačích, jeden virtuální počítač nebo všechny virtuální počítače v rámci skupiny prostředků. Tato sada runbook má dva parametry, jak je znázorněno na následujícím snímku obrazovky: název virtuálního počítače a název skupiny prostředků.

![Automatizace pracovního postupu Powershellu](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

V této definici parametru parametry  **\$VMName** a  **\$resourceGroupName** jsou jednoduché parametry typu String. Ale sady runbook Powershellu a pracovních postupů Powershellu podporují všech typů jednoduché a komplexní typy, jako je například **objekt** nebo **PSCredential** pro vstupní parametry.

Pokud vaše sada runbook má vstupní parametr typu objektu, použijte PowerShell zatřiďovací tabulku s (název, hodnotu) páry a zajistěte tak předání hodnotu. Například pokud máte v sadě runbook následující parametr:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Můžete poté předat parametr následující hodnotu:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Při předání žádná hodnota pro volitelný `[String]` zadejte parametr, který má _výchozí hodnota_ z `\$null`, bude hodnota parametru _prázdný řetězec_, **není** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurace vstupních parametrů v grafických runbookách

K [konfigurace grafický runbook](automation-first-runbook-graphical.md) se vstupními parametry, vytvoříme grafického runbooku, který zobrazí podrobnosti o virtuálních počítačích, buď jeden virtuální počítač nebo všechny virtuální počítače v rámci skupiny prostředků. Konfigurace sady runbook se skládá z dvou hlavních činností, jak je popsáno níže.

[**Ověření Runbooků pomocí účtu spustit jako pro Azure** ](automation-sec-configure-azure-runas-account.md) k ověřování pomocí Azure.

[**Get-AzureRmVm** ](/powershell/module/azurerm.compute/get-azurermvm) zobrazíte vlastnosti virtuálního počítače.

Můžete použít [ **Write-Output** ](/powershell/module/microsoft.powershell.utility/write-output) aktivity do výstupního názvy virtuálních počítačů. Aktivita **Get-AzureRmVm** přijímá dva parametry, **název virtuálního počítače** a **název skupiny prostředků**. Protože tyto parametry může vyžadovat rozdílné hodnoty při každém spuštění sady runbook, můžete přidat vstupních parametrů do runbooku. Tady je postup pro přidání vstupní parametry:

1. Výběr grafického runbooku z **sady Runbook** okna a pak klikněte na tlačítko [ **upravit** ](automation-graphical-authoring-intro.md) ho.
2. V editoru sady runbook, klikněte na **vstup a výstup** otevřít **vstup a výstup** okno.

   ![Grafický runbook služby Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. **Vstup a výstup** okně zobrazí vstupní parametry, které jsou definovány pro sadu runbook. V tomto okně můžete přidat nový vstupní parametr nebo upravit konfiguraci existující vstupní parametr. Chcete-li přidat nový parametr pro sadu runbook, klikněte na tlačítko **přidat vstup** otevřít **vstupní parametr Runbooku** okno. Tam si můžete nakonfigurovat následující parametry:

   | **Vlastnost** | **Popis** |
   |:--- |:--- |
   | Název |Povinná hodnota. Název parametru Toto musí být jedinečný v rámci sady runbook a může obsahovat jenom písmena, číslice nebo znaky podtržení. Musí začínat písmenem. |
   | Popis |Volitelné. Popis účelu vstupního parametru. |
   | Type |Volitelné. Datový typ, který je možné očekávat u hodnotu parametru. Jsou podporované typy parametrů **řetězec**, **Int32**, **Int64**, **desítkové**, **logická**,  **Datum a čas**, a **objekt**. Pokud datový typ není zaškrtnuto, bude výchozí **řetězec**. |
   | Povinné |Volitelné. Určuje, zda je nutné zadat hodnotu pro parametr. Pokud se rozhodnete **Ano**, pak je nutné zadat hodnotu při spuštění runbooku. Pokud se rozhodnete **žádné**, pak hodnota není požadovaný při spuštění runbooku a může být nastaven výchozí hodnotu. |
   | Výchozí hodnota |Volitelné. Určuje hodnotu, která se používá pro parametr, pokud není hodnota předávané při spuštění runbooku. Výchozí hodnotu můžete nastavit pro parametr, který není povinné. Chcete-li nastavit výchozí hodnotu, zvolte **vlastní**. Tato hodnota se používá, pokud je jiná hodnota poskytnutá při spuštění runbooku. Zvolte **žádný** Pokud nechcete zadat jakékoli výchozí hodnotu. |

    ![Přidat nový vstup](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Vytvořit dva parametry s následujícími vlastnostmi, které jsou používány **Get-AzureRmVm** aktivity:

   * **Parametr1:**
     * Název – VMName
     * Typ – řetězce
     * Povinné – ne
   * **Parametr2:**
     * Název – název skupiny prostředků
     * Typ – řetězce
     * Povinné – ne
     * Výchozí hodnota – vlastní
     * Vlastní výchozí hodnota - \<název skupiny prostředků, která obsahuje virtuální počítače\>

5. Jakmile přidáte parametry, klikněte na tlačítko **OK**. Teď je můžete zobrazit **vstup a výstup stránky**. Klikněte na tlačítko **OK** znovu a potom klikněte na tlačítko **Uložit** a **publikovat** své sadě runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurace vstupních parametrů v sadách runbook Pythonu

Na rozdíl od Powershellu, pracovní postup prostředí PowerShell a grafické runbooky nepřebírají runbooky Python pojmenované parametry.
Všechny vstupní parametry jsou analyzovány jako pole hodnot argumentů.
Přístup k poli pomocí importu `sys` modul skript Pythonu a poté použijete `sys.argv` pole.
Je důležité si uvědomit, že první prvek pole, `sys.argv[0]`, je název skriptu, tedy první vstupní parametr skutečné `sys.argv[1]`.

Příklad použití vstupních parametrů v runbooku v Pythonu, naleznete v tématu [Můj první runbook Pythonu ve službě Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Přiřadit hodnoty k zadání parametrů v sadách runbook

Můžete předat hodnoty pro vstupní parametry v sadách runbook v následujících scénářích:

### <a name="start-a-runbook-and-assign-parameters"></a>Spusťte sadu runbook a přiřadit parametry

Sady runbook lze spustit mnoho způsobů: na webu Azure portal, pomocí webhooku, pomocí rutin prostředí PowerShell, rozhraní REST API nebo pomocí sady SDK. Dál probereme různé metody pro spuštění sady runbook a přiřazování parametry.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Spuštění publikovaného runbooku pomocí webu Azure portal a přiřadit parametry

Když jste [spuštění sady runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal), **spustit Runbook** se otevře okno a zadáte hodnoty pro parametry, které jste vytvořili.

![Spustit na portálu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Popisek pod do vstupního pole zobrazí se atributy, které jsou nastavené pro parametr. Atributy zahrnují povinné nebo volitelné, typ a výchozí hodnota. V bublině nápovědy vedle názvu parametru zobrazí se všechny klíčové informace potřebné k rozhodování o vstupní hodnoty parametrů. Tyto informace zahrnují, zda je parametr povinný nebo volitelný. Zahrnuje také typ a výchozí hodnota (pokud existuje) a další užitečné poznámky.

> [!NOTE]
> Podpora parametry typu řetězec **prázdný** hodnoty řetězce.  Zadání **[EmptyString]** jako vstupní parametr pole předá parametr prázdný řetězec. Také parametry typu řetězec nepodporují **Null** předané hodnoty. Pokud předáte není žádnou hodnotu pro parametr řetězce, pak Powershellu interpretuje ji jako hodnotu null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Spuštění publikovaného runbooku pomocí rutin prostředí PowerShell a přiřadit parametry

* **Rutiny Azure Resource Manageru:** Můžete spustit runbook služby Automation, který byl vytvořen ve skupině prostředků s použitím [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Příklad:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Rutiny modelu nasazení Azure classic:** Můžete spustit runbook služby automation, který byl vytvořen v výchozí skupiny prostředků s použitím [Start AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Příklad:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Pokud runbook spustíte s použitím rutin prostředí PowerShell, výchozí parametr **MicrosoftApplicationManagementStartedBy** se vytvoří s hodnotou **Powershellu**. Tento parametr můžete zobrazit **podrobnosti úlohy** stránky.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Spuštění runbooku pomocí sady SDK a přiřadit parametry

* **Metoda Azure Resource Manageru:** Spuštění runbooku pomocí sady SDK programovací jazyk. Tady je fragment kódu jazyka C# pro spuštění sady runbook ve vašem účtu Automation. Můžete zobrazit veškerý kód v našich [úložiště GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Metoda modelu nasazení Azure classic:** Spuštění runbooku pomocí sady SDK programovací jazyk. Tady je fragment kódu jazyka C# pro spuštění sady runbook ve vašem účtu Automation. Můžete zobrazit veškerý kód v našich [úložiště GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Chcete-li spustit tuto metodu, vytvořit adresář k uložení parametrů runbooku **VMName** a **resourceGroupName**a jejich hodnoty. Spusťte sadu runbook. Níže je fragment kódu jazyka C# pro volání metody, která je definována výše.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Spuštění runbooku pomocí rozhraní REST API a přiřadit parametry

Úlohy runbooku se dají vytvářet a spuštěn pomocí REST API služby Azure Automation s použitím **UMÍSTIT** metodu s následující identifikátor URI požadavku: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


V identifikátoru URI požadavku nahraďte následující parametry:

* **ID předplatného:** ID vašeho předplatného Azure.  
* **resourceGroupName:** Název skupiny prostředků pro účet Automation.
* **automationAccountName:** Název účtu služby automation, která je hostována v rámci zadané cloudové služby.  
* **jobName:** Identifikátor GUID úlohy. Identifikátory GUID v prostředí PowerShell můžete vytvořit pomocí **[GUID]::NewGuid(). ToString()** příkazu.

Pokud chcete předat parametry úlohy runbooku, použijte datovou část požadavku. Trvá následující dvě vlastnosti, které jsou k dispozici ve formátu JSON:

* **Název sady Runbook:** Povinná hodnota. Název sady runbook pro spuštění úlohy.  
* **Parametry sady Runbook:** Volitelné. Slovník seznamu parametrů (název, hodnotu) naformátujte kde název by měl být typu String a hodnota může být libovolný platný JSON.

Pokud chcete spustit **Get-AzureVMTextual** sady runbook, který jste vytvořili pomocí **VMName** a **resourceGroupName** jako parametry, pomocí následujícího formátu JSON pro datová část požadavku.

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

Stavový kód HTTP 201 je vrácena, jestliže úspěšném vytvoření úlohy. Další informace o hlavičky odpovědi a text odpovědi, najdete v článku o tom, jak [vytvoření úlohy runbooku pomocí rozhraní REST API.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Otestování sady runbook a přiřadit parametry

Při vám [otestovat verzi konceptu sady runbook](automation-testing-runbook.md) pomocí možnosti testů **testování** stránka se otevře a konfigurováním hodnoty pro parametry, které jste vytvořili.

![Testování a přiřadit parametry](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Připojení plánu k runbooku a přiřadit parametry

Je možné [připojení plánu](automation-schedules.md) k sadě runbook tak, aby sada runbook se spouští v určitém čase. Když vytvoříte plán a sada runbook používá tyto hodnoty, když se spustí podle plánu přiřadíte vstupní parametry. Plán nelze uložit, dokud jsou k dispozici všechny hodnoty povinných parametrů.

![Plánování a přidělování parametry](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Vytvořit webhook pro runbook a přiřadit parametry

Můžete vytvořit [webhooku](automation-webhooks.md) vaší sady runbook a vstupní parametry runbooku. Webhook nelze uložit, dokud jsou k dispozici všechny hodnoty povinných parametrů.

![Vytvořit webhook a přiřadit parametry](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Při spuštění sady runbook pomocí webhooku, předdefinované vstupní parametr **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** se zasílá společně s vstupní parametry, které jste definovali. Můžete kliknout a rozbalte **WebhookData** parametr pro další podrobnosti.

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Předání objektu JSON do runbooku

Může být užitečné k ukládání dat, které chcete předat do sady runbook v souboru JSON.
Například může vytvořit soubor JSON, který obsahuje všechny parametry, které chcete předat do sady runbook. K tomuto účelu, budete muset převést na řetězec ve formátu JSON a poté převést řetězec na objekt prostředí PowerShell, než je předán do runbooku.

V tomto příkladu budete mít skript prostředí PowerShell, který volá [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) spuštění Powershellového runbooku, předá obsah ve formátu JSON do runbooku.
Powershellový runbook spustí virtuální počítač Azure, získávání parametrů pro virtuální počítač z formátu JSON, který byl předán.

### <a name="create-the-json-file"></a>Vytvořte soubor JSON

Zadejte následující test do textového souboru a uložte ho jako `test.json` někde v místním počítači.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Vytvoření sady runbook

Vytvořte nový runbook Powershellu s názvem "Test-Json" ve službě Azure Automation.
Zjistěte, jak vytvořit nový runbook Powershellu, najdete v článku [Můj první Powershellový runbook](automation-first-runbook-textual-powershell.md).

Tak, aby přijímal JSON data, runbook přijme jako vstupní parametr objekt.

Sada runbook pak můžete použít vlastnosti definované ve formátu JSON.

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

Uložení a publikování této sady runbook ve vašem účtu Automation.

### <a name="call-the-runbook-from-powershell"></a>Volání runbooku z prostředí PowerShell

Nyní můžete volat sadu runbook z místního počítače pomocí Azure Powershellu.
Spusťte následující příkazy Powershellu:

1. Přihlaste se k Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

  Budete vyzváni k zadání přihlašovacích údajů Azure.

  > [!IMPORTANT]
  > **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo ve vašem účtu Automation můžete aktualizovat moduly.

1. Získat obsah souboru JSON a převést na řetězec:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` představuje cestu, kam jste uložili soubor JSON.

1. Převést řetězec obsah `$json` na objekt prostředí PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Vytvořit tabulku hash pro parametry `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Všimněte si, že nastavujete hodnotu `Parameters` na objekt prostředí PowerShell, který obsahuje hodnoty ze souboru JSON.
1. Spuštění runbooku

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Další postup

* Podrobnosti o různých způsobech spouštění sady runbook najdete v tématu [spuštění sady runbook](automation-starting-a-runbook.md).
* Upravit textové sady runbook, najdete v tématu [úpravy textových runbooků](automation-edit-textual-runbook.md).
* Chcete-li upravit grafický runbook, přečtěte si [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).