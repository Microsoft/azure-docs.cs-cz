---
title: Konfigurace aplikace v Pythonu – Azure App Service
description: Tento kurz popisuje možnosti vytváření a konfigurace aplikací Pythonu pro službu Azure App Service v Linuxu.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: f8894132dae179be2d5d9d9b6887851be78d7746
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60854884"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurace aplikace v Pythonu s Linuxem pro Azure App Service

Tento článek popisuje, jak [služby Azure App Service](app-service-linux-intro.md) spouští aplikace v Pythonu a jak můžete přizpůsobit chování služby App Service v případě potřeby. Aplikace v Pythonu se musí nasadit všechny požadované [pip](https://pypi.org/project/pip/) moduly.

Modul pro nasazení služby App Service automaticky aktivuje virtuální prostředí a spustí `pip install -r requirements.txt` za vás při nasazování [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), nebo [zazipovaný balíček s](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s procesy sestavení, které jsou zapnuty.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Pythonu, kteří používají předdefinované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, měli byste postupovat podle [rychlý start Python](quickstart-python.md) a [Python s PostgreSQL kurzu](tutorial-python-postgresql-app.md) první.

> [!NOTE]
> Linux momentálně možnost se doporučuje pro spuštění aplikace v Pythonu ve službě App Service. Informace o možnosti Windows, naleznete v tématu [Pythonu ve Windows charakter služby App Service](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Zobrazit verze Pythonu

Chcete-li zobrazit aktuální verzi Pythonu, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze Pythonu, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Místo vytvoření vlastní image kontejneru, můžete spustit nepodporovanou verzi Pythonu. Další informace najdete v tématu [použití vlastní image Dockeru](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Nastavení verze Pythonu

Spuštěním následujícího příkazu [Cloud Shell](https://shell.azure.com) nastavení verze Pythonu 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>Vlastnosti kontejneru

Aplikace v Pythonu nasadily do App Service v Linuxu spusťte v kontejneru Dockeru, který je definován v úložišti GitHub [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) nebo [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Tento kontejner má následující vlastnosti:

- Aplikace se spouštějí pomocí [Gunicorn WSGI HTTP Server](https://gunicorn.org/) za použití dalších argumentů `--bind=0.0.0.0 --timeout 600`.

- Základní image zahrnuje ve výchozím nastavení webovou architekturu Flask, ale kontejner podporuje další formáty, které odpovídají WSGI a jsou kompatibilní s Pythonem 3.7, například Django.

- Pokud chcete instalovat doplňkové balíčky, jako třeba Django, vytvořte soubor [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) v kořenové složce projektu pomocí `pip freeze > requirements.txt`. Pak publikujte projekt na službu App Service nasazením z Gitu. Tím se automaticky spustí `pip install -r requirements.txt` v kontejneru pro instalaci závislostí aplikace.

## <a name="container-startup-process"></a>Proces spuštění kontejneru

V průběhu spuštění služba App Service v kontejneru Linuxu spustí následující kroky:

1. Použití [vlastní spouštěcí příkaz](#customize-startup-command), pokud je k dispozici.
2. Zkontrolujte existenci [aplikace Django](#django-app)a pokud se zjistí spuštění Gunicorn pro něj.
3. Zkontrolujte existenci [aplikace Flask](#flask-app)a pokud se zjistí spuštění Gunicorn pro něj.
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

Pro Flask, App Service hledá soubor s názvem *application.py* nebo *app.py* a spustí Gunicorn následujícím způsobem:

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

## <a name="customize-startup-command"></a>Přizpůsobení spouštěcí příkaz

Zadáním vlastního spouštěcího příkazu serveru Gunicorn můžete řídit chování spuštění kontejneru. Provedete to spuštěním následujícího příkazu [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Například pokud máte aplikaci Flask, jejichž hlavní modul je *hello.py* a pojmenovaný objekt aplikace Flask v daném souboru `myapp`, pak  *\<vlastního příkazu >* vypadá takto:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Pokud je hlavní modul v podsložce, například `website`, zadejte tuto složku pomocí argumentu `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Můžete také přidat libovolné další argumenty pro Gunicorn k  *\<vlastního příkazu >*, jako například `--workers=4`. Další informace najdete v [Running Gunicorn (Spuštění serveru Gunicorn)](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Použití bez Gunicorn serveru, jako například [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), můžete nahradit  *\<vlastního příkazu >* kód podobný tomuto:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Služba App Service ignoruje všechny chyby, ke kterým dojde při zpracování souboru vlastního příkazu. Pak proces spuštění pokračuje hledáním aplikací Flask a Django. Pokud neuvidíte očekávané chování, zkontrolujte, že je spouštěcí soubor nasazený do služby App Service a neobsahuje žádné chyby.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) mimo kód vaší aplikace. Pak můžete přistupovat pomocí standardu [os.environ](https://docs.python.org/3/library/os.html#os.environ) vzor. Například pro přístup k nastavení aplikace volá `WEBSITE_SITE_NAME`, použijte následující kód:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Zjistit relace HTTPS

Ve službě App Service [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se odehrává na nástroje pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS kontaktovat vaši aplikaci jako nešifrované požadavky HTTP. Pokud požadavkům vašich aplikací logiky ke kontrole, pokud jsou zašifrované požadavky uživatelů, nebo Ne, zkontrolujte `X-Forwarded-Proto` záhlaví.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Oblíbené webové architektury umožněte přístup `X-Forwarded-*` informace o vzoru standardní aplikace. V [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) zkontroluje hodnotu vlastnosti `X_FORWARDED_PROTO` ve výchozím nastavení.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

- **Výchozí aplikaci uvidíte po nasazení kódu vlastní aplikace.** Výchozí aplikace se zobrazí, protože buď nasazený kód vaší aplikace do služby App Service nebo služby App Service se nepovedlo najít kód vaší aplikace a spustili aplikaci výchozí místo.
- Restartujte službu App Service, počkejte 15-20 sekund a znovu zkontrolujte aplikaci.
- Ujistěte se, že používáte App Service pro Linux, a ne instanci založenou na Windows. V Azure CLI spusťte příkaz `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` a hodnoty `<resource_group_name>` a `<app_service_name>` nahraďte odpovídajícím způsobem. Jako výstup by se mělo zobrazit `app,linux`. Pokud ne, vytvořte službu App Service znovu a zvolte Linux.
- Připojte se přímo ke službě App Service pomocí SSH nebo konzoly Kudu a ověřte, že vaše soubory v *site/wwwroot* existují. Pokud soubory neexistují, zopakujte proces nasazení a aplikaci znovu nasaďte.
- Pokud soubory existují, neidentifikovala služba App Service konkrétní spouštěcí soubor. Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).
- **V prohlížeči se zobrazí zpráva „Služba není dostupná“.** Vypršel časový limit čekání prohlížeče na odpověď služby App Service. To naznačuje, že služba App Service sice spustila server Gunicorn, ale argumenty, které specifikuje kód aplikace, jsou nesprávné.
- Aktualizujte okno prohlížeče, zejména v případě, že používáte nejnižší cenové úrovně v Plánu služby App Service. Aplikace se může spouštět pomaleji (když používáte například úrovně free) a po aktualizaci okna prohlížeče začne znovu odpovídat.
- Zkontrolujte, že má aplikace strukturu, kterou služba App Service očekává u aplikací [Django](#django-app) nebo [Flask](#flask-app), nebo použijte [vlastní spouštěcí příkaz](#customize-startup-command).
- [Přístup k datovému proudu protokolů](#access-diagnostic-logs).

## <a name="next-steps"></a>Další postup

Integrované image Pythonu ve službě App Service v Linuxu je aktuálně ve verzi Preview a můžete přizpůsobit příkaz používá ke spuštění vaší aplikace. Produkční aplikace v Pythonu můžete místo toho vytvářet taky s využitím vlastního kontejneru.

> [!div class="nextstepaction"]
> [Kurz: Aplikace v Pythonu s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Kurz: Nasazení z kontejneru soukromého úložiště](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [App Service Linuxu – nejčastější dotazy](app-service-linux-faq.md)