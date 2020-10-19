---
title: Místní instalace a vývoj pomocí emulátoru Azure Cosmos DB
description: Naučte se instalovat a používat emulátor Azure Cosmos DB v prostředích Windows, Linux, macOS a Windows Docker. Pomocí emulátoru můžete svou aplikaci místně vyvíjet a testovat bez vytváření předplatného Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperfq1
ms.openlocfilehash: e846f00388ce21690729f62592c86b73cc42c3f3
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173780"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Instalace a použití emulátoru Azure Cosmos pro místní vývoj a testování

Emulátor Azure Cosmos poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Pomocí emulátoru Azure Cosmos můžete svou aplikaci vyvíjet a testovat místně, aniž byste museli vytvářet předplatné Azure nebo náklady. Až budete spokojeni s tím, jak vaše aplikace funguje v emulátoru Azure Cosmos, můžete přejít na používání účtu Azure Cosmos v cloudu. Tento článek popisuje, jak nainstalovat a používat emulátor v prostředích Windows, Linux, macOS a Windows Docker.

## <a name="download-the-emulator"></a>Stažení emulátoru

Pokud chcete začít, Stáhněte a nainstalujte si nejnovější verzi emulátoru Azure Cosmos na místním počítači. V článku [poznámky k verzi emulátoru](local-emulator-release-notes.md) najdete seznam všech dostupných verzí a aktualizací funkcí, které byly provedeny v jednotlivých verzích.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[Stáhnout emulátor Azure Cosmos](https://aka.ms/cosmosdb-emulator)**

Pomocí emulátoru Azure Cosmos můžete vyvíjet aplikace s využitím účtů [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)a [Table](local-emulator.md#table-api) API. Průzkumník dat v emulátoru je aktuálně podporuje jenom prohlížení dat SQL. data vytvořená pomocí klientských aplikací MongoDB, Gremlin/Graph a Cassandra v tuto chvíli zobrazit nelze. Další informace najdete v tématu [jak se připojit ke koncovému bodu emulátoru](#connect-with-emulator-apis) z různých rozhraní API.

## <a name="how-does-the-emulator-work"></a>Jak emulátor funguje?

Emulátor Azure Cosmos zajišťuje emulaci Azure Cosmos DB služby s vysokou přesností. Podporuje ekvivalentní funkce jako Azure Cosmos DB, což zahrnuje vytváření dat, dotazování na data, zřizování a škálování kontejnerů a spouštění uložených procedur a triggerů. Můžete vyvíjet a testovat aplikace pomocí emulátoru Azure Cosmos a nasazovat je do Azure v globálním měřítku pomocí aktualizace koncového bodu připojení Azure Cosmos DB.

Přestože je emulace služby Azure Cosmos DB věrná, implementace emulátoru se od služby liší. Emulátor například používá standardní součásti operačního systému, jako je místní systém souborů pro trvalost a sada protokolů HTTPS pro připojení. Funkce, které se spoléhají na infrastrukturu Azure, jako je globální replikace, latence v milisekundách pro čtení a zápis a přizpůsobitelné úrovně konzistence se nepoužijí při použití emulátoru.

Data mezi emulátorem Azure Cosmos a službou Azure Cosmos DB můžete migrovat pomocí [nástroje Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Rozdíly mezi emulátorem a cloudovou službou

Vzhledem k tomu, že emulátor Azure Cosmos poskytuje emulované prostředí, které běží na místní pracovní stanici pro vývojáře, je mezi emulátorem a účtem Azure Cosmos v cloudu několik rozdílů:

* V současné době se v emulátoru **Průzkumník dat** v emulátoru plně podporuje jenom klienti API SQL. Zobrazení a operace **Průzkumník dat** pro rozhraní API Azure Cosmos DB, jako jsou MongoDB, Table, Graph a Cassandra API, nejsou plně podporované.

* Emulátor podporuje jenom jeden pevný účet a dobře známý primární klíč. Při použití emulátoru Azure Cosmos se klíč nedá znovu vygenerovat. výchozí klíč ale můžete změnit pomocí možnosti [příkazového řádku](emulator-command-line-parameters.md) .

* Pomocí emulátoru můžete vytvořit účet Azure Cosmos jenom v režimu [zřízené propustnosti](set-throughput.md) . v současné době nepodporuje režim bez [serveru](serverless.md) .

* Emulátor není škálovatelná služba a nepodporuje velký počet kontejnerů. Při použití emulátoru Azure Cosmos ve výchozím nastavení můžete vytvořit až 25 kontejnerů pevné velikosti v 400 RU/s (podporuje se jenom pomocí sad Azure Cosmos DB SDK) nebo 5 neomezených kontejnerů. Další informace o tom, jak tuto hodnotu změnit, najdete v článku [Nastavení hodnoty PartitionCount](emulator-command-line-parameters.md#set-partitioncount) .

* Emulátor nenabízí různé [Azure Cosmos dB úrovně konzistence](consistency-levels.md) , jako je cloudová služba.

* Emulátor nenabízí replikaci ve [více oblastech](distribute-data-globally.md).

* Vzhledem k tomu, že kopie vašeho emulátoru Azure Cosmos nemusí být vždy aktuální s nejnovějšími změnami ve službě Azure Cosmos DB, měli byste vždy použít [Azure Cosmos DB Plánovač kapacity](estimate-ru-with-capacity-planner.md) k přesnému odhadu potřeb vaší aplikace propustnosti (ru).

* Emulátor podporuje maximální velikost vlastností ID 254 znaků.

## <a name="install-the-emulator"></a>Instalace emulátoru

Před instalací emulátoru se ujistěte, že máte následující požadavky na hardware a software:

* Požadavky na software:
  * V současné době jsou podporovány systémy Windows Server 2012 R2, Windows Server 2016, 2019 nebo Windows 8, což je hostitelský operační systém 10. Hostitelský operační systém se zapnutou službou Active Directory se v tuto chvíli nepodporuje.
  * 64bitový operační systém

* Minimální požadavky na hardware:
  * 2 GB RAM
  * 10 GB volného místa na disku

* Pokud chcete nainstalovat, nakonfigurovat a spustit emulátor Azure Cosmos, musíte mít v počítači oprávnění správce. Emulátor přidá certifikát a nastaví také pravidla brány firewall, aby bylo možné spustit jeho služby. Proto jsou oprávnění správce nutná k tomu, aby emulátor mohl tyto operace provádět.

Pokud chcete začít, Stáhněte a nainstalujte si nejnovější verzi [emulátoru Azure Cosmos](https://aka.ms/cosmosdb-emulator) na místním počítači. Pokud narazíte na problémy při instalaci emulátoru, přečtěte si článek [řešení potíží s emulátorem](troubleshoot-local-emulator.md) pro ladění.

V závislosti na požadavcích na systém můžete spustit emulátor v [systému Windows](#run-on-windows), [Docker for Windows](#run-on-windows-docker), [Linux nebo MacOS](#run-on-linux-macos) , jak je popsáno v dalších částech tohoto článku.

## <a name="check-for-emulator-updates"></a>Vyhledat aktualizace emulátoru

Každá verze emulátoru je dodávána se sadou aktualizací funkcí nebo oprav chyb. Pokud chcete zobrazit dostupné verze, přečtěte si článek [poznámky k verzi emulátoru](local-emulator-release-notes.md) .

Pokud jste použili výchozí nastavení, budou data odpovídající emulátoru po instalaci uložena v umístění%LOCALAPPDATA%\CosmosDBEmulator. Můžete nakonfigurovat jiné umístění pomocí nastavení volitelné cesty k datům; To je `/DataPath=PREFERRED_LOCATION` jako [parametr příkazového řádku](emulator-command-line-parameters.md). Data vytvořená v jedné verzi emulátoru Azure Cosmos není zaručená, že budou přístupná, pokud používáte jinou verzi. Pokud potřebujete zachovat data po dlouhou dobu, doporučujeme ukládat tato data do účtu Azure Cosmos místo emulátoru Azure Cosmos.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Použití emulátoru ve Windows

Emulátor Azure Cosmos se `C:\Program Files\Azure Cosmos DB Emulator` ve výchozím nastavení instaluje do umístění. Pokud chcete spustit emulátor Azure Cosmos ve Windows, vyberte tlačítko **Start** nebo stiskněte klávesu Windows. Začněte psát **emulátor Azure Cosmos**a vyberte ze seznamu aplikací emulátor.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Vyberte tlačítko Start nebo stiskněte klávesu Windows, začněte psát emulátor Azure Cosmos a vyberte ze seznamu aplikací emulátor.":::

Po spuštění emulátoru se zobrazí ikona v oznamovací oblasti hlavního panelu systému Windows. V prohlížeči se v této adrese URL automaticky otevře Průzkumník dat Azure Cosmos `https://localhost:8081/_explorer/index.html` .

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Vyberte tlačítko Start nebo stiskněte klávesu Windows, začněte psát emulátor Azure Cosmos a vyberte ze seznamu aplikací emulátor.":::

Emulátor můžete také spustit a zastavit z příkazového řádku nebo příkazů PowerShellu. Další informace najdete v [referenčním článku k nástroji příkazového řádku](emulator-command-line-parameters.md) .

Emulátor Azure Cosmos ve výchozím nastavení běží na místním počítači (localhost), který naslouchá na portu 8081. Adresa se zobrazí jako `https://localhost:8081/_explorer/index.html`. Pokud zavřete Průzkumníka a chcete ho znovu otevřít později, můžete buď otevřít adresu URL v prohlížeči, nebo ji spustit z emulátoru Azure Cosmos v ikoně na hlavním panelu Windows, jak je znázorněno níže.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Vyberte tlačítko Start nebo stiskněte klávesu Windows, začněte psát emulátor Azure Cosmos a vyberte ze seznamu aplikací emulátor.":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Použít emulátor na Docker for Windows

Emulátor Azure Cosmos můžete spustit v kontejneru Docker systému Windows. Další informace najdete v [centru Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) pro příkaz Docker Pull a na [GitHubu](https://github.com/Azure/azure-cosmos-db-emulator-docker) `Dockerfile` . V současné době emulátor nefunguje v Docker pro Oracle Linux. K provedení emulátoru v Docker for Windows použijte následující pokyny:

1. Po instalaci [Docker for Windows](https://www.docker.com/docker-windows) přepněte do kontejnerů Windows tak, že kliknete pravým tlačítkem na ikonu Docker na panelu nástrojů a vyberete **Přepnout na kontejnery Windows**.

1. Potom si spuštěním následujícího příkazu z vašeho oblíbeného prostředí stáhněte image emulátoru z Centra Dockeru.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Pokud chcete spustit image, spusťte následující příkazy v závislosti na příkazovém řádku nebo prostředí PowerShellu:

   # <a name="command-line"></a>[Příkazový řádek](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Image Docker založené na Windows nemusí být všeobecně kompatibilní s každým hostitelským operačním systémem Windows. Například výchozí image emulátoru Azure Cosmos je kompatibilní jenom s Windows 10 a Windows serverem 2016. Pokud potřebujete image, která je kompatibilní se systémem Windows Server 2019, spusťte následující příkaz:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   Odpověď vypadá podobně jako následující zpráva:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Pokud se při provádění `docker run` příkazu zobrazí chyba konfliktu portů (to znamená, že zadaný port je již používán), předejte vlastní port změnou čísel portů. Například můžete změnit parametr-p 8081:8081 na "-p 443:8081".

1. Nyní použijte koncový bod emulátoru a primární klíč z odpovědi a importujte certifikát TLS/SSL do hostitele. Certifikát TLS/SSL naimportujete spuštěním následujících kroků z příkazového řádku správce:

   # <a name="command-line"></a>[Příkazový řádek](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Pokud po spuštění emulátoru zavřete interaktivní prostředí, vypne se kontejner emulátoru. Chcete-li znovu otevřít Průzkumníka dat, přejděte v prohlížeči na následující adresu URL. Koncový bod emulátoru je uveden ve zprávě s odpovědí uvedené výše.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Pokud máte klientskou aplikaci .NET spuštěnou v kontejneru Docker Linux a používáte emulátor Azure Cosmos na hostitelském počítači, použijte pokyny v následující části k importu certifikátu do kontejneru Docker pro Linux.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Obnovování certifikátů emulátoru při spuštění v kontejneru Docker

Při spuštění emulátoru v kontejneru Docker se certifikáty přidružené k emulátoru znovu vygenerují pokaždé, když zastavíte a restartujete příslušný kontejner. Vzhledem k tomu, že je nutné znovu naimportovat certifikáty po každém spuštění kontejneru. Pokud chcete toto omezení obejít, můžete k vytvoření vazby kontejneru Docker na konkrétní IP adresu a image kontejneru použít soubor s možností sestavení Docker.

Můžete například použít následující konfiguraci v rámci souboru Docker pro sestavení, nezapomeňte ji naformátovat podle svého požadavku: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Použití emulátoru v systému Linux nebo macOS

V současné době se emulátor Azure Cosmos dá spustit jenom v systému Windows. Pokud používáte Linux nebo macOS, můžete emulátor spustit na virtuálním počítači s Windows, který je hostovaný v hypervisoru, jako je Parallel nebo VirtualBox.

> [!NOTE]
> Pokaždé, když restartujete virtuální počítač s Windows, který je hostovaný v hypervisoru, musíte certifikát znovu naimportovat, protože se změní IP adresa virtuálního počítače. Import certifikátu není nutný pro případ, že jste nakonfigurovali virtuální počítač pro zachování IP adresy.

K použití emulátoru v prostředích se systémem Linux nebo macOS použijte následující postup:

1. Z virtuálního počítače s Windows spusťte následující příkaz a poznamenejte si adresu IPv4:

   ```bash
   ipconfig.exe
   ```

1. V rámci aplikace změňte adresu URL koncového bodu tak, aby používala IPv4 adresu vrácenou `ipconfig.exe` místo `localhost` .

1. Z virtuálního počítače s Windows spusťte z příkazového řádku emulátor Azure Cosmos pomocí následujících možností. Podrobnosti o parametrech podporovaných příkazovým řádkem najdete v referenčních informacích k [nástroji příkazového řádku emulátoru](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Nakonec potřebujete vyřešit proces důvěryhodných certifikátů mezi aplikací spuštěnou v prostředí Linux nebo Mac a emulátorem. K vyřešení certifikátu můžete použít jednu z následujících dvou možností:

   1. [Importujte certifikát TLS/SSL emulátoru do prostředí systému Linux nebo Mac](#import-certificate) nebo
   2. [Zakázání ověřování TLS/SSL v aplikaci](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Možnost 1: Import certifikátu TLS/SSL emulátoru

Následující části ukazují, jak importovat certifikát TLS/SSL emulátoru do prostředí Linux a macOS.

#### <a name="linux-environment"></a>Prostředí Linux

Pokud pracujete na platformě Linux, aplikace .NET Relay na OpenSSL provede ověření:

1. [Exportujte certifikát ve formátu PFX](local-emulator-export-ssl-certificates.md#export-emulator-certificate). Možnost PFX je k dispozici při výběru exportu privátního klíče.

1. Zkopírujte tento soubor PFX do svého prostředí Linux.

1. Převést soubor PFX na soubor CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Zkopírujte soubor CRT do složky, která obsahuje vlastní certifikáty v distribuci systému Linux. V Debian distribucích se obvykle nachází na `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Aktualizujte certifikáty TLS/SSL, které budou aktualizovat `/etc/ssl/certs/` složku.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>prostředí macOS

Pokud pracujete na Macu, použijte následující postup:

1. [Exportujte certifikát ve formátu PFX](local-emulator-export-ssl-certificates.md#export-emulator-certificate). Možnost PFX je k dispozici při výběru exportu privátního klíče.

1. Zkopírujte tento soubor PFX do prostředí Mac.

1. Otevřete aplikaci pro *přístup k řetězci klíčů* a IMPORTUJTE soubor PFX.

1. Otevřete seznam certifikátů a Identifikujte ho názvem `localhost` .

1. Otevřete kontextovou nabídku pro tuto konkrétní položku, vyberte možnost *získat položku* a v části *důvěřovat*  >  *při použití tohoto certifikátu* vyberte možnost *vždy důvěřovat*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Vyberte tlačítko Start nebo stiskněte klávesu Windows, začněte psát emulátor Azure Cosmos a vyberte ze seznamu aplikací emulátor.":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Možnost 2: Zakázání ověřování SSL v aplikaci

Vypnutí ověřování SSL se doporučuje jenom pro účely vývoje a nemělo by se dělat při spuštění v produkčním prostředí. Následující příklady ukazují, jak zakázat ověřování SSL pro aplikace .NET a Node.js.

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

Pro všechny aplikace běžící v rozhraní, které je kompatibilní s .NET Standard 2,1 nebo novějším, můžeme využít tyto `CosmosClientOptions.HttpClientFactory` kroky:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2,0](#tab/ssl-netstd20)

Pro všechny aplikace běžící v rámci architektury kompatibilní s .NET Standard 2,0 můžeme využít tyto `CosmosClientOptions.HttpClientFactory` kroky:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

U Node.jsch aplikací můžete upravit `package.json` soubor pro nastavení `NODE_TLS_REJECT_UNAUTHORIZED` při spuštění aplikace:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Povolit přístup k emulátoru v místní síti

Pokud máte více počítačů pomocí jedné sítě, a pokud nastavíte emulátor na jednom počítači a chcete k němu přistupovat z jiného počítače. V takovém případě je nutné povolit přístup k emulátoru v místní síti.

Emulátor můžete spustit v místní síti. Pokud chcete povolit přístup k síti, zadejte na `/AllowNetworkAccess` [příkazovém řádku](emulator-command-line-parameters.md)možnost, která také vyžaduje, abyste zadali `/Key=key_string` nebo `/KeyFile=file_name` . Můžete použít `/GenKeyFile=file_name` k vygenerování souboru s náhodným klíčem. Pak můžete předat to `/KeyFile=file_name` nebo `/Key=contents_of_file` .

Pokud chcete povolit přístup k síti poprvé, měl by uživatel vypnout emulátor a odstranit adresář dat emulátoru *%localappdata%\CosmosDBEmulator*.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Ověřování připojení při použití emulátoru

Stejně jako u Azure Cosmos DB v cloudu musí být ověřeny všechny požadavky, které provedete v emulátoru Azure Cosmos. Emulátor Azure Cosmos podporuje pouze zabezpečenou komunikaci pomocí protokolu TLS. Emulátor Azure Cosmos podporuje jeden pevný účet a známý ověřovací klíč pro ověřování pomocí primárního klíče. Tento účet a klíč jsou jediné přihlašovací údaje povolené pro použití s emulátorem Azure Cosmos. Jsou to tyto:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Primární klíč podporovaný emulátorem Azure Cosmos je určený jenom pro použití s emulátorem. V emulátoru Azure Cosmos nemůžete použít svůj účet a klíč produkčního Azure Cosmos DB.

> [!NOTE]
> Pokud jste spustili emulátor s možností/Key, použijte místo výchozího klíče vygenerovaný klíč `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Další informace o možnosti/Key naleznete v tématu [Reference k nástroji příkazového řádku.](emulator-command-line-parameters.md)

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Připojení k různým rozhraním API pomocí emulátoru

### <a name="sql-api"></a>SQL API

Jakmile na ploše spustíte emulátor Azure Cosmos, můžete k interakci s emulátorem použít libovolnou podporovanou [sadu Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) nebo [Azure Cosmos DB REST API](/rest/api/cosmos-db/) . Emulátor Azure Cosmos obsahuje také integrovaný Průzkumník dat, který umožňuje vytvářet kontejnery pro rozhraní SQL API nebo Azure Cosmos DB pro rozhraní API služby Mongo DB. Pomocí Průzkumníka dat můžete zobrazit a upravit položky bez psaní kódu.

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Jakmile na ploše spustíte emulátor Azure Cosmos, můžete k interakci s emulátorem použít [rozhraní API Azure Cosmos DB pro MongoDB](mongodb-introduction.md) . Spusťte emulátor z [příkazového řádku](emulator-command-line-parameters.md) jako správce s názvem "/EnableMongoDbEndpoint". Pak použijte následující připojovací řetězec pro připojení k účtu rozhraní MongoDB API:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Rozhraní Table API

Jakmile na ploše spustíte emulátor Azure Cosmos, můžete k interakci s emulátorem použít [sadu SDK pro rozhraní API pro tabulky Azure Cosmos DB](table-storage-how-to-use-dotnet.md) . Spusťte emulátor z [příkazového řádku](emulator-command-line-parameters.md) jako správce s názvem "/EnableTableEndpoint". V dalším kroku spusťte následující kód pro připojení k účtu rozhraní Table API:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Rozhraní Cassandra API

Spusťte emulátor z [příkazového řádku](emulator-command-line-parameters.md) správce s názvem "/EnableCassandraEndpoint". Případně můžete také nastavit proměnnou prostředí `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` .

1. [Instalace Pythonu 2,7](https://www.python.org/downloads/release/python-2716/)

1. [Instalace rozhraní příkazového řádku Cassandra/CQLSH](https://cassandra.apache.org/download/)

1. V běžném okně příkazového řádku spusťte následující příkazy:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. V prostředí CQLSH spusťte následující příkazy pro připojení ke koncovému bodu Cassandra:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Rozhraní Gremlin API

Spusťte emulátor z [příkazového řádku](emulator-command-line-parameters.md)správce s názvem "/EnableGremlinEndpoint". Případně můžete také nastavit proměnnou prostředí. `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [Nainstalujte Apache-tinkerpop-Gremlin-Console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. V Průzkumníku dat emulátoru vytvořte databázi "DB1" a kolekci "coll1"; pro klíč oddílu vyberte "/Name".

1. V běžném okně příkazového řádku spusťte následující příkazy:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. V prostředí Gremlin spusťte následující příkazy pro připojení ke koncovému bodu Gremlin:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Odinstalace místního emulátoru

K odinstalaci emulátoru použijte následující postup:

1. Zavřete všechny otevřené instance místního emulátoru tak, že kliknete pravým tlačítkem na ikonu **emulátoru Azure Cosmos** na hlavním panelu systému a pak vyberete **ukončit**. Ukončení všech instancí může chvíli trvat.

1. Do vyhledávacího pole Windows zadejte **aplikace & funkce** a vyberte **aplikace & funkce (nastavení systému)** výsledek.

1. V seznamu aplikací se posuňte k **emulátoru Azure Cosmos DB**, vyberte ho, klikněte na **odinstalovat**, pak potvrďte a vyberte **odinstalovat** znovu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak používat místní emulátor k bezplatnému místnímu vývoji. Teď můžete přejít k dalším článkům:

* [Export certifikátů emulátoru Azure Cosmos pro použití s aplikacemi Java, Python a Node.js](local-emulator-export-ssl-certificates.md)
* [Použití parametrů příkazového řádku a příkazů prostředí PowerShell k řízení emulátoru](emulator-command-line-parameters.md)
* [Ladění problémů s emulátorem](troubleshoot-local-emulator.md)
