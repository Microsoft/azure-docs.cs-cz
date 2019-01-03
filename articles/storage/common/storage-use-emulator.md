---
title: Použití emulátoru úložiště Azure pro vývoj a testování | Dokumentace Microsoftu
description: Emulátor úložiště Azure poskytuje bezplatná místní vývojové prostředí pro vývoj a testování aplikací s Azure Storage. Zjistěte, jak jsou žádosti o autorizaci, jak se připojit k emulátoru z vaší aplikace a jak pomocí nástroje příkazového řádku.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/10/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 22c7adc5db044568b4aa49dbbb0e36d2c919f6a6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629621"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Použití emulátoru úložiště Azure pro vývoj a testování

Emulátor úložiště Microsoft Azure poskytuje místní prostředí, které emuluje služby Azure Blob, Queue a Table pro účely vývoje. Pomocí emulátoru úložiště, můžete otestovat aplikaci místně, služeb úložiště bez vytváření předplatného Azure a bez jakýchkoli nákladů. Jakmile budete spokojeni s fungováním aplikace v emulátoru, můžete přejít na účet úložiště Azure v cloudu.

## <a name="get-the-storage-emulator"></a>Získejte emulátor úložiště
Emulátor úložiště je k dispozici jako součást [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště můžete také nainstalovat pomocí [samostatný instalační program](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (s přímým přístupem ke stažení). Pokud chcete nainstalovat emulátor úložiště, musí mít oprávnění správce v počítači.

Emulátor úložiště je aktuálně běží pouze na Windows. Pro ty, které zvažují emulátor úložiště pro Linux, jednou z možností je komunita zachována, emulátor úložiště opensourcových [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Data vytvořená v jedné verze emulátoru úložiště nemusí být dostupné při používání jiné verze. Pokud je potřeba pro dlouhodobé uchovávání dat, doporučujeme ukládat data v účtu úložiště Azure, nikoli v emulátoru úložiště.
> 
> Emulátor úložiště závisí na konkrétní verze knihovny OData. Nahrazování knihoven DLL OData používaný emulátorem úložiště s jinými verzemi není podporováno a může způsobit neočekávané chování. Však všechny verze protokolu OData podporované službou úložiště slouží k odesílání požadavků na emulátoru.

## <a name="how-the-storage-emulator-works"></a>Jak funguje emulátoru úložiště
Emulátor úložiště používá místní instanci systému Microsoft SQL Server a místním systému souborů k emulaci služby Azure storage. Ve výchozím nastavení emulátor úložiště používá databázi v Microsoft SQL Server 2012 Express LocalDB. Můžete nakonfigurovat pro přístup k místní instanci systému SQL Server namísto LocalDB instance emulátoru úložiště. Další informace najdete v tématu [Start a inicializovat emulátor úložiště](#start-and-initialize-the-storage-emulator) části dále v tomto článku.

Emulátor úložiště se připojí k serveru SQL Server nebo LocalDB pomocí ověřování Windows.

Existují některé rozdíly ve funkcích mezi emulátoru úložiště a služby Azure storage. Další informace o těchto rozdílech najdete v tématu [rozdíly mezi Azure Storage a emulátoru úložiště](#differences-between-the-storage-emulator-and-azure-storage) části dále v tomto článku.

## <a name="start-and-initialize-the-storage-emulator"></a>Spuštění a inicializovat emulátor úložiště

Chcete-li spustit emulátor úložiště Azure:
1. Vyberte **Start** tlačítko nebo stisknutím klávesy **Windows** klíč.
2. Začněte psát `Azure Storage Emulator`.
3. Vyberte ze seznamu zobrazených aplikací v emulátoru.

Po spuštění emulátoru úložiště, zobrazí se okno příkazového řádku. Toto okno konzoly můžete spustit a zastavit emulátor úložiště, vymazat data, získat stav a inicializovat emulátor. Další informace najdete v tématu [odkaz na nástroj příkazového řádku emulátor úložiště](#storage-emulator-command-line-tool-reference) části dále v tomto článku.

Po spuštění emulátoru se v oznamovací oblasti hlavního panelu Windows zobrazí jeho ikona.

Když zavřete okno příkazového řádku emulátor úložiště, emulátor úložiště bude nadále spuštěna. V okně konzoly se emulátor úložiště znovu vyvolejte, postupujte podle předchozích kroků jakoby spuštění emulátoru úložiště.

Při prvním spuštění emulátoru úložiště Místní úložiště prostředí je inicializována za vás. Inicializace procesu vytvoří databázi LocalDB a vyhrazuje porty HTTP pro každou službu místního úložiště.

Emulátor úložiště je nainstalovaný ve výchozím nastavení `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Můžete použít [Microsoft Azure Storage Explorer](http://storageexplorer.com) pro práci s prostředky emulátor místního úložiště. Vyhledejte "(vývoj)" v části "Účty" ve stromové struktuře Průzkumníka služby Storage prostředků po instalaci a spuštění emulátoru úložiště.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializovat emulátor úložiště k použití jiné databáze SQL

Nástroj příkazového řádku emulátor úložiště můžete inicializovat emulátor úložiště tak, aby odkazovala na instanci SQL database jinou než výchozí instanci LocalDB:

1. Otevřete okno konzoly emulátor úložiště, jak je popsáno v [Start a inicializovat emulátor úložiště](#start-and-initialize-the-storage-emulator) oddílu.
1. V okně konzoly, zadejte následující příkaz, kde `<SQLServerInstance>` je název instance systému SQL Server. Chcete-li použít databázi LocalDB, zadejte `(localdb)\MSSQLLocalDb` jako instanci systému SQL Server.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Můžete také použít následující příkaz, který přesměruje emulátor používat výchozí instanci SQL serveru:

  `AzureStorageEmulator.exe init /server .`

  Nebo můžete použít následující příkaz, který znovu inicializuje databázi na instanci LocalDB výchozí:

  `AzureStorageEmulator.exe init /forceCreate`

Další informace o těchto příkazech najdete v tématu [odkaz na nástroj příkazového řádku emulátor úložiště](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Můžete použít [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) ke správě vaší instance SQL serveru, včetně LocalDB instalace. V SMSS **připojit k serveru** dialogové okno, zadejte `(localdb)\MSSQLLocalDb` v **název serveru:** pole, které chcete připojit k instanci LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Ověřování požadavků na emulátor úložiště
Po instalaci a spustit emulátor úložiště, můžete otestovat kód proti ho. Stejně jako u služby Azure Storage v cloudu, musí být ověřeny všechny požadavky provedené v emulátoru úložiště, pokud je anonymní žádosti. Můžete povolit požadavky na emulátoru úložiště používat ověřování pomocí sdíleného klíče nebo pomocí sdíleného přístupového podpisu (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizaci pomocí sdíleného klíče přihlašovacích údajů
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o připojovacích řetězcích najdete v tématu [připojovacích řetězců Azure Storage konfigurace](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizaci pomocí sdíleného přístupového podpisu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Některé knihovny klienta úložiště Azure, jako je například knihovna pro Xamarin, podporují pouze ověřování pomocí token sdíleného přístupového podpisu (SAS). Můžete vytvořit token SAS pomocí některého nástroje, například [Průzkumníka služby Storage](http://storageexplorer.com/) nebo jinou aplikaci, která podporuje ověřování pomocí sdíleného klíče.

SAS token můžete vygenerovat také pomocí Azure Powershellu. Následující příklad generuje token SAS s úplnými oprávněními pro kontejner objektů blob:

1. Instalace Azure Powershellu, pokud jste tak dosud neučinili (pomocí nejnovější verze prostředí Azure PowerShell se doporučuje rutiny). Pokyny k instalaci, naleznete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-Az-ps).
2. Otevřete prostředí Azure PowerShell a spuštěním následujících příkazů nahraďte `CONTAINER_NAME` s názvem podle svého výběru:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Výsledný sdíleného přístupového podpisu URI pro nový kontejner by měl vypadat přibližně:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sdílený přístupový podpis vytvořené v tomto příkladu je platný jeden den. Podpis uděluje plný přístup (čtení, zápisu, delete, list) pro objekty BLOB v kontejneru.

Další informace o sdílených přístupových podpisů najdete v tématu [použití sdílených přístupových podpisů (SAS) ve službě Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresování prostředků v emulátoru úložiště
Koncové body služby pro emulátor úložiště se liší od těch, které účet úložiště Azure. Rozdíl je, protože místním počítači nebude provádět překlad názvů domén, koncových bodech emulátor úložiště, které se místní adresy vyžaduje.

Při adresování prostředků v účtu služby Azure storage, můžete použít následující schéma. Název účtu je součástí názvu hostitele identifikátoru URI a prostředků se řeší je část cesty identifikátoru URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Například následující identifikátor URI je platná adresa pro objekty blob v účtu služby Azure storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Emulátor úložiště, protože místním počítači nebude provádět překlad názvů domén, název účtu je však cesta k identifikátoru URI namísto názvu hostitele součástí. Použijte následující formát identifikátoru URI pro prostředek v emulátoru úložiště:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Následující adresa může být například použít pro přístup k objektu blob v emulátoru úložiště:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Koncové body služby pro emulátor úložiště jsou:

* Služba objektů blob: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Služba front: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Služba Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresování účet sekundární s RA-GRS
Od verze 3.1, emulátor úložiště podporuje geo redundantní replikaci jen pro čtení (RA-GRS). Pro prostředky úložiště v cloudu i v místním emulátoru, můžete přístup k sekundární lokality pomocí připojení – sekundární k názvu účtu. Následující adresa může být například použít pro přístup k objektu blob s využitím sekundární jen pro čtení v emulátoru úložiště:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Pro programový přístup do sekundární lokality s emulátorem úložiště použijte klientskou knihovnu pro úložiště pro .NET verze 3.2 nebo novější. Zobrazit [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) podrobnosti.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Odkaz na nástroj příkazového řádku emulátoru úložiště
Od verze 3.0 se do okna konzoly se zobrazí při spuštění emulátoru úložiště. V okně konzoly použijte příkazový řádek pro spuštění a zastavení emulátor, stejně jako dotaz na stav a provádět jiné operace.

> [!NOTE]
> Pokud máte Microsoft Azure compute nainstalovat emulátor, systémové ikony se zobrazí při spuštění emulátoru úložiště. Klikněte pravým tlačítkem na ikonu zobrazíte nabídku, která nabízí grafický způsob spuštění a zastavení emulátoru úložiště.
>
>

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Možnosti
Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `/help`.

| Možnost | Popis | Příkaz | Argumenty |
| --- | --- | --- | --- |
| **Start** |Spuštění emulátoru úložiště. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Spusťte emulátor v aktuálním procesu místo vytvoření nového procesu. |
| **Stop** |Zastaví emulátoru úložiště. |`AzureStorageEmulator.exe stop` | |
| **Stav** |Vypíše stav emulátoru úložiště. |`AzureStorageEmulator.exe status` | |
| **Vymazat** |Vymaže data ve všech služeb, které jsou zadané na příkazovém řádku. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*objekt BLOB*: Data v objektech blob vymaže. <br/>*fronty*: Vymaže data ve frontě. <br/>*Tabulka*: Vymaže dat tabulky. <br/>*Všechny*: Vymaže všechna data ve všech služeb. |
| **Inicializace** |Provádí jednorázová inicializace nastavení emulátoru. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Určuje server, který je hostitelem instance serveru SQL. <br/>*instanceName – sqlinstance*: Určuje název instance SQL pro použití výchozí instance serveru. <br/>*-forcecreate*: Vynutí vytvoření databáze SQL, i v případě, že již existuje. <br/>*-skipcreate*: Přeskočí vytvoření databáze SQL. To má přednost před - forcecreate.<br/>*-reserveports*: Pokusy o rezervaci portů HTTP přidruženého ke službám.<br/>*-unreserveports*: Pokus o odebrání rezervace pro porty HTTP přidruženého ke službám. To má přednost před - reserveports.<br/>*-inprocess*: Provede inicializaci v aktuálním procesu místo vytváření podřízeného procesu nový proces. Aktuální proces musí být spuštěn se zvýšenými oprávněními, pokud Změna portu rezervace. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Rozdíly mezi Azure Storage a emulátoru úložiště
Emulátor úložiště je emulovaných prostředí spuštěné v místní instanci SQL, existují rozdíly ve funkcích mezi emulátorem a účtu služby Azure storage v cloudu:

* Emulátor úložiště podporuje pouze jeden účet pevné a dobře známý ověřovací klíč.
* Emulátor úložiště není služba škálovatelného úložiště a nepodporuje velké množství souběžných klientů.
* Jak je popsáno v [adresování prostředků v emulátoru úložiště](#addressing-resources-in-the-storage-emulator), prostředky se tak vyřeší, jinak v emulátoru úložiště a účet úložiště Azure. Tento rozdíl je vzhledem k tomu, že překlad názvů domén je k dispozici v cloudu, ale ne na místním počítači.
* Od verze 3.1, emulátor účet úložiště podporuje geo redundantní replikaci jen pro čtení (RA-GRS). V emulátoru všechny účty mají povoleno RA-GRS a neexistuje žádné zpoždění mezi primární a sekundární repliky. Operace získat statistiky služby Blob, získat statistiky služby Queue a získat statistiky tabulky služby jsou podporovány v sekundární účet a vždy vrátí hodnotu `LastSyncTime` prvek odpovědi jako aktuální čas podle podkladové databázi SQL.
* Souborová služba a koncové body služeb protokolu SMB nejsou aktuálně podporované v emulátoru úložiště.
* Pokud používáte verzi služby úložiště, která se ještě nepodporuje emulátor, emulátor úložiště vrátí chybu VersionNotSupportedByEmulator (stavový kód HTTP 400 - Chybný požadavek).

### <a name="differences-for-blob-storage"></a>Rozdíly pro úložiště objektů Blob
Do úložiště objektů Blob se spustila v emulátoru platí následující rozdíly:

* Emulátor pouze podporuje objektu blob úložiště až do velikosti 2 GB.
* Maximální délka názvu objektu blob v emulátoru úložiště je 256 znaků, maximální délka názvu objektu blob ve službě Azure Storage je 1024 znaků.
* Přírůstkové kopírování umožňuje snímky z přepsána objektů BLOB, které se mají zkopírovat, který vrátí chybu ve službě.
* Rozdílové získání rozsahů stránek nefunguje mezi snímky zkopíruje pomocí příkazu přírůstkové kopírování objektů Blob.
* Operace Put Blob může být úspěšné vztahu k objektu blob, který existuje v emulátoru úložiště s aktivní zapůjčení, i v případě, že v požadavku nebylo zadáno ID zapůjčení.
* Doplňovací objekt Blob se nepodporují operace emulátorem. Pokus o operaci na doplňovací objekt blob se vrátí chyba FeatureNotSupportedByEmulator (stavový kód HTTP 400 - Chybný požadavek).

### <a name="differences-for-table-storage"></a>Rozdíly pro Table storage
Table Storage v emulátoru platí následující rozdíly:

* Vlastnosti kalendářních dat ve službě Table service v emulátoru úložiště podporují jenom rozsahu podporovaném parametrem (musí být pozdější než 1. ledna 1753) systému SQL Server 2005. Všechna data před 1. ledna 1753 se změní na tuto hodnotu. Přesnost kalendářních dat, je omezený na přesnost systému SQL Server 2005, což znamená, že data jsou přesné 1/300th sekundy.
* Emulátor úložiště podporuje oddílu klíč a řádek klíčová vlastnost hodnoty menší než 512 bajtů. Kromě toho celková velikost název účtu, název tabulky a názvech vlastností klíčů společně nesmí překročit 900 bajtů.
* Celková velikost řádku v tabulce v emulátoru úložiště je omezená na menší než 1 MB.
* Emulátor úložiště, zadejte vlastnosti dat `Edm.Guid` nebo `Edm.Binary` podporují pouze `Equal (eq)` a `NotEqual (ne)` operátory porovnání v dotazu filtru řetězce.

### <a name="differences-for-queue-storage"></a>Rozdíly pro Queue storage
Specifické pro Queue storage se spustila v emulátoru nejsou žádné rozdíly.

## <a name="storage-emulator-release-notes"></a>Zpráva k vydání verze emulátoru úložiště

### <a name="version-57"></a>Verzi 5.7
Je opravená chyba, která může způsobit chyby, pokud bylo povoleno protokolování.

### <a name="version-56"></a>Verze 5.6
* Emulátor úložiště teď podporuje verze 2018-03-28 služby úložiště na koncové body služby objektů Blob, Queue a Table.

### <a name="version-55"></a>Verze 5.5
* Emulátor úložiště teď podporuje verze 2017-11-09 služby úložiště na koncové body služby objektů Blob, Queue a Table.
* Byla přidána podpora pro objekt blob **vytvořeno** vlastnost, která vrátí čas vytvoření objektu blob.

### <a name="version-54"></a>Verze 5.4
Pokud chcete zlepšit stabilitu instalace, emulátor již nebude pokoušet o rezervaci portů v době instalace. Potřeby port rezervace použít *- reserveports* možnost **init** příkaz, který je zadat.

### <a name="version-53"></a>Verze 5.3
Emulátor úložiště teď podporuje verze 2017-07-29 služby úložiště na koncové body služby objektů Blob, Queue a Table.

### <a name="version-52"></a>Verze 5.2
* Emulátor úložiště teď podporuje verze 2017-04-17 služby úložiště na koncové body služby objektů Blob, Queue a Table.
* Je opravená chyba, kde se právě hodnoty vlastností tabulky správně kódovaný.

### <a name="version-51"></a>Verze 5.1
Oprava chyby, kde se vrací emulátor úložiště `DataServiceVersion` záhlaví v některé odpovědi, kde služba nebyla.

### <a name="version-50"></a>Verze 5.0
* Instalační program emulátor úložiště už zkontroluje existující MSSQL a nainstaluje rozhraní .NET Framework.
* Instalační program emulátor úložiště už vytvoří databázi jako součást instalace. Databáze se vytvoří stále v případě potřeby jako součást spuštění.
* Vytvoření databáze už vyžaduje zvýšená oprávnění.
* Rezervace port již nejsou potřebné pro spuštění.
* Přidá následujících možností `init`: `-reserveports` (vyžaduje zvýšení úrovně oprávnění), `-unreserveports` (vyžaduje zvýšení úrovně oprávnění), `-skipcreate`.
* Možnost uživatelské prostředí emulátoru úložiště na ikonu na hlavním panelu systému nyní spustí rozhraní příkazového řádku. Staré grafického uživatelského rozhraní již není k dispozici.
* Některé knihovny DLL byly odebrány nebo přejmenovány.

### <a name="version-46"></a>Verze 4.6
* Emulátor úložiště teď podporuje verze 2016-05-31 služeb úložiště na koncové body služby objektů Blob, Queue a Table.

### <a name="version-45"></a>Verze 4.5
* Je opravená chyba, která způsobila instalace emulátoru úložiště při zálohování databáze byla přejmenována a inicializace.

### <a name="version-44"></a>Verze 4.4
* Emulátor úložiště teď podporuje verze 2015-12-11 služby úložiště na koncové body služby objektů Blob, Queue a Table.
* Kolekce paměti na emulátor úložiště dat objektů blob je teď mnohem efektivnější, při práci s velkým počtem objektů BLOB.
* Je opravená chyba, která způsobila kontejneru XML seznamu ACL k ověření mírně odlišně od jak službu storage to dělá.
* Je opravená chyba, která někdy způsobovala maximální a minimální hodnoty data a času, aby oznámený v časovém pásmu nesprávné.

### <a name="version-43"></a>Verze 4.3
* Emulátor úložiště teď podporuje verze 2015-07-08 služby úložiště na koncové body služby objektů Blob, Queue a Table.

### <a name="version-42"></a>Verze 4.2
* Emulátor úložiště teď podporuje verze 2015-04-05 služby úložiště na koncové body služby objektů Blob, Queue a Table.

### <a name="version-41"></a>Verze 4.1
* Emulátor úložiště teď podporuje verze 2015-02-21 služby úložiště na koncové body objektu Blob, Queue a Table service, s výjimkou nové funkce doplňovací objekt Blob.
* Pokud používáte verzi služby úložiště, která se ještě nepodporuje emulátor, vrátí emulátor smysluplné chybovou zprávu. Doporučujeme používat nejnovější verze emulátoru. Pokud narazíte na chybu VersionNotSupportedByEmulator (stavový kód HTTP 400 - Chybný požadavek), stáhněte si prosím nejnovější verze emulátoru úložiště.
* Je opravená chyba, ve které datové entity tabulky podmínku způsobil závodu pravděpodobně nesprávná během sloučení souběžných operací.

### <a name="version-40"></a>Verze 4.0
* Spustitelný soubor emulátoru úložiště byl přejmenován na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Verze 3.2
* Emulátor úložiště teď podporuje verze 2014-02-14 služby úložiště na koncové body služby objektů Blob, Queue a Table. Soubor koncové body služby nejsou aktuálně podporované v emulátoru úložiště. Zobrazit [označování verzí u služby Azure Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) podrobnosti o verzi 2014-02-14.

### <a name="version-31"></a>Verze 3.1
* Geograficky redundantní úložiště jen pro čtení (RA-GRS) je nyní podporována v emulátoru úložiště. Získat statistiky služby Blob, získat statistiky služby Queue a získat statistiky API služby tabulky jsou podporovány pro sekundární účet a vždy vrátí hodnotu prvku LastSyncTime odpovědi jako aktuální čas podle podkladové databázi SQL. Pro programový přístup do sekundární lokality s emulátorem úložiště použijte klientskou knihovnu pro úložiště pro .NET verze 3.2 nebo novější. Podrobnosti najdete v Microsoft Azure Storage Client Library pro .NET – referenční informace.

### <a name="version-30"></a>Verze 3.0
* Emulátor úložiště Azure je už součástí stejného balíčku jako emulátor služby výpočty.
* Grafické uživatelské rozhraní pro emulátor úložiště je zastaralé a místo toho použití možné používat skripty rozhraní příkazového řádku. Podrobnosti o rozhraní příkazového řádku naleznete v tématu referenční nástroj příkazového řádku emulátoru úložiště. Grafické rozhraní budou i nadále k dispozici ve verzi 3.0, ale to je přístupná jenom po nainstalování emulátoru Compute tak, že kliknete pravým tlačítkem na ikonu na hlavním panelu systému a vyberete Zobrazit uživatelské prostředí emulátoru úložiště.
* Verze 2013-08-15 služby Azure storage je teď plně podporovaná. (Dříve tato verze byla podporována pouze verze 2.2.1 emulátorem úložiště ve verzi Preview.)

## <a name="next-steps"></a>Další postup

* Vyhodnocení emulátor úložiště napříč platformami, Udržovat komunity open source [Azurite](https://github.com/arafato/azurite). 
* [Ukázky pro Azure Storage pomocí .NET](../storage-samples-dotnet.md) obsahuje odkazy na několik vzorových kódů můžete použít při vývoji vaší aplikace.
* Můžete použít [Microsoft Azure Storage Explorer](http://storageexplorer.com) pro práci s prostředky ve vašem cloudovém účtu úložiště a v emulátoru úložiště.
