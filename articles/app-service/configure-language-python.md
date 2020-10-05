---
title: Konfigurace aplikací pro Linux Python
description: Přečtěte si, jak nakonfigurovat předem sestavený kontejner Pythonu pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: 88a3876757c3f51d1fc70692976987560ee1b991
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961631"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux v Pythonu pro Azure App Service

Tento článek popisuje, jak [Azure App Service](overview.md) spouští aplikace v Pythonu a jak můžete v případě potřeby přizpůsobit chování App Service. Aplikace Python musí být nasazené se všemi požadovanými moduly [PIP](https://pypi.org/project/pip/) .

Modul pro nasazení App Service automaticky aktivuje virtuální prostředí a spustí se při `pip install -r requirements.txt` nasazení [úložiště Git](deploy-local-git.md)nebo [balíčku zip](deploy-zip.md) s procesy sestavení přepnutými na.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v Pythonu, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste nikdy Azure App Service nepoužili, měli byste nejdřív postupovat podle kurzu [rychlý Start](quickstart-python.md) a [Pythonu v](tutorial-python-postgresql-app.md) Pythonu.

> [!NOTE]
> Linux je aktuálně doporučenou možností pro spouštění aplikací v Pythonu v App Service. Informace o možnostech Windows najdete v tématu věnovaném jazyku [Python na App Service systému Windows](/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Zobrazit verzi Pythonu

Pokud chcete zobrazit aktuální verzi Pythonu, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze Pythonu, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Nepodporovanou verzi Pythonu můžete spustit místo toho vytvořením vlastní image kontejneru. Další informace najdete v tématu věnovaném [použití vlastní image Dockeru](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-python-version"></a>Nastavit verzi Pythonu

Spusťte následující příkaz v [Cloud Shell](https://shell.azure.com) k nastavení verze Pythonu na 3,7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud nasadíte aplikaci s použitím balíčků Git nebo zip se zapnutou možností automatizace sestavení, App Service sestavování kroků automatizace pomocí následujícího postupu:

1. Spusťte vlastní skript, pokud je určen `PRE_BUILD_SCRIPT_PATH` .
1. Spusťte `pip install -r requirements.txt`.
1. Pokud se *Manage.py* najde v kořenovém adresáři úložiště, spusťte *Manage.py collectstatic*. Pokud je však `DISABLE_COLLECTSTATIC` Tento krok nastaven na hodnotu `true` , bude tento krok přeskočen.
1. Spusťte vlastní skript, pokud je určen `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND` a `DISABLE_COLLECTSTATIC` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy před sestavením, definujte `PRE_BUILD_COMMAND` . Chcete-li spustit příkazy po sestavení, definujte `POST_BUILD_COMMAND` . Pokud chcete zakázat spouštění collectstatic při sestavování aplikací Django, nastavte `DISABLE_COLLECTSTATIC=true` .

Následující příklad určuje dvě proměnné pro řadu příkazů, které jsou odděleny čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v tématu [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak App Service spouští a vytváří aplikace v Pythonu v systému Linux, najdete v [dokumentaci k Oryx: jak se zjišťují a vytváří aplikace v Pythonu](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

## <a name="container-characteristics"></a>Vlastnosti kontejneru

Aplikace Python nasazené do App Service v systému Linux běží v kontejneru Docker, který je definovaný v [úložišti GitHub App Service Pythonu](https://github.com/Azure-App-Service/python). Konfigurace imagí najdete v adresářích, které jsou specifické pro danou verzi.

Tento kontejner má následující vlastnosti:

- Aplikace se spouštějí pomocí [Gunicorn WSGI HTTP Server](https://gunicorn.org/) za použití dalších argumentů `--bind=0.0.0.0 --timeout 600`.

- Základní image zahrnuje ve výchozím nastavení webovou architekturu Flask, ale kontejner podporuje další formáty, které odpovídají WSGI a jsou kompatibilní s Pythonem 3.7, například Django.

- Pokud chcete instalovat doplňkové balíčky, jako třeba Django, vytvořte soubor [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) v kořenové složce projektu pomocí `pip freeze > requirements.txt`. Pak publikujte projekt na službu App Service nasazením z Gitu. Tím se automaticky spustí `pip install -r requirements.txt` v kontejneru pro instalaci závislostí aplikace.

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
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Pokud chcete mít přesnější kontrolu nad spouštěcími příkazy, použijte [vlastní spouštěcí příkaz](#customize-startup-command) a nahraďte `<module>` názvem modulu obsahujícího *wsgi.py*.

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

Zadáním vlastního spouštěcího příkazu serveru Gunicorn můžete řídit chování spuštění kontejneru. Uděláte to tak, že na [Cloud Shell](https://shell.azure.com)spustíte následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Například pokud máte aplikaci v baňce, jejíž hlavní modul je *Hello.py* , a objekt aplikace v baňce je pojmenován `myapp` , pak *\<custom-command>* je následující:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Pokud je hlavní modul v podsložce, například `website`, zadejte tuto složku pomocí argumentu `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Můžete také přidat další argumenty pro Gunicorn do *\<custom-command>* , například `--workers=4` . Další informace najdete v [Running Gunicorn (Spuštění serveru Gunicorn)](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Pokud chcete použít jiný server než Gunicorn, jako je například [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), můžete nahradit *\<custom-command>* podobným způsobem:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Služba App Service ignoruje všechny chyby, ke kterým dojde při zpracování souboru vlastního příkazu. Pak proces spuštění pokračuje hledáním aplikací Flask a Django. Pokud neuvidíte očekávané chování, zkontrolujte, že je spouštěcí soubor nasazený do služby App Service a neobsahuje žádné chyby.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [OS. Environ –](https://docs.python.org/3/library/os.html#os.environ) . Například pro přístup k aplikačnímu nastavení s názvem `WEBSITE_SITE_NAME` použijete následující kód:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje, aby zkontrolovala, jestli jsou požadavky uživatele zašifrované, zkontrolujte `X-Forwarded-Proto` záhlaví.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Oblíbená webová rozhraní umožňují přístup k `X-Forwarded-*` informacím ve standardním vzoru aplikace. V [CodeIgniter](https://codeigniter.com/) [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) ve `X_FORWARDED_PROTO` výchozím nastavení kontroluje hodnotu.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

- **Výchozí aplikaci uvidíte po nasazení kódu vlastní aplikace.** Výchozí aplikace se zobrazí, protože buď nemáte nasazený kód aplikace App Service, nebo App Service se nepovedlo najít kód vaší aplikace a místo toho se spustila výchozí aplikace.
- Restartujte službu App Service, počkejte 15-20 sekund a znovu zkontrolujte aplikaci.
- Ujistěte se, že používáte App Service pro Linux, a ne instanci založenou na Windows. V Azure CLI spusťte příkaz `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` a hodnoty `<resource_group_name>` a `<app_service_name>` nahraďte odpovídajícím způsobem. Jako výstup by se mělo zobrazit `app,linux`. Pokud ne, vytvořte službu App Service znovu a zvolte Linux.
- Připojte se přímo ke službě App Service pomocí SSH nebo konzoly Kudu a ověřte, že vaše soubory v *site/wwwroot* existují. Pokud soubory neexistují, zopakujte proces nasazení a aplikaci znovu nasaďte.
- Pokud soubory existují, neidentifikovala služba App Service konkrétní spouštěcí soubor. Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).
- **V prohlížeči se zobrazí zpráva „Služba není dostupná“.** Vypršel časový limit čekání prohlížeče na odpověď služby App Service. To naznačuje, že služba App Service sice spustila server Gunicorn, ale argumenty, které specifikuje kód aplikace, jsou nesprávné.
- Aktualizujte okno prohlížeče, zejména v případě, že používáte nejnižší cenové úrovně v Plánu služby App Service. Aplikace se může spouštět pomaleji (když používáte například úrovně free) a po aktualizaci okna prohlížeče začne znovu odpovídat.
- Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).
- [Přístup ke streamu protokolů](#access-diagnostic-logs).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace v Pythonu s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Kurz: nasazení z privátního úložiště kontejnerů](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)