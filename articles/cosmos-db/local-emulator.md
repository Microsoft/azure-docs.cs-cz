---
title: Místní vývoj s využitím emulátor služby Azure Cosmos DB
description: Pomocí emulátoru služby Azure Cosmos DB můžete vyvinout a otestovat svou aplikaci místně zdarma bez vytvoření předplatného Azure.
services: cosmos-db
keywords: Emulátor služby Azure Cosmos DB
author: David-Noble-at-work
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: danoble
ms.openlocfilehash: 334396b99609ea52085e36ee2740583e0957c3a4
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600025"
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Použití emulátoru služby Azure Cosmos DB pro místní vývoj a testování

<table>
<tr>
  <td><strong>Binární soubory</strong></td>
  <td>[Stáhnout MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Centrum Dockeru](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Zdroj Dockeru</strong></td>
  <td>[GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>

Emulátor služby Azure Cosmos DB zajistí místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Pomocí emulátoru služby Azure Cosmos DB můžete vyvíjet a testovat aplikace místně bez vytváření předplatného Azure a bez jakýchkoli nákladů. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu.

V tuto chvíli Průzkumník dat v emulátoru plně podporuje pouze kolekce rozhraní SQL API a kolekce MongoDB. Kontejnery Table, Graph a Cassandra nejsou plně podporovány.

Tento článek se zabývá následujícími úkony:

> [!div class="checklist"]
> * Instalace emulátoru
> * Ověřování požadavků
> * Používání Průzkumníka dat v emulátoru
> * Export certifikátů SSL
> * Volání emulátoru z příkazového řádku
> * Spuštění emulátoru v aplikaci Docker for Windows
> * Shromažďování trasovacích souborů
> * Řešení potíží

## <a name="how-the-emulator-works"></a>Jak emulátor funguje

Emulátor služby Azure Cosmos DB s vysokou věrností emuluje službu Azure Cosmos DB. Podporuje identické funkce jako Azure Cosmos DB, včetně podpory pro vytváření a dotazování dokumentů JSON, zřizování a škálování kolekcí a spouštění uložených procedur a aktivačních událostí. V emulátoru služby Azure Cosmos DB můžete vyvíjet a testovat aplikace a potom je nasadit do Azure v globálním měřítku pouhou změnou konfigurace koncového bodu připojení služby Azure Cosmos DB.

Přestože je emulace služby Azure Cosmos DB věrná, implementace emulátoru se od služby liší. Emulátor například používá standardní součásti operačního systému, jako je místní systém souborů pro trvalost a sada protokolů HTTPS pro připojení. Funkce, které jsou závislé na infrastruktuře Azure, jako je globální replikace, latence pro čtení a zápis v řádu milisekund a nastavitelné úrovně konzistence, nejsou k dispozici.

## <a name="differences-between-the-emulator-and-the-service"></a>Rozdíly mezi emulátorem a službou
Vzhledem k tomu, že emulátor služby Azure Cosmos DB poskytuje emulované prostředí běžící na místní pracovní stanici vývojáře, existují některé rozdíly ve funkčnosti mezi emulátorem a účtem služby Azure Cosmos DB v cloudu:

* V současné době Průzkumník dat v emulátoru podporuje pouze kolekce rozhraní SQL API a kolekce MongoDB. Rozhraní API Table, Graph a Cassandra nejsou zatím podporovány.
* Emulátor služby Azure Cosmos DB podporuje pouze jeden pevný účet a známý hlavní klíč. Opětovné generování klíče není v emulátoru služby Azure Cosmos DB možné.
* Emulátor služby Azure Cosmos DB není škálovatelná služba a nebude podporovat velké množství kolekcí.
* Emulátor služby Azure Cosmos DB nepodporuje simulaci různých [úrovní konzistence služby Azure Cosmos DB](consistency-levels.md).
* Emulátor služby Azure Cosmos DB nepodporuje simulaci [replikace ve více oblastech](distribute-data-globally.md).
* Emulátor služby Azure Cosmos DB nepodporuje přepsání kvót služby, které jsou k dispozici ve službě Azure Cosmos DB (například omezení velikosti dokumentu nebo větší úložiště pro dělenou kolekci).
* Vzhledem k tomu, že vaše kopie emulátoru služby Azure Cosmos DB nemusí odrážet nejnovější změny služby Azure Cosmos DB, měli byste použít [plánovač kapacity služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner), abyste přesně odhadli provozní potřeby propustnosti (RU) vaší aplikace.

## <a name="system-requirements"></a>Systémové požadavky
Emulátor služby Azure Cosmos DB má následující požadavky na hardware a software:

* Požadavky na software
  * Windows Server 2012 R2, Windows Server 2016 nebo Windows 10
*   Minimální požadavky na hardware
  * 2 GB RAM
  * 10 GB volného místa na disku

## <a name="installation"></a>Instalace
Emulátor služby Azure Cosmos DB můžete stáhnout a nainstalovat z webu [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) nebo můžete emulátor spustit v aplikaci Docker for Windows. Pokyny týkající se použití emulátoru v aplikaci Docker for Windows najdete v části [Spuštění v Dockeru](#running-on-docker).

> [!NOTE]
> K instalaci, konfiguraci a spuštění emulátoru služby Azure Cosmos DB musíte mít v počítači oprávnění správce.

## <a name="running-on-windows"></a>Spuštění v systému Windows

Pokud chcete spustit emulátor služby Azure Cosmos DB, klikněte na tlačítko Start nebo stiskněte klávesu Windows. Začněte psát **Azure Cosmos DB Emulator** a vyberte emulátor v seznamu aplikací.

![Klikněte na tlačítko Start nebo stiskněte klávesu Windows, začněte psát **Azure Cosmos DB Emulator** a vyberte emulátor v seznamu aplikací.](./media/local-emulator/database-local-emulator-start.png)

Po spuštění emulátoru se v oznamovací oblasti hlavního panelu Windows zobrazí jeho ikona. ![Oznamovací oblast na hlavním panelu s ikonou místního emulátoru služby Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

Emulátor služby Azure Cosmos DB se ve výchozím nastavení spouští v místním počítači („localhost“) a naslouchá na portu 8081.

Emulátor služby Azure Cosmos DB je ve výchozím nastavení nainstalovaný v adresáři `C:\Program Files\Azure Cosmos DB Emulator`. Emulátor můžete také spustit a zastavit z příkazového řádku. Další informace najdete v [referenčních informacích k nástroji příkazového řádku](#command-line).

## <a name="start-data-explorer"></a>Spuštění Průzkumníka dat

Při spuštění emulátoru služby Azure Cosmos DB se v prohlížeči automaticky otevře Průzkumník dat služby Azure Cosmos DB. Adresa se zobrazí jako [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Pokud Průzkumníka zavřete a chcete ho později znovu otevřít, můžete otevřít adresu URL v prohlížeči nebo Průzkumníka spustit z emulátoru služby Azure Cosmos DB prostřednictvím ikony na hlavním panelu Windows, jak je zobrazeno níže.

![Spouštěč Průzkumníka dat v místním emulátoru služby Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Kontrola aktualizací
Průzkumník dat zjistí, zda je k dispozici nová aktualizace ke stažení.

> [!NOTE]
> Data vytvořená ve verzi emulátoru služby Azure Cosmos DB nemusí být dostupná při použití jiné verze. Pokud potřebujete data uchovat dlouhodobě, doporučujeme je uložit v účtu služby Azure Cosmos DB, nikoli v emulátoru služby Azure Cosmos DB.

## <a name="authenticating-requests"></a>Ověřování požadavků
Stejně jako u Azure Cosmos DB v cloudu musí být ověřen každý požadavek pro emulátor služby Azure Cosmos DB. Emulátor služby Azure Cosmos DB podporuje jeden pevný účet a známý hlavní ověřovací klíč. Tento účet a klíč jsou jediné přihlašovací údaje, které je možné použít s emulátorem služby Azure Cosmos DB. Jsou to tyto:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Pro použití s emulátorem je určen pouze hlavní klíč, který podporuje emulátor služby Azure Cosmos DB. S emulátorem služby Azure Cosmos DB nemůžete používat svůj provozní účet a klíč služby Azure Cosmos DB.

> [!NOTE]
> Pokud jste emulátor spustili pomocí možnosti /Key, použijte vygenerovaný klíč namísto klíče „C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==“.

Stejně jako služba Azure Cosmos DB i emulátor podporuje pouze zabezpečenou komunikaci přes SSL.

## <a name="running-on-a-local-network"></a>Spuštění v místní síti

Emulátor můžete spustit v místní síti. Pokud chcete povolit přístup k síti, zadejte na [příkazovém řádku](#command-line-syntax) možnost /AllowNetworkAccess, což také vyžaduje, abyste zadali možnost /Key = řetězec_klíče nebo /KeyFile = název_souboru. Pomocí možnosti /GenKeyFile = název_souboru si můžete předem vygenerovat soubor s náhodným klíčem. Potom ho můžete předat možnosti /KeyFile = název_souboru nebo /Key = obsah_souboru.

Jestliže chcete povolit přístup k síti poprvé, měli byste vypnout emulátor a vymazat datový adresář emulátoru (C:\ Users\uživatelské_jméno\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Vývoj v emulátoru
Jakmile máte v počítači spuštěný emulátor služby Azure Cosmos DB, můžete k interakci s emulátorem použít libovolnou podporovanou sadu [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) nebo rozhraní [REST API služby Azure Cosmos DB](/rest/api/cosmos-db/). Emulátor služby Azure Cosmos DB také zahrnuje integrované Průzkumník dat, která umožňuje vytvářet kolekce pro rozhraní API Azure Cosmos DB pro SQL, MongoDB a zobrazení a úpravám dokumentů, aniž byste museli psát jakýkoli kód.

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"),
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Pokud používáte [podporu protokolů služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md), použijte následující připojovací řetězec:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Pro připojení k emulátoru služby Azure Cosmos DB můžete použít stávající nástroje, například [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio). Můžete také migrovat data mezi emulátorem služby Azure Cosmos DB a službou Azure Cosmos DB pomocí [nástroje pro migraci dat Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE]
> Pokud jste emulátor spustili pomocí možnosti /Key, použijte vygenerovaný klíč namísto klíče „C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==“.

Pomocí emulátoru služby Azure Cosmos DB můžete ve výchozím nastavení vytvořit až 25 kolekcí s jedním oddílem nebo 1 dělenou kolekci. Další informace o změně této hodnoty najdete v části [Nastavení hodnoty PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Export certifikátu SSL

Jazyky a moduly runtime rozhraní .NET používají k bezpečnému připojení k místnímu emulátoru služby Azure Cosmos DB úložiště certifikátů systému Windows. Další jazyky mají vlastní metody správy a použití certifikátů. Java používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), zatímco Python používá [obálky soketu](https://docs.python.org/2/library/ssl.html).

Pokud chcete získat certifikát pro použití s jazyky a moduly runtime, které se neintegrují s úložištěm certifikátů systému Windows, budete ho muset exportovat pomocí Správce certifikátů systému Windows. Můžete ho spustit tak, že spustíte certlm.msc, nebo postupujte podle podrobných pokynů v tématu pojednávajícím o [exportu certifikátů emulátoru služby Azure Cosmos DB](./local-emulator-export-ssl-certificates.md). Jakmile je správce certifikátů spuštěn, otevřete osobní certifikáty, jak je zobrazeno níže, a exportujte certifikát s popisným názvem „DocumentDBEmulatorCertificate“ jako soubor X.509 (.cer) s kódováním BASE-64.

![Certifikát SSL místního emulátoru služby Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Certifikát X.509 lze importovat do úložiště certifikátů Javy podle pokynů v tématu o [přidání certifikátu do úložiště certifikátů certifikační autority Javy](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Jakmile je certifikát importován do úložiště certifikátů, budou se aplikace Javy a MongoDB moci připojit k emulátoru služby Azure Cosmos DB.

Při připojování k emulátoru ze sad SDK Pythonu a Node.js je zakázáno ověřování SSL.

## <a id="command-line"></a>Reference nástroje příkazového řádku
Z umístění instalace můžete pomocí příkazového řádku spustit a zastavit emulátor, konfigurovat možnosti a provádět další operace.

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `CosmosDB.Emulator.exe /?`.

<table>
<tr>
  <td><strong>Možnost</strong></td>
  <td><strong>Popis</strong></td>
  <td><strong>Příkaz</strong></td>
  <td><strong>Argumenty</strong></td>
</tr>
<tr>
  <td>[Žádné argumenty]</td>
  <td>Spustí emulátor služby Azure Cosmos DB s výchozím nastavením.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Nápověda]</td>
  <td>Zobrazí seznam podporovaných argumentů příkazového řádku.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Získá stav emulátoru služby Azure Cosmos DB. Stav je indikován ukončovací kód: 1 = od, 2 = spuštěný, 3 = zastavena. Záporný ukončovací kód označuje, že došlo k chybě. Žádný jiný výstup neexistuje.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Shutdown</td>
  <td>Ukončí emulátor služby Azure Cosmos DB.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Určuje cestu, do které chcete uložit datové soubory. Výchozí hodnota je %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;cesta k datům&gt;</td>
  <td>&lt;DataPath&gt;: Přístupná cesta</td>
</tr>
<tr>
  <td>Port</td>
  <td>Určuje číslo portu pro emulátor. Výchozí hodnota je 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;Port&gt;: Jeden port číslo</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Určuje číslo portu, který chcete použít pro rozhraní API kompatibility MongoDB. Výchozí hodnota je 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongo port&gt;</td>
  <td>&lt;mongoport&gt;: Jeden port číslo</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Určuje porty, které chcete použít pro přímé připojení. Výchozí hodnoty jsou 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;přímé porty&gt;</td>
  <td>&lt;directports&gt;: Čárkami oddělený seznam portů 4</td>
</tr>
<tr>
  <td>Klíč</td>
  <td>Autorizační klíč pro emulátor. Klíč musí být 64bajtový vektor s kódováním base-64.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;klíč&gt;</td>
  <td>&lt;Klíč&gt;: Klíč musí být v kódování base-64 vektoru 64 bajtů</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Určuje, že je povoleno chování omezující četnost požadavků.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Určuje, že je zakázáno chování omezující četnost požadavků.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Nezobrazuje uživatelské rozhraní emulátoru.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Nezobrazuje Průzkumníka dat při spuštění.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Určuje maximální počet dělených kolekcí. Další informace získáte v části [Změna počtu kolekcí](#set-partitioncount).</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;počet oddílů&gt;</td>
  <td>&lt;partitioncount&gt;: Maximální počet povolených jednoho oddílu kolekce. Výchozí hodnota je 25. Maximální povolený počet je 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Určuje výchozí počet oddílů pro dělenou kolekci.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;výchozí počet oddílů&gt;</td>
  <td>&lt;výchozí počet oddílů&gt;: výchozí hodnota je 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Povolí přístup k emulátoru přes síť. Pokud chcete povolit přístup k síti, je nutné předat taky možnosti /Key =&lt;řetězec_klíče&gt; nebo/KeyFile =&lt;název_souboru&gt;.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;řetězec_klíče&gt;<br><br>nebo<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;název_souboru&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Při použití možnosti /AllowNetworkAccess nejsou upravována pravidla brány firewall.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Vygeneruje nový autorizační klíč a uloží ho do zadaného souboru. Generovaný klíč lze použít s možností /Key nebo/KeyFile.</td>
  <td>CosmosDB.Emulator.exe /GenKeyFile =&lt;cestu k souboru klíče&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Konzistence</td>
  <td>Nastaví výchozí úroveň konzistence pro účet.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;konzistence&gt;</td>
  <td>&lt;Konzistence&gt;: Hodnota musí být jedna z následujících [úrovně konzistence](consistency-levels.md): Relace, silné, konečný výsledek, nebo BoundedStaleness. Výchozí hodnota je Session.</td>
</tr>
<tr>
  <td>?</td>
  <td>Zobrazí zprávu nápovědy.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Změna počtu kolekcí

Pomocí emulátoru služby Azure Cosmos DB můžete ve výchozím nastavení vytvořit až 25 kolekcí s jedním oddílem nebo 1 dělenou kolekci. Změnou hodnoty **PartitionCount** můžete vytvořit až 250 kolekcí s jedním oddílem nebo 10 dělených kolekcí nebo libovolnou kombinaci, která nepřekračuje 250 jednotlivých oddílů (kde jedna dělená kolekce = 25 kolekcí s jedním oddílem).

Pokud se pokusíte vytvořit kolekci po překročení aktuálního počtu oddílů, emulátor vyvolá výjimku ServiceUnavailable s následující zprávou.

    Sorry, we are currently experiencing high demand in this region,
    and cannot fulfill your request at this time. We work continuously
    to bring more and more capacity online, and encourage you to try again.
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Pokud chcete změnit počet kolekcí, které jsou k dispozici pro emulátor služby Azure Cosmos DB, postupujte takto:

1. Odstraňte všechna místní data emulátoru služby Azure Cosmos DB tak, že kliknete pravým tlačítkem myši na ikonu **emulátoru služby Azure Cosmos DB** na hlavním panelu systému a potom kliknete na **Reset Data...** (Obnovit data).
2. Odstraňte všechna data emulátoru ve složce C:\Users\uživatelské_jméno\AppData\Local\CosmosDBEmulator.
3. Ukončete všechny otevřené instance tak, že kliknete pravým tlačítkem myši na ikonu **emulátoru služby Azure Cosmos DB** na hlavním panelu systému a potom kliknete na **Exit** (Konec). Ukončení všech instancí může chvíli trvat.
4. Nainstalujte nejnovější verzi [emulátoru služby Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).
5. Spusťte emulátor s příznakem PartitionCount nastaveným na hodnotu < = 250. Například: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Řízení emulátoru

Emulátor se dodává s modulem PowerShell, který umožňuje spustit, zastavit, odinstalovat a načíst stav této služby. Pokud ho chcete použít:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

nebo zadejte adresář `PSModules` do cesty `PSModulesPath` a naimportujte ho takto:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Zde je uveden seznam příkazů pro řízení emulátoru z PowerShellu:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Syntaxe

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Poznámky

Vrátí jednu z těchto hodnot ServiceControllerStatus: ServiceControllerStatus.StartPending ServiceControllerStatus.Running či ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Syntaxe

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

#### <a name="remarks"></a>Poznámky

Spustí emulátor. Ve výchozím nastavení tento příkaz čeká, dokud emulátor nebude připraven přijímat požadavky. Pokud chcete, aby se rutina vrátila, jakmile spustí emulátor, použijte možnost -NoWait.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Syntaxe

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Poznámky

Zastaví emulátor. Ve výchozím nastavení tento příkaz čeká, až emulátor je zcela vypnutý. Pokud chcete, aby se rutina vrátila, jakmile se emulátor začne vypínat, použijte možnost -NoWait.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Syntaxe

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Poznámky

Odinstaluje emulátor a volitelně odstraní úplný obsah $env:LOCALAPPDATA\CosmosDbEmulator.
Rutina zajišťuje zastavení emulátoru před jeho odinstalací.

## <a name="running-on-docker"></a>Spuštění v Dockeru

Emulátor služby Azure Cosmos DB můžete spustit v aplikaci Docker for Windows. Emulátor nefunguje v aplikaci Docker for Oracle Linux.

Jakmile [Docker for Windows](https://www.docker.com/docker-windows) nainstalujete, přepněte na kontejnery Windows tak, že kliknete pravým tlačítkem myši na ikonu Dockeru na panelu nástrojů a vyberete **Switch to Windows containers** (Přepnout na kontejnery Windows).

Potom si spuštěním následujícího příkazu z vašeho oblíbeného prostředí stáhněte image emulátoru z Centra Dockeru.

```
docker pull microsoft/azure-cosmosdb-emulator
```
Pokud chcete image spustit, spusťte následující příkazy.

Z příkazového řádku:
```cmd
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator
```

Z PowerShellu:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator
```

Odpověď vypadá podobně jako následující zpráva:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Nyní použijte koncový bod a hlavní klíč z odpovědi ve svém klientovi a importujte certifikát SSL do svého hostitele. Pokud chcete importovat certifikát SSL, spusťte následující příkazy z příkazového řádku správce:

Z příkazového řádku:
```cmd
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Z PowerShellu:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Zavřením interaktivního prostředí po spuštění emulátoru se ukončí kontejner emulátoru.

Pokud chcete otevřít Průzkumníka dat, přejděte v prohlížeči na následující adresu URL. Koncový bod emulátoru je uveden ve zprávě s odpovědí uvedené výše.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Řešení potíží

Následující tipy použijte k řešení potíží s emulátorem služby Azure Cosmos DB:

- Pokud jste nainstalovali novou verzi emulátoru a dochází k chybám, proveďte obnovení dat. Data obnovíte tak, že kliknete pravým tlačítkem myši na ikonu emulátoru služby Azure Cosmos DB na hlavním panelu systému a potom kliknete na Reset Data (Obnovit data). Pokud se takto chyby neopraví, můžete odinstalovat a znovu nainstalovat aplikaci. Pokyny najdete v části [Odinstalace místního emulátoru](#uninstall).

- Pokud se emulátor služby Azure Cosmos DB chybově ukončuje, shromážděte soubory výpisu stavu systému ve složce c:\Users\uživatelské_jméno\AppData\Local\CrashDumps, zkomprimujte je a připojte je k e-mailu, který odešlete na adresu [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Pokud dojde k selhání v programu CosmosDB.StartupEntryPoint.exe, spusťte z příkazového řádku správce následující příkaz: `lodctr /R`

- Pokud dojde k potížím s připojením, [shromážděte trasovací soubory](#trace-files), zkomprimujte je a připojte je k e-mailu, který odešlete na adresu [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Pokud se zobrazí zpráva **Služba není dostupná**, pravděpodobně se emulátoru nedaří inicializovat sadu síťových protokolů. Zkontrolujte, zda máte nainstalovaného klienta Pulse Secure nebo klienta Juniper Networks, protože potíže mohou způsobovat jejich ovladače síťových filtrů. Odinstalace ovladačů síťových filtrů třetích stran obvykle potíže vyřeší.

- Pokud emulátor běží, když počítač přechází do režimu spánku nebo instaluje nějaké aktualizace operačního systému, může se zobrazit zpráva, že **služba momentálně není dostupná**. Kliknutím pravým tlačítkem na ikonu, která se zobrazí v oznamovací oblasti Windows, a výběrem možnosti **Obnovit data** obnovte emulátor.

### <a id="trace-files"></a>Shromažďování trasovacích souborů

Pokud chcete shromažďovat trasovací soubory pro ladění, spusťte z příkazového řádku pro správu následující příkazy:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Sledujte hlavní panel systému a ujistěte se, že program je vypnutý. Může to chvíli trvat. Můžete také jen kliknout na **Exit** (Konec) v uživatelském rozhraní emulátoru služby Azure Cosmos DB.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reprodukujte problém. Pokud Průzkumník dat nefunguje, stačí několik sekund čekat na otevření prohlížeče a zachytit chybu.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Přejděte do složky `%ProgramFiles%\Azure Cosmos DB Emulator` a vyhledejte soubor docdbemulator_000001.etl.
7. Odešlete soubor .etl spolu s kroky pro zopakování potíží k ladění na adresu [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a id="uninstall"></a>Odinstalace místního emulátoru

1. Kliknutím pravým tlačítkem na ikonu emulátoru služby Azure Cosmos DB v oznamovací oblasti a následným kliknutím na Exit (Konec) ukončete všechny otevřené instance místního emulátoru. Ukončení všech instancí může chvíli trvat.
2. Do vyhledávacího pole ve Windows zadejte **Programy a funkce** a klikněte na výsledek **Programy a funkce (nastavení systému)**.
3. V seznamu aplikací se posuňte na položku **Azure Cosmos DB Emulator**, vyberte ji, klikněte na **Odinstalovat**, potvrďte a znovu klikněte na **Odinstalovat**.
4. Když je aplikace odinstalovaná, přejděte do složky `C:\Users\<user>\AppData\Local\CosmosDBEmulator` a odstraňte ji.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Odinstalace místního emulátoru
> * Spuštění emulátoru v aplikaci Docker for Windows
> * Ověření požadavků
> * Používání Průzkumníka dat v emulátoru
> * Export certifikátů SSL
> * Volání emulátoru z příkazového řádku
> * Shromažďování trasovacích souborů

V tomto kurzu jste zjistili, jak používat místní emulátor pro bezplatný místní vývoj. Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak exportovat certifikáty SSL emulátoru.

> [!div class="nextstepaction"]
> [Export certifikátů emulátoru služby Azure Cosmos DB](local-emulator-export-ssl-certificates.md)
