---
title: Řešení chyb pomocí Runbooků Automation v Azure
description: Zjistěte, jak vyřešit problémy s runbooky služby automatizace Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bb340b8439927f191bc4a22f385d85d4e21b1cdb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063795"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Řešení chyb pomocí runbooků

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Chyby ověřování při práci se sadami runbook automatizace Azure.

### <a name="sign-in-failed"></a>Scénář: Přihlaste se k účtu Azure se nezdařilo

#### <a name="issue"></a>Problém

Při práci s se zobrazí následující chyba `Add-AzureAccount` nebo `Connect-AzureRmAccount` rutiny.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Příčina

K této chybě dojde, pokud název asset přihlašovacích údajů není platný nebo pokud uživatelské jméno a heslo, které jste použili k nastavení asset přihlašovacích údajů automatizace nejsou platné.

#### <a name="resolution"></a>Řešení

Chcete-li určit příčinu chyby, proveďte následující kroky:  

1. Ujistěte se, že nemáte žádné speciální znaky, včetně **@** znak v názvu asset přihlašovacích údajů automatizace, kterou používáte pro připojení k Azure.  
2. Zkontrolujte, můžete použít uživatelské jméno a heslo, které jsou uložené v Azure Automation přihlašovací údaje ve svém místním prostředí PowerShell ISE editoru. Můžete provést spuštěním následující rutiny v integrovaném Skriptovacím prostředí PowerShell:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Pokud ověření selže místně, znamená to, zda nebyly správně nastavená přihlašovacích údajů Azure Active Directory. Odkazovat na [ověřování na Azure pomocí Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) příspěvku na blogu získat správně nastaven účet služby Azure Active Directory.  

### <a name="unable-to-find-subscription"></a>Scénář: Nelze najít předplatné Azure

#### <a name="issue"></a>Problém

Při práci s se zobrazí následující chyba `Select-AzureSubscription` nebo `Select-AzureRmSubscription` rutin.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Chyba

K této chybě dojde, pokud není platný název odběru, nebo pokud uživatele Azure Active Directory, který se pokouší získat podrobnosti o předplatném není nakonfigurován jako správce předplatného.

#### <a name="resolution"></a>Řešení

Chcete-li zjistit, zda jste správně ověřené Azure a mít přístup k odběru, který chcete vybrat, proveďte následující kroky:  

1. Ujistěte se, že spustíte **Add-AzureAccount** dřív, než spustíte **Select-AzureSubscription** rutiny.  
2. Pokud se pořád zobrazí tato chybová zpráva, upravit kód tak, že přidáte **Get-AzureSubscription** následující rutina **Add-AzureAccount** rutiny a potom spusťte kód. Nyní ověřte, zda výstup Get-AzureSubscription obsahuje podrobnosti o vašem předplatném.  

   * Pokud nevidíte všechny podrobnosti o předplatném ve výstupu, znamená to, že odběr není inicializován ještě.  
   * Pokud se zobrazí podrobnosti o předplatném ve výstupu, potvrďte, že používáte správné předplatné název nebo ID s **Select-AzureSubscription** rutiny.

### <a name="auth-failed-mfa"></a>Scénář: Ověřování na Azure se nezdařila, protože je povolené vícefaktorového ověřování

#### <a name="issue"></a>Problém

Zobrazí následující chybě při ověřování do Azure s Azure uživatelské jméno a heslo:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Příčina

Pokud máte služby Multi-Factor authentication na vašem účtu Azure, nebudete moct používat uživatele služby Azure Active Directory k ověření do Azure. Místo toho musíte použít certifikát nebo objekt služby pro ověřování v Azure.

#### <a name="resolution"></a>Řešení

Pomocí rutin modelu nasazení Azure classic používat certifikát, najdete v tématu [vytváření a přidání certifikátu pro správu služby Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Hlavní název služby pomocí rutiny Azure Resource Manager, najdete v tématu [vytváření služby objektu zabezpečení pomocí portálu Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md) a [ověřování hlavní název služby pomocí Azure Resource Manageru.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Běžné chyby při práci se sadami runbook

### <a name="job-attempted-3-times"></a>Scénář: Spuštění úlohy sady runbook došlo k pokusu o třikrát, ale její spuštění pokaždé, když se nezdařilo

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobeno z následujících důvodů:

1. Limit paměti. Neexistují zdokumentovaných omezení na množství paměti přidělené izolovaném prostoru [omezení služby Automation](../../azure-subscription-service-limits.md#automation-limits) , úloha může selhat ho pokud používá více než 400 MB paměti.

2. Modul není kompatibilní. Tato situace může nastat, pokud modul závislosti nejsou správné, a pokud tomu tak není, vaše sada runbook obvykle vrátí "Příkaz nebyl nalezen" nebo "Nelze vytvořit vazbu parametru" zprávy.

#### <a name="resolution"></a>Řešení

Problém opravte některý z následujících řešení:

* Navrhované metody pro práci v rámci limit paměti mají rozdělit zatížení mezi několika runbooky, není zpracovat tolik data v paměti, není k zápisu výstupu nepotřebné z vaší sady runbook nebo zvažte kolik kontrolní body zápisu do vašeho pracovního postupu Powershellu sady runbook.  

* Aktualizovat moduly Azure podle pokynů [aktualizaci modulů prostředí Azure PowerShell ve službě Azure Automation](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Scénář: Runbook nezdaří z důvodu deserializovaný objekt

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Příčina

Pokud vaše sada runbook je pracovní postup prostředí PowerShell, uloží na komplexní objekty deserializovaný formát s cílem zachovat vašemu stavu sady runbook, pokud je pozastavená pracovního postupu.

#### <a name="resolution"></a>Řešení

Některé z následujících tří řešení řešení tohoto problému:

1. Pokud jsou potrubí komplexní objekty z jedné rutiny do druhého, zalomení tyto rutiny v InlineScript.
2. Název nebo hodnotu, která je třeba předejte z komplexní objekt neprochází celý objekt.
3. Použijte Powershellový runbook místo runbook pracovního postupu Powershellu.

### <a name="quota-exceeded"></a>Scénář: Runbook úloha se nezdařila, protože překročil přidělenou kvótu

#### <a name="issue"></a>Problém

Úlohu runbook selže s chybou:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Příčina

K této chybě dojde, pokud provádění úlohy překročí kvótu volné 500 minutu pro váš účet. Tato kvóta se vztahuje na všechny typy spuštění úlohy například testování úlohu, spouštění úlohy z portálu, provádění úlohy pomocí webhooků a plánování úlohu provést pomocí portálu Azure nebo ve vašem datovém centru. Další informace o cenách pro automatizaci najdete v tématu [ceny automatizace](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Řešení

Pokud chcete použít více než 500 minut zpracování za měsíc, budete muset změnit své předplatné z volné úroveň na úroveň Basic. Můžete upgradovat na úroveň Basic provedením následujících kroků:  

1. Přihlaste se ke svému předplatnému Azure.  
2. Vyberte účet Automation, který chcete upgradovat.  
3. Klikněte na **nastavení** > **ceny**.
4. Klikněte na tlačítko **povolit** na dolní část stránky o upgrade vašeho účtu na **základní** vrstvy.

### <a name="cmdlet-not-recognized"></a>Scénář: Rutiny nebyl rozpoznán při provádění sady runbook

#### <a name="issue"></a>Problém

Úlohu runbook selže s chybou:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobena, když modul prostředí PowerShell nelze najít rutina, kterou používáte ve vašem runbooku. To může být způsobeno modul, který obsahuje rutinu chybí z účtu, dojde ke konfliktu názvů s název runbooku, nebo rutinu také existuje v jiném modulu a automatizace nelze přeložit název.

#### <a name="resolution"></a>Řešení

Problém opravte některý z následujících řešení:  

* Zkontrolujte, zda jste správně zadali název rutiny.  
* Zajistěte, aby rutiny v účtu Automation existuje a že nedošlo ke konfliktům. Pokud chcete ověřit, zda je přítomen rutinu, otevřete sadu runbook v režimu úprav a hledání rutiny, které chcete najít do knihovny nebo spustit `Get-Command <CommandName>`. Po ověření které rutiny jsou k dispozici k účtu, a že neexistují žádné název je v konfliktu s jinými rutinami nebo sady runbook, přidat na plátno a ujistěte se, že používáte platný parametr nastavit v sadě runbook.  
* Pokud máte ke konfliktu názvů, a rutina je k dispozici ve dvou různých modulů, můžete to vyřešit pomocí plně kvalifikovaný název rutiny. Například můžete použít **ModuleName\CmdletName**.  
* Pokud jsou prováděny runbook na místě v skupinu hybridních pracovních procesů, ujistěte se, že rutinu modulu nebo je nainstalován na počítači, který je hostitelem hybridní pracovní proces.

### <a name="evicted-from-checkpoint"></a>Scénář: Dlouho spuštěná sada runbook konzistentně selže s výjimkou: "Úloha nemůže dále běžet protože opakovaně bylo vyloučeno ze stejné kontrolní bod"

#### <a name="issue"></a>Problém

Toto chování je záměrné kvůli sledování "Úloha dostatečný podíl" procesů v rámci Azure Automation, která automaticky pozastaví runbook, pokud se provede déle než 3 hodiny. Chybovou zprávu vrácenou však neposkytuje "jaké další" možnosti.

#### <a name="cause"></a>Příčina

Sady runbook můžete pozastavit několik důvodů. Pozastaví dojít většinou z důvodu chyb. Například nezachycenou výjimku v runbooku, selhání sítě nebo havárie na Runbook Worker spuštění sady runbook, všechny příčina sady runbook pozastavena a začít od svého posledního kontrolního bodu při obnovení.

#### <a name="resolution"></a>Řešení

Zdokumentovaných řešení k tomuto problému vyhnout, je použití kontrolních bodů v pracovním postupu. Další informace najdete na [pracovních postupů PowerShell Learning](../automation-powershell-workflow.md#checkpoints). Podrobnější vysvětlení "Úloha dostatečný podíl" a kontrolního bodu najdete v tomto článku blog [pomocí kontrolních bodů v sadách Runbook](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Běžné chyby při importu modulů

### <a name="module-fails-to-import"></a>Scénář: Chyby při importu modulu nebo rutin nelze provést po importu

#### <a name="issue"></a>Problém

Modul se import nezdaří nebo importuje úspěšně, ale žádné rutiny se extrahují.

#### <a name="cause"></a>Příčina

Některé běžné příčiny, které modul nemusí úspěšně importovat do Azure Automation jsou:

* Struktura neodpovídá strukturu, která Automation potřebuje, aby byla v.
* Modul je závislá na jiný modul, která nebyla zavedena ke svému účtu Automation.
* Modul chybí jeho závislosti ve složce.
* `New-AzureRmAutomationModule` Rutina se používá k nahrání modul a nedali úložiště úplnou cestu nebo nebyly načteny modulu pomocí adresy URL veřejně přístupná.

#### <a name="resolution"></a>Řešení

Problém opravte některý z následujících řešení:

* Ujistěte se, že modul dodržuje následující formát: ModuleName.Zip **->** ModuleName nebo číslo verze **->** (ModuleName.psm1, ModuleName.psd1)
* Otevřete soubor .psd1 a zjistěte, zda modul všechny závislosti. Pokud ano, nahrajte do účtu Automation tyto moduly.
* Ujistěte se, že jsou všechny odkazované knihoven DLL umístěny ve složce modulu.

## <a name="next-steps"></a>Další postup

Pokud váš problém nenalezli nebo nemůžete vyřešit problém, navštíví některý z následujících kanály pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.