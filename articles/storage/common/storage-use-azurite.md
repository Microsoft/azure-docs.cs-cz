---
title: Použití emulátoru Azurite pro vývoj místních Azure Storage
description: Azurite Open Source emulátor poskytuje bezplatné místní prostředí pro testování vašich aplikací Azure Storage.
author: twooley
ms.author: twooley
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: a921de0d976b9c92ca7978feb7caf69484ba9379
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277121"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Použití emulátoru Azurite pro vývoj místních Azure Storage

Azurite Open Source emulátor poskytuje bezplatné místní prostředí pro testování objektů blob Azure a aplikací úložiště ve frontě. Až budete spokojeni s tím, jak vaše aplikace pracuje místně, přepněte se na použití účtu Azure Storage v cloudu. Emulátor poskytuje podporu pro různé platformy v systémech Windows, Linux a macOS.

Azurite je budoucí platforma emulátoru úložiště. Azurite nahrazuje [emulátor Azure Storage](storage-use-emulator.md). Azurite se bude dál aktualizovat, aby podporovala nejnovější verze rozhraní Azure Storage API.

Existuje několik různých způsobů, jak nainstalovat a spustit Azurite v místním systému:

  1. [Instalace a spuštění rozšíření Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instalace a spuštění Azurite pomocí NPM](#install-and-run-azurite-by-using-npm)
  1. [Instalace a spuštění image Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Klonování, sestavování a spouštění Azurite z úložiště GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instalace a spuštění rozšíření Azurite Visual Studio Code

V Visual Studio Code Vyberte podokno **rozšíření** a vyhledejte *Azurite* na **webu rozšíření: Marketplace**.

![Tržiště rozšíření Visual Studio Code](media/storage-use-azurite/azurite-vs-code-extension.png)

V prohlížeči můžete také přejít na [trh rozšíření Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) . Kliknutím na tlačítko **nainstalovat** otevřete Visual Studio Code a přejdete přímo na stránku rozšíření Azurite.

Rozšíření podporuje následující příkazy Visual Studio Code. Chcete-li otevřít paletu příkazů, stiskněte klávesu F1 v Visual Studio Code. 

   - **Azurite: vyčištění** a resetování všech průniku dat Azurite Services
   - **Azurite: vyčistit službu BLOB** Service – vyčistit službu BLOB Service
   - **Azurite: služba vyčištění fronty** – služba fronty vyčištění
   - **Azurite: zavřít** a zavřít všechny služby Azurite
   - **Azurite: zavření služby BLOB** – ukončení služby BLOB Service
   - **Azurite: Close Service** – služba front ukončení
   - **Azurite: Start** – spustí všechny služby Azurite.
   - **Azurite: spuštění služby BLOB** -spuštění služby BLOB Service
   - **Azurite: Start Service** – služba front spuštění

Pokud chcete nakonfigurovat Azurite v rámci Visual Studio Code, vyberte podokno rozšíření. Vyberte ikonu **Správa** (ozubeného kolečka) pro **Azurite**. Vyberte **nastavení rozšíření**.

![Azurites konfigurovat nastavení rozšíření](media/storage-use-azurite/azurite-configure-extension-settings.png)

Podporovaná jsou následující nastavení:

   - **Azurite: Host objektu BLOB** – koncový bod naslouchací služby BLOB Service. Výchozí nastavení je 127.0.0.1.
   - **Azurite: port objektu BLOB** – port naslouchání BLOB Service. Výchozí port je 10000.
   - **Azurite: CERT** -PATH-cesta k místně důvěryhodnému souboru certifikátu PEM nebo PFX pro povolení režimu https.
   - **Azurite: Debug** – výstup protokolu ladění do kanálu Azurite. Výchozí hodnota je **false** (nepravda).
   - **Azurite: klíčová** cesta k souboru důvěryhodného PEM klíče, který je vyžadován při **Azurite: CERT** odkazuje na soubor PEM.
   - **Azurite: Location** – cesta k umístění pracovního prostoru. Výchozí je Visual Studio Code pracovní složka.
   - **Azurite: volný** režim – povolení volného režimu, který ignoruje nepodporované hlavičky a parametry.
   - **Azurite: OAuth** – volitelná úroveň OAuth.
   - **Azurite: PWD** -Password pro soubor PFX. Požadováno v případě, že **Azurite: CERT** odkazuje na soubor PFX.
   - **Azurite: Queue Host** – koncový bod naslouchací služby služba front. Výchozí nastavení je 127.0.0.1.
   - **Azurite: port fronty** – port naslouchání služba front. Výchozí port je 10001.
   - **Azurite: bezobslužný** bezobslužný režim zakáže protokol přístupu. Výchozí hodnota je **false** (nepravda).
   - **Azurite: Přeskočit kontrolu verze rozhraní API** – Přeskočit kontrolu verze API žádosti Výchozí hodnota je **false** (nepravda).

## <a name="install-and-run-azurite-by-using-npm"></a>Instalace a spuštění Azurite pomocí NPM

Tato metoda instalace vyžaduje, abyste měli nainstalovanou [ verziNode.js 8,0 nebo novější](https://nodejs.org) . Správce balíčků Node (npm) je nástroj pro správu balíčků, který je součástí každé instalace Node.js. Po instalaci Node.js spusťte následující `npm` příkaz a nainstalujte Azurite.

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

Následující příkaz spustí image Docker Azurite. `-p 10000:10000`Parametr přesměrovává požadavky z portu hostitelského počítače 10000 na instanci Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Zadejte umístění pracovního prostoru**:

V následujícím příkladu `-v c:/azurite:/data` parametr určuje *c:/Azurite* jako umístění trvalého data Azurite. Adresář, *c:/Azurite*, musí být vytvořen před spuštěním příkazu Docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Spustit pouze službu BLOB Service**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Další informace o konfiguraci Azurite při spuštění najdete v tématu [Možnosti příkazového řádku](#command-line-options).

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
> Azurite nelze spustit z příkazového řádku, pokud jste nainstalovali pouze rozšíření Visual Studio Code. Místo toho použijte Visual Studio Code paletu příkazů. Další informace najdete v tématu [instalace a spuštění rozšíření Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Pokud chcete začít hned s příkazovým řádkem, vytvořte adresář s názvem *c:\azurite* a potom spusťte Azurite spuštěním následujícího příkazu:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Tento příkaz oznamuje Azurite, aby ukládal všechna data do konkrétního adresáře *c:\azurite*. Pokud `--location` je tato možnost vynechána, použije se aktuální pracovní adresář.

## <a name="command-line-options"></a>Možnosti příkazového řádku

Tato část podrobně popisuje přepínače příkazového řádku, které jsou k dispozici při spuštění Azurite.

### <a name="help"></a>Help

**Volitelné** – získat nápovědu příkazového řádku pomocí `-h` `--help` přepínače nebo.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Přijímající hostitel objektu BLOB

**Volitelné** – ve výchozím nastavení bude Azurite naslouchat serveru 127.0.0.1 jako místní server. Pomocí `--blobHost` přepínače nastavte adresu podle vašich požadavků.

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

### <a name="blob-listening-port-configuration"></a>Konfigurace portu naslouchání objektem BLOB

**Volitelné** – ve výchozím nastavení bude Azurite naslouchat BLOB Service na portu 10000. Pomocí `--blobPort` přepínače zadejte port pro naslouchání, který požadujete.

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

### <a name="queue-listening-host"></a>Hostitel naslouchání front

**Volitelné** – ve výchozím nastavení bude Azurite naslouchat serveru 127.0.0.1 jako místní server. Pomocí `--queueHost` přepínače nastavte adresu podle vašich požadavků.

Přijímat žádosti jenom v místním počítači:

```console
azurite --queueHost 127.0.0.1
```

Povolte vzdálené požadavky:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Povolení vzdálených požadavků může způsobit, že váš systém bude zranitelný vůči externím útokům.

### <a name="queue-listening-port-configuration"></a>Konfigurace portu naslouchání frontou

**Volitelné** – ve výchozím nastavení bude Azurite naslouchat služba front na portu 10001. Pomocí `--queuePort` přepínače zadejte port pro naslouchání, který požadujete.

> [!NOTE]
> Po použití přizpůsobeného portu musíte aktualizovat připojovací řetězec nebo odpovídající konfiguraci v Azure Storagech nástrojích nebo sadách SDK.

Přizpůsobení portu pro naslouchání Služba front:

```console
azurite --queuePort 8888
```

Nechat systém automaticky vybrat dostupný port:

```console
azurite --queuePort 0
```

Používaný port se zobrazí při spuštění Azurite.

### <a name="workspace-path"></a>Cesta k pracovnímu prostoru

**Volitelné** – Azurite ukládá data na místní disk během provádění. Pomocí `-l` přepínače nebo `--location` Zadejte cestu k umístění pracovního prostoru. Ve výchozím nastavení se použije aktuální pracovní adresář procesu. Poznamenejte si malé písmeno l.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Přístup k protokolu

**Volitelné** – ve výchozím nastavení se protokol přístupu zobrazí v okně konzoly. Zakažte zobrazování protokolu přístupu pomocí `-s` `--silent` přepínače nebo.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Protokol ladění

**Volitelné** – protokol ladění obsahuje podrobné informace o každém trasování zásobníku požadavků a výjimek. Povolte protokol ladění zadáním platné místní cesty k souboru `-d` nebo `--debug` přepínači.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Volný režim

**Volitelné** – ve výchozím nastavení Azurite použije striktní režim pro blokování nepodporovaných hlaviček a parametrů požadavků. Zakažte striktní režim pomocí `-L` přepínače nebo `--loose` . Všimněte si, že se jedná o velké písmeno L.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Verze

**Volitelné** – zobrazí se číslo nainstalované verze Azurite pomocí `-v` `--version` přepínače nebo.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Konfigurace certifikátu (HTTPS)

**Volitelné** – ve výchozím nastavení používá AZURITE protokol HTTP. Režim HTTPS povolíte tak, že zadáte cestu k souboru certifikátu aplikace PEM (Privacy Enhanced mail) nebo [Personal Information Exchange (. pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) na `--cert` přepínač.

Když `--cert` je k dispozici soubor PEM, je nutné zadat odpovídající `--key` přepínač.

```console
azurite --cert path/server.pem --key path/key.pem
```

Když `--cert` je k dispozici soubor PFX, je nutné zadat odpovídající `--pwd` přepínač.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Podrobné informace o vytváření souborů PEM a PFX najdete v tématu [nastavení protokolu HTTPS](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>Konfigurace OAuth

**Volitelné** – povolte ověřování OAuth pro Azurite pomocí `--oauth` přepínače.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth vyžaduje koncový bod HTTPS. Zajistěte, aby byl protokol HTTPS povolený `--cert` přepínačem spolu s `--oauth` přepínačem.

Azurite podporuje základní ověřování zadáním `basic` parametru pro `--oauth` přepínač. Azurite provede základní ověřování, jako je ověřování příchozího tokenu nosiče, kontrola vystavitele, cílové skupiny a vypršení platnosti. Azurite nekontroluje podpis nebo oprávnění tokenu.

### <a name="skip-api-version-check"></a>Přeskočit kontrolu verze rozhraní API

**Volitelné** – při spuštění Azurite zkontroluje, jestli je požadovaná verze rozhraní API platná. Následující příkaz přeskočí kontrolu verze rozhraní API:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Autorizace pro nástroje a sady SDK

Připojte se k Azurite z Azure Storage sady SDK nebo nástrojů, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), pomocí jakékoli strategie ověřování. Vyžaduje se ověření. Azurite podporuje autorizaci pomocí OAuth, sdíleného klíče a signatury sdíleného přístupu (SAS). Azurite také podporuje anonymní přístup k veřejným kontejnerům.

Pokud používáte sady SDK Azure, spusťte Azurite s `--oauth basic and --cert --key/--pwd` možnostmi.

### <a name="well-known-storage-account-and-key"></a>Dobře známý účet úložiště a klíč

Azurite přijímá stejný dobře známý účet a klíč, který používá starší emulátor Azure Storage.

- Název účtu: `devstoreaccount1`
- Klíč účtu: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Vlastní účty úložiště a klíče

Azurite podporuje vlastní názvy a klíče účtu úložiště nastavením `AZURITE_ACCOUNTS` proměnné prostředí v následujícím formátu: `account1:key1[:key2];account2:key1[:key2];...` .

Použijte například vlastní účet úložiště, který má jeden klíč:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Nebo použijte více účtů úložiště se dvěma klíči:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite ve výchozím nastavení aktualizuje vlastní názvy účtů a klíče z proměnné prostředí. Pomocí této funkce můžete klíč účtu dynamicky otočit nebo přidat nové účty úložiště, aniž byste museli restartovat Azurite.

> [!NOTE]
> Výchozí `devstoreaccount1` účet úložiště je při nastavování vlastních účtů úložiště zakázaný.

### <a name="connection-strings"></a>Připojovací řetězce

Nejjednodušší způsob, jak se připojit k Azurite z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru vaší aplikace, který odkazuje na zástupce *UseDevelopmentStorage = true*. Tady je příklad připojovacího řetězce v souboru *app.config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>Připojovací řetězce HTTP

Následujícím připojovacím řetězcům můžete předat sady SDK nebo nástroje [Azure](https://aka.ms/azsdk) , jako je Azure CLI 2,0 nebo Průzkumník služby Storage.

Úplný připojovací řetězec je:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Pokud se chcete připojit pouze ke službě BLOB Service, připojovací řetězec:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Pokud se chcete připojit pouze ke službě Queue, připojovací řetězec:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>Připojovací řetězce HTTPS

Úplný připojovací řetězec HTTPS:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Pokud chcete použít jenom službu BLOB Service, je připojovací řetězec HTTPS:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Aby bylo možné používat pouze službu Queue, je připojovací řetězec HTTPS:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Pokud jste použili `dotnet dev-certs` k vygenerování certifikátu podepsaného svým držitelem, použijte následující připojovací řetězec.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Aktualizujte připojovací řetězec při použití [vlastních účtů a klíčů úložiště](#custom-storage-accounts-and-keys).

Další informace najdete v tématu [Konfigurace připojovacích řetězců Azure Storage](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Sady Azure SDK

Pokud chcete používat Azurite se sadami [SDK Azure](https://aka.ms/azsdk), použijte možnosti OAuth a https:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Pak můžete vytvořit instanci objektu BlobContainerClient, BlobServiceClient nebo BlobClient.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

Můžete také vytvořit instanci QueueClient nebo QueueServiceClient.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

K zobrazení dat uložených v Azurite můžete použít Průzkumník služby Storage.

#### <a name="connect-to-azurite-using-http"></a>Připojení k Azurite pomocí protokolu HTTP

V Průzkumník služby Storage se připojte k Azurite pomocí následujících kroků:

 1. Vyberte ikonu **Spravovat účty** .
 1. Vyberte **Přidat účet** .
 1. Vybrat **připojit k místnímu emulátoru**
 1. Vyberte **Další**.
 1. Upravte pole **zobrazované jméno** na název dle vašeho výběru.
 1. Znovu vybrat **Další**
 1. Vyberte **připojit**

#### <a name="connect-to-azurite-using-https"></a>Připojení k Azurite pomocí protokolu HTTPS

Ve výchozím nastavení Průzkumník služby Storage neotevře koncový bod HTTPS, který používá certifikát podepsaný svým držitelem. Pokud používáte Azurite s protokolem HTTPS, pravděpodobně budete používat certifikát podepsaný svým držitelem. V Průzkumník služby Storage importujte certifikáty SSL přes dialogové okno **Upravit** certifikáty  ->  **SSL**  ->  **importovat certifikáty** .

##### <a name="import-certificate-to-storage-explorer"></a>Importovat certifikát do Průzkumník služby Storage

1. Najděte certifikát na místním počítači.
1. V Průzkumník služby Storage pokračujte v **úpravách**  ->  **certifikátů SSL**  ->  **Import certifikátů** a import certifikátu.

Pokud certifikát neimportujete, zobrazí se chyba:

`unable to verify the first certificate` nebo `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Přidat Azurite prostřednictvím připojovacího řetězce HTTPS

Pomocí těchto kroků přidejte Azurite HTTPS do Průzkumník služby Storage:

1. Výběr **přepínače Průzkumník**
1. Vyberte **místní & připojené** .
1. Klikněte pravým tlačítkem na **účty úložiště** a vyberte **připojit k Azure Storage**.
1. Vyberte **Použít připojovací řetězec**
1. Vyberte **Další**.
1. Do pole **Zobrazovaný název** zadejte hodnotu.
1. Zadejte [připojovací řetězec https](#https-connection-strings) z předchozí části tohoto dokumentu.
1. Vyberte **Další**.
1. Vyberte **připojit**

## <a name="workspace-structure"></a>Struktura pracovního prostoru

Při inicializaci Azurite se můžou v umístění pracovního prostoru vytvořit následující soubory a složky.

- `__blobstorage__` – Adresář obsahující Azurite BLOB Service trvalá binární data
- `__queuestorage__` -Adresář, který obsahuje zadržená binární data služby front Azurite
- `__azurite_db_blob__.json` – Azurite soubor metadat služby BLOB Service
- `__azurite_db_blob_extent__.json` – Azurite soubor metadat rozsahu služby BLOB Service
- `__azurite_db_queue__.json` – Soubor metadat služby Azurite Queue
- `__azurite_db_queue_extent__.json` – Soubor metadat rozsahu služby fronty Azurite

Pokud chcete Azurite vyčistit, odstraňte výše uvedené soubory a složky a restartujte emulátor.

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

Azurite nepodporuje velké počty připojených klientů. Není zajištěna žádná záruka na výkon. Azurite je určena pro účely vývoje a testování.

### <a name="error-handling"></a>Zpracování chyb

Azurite je zarovnán s Azure Storage logikou zpracování chyb, ale existují rozdíly. Například chybové zprávy mohou být rozdílné, zatímco kódy stavů chyby jsou zarovnány.

### <a name="ra-grs"></a>RA-GRS

Azurite podporuje geograficky redundantní replikaci s přístupem pro čtení (RA-GRS). Pro prostředky úložiště přejděte k sekundárnímu umístění připojením `-secondary` k názvu účtu. Například následující adresa může být použita pro přístup k objektu BLOB pomocí sekundárního souboru jen pro čtení v Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Podpora tabulek

Podpora tabulek v Azurite je momentálně ve vývoji a je otevřená pro přispívání! V případě nejnovějšího postupu se podívejte na projekt [tabulky Azurite V3](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) .

Podpora trvalých funkcí vyžaduje tabulky.

## <a name="azurite-is-open-source"></a>Azurite je open source

Příspěvky a návrhy pro Azurite jsou Vítá vás. Přejít na stránku [projektu GitHubu](https://github.com/Azure/Azurite/projects) Azurite nebo [problémy GitHubu](https://github.com/Azure/Azurite/issues) pro milníky a pracovní položky, které sledujeme pro nadcházející funkce a opravy chyb. Podrobné pracovní položky jsou také sledovány na GitHubu.

## <a name="next-steps"></a>Další kroky

- [Použijte emulátor Azure Storage pro vývoj a testování](storage-use-emulator.md) dokumentů starší verze Azure Storage emulátor, který nahrazuje Azurite.
- [Konfigurace připojovacích řetězců Azure Storage](storage-configure-connection-string.md) vysvětluje, jak sestavit platný připojovací řetězec Azure Storage.
