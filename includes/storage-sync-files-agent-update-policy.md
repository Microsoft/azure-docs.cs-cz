Agent synchronizace souboru Azure se aktualizuje pravidelně přidat další nové funkce a řešit problémy. Doporučujeme, aby že konfigurace webu Microsoft Update a získat aktualizace pro agenta synchronizace souboru Azure, jako jsou k dispozici.

#### <a name="major-vs-minor-agent-versions"></a>Hlavní oproti verze menší agentů
* Verze agentů hlavní často obsahují nové funkce a mít se zvyšující číslo jako první část číslo verze. Příklad: *2.\*.\**
* Méně závažné agenta verze se také označují jako "opravy" a vydávají častěji, než hlavní verze. Často obsahují oprav chyb a vylepšení menší, ale žádné nové funkce. Příklad:  *\*.3.\**

#### <a name="upgrade-paths"></a>Možnosti upgradu
Existují tři schválení a testovat způsoby, jak nainstalovat aktualizace agenta synchronizace souboru Azure. Tyto aktualizace práce cesty pro hlavní a vedlejší verze.
1. **(Doporučeno) Konfigurace webu Microsoft Update a automaticky stáhnout a nainstalovat aktualizace agenta.**  
    Vždy doporučujeme, přičemž každá synchronizace souborů Azure aktualizace zajistit, že máte přístup k nejnovější opravy pro agenta serveru. Microsoft Update i tento proces bezproblémové, automaticky stahuje a instaluje aktualizace za vás.
2. **Oprava existující agenta synchronizace souboru Azure pomocí opravný soubor Microsoft Update nebo .msp, která se spustitelný soubor. Nejnovější balíček aktualizace synchronizace souboru Azure si můžete stáhnout z [katalogu služby Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Spuštění spustitelného souboru .msp upgraduje instalaci synchronizace Azure souboru se stejnou metodu používá automaticky Microsoft Update v předchozí upgradu. Použití opravy Microsoft Update bude provést upgrade na místě instalace synchronizace souboru Azure.
3. **Stáhnout nejnovější instalační program agenta synchronizace souboru Azure z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Stažení instalačního programu je balíček Microsoft Installer nebo spustitelný soubor .msi.**  
    Upgrade existující instalace agenta Azure souboru Sync, odinstalovat starší verzi a poté nainstalujte nejnovější verzi z stažený instalační program. Registrace serveru skupiny pro synchronizaci a další nastavení se spravuje pomocí Azure Sync soubor Instalační služby.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Zaručuje, změn a životního cyklu správy agenta
Synchronizace služby Azure souboru je cloudové služby, který umožňuje nepřetržitě zavedení nových funkcí a funkce. To znamená, že konkrétní verze agenta synchronizace souboru Azure může podporovat pouze po omezenou dobu. Usnadňuje nasazení těchto pravidel pro zaručit, budete mít při dostatku času a oznámení, aby dokázala pojmout agenta aktualizace a upgrady v procesu správy změn:

- Verze agentů hlavní jsou podporovány pro nejméně šest měsíců od data původní verze.
- Nemůžeme zaručit, že je k překrytí alespoň tři měsíce mezi podpoře hlavní agenta verze. 
- Vydávání upozornění pro registrované servery pomocí agenta vypršela platnost brzy na se alespoň tři měsíce před vypršením platnosti. Můžete se podívat, pokud zaregistrovaný server používá starší verzi agenta části registrované servery služby synchronizace úložiště.
- Doba platnosti verze menší agenta je vázána přidružené hlavní verze. Například při uvolnění agenta verze 3.0, agent verze 2. \* budou všechny nastavit vyprší společně.

> [!Note]
> Instalace verze agenta s upozornění vypršení platnosti se zobrazí varování ale úspěšné. Probíhá pokus o nainstalování nebo připojení s vypršenou platností agenta verzí není podporována a se zablokuje.