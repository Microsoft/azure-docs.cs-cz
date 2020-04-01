---
title: Použití emulátoru úložiště Azure pro vývoj a testování | Dokumenty společnosti Microsoft
description: Emulátor úložiště Azure poskytuje bezplatné prostředí místního vývoje pro vývoj a testování aplikací Azure Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 997aa9d96f2f52331865fd15d97443d74bb8bc1f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398015"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Použití emulátoru úložiště Azure pro vývoj a testování

Emulátor úložiště Microsoft Azure je nástroj, který emuluje služby Azure Blob, Queue a Table pro účely místního vývoje. Aplikaci můžete otestovat na místně služby úložiště bez vytvoření předplatného Azure nebo vzniklé žádné náklady. Až budete spokojeni s tím, jak vaše aplikace funguje v emulátoru, přepněte na používání účtu úložiště Azure v cloudu.

## <a name="get-the-storage-emulator"></a>Získání emulátoru úložiště

Emulátor úložiště je k dispozici jako součást [sady Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště můžete nainstalovat také pomocí [samostatného instalačního programu](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (přímé stahování). Chcete-li nainstalovat emulátor úložiště, musíte mít v počítači oprávnění správce.

Emulátor úložiště aktuálně běží pouze v systému Windows. Pokud potřebujete emulátor úložiště pro Linux, jednou z možností je komunita udržuje, open-source storage emulátor [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Data vytvořená v jedné verzi emulátoru úložiště není zaručeno, že budou přístupná při použití jiné verze. Pokud potřebujete zůstat data dlouhodobě, doporučujeme uložit tato data v účtu úložiště Azure, nikoli v emulátoru úložiště.
> 
> Emulátor úložiště závisí na konkrétních verzích knihoven OData. Nahrazení knihovny DLL OData používané emulátorem úložiště jinými verzemi není podporováno a může způsobit neočekávané chování. Však všechny verze OData podporované službou úložiště však mohou být použity k odesílání požadavků emulátoru.

## <a name="how-the-storage-emulator-works"></a>Jak funguje emulátor úložiště

Emulátor úložiště používá k emulaci služeb úložiště Azure místní databázi Microsoft SQL Server 2012 Express. Můžete nakonfigurovat emulátor úložiště pro přístup k místní instanci serveru SQL Server namísto instance LocalDB. Další informace najdete v [tématu Start a inicializovat emulátor úložiště](#start-and-initialize-the-storage-emulator) dále v tomto článku.

Emulátor úložiště se připojuje k serveru SQL Server nebo LocalDB pomocí ověřování systému Windows.

Mezi emulátorem úložiště a službami úložiště Azure existují určité rozdíly ve funkčnosti. Další informace o těchto rozdílech najdete [v tématu rozdíly mezi emulátorúložiště a Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) části dále v tomto článku.

## <a name="start-and-initialize-the-storage-emulator"></a>Spuštění a inicializaci emulátoru úložiště

Spuštění emulátoru úložiště Azure:

1. Vyberte tlačítko **Start** nebo stiskněte klávesu **Windows.**
2. Začněte `Azure Storage Emulator`psát .
3. Vyberte emulátor ze seznamu zobrazených aplikací.

Po spuštění emulátoru úložiště se zobrazí okno příkazového řádku. Toto okno konzoly můžete použít ke spuštění a zastavení emulátoru úložiště. Můžete také vymazat data, získat stav a inicializovat emulátor z příkazového řádku. Další informace naleznete v části odkaz na [nástroj příkazového řádku emulátoru úložiště](#storage-emulator-command-line-tool-reference) dále v tomto článku.

> [!NOTE]
> Emulátor úložiště Azure se nemusí spustit správně, pokud v systému běží jiný emulátor úložiště, jako je například Azurite.

Po spuštění emulátoru se v oznamovací oblasti hlavního panelu Windows zobrazí jeho ikona.

Po zavření okna příkazového řádku emulátoru úložiště bude emulátor úložiště nadále spuštěn. Chcete-li znovu vyvolat okno konzoly emulátoru úložiště, postupujte podle předchozích kroků, jako by bylo možné spouštět emulátor úložiště.

Při prvním spuštění emulátoru úložiště je pro vás inicializováno místní prostředí úložiště. Proces inicializace vytvoří databázi v LocalDB a rezervuje http porty pro každou službu místního úložiště.

Emulátor úložiště je ve výchozím `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`nastavení nainstalován na .

> [!TIP]
> [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com) můžete použít k práci s prostředky emulátoru místního úložiště. Vyhledejte "(Emulátor - výchozí porty) (Klíč)" v části "Místní & připojeno" ve stromu prostředků Průzkumníka úložiště po instalaci a spuštění emulátoru úložiště.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializovat emulátor úložiště pro použití jiné databáze SQL

Pomocí nástroje příkazového řádku emulátoru úložiště můžete inicializovat emulátor úložiště tak, aby ukazoval na jinou instanci databáze SQL než výchozí instanci LocalDB:

1. Otevřete okno konzoly Emulátor úložiště, jak je popsáno v [části Start, a inicializujte emulátor úložiště.](#start-and-initialize-the-storage-emulator)
1. V okně konzoly zadejte následující `<SQLServerInstance>` příkaz, kde je název instance serveru SQL Server. Chcete-li použít `(localdb)\MSSQLLocalDb` LocalDB, zadejte jako instanci serveru SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Můžete také použít následující příkaz, který nasměruje emulátor použít výchozí instanci serveru SQL Server:

   `AzureStorageEmulator.exe init /server .`

   Nebo můžete použít následující příkaz, který znovu inicializuje databázi na výchozí instanci LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Další informace o těchto příkazech naleznete v [tématu Odkaz na nástroj příkazového řádku emulátoru úložiště](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Ke správě instancí serveru SQL Server, včetně instalace LocalDB, můžete použít sadu [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). V dialogovém okně SMSS `(localdb)\MSSQLLocalDb` Připojit k **serveru** zadejte do pole Název **serveru:** pro připojení k instanci LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Ověřování požadavků proti emulátoru úložiště

Po instalaci a spuštění emulátoru úložiště, můžete otestovat kód proti němu. Každý požadavek, který provedete proti emulátoru úložiště, musí být autorizován, pokud se nejedná o anonymní požadavek. Můžete autorizovat požadavky proti emulátoru úložiště pomocí ověřování pomocí sdíleného klíče nebo pomocí sdíleného přístupového podpisu (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizace pomocí přihlašovacích údajů ke sdílenému klíči

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o připojovacích řetězcích [najdete v tématu Konfigurace připojovacích řetězců úložiště Azure](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizace pomocí sdíleného přístupového podpisu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Některé klientské knihovny úložiště Azure, jako je například knihovna Xamarin, podporují pouze ověřování pomocí tokenu sdíleného přístupového podpisu (SAS). Token SAS můžete vytvořit pomocí [Průzkumníka úložiště](https://storageexplorer.com/) nebo jiné aplikace, která podporuje ověřování pomocí sdíleného klíče.

Token SAS můžete také generovat pomocí Azure PowerShellu. Následující příklad generuje token SAS s úplnými oprávněními pro kontejner objektů blob:

1. Pokud jste to ještě neudělali (doporučujeme nainstalovat Azure PowerShell (pomocí nejnovější verze rutin Azure PowerShellu). Pokyny k instalaci najdete [v tématu Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-Az-ps).
2. Otevřete Azure PowerShell a spusťte `CONTAINER_NAME` následující příkazy a nahraďte název, který si vyberete:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Výsledný identifikátor URI sdíleného přístupového podpisu pro nový kontejner by měl být podobný:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sdílený přístupový podpis vytvořený v tomto příkladu je platný po dobu jednoho dne. Podpis uděluje úplný přístup (čtení, zápis, odstranění, seznam) k objektům BLOB v kontejneru.

Další informace o sdílených přístupových podpisech najdete v tématu [Udělení omezeného přístupu k prostředkům úložiště Azure pomocí sdílených přístupových podpisů (SAS).](storage-sas-overview.md)

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresování prostředků v emulátoru úložiště

Koncové body služby pro emulátor úložiště se liší od koncových bodů pro účet úložiště Azure. Místní počítač neprovede překlad názvů domén, což vyžaduje, aby koncové body emulátoru úložiště byly místní adresy.

Když řešíte prostředek v účtu úložiště Azure, použijete následující schéma. Název účtu je součástí názvu hostitele URI a adresovaný prostředek je součástí cesty identifikátoru URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Například následující identifikátor URI je platná adresa pro objekt blob v účtu úložiště Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Vzhledem k tomu, že místní počítač neprovádí překlad názvů domén, je název účtu součástí cesty URI namísto názvu hostitele. Pro prostředek v emulátoru úložiště použijte následující formát URI:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Pro přístup k objektu blob v emulátoru úložiště může být například použita následující adresa:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Koncové body služby pro emulátor úložiště jsou:

* Služba objektů blob:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Služba fronty:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Stolní servis:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Řešení sekundárního účtu pomocí RA-GRS

Počínaje verzí 3.1 podporuje emulátor úložiště geograficky redundantní replikaci pro čtení (RA-GRS). K sekundárnímu umístění můžete přistupovat připojením -secondary k názvu účtu. Například následující adresa může být použita pro přístup k objektu blob pomocí sekundární ho teprve pro čtení v emulátoru úložiště:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Pro programový přístup k sekundárnímu emulátoru úložiště použijte klientskou knihovnu úložiště pro rozhraní .NET verze 3.2 nebo novější. Podrobnosti najdete v [klientské knihovně úložiště Microsoft Azure.](https://msdn.microsoft.com/library/azure/dn261237.aspx)
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Odkaz na nástroj příkazového řádku emulátoru úložiště

Počínaje verzí 3.0 se při spuštění emulátoru úložiště zobrazí okno konzoly. Pomocí příkazového řádku v okně konzoly spusťte a zastavte emulátor. Můžete také dotaz na stav a dělat další operace z příkazového řádku.

> [!NOTE]
> Pokud máte nainstalovaný výpočetní emulátor Microsoft Azure, zobrazí se při spuštění emulátoru úložiště ikona na hlavním panelu. Kliknutím pravým tlačítkem myši na ikonu zobrazíte nabídku, která poskytuje grafický způsob spuštění a zastavení emulátoru úložiště.
>
>

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Možnosti

Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `/help`.

| Možnost | Popis | Příkaz | Argumenty |
| --- | --- | --- | --- |
| **Zahájení** |Spustí emulátor úložiště. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess*: Spusťte emulátor v aktuálním procesu namísto vytvoření nového procesu. |
| **Zastavit** |Zastaví emulátor úložiště. |`AzureStorageEmulator.exe stop` | |
| **Stav** |Vytiskne stav emulátoru úložiště. |`AzureStorageEmulator.exe status` | |
| **Vymazat** |Vymaže data ve všech službách určených na příkazovém řádku. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*objekt blob*: Vymaže data objektu blob. <br/>*fronta*: Vymaže data fronty. <br/>*tabulka*: Vymaže data tabulky. <br/>*vše*: Vymaže všechna data ve všech službách. |
| **Init** |Má jednorázovou inicializaci nastavení emulátoru. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Určuje server hostující instanci SQL. <br/>*-sqlinstance instanceName*: Určuje název instance SQL, která má být použita ve výchozí instanci serveru. <br/>*-forcecreate*: Vynutí vytvoření databáze SQL, i když již existuje. <br/>*-skipcreate*: Přeskočí vytvoření databáze SQL. To má přednost před -forcecreate.<br/>*-reserveports*: Pokusy o rezervaci http portů přidružených ke službám.<br/>*-unreserveports*: Pokusy o odebrání rezervací pro http porty přidružené ke službám. To má přednost před -reserveports.<br/>*-inprocess*: Provádí inicializaci v aktuálním procesu namísto tření nového procesu. Aktuální proces musí být spuštěn se zvýšenými oprávněními, pokud změna rezervace portů. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Rozdíly mezi emulátorem úložiště a úložištěm Azure

Vzhledem k tomu, že emulátor úložiště je místní emulované prostředí, existují rozdíly mezi použitím emulátoru a účtu úložiště Azure v cloudu:

* Emulátor úložiště podporuje pouze jeden pevný účet a známý ověřovací klíč.
* Emulátor úložiště není škálovatelná služba úložiště a nepodporuje velký počet souběžných klientů.
* Jak je popsáno v [adresování prostředků v emulátoru úložiště](#addressing-resources-in-the-storage-emulator), prostředky jsou adresovány odlišně v emulátoru úložiště oproti účtu úložiště Azure. Rozdíl je v tom, že překlad názvů domén je k dispozici v cloudu, ale ne v místním počítači.
* Počínaje verzí 3.1 podporuje účet emulátoru úložiště geograficky redundantní replikaci pro čtení (RA-GRS). V emulátoru mají všechny účty povolenou funkci RA-GRS a mezi primární a sekundární replikou nikdy není žádné zpoždění. Získat statistiky služby blob, získat statistiky služby fronty a získat statistiky služby tabulky jsou `LastSyncTime` podporovány na sekundární účet a vždy vrátí hodnotu prvku odpovědi jako aktuální čas podle podkladové databáze SQL.
* V emulátoru úložiště nejsou aktuálně podporovány koncové body služby Soubor a protokol protokolu SMB.
* Pokud používáte verzi služby úložiště, která není podporována emulátorem, emulátor vrátí versionNotSupportedByEmulator chyba (stavový kód HTTP 400 - Bad Request).

### <a name="differences-for-blob-storage"></a>Rozdíly pro úložiště objektů Blob

Následující rozdíly platí pro úložiště objektů Blob v emulátoru:

* Emulátor úložiště podporuje jenom velikost objektů blob až 2 GB.
* Maximální délka názvu objektu blob v emulátoru úložiště je 256 znaků, zatímco maximální délka názvu objektu blob ve službě Azure Storage je 1024 znaků.
* Přírůstková kopie umožňuje zkopírovat snímky z přepsaných objektů BLOB, což vrátí selhání služby.
* Získat rozsahy stránek diff nefunguje mezi snímky zkopírované pomocí přírůstkové kopírování objektu blob.
* Operace Objektu blob put může být úspěšná proti objektu blob, který existuje v emulátoru úložiště s aktivní zapůjčení i v případě, že ID zapůjčení nebyl zadán v požadavku.
* Připojit operace objektu blob nejsou podporovány emulátorem. Při pokusu o operaci na objekt blob připojit vrátí featureNotSupportedByEmulator chyba (stavový kód HTTP 400 - chybný požadavek).

### <a name="differences-for-table-storage"></a>Rozdíly pro úložiště tabulek

Následující rozdíly platí pro table storage v emulátoru:

* Vlastnosti data ve službě Table ve službě emulátor úložiště podporují pouze rozsah podporovaný sql serverem 2005 (musí být pozdější než 1. ledna 1753). Všechna data před 1. Přesnost dat je omezena na přesnost SQL Server 2005, což znamená, že data jsou přesné na 1/300th sekundy.
* Emulátor úložiště podporuje hodnoty vlastností klíče oddílu a řádku méně než 512 bajtů. Celková velikost názvu účtu, názvu tabulky a názvů klíčových vlastností dohromady nesmí překročit 900 bajtů.
* Celková velikost řádku v tabulce v emulátoru úložiště je omezena na méně než 1 MB.
* V emulátoru úložiště vlastnosti `Edm.Guid` datového typu nebo `Edm.Binary` podporují pouze `Equal (eq)` operátory porovnání a `NotEqual (ne)` v řetězecích filtrů dotazu.

### <a name="differences-for-queue-storage"></a>Rozdíly pro úložiště fronty

Neexistují žádné rozdíly specifické pro úložiště fronty v emulátoru.

## <a name="storage-emulator-release-notes"></a>Poznámky k verzi emulátoru úložiště

### <a name="version-510"></a>Verze 5.10

* Emulátor úložiště nevymítne verzi 2019-07-07 služeb úložiště v koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-59"></a>Verze 5.9

* Emulátor úložiště nevymítne verzi 2019-02-02 služeb úložiště v koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-58"></a>Verze 5.8

* Emulátor úložiště nevymítne verzi 2018-11-09 služeb úložiště v koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-57"></a>Verze 5.7

* Opravena chyba, která by způsobila selhání, pokud by bylo povoleno protokolování.

### <a name="version-56"></a>Verze 5.6

* Emulátor úložiště nyní podporuje verzi 2018-03-28 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-55"></a>Verze 5.5

* Emulátor úložiště nyní podporuje verzi 2017-11-09 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.
* Byla přidána podpora pro vlastnost **Vytvořeno** objektu blob, která vrací čas vytvoření objektu blob.

### <a name="version-54"></a>Verze 5.4

* Chcete-li zlepšit stabilitu instalace, emulátor se již nepokouší rezervovat porty v době instalace. Pokud chcete rezervace portů, použijte *-reserveports* možnost **init** příkaz uzadat.

### <a name="version-53"></a>Verze 5.3

* Emulátor úložiště nyní podporuje verzi 2017-07-29 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-52"></a>Verze 5.2

* Emulátor úložiště nyní podporuje verzi 2017-04-17 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.
* Opravena chyba, kdy nebyly správně kódovány hodnoty vlastností tabulky.

### <a name="version-51"></a>Verze 5.1

* Opravena chyba, kdy emulátor úložiště `DataServiceVersion` vracel hlavičku v některých odpovědích, kde služba nebyla.

### <a name="version-50"></a>Verze 5.0

* Instalační program emulátoru úložiště již nekontroluje existující instalace mssql a .NET Framework.
* Instalační program emulátoru úložiště již nevytvoří databázi jako součást instalace. Databáze bude stále vytvořena v případě potřeby jako součást spuštění.
* Vytváření databáze již nevyžaduje zvýšení oprávnění.
* Rezervace portů již nejsou potřebné pro spuštění.
* Přidá následující `init`volby `-reserveports` : (vyžaduje `-unreserveports` výšku), `-skipcreate`(vyžaduje výšku), .
* Možnost uživatelského rozhraní emulátoru úložiště na ikoně systémové liště nyní spouští rozhraní příkazového řádku. Staré grafické uživatelské rozhraní již není k dispozici.
* Některé knihovny DLL byly odebrány nebo přejmenovány.

### <a name="version-46"></a>Verze 4.6

* Emulátor úložiště nyní podporuje verzi 2016-05-31 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-45"></a>Verze 4.5

* Opravena chyba, která způsobila selhání instalace a inicializace při přejmenování záložní databáze.

### <a name="version-44"></a>Verze 4.4

* Emulátor úložiště nyní podporuje verzi 2015-12-11 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.
* Úložiště emulátoru uvolňování dat objektů blob je teď efektivnější při práci s velkým počtem objektů BLOB.
* Opravena chyba, která způsobovala, že byl kontejner ACL XML ověřen mírně odlišně od způsobu, jakým to služba úložiště provádí.
* Opravena chyba, která někdy způsobovala, že hodnoty max a min DateTime byly hlášeny v nesprávném časovém pásmu.

### <a name="version-43"></a>Verze 4.3

* Emulátor úložiště nyní podporuje verzi 2015-07-08 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-42"></a>Verze 4.2

* Emulátor úložiště nyní podporuje verzi 2015-04-05 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka.

### <a name="version-41"></a>Verze 4.1

* Emulátor úložiště nyní podporuje verzi 2015-02-21 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka. Nepodporuje nové funkce objektu blob append.
* Emulátor nyní vrátí smysluplnou chybovou zprávu pro nepodporované verze služeb úložiště. Doporučujeme používat nejnovější verzi emulátoru. Pokud se zobrazí chyba VersionNotSupportedByEmulator (stavový kód HTTP 400 - Chybný požadavek), stáhněte nejnovější verzi emulátoru.
* Opravena chyba, v které spor způsobil, že data entity tabulky byla během souběžných operací sloučení nesprávná.

### <a name="version-40"></a>Verze 4.0

* Spustitelný soubor emulátoru úložiště byl přejmenován na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Verze 3.2

* Emulátor úložiště nyní podporuje verzi 2014-02-14 služeb úložiště na koncových bodech služby Blob, Fronta a Tabulka. Koncové body služby souborů nejsou aktuálně podporovány v emulátoru úložiště. Podrobnosti o verzi 2014-02-14 najdete v [tématu Správa verzí pro Službu Úložiště Azure.](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services)

### <a name="version-31"></a>Verze 3.1

* Geograficky redundantní úložiště pro čtení (RA-GRS) je nyní podporováno v emulátoru úložiště. `Get Blob Service Stats`Rozhraní `Get Queue Service Stats`, `Get Table Service Stats` a rozhraní API jsou podporovány pro sekundární účet a vždy vrátí hodnotu prvku odpovědi LastSyncTime jako aktuální čas podle podkladové databáze SQL. Pro programový přístup k sekundárnímu emulátoru úložiště použijte klientskou knihovnu úložiště pro rozhraní .NET verze 3.2 nebo novější. Podrobnosti najdete v klientské knihovně úložiště Microsoft Azure pro odkaz na rozhraní .NET.

### <a name="version-30"></a>Verze 3.0

* Emulátor úložiště Azure se už nedodává ve stejném balíčku jako emulátor výpočetního prostředí.
* Grafické uživatelské rozhraní emulátoru úložiště je zastaralé. Byl nahrazen skriptovatelným rozhraním příkazového řádku. Podrobnosti o rozhraní příkazového řádku naleznete v tématu Odkaz na nástroj příkazového řádku emulátoru úložiště. Grafické rozhraní bude i nadále k dispozici ve verzi 3.0, ale lze k němu přistupovat pouze v případě, že je nainstalován compute emulátor kliknutím pravým tlačítkem myši na ikonu na hlavním panelu systému a výběrem uživatelského rozhraní zobrazit emulátor úložiště.
* Verze 2013-08-15 služeb úložiště Azure je teď plně podporovaná. (Dříve byla tato verze podporována pouze emulátorem úložiště verze 2.2.1 Preview.)

## <a name="next-steps"></a>Další kroky

* Vyhodnoťte multiplatformní, komunitně udržovaný emulátor úložiště [azurit s](https://github.com/azure/azurite)otevřeným zdrojovým kódem . 
* [Ukázky služby Azure Storage pomocí rozhraní .NET](../storage-samples-dotnet.md) obsahují odkazy na několik ukázek kódu, které můžete použít při vývoji aplikace.
* [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com) můžete použít ke práci s prostředky ve vašem účtu cloudového úložiště a v emulátoru úložiště.
