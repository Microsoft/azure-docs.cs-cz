---
title: Vytvoření modulu integrace pro Azure Automation
description: Kurz vás provede vytvořením, otestováním a ukázkovým použitím modulů integrace ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990338"
---
# <a name="azure-automation-integration-modules"></a>Moduly integrace pro Azure Automation

PowerShell je základní technologií, která stojí za službou Azure Automation. Vzhledem k tomu, že Azure Automation je postavená na PowerShellu, jsou moduly PowerShellu klíčem k rozšiřitelnosti služby Azure Automation. V tomto článku se dozvíte, jaké jsou specifikace použití Azure Automation modulů Powershellu, označuje jako integrační moduly. Dozvíte se víc i doporučené postupy pro vytváření vlastních modulů Powershellu, abyste měli jistotu, že fungují jako moduly integrace v rámci Azure Automation. 

## <a name="what-is-a-powershell-module"></a>Co je modul PowerShellu?

Modul Powershellu je skupina rutin Powershellu, například **Get-Date** nebo **Copy-Item**, které lze používat z:

* Konzola Powershellu
* oddílů
* workflows
* Sady Runbook
* Prostředky DSC

Všechny funkce Powershellu je dostupné prostřednictvím rutin a prostředky DSC. Každou rutinu a prostředek DSC se zálohuje pomocí modulu Powershellu, mnoho z nich se dodává přímo s Powershellem. Například **Get-Date** rutiny je součástí `Microsoft.PowerShell.Utility` modul prostředí PowerShell a **Copy-Item** rutiny je součástí `Microsoft.PowerShell.Management` modul prostředí PowerShell a prostředek DSC balíček je součástí Modulu Powershellu PSDesiredStateConfiguration. Oba tyto moduly se dodávají spolu s PowerShellem. Řada modulů Powershellu není dodávají jako součást Powershellu. Tyto moduly se distribuují s produkty prvních nebo třetích stran nebo na místech, jako je Galerie prostředí PowerShell. Moduly jsou užitečné, protože složité úlohy zjednodušují prostřednictvím zapouzdřené funkcionality.  Další informace o [modulech PowerShellu najdete na webu MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Co je modul integrace pro Azure Automation?

Modul integrace se liší od modulu Powershellu. Je to prostě modul PowerShellu, který volitelně obsahuje jeden další soubor – soubor metadat určujících typ připojení Azure Automation, který se bude používat s rutinami modulu v runboocích. Moduly Powershellu můžete importovat do Azure Automation k jejich rutiny zpřístupnili pro použití v rámci sady runbook a jejich prostředků DSC k dispozici pro použití v rámci konfigurací DSC. Na pozadí Azure Automation tyto moduly ukládá a v úlohy runbooku a čas spuštění úlohy kompilace DSC je načte do karantény Azure Automation, kde spuštění sady runbook a kompilaci konfigurace DSC. Všechny prostředky DSC v modulech se také automaticky umístí na serveru vyžádané replikace automatizace DSC. Si je mohly vyžádat počítače při jejich použít konfigurace DSC.  

Řadu modulů Azure Powershellu dodáváme úprav ve službě Azure Automation. Ale můžete importovat další moduly Powershellu pro libovolnou systém, službu nebo nástroj, kterou chcete integrovat s.

> [!NOTE]
> Některé moduly se dodávají jako **globální moduly** ve službě Automation. Tyto globální moduly jsou dostupné po vytvoření účtu automation, a My je aktualizujeme někdy, který je automaticky nasadí do vašeho účtu automation. Pokud nechcete, aby se automaticky aktualizují, můžete vždy importovat stejný modul sami a, který má přednost před globální verzí modulu, dodávanou v službě.

Formát, ve kterém budete importovat balíček modulu integrace, je komprimovaný soubor se stejným názvem, jako má modul, a navíc s příponou .zip. Obsahuje modul Windows PowerShellu a všechny podpůrné soubory včetně souboru manifestu (.psd1), pokud ho modul obsahuje.

Pokud má modul obsahovat typ připojení Azure Automation, musí obsahovat také soubor s názvem `<ModuleName>-Automation.json`, který určuje vlastnosti typu připojení. Toto je soubor json, který je umístěn ve složce modulu vašeho komprimovaného souboru .zip. Tento soubor obsahuje pole **připojení** , která je nutná pro připojení k systému nebo službě, které modul představuje. Tato konfigurace končí vytvoření typu připojení ve službě Azure Automation. Pomocí tohoto souboru můžete pro typ připojení modulu nastavit názvy polí, typy polí a možnosti, jestli mají být pole šifrovaná nebo volitelná. Následující příklad uvádíme šablonu ve formátu json:

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Pokud jste nasadili Service Management Automation a vytvářením balíčků modulů integrace pro svoje automatizační runbooky, tato šablona by měla vypadat povědomě vám.

## <a name="authoring-best-practices"></a>Osvědčené postupy pro vytváření obsahu

I když jsou moduly integrace moduly Powershellu, je stále několik věcí, které doporučujeme, abyste že měli zvážit při vytváření modulu Powershellu pro použití ve službě Azure Automation. Některá doporučení jsou užitečné k tomu, aby moduly dobře fungovaly v prostředí PowerShell Workflow.

1. Ke každé rutině v modulu přidejte i stručný obsah, popis a pomocný identifikátor URI. V PowerShellu můžete definovat určité informace nápovědy k rutinám, abyste uživatelům umožnili zobrazit nápovědu k jejich používání pomocí rutiny **Get-Help**. Následuje příklad, jak můžete definovat stručný a pomocný identifikátor URI pro modul Powershellu napsané v souboru .psm1. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Zadání těchto informací zobrazí tuto nápovědu pomocí **Get-Help** rutiny v konzole prostředí PowerShell. Tyto informace se také zobrazuje v rámci Azure Automation.  Například při vkládání aktivit během vytváření obsahu runbooku. Kliknutím na "Zobrazit podrobnou nápovědu" otevřete pomocný identifikátor URI na jiné kartě webového prohlížeče, které používáte pro přístup ke službě Azure Automation.

   ![Nápověda k modulu integrace](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Pokud se modul spouští pro vzdálený systém:

   1. Musí obsahovat soubor s metadaty modulu integrace, který definuje informace potřebné pro připojení ke vzdálenému systému (to znamená typ připojení).
   2. Každá rutina v modulu musí být schopná přijmout objekt připojení (instanci takového typu připojení) jako parametr.  

    Používání rutin v modulu v Azure Automation bude snazší, když povolíte předávání objektů s poli typu připojení jako parametru pro rutinu. To umožňuje uživatelům mapovat parametry prostředku připojení na odpovídající parametry rutiny při každém volání rutiny.

    Výše uvedený příklad runbooku používá asset připojení Twilio, který se nazývá CorpTwilio, pro přístup k assetu Twilio a k vrácení všech telefonních čísel na účtu.  Všimněte si, jak mapuje pole připojení na parametry rutiny?

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Jednodušším a lepším způsobem, abyste toto chování je předat objekt připojení přímo do rutiny –

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Chování podobně jako v předchozím příkladu můžete svým rutinám povolit neboť jim umožňuje přijímat objekt připojení přímo jako parametr místo přijímáním polí připojení pro parametry. Obvykle byste měli u každé sady parametrů tak, aby uživatel bez použití služby Azure Automation mohl vaše rutiny volat bez vytváření zatřiďovací tabulku tak, aby fungoval jako objekt připojení. Sada parametrů **SpecifyConnectionFields**, slouží k předání připojení vlastnosti pole jeden po druhém. **UseConnectionObject** vám umožní předat připojení přímo. Jak můžete vidět, rutina Send-TwilioSMS v [modulu PowerShellu Twilio](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) umožňuje předání oběma způsoby:

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Definujte výstupní typ pro všechny rutiny v modulu. Definování typu výstupu rutiny umožňuje technologii IntelliSense, aby vám v době návrhu pomohla zjistit výstupní vlastnosti rutiny, které použijete při vytváření obsahu. To se hodí zejména při automatizaci vytváření grafického obsahu runbooku, kde je znalost doby návrhu klíčová pro snadnou práci s modulem.

   ![Typ výstupu grafického runbooku](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Toto chování je podobné funkci "našeptávání" výstupu rutiny v prostředí PowerShell ISE bez nutnosti jeho spuštění.

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Rutiny v modulu nesmí přijímat komplexní typy objektů pro parametry. Pracovní postup PowerShellu se liší od PowerShellu tím, že komplexní typy ukládá v deserializované podobě. Primitivní typy zůstanou jako primitiva, ale komplexní typy převést na jejich deserializované verze, které jsou v podstatě kontejnery vlastností. Pokud jste například použili rutinu **Get-Process** v runbooku (nebo v pracovním postupu PowerShellu), měla by vrátit objekt typu [Deserialized.System.Diagnostic.Process] a ne očekávaný typ [System.Diagnostic.Process]. Tento typ má stejné vlastnosti jako nedeserializovaný typ, ale nemá žádné metody. A pokud se pokusíte předat tuto hodnotu jako parametr rutiny, kde rutina očekává hodnotu [System.Diagnostic.Process] pro tento parametr, se zobrazí následující chybová zpráva: *Nelze zpracovat transformaci argumentu na parametru 'proces'. Chyba: "Nelze převést hodnotu"System.Diagnostics.Process (CcmExec)"typu"Deserialized.System.Diagnostics.Process"typ"System.Diagnostics.Process".*   Je to proto, že došlo k neshodě typů mezi očekávaným typem [System.Diagnostic.Process] a daným typem [Deserialized.System.Diagnostic.Process]. Způsobem řešení tohoto problému je zajistit, že rutiny modulu nevyřídí komplexních typů jako parametrů. Následuje ukázka nesprávného způsobu řešení.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    Správný způsob je do jednoduchého typu, který se používá interně rutina získejte komplexní objekt a jeho použití. Vzhledem k tomu, že rutiny se spouštějí v kontextu PowerShellu a ne v kontextu pracovního postupu PowerShellu, $process se uvnitř rutiny stane správným typem [System.Diagnostic.Process].  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Assety připojení v runboocích představují zatřiďovací tabulky, které jsou komplexním typem, a ještě se zdát, že tyto zatřiďovací tabulky být bezchybně předávané do rutin pro jejich "parametr připojení, se bez výjimky pro přetypování. Technicky jsou některé typy PowerShellu schopné provést přetypování správně ze serializované podoby do deserializované a proto mohou být předány do rutin jako parametry, které přijímají nedeserializovaný typ. Zatřiďovací tabulka je jedním z nich. Je možné implementovat způsobem, který můžou správně deserializovat také Autor modulu definované typy, ale existují některé kompromisy vzít v úvahu. Tento typ musí mít výchozí konstruktor, musí mít všechny svoje veřejné vlastnosti a musí mít PSTypeConverter. Ale pro už definovaných typů, které Autor modulu nevlastní, neexistuje žádný způsob, jak "Opravit", proto doporučení a vyhnuli používání komplexních typů jako parametrů. Tip pro vytváření Runbooků: Pokud vaše rutiny potřebují přijmout parametr komplexního typu, řešením pro pracovní postupy Powershellu je zabalit rutinu, která generuje komplexní typ a rutinu, která využívá komplexní typ, do stejné aktivity InlineScript. Vzhledem k tomu, že InlineScript svůj obsah spouští jako PowerShell spíš než jako pracovní postup PowerShellu, rutina generující komplexní typ vytvoří tento správný typ a ne deserializovaný komplexní typ.

5. Nastavte všechny rutiny v modulu jako bezstavové. Pracovní postup PowerShellu spouští každou rutinu volanou v pracovním postupu v jiné relaci. To znamená, že rutiny, které závisí na stavu relace vytvořená nebo upravená jinými rutinami ve stejném modulu nebudou v runboocích pracovního postupu Powershellu fungovat.  Následuje příklad špatného postupu.
   
    ```powershell
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```

6. Modul musí být plně obsažený v balíčku, na který můžete použít příkaz Xcopy. Moduly Azure Automation jsou distributd k sandboxy Automation, když se runbooky potřebují spustit. Moduly musí pracovat nezávisle, které běží na hostiteli. By měl být schopen Zip nahoru a přesunout balíček modulu a fungovat jako obvykle po importování do jiného hostitele prostředí PowerShell. V pořadí, které se provedou nesmí být modul závislý na žádném souboru mimo složku modulu. Tato složka je složka, kterou zazipujete při importu modulu do služby Azure Automation. Modul by neměl závisí také na žádném jedinečném nastavení registrů na hostiteli, jako je nastavena tato nastavení při instalaci produktu. Pokud tento osvědčený postup nedodržíte, modul nebude možné použít ve službě Azure Automation.  

7. Pokud odkazující na [moduly Azure Powershellu Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) v modulu, ujistěte se také nejsou odkazující na `AzureRM`. `Az` Modulu nelze použít ve spojení s `AzureRM` moduly. `Az` je podporován v sadách runbook, ale nejsou ve výchozím nastavení importován. Další informace o `Az` moduly a důležité informace o vzít v úvahu, najdete v části [Az podpora modulu ve službě Azure Automation](az-modules.md).

## <a name="next-steps"></a>Další postup

* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o vytváření modulů PowerShellu najdete v článku [Psaní modulu Windows PowerShellu](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
