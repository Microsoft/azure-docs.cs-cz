---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77123219"
---
Agent Synchronizace souborů Azure se pravidelně aktualizuje, aby bylo možné přidat nové funkce a vyřešit problémy. Doporučujeme, abyste nakonfigurovali Microsoft Update, abyste získali aktualizace pro agenta Synchronizace souborů Azure, jak jsou k dispozici.

#### <a name="major-vs-minor-agent-versions"></a>Hlavní verze agentů vs.
* Hlavní verze agenta často obsahují nové funkce a v první části čísla verze se zvyšuje číslo. Příklad: \* 2. \* .\*\*
* Dílčí verze agenta se také nazývají "opravy" a jsou vydávány častěji než hlavní verze. Často obsahují opravy chyb a menší vylepšení, ale žádné nové funkce. Příklad: \* \* . 3.\*\*

#### <a name="upgrade-paths"></a>Cesty upgradu
Existují čtyři schválené a testované způsoby, jak nainstalovat aktualizace agenta Synchronizace souborů Azure. 
1. **Doporučeno Nakonfigurujte Microsoft Update pro automatické stažení a instalaci aktualizací agenta.**  
    Vždycky doporučujeme, abyste provedli každou aktualizaci Synchronizace souborů Azure, abyste měli jistotu, že máte přístup k nejnovějším opravám pro agenta serveru. Microsoft Update tento proces plynule provede automatickým stažením a instalací aktualizací.
2. **Pomocí AfsUpdater.exe stáhnout a nainstalovat aktualizace agenta.**  
    AfsUpdater.exe se nachází v instalačním adresáři agenta. Dvakrát klikněte na spustitelný soubor ke stažení a instalaci aktualizací agenta. 
3. **Opravte stávajícího agenta Synchronizace souborů Azure pomocí souboru Microsoft Update opravy nebo spustitelného souboru. msp. Nejnovější balíček aktualizace Synchronizace souborů Azure lze stáhnout z [katalogu Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Spuštění souboru. msp provede upgrade instalace Synchronizace souborů Azure stejným způsobem použitým automaticky pomocí Microsoft Update v předchozí cestě upgradu. Použití opravy Microsoft Update provede místní upgrade instalace Synchronizace souborů Azure.
4. **Stáhněte si nejnovější Synchronizace souborů Azure instalátor agenta z webu [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Pokud chcete upgradovat existující instalaci agenta Synchronizace souborů Azure, odinstalujte starší verzi a pak nainstalujte nejnovější verzi ze staženého instalačního programu. Služba Synchronizace souborů Azure Installer udržuje registraci serveru, skupiny synchronizace a všechna ostatní nastavení.

#### <a name="automatic-agent-lifecycle-management"></a>Automatické řízení životního cyklu agentů
S agentem verze 6 zavedl tým synchronizace souborů funkci automatického upgradu agenta. Můžete vybrat kterýkoli ze dvou režimů a určit časové období údržby, ve kterém se upgrade na serveru bude pokoušet. Tato funkce je navržená tak, aby vám pomohla se správou životního cyklu agentů tím, že poskytuje guardrail, který brání vašemu vypršení platnosti vašeho agenta, nebo umožňuje neaktuální nastavení.
1. **Výchozí nastavení** se pokusí zabránit agentovi v vypršení platnosti. Během 21 dní od data vypršení platnosti agenta se agent pokusí provést upgrade sami. Spustí se pokus o upgrade jednou týdně během 21 dnů před vypršením platnosti a ve vybraném časovém období údržby. **Tato možnost neeliminuje nutnost provádět běžné Microsoft Update opravy.**
1. Volitelně můžete vybrat, že se bude agent automaticky upgradovat, jakmile bude k dispozici nová verze agenta (aktuálně neplatí pro clusterované servery). Tato aktualizace proběhne během vybraného časového období údržby a umožní vašemu serveru využívat nové funkce a vylepšení, jakmile budou všeobecně dostupné. Toto je doporučené nastavení bez obav, které bude poskytovat hlavní verze agentů, stejně jako běžné aktualizace na váš server. Každý vydaný agent má kvalitu GA. Pokud vyberete tuto možnost, Microsoft zabere nejnovější verzi agenta. Servery v clusteru jsou vyloučené. Po dokončení letu bude agent také k dispozici na [webu Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.MS/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Změna nastavení automatického upgradu

Následující pokyny popisují, jak změnit nastavení po dokončení instalačního programu, pokud potřebujete provést změny.

Otevřete konzolu PowerShellu a přejděte do adresáře, kam jste nainstalovali agenta synchronizace, a pak importujte rutiny serveru. Ve výchozím nastavení by to vypadalo přibližně takto:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Můžete spustit, `Get-StorageSyncAgentAutoUpdatePolicy` Pokud chcete kontrolovat aktuální nastavení zásad a určit, jestli ho chcete změnit.

Chcete-li změnit aktuální nastavení zásad na záznam opožděné aktualizace, můžete použít:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Chcete-li změnit aktuální nastavení zásad na okamžitou stopu aktualizace, můžete použít:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Životní cyklus agenta a záruky správy změn
Synchronizace souborů Azure je cloudová služba, která průběžně přináší nové funkce a vylepšení. To znamená, že konkrétní verzi agenta Synchronizace souborů Azure lze podporovat pouze po dobu omezeného času. V zájmu usnadnění nasazení vám následující pravidla zaručují dostatek času a oznámení, aby bylo možné v procesu správy změn zajišťovat aktualizace a upgrady agenta:

- Hlavní verze agenta se podporují aspoň po dobu šesti měsíců od data prvotní verze.
- U podpory hlavních verzí agentů garantujeme, že se překrývá aspoň tři měsíce. 
- Upozornění se vydávají pro registrované servery s použitím již neplatného agenta, který uplyne nejméně tři měsíce před vypršením platnosti. Můžete zjistit, jestli registrovaný Server používá starší verzi agenta v části registrované servery služby synchronizace úložiště.
- Životnost dílčí verze agenta je svázána s přidruženou hlavní verzí. Například když se uvolní Agent verze 3,0, verze agenta 2. \* nastaví se tak, aby vyprší dohromady.

> [!Note]
> Instalace verze agenta s upozorněním na vypršení platnosti zobrazí upozornění, ale bude úspěšná. Pokus o instalaci nebo připojení pomocí verze agenta s vypršenou platností není podporován a bude zablokován.
