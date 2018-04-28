---
title: Vstupní parametry runbooku
description: Vstupní parametry Runbooku zvýšíte flexibilitu sad runbook tím, že se můžete k předávání dat k sadě runbook, jakmile je spuštěno. Tento článek popisuje různé scénáře, kdy se používá vstupních parametrů v sadách runbook.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 19b0e17807adc0e7a4522fd13cd85779cdbcafd6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="runbook-input-parameters"></a>Vstupní parametry runbooku

Vstupní parametry Runbooku zvýšíte flexibilitu sad runbook tím, že se můžete k předávání dat do ní při jeho spuštění. Parametry umožňují akce runbook jako cíle pro konkrétní scénáře a různá prostředí. V tomto článku jste provede různé scénáře použití vstupních parametrů v sadách runbook.

## <a name="configure-input-parameters"></a>Konfigurace vstupní parametry

Vstupní parametry se dá nakonfigurovat v prostředí PowerShell, pracovní postup prostředí PowerShell, Python a grafické runbooky. Sada runbook může mít několik parametrů s různými datovými typy nebo žádné parametry vůbec. Vstupní parametry lze povinné nebo volitelné a můžete přiřadit výchozí hodnotu pro volitelné parametry. Vstupní parametry pro sady runbook můžete přiřadit hodnoty, při spuštění prostřednictvím jednoho z dostupných metod. Tyto metody zahrnují spuštění runbooku z portálu nebo webové službě. Můžete také spustit jako podřízené sady runbook, která je volána vložené v jiné sady runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Konfigurace vstupních parametrů v sadách runbook Powershellu a pracovní postup prostředí PowerShell

Prostředí PowerShell a [runbooky pracovních postupů Powershellu](automation-first-runbook-textual.md) ve službě Azure Automation podporují vstupní parametry, které jsou definovány pomocí následující atributy:  

| **Vlastnost** | **Popis** |
|:--- |:--- |
| Typ |Povinná hodnota. Datový typ pro hodnotu parametru. Libovolného typu .NET je platný. |
| Název |Povinná hodnota. Název parametru. Toto musí být jedinečný v rámci sady runbook a může obsahovat jenom písmena, číslice nebo znak podtržítka. Musí začínat písmenem. |
| Povinné |Volitelné. Určuje, zda musí být zadána hodnota pro parametr. Pokud nastavíte **$true**, potom při spuštění runbooku, je třeba zadat hodnotu. Pokud nastavíte **$false**, pak hodnota je volitelná. |
| Výchozí hodnota |Volitelné. Určuje hodnotu, která se používá pro parametr, pokud není hodnota předaná při spuštění runbooku. Výchozí hodnota se dá nastavit pro libovolný parametr a bude automaticky nastavit parametr volitelný bez ohledu na povinná nastavení. |

Prostředí Windows PowerShell podporuje další atributy vstupní parametry než těch, které zde uvedeny, například ověřování, aliasy, a nastaví parametr. Ale automatizace Azure aktuálně podporuje pouze předchozí vstupní parametry.

Definici parametru v runboocích pracovního postupu Powershellu má následující obecné formulář, kde jsou několik parametrů oddělených čárkami.

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
> Když definujete parametry, pokud neurčíte **povinné** atribut, pak ve výchozím nastavení, považuje volitelný parametr. Navíc pokud nastavíte výchozí hodnotu pro parametr v runboocích pracovního postupu Powershellu, bude zpracován pomocí prostředí PowerShell jako volitelný parametr, bez ohledu na to **povinné** hodnota atributu.
> 
> 

Jako příklad nakonfigurujeme vstupní parametry runbooku pracovního postupu Powershellu, který zobrazí podrobnosti o virtuální počítače, jeden virtuální počítač nebo všechny virtuální počítače ve skupině prostředků. Tato sada runbook má dva parametry, jak je vidět na následujícím snímku obrazovky: název virtuálního počítače a název skupiny prostředků.

![Pracovní postup automatizace prostředí PowerShell](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

V této definici parametru parametry **$VMName** a **$resourceGroupName** jsou jednoduché parametry typu String. Ale prostředí PowerShell a pracovní postup prostředí PowerShell runbooky podporují všechny typy jednoduché a komplexní typy, jako například **objekt** nebo **PSCredential** pro vstupní parametry.

Pokud vaše sada runbook má vstupní parametr typu objektu, použijte prostředí PowerShell zatřiďovací tabulku s (název, hodnotu) páry předat hodnotu. Například pokud máte v sadě runbook následující parametr:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Můžete poté předat parametr následující hodnotu:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurace vstupní parametry v grafické runbooky

K [konfigurace grafický runbook](automation-first-runbook-graphical.md) s vstupní parametry, vytvoříme grafického runbooku, který zobrazí podrobnosti o virtuálních počítačích, buď jeden virtuální počítač nebo všechny virtuální počítače ve skupině prostředků. Konfigurace sady runbook se skládá z dvě hlavní aktivity, jak je popsáno níže.

[**Ověření Runbooků pomocí účtu spustit v Azure jako** ](automation-sec-configure-azure-runas-account.md) k ověření pomocí Azure.

[**Get-AzureRmVm** ](https://msdn.microsoft.com/library/mt603718.aspx) se získat vlastnosti virtuálního počítače.

Můžete použít [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) aktivity k vypsání názvy virtuálních počítačů. Aktivita **Get-AzureRmVm** dva parametry, **název virtuálního počítače** a **název skupiny prostředků**. Vzhledem k tomu, že tyto parametry může vyžadovat různé hodnoty, při každém spuštění sady runbook, můžete přidat vstupních parametrů do runbooku. Tady jsou kroky k přidání vstupních parametrů:

1. Vyberte grafický runbook z **Runbooky** okna a potom klikněte na [ **upravit** ](automation-graphical-authoring-intro.md) ho.
2. V editoru runbooků, klikněte na **vstup a výstup** otevřete **vstup a výstup** okno.
   
    ![Grafický runbook automatizace](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. **Vstup a výstup** zobrazuje seznam vstupních parametrů, které jsou definovány pro sadu runbook. V tomto okně můžete přidat nový vstupní parametr nebo upravit konfiguraci existující vstupní parametr. Chcete-li přidat nový parametr pro sadu runbook, klikněte na tlačítko **přidat vstup** otevřete **vstupní parametr Runbooku** okno. Zde můžete nakonfigurovat následující parametry:
   
   | **Vlastnost** | **Popis** |
   |:--- |:--- |
   | Název |Povinná hodnota. Název parametru. Toto musí být jedinečný v rámci sady runbook a může obsahovat jenom písmena, číslice nebo znak podtržítka. Musí začínat písmenem. |
   | Popis |Volitelné. Popis o účelu vstupní parametr. |
   | Typ |Volitelné. Datový typ, který je očekávaná hodnota parametru. Parametr podporované typy jsou **řetězec**, **Int32**, **Int64**, **Decimal**, **Boolean**, **data a času**, a **objekt**. Pokud datový typ není vybraná, je standardně **řetězec**. |
   | Povinné |Volitelné. Určuje, zda musí být zadána hodnota pro parametr. Pokud se rozhodnete **Ano**, potom při spuštění runbooku, je třeba zadat hodnotu. Pokud se rozhodnete **žádné**, pak hodnota není požadovaná při spuštění runbooku a může být nastaven výchozí hodnotu. |
   | Výchozí hodnota |Volitelné. Určuje hodnotu, která se používá pro parametr, pokud není hodnota předaná při spuštění runbooku. Výchozí hodnotu lze nastavit pro parametr, který není povinné. Pokud chcete nastavit výchozí hodnotu, zvolte **vlastní**. Tato hodnota se používá, pokud je při spuštění runbooku zadat jinou hodnotu. Zvolte **žádné** Pokud nechcete zadat žádnou výchozí hodnotu. |
   
    ![Přidat nové vstup](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Vytvořte dva parametry s následujícími vlastnostmi, které používá **Get-AzureRmVm** aktivity:
   
   * **Parametr1:**
     
     * Název – VMName
     * Typ – řetězec
     * Povinné – ne
   * **Parameter2:**
     
     * Název – název skupiny prostředků
     * Typ – řetězec
     * Povinné – ne
     * Výchozí hodnota - vlastní
     * Vlastní výchozí hodnota - \<název skupiny prostředků, která obsahuje virtuální počítače >
5. Jakmile přidáte parametry, klikněte na tlačítko **OK**. Teď si můžete zobrazit je do **vstup a výstup okna**. Klikněte na tlačítko **OK** znovu a potom klikněte na **Uložit** a **publikovat** runbooku.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurace vstupních parametrů v sadách runbook Python

Na rozdíl od prostředí PowerShell, pracovní postup prostředí PowerShell a grafické runbooky Python runbooky nepřebírají pojmenované parametry.
Všechny vstupní parametry jsou analyzovat jako pole argumentů hodnot.
Přístup k poli importováním `sys` modul Python skript a potom pomocí `sys.argv` pole.
Je důležité si uvědomit, že první prvek pole, `sys.argv[0]`, je název skriptu, tak, aby první skutečný vstupní parametr `sys.argv[1]`.

Příklad použití vstupní parametry v sadě runbook Python, naleznete v části [Můj první runbook Python ve službě Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Přiřazení hodnoty pro vstupní parametry v sadách runbook

Můžete předat hodnoty pro vstupní parametry v sadách runbook v následujících scénářích:

### <a name="start-a-runbook-and-assign-parameters"></a>Spuštění sady runbook a přiřaďte parametry

Sady runbook lze spustit mnoho způsobů: prostřednictvím portálu Azure, s webhook, jehož, pomocí rutin prostředí PowerShell, pomocí rozhraní REST API nebo pomocí sady SDK. Níže probereme různé metody pro spuštění sady runbook a přiřazení parametry.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Spusťte publikované sady runbook pomocí portálu Azure a přiřadit parametry

Pokud jste [spuštění runbooku](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), **spuštění Runbooku** otevře se okno a můžete zadat hodnoty pro parametry, které jste vytvořili.

![Začít používat portál](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

V popisku pod vstupní pole uvidíte atributy, které byly nastaveny pro parametr. Atributy zahrnují povinné nebo volitelné, typ a výchozí hodnota. V bublině nápovědy vedle názvu parametru se zobrazí všechny klíčové informace, které potřebujete učinit rozhodnutí o vstupní hodnoty parametrů. Tyto informace zahrnují, zda je parametr povinný nebo volitelné. Zahrnuje také typ a výchozí hodnota (pokud existuje) a další užitečné poznámky.

> [!NOTE]
> Podpora parametry typu String **prázdný** hodnoty řetězců.  Zadání **[EmptyString]** v vstupní parametr pole předá parametr prázdný řetězec. Také nepodporují parametry typu řetězec **Null** předáním hodnoty. Pokud předáte nemáte žádnou hodnotu pro parametr řetězce, pak prostředí PowerShell ji interpretuje jako hodnotu null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Spustit publikované sady runbook pomocí rutin prostředí PowerShell a přiřaďte parametry

* **Rutiny Azure Resource Manager:** můžete spustit runbook služby automatizace, který byl vytvořen ve skupině prostředků s použitím [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Příklad:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Rutiny modelu nasazení Azure classic:** můžete spustit runbook služby automatizace, který byl vytvořen v do výchozí skupiny prostředků pomocí [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Příklad:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Při spuštění sady runbook pomocí rutin prostředí PowerShell, parametr výchozí **MicrosoftApplicationManagementStartedBy** je vytvořena s hodnotou **prostředí PowerShell**. Tento parametr v můžete zobrazit **podrobnosti úlohy** okno.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Spuštění sady runbook pomocí sady SDK a přiřaďte parametry

* **Azure Resource Manager metoda:** sady runbook můžete spustit pomocí sady SDK programovací jazyk. Níže je fragmentu kódu C# pro spuštění sady runbook ve vašem účtu Automation. Můžete zobrazit všechny kódu v našem [úložiště GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
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
* **Metoda modelu nasazení Azure classic:** sady runbook můžete spustit pomocí sady SDK programovací jazyk. Níže je fragmentu kódu C# pro spuštění sady runbook ve vašem účtu Automation. Můžete zobrazit všechny kódu v našem [úložiště GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
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
  
  Chcete-li spustit tuto metodu, vytvořit adresář k uložení parametry runbooku **VMName** a **resourceGroupName**a jejich hodnoty. Spusťte sadu runbook. Zde je fragment kódu jazyka C# pro volání metody, která je definována výše.
  
  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Spuštění sady runbook pomocí rozhraní REST API a přiřaďte parametry
Úlohy runbooku můžete vytvořit a začít s REST API služby Azure Automation pomocí **PUT** metoda s následující URI žádosti:

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

V identifikátoru URI žádosti vyměňte následující parametry:

* **id předplatného:** ID vašeho předplatného Azure.  
* **Název cloudové služby:** služby název cloudu, na který by měl být požadavek odeslán.  
* **název účtu Automation:** název účtu automation, který je hostován v rámci zadané cloudové služby.  
* **id úlohy:** identifikátor GUID pro úlohu. Identifikátory GUID v prostředí PowerShell můžete vytvořit pomocí **[GUID]::NewGuid(). ToString()** příkaz.

Chcete-li předat parametry do úlohy runbooku, použijte textu požadavku. Jak dlouho trvá následující dvě vlastnosti zadaný ve formátu JSON:

* **Název sady Runbook:** vyžaduje. Název sady runbook pro úlohu spustit.  
* **Parametry sady Runbook:** volitelné. Slovník seznam parametrů v (název, hodnotu) formátu, kde název by měl být typu String a hodnota může být libovolná platná hodnota JSON.

Pokud chcete spustit **Get-AzureVMTextual** runbooku, který byl vytvořen již dříve s **VMName** a **resourceGroupName** jako parametry, pomocí následujícího formátu JSON pro textu požadavku.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

Stavový kód HTTP 201 je vrácena, pokud je úloha úspěšně vytvořen. Další informace o hlavičky odpovědi a text odpovědi, najdete v článku o tom, jak [vytvořit úlohu runbooku pomocí rozhraní REST API.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Otestování sady runbook a přiřaďte parametry
Při jste [otestovat verzi konceptu sady runbook](automation-testing-runbook.md) pomocí možnosti testu **testování** otevře se stránka a můžete nastavit hodnoty pro parametry, které jste vytvořili.

![Testování a přiřadit parametry](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Propojit plán s sady runbook a přiřaďte parametry
Můžete [propojit plán s](automation-schedules.md) do runbooku, aby sada runbook spuštěna v určitém čase. Když vytvoříte plán a sada runbook používá tyto hodnoty, když se spustila podle plánu přiřadíte vstupní parametry. Plán nelze uložit, dokud nebudou k dispozici všechny hodnoty povinný parametr.

![Parametry plánu a přiřazení](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Vytvoření webhooku pro sadu runbook a přiřazení parametry
Můžete vytvořit [webhooku](automation-webhooks.md) pro runbook a nakonfigurovat vstupní parametry runbooku. Webhook nelze uložit, dokud nebudou k dispozici všechny hodnoty povinný parametr.

![Vytvoření webhooku a přiřazení parametry](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Při spuštění sady runbook pomocí webhooku, předdefinované vstupní parametr **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** je odeslaný společně vstupní parametry, které jste definovali. Můžete kliknout na rozšíření **WebhookData** parametr další podrobnosti.

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Další postup
* Další informace o runbook vstup a výstup, najdete v části [Azure Automation: runbook vnořených sad runbook, vstup a výstup](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Podrobnosti o různých způsobech spouštění sady runbook najdete v tématu [spuštění sady runbook](automation-starting-a-runbook.md).
* Chcete-li upravit textový, přečtěte [úpravy textovou runbooky](automation-edit-textual-runbook.md).
* Upravit grafický runbook, najdete v tématu [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).

