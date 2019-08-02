---
title: Použití Azurite Open Source emulátoru pro vývoj a testování BLOB Storage (Preview)
description: Azurite Open Source emulátor (Preview) poskytuje bezplatné místní prostředí pro testování aplikací služby Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/12/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: ebecd6cf9af5395e4da2b395ca9b2ff974a75409
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721695"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>Použití Azurite Open Source emulátoru pro vývoj a testování BLOB Storage (Preview)

Azurite verze 3 Open-Source emulátor (Preview) poskytuje bezplatné místní prostředí pro testování aplikací služby Azure Blob Storage. Až budete spokojeni s tím, jak vaše aplikace pracuje místně, přepněte se na použití účtu Azure Storage v cloudu. Emulátor poskytuje podporu pro různé platformy v systémech Windows, Linux a MacOS. Azurite V3 podporuje rozhraní API implementovaná službou Azure Blob service.

Azurite je budoucí platforma emulátoru úložiště. Azurite nahrazuje [emulátor Azure Storage](storage-use-emulator.md). Azurite se bude dál aktualizovat, aby podporovala nejnovější verze rozhraní Azure Storage API.

Existuje několik různých způsobů, jak nainstalovat a spustit Azurite v místním systému:

  1. [Instalace a spuštění rozšíření Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalace a spuštění Azurite pomocí NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalace a spuštění image Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Klonování, sestavování a spouštění Azurite z úložiště GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalace a spuštění rozšíření Azurite Visual Studio Code

V Visual Studio Code Vyberte podokno **rozšíření** a vyhledejte *Azurite* na **webu rozšíření: Marketplace**.

![Tržiště rozšíření Visual Studio Code](media/storage-use-azurite/azurite-vs-code-extension.png)

Případně přejděte na [vs Code na trh rozšíření](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) v prohlížeči. Kliknutím na tlačítko **nainstalovat** otevřete Visual Studio Code a přejdete přímo na stránku rozšíření Azurite.

Azurite můžete rychle spustit nebo zavřít kliknutím na **Azurite službu BLOB Service** na stavovém řádku vs Code nebo na paletě příkazů vs Code vydávat následující příkazy. Chcete-li otevřít paletu příkazů, stiskněte klávesu **F1** v vs Code.

Rozšíření podporuje následující příkazy Visual Studio Code:

   * **Azurite: Start** – spustí všechny služby Azurite Services.
   * **Azurite: Zavřít** – zavřít všechny služby Azurite Services
   * **Azurite: Vyčištění** – resetování všech průniku dat služby Azurite Services
   * **Azurite: Start** -BLOB Start – služba BLOB
   * **Azurite: Zavřít** – zavřít službu BLOB
   * **Azurite: Vyčištění** – služba vyčištění objektů BLOB v objektu BLOB

Pokud chcete nakonfigurovat Azurite v rámci Visual Studio Code, vyberte podokno rozšíření a klikněte pravým tlačítkem na **Azurite**. Vyberte **Konfigurovat nastavení rozšíření**.

![Azurite konfigurovat nastavení rozšíření](media/storage-use-azurite/azurite-configure-extension-settings.png)

Podporovaná jsou následující nastavení:

   * **Azurite: Hostitel** objektu BLOB – koncový bod naslouchací služby BLOB Service. Výchozí nastavení je 127.0.0.1.
   * **Azurite: Port** objektu BLOB – port naslouchání BLOB Service. Výchozí port je 10000.
   * **Azurite: Ladit** – výstup protokolu ladění na Azuriteový kanál. Výchozí hodnota je **false**.
   * **Azurite: Location** (umístění) – cesta k umístění pracovního prostoru. Výchozí je Visual Studio Code pracovní složka.
   * **Azurite: Tiché** – bezobslužný režim zakáže protokol přístupu. Výchozí hodnota je **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instalace a spuštění Azurite pomocí NPM

Tato metoda instalace vyžaduje, abyste nainstalovali [Node. js verze 8,0 nebo novější](https://nodejs.org) . **npm** je nástroj pro správu balíčků, který je součástí každé instalace Node. js. Po instalaci Node. js spusťte následující příkaz **npm** k instalaci Azurite.

```console
npm install -g azurite
```

Po instalaci Azurite najdete informace v tématu [spuštění Azurite z příkazového řádku](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instalace a spuštění image Azurite Docker

Použijte [dockerhubu](https://hub.docker.com/) k získání [nejnovější image Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) pomocí následujícího příkazu:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Spusťte image Docker Azurite**:

Následující příkaz spustí image Docker Azurite. `-p 10000:10000` Parametr přesměrovává požadavky z portu hostitelského počítače 10000 na instanci Docker.

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**Zadejte umístění pracovního prostoru**:

V následujícím příkladu `-v c:/azurite:/data` parametr určuje `c:/azurite` jako umístění trvalého data Azurite.

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Nastavit všechny parametry Azurite**:

Tento příklad ukazuje, jak nastavit všechny parametry příkazového řádku. Všechny níže uvedené parametry by měly být umístěny na jednom příkazovém řádku.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

Další informace o konfiguraci Azurite při spuštění najdete v tématu [Možnosti příkazového řádku](#command-line-options) .

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klonování, sestavování a spouštění Azurite z úložiště GitHub

Tato metoda instalace vyžaduje, abyste měli nainstalovaný [Git](https://git-scm.com/) . Naklonujte [úložiště GitHub](https://github.com/azure/azurite) pro projekt Azurite pomocí následujícího příkazu konzoly.

```console
git clone https://github.com/Azure/Azurite.git
```

Po klonování zdrojového kódu spusťte následující příkazy z kořenového adresáře klonovaného úložiště a sestavte a nainstalujte Azurite.

```console
npm install
npm run build
npm install -g
```

Po instalaci a sestavování Azurite najdete informace v tématu [spuštění Azurite z příkazového řádku](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Spuštění Azurite z příkazového řádku

> [!NOTE]
> Azurite nelze spustit z příkazového řádku, pokud jste nainstalovali pouze rozšíření Visual Studio Code. Místo toho použijte VS Code paletu příkazů. Další informace najdete v tématu [instalace a spuštění rozšíření Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Pokud chcete začít hned s příkazovým řádkem, vytvořte adresář s názvem **c:\azurite**a potom spusťte Azurite spuštěním následujícího příkazu:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Tento příkaz oznamuje Azurite, aby ukládal všechna data do konkrétního adresáře **c:\azurite**. Pokud je možnost **--Location** vynechána, použije se aktuální pracovní adresář.

## <a name="command-line-options"></a>Možnosti příkazového řádku

Tato část podrobně popisuje přepínače příkazového řádku, které jsou k dispozici při spuštění Azurite. Všechny přepínače příkazového řádku jsou volitelné.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

Přepínač **-l** je zkratka pro- **-Location**, **-s** je klávesová zkratka pro **--Silent**a **-d** je zkratka pro **--Debug**.

### <a name="listening-host"></a>Naslouchací hostitel

**Volitelné** Ve výchozím nastavení bude Azurite naslouchat na adresu 127.0.0.1 jako místní server. Použijte přepínač **--blobHost** a nastavte adresu na vaše požadavky.

Přijímat žádosti jenom v místním počítači:

```console
azurite --blobHost 127.0.0.1
```

Povolte vzdálené požadavky:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Povolení vzdálených požadavků může způsobit, že váš systém bude zranitelný vůči externím útokům.

### <a name="listening-port-configuration"></a>Konfigurace portu naslouchá

**Volitelné** Ve výchozím nastavení bude Azurite naslouchat Blob service na portu 10000. Použijte přepínač **--blobPort** k určení portu pro naslouchání, který požadujete.

> [!NOTE]
> Po použití přizpůsobeného portu musíte aktualizovat připojovací řetězec nebo odpovídající konfiguraci v Azure Storagech nástrojích nebo sadách SDK.

Přizpůsobení portu pro naslouchání Blob service:

```console
azurite --blobPort 8888
```

Nechat systém automaticky vybrat dostupný port:

```console
azurite --blobPort 0
```

Používaný port se zobrazí při spuštění Azurite.

### <a name="workspace-path"></a>Cesta k pracovnímu prostoru

**Volitelné** Azurite ukládá data na místní disk během provádění. Použijte přepínač **--Location** a zadejte cestu jako umístění pracovního prostoru. Ve výchozím nastavení se použije aktuální pracovní adresář procesu.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Přístup k protokolu

**Volitelné** Ve výchozím nastavení se protokol přístupu zobrazuje v okně konzoly. Zakažte zobrazování protokolu přístupu pomocí přepínače **--Silent** .

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Protokol ladění

**Volitelné** Protokol ladění obsahuje podrobné informace o každém trasování zásobníku požadavků a výjimek. Povolte protokol ladění zadáním platné cesty místního souboru pro přepínač **--Debug** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Autorizace pro nástroje a sady SDK

Připojte se k Azurite z Azure Storage sady SDK nebo nástrojů, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), pomocí jakékoli strategie ověřování. Je požadováno ověřování. Azurite podporuje autorizaci pomocí sdílených klíčů a podpisů sdíleného přístupu (SAS). Azurite také podporuje anonymní přístup k veřejným kontejnerům.

### <a name="well-known-storage-account-and-key"></a>Dobře známý účet úložiště a klíč

Pomocí Azurite můžete použít následující název účtu a klíč. Jedná se o stejný dobře známý účet a klíč, který používá starší emulátor úložiště Azure.

* Název účtu:`devstoreaccount1`
* Klíč účtu:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Kromě ověřování SharedKey Azurite podporuje ověřování SAS účtů a služeb. Anonymní přístup je k dispozici také v případě, že je kontejner nastaven tak, aby povoloval veřejný přístup.

### <a name="connection-string"></a>Připojovací řetězec

Nejjednodušší způsob, jak se připojit k Azurite z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru vaší aplikace, který odkazuje na zástupce *UseDevelopmentStorage = true*. Tady je příklad připojovacího řetězce v souboru *App. config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Další informace najdete v tématu [Konfigurace připojovacích řetězců Azure Storage](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Průzkumník služby Storage

V Průzkumník služby Azure Storage se připojte k Azurite kliknutím na ikonu **Přidat účet** a pak vyberte **připojit k místnímu emulátoru** a klikněte na **připojit**.

## <a name="differences-between-azurite-and-azure-storage"></a>Rozdíly mezi Azurite a Azure Storage

Existují funkční rozdíly mezi místními instancemi Azurite a účtem Azure Storage v cloudu.

### <a name="endpoint-and-connection-url"></a>Adresa URL koncového bodu a připojení

Koncové body služby pro Azurite se liší od koncových bodů účtu Azure Storage. V místním počítači není rozlišování názvů domén, což vyžaduje, aby koncové body Azurite byly místní adresy.

Při adresování prostředku v účtu Azure Storage je název účtu součástí názvu hostitele identifikátoru URI. Řešený prostředek je součástí cesty identifikátoru URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Následující identifikátor URI je platná adresa pro objekt BLOB v účtu Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Vzhledem k tomu, že místní počítač neprovádí překlad názvů domén, je název účtu součástí cesty identifikátoru URI místo názvu hostitele. Pro prostředek v Azurite použijte následující formát identifikátoru URI:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Pro přístup k objektu BLOB v Azurite je možné použít následující adresu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Škálování a výkon

Azurite není škálovatelná služba úložiště a nepodporuje velký počet souběžných klientů. Není zajištěna žádná záruka na výkon. Azurite je určena pro účely vývoje a testování.

### <a name="error-handling"></a>Zpracování chyb

Azurite je zarovnán s Azure Storage logikou zpracování chyb, ale existují rozdíly. Například chybové zprávy mohou být rozdílné, zatímco kódy stavů chyby jsou zarovnány.

### <a name="ra-grs"></a>RA-GRS

Azurite podporuje geograficky redundantní replikaci s přístupem pro čtení (RA-GRS). Pro prostředky úložiště přejděte k sekundárnímu umístění připojením **– sekundární** k názvu účtu. Například následující adresa může být použita pro přístup k objektu BLOB pomocí sekundárního souboru jen pro čtení v Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite je open source

Příspěvky a návrhy pro Azurite jsou Vítá vás. Přejít na stránku [projektu GitHubu](https://github.com/Azure/Azurite/projects) Azurite nebo [problémy GitHubu](https://github.com/Azure/Azurite/issues) pro milníky a pracovní položky, které sledujeme pro nadcházející funkce a opravy chyb. Podrobné pracovní položky jsou také sledovány na GitHubu.

## <a name="next-steps"></a>Další kroky

* [Použijte emulátor úložiště Azure pro vývoj a testování](storage-use-emulator.md) dokumentů staršího emulátoru úložiště Azure, který nahrazuje Azurite.
* [Konfigurace připojovacích řetězců Azure Storage](storage-configure-connection-string.md) vysvětluje, jak sestavit platný připojovací řetězec úložiště Azure.
