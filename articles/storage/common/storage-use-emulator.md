---
title: "Použití emulátoru úložiště Azure pro vývoj a testování | Microsoft Docs"
description: "Emulátor úložiště Azure poskytuje volné místní vývojové prostředí pro vývoj a testování aplikací s Azure Storage. Zjistěte, jak jsou žádosti o ověření, jak se připojit k emulátoru z vaší aplikace a jak pomocí nástroje příkazového řádku."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: tamram
ms.openlocfilehash: 13aee7bbbe58c0a4183eddc0881aaed8cbebd956
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Použití emulátoru úložiště Azure pro vývoj a testování

Emulátor úložiště Microsoft Azure poskytuje místní prostředí, které emuluje služby Azure Blob, Queue a Table pro účely vývoje. Pomocí emulátoru úložiště, můžete aplikaci můžete otestovat s místně, služby úložiště bez vytváření předplatného Azure nebo nákladům. Až budete spokojeni s jak funguje aplikaci v emulátoru, můžete přejít k používání účtu úložiště Azure v cloudu.

## <a name="get-the-storage-emulator"></a>Získat emulátor úložiště
Emulátor úložiště je k dispozici jako součást [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště můžete nainstalovat také pomocí [samostatný instalační program systému](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (přímé ke stažení). Pokud chcete nainstalovat emulátor úložiště, musí mít oprávnění správce v počítači.

Emulátor úložiště aktuálně běží jenom v systému Windows. Pro ty zvažování emulátor úložiště pro Linux, jednou z možností je komunity udržovat, emulátor úložiště s otevřeným zdrojem [Azurite](https://github.com/arafato/azurite).

> [!NOTE]
> Data vytvořená v jedné verzi emulátoru úložiště nemusí být dostupný, pokud používáte jinou verzi. Pokud potřebujete zachovat data pro z dlouhodobého hlediska, doporučujeme uložit data v účtu úložiště Azure, a nikoli v emulátoru úložiště.
> <p/>
> Emulátor úložiště závisí na konkrétní verze knihoven OData. Nahrazování OData knihovny DLL používané emulátor úložiště s jinými verzemi není podporován a můžou způsobit neočekávané chování. Všechny verze OData nepodporuje službu úložiště lze však odesílat žádosti do emulátoru.
>

## <a name="how-the-storage-emulator-works"></a>Jak funguje emulátor úložiště
Emulátor úložiště používá místní instanci systému Microsoft SQL Server a místního systému souborů emulovat služby Azure storage. Ve výchozím nastavení emulátor úložiště používá databázi v Microsoft SQL Server 2012 Express LocalDB. Můžete nakonfigurovat emulátor úložiště pro přístup k místní instanci systému SQL Server namísto instanci LocalDB. Další informace najdete v tématu [spuštění a inicializaci emulátor úložiště](#start-and-initialize-the-storage-emulator) později v tomto článku.

Emulátor úložiště se připojí k serveru SQL Server nebo LocalDB pomocí ověřování systému Windows.

Existují některé rozdíly ve funkcích mezi emulátor úložiště a služby úložiště Azure. Další informace o tyto rozdíly, najdete v článku [rozdíly mezi emulátor úložiště a Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) později v tomto článku.

## <a name="start-and-initialize-the-storage-emulator"></a>Spuštění a inicializaci emulátor úložiště

### <a name="run-the-azure-storage-emulator-in-dockerhttpshubdockercomrmicrosoftazure-storage-emulator"></a>[Spusťte emulátor úložiště Azure v Docker](https://hub.docker.com/r/microsoft/azure-storage-emulator/)
```
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 microsoft/azure-storage-emulator
```

### <a name="using-sdk"></a>Pomocí sady SDK

Spusťte emulátor úložiště Azure:
1. Vyberte **spustit** tlačítko nebo klikněte na tlačítko **Windows** klíč.
1. Začněte psát `Azure Storage Emulator`.
1. Emulátor vyberte ze seznamu zobrazených aplikací.

Když se spustí se emulátor úložiště, zobrazí se okno příkazového řádku. Toto okno konzoly můžete spustit a zastavit emulátor úložiště, vymazat data, získat stav a inicializace emulátor. Další informace najdete v tématu [odkaz na nástroj příkazového řádku emulátor úložiště](#storage-emulator-command-line-tool-reference) později v tomto článku.

Když na emulátoru běží, se zobrazí na ikonu v oznamovací oblasti hlavního panelu Windows.

Když zavřete okno příkazového řádku emulátor úložiště, emulátor úložiště bude nadále spouštět. Se znovu zprovoznit v okně konzoly emulátor úložiště, postupujte podle předchozích kroků, jako kdyby spouštění emulátor úložiště.

Při prvním spuštění emulátor úložiště v prostředí místní úložiště je pro vás inicializován. Proces inicializace vytvoří databázi v instanci LocalDB a rezerv HTTP portů pro každou službu místní úložiště.

Emulátor úložiště je nainstalována ve výchozím nastavení `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Můžete použít [Microsoft Azure Storage Explorer](http://storageexplorer.com) pro práci s prostředky emulátor místního úložiště. Vyhledejte "(vývoj)" v části "Účty úložiště" ve stromu prostředky Storage Explorer po nainstalována a spuštěna emulátor úložiště.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializace emulátor úložiště používat jiné databázi SQL
Nástroj příkazového řádku emulátor úložiště můžete inicializovat emulátor úložiště tak, aby odkazoval na instanci SQL databáze než výchozí instanci LocalDB:

1. Otevřete okno konzoly emulátor úložiště, jak je popsáno v [spuštění a inicializaci emulátor úložiště](#start-and-initialize-the-storage-emulator) části.
1. V okně konzoly, zadejte následující příkaz, kde `<SQLServerInstance>` je název instance systému SQL Server. Chcete-li použít LocalDB, zadejte `(localdb)\MSSQLLocalDb` jako instanci systému SQL Server.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Můžete také použít následující příkaz, který přesměruje emulátor pro použití výchozí instance systému SQL Server:

  `AzureStorageEmulator.exe init /server .\\`

  Nebo můžete použít následující příkaz, který znovu inicializuje databázi na instanci LocalDB výchozí:

  `AzureStorageEmulator.exe init /forceCreate`

Další informace o těchto příkazech najdete v tématu [odkaz na nástroj příkazového řádku emulátor úložiště](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Můžete použít [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) ke správě vaší instance systému SQL Server, včetně instalace LocalDB. V SMSS **připojit k serveru** dialogové okno, zadejte `(localdb)\MSSQLLocalDb` v **název serveru:** pole pro připojení k instanci LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Ověřování požadavků na emulátor úložiště
Jakmile jste instalaci a spuštění emulátor úložiště, můžete otestovat svůj kód s ho. Stejně jako u Azure Storage v cloudu, musí být ověřeny všechny žádosti, které provedete emulátoru úložiště, pokud je požadavek anonymní. Požadavky na emulátoru úložiště pomocí ověřování sdílený klíč, nebo pomocí sdíleného přístupového podpisu (SAS), můžete ověřovat.

### <a name="authenticate-with-shared-key-credentials"></a>Ověření pomocí sdíleného klíče pověření
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o připojovacích řetězcích najdete v tématu [připojovacích řetězců Azure Storage konfigurace](../storage-configure-connection-string.md).

### <a name="authenticate-with-a-shared-access-signature"></a>Ověření pomocí sdíleného přístupového podpisu
Některé knihovny klienta úložiště Azure, jako je například knihovně Xamarin podporují jenom ověřování pomocí tokenu sdíleného přístupového podpisu (SAS). Můžete vytvořit token SAS, pomocí nástroje, například [Storage Explorer](http://storageexplorer.com/) nebo jiné aplikace, která podporuje ověření sdíleným klíčem.

Můžete také vygenerovat SAS token pomocí prostředí Azure PowerShell. Následující příklad vytvoří SAS token s úplnými oprávněními na kontejner objektů blob:

1. Instalace Azure PowerShell, pokud jste to ještě neudělali (pomocí nejnovější verzi prostředí Azure PowerShell se doporučuje rutiny). Pokyny k instalaci naleznete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Otevřete prostředí Azure PowerShell a spusťte následující příkazy, nahraďte `ACCOUNT_NAME` a `ACCOUNT_KEY==` s svoje vlastní přihlašovací údaje a `CONTAINER_NAME` s názvem vašeho výběru:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Výsledný sdílený přístupový podpis identifikátor URI pro nový kontejner by měl vypadat podobně jako:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sdílený přístupový podpis vytvořené pomocí v tomto příkladu je platný pro jeden den. Podpis uděluje úplný přístup (pro čtení, zápisu, odstranění, seznamu) na objekty BLOB v kontejneru.

Další informace o sdílených přístupových podpisů najdete v tématu [pomocí sdílené přístupové podpisy (SAS) ve službě Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Přidělování prostředků v emulátoru úložiště
Koncové body služby pro emulátor úložiště se liší od těch, které účet úložiště Azure. Rozdílem je, protože místní počítač neprovede překlad názvu domény, které vyžadují koncové body emulátor úložiště jako místní adresy.

Při adresování prostředků v účtu úložiště Azure, můžete použít následující schéma. Název účtu je součástí názvu hostitele URI a prostředků adresovaném je součástí cesta URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Například následující identifikátor URI je platnou adresu pro objekt blob v účtu úložiště Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Emulátor úložiště, protože místní počítač neprovede překlad názvu domény, název účtu je však součástí cesta k identifikátoru URI místo názvu hostitele. Použijte následující formát identifikátoru URI pro prostředek v emulátoru úložiště:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Například následující adresy se dají používat pro přístup k objektu blob v emulátoru úložiště:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Koncové body služby pro emulátor úložiště jsou:

* Služba objektů blob: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Služba front: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Služba Table: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresování sekundární s RA-GRS účtu
Od verze 3.1, emulátor úložiště podporuje geo redundantní replikaci přístup pro čtení (RA-GRS). Pro prostředky úložiště v cloudu a v místní emulátoru, můžete přístup k sekundární umístění pomocí připojování - sekundární k názvu účtu. Například následující adresy se dají používat pro přístup k objektu blob pomocí sekundární jen pro čtení v emulátoru úložiště:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Pro programový přístup k sekundární pomocí emulátoru úložiště použijte knihovnu klienta služby Storage pro .NET verze 3.2 nebo vyšší. Najdete v článku [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) podrobnosti.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Odkaz na nástroj příkazového řádku emulátor úložiště
Od verze 3.0 se do okna konzoly se zobrazí při spuštění emulátor úložiště. V okně konzoly, zadejte příkaz pro spuštění a zastavení emulátoru, jakož i dotaz na stav a provádění dalších operací.

> [!NOTE]
> Pokud máte Microsoft Azure výpočetní emulátor nainstalovaná, zobrazí se při spuštění emulátor úložiště ikonu na hlavním panelu. Klikněte pravým tlačítkem myši na ikonu zobrazí nabídka, která poskytuje grafický způsob spuštění a zastavení emulátor úložiště.
>
>

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Možnosti
Chcete-li zobrazit seznam možností, zadejte `/help` na příkazovém řádku.

| Možnost | Popis | Příkaz | Argumenty |
| --- | --- | --- | --- |
| **Start** |Spustí se emulátor úložiště. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Spusťte emulátor v aktuálním procesu místo vytvoření nového procesu. |
| **Stop** |Zastaví emulátor úložiště. |`AzureStorageEmulator.exe stop` | |
| **Stav** |Zobrazí stav emulátoru úložiště. |`AzureStorageEmulator.exe status` | |
| **Zrušte zaškrtnutí** |Vymaže data ve všech služeb zadat na příkazovém řádku. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*objekt BLOB*: blob, které vymaže data. <br/>*fronty*: vymaže data fronty. <br/>*Tabulka*: vymaže data tabulky. <br/>*všechny*: Vymaže všechna data v všechny služby. |
| **Init –** |Provede jednorázovou inicializace nastavit emulátor. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Určuje server, který je hostitelem instance serveru SQL. <br/>*-sqlinstance instanceName*: Určuje název instance SQL, který se má použít ve výchozí instanci serveru. <br/>*-forcecreate*: Vynutí vytvoření databáze SQL, i v případě, že již existuje. <br/>*-skipcreate*: přeskočí vytvoření databáze SQL. To má přednost před - forcecreate.<br/>*-reserveports*: pokusí rezervovat portech HTTP přidruženého ke službám.<br/>*-unreserveports*: pokusy o odebrání rezervace portech HTTP přidruženého ke službám. To má přednost před - reserveports.<br/>*-inprocess*: provede inicializaci v aktuálním procesu místo například nový proces. Aktuální proces musí být spuštěn se zvýšenými oprávněními, pokud změnit port rezervace. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Rozdíly mezi emulátor úložiště a Azure Storage
Protože emulátor úložiště je emulovaných prostředí spuštěné v místní instanci SQL, existují rozdíly ve funkcích mezi emulátoru a účet úložiště Azure v cloudu:

* Emulátor úložiště podporuje jenom jeden účet opravené a dobře známé ověřovací klíč.
* Emulátor úložiště není služba škálovatelné úložiště a nepodporuje velký počet souběžných klientů.
* Jak je popsáno v [adresování prostředků v emulátoru úložiště](#addressing-resources-in-the-storage-emulator), prostředky se řešit odlišně v emulátoru úložiště a účet úložiště Azure. Tento rozdíl je, protože je k dispozici překlad názvu domény v cloudu, ale není v místním počítači.
* Od verze 3.1, účet emulátor úložiště podporuje geo redundantní replikaci přístup pro čtení (RA-GRS). V emulátoru všechny účty mít povoleno RA-GRS a nikdy neexistuje žádné prodleva mezi primární a sekundární repliky. Operace získání statistiky služby objektů Blob, získat statistiky fronty služby a získat statistiky tabulky služby jsou podporovány v sekundární účet a bude vždy vrátí hodnotu `LastSyncTime` element odpovědi jako aktuální čas podle základní databáze SQL.
* Souborová služba a koncové body služby protokolu SMB v emulátoru úložiště aktuálně nepodporují.
* Pokud používáte verzi služby storage, který ještě není podporovaný emulátorem, emulátor úložiště vrátí chybu VersionNotSupportedByEmulator (kód stavu HTTP 400 – Chybný požadavek).

### <a name="differences-for-blob-storage"></a>Rozdíly pro úložiště objektů Blob
Do úložiště objektů Blob v emulátoru platí následující rozdíly:

* Emulátor pouze podporuje objektu blob úložiště velikostí až 2 GB.
* Přírůstkové kopie umožňuje snímky ze přepsána objektů BLOB, které se mají zkopírovat, který vrátí chybu ve službě.
* Diff rozsahů stránek Get nefunguje mezi snímky zkopírovat pomocí přírůstkové kopie objektů Blob.
* Operace Put objekt Blob může být úspěšné proti objekt blob, který již existuje v emulátoru úložiště s aktivní zapůjčení, i v případě, že v požadavku nebylo zadáno ID zapůjčení.
* Append – objekt Blob emulátorem nepodporuje operace. Probíhá pokus operaci na doplňovací objekt blob vrátí chybu FeatureNotSupportedByEmulator (kód stavu HTTP 400 – Chybný požadavek).

### <a name="differences-for-table-storage"></a>Rozdíly pro úložiště tabulek
Table Storage v emulátoru platí následující rozdíly:

* Vlastnosti kalendářních dat ve službě Table v emulátoru úložiště podporují pouze rozsah (musí být pozdější než 1. ledna 1753) systému SQL Server 2005 nepodporuje. Všechna data před 1. ledna 1753 jsou na tuto hodnotu změnit. Přesnost kalendářních dat je omezená na přesnost systému SQL Server 2005, což znamená, že data jsou přesné 1/300th sekundy.
* Emulátor úložiště podporuje oddílu klíč řádku klíče hodnoty vlastností a menší než 512 bajtů. Celková velikost název účtu, název tabulky a názvů vlastností klíče společně kromě toho nesmí přesáhnout 900 bajtů.
* Celková velikost řádku tabulky v emulátoru úložiště je omezen na méně než 1 MB.
* V emulátoru úložiště, zadejte vlastnosti dat `Edm.Guid` nebo `Edm.Binary` podporují pouze `Equal (eq)` a `NotEqual (ne)` operátory porovnání v dotazu filtrovat řetězce.

### <a name="differences-for-queue-storage"></a>Rozdíly pro úložiště
Konkrétní Queue Storage v emulátoru nejsou žádné rozdíly.

## <a name="storage-emulator-release-notes"></a>Poznámky k verzi emulátoru úložiště
### <a name="version-52"></a>Verze 5.2
* Emulátor úložiště teď podporuje verze 2017-04-17 služby úložiště na koncové body služby objektů Blob, fronty a tabulky.
* Opravit chyby, kde nebyly se správně kódovaný hodnoty vlastností tabulky.

### <a name="version-51"></a>Verze 5.1
* Pevné chyby, kde byla vrácení emulátor úložiště `DataServiceVersion` záhlaví v některé odpovědí, pokud služba nebyla.

### <a name="version-50"></a>Verze 5.0
* Instalační program emulátor úložiště už existující MSSQL kontroluje a nainstaluje rozhraní .NET Framework.
* Instalační program emulátor úložiště už vytvoří databázi jako součást instalace. Stále se vytvoří databáze v případě potřeby jako součást spuštění.
* Vytvoření databáze už se vyžaduje zvýšení oprávnění.
* Rezervace port již nejsou potřebné pro spuštění.
* Přidá následujících možností `init`: `-reserveports` (vyžaduje zvýšení oprávnění), `-unreserveports` (vyžaduje zvýšení oprávnění), `-skipcreate`.
* Možnost uživatelské prostředí emulátoru úložiště na hlavním panelu ikonu systému nyní spustí rozhraní příkazového řádku. Původní grafické uživatelské rozhraní již není k dispozici.
* Některých knihoven DLL přesunutý nebo přejmenovaný.

### <a name="version-46"></a>Verze 4.6
* Emulátor úložiště teď podporuje verze 2016-05-31 služby úložiště na koncové body služby objektů Blob, fronty a tabulky.

### <a name="version-45"></a>verze 4.5
* Opravit chyby, která způsobila, že instalace emulátoru úložiště dojde k chybě při zálohování databáze byla přejmenována a inicializace.

### <a name="version-44"></a>Verze 4.4
* Emulátor úložiště teď podporuje verze 2015-12-11 služby úložiště na koncové body služby objektů Blob, fronty a tabulky.
* Uvolňování paměti emulátor úložiště dat blob je nyní efektivnější při plánování práce s velkého počtu objektů BLOB.
* Opravit chyby, která způsobila, že kontejner seznamu ACL XML má být ověřen trochu jinak z jak službu úložiště dělá.
* Opravit chybu, která může někdy dojít, maximální a minimální hodnoty DateTime, aby byly hlášené v časovém pásmu nesprávné.

### <a name="version-43"></a>Verze 4.3
* Emulátor úložiště teď podporuje verze 2015-07-08 služby úložiště na koncové body služby objektů Blob, fronty a tabulky.

### <a name="version-42"></a>Verzi 4.2
* Emulátor úložiště teď podporuje verze 2015-04-05 služby úložiště na koncové body služby objektů Blob, fronty a tabulky.

### <a name="version-41"></a>Verze 4.1
* Emulátor úložiště teď podporuje verze 2015-02-21 služby úložiště na objekt Blob, Queue a Table koncové body služby, s výjimkou nové funkce připojit objektů Blob.
* Pokud používáte verzi služby storage, který ještě není podporovaný emulátorem, vrátí emulátoru smysluplný chybová zpráva. Doporučujeme používat nejnovější verze emulátoru. Pokud dojde k chybě VersionNotSupportedByEmulator (kód stavu HTTP 400 – Chybný požadavek), stáhněte si prosím nejnovější verze emulátoru úložiště.
* Opravit chyby ve kterém dat soupeření podmínku způsobila tabulka entity jako nesprávná během operace souběžných sloučení.

### <a name="version-40"></a>verze 4.0
* Emulátor úložiště spustitelný soubor byl přejmenován na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Verze 3.2
* Emulátor úložiště teď podporuje verze 2014-02-14 služby úložiště na koncové body služby objektů Blob, fronty a tabulky. Koncové body služby souboru nejsou aktuálně podporované v emulátoru úložiště. V tématu [Správa verzí pro služby Azure Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) podrobnosti o verzi 2014-02-14.

### <a name="version-31"></a>Verze 3.1
* Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) se teď podporuje v emulátoru úložiště. Získat statistiky služby objektů Blob, získat statistiky fronty služby a získat statistiky API služby tabulky jsou podporované pro sekundární účet a vždy vrátí hodnotu elementu LastSyncTime odpovědi jako aktuální čas podle základní databáze SQL. Pro programový přístup k sekundární pomocí emulátoru úložiště použijte knihovnu klienta služby Storage pro .NET verze 3.2 nebo vyšší. Podrobnosti najdete v Microsoft Azure Storage Client Library pro .NET – referenční informace.

### <a name="version-30"></a>Verze 3.0
* Emulátor úložiště Azure je už dodávané ve stejném balíčku jako emulátoru služby výpočty v.
* Grafické uživatelské rozhraní pro emulátor úložiště je zastaralý považuje Skriptovatelná rozhraní příkazového řádku. Podrobnosti na rozhraní příkazového řádku najdete v tématu odkaz nástroj příkazového řádku emulátoru úložiště. Grafické rozhraní bude i nadále existovat ve verzi 3.0, ale jenom k němu po nainstalování emulátoru výpočetní kliknutím pravým tlačítkem myši na ikonu hlavním panelu a výběrem možnosti zobrazit uživatelské prostředí emulátoru úložiště.
* Teď je plně podporuje verze 2013-08-15 služeb úložiště Azure. (Dřív byla tato verze pouze podporované v emulátoru úložiště verze 2.2.1 Preview.)

## <a name="next-steps"></a>Další postup

* Vyhodnocení emulátor úložiště napříč platformami, udržuje komunitní s otevřeným zdrojem [Azurite](https://github.com/arafato/azurite). 
* [Ukázek Azure Storage pomocí rozhraní .NET](../storage-samples-dotnet.md) obsahuje odkazy na několik ukázky kódu, můžete použít při vývoji aplikace.
* Můžete použít [Microsoft Azure Storage Explorer](http://storageexplorer.com) pro práci s prostředky ve vašem cloudu účet úložiště a v emulátoru úložiště.
