---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: b099f5ff7e43f2deeb3b8c41adcb802cd431a65a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635999"
---
Aktualizace agenta Azure File Sync pravidelně přidávat nové funkce a řešit problémy. Doporučujeme, abyste že konfiguraci získat aktualizace pro agenta Azure File Sync, jako jsou k dispozici služby Microsoft Update.

#### <a name="major-vs-minor-agent-versions"></a>Hlavní vs. verze menší agentů
* Verze agentů hlavní často obsahují nové funkce a mají rostoucí počet jako první součást čísla verze. Příklad: *2.\*.\**
* Verze menší agentů se také označují jako "opravy" a vydávají častěji než hlavní verze. Často obsahují opravy chyb a vylepšení menší, ale žádné nové funkce. Příklad:  *\*.3.\**

#### <a name="upgrade-paths"></a>Možnosti upgradu
Existují čtyři schválení a testovat způsobů, jak nainstalovat aktualizace agenta Azure File Sync. 
1. **(Upřednostňováno) Konfigurace služby Microsoft Update automaticky stáhla a nainstalovala aktualizace agenta.**  
    Vždy doporučujeme všechny aktualizace Azure File Sync, ujistěte se, že máte přístup k nejnovější opravy agenta serveru. Microsoft Update je tento proces bezproblémový, automaticky stažením a instalací aktualizace za vás.
2. **Použití AfsUpdater.exe ke stažení a instalaci aktualizací agenta.**  
    AfsUpdater.exe se nachází v instalační adresář agenta. Poklikejte na spustitelný soubor ke stažení a instalaci aktualizací agenta. 
3. **Oprava existujícího agenta Azure File Sync s použitím souboru opravy Microsoft Update nebo spustitelný soubor .msp. Nejnovější balíček aktualizace Azure File Sync si můžete stáhnout z [katalogu služby Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Spuštění spustitelného souboru .msp upgraduje instalaci sady Azure File Sync s stejnou metodu používá automaticky pomocí Microsoft Update v předchozí způsob upgradu. Použití služby Microsoft Update opravy provede upgrade místní instalaci Azure File Sync.
4. **Stáhněte si nejnovější instalační program agenta Azure File Sync z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Stažení instalačního programu je balíček Microsoft Installer nebo spustitelného souboru .msi.**  
    Pokud chcete upgradovat existující instalaci agenta Azure File Sync, odinstalujte starší verzi a potom nainstalujte nejnovější verzi z stažený instalační program. Registrace serveru, skupiny synchronizace a případná další nastavení spravuje Instalační služby Azure File Sync.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Zaručuje správy životního cyklu a změnit agenta
Azure File Sync je Cloudová služba, která umožňuje průběžně zavedení nových funkcí a funkce. To znamená, že konkrétní verzi agenta Azure File Sync může podporovat jenom po omezenou dobu. Pro usnadnění nasazení, mají následující pravidla vám zajistit dostatek času a oznámení tak, aby vyhovovaly agenta aktualizace a upgrady v procesu správy změn:

- Hlavní agenta verze nejsou podporovány pro alespoň šest měsíců od data původní vydaná verze.
- Garantujeme, že dojde k překrytí mezi podpory verzí agentů hlavní alespoň tři měsíce. 
- Upozornění budou vydány pro registrované servery pomocí agenta vypršela platnost brzy době přestanou alespoň tři měsíce před vypršením platnosti. Můžete zkontrolovat, zda registrovaný server používá starší verzi agenta části registrované servery služby synchronizace úložiště.
- Životnost verze menší agenta je vázán na odpovídající hlavní verze. Například při vydání verze agenta 3.0, agent verze 2. \* se všechny nastaví vypršení platnosti společně.

> [!Note]
> Instalace verze agenta pomocí upozornění na vypršení platnosti zobrazila upozornění ale úspěšné. Pokus o instalaci nebo se připojit s vypršenou platností agenta verzí není podporována a budou blokovány.
