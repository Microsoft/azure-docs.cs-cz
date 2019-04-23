---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 4f59f68c1598f737ea7cb3a0e8046fc0779ed9d3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60118485"
---
Aktualizace agenta Azure File Sync pravidelně přidávat nové funkce a řešit problémy. Doporučujeme, abyste že konfiguraci získat aktualizace pro agenta Azure File Sync, jako jsou k dispozici služby Microsoft Update.

#### <a name="major-vs-minor-agent-versions"></a>Hlavní vs. verze menší agentů
* Verze agentů hlavní často obsahují nové funkce a mají rostoucí počet jako první součást čísla verze. Příklad: \*2.\*.\*\*
* Verze menší agentů se také označují jako "opravy" a vydávají častěji než hlavní verze. Často obsahují opravy chyb a vylepšení menší, ale žádné nové funkce. Příklad: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Možnosti upgradu
Existují čtyři schválení a testovat způsobů, jak nainstalovat aktualizace agenta Azure File Sync. 
1. **(Upřednostňováno) Konfigurace služby Microsoft Update automaticky stáhla a nainstalovala aktualizace agenta.**  
    Vždy doporučujeme všechny aktualizace Azure File Sync, ujistěte se, že máte přístup k nejnovější opravy agenta serveru. Microsoft Update je tento proces bezproblémový, automaticky stažením a instalací aktualizace za vás.
2. **Použití AfsUpdater.exe ke stažení a instalaci aktualizací agenta.**  
    AfsUpdater.exe se nachází v instalační adresář agenta. Poklikejte na spustitelný soubor ke stažení a instalaci aktualizací agenta. 
3. **Oprava existujícího agenta Azure File Sync s použitím souboru opravy Microsoft Update nebo spustitelný soubor .msp. Nejnovější balíček aktualizace Azure File Sync si můžete stáhnout z [katalogu služby Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Spuštění spustitelného souboru .msp upgraduje instalaci sady Azure File Sync s stejnou metodu používá automaticky pomocí Microsoft Update v předchozí způsob upgradu. Použití služby Microsoft Update opravy provede upgrade místní instalaci Azure File Sync.
4. **Stáhněte si nejnovější instalační program agenta Azure File Sync z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Pokud chcete upgradovat existující instalaci agenta Azure File Sync, odinstalujte starší verzi a potom nainstalujte nejnovější verzi z stažený instalační program. Registrace serveru, skupiny synchronizace a případná další nastavení spravuje Instalační služby Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Správa životního cyklu agentů pro automatickou diagnostiku
Agent verze 6 přináší týmu synchronizace souboru funkci Automatický upgrade agenta. Můžete vybrat jednu ze dvou režimů používaných a určete časové období údržby, ve kterém se pokus o upgrade na serveru. Tato funkce slouží k pomáhají se správou životního cyklu agenta tím, že poskytuje lanovým brání agenta z vypršení platnosti nebo umožňující bezproblémové, zůstat aktuální nastavení.
1. **Výchozí nastavení** se pokusí o zabránit agenta z vypršení platnosti. V rámci odeslaných vypršení platnosti agenta na 21 dnů agent se pokusí místním upgradu. Pokus o upgrade jednou za týden do 21 dnů před vypršením platnosti a v okně vybrané údržby spustí. **Tato možnost není eliminuje nutnost provádění pravidelných opravy Microsoft Update.**
2. Volitelně můžete vybrat, že agenta bude automaticky sám sebe aktualizovat poté, co bude k dispozici nová verze agenta. Bude také dojít během časového období údržby vybrané a povolit vašeho serveru, abyste využili výhod nových funkcí a vylepšení, jakmile budou obecně dostupné. Toto je doporučené, bez obav nastavení, která bude poskytovat verze hlavní agentů, jakož i opravy pravidelné aktualizace na server.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Zaručuje správy životního cyklu a změnit agenta
Azure File Sync je Cloudová služba, která průběžně zavádí nové funkce a vylepšení. To znamená, že konkrétní verzi agenta Azure File Sync může podporovat jenom po omezenou dobu. Následující pravidla pro usnadnění nasazení, zajistit, že máte dostatek času a oznámení tak, aby vyhovovaly agenta aktualizace a upgrady v procesu správy změn:

- Hlavní agenta verze nejsou podporovány pro alespoň šest měsíců od data původní vydaná verze.
- Garantujeme, že dojde k překrytí mezi podpory verzí agentů hlavní alespoň tři měsíce. 
- Upozornění budou vydány pro registrované servery pomocí agenta vypršela platnost brzy době přestanou alespoň tři měsíce před vypršením platnosti. Můžete zkontrolovat, zda registrovaný server používá starší verzi agenta části registrované servery služby synchronizace úložiště.
- Životnost verze menší agenta je vázán na odpovídající hlavní verze. Například při vydání verze agenta 3.0, agent verze 2. \* se všechny nastaví vypršení platnosti společně.

> [!Note]
> Instalace verze agenta pomocí upozornění na vypršení platnosti zobrazila upozornění ale úspěšné. Pokus o instalaci nebo se připojit s vypršenou platností agenta verzí není podporována a budou blokovány.
