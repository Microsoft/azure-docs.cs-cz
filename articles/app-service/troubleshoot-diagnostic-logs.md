---
title: Povolení diagnostického protokolování
description: Zjistěte, jak povolit protokolování diagnostiky a přidat instrumentaci do vaší aplikace a jak získat přístup k informacím protokolovaných v Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: 433f8fa36f17f7cb145261273586a684658acda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280466"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Povolení protokolování diagnostiky aplikací ve službě Azure App Service
## <a name="overview"></a>Přehled
Azure poskytuje integrovanou diagnostiku, která vám pomůže s laděním [aplikace App Service](overview.md). V tomto článku se dozvíte, jak povolit protokolování diagnostiky a přidat instrumentaci do vaší aplikace, stejně jako jak získat přístup k informacím protokolovaných Azure.

Tento článek používá [portál Azure](https://portal.azure.com) a Azure CLI pro práci s diagnostickými protokoly. Informace o práci s diagnostickými protokoly pomocí Sady Visual Studio najdete [v tématu Poradce při potížích s Azure v sadě Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Kromě protokolování pokyny v tomto článku je tu nové, integrované funkce protokolování s Azure Monitoring. Další informace o této funkci najdete v části [Odeslat protokoly do Azure Monitoru (preview).](#send-logs-to-azure-monitor-preview) 
>
>

|Typ|Platforma|Umístění|Popis|
|-|-|-|-|
| Protokolování aplikací | Windows, Linux | Souborový systém služby App Service nebo objekty BLOB úložiště Azure | Zaprotokoluje zprávy generované kódem aplikace. Zprávy mohou být generovány webovým rámcem, který zvolíte, nebo z kódu aplikace přímo pomocí standardního vzoru protokolování vašeho jazyka. Každé zprávě je přiřazena jedna z následujících kategorií: **Kritická**, **Chyba**, **Upozornění**, **Informace**, **Ladění**a **Trasování**. Můžete vybrat, jak podrobné má být protokolování, nastavením úrovně závažnosti při povolení protokolování aplikací.|
| Protokolování webového serveru| Windows | Souborový systém služby App Service nebo objekty BLOB úložiště Azure| Nezpracovaná data požadavků HTTP ve [formátu souboru rozšířeného protokolu W3C](/windows/desktop/Http/w3c-logging). Každá zpráva protokolu obsahuje data, jako je například metoda HTTP, identifikátor URI prostředku, IP klient, port klienta, uživatelský agent, kód odpovědi a tak dále. |
| Podrobné chybové zprávy| Windows | Systém souborů služby App Service | Kopie chybových stránek *HTM,* které by byly odeslány do prohlížeče klienta. Z bezpečnostních důvodů by neměly být klientům v produkčním prostředí odesílány podrobné chybové stránky, ale služba App Service může chybovou stránku uložit pokaždé, když dojde k chybě aplikace, která má kód HTTP 400 nebo vyšší. Stránka může obsahovat informace, které mohou pomoci určit, proč server vrací kód chyby. |
| Trasování neúspěšných požadavků | Windows | Systém souborů služby App Service | Podrobné informace o trasování neúspěšných požadavků, včetně trasování součástí služby IIS použitých ke zpracování požadavku a času v jednotlivých součástech. Je to užitečné, pokud chcete zlepšit výkon webu nebo izolovat konkrétní chybu PROTOKOLU HTTP. Pro každý neúspěšný požadavek, který obsahuje soubor protokolu XML, je generována jedna složka a šablona stylů XSL, pomocí které lze soubor protokolu zobrazit. |
| Protokolování nasazení | Windows, Linux | Systém souborů služby App Service | Protokoly při publikování obsahu do aplikace. Protokolování nasazení probíhá automaticky a neexistují žádná konfigurovatelná nastavení pro protokolování nasazení. Pomáhá určit, proč se nasazení nezdařilo. Pokud například použijete [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), můžete pomocí protokolování nasazení určit, proč skript selhává. |

> [!NOTE]
> Služba App Service poskytuje vyhrazený interaktivní diagnostický nástroj, který vám pomůže vyřešit potíže s aplikací. Další informace najdete v [tématu Přehled diagnostiky služby Azure App Service](overview-diagnostics.md).
>
> Kromě toho můžete použít další služby Azure ke zlepšení možností protokolování a monitorování vaší aplikace, jako je [Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Povolení protokolování aplikací (Windows)

Pokud chcete povolit protokolování aplikací pro aplikace pro Windows na [webu Azure Portal](https://portal.azure.com), přejděte do aplikace a vyberte **protokoly služby App Service**.

Vyberte **Zapnuto** pro **protokolování aplikací (souborový systém)** nebo **protokolování aplikací (Blob)** nebo obojí. 

Možnost **Souborový systém** je pro účely dočasného ladění a sama se vypne za 12 hodin. Možnost **objektu blob** je pro dlouhodobé protokolování a potřebuje kontejner úložiště objektů blob pro zápis protokolů.  Možnost **objektu blob** také obsahuje další informace ve zprávách protokolu, jako je například ID původní instance virtuálního virtuálního zařízení zprávy protokolu (`InstanceId`), ID vlákna (`Tid`) a podrobnější časové razítko ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> V současné době lze do úložiště objektů blob zapisovat jenom protokoly aplikací .NET. Java, PHP, Node.js, protokoly aplikací Pythonu mohou být uloženy pouze v systému souborů App Service (bez úprav kódu pro zápis protokolů do externího úložiště).
>
> Také pokud [znovu vygenerujete přístupové klíče účtu úložiště](../storage/common/storage-create-storage-account.md), je nutné obnovit příslušnou konfiguraci protokolování, aby bylo nutné používat aktualizované přístupové klíče. Použijte následující postup:
>
> 1. Na kartě **Konfigurovat** nastavte příslušnou funkci protokolování na **vypnuto**. Uložte nastavení.
> 2. Povolte protokolování k objektu blob účtu úložiště znovu. Uložte nastavení.
>
>

Vyberte **úroveň**nebo úroveň podrobností, které chcete protokolovat. V následující tabulce jsou uvedeny kategorie protokolů zahrnuté v každé úrovni:

| Úroveň | Zahrnuté kategorie |
|-|-|
|**Disabled** (Zakázáno) | Žádný |
|**Chyba** | Chyba, kritická |
|**Upozornění** | Varování, Chyba, Kritická|
|**Informace** | Informace, Varování, Chyba, Kritická|
|**Podrobné** | Trasování, Ladění, Informace, Upozornění, Chyba, Kritické (všechny kategorie) |

Po dokončení vyberte **Uložit**.

## <a name="enable-application-logging-linuxcontainer"></a>Povolení protokolování aplikací (Linux/Container)

Pokud chcete povolit protokolování aplikací pro linuxové aplikace nebo vlastní kontejnerové aplikace na [webu Azure Portal](https://portal.azure.com), přejděte do aplikace a vyberte **protokoly služby App Service**.

V **protokolování aplikací**vyberte **možnost Systém souborů**.

V **části Kvóta (MB)** zadejte diskovou kvótu pro protokoly aplikací. V **období uchovávání (dny)** nastavte počet dní, po které mají být protokoly uchovávány.

Po dokončení vyberte **Uložit**.

## <a name="enable-web-server-logging"></a>Povolení protokolování webového serveru

Pokud chcete povolit protokolování webového serveru pro aplikace pro Windows na [webu Azure Portal](https://portal.azure.com), přejděte do aplikace a vyberte **protokoly služby App Service**.

Pro **protokolování webového serveru**vyberte **úložiště** pro ukládání protokolů v úložišti objektů blob nebo **systém souborů** pro ukládání protokolů v systému souborů služby App Service. 

V **období uchovávání (dny)** nastavte počet dní, po které mají být protokoly uchovávány.

> [!NOTE]
> Pokud [znovu vygenerujete přístupové klíče účtu úložiště](../storage/common/storage-create-storage-account.md), je nutné obnovit příslušnou konfiguraci protokolování, aby bylo nutné používat aktualizované klíče. Použijte následující postup:
>
> 1. Na kartě **Konfigurovat** nastavte příslušnou funkci protokolování na **vypnuto**. Uložte nastavení.
> 2. Povolte protokolování k objektu blob účtu úložiště znovu. Uložte nastavení.
>
>

Po dokončení vyberte **Uložit**.

## <a name="log-detailed-errors"></a>Protokolovat podrobné chyby

Pokud chcete chybovou stránku nebo trasování neúspěšných požadavků pro aplikace pro Windows uložit na [webu Azure Portal](https://portal.azure.com), přejděte do aplikace a vyberte **protokoly služby App Service**.

V části **Podrobné protokolování chyb** nebo **Trasování neúspěšných požadavků**vyberte Možnost **Zapnuto**a pak vyberte **Uložit**.

Oba typy protokolů jsou uloženy v systému souborů služby App Service. Je zachováno až 50 chyb (souborů/složek). Pokud počet souborů HTML překročí 50, nejstarší 26 chyb se automaticky odstraní.

## <a name="add-log-messages-in-code"></a>Přidání zpráv protokolu do kódu

V kódu aplikace použijete obvyklé možnosti protokolování k odesílání zpráv protokolu do protokolů aplikace. Například:

- ASP.NET aplikace mohou použít třídu [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) k protokolování informací do protokolu diagnostiky aplikací. Například:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Ve výchozím nastavení používá ASP.NET Core poskytovatele protokolování [Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Další informace najdete [v tématu ASP.NET protokolování jádra v Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Protokoly datových proudů

Před streamováním protokolů v reálném čase povolte požadovaný typ protokolu. Veškeré informace zapsané do souborů končících na .txt, .log nebo .htm, které jsou uloženy v adresáři */LogFiles* (d:/home/logfiles), jsou přenášeny datovým proudem službou App Service.

> [!NOTE]
> Některé typy vyrovnávací paměti protokolování zapisují do souboru protokolu, což může mít za následek mimopořadí událostí v datovém proudu. Například položka protokolu aplikace, ke které dochází, když uživatel navštíví stránku, může být zobrazena v datovém proudu před odpovídající položkou protokolu HTTP pro požadavek na stránku.
>

### <a name="in-azure-portal"></a>Na portálu Azure

Pokud chcete streamovat protokoly na [webu Azure Portal](https://portal.azure.com), přejděte do aplikace a vyberte Log **stream**. 

### <a name="in-cloud-shell"></a>V prostředí Cloud Shell

Chcete-li streamovat protokoly živě v [prostředí Cloud Shell](../cloud-shell/overview.md), použijte následující příkaz:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Chcete-li filtrovat konkrétní události, například chyby, použijte parametr **--Filter.** Například:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Chcete-li filtrovat určité typy protokolů, například HTTP, použijte parametr **--Path.** Například:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>V místním terminálu

Pokud chcete streamovat protokoly v místní konzoli, [nainstalujte azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) a [přihlaste se ke svému účtu](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Po přihlášení se řidit podle [pokynů pro Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Přístup k souborům protokolu

Pokud nakonfigurujete možnost objektů BLOB azure storage pro typ protokolu, budete potřebovat klientský nástroj, který pracuje s Azure Storage. Další informace najdete v tématu [Nástroje klienta úložiště Azure](../storage/common/storage-explorers.md).

Pro protokoly uložené v systému souborů Služby Aplikace je nejjednodušší stáhnout soubor ZIP v prohlížeči na adrese:

- Linux/kontejnerové aplikace:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplikace pro Windows:`https://<app-name>.scm.azurewebsites.net/api/dump`

Pro linuxové/kontejnerové aplikace obsahuje soubor ZIP výstupní protokoly konzoly pro hostitele dockeru i pro kontejner dockeru. Pro aplikaci s horizontálním navýšením kapacity obsahuje soubor ZIP jednu sadu protokolů pro každou instanci. V systému souborů Služby App Service jsou tyto soubory protokolu obsahem adresáře */home/LogFiles.*

U aplikací pro Windows obsahuje soubor ZIP obsah adresáře *D:\Home\LogFiles* v systému souborů Služby App Service. Má následující strukturu:

| Typ protokolu | Adresář | Popis |
|-|-|-|
| **Protokoly aplikací** |*/LogFiles/Aplikace/* | Obsahuje jeden nebo více textových souborů. Formát zpráv protokolu závisí na poskytovateli protokolování, který používáte. |
| **Trasování neúspěšných požadavků** | */LogFiles/W3SVC#########/* | Obsahuje soubory XML a soubor XSL. Formátované soubory XML můžete zobrazit v prohlížeči. |
| **Podrobné protokoly chyb** | */LogFiles/DetailedErrors/* | Obsahuje soubory chyb HTM. Soubory HTM můžete zobrazit v prohlížeči.<br/>Dalším způsobem, jak zobrazit trasování neúspěšných požadavků, je přejít na stránku aplikace na portálu. V levé nabídce vyberte **Diagnostikovat a řešit problémy**, vyhledejte **protokoly trasování neúspěšných požadavků**a potom klepnutím na ikonu procházejte a zobrazte požadované trasování. |
| **Protokoly webového serveru** | */LogFiles/http/RawLogs/* | Obsahuje textové soubory formátované pomocí [formátu rozšířeného souboru protokolu W3C](/windows/desktop/Http/w3c-logging). Tyto informace lze číst pomocí textového editoru nebo nástroje, jako je [Analyzátor protokolů](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>Služba App Service nepodporuje `s-ip`pole `cs-version` `s-computername`, nebo. |
| **Protokoly nasazení** | */LogFiles/Git/* a */deployments/* | Obsahují protokoly generované interními procesy nasazení a protokoly pro nasazení Gitu. |

## <a name="send-logs-to-azure-monitor-preview"></a>Odeslání protokolů do Služby Azure Monitor (preview)

S novou [integrací Azure Monitoru](https://aka.ms/appsvcblog-azmon)můžete [vytvořit diagnostická nastavení (preview)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) a odesílat protokoly do účtů úložiště, centra událostí a analýzy protokolů. 

> [!div class="mx-imgBorder"]
> ![Nastavení diagnostiky (náhled)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Podporované typy protokolů

V následující tabulce jsou uvedeny podporované typy protokolů a popisy: 

| Typ protokolu | Podpora systému Windows | Podpora pro Linux (Docker) | Popis |
|-|-|-|
| AppServiceConsoleLogs | Tba | Ano | Standardní výstup a standardní chyba |
| Protokoly HTTPSlužby AppService | Ano | Ano | Protokoly webového serveru |
| AppServiceEnvironmentPlatformLogs | Ano | Ano | Prostředí služby App Service: škálování, změny konfigurace a protokoly stavu|
| AppServiceAuditLogs | Ano | Ano | Přihlašovací aktivita přes FTP a Kudu |
| AppServiceFileAuditLogs | Tba | Ano | Změny souborů přes FTP a Kudu |
| AppServiceAppLogs | Tba | Java SE & Tomcat | Protokoly aplikací |

## <a name="next-steps"></a><a name="nextsteps"></a>Další kroky
* [Protokoly dotazů pomocí Azure Monitoru](../azure-monitor/log-query/log-query-overview.md)
* [Jak monitorovat službu Azure App Service](web-sites-monitor.md)
* [Poradce při potížích s azure app service ve Visual Studiu](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsightu](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
