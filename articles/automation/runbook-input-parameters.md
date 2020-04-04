---
title: Vstupní parametry runbooku
description: Vstupní parametry sady Runbook zvyšují flexibilitu runbooků tím, že umožňují předávat data do sady Runbook při spuštění. Tento článek popisuje různé scénáře, kde se vstupní parametry používají v sadách Runbook.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656034"
---
# <a name="runbook-input-parameters"></a>Vstupní parametry runbooku

Vstupní parametry runbooku zvyšují flexibilitu runbooku tím, že jí umožňují předávání dat při jeho spuštění. Tyto parametry umožňují akce runbooku, které mají být zaměřeny na konkrétní scénáře a prostředí. Tento článek popisuje konfiguraci a použití vstupních parametrů v sadách Runbook.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Konfigurace vstupních parametrů

Můžete nakonfigurovat vstupní parametry pro powershellové, pracovní postupy PowerShellu, grafické a pythonové sady runbooků. Runbook může mít více parametrů s různými datovými typy nebo vůbec žádné parametry. Vstupní parametry mohou být povinné nebo volitelné a pro volitelné parametry můžete použít výchozí hodnoty.

Vstupním parametrům runbooku přiřadíte při spuštění hodnoty. Runbook můžete spustit z webu Azure Portal, webové služby nebo PowerShellu. Můžete také spustit jeden jako podřízený runbook, který se nazývá vložkou v jiném runbooku.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurace vstupních parametrů v sadách Runbook powershellu

Runbooky pracovního postupu Prostředí PowerShell a Prostředí v Azure Automation podporují vstupní parametry, které jsou definovány prostřednictvím následujících vlastností. 

| **Vlastnost** | **Popis** |
|:--- |:--- |
| Typ |Povinná hodnota. Pro hodnotu parametru byl očekáván datový typ. Libovolný typ .NET je platný. |
| Name (Název) |Povinná hodnota. Název parametru Tento název musí být jedinečný v rámci runbooku, musí začínat písmenem a může obsahovat pouze písmena, čísla nebo podtržítka. |
| Povinné |Nepovinný parametr. Logická hodnota určující, zda parametr vyžaduje hodnotu. Pokud tuto hodnotu nastavíte na hodnotu True, musí být při spuštění sady Runbook poskytnuta hodnota. Pokud nastavíte hodnotu False, hodnota je volitelná. Pokud nezadáte hodnotu vlastnosti, `Mandatory` prostředí PowerShell považuje vstupní parametr ve výchozím nastavení za volitelný. |
| Výchozí hodnota |Nepovinný parametr. Hodnota, která se používá pro parametr, pokud žádná vstupní hodnota je předána při spuštění runbooku. Sada Runbook může nastavit výchozí hodnotu pro libovolný parametr. |

Prostředí Windows PowerShell podporuje více atributů vstupních parametrů, než jsou uvedeny výše, jako je například ověření, aliasy a sady parametrů. Azure Automation však aktuálně podporuje pouze vlastnosti vstupního parametru uvedené.

Jako příklad se podívejme na definici parametru v runbooku pracovního postupu prostředí PowerShell. Tato definice má následující obecný tvar, kde více parametrů jsou odděleny čárkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Teď nakonfigurujeme vstupní parametry pro runbook pracovního postupu prostředí PowerShell, který vypočtou podrobnosti o virtuálních počítačích, buď jeden virtuální počítač, nebo všechny virtuální počítače v rámci skupiny prostředků. Tato runbook má dva parametry, jak je znázorněno na`VMName`následujícím snímku obrazovky: název`resourceGroupName`virtuálního počítače ( ) a název skupiny prostředků ( ).

![Pracovní postup prostředí PowerShell automatizace](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

V této definici parametru jsou vstupní parametry jednoduchými parametry typu string.

Všimněte si, že runbooky pracovního postupu prostředí PowerShell a Prostředí PowerShell podporují všechny jednoduché typy a složité typy, jako `Object` jsou vstupní parametry nebo `PSCredential` pro ně. Pokud má vaše runbook vstupní parametr objektu, musíte použít hodnotitelnou hodnotu prostředí PowerShell s dvojicemi název-hodnota předat hodnotu. Například máte následující parametr v runbooku.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

V takovém případě můžete parametru předat následující hodnotu.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Pokud nepředáte hodnotu volitelnému parametru String s nulovou výchozí hodnotou, hodnota parametru je prázdný řetězec namísto Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurace vstupních parametrů v grafických sadách runbooků

Chcete-li ilustrovat konfiguraci vstupních parametrů pro grafickou runbook, pojďme vytvořit runbook, který výstupy podrobnosti o virtuálních počítačích, buď jeden virtuální počítač nebo všechny virtuální počítače v rámci skupiny prostředků. Podrobnosti naleznete [v tématu Moje první grafická kniha runbook](automation-first-runbook-graphical.md).

Grafický runbook používá tyto hlavní aktivity runbooku:

* Konfigurace účtu Azure Run As k ověření pomocí Azure. 
* Definice rutiny [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) pro získání vlastností virtuálního počítače.
* Použití aktivity [zápisu a výstupu](/powershell/module/microsoft.powershell.utility/write-output) k výstupu názvů virtuálních počítačů. 

Aktivita `Get-AzVM` definuje dva vstupy, název virtuálního virtuálního serveru a název skupiny prostředků. Vzhledem k tomu, že tyto názvy se mohou lišit při každém spuštění runbooku, musíte do runbooku přidat vstupní parametry, abyste tyto vstupy přijali. Informace o [vytváření grafických funkcí v azure automation](automation-graphical-authoring-intro.md).

Chcete-li nakonfigurovat vstupní parametry, postupujte takto.

1. Na stránce Runbook vyberte grafickou runbook u klikněte na **Upravit**.
2. V grafickém editoru klikněte na tlačítko **Vstup a výstup** a potom **přidejte vstup** a otevřete podokno Vstupní parametr knihy Runbook.

   ![Automatizace grafického runbooku](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Ovládací prvek Vstup a Výstup zobrazí seznam vstupních parametrů, které jsou definovány pro runbook. Zde můžete buď přidat nový vstupní parametr, nebo upravit konfiguraci existujícího vstupního parametru. Chcete-li přidat nový parametr pro runbook, klikněte na **Přidat vstup** otevřete okno **vstupního parametru runbooku,** kde můžete konfigurovat parametry pomocí vlastností definovaných v [grafickém vytváření v Azure Automation](automation-graphical-authoring-intro.md).

    ![Přidat nový vstup](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Vytvořte dva parametry s následujícími `Get-AzVM` vlastnostmi, které bude aktivita používat, a klepněte na tlačítko **OK**.

   * Parametr 1:
        * **Název** -- **VMName**
        * **Typ** -- Řetězec
        * **Povinné** -- **ne**

   * Parametr 2:
        * **Name** -- **Název_prostředkuNázev_názvu_prostředku**
        * **Typ** -- Řetězec
        * **Povinné** -- **ne**
        * **Výchozí hodnota** -- **Vlastní**
        * Vlastní výchozí hodnota – název skupiny prostředků, která obsahuje virtuální chody

5. Zobrazení parametrů v ovládacím prvku Vstup a Výstup. 
6. Znovu klepněte na **tlačítko OK** a potom klepněte na tlačítko **Uložit**.
7. Kliknutím na **Publikovat** publikujte runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurace vstupních parametrů v runbookech Pythonu

Na rozdíl od Prostředí PowerShell, pracovního postupu prostředí PowerShell a grafických sad runbooků neberou sady Runbook v Pythonu pojmenované parametry. Editor runbooku analyzuje všechny vstupní parametry jako pole hodnot argumentů. K poli můžete přistupovat `sys` importem modulu do skriptu `sys.argv` Pythonu a potom pomocí pole. Je důležité si uvědomit, že první `sys.argv[0]`prvek pole, , je název skriptu. Proto je `sys.argv[1]`prvním skutečným vstupním parametrem .

Příklad použití vstupních parametrů v runbooku Pythonu najdete v tématu [Moje první runbook v Pythonu v Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Přiřazení hodnot vstupním parametrům v sadách Runbook

Tato část popisuje několik způsobů předávání hodnot vstupním parametrům v sadách Runbook. Hodnoty parametrů můžete přiřadit, když:

* [Spuštění runbooku](#start-a-runbook-and-assign-parameters)
* [Testování runbooku](#test-a-runbook-and-assign-parameters)
* [Propojení plánu pro runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Vytvoření webhooku pro runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Spuštění runbooku a přiřazení parametrů

Runbook lze spustit mnoha způsoby: prostřednictvím portálu Azure, s webhooku, s rutinami Prostředí PowerShell, s rozhraním REST API nebo s sadou SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Spuštění publikovaného runbooku pomocí portálu Azure portal a přiřazení parametrů

Při [spuštění sady Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) na portálu Azure se otevře okno **Spustit runbook** a můžete zadat hodnoty pro parametry, které jste vytvořili.

![Začněte používat portál](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

V popisku pod vstupním polem se zobrazí vlastnosti, které byly nastaveny tak, aby definovaly atributy parametrů, například povinná nebo volitelná, typová, výchozí hodnota. Bublina nápovědy vedle názvu parametru také definuje klíčové informace potřebné k rozhodování o vstupních hodnotách parametrů. 

> [!NOTE]
> Parametry řetězce podporují prázdné hodnoty typu String. Zadání `[EmptyString]` do pole vstupního parametru předá parametru prázdný řetězec. Parametry řetězce také nepodporují hodnotu Null. Pokud nepředáte parametru řetězce žádnou hodnotu, prostředí PowerShell ji interpretuje jako null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Spuštění publikované knihy Runbook pomocí rutin prostředí PowerShell a přiřazení parametrů

* **Rutiny Správce prostředků Azure:** Runbook automatizace, který byl vytvořen ve skupině prostředků pomocí [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Rutiny modelu klasického nasazení Azure:** Spustit runbook automatizace, který byl vytvořen ve výchozí skupině prostředků pomocí [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Při spuštění runbooku pomocí rutin prostředí PowerShell je `MicrosoftApplicationManagementStartedBy`s hodnotou `PowerShell`vytvořen výchozí parametr , . Tento parametr můžete zobrazit v podokně podrobností o projektu.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Spuštění sady Runbook pomocí sady SDK a přiřazení parametrů

* **Metoda Azure Resource Manager:** Runbook můžete spustit pomocí sady SDK programovacího jazyka. Níže je fragment kódu Jazyka C# pro spuštění runbooku ve vašem účtu Automation. Veškerý kód si můžete prohlédnout v našem [úložišti GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

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

* **Metoda modelu klasického nasazení Azure:** Runbook můžete spustit pomocí sady SDK programovacího jazyka. Níže je fragment kódu Jazyka C# pro spuštění runbooku ve vašem účtu Automation. Veškerý kód si můžete prohlédnout v našem [úložišti GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

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

   Chcete-li spustit tuto metodu, vytvořte slovník `VMName` `resourceGroupName` pro uložení parametrů runbooku a jejich hodnot. Pak spusťte runbook. Níže je fragment kódu C# pro volání metody, která je definována výše.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Spuštění runbooku pomocí rozhraní REST API a přiřazení parametrů

Můžete vytvořit a spustit úlohu runbooku s rozhraním AZURE Automation REST API pomocí `PUT` metody s následujícím identifikátorem URI požadavku:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

V identifikátoru URI požadavku nahraďte následující parametry:

* `subscriptionId`: ID předplatného Azure.  
* `resourceGroupName`: Název skupiny prostředků pro účet automatizace.
* `automationAccountName`: Název účtu Automation, který je hostovaný v rámci zadané cloudové služby.  
* `jobName`: Identifikátor GUID pro úlohu. Identifikátory GUID v prostředí PowerShell lze vytvořit pomocí `[GUID]::NewGuid().ToString()*`.

Chcete-li předat parametry úloze runbooku, použijte tělo požadavku. Bere následující informace, poskytované ve formátu JSON:

* Název runbooku: Povinné. Název runbooku pro spuštění úlohy.  
* Parametry runbooku: Volitelné. Slovník seznamu parametrů ve formátu (název, hodnota), kde název je typu String a hodnota může být libovolná platná hodnota JSON.

Pokud chcete spustit **get-AzureVMTextal** runbook vytvořený `VMName` `resourceGroupName` dříve s a jako parametry, použijte následující formát JSON pro tělo požadavku.

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

Stavový kód HTTP 201 je vrácen, pokud je úloha úspěšně vytvořena. Další informace o hlavičkách odpovědí a těle odpovědi najdete v [tématu vytvoření úlohy runbooku pomocí rozhraní REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testování runbooku a přiřazení parametrů

Při [testování konceptverze sady Runbook](automation-testing-runbook.md) pomocí možnosti test se otevře stránka Test. Na této stránce můžete nakonfigurovat hodnoty pro parametry, které jste vytvořili.

![Testování a přiřazení parametrů](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Propojení plánu se knihou Runbook a přiřazení parametrů

Plán můžete propojit s [runbookem](automation-schedules.md) tak, aby se runbook spouštěl v určitý čas. Při vytváření plánu přiřadíte vstupní parametry a soubor Runbook použije tyto hodnoty při spuštění podle plánu. Plán nelze uložit, dokud nebudou k dispozici všechny povinné hodnoty parametrů.

![Plánování a přiřazení parametrů](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Vytvoření webového háku pro runbook a přiřazení parametrů

Můžete vytvořit [webhookpro](automation-webhooks.md) váš runbook a nakonfigurovat vstupní parametry sady Runbook. Webhook nelze uložit, dokud nebudou k dispozici všechny povinné hodnoty parametrů.

![Vytvoření webového háku a přiřazení parametrů](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Při spuštění runbooku pomocí webhooku je odeslán `[WebhookData](automation-webhooks.md)` předdefinovaný vstupní parametr spolu se vstupními parametry, které definujete. 

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Předání objektu JSON do runbooku

Může být užitečné ukládat data, která chcete předat do runbooku v souboru JSON. Můžete například vytvořit soubor JSON, který obsahuje všechny parametry, které chcete předat do runbooku. Chcete-li to provést, musíte převést kód JSON na řetězec a potom převést řetězec na objekt Prostředí PowerShell před předáním do runbooku.

Tato část používá příklad, ve kterém skript prostředí PowerShell volá [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) ke spuštění runbooku prostředí PowerShell a předá obsah souboru JSON do runbooku. Runbook prostředí PowerShell spustí virtuální počítač Azure načtením parametrů pro virtuální počítač z objektu JSON.

### <a name="create-the-json-file"></a>Vytvoření souboru JSON

Zadejte následující kód do textového souboru a uložte jej jako **test.json** někde v místním počítači.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Vytvoření runbooku

Vytvořte novou runbook prostředí PowerShell s názvem **Test-Json** v Azure Automation. Podívejte [se na můj první PowerShell runbook](automation-first-runbook-textual-powershell.md).

Chcete-li přijmout data JSON, runbook musí mít objekt jako vstupní parametr. Runbook pak můžete použít vlastnosti definované v souboru JSON.

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

Uložte a publikujte tento runbook ve svém účtu Automation.

### <a name="call-the-runbook-from-powershell"></a>Volání runbooku z PowerShellu

Teď můžete volat runbook z místního počítače pomocí Azure PowerShellu. 

1. Přihlaste se k Azure, jak je znázorněno. Potom se zobrazí výzva k zadání přihlašovacích údajů Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Pro sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell `Connect-AzAccount`a jsou aliasy pro . Všimněte si, že tyto aliasy nejsou k dispozici pro grafické sady Runbook. Grafická kniha runbook `Connect-AzAccount` může používat pouze samostatně.

1. Získejte obsah uloženého souboru JSON a převeďte jej na řetězec. `JsonPath`označuje cestu, kam jste uložili soubor JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Převeďte obsah `$json` řetězce na objekt prostředí PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Vytvořte hodnotitelnou hodnotu `Start-AzAutomationRunbook`hash pro parametry pro rozhraní . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Všimněte si, že nastavujete hodnotu objektu `Parameters` PowerShell, který obsahuje hodnoty ze souboru JSON.
1. Spusťte runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Další kroky

* Podrobnosti o různých způsobech spuštění runbooku najdete v [tématu Spuštění runbooku](automation-starting-a-runbook.md).
* Chcete-li upravit textovou knihu runbook, přečtěte si [knihu Úpravy textových sad runbooků](automation-edit-textual-runbook.md).
* Chcete-li upravit grafickou knihu runbook, přečtěte si [informace o vytváření grafických funkcí v aplikaci Azure Automation](automation-graphical-authoring-intro.md).
