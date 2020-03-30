---
title: Konfigurace aplikací Linux Python
description: Přečtěte si, jak nakonfigurovat předem sestavený kontejner Pythonu pro vaši aplikaci. Tento článek ukazuje nejběžnější úlohy konfigurace.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8a9276f73c1d9bdf0289f41bb59340b29f5a2575
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046027"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurace aplikace Linux Python pro Azure App Service

Tento článek popisuje, jak [služba Azure App Service](app-service-linux-intro.md) spouští aplikace Pythonu a jak můžete přizpůsobit chování služby App Service v případě potřeby. Aplikace Pythonu musí být nasazeny se všemi požadovanými [moduly pip.](https://pypi.org/project/pip/)

Modul nasazení služby App Service automaticky `pip install -r requirements.txt` aktivuje virtuální prostředí a spustí se za vás, když nasadíte [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)nebo [balíček Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) se zapnutými procesy sestavení.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře Pythonu, kteří používají vestavěný kontejner Linuxu ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, měli byste se nejprve řídit [rychlým startem Pythonu](quickstart-python.md) a [Pythonem s postgreSQL kurzem.](tutorial-python-postgresql-app.md)

> [!NOTE]
> Linux je v současné době doporučená možnost pro spouštění aplikací Pythonu v App Service. Informace o možnosti Windows najdete v [pythonu na windows flavor app service](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Zobrazit verzi Pythonu

Chcete-li zobrazit aktuální verzi Pythonu, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze Pythonu, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Můžete spustit nepodporovanou verzi Pythonu vytvořením vlastní image kontejneru místo. Další informace naleznete [v tématu použití vlastní image Dockeru](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Nastavit verzi Pythonu

Spusťte následující příkaz v [prostředí Cloud Shell](https://shell.azure.com) a nastavte verzi Pythonu na verzi 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud aplikaci nasadíte pomocí balíčků Git nebo zip se zapnutou automatizací sestavení, služba App Service provede automatizaci automatizace následujícím pořadím:

1. Spusťte vlastní `PRE_BUILD_SCRIPT_PATH`skript, pokud je určen programem .
1. Spusťte `pip install -r requirements.txt`.
1. Pokud *manage.py* je nalezen v kořenovém adresáři úložiště, spusťte *manage.py collectstatic*. Pokud `DISABLE_COLLECTSTATIC` je však `true`nastavena na , je tento krok přeskočen.
1. Spusťte vlastní `POST_BUILD_SCRIPT_PATH`skript, pokud je určen programem .

`PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND`a `DISABLE_COLLECTSTATIC` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy `PRE_BUILD_COMMAND`předběžného sestavení, definujte . Chcete-li spustit příkazy `POST_BUILD_COMMAND`po sestavení, definujte . Chcete-li zakázat spouštění shromažďovaných aplikací Collectstatic, nastavte . `DISABLE_COLLECTSTATIC=true`

Následující příklad určuje dvě proměnné řady příkazů oddělených čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v [tématu Konfigurace Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak služba App Service běží a vytváří aplikace Pythonu v Linuxu, najdete v [dokumentaci oryxu: Jak jsou aplikace Pythonu detekovány a sestaveny](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

## <a name="container-characteristics"></a>Vlastnosti kontejneru

Aplikace Pythonu nasazené do služby App Service na Linuxu běží v kontejneru Dockeru, který je definovaný v [úložišti GitHub služby App Service](https://github.com/Azure-App-Service/python). Konfigurace obrázků najdete v adresářích specifických pro verzi.

Tento kontejner má následující vlastnosti:

- Aplikace se spouštějí pomocí [Gunicorn WSGI HTTP Server](https://gunicorn.org/) za použití dalších argumentů `--bind=0.0.0.0 --timeout 600`.

- Základní image zahrnuje ve výchozím nastavení webovou architekturu Flask, ale kontejner podporuje další formáty, které odpovídají WSGI a jsou kompatibilní s Pythonem 3.7, například Django.

- Pokud chcete instalovat doplňkové balíčky, jako třeba Django, vytvořte soubor [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) v kořenové složce projektu pomocí `pip freeze > requirements.txt`. Pak publikujte projekt na službu App Service nasazením z Gitu. Tím se automaticky spustí `pip install -r requirements.txt` v kontejneru pro instalaci závislostí aplikace.

## <a name="container-startup-process"></a>Proces spuštění kontejneru

V průběhu spuštění služba App Service v kontejneru Linuxu spustí následující kroky:

1. Použijte [vlastní příkaz pro spuštění](#customize-startup-command), pokud je k dispozici.
2. Zkontrolujte existenci [aplikace Django](#django-app)a spusťte Gunicorn pro to, pokud je detekován.
3. Zkontrolujte, zda existuje [aplikace Flask](#flask-app), a spustit Gunicorn pro něj, pokud je detekován.
4. Pokud se žádná další aplikace nenajde, následuje spuštění výchozí aplikace sestavené do kontejneru.

Podrobnosti ke každému bodu poskytnou následující oddíly.

### <a name="django-app"></a>Aplikace Django

Služba App Service hledá pro aplikace Django soubory se jménem `wsgi.py` v kódu aplikace. Pak spustí server Gunicorn s použitím následujícího příkazu:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Pokud chcete mít přesnější kontrolu nad spouštěcími příkazy, použijte [vlastní spouštěcí příkaz](#customize-startup-command) a nahraďte `<module>` názvem modulu obsahujícího *wsgi.py*.

### <a name="flask-app"></a>Aplikace Flask

Pro Flask, App Service hledá soubor s názvem *application.py* nebo *app.py* a spustí Gunicorn takto:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Pokud je hlavní modul aplikace v jiném souboru, použijte jiný název objektu aplikace. Když chcete zadat další argumenty serveru Gunicorn, použijte [vlastní spouštěcí příkaz](#customize-startup-command).

### <a name="default-behavior"></a>Výchozí chování

Pokud služba App Service nenajde vlastní příkaz, aplikaci Django nebo aplikaci Flask, spustí výchozí aplikaci jen pro čtení umístěnou ve složce _opt/defaultsite_. Výchozí aplikace se zobrazí takto:

![Výchozí služba App Service na Linuxové webové stránce](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Přizpůsobit příkaz při spuštění

Zadáním vlastního spouštěcího příkazu serveru Gunicorn můžete řídit chování spuštění kontejneru. Chcete-li to provést, spuštění následujícího příkazu v [prostředí Cloud :](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Například pokud máte flask aplikace, jejíž hlavní modul je *hello.py* a `myapp`flask app objekt v tomto souboru je pojmenován , pak * \<vlastní příkaz>* je následující:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Pokud je hlavní modul v podsložce, například `website`, zadejte tuto složku pomocí argumentu `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Můžete také přidat další argumenty pro * \< *Gunicorn na vlastní `--workers=4`příkaz>, jako je . Další informace najdete v [Running Gunicorn (Spuštění serveru Gunicorn)](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Pokud chcete použít server bez Gunicornu, například [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), můžete * \<nahradit>vlastních příkazů* něčím takovým:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Služba App Service ignoruje všechny chyby, ke kterým dojde při zpracování souboru vlastního příkazu. Pak proces spuštění pokračuje hledáním aplikací Flask a Django. Pokud neuvidíte očekávané chování, zkontrolujte, že je spouštěcí soubor nasazený do služby App Service a neobsahuje žádné chyby.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [os.environ.](https://docs.python.org/3/library/os.html#os.environ) Chcete-li například získat `WEBSITE_SITE_NAME`přístup k nastavení aplikace s názvem , použijte následující kód:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Rozpoznat relaci HTTPS

Ve službě App Service dojde k [ukončení SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v síťových nástrojích pro vyrovnávání zatížení, takže všechny požadavky HTTPS se dostanou do vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky `X-Forwarded-Proto` uživatelů zašifrované nebo ne, zkontrolujte záhlaví.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Oblíbené webové architektury umožňují `X-Forwarded-*` přístup k informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) kontroluje `X_FORWARDED_PROTO` hodnotu ve výchozím nastavení.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

- **Výchozí aplikaci uvidíte po nasazení kódu vlastní aplikace.** Výchozí aplikace se zobrazí, protože jste buď nenasadili kód aplikace do služby App Service, nebo se službě App Service nepodařilo najít kód aplikace a místo toho spustila výchozí aplikaci.
- Restartujte službu App Service, počkejte 15-20 sekund a znovu zkontrolujte aplikaci.
- Ujistěte se, že používáte App Service pro Linux, a ne instanci založenou na Windows. V Azure CLI spusťte příkaz `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` a hodnoty `<resource_group_name>` a `<app_service_name>` nahraďte odpovídajícím způsobem. Jako výstup by se mělo zobrazit `app,linux`. Pokud ne, vytvořte službu App Service znovu a zvolte Linux.
- Připojte se přímo ke službě App Service pomocí SSH nebo konzoly Kudu a ověřte, že vaše soubory v *site/wwwroot* existují. Pokud soubory neexistují, zopakujte proces nasazení a aplikaci znovu nasaďte.
- Pokud soubory existují, neidentifikovala služba App Service konkrétní spouštěcí soubor. Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).
- **V prohlížeči se zobrazí zpráva „Služba není dostupná“.** Vypršel časový limit čekání prohlížeče na odpověď služby App Service. To naznačuje, že služba App Service sice spustila server Gunicorn, ale argumenty, které specifikuje kód aplikace, jsou nesprávné.
- Aktualizujte okno prohlížeče, zejména v případě, že používáte nejnižší cenové úrovně v Plánu služby App Service. Aplikace se může spouštět pomaleji (když používáte například úrovně free) a po aktualizaci okna prohlížeče začne znovu odpovídat.
- Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).
- [Přístup k datovému proudu protokolu](#access-diagnostic-logs).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Aplikace Python s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Kurz: Nasazení z úložiště soukromých kontejnerů](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k aplikační službě Linux](app-service-linux-faq.md)
