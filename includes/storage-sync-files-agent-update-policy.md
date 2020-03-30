---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123219"
---
Agent Azure File Sync se pravidelně aktualizuje, aby přidal nové funkce a řešil problémy. Doporučujeme nakonfigurovat službu Microsoft Update tak, aby aktualizace pro agenta Azure File Sync byly k dispozici.

#### <a name="major-vs-minor-agent-versions"></a>Hlavní verze vs. minor agent
* Verze hlavních agentů často obsahují nové funkce a mají rostoucí počet jako první část čísla verze. Například: \*2.\*.\*\*
* Dílčí agent verze se také nazývají "opravy" a jsou vydávány častěji než hlavní verze. Často obsahují opravy chyb a menší vylepšení, ale žádné nové funkce. Například: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Cesty upgradu
Existují čtyři schválené a testované způsoby instalace aktualizací agenta Azure File Sync. 
1. **(Preferováno) Nakonfigurujte službu Microsoft Update tak, aby automaticky stahovala a instalovala aktualizace agentů.**  
    Vždy doporučujeme, abyste měli přístup k nejnovějším opravám pro agenta serveru. Služba Microsoft Update umožňuje bezproblémový proces tím, že automaticky stahuje a instaluje aktualizace.
2. **Pomocí programu AfsUpdater.exe můžete stahovat a instalovat aktualizace agentů.**  
    Soubor AfsUpdater.exe je umístěn v instalačním adresáři agenta. Poklepáním na spustitelný soubor stáhnete a nainstalujete aktualizace agentů. 
3. **Opravte stávajícího agenta Azure File Sync pomocí souboru opravy Microsoft Update nebo spustitelného souboru MSP. Nejnovější balíček aktualizace Synchronizace souborů Azure lze stáhnout z [katalogu Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Spuštěním spustitelného souboru Msp inovujete instalaci synchronizace souborů Azure stejnou metodou, kterou automaticky používá služba Microsoft Update v předchozí cestě upgradu. Použití opravy Microsoft Update provede vlastní upgrade instalace Synchronizace souborů Azure.
4. **Stáhněte si nejnovější instalační program agenta Synchronizace souborů Azure ze [služby Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Chcete-li upgradovat existující instalaci agenta Synchronizace souborů Azure, odinstalujte starší verzi a nainstalujte nejnovější verzi ze staženého instalačního programu. Registrace serveru, skupiny synchronizace a všechna další nastavení jsou udržovány instalačním programem Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Automatická správa životního cyklu agenta
S agentem verze 6, tým pro synchronizaci souborů zavedl a agent a funkce automatického upgradu. Můžete vybrat jeden ze dvou režimů a určit okno údržby, ve kterém se má na serveru provést pokus o upgrade. Tato funkce je navržena tak, aby vám pomohla se správou životního cyklu agenta tím, že buď poskytne svodidla, které agentovi zabrání vypršení platnosti, nebo umožní bezproblémové nastavení aktuálního nastavení.
1. **Výchozí nastavení** se pokusí zabránit vypršení platnosti agenta. Do 21 dnů od zaúčtovaného data vypršení platnosti agenta se agent pokusí o vlastní upgrade. Začne se pokoušet o upgrade jednou týdně do 21 dnů před vypršením platnosti a ve vybraném okně údržby. **Tato možnost nevylučuje potřebu běžných oprav služby Microsoft Update.**
1. Volitelně můžete vybrat, že se agent automaticky provede upgradem, jakmile bude k dispozici nová verze agenta (aktuálně se nevztahuje na clusterované servery). Tato aktualizace bude probíhat během vybraného okna údržby a umožní serveru využívat nové funkce a vylepšení, jakmile budou obecně k dispozici. Toto je doporučené, bezstarostné nastavení, které bude poskytovat hlavní verze agenta, stejně jako pravidelné aktualizace oprav na serveru. Každý propuštěný agent je v kvalitě GA. Pokud vyberete tuto možnost, společnost Microsoft vám přenese nejnovější verzi agenta. Clusterované servery jsou vyloučeny. Po dokončení letu bude agent k dispozici také na webu [služby Stažení softwaru](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Změna nastavení automatického upgradu

Následující pokyny popisují, jak změnit nastavení po dokončení instalačního programu, pokud potřebujete provést změny.

Otevřete konzolu PowerShell a přejděte do adresáře, do kterého jste nainstalovali agenta synchronizace, a pak importujte serverové rutiny. Ve výchozím nastavení by to vypadalo nějak takto:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Můžete spustit `Get-StorageSyncAgentAutoUpdatePolicy` zkontrolovat aktuální nastavení zásad a určit, zda chcete změnit.

Chcete-li změnit aktuální nastavení zásad na stopu zpožděné aktualizace, můžete použít:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Chcete-li změnit aktuální nastavení zásad na okamžitou aktualizaci, můžete použít:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Záruky životního cyklu agenta a správy změn
Azure File Sync je cloudová služba, která neustále zavádí nové funkce a vylepšení. To znamená, že konkrétní verze agenta Synchronizace souborů Azure může být podporována jenom po omezenou dobu. Pro usnadnění nasazení následující pravidla zaručují, že máte dostatek času a oznámení pro přizpůsobení aktualizacím nebo upgradům agentů v procesu správy změn:

- Verze hlavních agentů jsou podporovány po dobu nejméně šesti měsíců od data počátečního vydání.
- Garantujeme, že mezi podporou hlavních verzí agentů dochází k překrývání nejméně tří měsíců. 
- Upozornění jsou vydávána pro registrované servery, které používají agenta, jehož platnost brzy vyprší, nejméně tři měsíce před vypršením platnosti. Můžete zkontrolovat, zda registrovaný server používá starší verzi agenta v části registrované servery služby Synchronizace úložiště.
- Životnost verze dílčího agenta je vázána na přidruženou hlavní verzi. Například při uvolnění agenta verze 3.0, agent verze 2. \* budou nastaveny tak, aby vyprší společně.

> [!Note]
> Instalace verze agenta s upozorněním vypršení platnosti zobrazí upozornění, ale úspěšné. Pokus o instalaci nebo připojení k agentní verzi, jejíž platnost vypršela, není podporován a bude blokován.
