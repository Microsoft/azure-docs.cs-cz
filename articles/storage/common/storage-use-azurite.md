---
title: Použití emulátoru Azurite pro místní vývoj úložiště Azure
description: Emulátor azuritového open source (preview) poskytuje bezplatné místní prostředí pro testování aplikací úložiště Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029921"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Použití emulátoru Azurite pro vývoj a testování místního úložiště Azure (preview)

Emulátor azuritverze 3.2 s otevřeným zdrojovým kódem (preview) poskytuje bezplatné místní prostředí pro testování aplikací úložiště objektů blob azure a fronty. Až budete spokojeni s tím, jak vaše aplikace funguje místně, přepněte na používání účtu Azure Storage v cloudu. Emulátor poskytuje podporu napříč platformami ve Windows, Linuxu a MacOS. Azurite v3 podporuje api implementovaná službou Azure Blob.

Azurite je budoucí platforma pro emulátor úložiště. Azurit nahrazuje [emulátor úložiště Azure](storage-use-emulator.md). Azurite bude i nadále aktualizován, aby podporoval nejnovější verze azure storage API.

Existuje několik různých způsobů instalace a spuštění azuritu v místním systému:

  1. [Instalace a spuštění rozšíření azurového kódu Visual Studio](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalace a spuštění azuritu pomocí npm](#install-and-run-azurite-by-using-npm)
  1. [Instalace a spuštění bitové kopie Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Klonování, vytváření a spouštění Azurite z úložiště GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalace a spuštění rozšíření azurového kódu Visual Studio

V rámci kódu sady Visual Studio vyberte podokno **ROZŠÍŘENÍ** a vyhledejte *azurit* v **rozšíření:MARKETPLACE**.

![Tržiště rozšíření kódu sady Visual Studio](media/storage-use-azurite/azurite-vs-code-extension.png)

Případně přejděte na [trh rozšíření VS Code](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) ve vašem prohlížeči. Kliknutím na tlačítko **Instalovat** otevřete kód Sady Visual Studio a přejděte přímo na stránku rozšíření Azurit.

Azurit můžete rychle spustit nebo zavřít kliknutím na **[Azurite Blob Service]** nebo **[Azurite Queue Service]** ve stavovém řádku kódu VS nebo vydáním následujících příkazů v paletě příkazů Kódu VS. Chcete-li otevřít paletu příkazů, stiskněte **klávesu F1** v části Kód VS.

Rozšíření podporuje následující příkazy visual studio kód:

   * **Azurit: Start** - Spusťte všechny služby Azurit
   * **Azurite: Zavřít** - Zavřít všechny služby Azurit
   * **Azurite: Clean** - Reset všechna data trvalosti služeb Azurite
   * **Azurit: Spustit službu blob** – spustit službu objektů blob
   * **Azurite: Zavřít službu blob** – zavřít službu objektů blob
   * **Azurite: Čistá služba blob –** služba Čistý objekt blob
   * **Azurit: Spustit službu fronty** – spustit službu fronty
   * **Azurit: Zavřít službu fronty** - Zavřít službu fronty
   * **Azurite: Služba čisté fronty** – služba čisté fronty

Chcete-li nakonfigurovat Azurit v rámci kódu sady Visual Studio, vyberte podokno rozšíření. Vyberte ikonu **Spravovat** (ozubené kolo) pro **Azurit**. Vyberte **Konfigurovat nastavení rozšíření**.

![Konfigurace nastavení rozšíření Azurite](media/storage-use-azurite/azurite-configure-extension-settings.png)

Jsou podporována následující nastavení:

   * **Azurit: Objekt blob Host** - koncový bod naslouchání služby Blob. Výchozí nastavení je 127.0.0.1.
   * **Azurit: Blob Port** - Port pro poslech služby Blob. Výchozí port je 10000.
   * **Azurit: Ladění** - Výstup protokolu ladění do kanálu Azurit. Výchozí hodnota je **false** (nepravda).
   * **Azurit: Umístění** – cesta umístění pracovního prostoru. Výchozí je pracovní složka Kód sady Visual Studio.
   * **Azurit: Hostitel fronty** – koncový bod naslouchání službě Fronta. Výchozí nastavení je 127.0.0.1.
   * **Azurit: Port fronty** – naslouchací port služby Fronta. Výchozí port je 10001.
   * **Azurit: Tichý** - Tichý režim zakáže přístupový protokol. Výchozí hodnota je **false** (nepravda).

## <a name="install-and-run-azurite-by-using-npm"></a>Instalace a spuštění azuritu pomocí npm

Tato metoda instalace vyžaduje, abyste měli nainstalován [soubor Node.js verze 8.0 nebo novější.](https://nodejs.org) **npm** je nástroj pro správu balíčků, který je součástí každé instalace node.js. Po instalaci souboru Node.js spusťte následující příkaz **npm** k instalaci azuritu.

```console
npm install -g azurite
```

Po instalaci azuritu viz [Spuštění azuritu z příkazového řádku](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalace a spuštění bitové kopie Azurite Docker

Pomocí [DockerHubu](https://hub.docker.com/) můžete vytáhnout [nejnovější bitovou kopii Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) pomocí následujícího příkazu:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Spusťte obrázek Azurite Docker**:

Následující příkaz spustí image Azurite Docker. Parametr `-p 10000:10000` přesměruje požadavky z portu hostitelského počítače 10000 na instanci Dockeru.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Zadejte umístění pracovního prostoru**:

V následujícím příkladu `-v c:/azurite:/data` parametr určuje *c:/azurit* jako umístění trvalých dat Azurite. Adresář *c:/azurit*musí být vytvořen před spuštěním příkazu Dockeru.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Spuštění pouze služby objektů blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Nastavte všechny parametry Azuritu**:

Tento příklad ukazuje, jak nastavit všechny parametry příkazového řádku. Všechny níže uvedené parametry by měly být umístěny na jednom příkazovém řádku.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Další informace o konfiguraci azuritu při spuštění najdete v tématu [Možnosti příkazového řádku.](#command-line-options)

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klonování, vytváření a spouštění Azurite z úložiště GitHub

Tato metoda instalace vyžaduje, abyste měli nainstalovaný [Git.](https://git-scm.com/) Klonujte [úložiště GitHub](https://github.com/azure/azurite) pro projekt Azurit pomocí následujícího příkazu konzoly.

```console
git clone https://github.com/Azure/Azurite.git
```

Po klonování zdrojového kódu spusťte následující příkazy z kořenového adresáře klonovaného repo k sestavení a instalaci Azurite.

```console
npm install
npm run build
npm install -g
```

Po instalaci a sestavení Azuritu viz [Spustit Azurit z příkazového řádku](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Spuštění azuritu z příkazového řádku

> [!NOTE]
> Azurit nelze spustit z příkazového řádku, pokud jste nainstalovali pouze rozšíření kódu sady Visual Studio. Místo toho použijte paletu příkazů VS Kód. Další informace naleznete v [tématu Instalace a spuštění rozšíření Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Chcete-li začít okamžitě pomocí příkazového řádku, vytvořte adresář s názvem **c:\azurite**, a pak spusťte Azurit vydáním následujícího příkazu:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Tento příkaz říká Azuritovi, aby ukládal všechna data v určitém **adresáři, c:\azurit**. Pokud je možnost **--location** vynechána, použije aktuální pracovní adresář.

## <a name="command-line-options"></a>Možnosti příkazového řádku

Tato část podrobně popisuje přepínače příkazového řádku, které jsou k dispozici při spouštění Azurite. Všechny přepínače příkazového řádku jsou volitelné.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** je zkratka pro **--debug**, **-l** switch je zkratka pro **--location**, **-s** je zkratka pro **--silent**, a **-h** je zkratka pro **--help**.

### <a name="blob-listening-host"></a>Hostitel naslouchání objektu blob

**Nepovinné** Ve výchozím nastavení bude Azurite poslouchat 127.0.0.1 jako místní server. Pomocí přepínače **--blobHost** nastavte adresu podle svých požadavků.

Přijímat požadavky pouze na místním počítači:

```console
azurite --blobHost 127.0.0.1
```

Povolit vzdálené požadavky:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Povolení vzdálených požadavků může způsobit, že váš systém bude zranitelný vůči externím útokům.

### <a name="blob-listening-port-configuration"></a>Konfigurace naslouchacího portu objektu Blob

**Nepovinné** Ve výchozím nastavení bude Azurit poslouchat službu Blob na portu 10000. Pomocí přepínače **--blobPort** určete naslouchací port, který požadujete.

> [!NOTE]
> Po použití přizpůsobeného portu je potřeba aktualizovat připojovací řetězec nebo odpovídající konfiguraci v nástrojích úložiště Azure nebo sadách SDK.

Přizpůsobte si naslouchací port služby Blob:

```console
azurite --blobPort 8888
```

Nechte systém automaticky vybrat dostupný port:

```console
azurite --blobPort 0
```

Port, který se používá, se zobrazí během spuštění azuritu.

### <a name="queue-listening-host"></a>Hostitel naslouchání fronty

**Nepovinné** Ve výchozím nastavení bude Azurite poslouchat 127.0.0.1 jako místní server. Pomocí přepínače **--queueHost** nastavte adresu podle svých požadavků.

Přijímat požadavky pouze na místním počítači:

```console
azurite --queueHost 127.0.0.1
```

Povolit vzdálené požadavky:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Povolení vzdálených požadavků může způsobit, že váš systém bude zranitelný vůči externím útokům.

### <a name="queue-listening-port-configuration"></a>Konfigurace naslouchacího portu fronty

**Nepovinné** Ve výchozím nastavení bude Azurit naslouchat službě Queue na portu 10001. Pomocí přepínače **--queuePort** určete naslouchací port, který požadujete.

> [!NOTE]
> Po použití přizpůsobeného portu je potřeba aktualizovat připojovací řetězec nebo odpovídající konfiguraci v nástrojích úložiště Azure nebo sadách SDK.

Přizpůsobte naslouchací port služby Fronta:

```console
azurite --queuePort 8888
```

Nechte systém automaticky vybrat dostupný port:

```console
azurite --queuePort 0
```

Port, který se používá, se zobrazí během spuštění azuritu.

### <a name="workspace-path"></a>Cesta pracovního prostoru

**Nepovinné** Azurit ukládá data na místní disk během provádění. Pomocí přepínače **--umístění** určete cestu jako umístění pracovního prostoru. Ve výchozím nastavení bude použit aktuální pracovní adresář procesu.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Protokol přístupu

**Nepovinné** Ve výchozím nastavení se protokol přístupu zobrazí v okně konzoly. Zakažte zobrazení protokolu přístupu pomocí **přepínače --silent.**

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Protokol ladění

**Nepovinné** Protokol ladění obsahuje podrobné informace o každém požadavku a trasování zásobníku výjimek. Povolte protokol ladění poskytnutím platné místní cesty k přepínači **--debug.**

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Volný režim

**Nepovinné** Ve výchozím nastavení azurit používá přísný režim pro blokování nepodporovaných hlaviček a parametrů požadavků. Zakažte přísný režim pomocí **přepínače --loose.**

```console
azurite --loose
```

Všimněte si hlavního zkratkového spínače "L":

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autorizace nástrojů a sad SDK

Připojte se k Azuritu z sad Azure Storage SDK nebo nástrojů, jako je [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), pomocí jakékoli strategie ověřování. Je vyžadováno ověření. Azurite podporuje autorizaci pomocí sdíleného klíče a sdílených přístupových podpisů (SAS). Azurite také podporuje anonymní přístup k veřejným kontejnerům.

### <a name="well-known-storage-account-and-key"></a>Známý účet úložiště a klíč

Můžete použít následující název účtu a klíč s Azurite. Jedná se o stejný známý účet a klíč používaný starším emulátorem úložiště Azure.

* Název účtu:`devstoreaccount1`
* Klíč účtu:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Kromě ověřování Sdílených klíčů podporuje Azurite ověřování SAS účtu a služby. Anonymní přístup je k dispozici také v případě, že je kontejner nastaven tak, aby umožňoval veřejný přístup.

### <a name="connection-string"></a>Připojovací řetězec

Nejjednodušší způsob, jak se připojit k Azuritu z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru aplikace, který odkazuje na zástupce *UseDevelopmentStorage=true*. Tady je příklad připojovacího řetězce v souboru *app.config:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Další informace najdete [v tématu Konfigurace připojovacích řetězců služby Azure Storage](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Vlastní účty úložiště a klíče

Azurite podporuje vlastní názvy účtů `AZURITE_ACCOUNTS` úložiště a klíče nastavením proměnné prostředí v následujícím formátu: `account1:key1[:key2];account2:key1[:key2];...`.

Použijte například vlastní účet úložiště, který má jeden klíč:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Nebo použijte více účtů úložiště se 2 klíči:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurit aktualizuje vlastní názvy účtů a klíče z proměnné prostředí každou minutu ve výchozím nastavení. Pomocí této funkce můžete dynamicky otáčet klíč účtu nebo přidávat nové účty úložiště bez restartování Azuritu.

> [!NOTE]
> Výchozí `devstoreaccount1` účet úložiště je zakázán, když nastavíte vlastní účty úložiště.

> [!NOTE]
> Při použití vlastních názvů účtů a klíčů odpovídajícím způsobem aktualizujte připojovací řetězec.

> [!NOTE]
> Pomocí `export` klíčového slova můžete nastavit proměnné prostředí `set` v prostředí Linuxu, které se používají v systému Windows.

### <a name="storage-explorer"></a>Storage Explorer

V Průzkumníku úložiště Azure se připojte k Azuritu tak, že kliknete na ikonu **Přidat účet,** pak vyberte **Připojit k místnímu emulátoru** a klikněte na **Připojit**.

## <a name="differences-between-azurite-and-azure-storage"></a>Rozdíly mezi Azurite a Azure Storage

Existují funkční rozdíly mezi místní instance Azurite a účet Azure Storage v cloudu.

### <a name="endpoint-and-connection-url"></a>Koncová adresa URL a adresa URL připojení

Koncové body služby pro Azurit se liší od koncových bodů účtu Azure Storage. Místní počítač neprovede překlad názvů domén, což vyžaduje, aby koncové body Azuritu byly místní adresy.

Když řešíte prostředek v účtu Azure Storage, název účtu je součástí názvu hostitele URI. Prostředek, který je adresován, je součástí cesty identifikátoru URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Následující identifikátor URI je platná adresa pro objekt blob v účtu Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Vzhledem k tomu, že místní počítač neprovádí překlad názvů domén, je název účtu součástí cesty URI namísto názvu hostitele. Pro prostředek v Azuritu použijte následující formát URI:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Pro přístup k objektu blob v azuritu může být použita následující adresa:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Škálování a výkon

Azurite není škálovatelná služba úložiště a nepodporuje velký počet souběžných klientů. Neexistuje žádná záruka výkonu. Azurit je určen pro vývojové a testovací účely.

### <a name="error-handling"></a>Zpracování chyb

Azurit je zarovnán s logikou zpracování chyb služby Azure Storage, ale existují rozdíly. Chybové zprávy se mohou například lišit, zatímco kódy stavu chyb se zarovnají.

### <a name="ra-grs"></a>RA-GRS

Azurit podporuje geograficky redundantní replikaci pro čtení (RA-GRS). Pro prostředky úložiště přístup k sekundární umístění **připojením -secondary** k názvu účtu. Pro přístup k objektu blob pomocí sekundárního objektu jen pro čtení v Azuritu může být například použita následující adresa:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite je open-source

Příspěvky a návrhy na Azurit jsou vítány. Přejděte na stránku projektu Azurite [GitHub](https://github.com/Azure/Azurite/projects) nebo [problémy githubu](https://github.com/Azure/Azurite/issues) pro milníky a pracovní položky, které sledujeme pro nadcházející funkce a opravy chyb. Podrobné pracovní položky jsou také sledovány v GitHubu.

## <a name="next-steps"></a>Další kroky

* [Použijte emulátor úložiště Azure pro vývoj a testování](storage-use-emulator.md) dokumentů staršíe emulátoru úložiště Azure, který je nahrazen Azurite.
* [Konfigurace připojovacích řetězců azure úložiště](storage-configure-connection-string.md) vysvětluje, jak sestavit platný připojovací řetězec Azure STorage.
