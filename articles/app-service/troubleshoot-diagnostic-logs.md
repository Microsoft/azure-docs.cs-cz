---
title: Povolení diagnostického protokolování
description: Naučte se, jak povolit diagnostické protokolování a přidat instrumentaci do vaší aplikace, a jak získat přístup k informacím protokolovaným v Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
ms.openlocfilehash: 99a3c9a9c26eebe8dfdf11baf718fd13f7539607
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025272"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Povolit protokolování diagnostiky pro aplikace v Azure App Service
## <a name="overview"></a>Přehled
Azure poskytuje integrovanou diagnostiku, která vám pomůže s laděním [aplikace App Service](overview.md). V tomto článku se dozvíte, jak povolit diagnostické protokolování a přidat instrumentaci do aplikace, a jak získat přístup k informacím protokolovaným v Azure.

Tento článek používá [Azure Portal](https://portal.azure.com) a Azure CLI pro práci s diagnostickými protokoly. Informace o práci s diagnostickými protokoly pomocí sady Visual Studio najdete v tématu [řešení potíží s Azure v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Kromě pokynů k protokolování v tomto článku je k dispozici nová integrovaná funkce protokolování s monitorováním Azure. Další informace o této funkci najdete v části [odeslání protokolů do Azure monitor (Preview)](#send-logs-to-azure-monitor-preview) . 
>
>

|Typ|Platforma|Umístění|Popis|
|-|-|-|-|
| Protokolování aplikací | Windows, Linux | App Service objekty blob systému souborů nebo Azure Storage | Protokoluje zprávy vygenerované kódem vaší aplikace. Zprávy mohou být generovány webovým rozhraním, které si zvolíte, nebo z kódu vaší aplikace přímo pomocí standardního vzoru protokolování vašeho jazyka. Každé zprávě je přiřazena jedna z následujících kategorií: **kritická**, **Chyba**, **Upozornění**, **informace**, **ladění** a **trasování**. Pokud povolíte protokolování aplikací, můžete vybrat, jak chcete, aby protokolování bylo, nastavením úrovně závažnosti.|
| Protokolování webového serveru| Windows | App Service nebo objekty blob Azure Storage systému souborů| Nezpracovaná data požadavku HTTP ve [formátu W3C Extended](/windows/desktop/Http/w3c-logging). Každá zpráva protokolu obsahuje data, jako je například metoda HTTP, identifikátor URI prostředku, IP adresa klienta, port klienta, uživatelský agent, kód odpovědi atd. |
| Podrobné chybové zprávy| Windows | App Service systému souborů | Kopie chybových stránek *. htm* , které byly odeslány do prohlížeče klienta. Z bezpečnostních důvodů by podrobné chybové stránky neměly být odesílány klientům v produkčním prostředí, ale App Service mohou tuto chybovou stránku uložit pokaždé, když dojde k chybě aplikace, která má kód HTTP 400 nebo vyšší. Stránka může obsahovat informace, které vám pomohou určit, proč Server vrací kód chyby. |
| Trasování chybných požadavků | Windows | App Service systému souborů | Podrobné informace o trasování o neúspěšných žádostech, včetně trasování komponent služby IIS použitých ke zpracování žádosti a času provedeného v každé součásti. To je užitečné, pokud chcete zlepšit výkon webu nebo izolovat konkrétní chybu protokolu HTTP. Jedna složka je vygenerována pro každou neúspěšnou žádost, která obsahuje soubor protokolu XML, a šablonu stylů XSL pro zobrazení souboru protokolu s. |
| Protokolování nasazení | Windows, Linux | App Service systému souborů | Protokoluje se při publikování obsahu do aplikace. Protokolování nasazení probíhá automaticky a není k dispozici žádné konfigurovatelné nastavení pro protokolování nasazení. Pomůže vám určit, proč nasazení selhalo. Pokud například použijete [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), můžete pomocí protokolování nasazení zjistit, proč se skript nedaří. |

> [!NOTE]
> App Service poskytuje vyhrazený interaktivní nástroj pro diagnostiku, který vám může pomoct při odstraňování potíží s aplikací. Další informace najdete v tématu [Přehled diagnostiky Azure App Service](overview-diagnostics.md).
>
> Kromě toho můžete pomocí dalších služeb Azure zlepšit možnosti protokolování a monitorování vaší aplikace, například [Azure monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Povolit protokolování aplikace (Windows)

> [!NOTE]
> Protokolování aplikace pro úložiště objektů BLOB může používat jenom účty úložiště ve stejné oblasti, jako je App Service

Pokud chcete povolit protokolování aplikací pro aplikace pro Windows v [Azure Portal](https://portal.azure.com), přejděte do svojí aplikace a vyberte **protokoly App Service**.

Vyberte možnost **zapnuto** buď pro **protokolování aplikace (systém souborů)** , nebo **protokolování aplikace (BLOB)**, nebo pro obojí. 

Možnost **systému souborů** je určena pro dočasné účely ladění a sama se odpíná za 12 hodin. Možnost **BLOB** je určena pro dlouhodobé protokolování a potřebuje kontejner úložiště objektů BLOB k zápisu protokolů do.  Možnost **BLOB** obsahuje taky další informace v protokolových zprávách, jako je ID instance virtuálního počítače v protokolu ( `InstanceId` ), ID vlákna ( `Tid` ) a podrobnější časové razítko ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Do úložiště objektů BLOB se teď dají zapisovat jenom protokoly aplikací .NET. Protokoly aplikací v jazyce Java, PHP, Node.js a Python lze ukládat pouze do systému souborů App Service (bez úprav kódu pro zápis protokolů do externího úložiště).
>
> Pokud navíc [znovu vygenerujete přístupové klíče účtu úložiště](../storage/common/storage-account-create.md), musíte resetovat příslušnou konfiguraci protokolování tak, aby používala aktualizované přístupové klíče. Použijte následující postup:
>
> 1. Na kartě **Konfigurovat** nastavte příslušnou funkci protokolování na **vypnuto**. Uložte nastavení.
> 2. Znovu povolte protokolování do objektu BLOB účtu úložiště. Uložte nastavení.
>
>

Vyberte **úroveň** nebo úroveň podrobností, které se mají protokolovat. V následující tabulce jsou uvedeny kategorie protokolů, které jsou součástí jednotlivých úrovní:

| Úroveň | Zahrnuté kategorie |
|-|-|
|**Zakázáno** | Žádné |
|**Chyba** | Chyba, kritická |
|**Upozornění** | Upozornění, chyba, kritická|
|**Informace** | Informace, varování, chyba, kritické|
|**Podrobné** | Trasování, ladění, informace, varování, chyba, kritická (všechny kategorie) |

Po dokončení vyberte **Uložit**.

## <a name="enable-application-logging-linuxcontainer"></a>Povolit protokolování aplikace (Linux/kontejner)

Pokud chcete povolit protokolování aplikací pro aplikace pro Linux nebo vlastní kontejnerové aplikace v [Azure Portal](https://portal.azure.com), přejděte do vaší aplikace a vyberte **protokoly App Service**.

V **protokolování aplikace** vyberte možnost **systém souborů**.

V části **kvóta (MB)** zadejte diskovou kvótu pro protokoly aplikací. V části **Doba uchování (dny)** nastavte počet dní, po které se mají protokoly uchovávat.

Po dokončení vyberte **Uložit**.

## <a name="enable-web-server-logging"></a>Povolit protokolování webového serveru

Pokud chcete povolit protokolování webových serverů pro aplikace pro Windows v [Azure Portal](https://portal.azure.com), přejděte do svojí aplikace a vyberte **protokoly App Service**.

Pro **protokolování webového serveru** vyberte **úložiště** pro ukládání protokolů v úložišti objektů BLOB nebo do **systému souborů** pro ukládání protokolů v App Service systému souborů. 

V části **Doba uchování (dny)** nastavte počet dní, po které se mají protokoly uchovávat.

> [!NOTE]
> Pokud [znovu vygenerujete přístupové klíče účtu úložiště](../storage/common/storage-account-create.md), musíte obnovit příslušnou konfiguraci protokolování tak, aby používala aktualizované klíče. Použijte následující postup:
>
> 1. Na kartě **Konfigurovat** nastavte příslušnou funkci protokolování na **vypnuto**. Uložte nastavení.
> 2. Znovu povolte protokolování do objektu BLOB účtu úložiště. Uložte nastavení.
>
>

Po dokončení vyberte **Uložit**.

## <a name="log-detailed-errors"></a>Podrobné protokolování chyb

Pokud chcete uložit chybovou stránku nebo trasování chybných požadavků pro aplikace pro Windows v [Azure Portal](https://portal.azure.com), přejděte do svojí aplikace a vyberte **App Service protokoly**.

V části **podrobné protokolování chyb** nebo **trasování neúspěšných požadavků** vyberte **zapnuto** a pak vyberte **Uložit**.

Oba typy protokolů jsou uloženy v App Service systému souborů. Zachovají se až 50 chyb (soubory/složky). Když počet souborů HTML překračuje 50, automaticky se odstraní nejstarší 26 chyb.

## <a name="add-log-messages-in-code"></a>Přidat protokolové zprávy v kódu

V kódu aplikace používáte obvyklá Protokolovací zařízení k posílání zpráv protokolu do protokolů aplikací. Například:

- ASP.NET aplikace mohou použít třídu [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) k protokolování informací do protokolu nástroje Application Diagnostics. Například:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Ve výchozím nastavení používá ASP.NET Core poskytovatele protokolování [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Další informace najdete v tématu [ASP.NET Core protokolování v Azure](/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Protokoly datových proudů

Než začnete streamovat protokoly v reálném čase, povolte typ protokolu, který chcete. Všechny informace zapsané do souborů, které končí. txt,. log nebo. htm uložené v adresáři */Logfiles* (d:/Home/LogFiles), jsou streamované pomocí App Service.

> [!NOTE]
> Některé typy vyrovnávací paměti pro protokolování zapisují do souboru protokolu, což může vést k vyřazení událostí mimo pořadí v datovém proudu. Například položka aplikačního protokolu, která se zobrazí, když uživatel navštíví stránku, se může zobrazit v datovém proudu předtím, než odpovídající položka protokolu HTTP pro požadavek na stránku.
>

### <a name="in-azure-portal"></a>V Azure Portal

Pokud chcete streamovat protokoly v [Azure Portal](https://portal.azure.com), přejděte do svojí aplikace a vyberte **log Stream**. 

### <a name="in-cloud-shell"></a>V Cloud Shell

Pokud chcete streamovat živé vysílání v [Cloud Shell](../cloud-shell/overview.md), použijte následující příkaz:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Chcete-li filtrovat konkrétní události, jako jsou například chyby, použijte parametr **--Filter** . Například:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Chcete-li filtrovat konkrétní typy protokolů, jako je například HTTP, použijte parametr **--path** . Například:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>V místním terminálu

Pokud chcete streamovat protokoly v místní konzole, nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [Přihlaste se ke svému účtu](/cli/azure/authenticate-azure-cli). Po přihlášení za ním následují [pokyny Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Přístup k souborům protokolu

Pokud nakonfigurujete možnost Azure Storage objekty blob pro typ protokolu, budete potřebovat klientský nástroj, který pracuje s Azure Storage. Další informace najdete v tématu [Azure Storage klientských nástrojů](../storage/common/storage-explorers.md).

V případě protokolů uložených v App Serviceovém systému souborů nejjednodušší způsob, jak stáhnout soubor ZIP v prohlížeči v:

- Aplikace pro Linux/kontejner: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplikace pro Windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

Pro aplikace Linux/kontejner obsahuje soubor ZIP protokoly výstupu konzoly hostitele Docker i kontejneru Docker. Pro aplikaci s horizontálním rozšířením souboru ZIP obsahuje jednu sadu protokolů pro každou instanci. V App Service systému souborů jsou tyto soubory protokolu obsahem adresáře */Home/LogFiles* .

V případě aplikací pro Windows soubor ZIP obsahuje obsah adresáře *D:\Home\LogFiles* v systému souborů App Service. Má následující strukturu:

| Typ protokolu | Adresář | Popis |
|-|-|-|
| **Protokoly aplikací** |*/LogFiles/Application/* | Obsahuje jeden nebo více textových souborů. Formát zpráv protokolu závisí na poskytovateli protokolování, který používáte. |
| **Trasování chybných žádostí** | */LogFiles/W3SVC#########/* | Obsahuje soubory XML a soubor XSL. Formátované soubory XML můžete zobrazit v prohlížeči. |
| **Podrobné protokoly chyb** | */LogFiles/DetailedErrors/* | Obsahuje soubory chyb HTM. Soubory HTM můžete zobrazit v prohlížeči.<br/>Dalším způsobem, jak zobrazit trasování chybných požadavků, je přejít na stránku aplikace na portálu. V nabídce vlevo vyberte **diagnostikovat a řešit problémy**, vyhledejte **protokoly pro trasování chybných požadavků** a potom klikněte na ikonu pro procházení a zobrazení požadovaného trasování. |
| **Protokoly webového serveru** | */LogFiles/http/RawLogs/* | Obsahuje textové soubory formátované pomocí [rozšířeného formátu souboru protokolu W3C](/windows/desktop/Http/w3c-logging). Tyto informace lze číst pomocí textového editoru nebo nástroje, jako je například [analyzátor protokolů](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>App Service nepodporuje `s-computername` `s-ip` pole,, ani `cs-version` . |
| **Protokoly nasazení** | */Logfiles/Git/* a */Deployments/* | Obsahují protokoly generované interními procesy nasazení a protokoly pro nasazení Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Odeslat protokoly do Azure Monitor (Preview)

S novou [integrací Azure monitor](https://aka.ms/appsvcblog-azmon)můžete [vytvořit nastavení diagnostiky (Preview)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) k odesílání protokolů do účtů úložiště Event Hubs a Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Nastavení diagnostiky (Preview)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Podporované typy protokolů

Následující tabulka uvádí podporované typy a popisy protokolů: 

| Typ protokolu | Windows | Kontejner Windows | Linux | Kontejner pro Linux | Popis |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Yes | Yes | Yes | Standardní výstup a standardní chyba |
| AppServiceHTTPLogs | Yes | Yes | Yes | Yes | Protokoly webového serveru |
| AppServiceEnvironmentPlatformLogs | Yes | Není k dispozici | Ano | Yes | App Service Environment: škálování, změny konfigurace a protokoly stavu|
| AppServiceAuditLogs | Yes | Yes | Yes | Yes | Přihlašovací aktivita prostřednictvím FTP a Kudu |
| AppServiceFileAuditLogs | Yes | Yes | TBA | TBA | Změny souborů provedené v obsahu webu; dostupné jenom pro úroveň Premium a vyšší |
| AppServiceAppLogs | ASP .NET | ASP .NET | Java SE & obrázky s Tomcat, na kterých se nachází <sup>1</sup> | Java SE & obrázky s Tomcat, na kterých se nachází <sup>1</sup> | Protokoly aplikací |
| AppServiceIPSecAuditLogs  | Yes | Yes | Yes | Yes | Žádosti z pravidel IP adres |
| AppServicePlatformLogs  | TBA | Yes | Yes | Yes | Protokoly operací kontejneru |

<sup>1</sup> pro aplikace Java se do nastavení aplikace přidejte $website _AZMON_PREVIEW_ENABLED a nastavte ji na 1 nebo true.

## <a name="next-steps"></a><a name="nextsteps"></a> Další kroky
* [Dotazování protokolů pomocí Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Jak monitorovat Azure App Service](web-sites-monitor.md)
* [Řešení potíží s Azure App Service v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
