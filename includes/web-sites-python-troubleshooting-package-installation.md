Některé balíčky se při spuštění v Azure nemusí pomocí systému pip nainstalovat.  Může to být jednoduše proto, že nejsou dostupné v indexu balíčků Pythonu.  Důvodem může být také to, že je požadován kompilátor (kompilátor není dostupný v počítači, ve kterém běží webová aplikace ve službě Azure App Service).

V této části najdete popis způsobů řešení tohoto problému.

### <a name="request-wheels"></a>Vyžádání souborů wheel
Pokud instalace balíčku vyžaduje kompilátor, pokuste se kontaktovat vlastníka balíčku a požádejte ho o zpřístupnění souborů wheel balíčku.

Díky nedávno vydanému z [Microsoft Visual C++ Compiler for Python 2.7][Microsoft Visual C++ Compiler for Python 2.7], je teď snazší sestavovat balíčky, které mají nativní kód pro Python 2.7.

### <a name="build-wheels-requires-windows"></a>Sestavení souborů wheel (vyžaduje Windows)
Poznámka: Při použití této možnosti je nutné balíček zkompilovat pomocí prostředí Python, které odpovídá platformě, architektuře a verzi použité ve webové aplikaci ve službě Azure App Service (Windows/32-bit/2.7 nebo 3.4).

Pokud se balíček nenainstaluje, protože vyžaduje kompilátor, můžete kompilátor nainstalovat na místním počítači a sestavit pro balíček soubor wheel, který potom zahrnete do úložiště.

Uživatelé systémů Mac a Linux: Pokud nemáte přístup k počítači s Windows, přečtěte si téma [vytvořit virtuální počítač spuštěný Windows] [ Create a Virtual Machine Running Windows] pro vytvoření virtuálního počítače v Azure.  Můžete ho použít k vytvoření souborů wheel, přidat je do úložiště a virtuální počítač zahodit. 

V případě Pythonu 2.7 můžete nainstalovat [Microsoft Visual C++ Compiler for Python 2.7][Microsoft Visual C++ Compiler for Python 2.7].

V případě Pythonu 3.4 můžete nainstalovat [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express].

K sestavení souborů wheel budete potřebovat balíček wheel:

    env\scripts\pip install wheel

Závislost zkompilujete pomocí příkazu `pip wheel`:

    env\scripts\pip wheel azure==0.8.4

Vytvoříte tak soubor .whl ve složce \wheelhouse.  Složku \wheelhouse a soubory wheel přidejte do úložiště.

Upravte soubor requirements.txt a přidejte na jeho začátek možnost `--find-links`. Tento parametr řekne systému pip, aby před použitím indexu balíčků Pythonu nejprve vyhledal přesnou shodu v místní složce.

    --find-links wheelhouse
    azure==0.8.4

Pokud chcete zahrnout všechny svoje závislosti do složky \wheelhouse, a index balíčků Pythonu vůbec nechcete použít, můžete přidáním parametru `--no-index` na začátek souboru requirements.txt vynutit, aby systém pip index balíčků ignoroval.

    --no-index

### <a name="customize-installation"></a>Přizpůsobení instalace
Skript nasazení můžete přizpůsobit tak, aby nainstalovat balíček ve virtuálním prostředí pomocí alternativního instalačního programu, jako je například easy\_install.  Příklad tohoto postupu označený jako komentář najdete v souboru deploy.cmd.  Ujistěte se, že tyto balíčky nejsou uvedené v souboru requirements.txt, abyste systému pip zabránili v jejich instalaci.

Do skriptu nasazení přidejte následující kód:

    env\scripts\easy_install somepackage

V některých případech můžete k instalaci z instalačního souboru .exe použít také instalační program easy\_install (některé balíčky jsou kompatibilní s formátem .zip, takže je easy\_install podporuje).  Přidejte instalační program do úložiště a předáním cesty spustitelnému souboru spusťte easy\_install.

Do skriptu nasazení přidejte následující kód:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Zahrnutí virtuálního prostředí do úložiště (vyžaduje Windows)
Poznámka: Při použití této možnosti je nutné použít virtuální prostředí, které odpovídá platformě, architektuře a verzi použité ve webové aplikaci ve službě Azure App Service (Windows/32-bit/2.7 nebo 3.4).

Pokud do úložiště zahrnete virtuální prostředí, můžete skriptu nasazení zabránit ve správě virtuálního prostředí v Azure vytvořením prázdného souboru:

    .skipPythonDeployment

Existující virtuální prostředí v aplikaci se doporučuje odstranit, abyste zabránili zanechání nepotřebných souborů z období, kdy bylo virtuální prostředí spravováno automaticky.

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
