---
title: Vytvoření modulu integrace pro Azure Automation
description: Kurz vás provede vytvořením, otestováním a ukázkovým použitím modulů integrace ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 579be1f240f5661503caea16f7a7e42ee31a2fdd
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495594"
---
# <a name="azure-automation-integration-modules"></a>Moduly integrace pro Azure Automation
PowerShell je základní technologií, která stojí za službou Azure Automation. Vzhledem k tomu, že Azure Automation je postavená na PowerShellu, jsou moduly PowerShellu klíčem k rozšiřitelnosti služby Azure Automation. V tomto článku vás provedeme specifiky používání služby Azure Automation modulů Powershellu, jsou označovány jako "Moduly integrace" a osvědčené postupy pro vytváření vlastních modulů Powershellu, abyste měli jistotu, že fungují jako moduly integrace v rámci Azure Automatizace. 

## <a name="what-is-a-powershell-module"></a>Co je modul PowerShellu?
Modul PowerShellu je skupina rutin PowerShellu, například **Get-Date** nebo **Copy-Item**, které můžete používat v konzole PowerShellu, skriptech, pracovních postupech, runboocích a prostředcích DSC PowerShellu, například WindowsFeature nebo File, které můžete používat v konfiguracích DSC PowerShellu. Všechny funkce PowerShellu jsou dostupné prostřednictvím rutin a prostředků DSC a každá rutina nebo prostředek DSC se zálohuje pomocí modulu PowerShellu (většina z nich se dodává přímo s PowerShellem). Například rutina **Get-Date** je součástí modulu PowerShellu Microsoft.PowerShell.Utility a rutina **Copy-Item** je součástí modulu PowerShellu Microsoft.PowerShell.Management. Prostředek DSC Balíček je součástí modulu PowerShellu PSDesiredStateConfiguration. Oba tyto moduly se dodávají spolu s PowerShellem. Řada modulů PowerShellu se nedodává jako součást PowerShellu, ale distribuuje se s produkty prvních nebo třetích stran, například s aplikací System Center 2012 Configuration Manager, nebo prostřednictvím rozsáhlé komunity PowerShellu na místech, jako je třeba Galerie prostředí PowerShell. Moduly jsou užitečné, protože složité úlohy zjednodušují prostřednictvím zapouzdřené funkcionality.  Další informace o [modulech PowerShellu najdete na webu MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Co je modul integrace pro Azure Automation?
Modul integrace se liší od modulu Powershellu. Je to prostě modul PowerShellu, který volitelně obsahuje jeden další soubor – soubor metadat určujících typ připojení Azure Automation, který se bude používat s rutinami modulu v runboocích. Bez ohledu na volitelný soubor můžete tyto moduly PowerShellu importovat do Azure Automation, abyste jejich rutiny zpřístupnili pro použití v rámci runbooků a jejich prostředků DSC, které jsou dostupné pro použití v rámci konfigurací DSC. Azure Automation tyto moduly na pozadí ukládá a v čase spuštění úlohy runbooku a úlohy kompilace DSC je načte do izolovaného prostoru (sandbox) v Azure Automation, kde se runbooky spustí a konfigurace DSC se zkompilují. Veškeré prostředky DSC v modulech se také automaticky umístí na server vyžádané replikace Automation DSC, aby si je mohly vyžádat počítače, které chtějí použít konfigurace DSC.  

Řadu modulů Azure PowerShellu dodáváme jako součást služby Azure Automation, abyste mohli okamžitě začít s automatizací správy Azure. Samozřejmě můžete importovat další moduly PowerShellu pro libovolný systém, službu nebo nástroj, které chcete integrovat. 

> [!NOTE]
> Některé moduly se dodávají jako "globální"moduly ve službě Automation. Tyto globální moduly jsou dostupné po vytvoření účtu automation, a My je aktualizujeme někdy, který je automaticky nasadí do vašeho účtu automation. Pokud nechcete, aby se automaticky aktualizují, můžete vždy importovat stejný modul sami a, který má přednost před globální verzí modulu, dodávanou v službě. 

Formát, ve kterém budete importovat balíček modulu integrace, je komprimovaný soubor se stejným názvem, jako má modul, a navíc s příponou .zip. Obsahuje modul Windows PowerShellu a všechny podpůrné soubory včetně souboru manifestu (.psd1), pokud ho modul obsahuje.

Pokud má modul obsahovat typ připojení Azure Automation, musí obsahovat také soubor s názvem `<ModuleName>-Automation.json`, který určuje vlastnosti typu připojení. To je umístěn ve složce modulu vašeho komprimovaného souboru .zip soubor json a obsahuje pole "připojení", která je nutná pro připojení k systému nebo službě, které modul představuje. Ukončí se do vytvoření typu připojení ve službě Azure Automation. Pomocí tohoto souboru můžete pro typ připojení modulu nastavit názvy polí, typy polí a možnosti, jestli mají být pole šifrovaná nebo volitelná. Níže uvádíme šablonu ve formátu json:

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

Pokud jste nasadili Service Management Automation a vytvářením balíčků modulů integrace pro svoje automatizační runbooky, to by měla vypadat povědomě vám. 

## <a name="authoring-best-practices"></a>Osvědčené postupy pro vytváření obsahu
I když jsou moduly integrace v podstatě moduly Powershellu, je stále několik věcí, které doporučujeme, abyste že měli zvážit při vytváření modulů Powershellu, aby bylo nejužitečnější ve službě Azure Automation. Některé z nich se týkají konkrétně Azure Automation a některé z nich jsou užitečné k tomu, aby moduly dobře fungovaly v pracovním postupu Powershellu bez ohledu na to, jestli používáte Automation. 

1. Ke každé rutině v modulu přidejte i stručný obsah, popis a pomocný identifikátor URI. V PowerShellu můžete definovat určité informace nápovědy k rutinám, abyste uživatelům umožnili zobrazit nápovědu k jejich používání pomocí rutiny **Get-Help**. Následuje příklad, jak můžete definovat stručný a pomocný identifikátor URI pro modul Powershellu napsané v souboru .psm1.<br>  
   
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
   <br> Zadání těchto informací nezajistí zobrazení této nápovědy jenom pomocí rutiny **Get-Help** v konzole PowerShellu, ale zobrazí tuto funkci nápovědy také v rámci Azure Automation.  Například při vkládání aktivit během vytváření obsahu runbooku. Kliknutím na "Zobrazit podrobnou nápovědu" otevřete pomocný identifikátor URI na jiné kartě webového prohlížeče, které používáte pro přístup ke službě Azure Automation.<br>![Nápověda k modulu integrace](media/automation-integration-modules/automation-integration-module-activitydesc.png)
1. Pokud se modul spouští pro vzdálený systém,

    a. Musí obsahovat soubor s metadaty modulu integrace, který definuje informace potřebné pro připojení ke vzdálenému systému (to znamená typ připojení).  
    b. Každá rutina v modulu musí být schopná přijmout objekt připojení (instanci takového typu připojení) jako parametr.  

    Používání rutin v modulu v Azure Automation bude snazší, když povolíte předávání objektů s poli typu připojení jako parametru pro rutinu. Tímto způsobem uživatelé nemají mapovat parametry prostředku připojení na odpovídající parametry rutiny při každém volání rutiny. Výše uvedený příklad runbooku používá asset připojení Twilio, který se nazývá CorpTwilio, pro přístup k assetu Twilio a k vrácení všech telefonních čísel na účtu.  Všimli jste si, jak mapuje pole připojení na parametry rutiny?<br>
   
    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Jednodušším a lepším způsobem je předat objekt připojení přímo do rutiny –
   
    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    Takové chování můžete svým rutinám povolit tak, že jim dovolíte přijímat objekt připojení přímo jako parametr místo určování parametrů prostým přijímáním polí připojení. Obvykle byste měli u každé sady parametrů tak, aby uživatel bez použití služby Azure Automation mohl vaše rutiny volat bez vytváření zatřiďovací tabulku tak, aby fungoval jako objekt připojení. Níže uvedená sada parametrů **SpecifyConnectionFields** slouží k postupnému předávání vlastností polí připojení. **UseConnectionObject** vám umožní předat připojení přímo. Jak můžete vidět, rutina Send-TwilioSMS v [modulu PowerShellu Twilio](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) umožňuje předání oběma způsoby: 
   
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
   <br>
1. Definujte výstupní typ pro všechny rutiny v modulu. Definování typu výstupu rutiny umožňuje technologii IntelliSense, aby vám v době návrhu pomohla zjistit výstupní vlastnosti rutiny, které použijete při vytváření obsahu. To se hodí zejména při vytváření grafického obsahu runbooku Automation, kde je znalost doby návrhu klíčová pro snadnou práci s modulem.<br><br> ![Typ výstupu grafického runbooku](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Podobá se funkci „našeptávání“ výstupu rutiny v integrovaném skriptovacím prostředí PowerShellu bez nutnosti jeho spuštění.<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
1. Rutiny v modulu nesmí přijímat komplexní typy objektů jako parametry. Pracovní postup PowerShellu se liší od PowerShellu tím, že komplexní typy ukládá v deserializované podobě. Primitivní typy zůstanou jako primitiva, ale komplexní typy budou převedené na jejich deserializované verze, které jsou v podstatě kontejnery vlastností. Pokud jste například použili rutinu **Get-Process** v runbooku (nebo v pracovním postupu PowerShellu), měla by vrátit objekt typu [Deserialized.System.Diagnostic.Process] a ne očekávaný typ [System.Diagnostic.Process]. Tento typ má stejné vlastnosti jako nedeserializovaný typ, ale nemá žádné metody. A pokud se pokusíte předat tuto hodnotu jako parametr rutiny, kde rutina očekává hodnotu [System.Diagnostic.Process] pro tento parametr, se zobrazí následující chybová zpráva: *Nelze zpracovat transformaci argumentu na parametru 'proces'. Chyba: "Nelze převést hodnotu"System.Diagnostics.Process (CcmExec)"typu"Deserialized.System.Diagnostics.Process"typ"System.Diagnostics.Process".*   Je to proto, že došlo k neshodě typů mezi očekávaným typem [System.Diagnostic.Process] a daným typem [Deserialized.System.Diagnostic.Process]. Způsobem řešení tohoto problému je zajistit, aby rutiny modulu nepřebíraly komplexní typy jako parametry. Následuje ukázka nesprávného způsobu řešení.
   
    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    A tady je správný způsob, který přijímá primitivum, které může rutina interně použít k převzetí a používání komplexního objektu. Vzhledem k tomu, že rutiny se spouštějí v kontextu PowerShellu a ne v kontextu pracovního postupu PowerShellu, $process se uvnitř rutiny stane správným typem [System.Diagnostic.Process].  
   
    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Assety připojení v runboocích představují zatřiďovací tabulky, které jsou komplexním typem, a ještě se zdát, že tyto zatřiďovací tabulky být bezchybně předávané do rutin pro jejich – parametr připojení, se bez výjimky pro přetypování. Technicky jsou některé typy PowerShellu schopné provést přetypování správně ze serializované podoby do deserializované a proto mohou být předány do rutin jako parametry, které přijímají nedeserializovaný typ. Zatřiďovací tabulka je jedním z nich. Je možné implementovat způsobem, který můžou správně deserializovat také Autor modulu definované typy, ale existují některé kompromisy vzít v úvahu. Tento typ musí mít výchozí konstruktor, musí mít všechny svoje veřejné vlastnosti a musí mít PSTypeConverter. Ale pro už definovaných typů, které Autor modulu nevlastní, neexistuje žádný způsob, jak "Opravit", proto doporučení a vyhnuli používání komplexních typů jako parametrů. Tip pro vytváření Runbooků: Pokud nějakého důvodu vaše rutiny je potřeba provést komplexní typ parametru, nebo používáte cizí modul, který vyžaduje parametr komplexního typu, řešením pro runbooky pracovních postupů Powershellu a pracovní postupy Powershellu v místním Powershellu, je zabalit rutinu která generuje komplexní typ a rutinu, která využívá komplexní typ, do stejné aktivity InlineScript. Vzhledem k tomu, že InlineScript svůj obsah spouští jako PowerShell spíš než jako pracovní postup PowerShellu, rutina generující komplexní typ vytvoří tento správný typ a ne deserializovaný komplexní typ.
1. Nastavte všechny rutiny v modulu jako bezstavové. Pracovní postup PowerShellu spouští každou rutinu volanou v pracovním postupu v jiné relaci. To znamená, že rutiny, které závisí na stavu relace, která je vytvořená nebo upravená jinými rutinami ve stejném modulu, nebudou v runboocích pracovního postupu PowerShellu fungovat.  Následuje příklad špatného postupu.
   
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
   <br>
1. Modul musí být plně obsažený v balíčku, na který můžete použít příkaz Xcopy. Moduly Azure Automation se distribuují do izolovaného prostoru (sandbox), a tak když se runbooky potřebují spustit, musí pracovat nezávisle na hostiteli, na kterém běží. To znamená, že byste měli být schopni Zip si balíček modulu ji přesunout do jiného hostitele se stejnou nebo novější verzí Powershellu a fungovat jako obvykle, při importu do prostředí PowerShell takového hostitele. Aby to tak proběhlo, nesmí být modul závislý na žádném souboru mimo složku modulu (složka, kterou zazipujete při importu do Azure Automation) ani na žádném jedinečném nastavení registrů na hostiteli, například nastavení z instalace produktu. Pokud tento osvědčený postup nedodržíte, modul nebude ve službě Azure Automation funkční.  

## <a name="next-steps"></a>Další postup

* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o vytváření modulů PowerShellu najdete v článku [Psaní modulu Windows PowerShellu](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)


