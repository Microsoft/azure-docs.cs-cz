---
title: 'Rychlý Start: Vytvoření aplikace v Pythonu'
description: Začněte s Azure App Service nasazením první aplikace v Pythonu do kontejneru Linux v App Service.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
ms.openlocfilehash: 8f48f31cdaaa555e0a8f6f0fd4756bb61a9f417d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741105"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Rychlý Start: Vytvoření aplikace v Pythonu v Azure App Service v systému Linux

V tomto rychlém startu nasadíte webovou aplikaci v Pythonu, která se [App Service v systému Linux](overview.md#app-service-on-linux), vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Pomocí místního [rozhraní příkazového řádku Azure (CLI)](/cli/azure/install-azure-cli) na počítači se systémem Mac, Linux nebo Windows nasadíte ukázku s použitím této baňky nebo Djangoch architektur. Webová aplikace, kterou nakonfigurujete, využívá bezplatnou App Serviceovou úroveň, takže v tomto článku nebudete mít žádné náklady.

> [!TIP]
> Pokud raději místo toho použijete Visual Studio Code, postupujte podle našeho **[Visual Studio Codeu pro rychlý start App Service](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** .

## <a name="set-up-your-initial-environment"></a>Nastavení počátečního prostředí

1. Mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Nainstalujte <a href="https://www.python.org/downloads/" target="_blank">Python 3,6 nebo vyšší</a>.
1. Nainstalujte rozhraní příkazového <a href="/cli/azure/install-azure-cli" target="_blank">řádku Azure CLI</a> 2.0.80 nebo novější, ve kterém spouštíte příkazy v jakémkoli prostředí pro zřizování a konfiguraci prostředků Azure.

Otevřete okno terminálu a ověřte, jestli je verze Pythonu 3,6 nebo vyšší:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Ověřte, jestli je verze Azure CLI 2.0.80á nebo vyšší:

```azurecli
az --version
```

Pak se přihlaste k Azure prostřednictvím rozhraní příkazového řádku:

```azurecli
az login
```

Tento příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů. Po dokončení příkazu se zobrazí výstup JSON obsahující informace o vašich předplatných.

Po přihlášení můžete spustit příkazy Azure pomocí Azure CLI a pracovat s prostředky ve vašem předplatném.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Vytvoření klonu ukázky

Naklonujte ukázkové úložiště pomocí následujícího příkazu a přejděte do ukázkové složky. (Pokud Git ještě nemáte,[nainstalujte Git](https://git-scm.com/downloads) .)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Pak přejděte do této složky:

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

Pak přejděte do této složky:

```terminal
cd python-docs-hello-django
```
::: zone-end

Ukázka obsahuje kód specifický pro rozhraní, který Azure App Service rozpoznat při spuštění aplikace. Další informace najdete v tématu [proces spuštění kontejneru](configure-language-python.md#container-startup-process).

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Spuštění ukázky

::: zone pivot="python-framework-flask"
1. Ujistěte se, že jste ve složce *Python-docs-Hello-World* . 

1. Vytvořte virtuální prostředí a nainstalujte závislosti:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Pokud narazíte na [errno 2] žádný takový soubor nebo adresář: requirements.txt. Ujistěte se, že jste ve složce *Python-docs-Hello-World* .

1. Spusťte vývojový server.

    ```terminal  
    flask run
    ```
    
    Ve výchozím nastavení server předpokládá, že je modul vstupu aplikace v *App.py* , jak se používá v ukázce. (Pokud použijete jiný název modulu, nastavte `FLASK_APP` proměnnou prostředí na tento název.)

1. Otevřete webový prohlížeč a v části použijte ukázkovou aplikaci `http://localhost:5000/` . Aplikace zobrazí zprávu **Hello, World!** .

    ![Místní spuštění ukázkové aplikace v Pythonu](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. V okně terminálu ukončete vývojový server stisknutím **kombinace kláves CTRL +** + **C** .
::: zone-end

::: zone pivot="python-framework-django"
1. Ujistěte se, že jste ve složce *Python-docs-Hello-Django* . 

1. Vytvořte virtuální prostředí a nainstalujte závislosti:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Pokud narazíte na [errno 2] žádný takový soubor nebo adresář: ' requirements.txt '. ', ujistěte se, že jste ve složce *Python-docs-Hello-Django* .
    
1. Spusťte vývojový server.

    ```terminal
    python manage.py runserver
    ```

1. Otevřete webový prohlížeč a v části použijte ukázkovou aplikaci `http://localhost:8000/` . Aplikace zobrazí zprávu **Hello, World!** .

    ![Místní spuštění ukázkové aplikace v Pythonu](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. V okně terminálu ukončete vývojový server stisknutím **kombinace kláves CTRL +** + **C** .
::: zone-end

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Nasazení ukázky

Nasaďte kód do místní složky ( *Python-docs-Hello-World* ) pomocí `az webapp up` příkazu:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Pokud `az` příkaz není rozpoznaný, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure, jak je popsané v tématu [Nastavení počátečního prostředí](#set-up-your-initial-environment).
- Pokud `webapp` příkaz není rozpoznaný, protože verze Azure CLI je 2.0.80 nebo vyšší. Pokud ne, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli).
- Nahraďte `<app_name>` názvem, který je jedinečný v rámci všech Azure ( *platné znaky jsou `a-z` , `0-9` a `-`* ). Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.
- `--sku F1`Argument vytvoří webovou aplikaci na cenové úrovni Free. Vynechejte tento argument pro použití rychlejší úrovně Premium, což stojí za hodinu.
- Volitelně můžete zahrnout argument `--location <location-name>` , kde `<location_name>` je dostupná oblast Azure. Seznam povolených oblastí pro váš účet Azure můžete načíst spuštěním [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) příkazu.
- Pokud se zobrazí chyba, "nebylo možné automaticky rozpoznat zásobník modulu runtime vaší aplikace", ujistěte se, že jste spustili příkaz ve složce *Python-docs-Hello-World* (baňka), nebo ve složce *Python-docs-Hello-Django* (Django), která obsahuje soubor *requirements.txt* . (Další informace najdete v tématu [řešení potíží při automatickém rozpoznávání potíží pomocí AZ WebApp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

Dokončení příkazu může trvat několik minut. Při spuštění poskytuje zpráva o vytváření skupiny prostředků, App Service plánování a hostování aplikace, konfiguraci protokolování a následném nasazení souboru ZIP. Pak mu zobrazí zprávu "aplikaci můžete spustit v http:// &lt; App-name &gt; . azurewebsites.NET", což je adresa URL aplikace v Azure.

![Příklad výstupu příkazu AZ WebApp up](./media/quickstart-python/az-webapp-up-output.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Přechod do aplikace

Ve webovém prohlížeči přejděte do nasazené aplikace na adrese URL `http://<app-name>.azurewebsites.net` . Spuštění aplikace se zpočátku zabere na chvíli.

Vzorový kód Pythonu spouští kontejner Linux v App Service pomocí integrované image.

![Spuštění ukázkové aplikace v Pythonu v Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Blahopřejeme!** Nasadili jste aplikaci v Pythonu na App Service.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Znovu nasadit aktualizace

V této části provedete změnu malého kódu a pak znovu nasadíte kód do Azure. Změna kódu obsahuje `print` příkaz pro generování výstupu protokolování, se kterým pracujete v následující části.

::: zone pivot="python-framework-flask"
Otevřete *App.py* v editoru a aktualizujte `hello` funkci tak, aby odpovídala následujícímu kódu. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Otevřete text *Hello/views. pys* v editoru a aktualizujte `hello` funkci tak, aby odpovídala následujícímu kódu.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Uložte změny a pak znovu nasaďte aplikaci pomocí `az webapp up` příkazu:

```azurecli
az webapp up
```

Tento příkaz používá hodnoty, které jsou uložené místně v souboru *. Azure/config* , včetně názvu aplikace, skupiny prostředků a plánu App Service.

Po dokončení nasazení přepněte zpátky do okna prohlížeče otevřeného na `http://<app-name>.azurewebsites.net` . Aktualizujte stránku, kde by se měla zobrazit upravená zpráva:

![Spuštění aktualizované ukázkové aplikace v Pythonu v Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code poskytuje výkonná rozšíření pro Python a Azure App Service, která zjednodušují proces nasazení webových aplikací v Pythonu na App Service. Další informace najdete v tématu [nasazení aplikací v Pythonu pro App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Protokoly datových proudů

Můžete přistupovat k protokolům konzoly generovaným zevnitř aplikace a kontejnerem, ve kterém je spuštěný. Protokoly obsahují výstup generovaný pomocí `print` příkazů.

Pokud chcete streamovat protokoly, spusťte příkaz [AZ WebApp log Tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) :

```azurecli
az webapp log tail
```

`--logs` `az webapp up` K automatickému otevření streamu protokolu při nasazení můžete taky použít parametr s příkazem.

Aktualizujte aplikaci v prohlížeči, aby se generovaly protokoly konzoly, které obsahují zprávy popisující požadavky HTTP na aplikaci. Pokud se žádný výstup nezobrazuje hned, zkuste to znovu za 30 sekund.

Soubory protokolu můžete také zkontrolovat v prohlížeči na `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Chcete-li zastavit streamování protokolů kdykoli, stiskněte klávesu **CTRL** + **C** v terminálu.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Chcete-li spravovat aplikaci, kterou jste vytvořili, otevřete <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Vyhledejte a vyberte **App Services** .

![Přejděte na App Services v Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Vyberte název aplikace Azure.

![Přejděte do aplikace Python v App Services Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Výběr aplikace otevře svou stránku **Přehled** , kde můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění.

![Správa aplikace v Pythonu na stránce s přehledem v Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

V nabídce App Service najdete různé stránky pro konfiguraci vaší aplikace.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. V závislosti na vašem umístění má skupina prostředků název, jako je například "appsvc_rg_Linux_CentralUS". Pokud použijete App Service SKU jinou než bezplatnou úroveň F1, tyto prostředky účtují průběžné náklady (viz [ceny App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků spuštěním následujícího příkazu:

```azurecli
az group delete --no-wait
```

Příkaz používá název skupiny prostředků uložený v souboru *. Azure/config* .

`--no-wait`Argument umožňuje příkazu vrátit se před dokončením operace.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: webová aplikace Python (Django) s PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](configure-language-python.md)

> [!div class="nextstepaction"]
> [Přidání přihlášení uživatele do webové aplikace v Pythonu](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Kurz: spuštění aplikace v Pythonu ve vlastním kontejneru](tutorial-custom-container.md)
