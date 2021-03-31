---
title: Řešení potíží s aplikacemi funkcí Pythonu v Azure Functions
description: Naučte se řešit potíže s funkcemi Pythonu.
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 9b9f5d389eda5d74e7e78cfcfa9a46fba7276cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87846033"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Řešení chyb Pythonu ve službě Azure Functions

Následuje seznam průvodců odstraňováním potíží pro běžné problémy ve funkcích Pythonu:

* [ModuleNotFoundError a chyba při importu](#troubleshoot-modulenotfounderror)
* [Nejde importovat ' cygrpc '.](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>Řešení potíží s ModuleNotFoundError

Tato část vám pomůže vyřešit chyby související s modulem ve vaší aplikaci funkcí Pythonu. K těmto chybám obvykle dojde v následující Azure Functions chybové zprávě:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

K tomuto problému dochází, když se aplikace funkce Pythonu nepovede načíst modul Pythonu. Hlavní příčinou této chyby je jeden z následujících problémů:

- [Balíček se nenašel.](#the-package-cant-be-found)
- [Balíček se nevyřešil řádným kolečkem pro Linux](#the-package-isnt-resolved-with-proper-linux-wheel)
- [Balíček je nekompatibilní s verzí překladače Pythonu.](#the-package-is-incompatible-with-the-python-interpreter-version)
- [Balíček je v konfliktu s jinými balíčky.](#the-package-conflicts-with-other-packages)
- [Balíček podporuje jenom platformy Windows nebo macOS.](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>Zobrazit soubory projektu

Chcete-li zjistit skutečnou příčinu problému, je třeba získat soubory projektů Pythonu, které jsou spuštěny ve vaší aplikaci Function App. Pokud nemáte soubory projektu v místním počítači, můžete je získat jedním z následujících způsobů:

- Pokud má aplikace Function App `WEBSITE_RUN_FROM_PACKAGE` nastavení aplikace a její hodnota je adresa URL, Stáhněte si soubor tak, že zkopírujete a vložíte adresu URL do prohlížeče.
- Pokud má aplikace Function App `WEBSITE_RUN_FROM_PACKAGE` a je nastavená na `1` , přejděte na `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` adresu a Stáhněte si soubor z poslední `href` adresy URL.
- Pokud aplikace Function App nemá výše zmíněné nastavení aplikace, přejděte na `https://<app-name>.scm.azurewebsites.net/api/settings` adresu a vyhledejte ji v části `SCM_RUN_FROM_PACKAGE` . Stáhněte si soubor tak, že zkopírujete a vložíte adresu URL do prohlížeče.
- Pokud žádná z těchto možností není pro vás vhodná, přejděte k `https://<app-name>.scm.azurewebsites.net/DebugConsole` obsahu v části a odhalte ho `/home/site/wwwroot` .

Zbytek tohoto článku vám pomůže vyřešit možné příčiny této chyby, když zkontrolujete obsah aplikace Function App, zjistíte původní příčinu a vyřešíte konkrétní problém.

### <a name="diagnose-modulenotfounderror"></a>Diagnostika ModuleNotFoundError

Tato část podrobně popisuje možné hlavní příčiny chyb souvisejících s modulem. Až zjistíte, která je pravděpodobná příčinou hlavní příčiny, můžete přejít na související zmírnění.

#### <a name="the-package-cant-be-found"></a>Balíček se nenašel.

Přejděte na `.python_packages/lib/python3.6/site-packages/<package-name>` nebo `.python_packages/lib/site-packages/<package-name>` . Pokud cesta k souboru neexistuje, je nejspíš příčinou hlavní příčiny.

Tento problém může způsobovat použití jiných nebo zastaralých nástrojů během nasazení.

Přečtěte si téma [Povolení vzdáleného sestavení](#enable-remote-build) nebo [sestavení nativních závislostí](#build-native-dependencies) pro zmírnění rizik.

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>Balíček se nevyřešil řádným kolečkem pro Linux

Přejít na `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` nebo `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Pomocí oblíbeného textového editoru otevřete soubor **kolečka** a podívejte se na **značku:** oddíl. Pokud hodnota značky neobsahuje **Linux**, může to být problém.

Funkce Pythonu běží jenom na Linux v Azure: Functions runtime v2. x běží na Debian Stretch a v3. x runtime na Debian Buster. Očekává se, že artefakt bude obsahovat správné binární soubory systému Linux. Použití `--build local` příznaku u základních nástrojů, jiných výrobců nebo zastaralých nástrojů může způsobit použití starších binárních souborů.

Přečtěte si téma [Povolení vzdáleného sestavení](#enable-remote-build) nebo [sestavení nativních závislostí](#build-native-dependencies) pro zmírnění rizik.

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>Balíček je nekompatibilní s verzí překladače Pythonu.

Přejít na `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` nebo `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Pomocí textového editoru otevřete soubor METADAT a podívejte se do části **třídění:** oddíl. Pokud část neobsahuje `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` , nebo `Python :: 3.8` , znamená to, že je verze balíčku buď příliš stará, nebo je pravděpodobně balíček již mimo údržbu.

Verzi Pythonu aplikace Function App můžete ověřit z [Azure Portal](https://portal.azure.com). Přejděte do aplikace Function App, zvolte **Průzkumník prostředků** a vyberte **Přejít**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Otevření Průzkumník prostředků pro aplikaci Function App na portálu":::

Po načtení Průzkumníka vyhledejte **LinuxFxVersion**, ve kterém se zobrazí verze Pythonu.

Přečtěte si téma [aktualizace balíčku na nejnovější verzi](#update-your-package-to-the-latest-version) nebo [nahrazení balíčku stejnými](#replace-the-package-with-equivalents) verzemi pro zmírnění rizik.

#### <a name="the-package-conflicts-with-other-packages"></a>Balíček je v konfliktu s jinými balíčky.

Pokud jste ověřili, že se balíček správně vyřeší se správnými koly Linux, může dojít ke konfliktu s jinými balíčky. V některých balíčcích můžou PyPi dokumenty objasnit nekompatibilní moduly. Například v nástroji je [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) k dispozici příkaz následujícím způsobem:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Dokumentaci k verzi balíčku najdete v části `https://pypi.org/project/<package-name>/<package-version>` .

Přečtěte si téma [aktualizace balíčku na nejnovější verzi](#update-your-package-to-the-latest-version) nebo [nahrazení balíčku stejnými](#replace-the-package-with-equivalents) verzemi pro zmírnění rizik.

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>Balíček podporuje jenom platformy Windows nebo macOS.

Otevřete `requirements.txt` s textovým editorem a podívejte se na balíček v `https://pypi.org/project/<package-name>` . Některé balíčky se spouští jenom na platformách Windows nebo macOS. Například pywin32 běží pouze v systému Windows.

K této `Module Not Found` chybě může dojít při použití systému Windows nebo MacOS pro místní vývoj. Balíček se ale nepovede importovat na Azure Functions, který používá Linux za běhu. To je pravděpodobně způsobeno použitím `pip freeze` exportu virtuálního prostředí do requirements.txt z počítače s Windows nebo MacOS během inicializace projektu.

Přečtěte si téma [nahrazení balíčku ekvivalenty](#replace-the-package-with-equivalents) nebo [zpracovávají requirements.txt](#handcraft-requirementstxt) pro zmírnění rizik.

### <a name="mitigate-modulenotfounderror"></a>Zmírnit ModuleNotFoundError

Níže jsou možné hrozby pro problémy související s modulem. Pomocí [výše uvedených diagnostiky](#diagnose-modulenotfounderror) určete, která z těchto rizik se má vyzkoušet.

#### <a name="enable-remote-build"></a>Povolit vzdálené sestavení

Ujistěte se, že je povoleno vzdálené sestavení. Způsob, jakým to uděláte, závisí na metodě nasazení.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Ujistěte se, že je nainstalovaná nejnovější verze [rozšíření Azure Functions pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . Ověřte, zda `.vscode/settings.json` existuje a obsahuje nastavení `"azureFunctions.scmDoBuildDuringDeployment": true` . V takovém případě prosím vytvořte tento soubor s `azureFunctions.scmDoBuildDuringDeployment` povoleným nastavením a znovu nasaďte projekt.

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Ujistěte se, že je nainstalovaná nejnovější verze [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) . Přejít do složky projektu místní funkce a použít `func azure functionapp publish <app-name>` pro nasazení.

## <a name="manual-publishing"></a>[Ruční publikování](#tab/manual)

Pokud balíček ručně publikujete do `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` koncového bodu, ujistěte se, že **SCM_DO_BUILD_DURING_DEPLOYMENT** i **ENABLE_ORYX_BUILD** jsou nastavené na **hodnotu true**. Další informace najdete v tématu [jak pracovat s nastavením aplikace](functions-how-to-use-azure-function-app-settings.md#settings).

---

#### <a name="build-native-dependencies"></a>Sestavit nativní závislosti

Ujistěte se, že je nainstalovaná nejnovější verze **Docker** i [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) . Přejít do složky projektu místní funkce a použít `func azure functionapp publish <app-name> --build-native-deps` pro nasazení.

#### <a name="update-your-package-to-the-latest-version"></a>Aktualizace balíčku na nejnovější verzi

Projděte si nejnovější verzi balíčku v `https://pypi.org/project/<package-name>` tématu a podívejte se do části **třídění:** oddíl. Balíček by měl být `OS Independent` nebo kompatibilní s `POSIX` `POSIX :: Linux` **operačním systémem** nebo. Programovací jazyk by měl také obsahovat `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` , nebo `Python :: 3.8` .

Pokud jsou tyto správné, můžete balíček aktualizovat na nejnovější verzi tak, že změníte řádek `<package-name>~=<latest-version>` v requirements.txt.

#### <a name="handcraft-requirementstxt"></a>Zpracovávají requirements.txt

Někteří vývojáři používají `pip freeze > requirements.txt` k vygenerování seznamu balíčků Pythonu pro vývojová prostředí. I když by tato pohodlí měla ve většině případů fungovat, můžou nastat problémy při nasazení různých platforem, jako je například vývoj funkcí místně ve Windows nebo macOS, ale publikování do aplikace Function App, která běží na Linux. V tomto scénáři `pip freeze` může aplikace zavádět neočekávané závislosti nebo závislosti specifické pro operační systém pro vaše místní vývojové prostředí. Tyto závislosti můžou narušit aplikaci funkcí Pythonu při spuštění v systému Linux.

Osvědčeným postupem je ověřit příkaz Import z každého souboru. py ve zdrojovém kódu projektu a pouze vrátit se změnami tyto moduly v souboru requirements.txt. To zaručuje, že řešení balíčků lze v různých operačních systémech správně zpracovat.

#### <a name="replace-the-package-with-equivalents"></a>Nahradit balíček ekvivalenty

Nejdřív byste se měli podívat na nejnovější verzi balíčku v `https://pypi.org/project/<package-name>` . Tento balíček má obvykle svou vlastní stránku GitHubu, přejít na oddíl **problémy** na GitHubu a vyhledat, zda byl problém vyřešen. Pokud ano, aktualizujte balíček na nejnovější verzi.

V některých případech je možné, že je balíček integrovaný do [standardní knihovny Pythonu](https://docs.python.org/3/library/) (například pathlib). Pokud ano, protože poskytujeme určitou distribuci Pythonu v Azure Functions (Python 3,6, Python 3,7 a Python 3,8), balíček v requirements.txt by měl být odebraný.

Pokud máte ale k problému nějaký problém, který se nevyřešil, a jste na termínu. Doporučujeme udělat si nějakého výzkumu a najít podobný balíček pro váš projekt. Komunita Pythonu vám obvykle poskytne širokou škálu podobných knihoven, které můžete použít.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>Řešení potíží nemůže importovat ' cygrpc '

Tato část vám pomůže odstranit chyby související s cygrpc ve vaší aplikaci funkcí Pythonu. K těmto chybám obvykle dojde v následující Azure Functions chybové zprávě:

> `Cannot import name 'cygrpc' from 'grpc._cython'`

K tomuto problému dochází, když se aplikace funkce Pythonu nespustí se správným překladačem Pythonu. Hlavní příčinou této chyby je jeden z následujících problémů:

- [Interpret jazyka Python neodpovídá architektuře operačního systému](#the-python-interpreter-mismatches-os-architecture)
- [Interpret Pythonu není podporován Azure Functions pracovníkem Pythonu](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>Diagnostikovat chybu Reference "cygrpc"

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Interpret jazyka Python neodpovídá architektuře operačního systému

Nejpravděpodobnější příčinou je, že 32 interpret Pythonu je nainstalován v 64 operačním systému.

Pokud používáte operační systém x64, zajistěte, aby byl překladač Python 3,6, 3,7 nebo 3,8 také 64 v 16bitovém verzi.

Překladač Pythonu bitová verze můžete kontrolovat pomocí následujících příkazů:

Ve Windows v PowerShellu: `py -c 'import platform; print(platform.architecture()[0])'`

V prostředí podobném systému UNIX: `python3 -c 'import platform; print(platform.architecture()[0])'`

Pokud se nejedná o neshodu mezi překladačem Pythonu bitová verze a architekturou operačního systému, Stáhněte si prosím správný překladač Pythonu od [Python Software Foundation](https://python.org/downloads/release).

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Interpret Pythonu není podporován Azure Functions pracovníkem Pythonu

Pracovník Pythonu Azure Functions podporuje jenom Python 3,6, 3,7 a 3,8.
Zkontrolujte prosím, jestli váš interpret Pythonu odpovídá očekávané verzi `py --version` ve Windows nebo `python3 --version` v systémech podobných platformě UNIX. Zajistěte, aby byl vrácen výsledek Python 3.6. x, Python 3.7. x nebo Python 3.8. x.

Pokud vaše verze překladače Pythonu nesplňuje naše očekávání, Stáhněte si prosím překladač Python 3,6, 3,7 nebo 3,8 z [Python Software Foundation](https://python.org/downloads/release).

## <a name="next-steps"></a>Další kroky

Pokud tento problém nemůžete vyřešit, ohlaste ho prosím týmu Functions:

> [!div class="nextstepaction"]
> [Ohlásit nevyřešený problém](https://github.com/Azure/azure-functions-python-worker/issues)
