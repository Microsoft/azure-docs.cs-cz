---
title: Konfigurace aplikací pro Linux Python
description: Naučte se konfigurovat kontejner Pythonu, ve kterém jsou webové aplikace spuštěné, pomocí Azure Portal a Azure CLI.
ms.topic: quickstart
ms.date: 10/06/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: df4b94702d14278a3279c504f52f46b922859db8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822824"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux v Pythonu pro Azure App Service

Tento článek popisuje, jak [Azure App Service](overview.md) spouští aplikace v Pythonu a jak můžete v případě potřeby přizpůsobit chování App Service. Aplikace Python musí být nasazené se všemi požadovanými moduly [PIP](https://pypi.org/project/pip/) .

Modul pro nasazení App Service automaticky aktivuje virtuální prostředí a `pip install -r requirements.txt` při nasazení [úložiště Git](deploy-local-git.md)nebo [balíčku zip](deploy-zip.md)se spustí za vás.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v Pythonu, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste Azure App Service nikdy nepoužili, postupujte podle kurzu [rychlý Start k Pythonu](quickstart-python.md) a [Pythonu s PostgreSQL](tutorial-python-postgresql-app.md).

Pro konfiguraci můžete použít buď [Azure Portal](https://portal.azure.com) , nebo rozhraní příkazového řádku Azure:

- **Azure Portal**použijte **Settings**  >  **konfigurační** stránku nastavení aplikace, jak je popsáno v tématu [Konfigurace App Service aplikace v Azure Portal](configure-common.md).

- **Azure CLI**: máte dvě možnosti.

    - Spusťte příkazy na [Azure Cloud Shell](../cloud-shell/overview.md), které můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spusťte příkazy místně pomocí instalace nejnovější verze rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)a pak se přihlaste k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> Linux je aktuálně doporučenou možností pro spouštění aplikací v Pythonu v App Service. Informace o možnostech Windows najdete v tématu věnovaném jazyku [Python na App Service systému Windows](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Konfigurace verze Pythonu

- **Azure Portal**: na stránce **Konfigurace** použijte kartu **Obecné nastavení** , jak je popsáno v tématu [Konfigurace obecných nastavení](configure-common.md#configure-general-settings) pro kontejnery Linux.

- Rozhraní příkazového **řádku Azure**:

    -  Zobrazit aktuální verzi Pythonu pomocí [AZ WebApp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli-interactive
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Nahraďte `<resource-group-name>` a `<app-name>` názvem vhodným pro vaši webovou aplikaci.
    
    - Nastavení verze Pythonu pomocí [AZ WebApp config set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli-interactive
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Zobrazit všechny verze Pythonu, které jsou podporované v Azure App Service pomocí [AZ WebApp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes):
    
        ```azurecli-interactive
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Nepodporovanou verzi Pythonu můžete spustit místo toho vytvořením vlastní image kontejneru. Další informace najdete v tématu věnovaném [použití vlastní image Dockeru](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

App Service systém sestavení s názvem Oryx provede následující kroky při nasazení aplikace pomocí balíčků Git nebo zip:

1. Spusťte vlastní skript před sestavením, pokud je určen `PRE_BUILD_COMMAND` nastavením.
1. Spusťte příkaz `pip install -r requirements.txt`. Soubor *requirements.txt* musí být přítomen v kořenové složce projektu. V opačném případě sestaví proces sestavení zprávu Chyba: "nepovedlo se najít setup.py nebo requirements.txt; Není spuštěná instalace PIP.
1. Pokud se *Manage.py* najde v kořenovém adresáři úložiště (indikuje Django aplikaci), spusťte *Manage.py collectstatic*. Pokud `DISABLE_COLLECTSTATIC` je ale nastavení `true` , tento krok se přeskočí.
1. Spusťte vlastní skript po sestavení, pokud je určen `POST_BUILD_COMMAND` nastavením.

Ve výchozím nastavení `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` jsou nastavení, a `DISABLE_COLLECTSTATIC` prázdná. 

- Pokud chcete při sestavování aplikací pro Django zakázat spouštění collectstatic, nastavte `DISABLE_COLLECTSTATIC` nastavení na true.

- Chcete-li spustit příkazy před sestavením, nastavte `PRE_BUILD_COMMAND` nastavení tak, aby obsahovalo buď příkaz, například `echo Pre-build command` nebo cestu k souboru skriptu relativně k vašemu kořenovému adresáři projektu, například `scripts/prebuild.sh` . Všechny příkazy musí pro kořenovou složku projektu použít relativní cesty.

- Chcete-li spustit příkazy po sestavení, nastavte `POST_BUILD_COMMAND` nastavení tak, aby obsahovalo buď příkaz, například `echo Post-build command` nebo cestu k souboru skriptu relativně k vašemu kořenovému adresáři projektu, například `scripts/postbuild.sh` . Všechny příkazy musí pro kořenovou složku projektu použít relativní cesty.

Další nastavení, která přizpůsobují automatizaci sestavení, najdete v tématu [Konfigurace Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Další informace o tom, jak App Service spouští a vytváří aplikace v Pythonu v systému Linux, najdete v tématu [jak Oryx detekuje a](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)sestavuje aplikace v Pythonu.

> [!NOTE]
> `PRE_BUILD_SCRIPT_PATH`Nastavení a `POST_BUILD_SCRIPT_PATH` jsou shodná s `PRE_BUILD_COMMAND` a `POST_BUILD_COMMAND` a jsou podporovány pro starší účely.
> 
> Nastavení s názvem `SCM_DO_BUILD_DURING_DEPLOYMENT` , pokud obsahuje `true` nebo 1, aktivuje Oryx Build během nasazování. Nastavení platí při nasazení pomocí Gitu, příkazu Azure CLI `az webapp up` a Visual Studio Code.

> [!NOTE]
> Vždy používejte relativní cesty ve všech skriptech před a po sestavení, protože kontejner sestavení, ve kterém běží Oryx, se liší od běhového kontejneru, ve kterém je aplikace spuštěná. Nikdy nespoléhá na přesné umístění složky projektu aplikace v rámci kontejneru (například na to, že je umístěn pod položkou *site/wwwroot*).

## <a name="container-characteristics"></a>Vlastnosti kontejneru

Při nasazení do App Service se aplikace Python spouštějí v kontejneru Docker pro Linux, který je definovaný v [úložišti GitHub App Service Pythonu](https://github.com/Azure-App-Service/python). Konfigurace imagí najdete v adresářích, které jsou specifické pro danou verzi.

Tento kontejner má následující vlastnosti:

- Aplikace se spouštějí pomocí [Gunicorn WSGI HTTP Server](https://gunicorn.org/) za použití dalších argumentů `--bind=0.0.0.0 --timeout 600`.
    - Můžete zadat nastavení konfigurace pro Gunicorn prostřednictvím souboru *Gunicorn.conf.py* v kořenovém adresáři projektu, jak je popsáno v tématu [Přehled konfigurace Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.Gunicorn.org). Můžete také [Upravit spouštěcí příkaz](#customize-startup-command).

    - Za účelem ochrany vaší webové aplikace před náhodným nebo úmyslným DDOS útoky se Gunicorn spouští za Nginx reverzní proxy, jak je popsáno v tématu [Deploying Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.Gunicorn.org).

- Ve výchozím nastavení zahrnuje základní image kontejneru pouze webovou architekturu s baněk, ale kontejner podporuje jiné architektury, které jsou kompatibilní s rozhraním WSGI a kompatibilní s Python 3.6 +, jako je například Django.

- Chcete-li nainstalovat další balíčky, například Django, vytvořte soubor [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) v kořenovém adresáři projektu, který určuje přímé závislosti. App Service pak tyto závislosti nainstaluje automaticky při nasazení projektu.

    Aby bylo možné nainstalovat závislosti, *musí* být soubor *requirements.txt* v kořenu projektu. V opačném případě sestaví proces sestavení zprávu Chyba: "nepovedlo se najít setup.py nebo requirements.txt; Není spuštěná instalace PIP. Pokud k této chybě dojde, ověřte umístění souboru požadavků.

## <a name="container-startup-process"></a>Proces spuštění kontejneru

V průběhu spuštění služba App Service v kontejneru Linuxu spustí následující kroky:

1. Použijte [vlastní spouštěcí příkaz](#customize-startup-command), pokud je k dispozici.
2. Zkontroluje existenci [aplikace Django](#django-app)a po zjištění jejího spuštění spustí Gunicorn.
3. Kontrolovat existenci [aplikace v baňce](#flask-app)a při zjištění Gunicorn spustit.
4. Pokud se žádná další aplikace nenajde, následuje spuštění výchozí aplikace sestavené do kontejneru.

Podrobnosti ke každému bodu poskytnou následující oddíly.

### <a name="django-app"></a>Aplikace Django

Služba App Service hledá pro aplikace Django soubory se jménem `wsgi.py` v kódu aplikace. Pak spustí server Gunicorn s použitím následujícího příkazu:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Pokud chcete mít konkrétnější kontrolu nad spouštěcím příkazem, použijte [vlastní spouštěcí příkaz](#customize-startup-command), nahraďte `<module>` názvem složky, která obsahuje *WSGI.py*, a přidejte `--chdir` argument, pokud tento modul není v kořenovém adresáři projektu. Pokud se například vaše *WSGI.py* nachází v kořenovém adresáři projektu v *knboard/back/end/config* , použijte argumenty `--chdir knboard/backend config.wsgi` .

Chcete-li povolit protokolování výroby, `--access-logfile` přidejte `--error-logfile` parametry a, jak je znázorněno v příkladech pro [vlastní spouštěcí příkazy](#customize-startup-command).

### <a name="flask-app"></a>Aplikace Flask

V případě baňky App Service vyhledá soubor s názvem *Application.py* nebo *App.py* a spustí Gunicorn následujícím způsobem:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Pokud je hlavní modul aplikace v jiném souboru, použijte jiný název objektu aplikace. Když chcete zadat další argumenty serveru Gunicorn, použijte [vlastní spouštěcí příkaz](#customize-startup-command).

### <a name="default-behavior"></a>Výchozí chování

Pokud služba App Service nenajde vlastní příkaz, aplikaci Django nebo aplikaci Flask, spustí výchozí aplikaci jen pro čtení umístěnou ve složce _opt/defaultsite_. Výchozí aplikace se zobrazí takto:

![Výchozí služba App Service na Linuxové webové stránce](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>Přizpůsobení spouštěcího příkazu

Jak bylo uvedeno výše v tomto článku, můžete zadat konfigurační nastavení pro Gunicorn prostřednictvím souboru *Gunicorn.conf.py* v kořenovém adresáři projektu, jak je popsáno v tématu [Přehled konfigurace Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Pokud taková konfigurace není dostatečná, můžete řídit chování kontejneru tím, že v souboru spouštěcího příkazu zadáte buď vlastní spouštěcí příkaz, nebo několik příkazů. Soubor spouštěcích příkazů může používat libovolný název, který si zvolíte, například *Startup.sh*, *Startup. cmd*, *startup.txt*a tak dále.

Všechny příkazy musí pro kořenovou složku projektu použít relativní cesty.

Zadání spouštěcího příkazu nebo souboru příkazů:

- **Azure Portal**: vyberte stránku **Konfigurace** aplikace a pak vyberte **Obecné nastavení**. V poli **spouštěcí příkaz** umístěte buď celý text spouštěcího příkazu, nebo název souboru spouštěcího příkazu. Pak vyberte **Uložit** , aby se změny projevily. Viz [Konfigurace obecných nastavení](configure-common.md#configure-general-settings) pro kontejnery Linux.

- **Azure CLI**: k nastavení spouštěcího příkazu nebo souboru použijte příkaz [AZ WebApp config set](/cli/azure/webapp/config#az_webapp_config_set) s `--startup-file` parametrem:

    ```azurecli-interactive
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Nahraďte `<custom-command>` celým textem spouštěcího příkazu nebo názvem souboru spouštěcího příkazu.
        
App Service ignoruje všechny chyby, ke kterým došlo při zpracování vlastního spouštěcího příkazu nebo souboru, a pak pokračuje v procesu jeho spuštění hledáním aplikací Django a baněk. Pokud nevidíte očekávané chování, zkontrolujte, že spouštěcí příkaz nebo soubor nejsou v chybovém provozu a že soubor spouštěcích příkazů je nasazený, aby App Service spolu s kódem vaší aplikace. Můžete také vyhledat další informace v [diagnostických protokolech](#access-diagnostic-logs) . Podívejte se také na stránku **Diagnostika a řešení problémů** aplikace na [Azure Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Příklady spouštěcích příkazů

- **Přidané argumenty Gunicorn**: Následující příklad přidá `--workers=4` do příkazového řádku Gunicorn pro spuštění aplikace Django: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Další informace najdete v [Running Gunicorn (Spuštění serveru Gunicorn)](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Povolit protokolování výroby pro Django**: přidejte `--access-logfile '-'` argumenty a `--error-logfile '-'` do příkazového řádku:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Tyto protokoly se zobrazí v [datovém proudu protokolu App Service](#access-diagnostic-logs).

    Další informace najdete v tématu [protokolování Gunicorn](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.Gunicorn.org).
    
- **Hlavní modul vlastní baňky**: ve výchozím nastavení App Service předpokládá, že hlavní modul aplikace v baňce je *Application.py* nebo *App.py*. Pokud váš hlavní modul používá jiný název, musíte upravit spouštěcí příkaz. Například YF máte aplikaci v baňce, jejíž hlavní modul je *Hello.py* a objekt aplikace baňky v tomto souboru je pojmenován `myapp` , příkaz je následující:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Pokud je hlavní modul v podsložce, například `website`, zadejte tuto složku pomocí argumentu `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Použít jiný server než Gunicorn**: Pokud chcete použít jiný webový server, jako je třeba [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), použijte jako spouštěcí příkaz nebo v souboru spouštěcího příkazu příslušný příkaz:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Přístup k nastavení aplikace jako proměnných prostředí

Nastavení aplikace jsou hodnoty uložené v cloudu, které jsou specifické pro vaši aplikaci, jak je popsáno v tématu [Konfigurace nastavení aplikace](configure-common.md#configure-app-settings). Tato nastavení jsou k dispozici pro kód vaší aplikace jako proměnné prostředí a jsou přístupná pomocí standardního vzoru [OS. Environ –](https://docs.python.org/3/library/os.html#os.environ) .

Pokud jste například vytvořili nastavení aplikace s názvem `DATABASE_SERVER` , následující kód načte hodnotu tohoto nastavení:

```python
db_server = os.environ['DATABASE_SERVER']
```
    
## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje, aby zkontrolovala, jestli jsou požadavky uživatele zašifrované, zkontrolujte `X-Forwarded-Proto` záhlaví.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Oblíbená webová rozhraní umožňují přístup k `X-Forwarded-*` informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/) [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) ve `X_FORWARDED_PROTO` výchozím nastavení kontroluje hodnotu.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Pokud chcete získat přístup k protokolům prostřednictvím **Monitoring**Azure Portal, vyberte  >  v místní nabídce vaší aplikace**Stream protokolu** monitorování.

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

- **Výchozí aplikaci uvidíte po nasazení kódu vlastní aplikace.** Výchozí aplikace se zobrazí, protože buď nemáte nasazený kód aplikace App Service, nebo App Service se nepovedlo najít kód vaší aplikace a místo toho se spustila výchozí aplikace.

    - Restartujte službu App Service, počkejte 15-20 sekund a znovu zkontrolujte aplikaci.
    
    - Ujistěte se, že používáte App Service pro Linux, a ne instanci založenou na Windows. V Azure CLI spusťte příkaz `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` a hodnoty `<resource-group-name>` a `<app-service-name>` nahraďte odpovídajícím způsobem. Jako výstup by se mělo zobrazit `app,linux`. Pokud ne, vytvořte službu App Service znovu a zvolte Linux.
    
    - Připojte se přímo ke službě App Service pomocí SSH nebo konzoly Kudu a ověřte, že vaše soubory v *site/wwwroot* existují. Pokud soubory neexistují, zopakujte proces nasazení a aplikaci znovu nasaďte.
    
    - Pokud soubory existují, neidentifikovala služba App Service konkrétní spouštěcí soubor. Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).

- **V prohlížeči se zobrazí zpráva „Služba není dostupná“.** Vypršel časový limit čekání prohlížeče na odpověď služby App Service. To naznačuje, že služba App Service sice spustila server Gunicorn, ale argumenty, které specifikuje kód aplikace, jsou nesprávné.

    - Aktualizujte okno prohlížeče, zejména v případě, že používáte nejnižší cenové úrovně v Plánu služby App Service. Aplikace se může spouštět pomaleji (když používáte například úrovně free) a po aktualizaci okna prohlížeče začne znovu odpovídat.

    - Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).

    - Prověřte [datový proud protokolu](#access-diagnostic-logs) pro všechny chybové zprávy.

- **Stream protokolu zobrazuje "nepovedlo se najít Setup.py nebo requirements.txt; Nespouští se instalace PIP. "**: procesu sestavení Oryx se nepovedlo najít soubor *requirements.txt* .

    - Pomocí SSH nebo konzoly Kudu se připojte přímo k App Service a ověřte, že *requirements.txt* existuje přímo v *lokalitě/wwwroot*. Pokud neexistuje, zajistěte, aby soubor v úložišti existoval a byl zahrnutý v nasazení. Pokud existuje v samostatné složce, přesuňte ji do kořenového adresáře.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace v Pythonu s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Kurz: nasazení z privátního úložiště kontejnerů](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)
