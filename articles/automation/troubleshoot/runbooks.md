---
title: Řešení potíží s Runbooky Azure Automation
description: Zjistěte, jak řešit problémy pomocí runbooků Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b96d723f6c7ca423343c0586f59770abb55ada9f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929345"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Řešení potíží s runbooky

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Chyby ověřování při práci s runbooky Azure Automation.

### <a name="sign-in-failed"></a>Scénář: Přihlášení k účtu Azure se nezdařilo

#### <a name="issue"></a>Problém

Při práci s se zobrazí následující chyba `Add-AzureAccount` nebo `Connect-AzureRmAccount` rutiny.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud název assetu přihlašovacích údajů není platný nebo pokud nejsou platné uživatelské jméno a heslo, které jste použili k nastavení služby Automation asset přihlašovacích údajů.

#### <a name="resolution"></a>Řešení

Aby bylo možné zjistit, co je špatně, proveďte následující kroky:  

1. Ujistěte se, že není nutné žádné speciální znaky, včetně **@** znak v názvu asset přihlašovacích údajů Automation, který používáte pro připojení k Azure.  
2. Zkontrolujte, že můžete použít uživatelské jméno a heslo, které jsou uloženy v přihlašovacích údajích Azure Automation v místním prostředí PowerShell ISE editor. Můžete to provést spuštěním následující rutiny v prostředí PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Pokud se ověření nezdaří místně, to znamená, že nebyly správně nastavený přihlašovacích údajů Azure Active Directory. Odkazovat na [ověřování v Azure pomocí Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogový příspěvek se získat účet služby Azure Active Directory zařídit správné nastavení.  

### <a name="unable-to-find-subscription"></a>Scénář: Nepovedlo se najít předplatné Azure

#### <a name="issue"></a>Problém

Při práci s se zobrazí následující chyba `Select-AzureSubscription` nebo `Select-AzureRmSubscription` rutiny.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Chyba

K této chybě dochází, pokud název předplatného není platný nebo pokud uživatele Azure Active Directory, který se pokouší získat podrobnosti o předplatném není nakonfigurovaný jako správce předplatného.

#### <a name="resolution"></a>Řešení

Aby bylo možné určit, pokud byly správně ověřeny pro Azure a přístup k předplatnému, které se pokoušíte vyberte, proveďte následující kroky:  

1. Ujistěte se, že spouštíte **Add-AzureAccount** dřív, než spustíte **Select-AzureSubscription** rutiny.  
2. Pokud se stále zobrazí tato chybová zpráva, upravte kód tak, že přidáte **Get-AzureSubscription** následující rutina **Add-AzureAccount** rutiny a pak spusťte kód. Teď ověřte, zda výstup Get-AzureSubscription obsahuje podrobnosti o předplatném.  

   * Pokud nevidíte žádné předplatné podrobnosti ve výstupu, to znamená, že předplatné ještě není inicializován.  
   * Pokud se zobrazí podrobnosti o předplatném na výstupu, potvrďte, že používáte správné předplatné název nebo ID s **Select-AzureSubscription** rutiny.

### <a name="auth-failed-mfa"></a>Scénář: Ověřování do Azure se nezdařila, protože je povolené ověřování službou Multi-Factor Authentication

#### <a name="issue"></a>Problém

Zobrazí chybová zpráva při ověřování do Azure pomocí Azure uživatelského jména a hesla:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Příčina

Pokud máte služby Multi-Factor authentication na vašem účtu Azure, nemůžete použít uživatele služby Azure Active Directory pro ověření do Azure. Místo toho musíte použít certifikát nebo instančního objektu pro ověření do Azure.

#### <a name="resolution"></a>Řešení

Pomocí rutin modelu nasazení Azure classic pomocí certifikátu, najdete v tématu [vytváření a přidání certifikátu pro správu služeb Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Použití instančního objektu s rutiny Azure Resource Manageru, najdete v tématu [vytváří se instanční objekt pomocí webu Azure portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md) a [ověřování instančního objektu pomocí Azure Resource Manageru.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Běžné chyby při práci s runbooky

### <a name="job-attempted-3-times"></a>Scénář: Spuštění úlohy sady runbook došlo k pokusu o třikrát po sobě, ale její spuštění pokaždé, když se nezdařilo

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobeno z následujících důvodů:

1. Limit paměti. Na tom, kolik paměti přidělených Sandboxu nejsou nepřispějete [omezení služby Automation](../../azure-subscription-service-limits.md#automation-limits) tak úlohy může selhat, je pokud používá více než 400 MB paměti.

2. Nekompatibilní modul. Tato situace může nastat, pokud závislostí modulů nejsou správné, a pokud nejsou, vaše sada runbook obvykle vrací "Příkaz nebyl nalezen" nebo "Nelze vytvořit vazbu parametru" zprávy.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:

* Navrhované metody pro práci v rámci omezení paměti jsou rozdělit zatížení mezi více runbooky, není zpracování co nejvíce dat v paměti, nikoli k zápisu zbytečné výstup z vaší sady runbook, nebo zvažte počet kontrolních bodů zápisu do vašich pracovních postupů prostředí PowerShell sady runbook.  

* Aktualizovat moduly Azure pomocí následujících kroků [aktualizace modulů Azure Powershellu ve službě Azure Automation](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Modul nejde importovat nebo se importuje správně, ale jsou extrahovány žádné rutiny.

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Příčina

Je několik běžných příčin, které modul nemusí úspěšně importovat do Azure Automation:

#### <a name="resolution"></a>Řešení

Struktura neodpovídá struktuře Automation musí být v.

1. Modul je závislá na jiný modul, který nebyl nasazen do vašeho účtu Automation.
2. Modul chybí jeho závislosti do složky.
3. Rutina se používá k nahrání modulu a nedali úložiště úplnou cestu nebo nebyly načteny modulu pomocí adresy URL veřejně přístupná.

### <a name="quota-exceeded"></a>Ujistěte se, že modul má tento formát: ModuleName.Zip <a name="quota-exceeded"> </a>  název modulu nebo číslo verze    (ModuleName.psm1, ModuleName.psd1)

#### <a name="issue"></a>Problém

Otevření souboru .psd1 a zjistěte, jestli modul mají všechny závislosti.

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Příčina

Pokud ano, nahrajte do účtu Automation tyto moduly. Ujistěte se, že jsou ve složce modulu všechny odkazované knihovny DLL debuggle. Pokud nenalezli váš problém nebo nepovedlo se vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

#### <a name="resolution"></a>Řešení

Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Můžete upgradovat na úroveň Basic podle následujících kroků:  

1. Přihlaste se ke svému předplatnému Azure.  
2. Vyberte účet Automation, který chcete upgradovat.  
3. Klikněte na **nastavení** > **ceny**.
4. Klikněte na tlačítko **povolit** v dolní části stránky můžete upgradovat svůj účet na **základní** vrstvy.

### <a name="cmdlet-not-recognized"></a>Scénář: Rutina nebyl rozpoznán při spuštění sady runbook

#### <a name="issue"></a>Problém

Otevření souboru .psd1 a zjistěte, jestli modul mají všechny závislosti.

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Příčina

K této chybě dojde, pokud modul Powershellu nejde najít rutiny, které používáte ve své sadě runbook. To může být způsobeno modulu, který obsahuje rutinu chybí z účtu, dojde ke konfliktu názvu pomocí názvu sady runbook nebo rutina také existuje v jiném modulu a automatizace nemůže přeložit název.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:  

* Zkontrolujte, zda jste správně zadali název rutiny.  
* Ujistěte se, že rutina existuje ve vašem účtu Automation a že nebyly zjištěny žádné konflikty. Chcete-li ověřit, zda je k dispozici rutiny, otevřete sadu runbook v režimu úprav a vyhledání rutiny, které chcete najít v knihovně nebo spuštění `Get-Command <CommandName>`. Jakmile ověříte, které rutina je k dispozici na účet a že nejsou žádné název je v konfliktu s jinými rutinami nebo sady runbook, přidejte ho na plátno a ujistěte se, že používáte platný parametr nastavení v sadě runbook.  
* Pokud máte ke konfliktu názvů a rutina je k dispozici ve dvou různých modulech, můžete vyřešit pomocí plně kvalifikovaného názvu pro rutinu. Například můžete použít **ModuleName\CmdletName**.  
* Pokud spouštíte ve skupině hybrid worker runbooku v místním, ujistěte se, že modul nebo rutina je nainstalován na počítači, který je hostitelem procesu hybrid worker.

### <a name="evicted-from-checkpoint"></a>Scénář: Dlouho spuštěná sada runbook konzistentně selže s výjimkou: "Úloha nemůže pokračovat se spuštěným protože opakovaně byl vyřazen ze stejné kontrolní bod"

#### <a name="issue"></a>Problém

Toto chování je záměrné kvůli sledování "Spravedlivé sdílení" procesů v rámci Azure Automation, která automaticky pozastaví runbook, pokud se provede déle než tři hodiny. Vrácená chybová zpráva, ale neposkytuje "Co dál" možnosti.

#### <a name="cause"></a>Příčina

Sady runbook můžete pozastavit pro z několika důvodů. Pozastaví stane většinou z důvodu chyby. Například nezachycená výjimka v sadě runbook, selhání sítě nebo selhání v procesu Runbook Worker spuštění sady runbook, všechny příčina sady runbook pozastaví a začít od svého posledního kontrolního bodu po obnovení.

#### <a name="resolution"></a>Řešení

Dokumentované řešení k tomuto problému vyhnout, je použít kontrolní body v pracovním postupu. Další informace najdete v tématu [pracovní postupy Powershellu Learning](../automation-powershell-workflow.md#checkpoints). Podrobnější informace o "Spravedlivé sdílení" a kontrolního bodu najdete v článku tohoto blogu [pomocí kontrolních bodů v sadách Runbook](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

### <a name="long-running-runbook"></a>Scénář: Dlouho spuštěná sada runbook nepodaří dokončit

#### <a name="issue"></a>Problém

Toto chování je záměrné Azure pomocí sandboxů kvůli sledování "Spravedlivé sdílení" procesů v rámci Azure Automation, která automaticky pozastaví runbook, pokud se provede déle než tři hodiny.

#### <a name="cause"></a>Příčina

Sada runbook spustil přes 3 hodiny limit povolený spravedlivé sdílení v Sandboxu Azure

#### <a name="resolution"></a>Řešení

Doporučené řešení je chcete spouštět sadu runbook [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní pracovní procesy nejsou omezeny [spravedlivé sdílení](../automation-runbook-execution.md#fair-share) limit runbook 3 hodiny jsou Azure karantény.

## <a name="common-errors-when-importing-modules"></a>Běžné chyby při importu modulů

### <a name="module-fails-to-import"></a>Scénář: Chyby modulu k importu nebo rutin nelze provést po dokončení importu

#### <a name="issue"></a>Problém

Modul nejde importovat nebo se importuje správně, ale jsou extrahovány žádné rutiny.

#### <a name="cause"></a>Příčina

Je několik běžných příčin, které modul nemusí úspěšně importovat do Azure Automation:

* Struktura neodpovídá struktuře Automation musí být v.
* Modul je závislá na jiný modul, který nebyl nasazen do vašeho účtu Automation.
* Modul chybí jeho závislosti do složky.
* `New-AzureRmAutomationModule` Rutina se používá k nahrání modulu a nedali úložiště úplnou cestu nebo nebyly načteny modulu pomocí adresy URL veřejně přístupná.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:

* Ujistěte se, že modul má tento formát: ModuleName.Zip **->** název modulu nebo číslo verze **->** (ModuleName.psm1, ModuleName.psd1)
* Otevření souboru .psd1 a zjistěte, jestli modul mají všechny závislosti. Pokud ano, nahrajte do účtu Automation tyto moduly.
* Ujistěte se, že jsou ve složce modulu všechny odkazované knihovny DLL debuggle.

## <a name="next-steps"></a>Další postup

Pokud nenalezli váš problém nebo nepovedlo se vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.