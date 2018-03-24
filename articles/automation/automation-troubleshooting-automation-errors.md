---
title: Řešení běžných potíží Azure Automation | Microsoft Docs
description: Tento článek obsahuje informace o řešení potíží a opravte běžné chyby Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
tags: top-support-issue
keywords: Chyba automatizace, řešení potíží problém
ms.openlocfilehash: 9764068dd7a1a499c61695f39bff726a8ea3aac9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Odstraňování běžných problémů ve službě Azure Automation 
Tento článek obsahuje nápovědu k odstraňování běžných chyb, může docházet v Azure Automation a navrhne možná řešení jejich řešení potíží.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Chyby ověřování při práci se sadami runbook automatizace Azure.
### <a name="scenario-sign-in-to-azure-account-failed"></a>Scénář: Přihlaste se k účtu Azure se nezdařilo
**Chyba:** při práci s rutiny Add-AzureAccount nebo Login-AzureRmAccount se zobrazí chyba "Unknown_user_type: Neznámý typ uživatele".

**Důvod chyby:** k této chybě dojde, pokud název asset přihlašovacích údajů není platný nebo pokud uživatelské jméno a heslo, které jste použili k nastavení asset přihlašovacích údajů automatizace nejsou platné.

**Tipy pro odstraňování potíží:** Chcete-li určit příčinu chyby, proveďte následující kroky:  

1. Ujistěte se, že nemáte žádné speciální znaky, včetně **@** znak v názvu asset přihlašovacích údajů automatizace, kterou používáte pro připojení k Azure.  
2. Zkontrolujte, můžete použít uživatelské jméno a heslo, které jsou uložené v Azure Automation přihlašovací údaje ve svém místním prostředí PowerShell ISE editoru. Můžete provést spuštěním následující rutiny v integrovaném Skriptovacím prostředí PowerShell:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. Pokud ověření selže místně, znamená to, zda nebyly správně nastavená přihlašovacích údajů Azure Active Directory. Odkazovat na [ověřování na Azure pomocí Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) příspěvku na blogu získat správně nastaven účet služby Azure Active Directory.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scénář: Nelze najít předplatné Azure
**Chyba:** se zobrazí chyba "odběr s názvem ``<subscription name>`` nebyl nalezen" při práci s rutiny Select-AzureSubscription nebo Select-AzureRmSubscription.

**Důvod chyby:** k této chybě dojde, pokud není platný název odběru, nebo pokud uživatele Azure Active Directory, který se pokouší získat podrobnosti o předplatném není nakonfigurován jako správce předplatného.

**Tipy pro odstraňování potíží:** Chcete-li zjistit, zda jste správně ověřené Azure a mají přístup k předplatnému chcete vybrat, proveďte následující kroky:  

1. Ujistěte se, že spustíte **Add-AzureAccount** dřív, než spustíte **Select-AzureSubscription** rutiny.  
2. Pokud se pořád zobrazí tato chybová zpráva, upravit kód tak, že přidáte **Get-AzureSubscription** následující rutina **Add-AzureAccount** rutiny a potom spusťte kód. Nyní ověřte, zda výstup Get-AzureSubscription obsahuje podrobnosti o vašem předplatném.  

   * Pokud nevidíte všechny podrobnosti o předplatném ve výstupu, znamená to, že odběr není inicializován ještě.  
   * Pokud se zobrazí podrobnosti o předplatném ve výstupu, potvrďte, že používáte správné předplatné název nebo ID s **Select-AzureSubscription** rutiny.   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scénář: Ověřování na Azure se nezdařila, protože je povolené vícefaktorového ověřování
**Chyba:** se zobrazí chyba "Add-AzureAccount: AADSTS50079: registrace silného ověřování (výš) se vyžaduje" při ověřování do Azure s Azure uživatelské jméno a heslo.

**Důvod chyby:** Pokud máte služby Multi-Factor authentication na vašem účtu Azure, nemůžete použít uživatele služby Azure Active Directory k ověření do Azure. Místo toho musíte použít certifikát nebo objekt služby pro ověřování v Azure.

**Tipy pro odstraňování potíží:** s rutinami pro model nasazení Azure classic používat certifikát, najdete v tématu [vytváření a přidání certifikátu pro správu služby Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Hlavní název služby pomocí rutiny Azure Resource Manager, najdete v tématu [vytváření služby objektu zabezpečení pomocí portálu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md) a [ověřování hlavní název služby pomocí Azure Resource Manageru.](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Běžné chyby při práci se sadami runbook
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>Scénář: Spuštění úlohy sady runbook došlo k pokusu o třikrát, ale její spuštění pokaždé, když se nezdařilo
**Chyba:** runbook selže s chybou "" Úloha byla pokusů tři ale se nezdařilo."

**Důvod chyby:** tato chyba může být způsobeno z následujících důvodů:  

1. Limit paměti. Neexistují zdokumentovaných omezení na množství paměti přidělené izolovaném prostoru [omezení služby Automation](../azure-subscription-service-limits.md#automation-limits) , úloha může selhat ho pokud používá více než 400 MB paměti. 

2. Modul není kompatibilní. Tato situace může nastat, pokud modul závislosti nejsou správné, a pokud tomu tak není, vaše sada runbook obvykle vrátí "Příkaz nebyl nalezen" nebo "Nelze vytvořit vazbu parametru" zprávy. 

**Tipy pro odstraňování potíží:** některý z následujících řešení opravě problému:

* Navrhované metody pro práci v rámci limit paměti mají rozdělit zatížení mezi několika runbooky, není zpracovat tolik data v paměti, není k zápisu výstupu nepotřebné z vaší sady runbook nebo zvažte kolik kontrolní body zápisu do vašeho pracovního postupu Powershellu sady runbook.  

* Aktualizovat moduly Azure podle pokynů [aktualizaci modulů prostředí Azure PowerShell ve službě Azure Automation](automation-update-azure-modules.md).  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scénář: Runbook nezdaří z důvodu deserializovaný objekt
**Chyba:** runbook selže s chybou "nelze vytvořit vazbu parametru ``<ParameterName>``. Nelze převést ``<ParameterType>`` hodnotu typu Deserialized ``<ParameterType>`` na typ ``<ParameterType>``".

**Důvod chyby:** Pokud vaše sada runbook v pracovním postupu Powershellu, je uložený na komplexní objekty v deserializovaný formát s cílem zachovat vašemu stavu sady runbook, pokud je pozastavená pracovního postupu.

**Tipy pro odstraňování potíží:** některý z následujících tří řešení řešení tohoto problému:

1. Pokud jsou potrubí komplexní objekty z jedné rutiny do druhého, zalomení tyto rutiny v InlineScript.
2. Název nebo hodnotu, která je třeba předejte z komplexní objekt neprochází celý objekt.
3. Použijte Powershellový runbook místo runbook pracovního postupu Powershellu.

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scénář: Runbook úloha se nezdařila, protože překročil přidělenou kvótu
**Chyba:** úlohu runbook selže s chybou "se kvóty pro měsíční celkový čas běhu úloh bylo dosaženo pro toto předplatné".

**Důvod chyby:** k této chybě dojde, pokud provádění úlohy překročí kvótu volné 500 minutu pro váš účet. Tato kvóta se vztahuje na všechny typy spuštění úlohy například testování úlohu, spouštění úlohy z portálu, provádění úlohy pomocí webhooků a plánování úlohu provést pomocí portálu Azure nebo ve vašem datovém centru. Další informace o cenách pro automatizaci najdete v tématu [ceny automatizace](https://azure.microsoft.com/pricing/details/automation/).

**Tipy pro odstraňování potíží:** Pokud chcete použít více než 500 minut zpracování za měsíc, budete muset změnit své předplatné z volné úroveň na úroveň Basic. Můžete upgradovat na úroveň Basic provedením následujících kroků:  

1. Přihlaste se ke svému předplatnému Azure.  
2. Vyberte účet Automation, který chcete upgradovat.  
3. Klikněte na **nastavení** > **ceny**.
4. Klikněte na tlačítko **povolit** na dolní část stránky o upgrade vašeho účtu na **základní** vrstvy.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scénář: Rutiny nebyl rozpoznán při provádění sady runbook
**Chyba:** úlohu runbook selže s chybou "``<cmdlet name>``: termín ``<cmdlet name>`` nebyl rozpoznán jako název rutiny, funkce, soubor skriptu nebo spustitelného programu."

**Důvod chyby:** této chybě dojde, pokud modul prostředí PowerShell nebyl nalezen rutinu používáte ve vaší sadě runbook. To může být způsobeno modul, který obsahuje rutinu chybí z účtu, dojde ke konfliktu názvů s název runbooku, nebo rutinu také existuje v jiném modulu a automatizace nelze přeložit název.

**Tipy pro odstraňování potíží:** některý z následujících řešení opravě problému:  

* Zkontrolujte, zda jste správně zadali název rutiny.  
* Zajistěte, aby rutiny v účtu Automation existuje a že nedošlo ke konfliktům. Pokud chcete ověřit, zda je přítomen rutinu, otevřete sadu runbook v režimu úprav a hledání rutiny, které chcete najít do knihovny nebo spustit **Get-Command ``<CommandName>``** . Po ověření které rutiny jsou k dispozici k účtu, a že neexistují žádné název je v konfliktu s jinými rutinami nebo sady runbook, přidat na plátno a ujistěte se, že používáte platný parametr nastavit v sadě runbook.  
* Pokud máte ke konfliktu názvů, a rutina je k dispozici ve dvou různých modulů, můžete to vyřešit pomocí plně kvalifikovaný název rutiny. Například můžete použít **ModuleName\CmdletName**.  
* Pokud jsou prováděny runbook na místě v skupinu hybridních pracovních procesů, ujistěte se, že rutinu modulu nebo je nainstalován na počítači, který je hostitelem hybridní pracovní proces.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scénář: Dlouho spuštěná sada runbook konzistentně selže s výjimkou: "Úloha nemůže dále běžet protože opakovaně bylo vyloučeno ze stejné kontrolní bod".
**Důvod chyby:** toto chování je záměrné kvůli sledování "Úloha dostatečný podíl" procesů v rámci Azure Automation, která automaticky pozastaví runbook, pokud se provede déle než 3 hodiny. Chybovou zprávu vrácenou však neposkytuje "jaké další" možnosti. Sady runbook můžete pozastavit několik důvodů. Pozastaví dojít většinou z důvodu chyb. Například nezachycenou výjimku v runbooku, selhání sítě nebo havárie na Runbook Worker spuštění sady runbook, všechny příčina sady runbook pozastavena a začít od svého posledního kontrolního bodu při obnovení.

**Tipy pro odstraňování potíží:** zdokumentovaných řešení k tomuto problému vyhnout, je použít kontrolní body v pracovním postupu. Další informace najdete na [pracovních postupů PowerShell Learning](automation-powershell-workflow.md#checkpoints). Podrobnější vysvětlení "Úloha dostatečný podíl" a kontrolního bodu najdete v tomto článku blog [pomocí kontrolních bodů v sadách Runbook](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Běžné chyby při importu modulů
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scénář: Chyby při importu modulu nebo rutin nelze provést po importu
**Chyba:** modul se import nezdaří nebo importuje úspěšně, ale žádné rutiny se extrahují.

**Důvod chyby:** jsou některé běžné příčiny, které modul nemusí úspěšně importovat do Azure Automation:

* Struktura neodpovídá strukturu, která Automation potřebuje, aby byla v.
* Modul je závislá na jiný modul, která nebyla zavedena ke svému účtu Automation.
* Modul chybí jeho závislosti ve složce.
* **New-AzureRmAutomationModule** rutina se používá k nahrání modul a nedali úložiště úplnou cestu nebo nebyly načteny modulu pomocí adresy URL veřejně přístupná.

**Tipy pro odstraňování potíží:** některý z následujících řešení opravě problému:

* Ujistěte se, že modul dodržuje následující formát: ModuleName.Zip **->** ModuleName nebo číslo verze **->** (ModuleName.psm1, ModuleName.psd1)
* Otevřete soubor .psd1 a zjistěte, zda modul všechny závislosti. Pokud ano, nahrajte do účtu Automation tyto moduly.
* Ujistěte se, že jsou všechny odkazované knihoven DLL umístěny ve složce modulu.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Běžné chyby při práci s potřeby konfigurace stavu (DSC)
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scénář: Uzel je ve stavu selhání s chybou "Nebyl nalezen."
**Chyba:** uzlu obsahuje sestavu s **se nezdařilo** stavu a který obsahuje chybu "pokus o získání akce ze serveru https://``<url>``//accounts/ ``<account-id>`` /Nodes(AgentId=``<agent-id>``) / GetDscAction se nezdařila, protože platnou konfigurací ``<guid>`` nebyl nalezen. "

**Důvod chyby:** této chybě obvykle dochází při uzlu je přiřazen název konfigurace (například ABC) namísto název konfigurace uzlu (například ABC. Webový server).

**Tipy k řešení potíží:**

* Ujistěte se, že přiřazujete uzlu se "název konfigurace uzlu" a ne "konfigurace název".
* Konfigurace uzlu můžete přiřadit k uzlu, pomocí portálu Azure nebo pomocí rutiny prostředí PowerShell.

  * Aby bylo možné přiřadit konfigurace uzlu do uzlu pomocí portálu Azure, otevřete **uzly DSC** stránky, pak vyberte uzel a klikněte na **konfigurace uzlu přiřazení** tlačítko.  
  * Aby bylo možné přiřadit konfigurace uzlu do uzlu pomocí rutiny prostředí PowerShell, použijte **Set-AzureRmAutomationDscNode** rutiny

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scénář: Žádná konfigurace uzlu (soubory MOF) byly vytvořeny při kompilaci konfigurace
**Chyba:** pozastaví úlohu kompilace DSC s chybou: "kompilace byl úspěšně dokončen, ale byly vygenerovány žádné configuration.mofs uzel".

**Důvod chyby:** při následující výraz **uzlu** – klíčové slovo v konfigurace DSC se vyhodnocuje `$null`, pak vytváří se žádné konfigurace uzlů.

**Tipy pro odstraňování potíží:** některý z následujících řešení opravě problému:

* Ujistěte se, že výraz vedle **uzlu** – klíčové slovo v definici konfigurace není vyhodnocení na $null.
* Pokud předáváte ConfigurationData při kompilaci konfigurace, ujistěte se, že předáváte očekávaných hodnot, které konfigurace vyžaduje, aby [ConfigurationData](automation-dsc-compile.md#configurationdata).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scénář: Sestavy uzlu DSC stane zablokované stav "v průběhu"
**Chyba:** DSC agenta výstupy "Nalezena žádná instance s danými hodnotami vlastností."

**Důvod chyby:** upgradu vaší verzi WMF a mají poškozen rozhraní WMI.

**Tipy pro odstraňování potíží:** o vyřešení problému postupujte podle pokynů v [DSC známé problémy a omezení](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) článku.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a>Scénář: Nelze použít pověření v konfigurace DSC
**Chyba:** úlohu kompilace DSC byla pozastavena s chybou: "System.InvalidOperationException Chyba při zpracování vlastnost pověření typu ``<some resource name>``: převádění a uložení zašifrované heslo jako prostý text je povoleno pouze v případě PSDscAllowPlainTextPassword je nastaven na hodnotu true ".

**Důvod chyby:** jste použili pověření v konfiguraci, ale neposkytli správné **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každý uzel konfigurace.

**Tipy k řešení potíží:**

* Ujistěte se, zda jste předat správnou **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každý uzel, konfigurace uvedené v konfiguraci. Další informace najdete v tématu [prostředky v Azure Automation DSC](automation-dsc-compile.md#assets).

## <a name="common-errors-when-onboarding-solutions"></a>Běžné chyby při připojování řešení

Při řešení registrace se můžete setkat chyby. Následuje seznam běžných chyb, které se můžete setkat.

### <a name="computergroupqueryformaterror"></a>ComputerGroupQueryFormatError

**Důvod chyby:**

Tento kód chyby znamená, že dotaz skupiny uloženého hledání počítače používají k zaměření řešení nebyla správně naformátována. Můžete změnit dotaz, nebo může byla změněna systému.

**Tipy k řešení potíží:**

Dotaz pro toto řešení a reonboard řešení, které vytvoří dotaz, můžete odstranit. Dotaz naleznete v rámci pracovního prostoru, v části **uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**, a kategorie dotazu je název řešení přiřazený k tomuto dotazu. Pokud jsou povolené více řešení, **MicrosoftDefaultComputerGroup** zobrazuje vícekrát pod **uložená hledání**.

### <a name="policyviolation"></a>PolicyViolation

**Důvod chyby:**

Tento kód chyby znamená, že nasazení se nezdařilo z důvodu narušení jedné nebo více zásad.

**Tipy k řešení potíží:**

Chcete-li úspěšně nasadit řešení, je potřeba zvážit, změna zásad uvedené. Jsou mnoho různých typů zásad, které lze definovat určité změny požadované závisí na zásadu, která je došlo k porušení. Například pokud zásady definované ve skupinu prostředků, který má odepřena oprávnění pro změnu obsahu určitých typů prostředků v rámci dané skupiny prostředků, můžete to, například udělat některé z následujících:

*   Úplně odeberte zásady.
* Pokuste se připojit k jiné skupině prostředků.
* Zkontrolovat, jestli zásady, tím, například:
   * Znovu cílení zásad konkrétní prostředek (například za účelem určitého účtu Automation).
   * Úprava sady prostředků zásad byl konfigurován k odepření.

Zkontrolujte oznámení v pravém horním rohu portálu Azure nebo přejděte do skupiny prostředků, která obsahuje váš účet automation a vyberte **nasazení** pod **nastavení** zobrazíte neúspěšný nasazení. Další informace o návštěvě zásad Azure: [přehled zásad Azure](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="next-steps"></a>Další postup

Pokud jste postupovali podle předchozích kroků pro řešení potíží a nemůžete najít odpověď, můžete zkontrolovat další podporu následující možnosti:

* Získáte pomoc od odborníků na Azure. Odeslat váš problém [fórech MSDN Azure nebo Stack Overflow](https://azure.microsoft.com/support/forums/).
* Soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a klikněte na tlačítko **získat podporu** pod **a technické podpory fakturace**.
* Zveřejnit skriptů požadavek na [centra skriptů](https://azure.microsoft.com/documentation/scripts/) Pokud hledáte řešení runbooku s Azure Automation nebo modul integrace.
* Zveřejnit připomínkám nebo funkcím požadavky pro Azure Automation na [User Voice](https://feedback.azure.com/forums/34192--general-feedback).
