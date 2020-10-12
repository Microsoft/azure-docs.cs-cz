---
title: Použití emulátoru Azure Storage pro vývoj a testování
description: Emulátor Azure Storage poskytuje bezplatné místní vývojové prostředí pro vývoj a testování vašich Azure Storagech aplikací.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 959a58a38861075c6509fe57136d8991eeb98ce6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588190"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Použití emulátoru Azure Storage pro vývoj a testování

Emulátor úložiště Microsoft Azure je nástroj, který emuluje služby Azure Blob, Queue a Table pro účely místního vývoje. Svou aplikaci můžete testovat i v místním úložišti, aniž byste museli vytvářet předplatné Azure nebo náklady. Až budete spokojeni s tím, jak vaše aplikace funguje v emulátoru, přepněte na použití účtu úložiště Azure v cloudu.

> [!IMPORTANT]
> Emulátor Azure Storage se už aktivně nevyvíjí. [**Azurite**](storage-use-azurite.md) je platforma pro emulátor úložiště, která přechází. Azurite nahrazuje emulátor Azure Storage. Azurite se bude dál aktualizovat, aby podporovala nejnovější verze rozhraní Azure Storage API. Další informace najdete v tématu [**použití emulátoru Azurite pro vývoj místních Azure Storage**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>Získání emulátoru úložiště

Emulátor úložiště je k dispozici jako součást [sady Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště můžete nainstalovat také pomocí [samostatného instalačního programu](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (přímo ke stažení). K instalaci emulátoru úložiště musíte mít v počítači oprávnění správce.

Emulátor úložiště se v tuto chvíli spouští jenom v systému Windows. Pokud potřebujete emulátor úložiště pro Linux, jedna možnost je udržovaná komunitou, open source emulátor úložiště [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Data vytvořená v jedné verzi emulátoru úložiště nejsou zaručená k přístupu při použití jiné verze. Pokud potřebujete zachovat data po dlouhou dobu, doporučujeme, abyste tato data ukládali v účtu služby Azure Storage, nikoli v emulátoru úložiště.
> 
> Emulátor úložiště závisí na konkrétních verzích knihoven OData. Nahrazení knihoven DLL OData používaných emulátorem úložiště v jiných verzích se nepodporuje a může způsobit neočekávané chování. K posílání požadavků na emulátor se ale dá použít libovolná verze OData podporovaná službou úložiště.

## <a name="how-the-storage-emulator-works"></a>Jak emulátor úložiště funguje

Emulátor úložiště používá místní instanci Microsoft SQL Server 2012 Express LocalDB k emulaci služeb Azure Storage. Místo instance LocalDB můžete nakonfigurovat emulátor úložiště pro přístup k místní instanci SQL Server. Další informace najdete v části [spuštění a inicializace emulátoru úložiště](#start-and-initialize-the-storage-emulator) dále v tomto článku.

Emulátor úložiště se připojuje k SQL Server nebo LocalDB pomocí ověřování systému Windows.

Mezi emulátor úložiště a služby Azure Storage existují některé rozdíly ve funkčnosti. Další informace o těchto rozdílech najdete v částech o [rozdílech mezi emulátorem úložiště a Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) části dále v tomto článku.

## <a name="start-and-initialize-the-storage-emulator"></a>Spuštění a inicializace emulátoru úložiště

Spuštění emulátoru Azure Storage:

1. Vyberte tlačítko **Start** nebo stiskněte klávesu **Windows** .
2. Začněte psát `Azure Storage Emulator` .
3. Vyberte emulátor ze seznamu zobrazených aplikací.

Po spuštění emulátoru úložiště se zobrazí okno příkazového řádku. Pomocí tohoto okna konzoly můžete spustit a zastavit emulátor úložiště. Můžete také vymazat data, získat stav a inicializovat emulátor z příkazového řádku. Další informace najdete v části referenční informace k [nástroji příkazového řádku emulátoru úložiště](#storage-emulator-command-line-tool-reference) dále v tomto článku.

> [!NOTE]
> Emulátor Azure Storage se nemusí správně spustit, pokud je v systému spuštěný jiný emulátor úložiště, například Azurite.

Po spuštění emulátoru se v oznamovací oblasti hlavního panelu Windows zobrazí jeho ikona.

Po zavření okna příkazového řádku emulátoru úložiště bude emulátor úložiště nadále spuštěn. Chcete-li znovu spustit okno konzoly emulátoru úložiště, postupujte podle předchozích kroků, jako je spuštění emulátoru úložiště.

Při prvním spuštění emulátoru úložiště se místní prostředí úložiště inicializuje za vás. Proces inicializace vytvoří databázi v LocalDB a rezervuje porty HTTP pro každou místní službu úložiště.

Emulátor úložiště je ve výchozím nastavení nainstalován do nástroje `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> K práci s prostředky emulátoru místního úložiště můžete použít [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com) . Po instalaci a spuštění emulátoru úložiště vyhledejte "(výchozí porty emulátoru) (klíč)" v části "místní & připojená" ve stromu Průzkumník služby Storage prostředků.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializace emulátoru úložiště pro použití jiné databáze SQL

Pomocí nástroje příkazového řádku emulátoru úložiště můžete inicializovat emulátor úložiště, aby odkazoval na jinou instanci služby SQL Database, než je výchozí instance LocalDB:

1. Otevřete okno konzoly emulátoru úložiště, jak je popsáno v části [spuštění a inicializace emulátoru úložiště](#start-and-initialize-the-storage-emulator) .
1. V okně konzoly zadejte následující příkaz, kde `<SQLServerInstance>` je název instance SQL Server. Chcete-li použít LocalDB, zadejte `(localdb)\MSSQLLocalDb` jako instanci SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Můžete také použít následující příkaz, který přesměruje emulátor na použití výchozí instance SQL Server:

   `AzureStorageEmulator.exe init /server .`

   Nebo můžete použít následující příkaz, který inicializuje databázi na výchozí instanci LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Další informace o těchto příkazech najdete v [referenčních informacích k nástroji příkazového řádku emulátoru úložiště](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Ke správě instancí služby SQL Server, včetně instalace LocalDB, můžete použít [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). V dialogovém okně SMSS **připojit k serveru** zadejte `(localdb)\MSSQLLocalDb` do pole **název serveru:** připojení k instanci LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Ověřování požadavků pomocí emulátoru úložiště

Po instalaci a spuštění emulátoru úložiště můžete svůj kód otestovat proti němu. Každý požadavek, který provedete u emulátoru úložiště, musí být autorizován, pokud se nejedná o anonymní požadavek. Požadavky na emulátor úložiště můžete autorizovat pomocí ověřování pomocí sdíleného klíče nebo pomocí sdíleného přístupového podpisu (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizovat s přihlašovacími údaji sdíleného klíče

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Další informace o připojovacích řetězcích najdete v tématu [konfigurace Azure Storage připojovacích řetězců](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizovat pomocí sdíleného přístupového podpisu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Některé klientské knihovny pro Azure Storage, jako je například knihovna Xamarin, podporují pouze ověřování pomocí tokenu sdíleného přístupového podpisu (SAS). Token SAS můžete vytvořit pomocí [Průzkumník služby Storage](https://storageexplorer.com/) nebo jiné aplikace, která podporuje ověřování pomocí sdíleného klíče.

Token SAS můžete vygenerovat také pomocí Azure PowerShell. Následující příklad vygeneruje token SAS s úplnými oprávněními k kontejneru objektů BLOB:

1. Pokud jste to ještě neudělali (doporučujeme použít nejnovější verzi rutin Azure PowerShell, nainstalujte Azure PowerShell). Pokyny k instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/install-Az-ps).
2. Otevřete Azure PowerShell a spusťte následující příkazy, které nahradíte názvem, který `CONTAINER_NAME` zvolíte:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Výsledný identifikátor URI signatury sdíleného přístupového podpisu pro nový kontejner by měl vypadat přibližně takto:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Sdílený přístupový podpis vytvořený v tomto příkladu je platný po dobu jednoho dne. Signatura udělí úplný přístup (čtení, zápis, odstranění, výpis) objektů BLOB v rámci kontejneru.

Další informace o sdílených přístupových podpisech najdete v tématu [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Adresování prostředků v emulátoru úložiště

Koncové body služby pro emulátor úložiště se liší od koncových bodů pro účet úložiště Azure. V místním počítači není rozlišování názvů domén, což vyžaduje, aby byly koncovými body emulátoru úložiště místní adresy.

Při adresování prostředku v účtu služby Azure Storage použijete následující schéma. Název účtu je součástí názvu hostitele URI a prostředek, který se řeší, je součástí cesty URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Například následující identifikátor URI je platná adresa pro objekt BLOB v účtu úložiště Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Vzhledem k tomu, že místní počítač neprovádí překlad názvů domén, je název účtu součástí cesty identifikátoru URI místo názvu hostitele. Pro prostředek v emulátoru úložiště použijte následující formát identifikátoru URI:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

K přístupu k objektu BLOB v emulátoru úložiště můžete například použít následující adresu:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Koncový bod služby pro emulátor úložiště:

* Blob service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Služba front: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adresování sekundárního účtu pomocí RA-GRS

Počínaje verzí 3,1 podporuje emulátor úložiště geograficky redundantní replikaci s přístupem pro čtení (RA-GRS). K sekundárnímu umístění můžete přistupovat připojením-Secondary k názvu účtu. Například následující adresa může být použita pro přístup k objektu BLOB pomocí sekundárního souboru jen pro čtení v emulátoru úložiště:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Pro programový přístup k sekundárnímu nástroji pomocí emulátoru úložiště použijte klientskou knihovnu pro úložiště pro .NET verze 3,2 nebo novější. Podrobnosti najdete v tématu [Microsoft Azure Storage Klientská knihovna pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Reference k nástroji příkazového řádku emulátoru úložiště

Počínaje verzí 3,0 se okno konzoly zobrazí při spuštění emulátoru úložiště. Pomocí příkazového řádku v okně konzoly spusťte a zastavte emulátor. Můžete také zadat dotaz na stav a provést jiné operace z příkazového řádku.

> [!NOTE]
> Pokud máte nainstalován emulátor Microsoft Azure COMPUTE, zobrazí se při spuštění emulátoru úložiště ikona hlavního panelu systému. Kliknutím pravým tlačítkem myši na ikonu zobrazíte nabídku, která poskytuje grafický způsob spuštění a zastavení emulátoru úložiště.
>
>

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Možnosti

Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `/help`.

| Možnost | Popis | Příkaz | Argumenty |
| --- | --- | --- | --- |
| **Zahájení** |Spustí emulátor úložiště. |`AzureStorageEmulator.exe start [-inprocess]` |*-Rezpracovat*: spustí emulátor v aktuálním procesu místo vytvoření nového procesu. |
| **Zastavit** |Zastaví emulátor úložiště. |`AzureStorageEmulator.exe stop` | |
| **Stav** |Vytiskne stav emulátoru úložiště. |`AzureStorageEmulator.exe status` | |
| **Vymazat** |Vymaže data ve všech službách zadaných v příkazovém řádku. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*BLOB*: vymaže data objektu BLOB. <br/>*Queue*: vymaže data ve frontě. <br/>*Table*: vymaže data tabulky. <br/>*All*: vymaže všechna data ve všech službách. |
| **Init** |Provede jednorázovou inicializaci pro nastavení emulátoru. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Server serverName\instanceName*: Určuje server, který je hostitelem instance SQL. <br/>*-SQLInstance instance*: Určuje název instance SQL, která se má použít ve výchozí instanci serveru. <br/>*-forceCreate*: vynutí vytvoření databáze SQL, a to i v případě, že již existuje. <br/>*-skipcreate*: přeskočí vytvoření databáze SQL. To má přednost před – forceCreate.<br/>*-reserveports*: pokusí se rezervovat porty HTTP přidružené ke službám.<br/>*-unreserveports*: pokusí se odebrat rezervace portů http přidružených ke službám. To má přednost před – reserveports.<br/>*-InProcess*: provede inicializaci v aktuálním procesu místo vytvoření nového procesu. Při změně rezervací portů je nutné spustit aktuální proces se zvýšenými oprávněními. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Rozdíly mezi emulátorem úložiště a Azure Storage

Vzhledem k tomu, že emulátor úložiště je místní emulované prostředí, existují rozdíly mezi použitím emulátoru a účtu úložiště Azure v cloudu:

* Emulátor úložiště podporuje jenom jeden pevný účet a dobře známý ověřovací klíč.
* Emulátor úložiště není škálovatelná služba úložiště a nepodporuje velký počet souběžných klientů.
* Jak je popsáno v tématu [adresování prostředků v emulátoru úložiště](#addressing-resources-in-the-storage-emulator), se prostředky řeší odlišně v emulátoru úložiště a v účtu úložiště Azure. Rozdíl je v tom, že překlad názvů domény je k dispozici v cloudu, ale ne v místním počítači.
* Počínaje verzí 3,1 podporuje účet emulátoru úložiště geograficky redundantní replikaci s přístupem pro čtení (RA-GRS). V emulátoru mají všechny účty povolený RA-GRS a nikdy neexistují žádné prodlevy mezi primárními a sekundárními replikami. Na sekundárním účtu se podporují operace získat statistiku služby blob, získat statistiky služby Queue Service a získat statistiku služby Table Service a vždycky vrátí hodnotu `LastSyncTime` elementu Response jako aktuální čas podle základní databáze SQL.
* Koncové body služby Souborové služby a protokolu SMB se v emulátoru úložiště aktuálně nepodporují.
* Pokud používáte verzi služby úložiště, která není podporována emulátorem, emulátor vrátí chybu VersionNotSupportedByEmulator (kód stavu HTTP 400-Chybný požadavek).

### <a name="differences-for-blob-storage"></a>Rozdíly pro úložiště objektů BLOB

Pro úložiště objektů BLOB v emulátoru platí následující rozdíly:

* Emulátor úložiště podporuje jenom velikosti objektů BLOB až do 2 GB.
* Maximální délka názvu objektu BLOB v emulátoru úložiště je 256 znaků, zatímco maximální délka názvu objektu BLOB v Azure Storage je 1024 znaků.
* Přírůstkové kopírování umožňuje zkopírovat snímky z přepsaných objektů blob, což vrátí chybu služby.
* Získat rozdíl mezi snímky zkopírovanými pomocí objektu BLOB přírůstkového kopírování nefunguje rozdíl v rozsahu stránek
* Operace Put BLOB může být úspěšná proti objektu blob, který existuje v emulátoru úložiště s aktivním zapůjčením, a to i v případě, že v požadavku není zadané ID zapůjčení.
* Emulátor nepodporuje operace připojení objektu BLOB. Pokus o operaci na doplňovacím objektu BLOB vrátí chybu FeatureNotSupportedByEmulator (kód stavu HTTP 400-Chybný požadavek).

### <a name="differences-for-table-storage"></a>Rozdíly pro úložiště tabulek

Následující rozdíly platí pro úložiště tabulek v emulátoru:

* Vlastnosti data v Table service v emulátoru úložiště podporují pouze rozsah podporovaný SQL Server 2005 (musí být pozdější než 1. ledna 1753). Všechna data před 1. ledna 1753 budou změněna na tuto hodnotu. Přesnost dat je omezená na přesnost SQL Server 2005, což znamená, že data jsou přesně na 1/300th sekundy.
* Emulátor úložiště podporuje hodnoty vlastností klíče oddílu a klíče řádku, které mají méně než 512 bajtů. Celková velikost názvu účtu, názvu tabulky a názvů klíčových vlastností dohromady nesmí překročit 900 bajtů.
* Celková velikost řádku v tabulce v emulátoru úložiště je omezená na méně než 1 MB.
* V emulátoru úložiště, vlastnosti datového typu `Edm.Guid` nebo `Edm.Binary` podporují pouze `Equal (eq)` `NotEqual (ne)` operátory porovnání a v řetězcích filtru dotazů.

### <a name="differences-for-queue-storage"></a>Rozdíly pro úložiště Queue

V emulátoru nejsou žádné rozdíly specifické pro úložiště Queue.

## <a name="storage-emulator-release-notes"></a>Poznámky k verzi emulátoru úložiště

### <a name="version-510"></a>Verze 5,10

* Emulátor úložiště odmítne verzi 2019-07-07 služeb úložiště v koncových bodech blob, Queue a Table Service.

### <a name="version-59"></a>Verze 5,9

* Emulátor úložiště odmítne verzi 2019-02-02 služeb úložiště v koncových bodech blob, Queue a Table Service.

### <a name="version-58"></a>Verze 5,8

* Emulátor úložiště odmítne verzi 2018-11-09 služeb úložiště v koncových bodech blob, Queue a Table Service.

### <a name="version-57"></a>Verze 5,7

* Opravili jsme chybu, která by způsobila chybu v případě povolení protokolování.

### <a name="version-56"></a>Verze 5,6

* Emulátor úložiště teď podporuje verze 2018-03-28 služeb úložiště v koncových bodech blob, Queue a Table service.

### <a name="version-55"></a>Verze 5,5

* Emulátor úložiště teď podporuje verze 2017-11-09 služeb úložiště v koncových bodech blob, Queue a Table service.
* Přidala se podpora pro vlastnost **vytvořeného** objektu blob, která vrací čas vytvoření objektu BLOB.

### <a name="version-54"></a>Verze 5,4

* Za účelem vylepšení stability při instalaci se emulátor již nepokouší rezervovat porty v době instalace. Pokud chcete rezervace portů, určete je pomocí možnosti *-reserveports* příkazu **init** .

### <a name="version-53"></a>Verze 5,3

* Emulátor úložiště teď podporuje verze 2017-07-29 služeb úložiště v koncových bodech blob, Queue a Table service.

### <a name="version-52"></a>Verze 5,2

* Emulátor úložiště teď podporuje verze 2017-04-17 služeb úložiště v koncových bodech blob, Queue a Table service.
* Byla opravena chyba, kdy hodnoty vlastností tabulky nebyly správně kódovány.

### <a name="version-51"></a>Verze 5,1

* Opravili jsme chybu, kdy emulátor úložiště vrátil `DataServiceVersion` hlavičku v některých odpovědích, kde služba nebyla.

### <a name="version-50"></a>Verze 5,0

* Instalační program emulátoru úložiště už nekontroluje existující instalaci MSSQL a .NET Framework.
* Instalační program emulátoru úložiště již v rámci instalace nevytváří databázi. Databáze bude v případě potřeby vytvořena v rámci spuštění.
* Vytvoření databáze již nevyžaduje zvýšení oprávnění.
* Pro spuštění už nejsou potřeba žádné rezervace portů.
* Přidá následující možnosti `init` : `-reserveports` (vyžaduje zvýšení oprávnění), `-unreserveports` (vyžaduje zvýšení oprávnění) `-skipcreate` .
* Možnost uživatelského rozhraní emulátoru úložiště v ikoně na hlavním panelu systému teď spouští rozhraní příkazového řádku. Starý GUI již není k dispozici.
* Některé knihovny DLL byly odebrány nebo přejmenovány.

### <a name="version-46"></a>Verze 4,6

* Emulátor úložiště teď podporuje verze 2016-05-31 služeb úložiště v koncových bodech blob, Queue a Table service.

### <a name="version-45"></a>Verze 4,5

* Opravili jsme chybu, která způsobila selhání instalace a inicializace při přejmenování záložní databáze.

### <a name="version-44"></a>Verze 4,4

* Emulátor úložiště teď podporuje verze 2015-12-11 služeb úložiště v koncových bodech blob, Queue a Table service.
* Shromažďování paměti dat objektů BLOB emulátoru úložiště je teď efektivnější při práci s velkým počtem objektů BLOB.
* Opravili jsme chybu, která způsobila, že XML seznamu ACL kontejneru se mírně liší od způsobu, jakým služba úložiště dělá.
* Opravili jsme chybu, která někdy způsobila, že se v nesprávném časovém pásmu nahlásí maximální a minimální hodnoty DateTime.

### <a name="version-43"></a>Verze 4,3

* Emulátor úložiště teď podporuje verze 2015-07-08 služeb úložiště v koncových bodech blob, Queue a Table service.

### <a name="version-42"></a>Verze 4,2

* Emulátor úložiště teď podporuje verze 2015-04-05 služeb úložiště v koncových bodech blob, Queue a Table service.

### <a name="version-41"></a>Verze 4,1

* Emulátor úložiště teď podporuje verze 2015-02-21 služeb úložiště v koncových bodech blob, Queue a Table service. Nepodporuje nové funkce přidávání objektů BLOB.
* Emulátor nyní vrátí smysluplnou chybovou zprávu pro nepodporované verze služeb úložiště. Doporučujeme použít nejnovější verzi emulátoru. Pokud se zobrazí chyba VersionNotSupportedByEmulator (kód stavu HTTP 400 – Chybný požadavek), Stáhněte si nejnovější verzi emulátoru.
* Opravili chybu, na kterou konflikt časování způsobil, že data entity tabulky v průběhu souběžných operací sloučení nejsou správná.

### <a name="version-40"></a>Verze 4,0

* Spustitelný soubor emulátoru úložiště byl přejmenován na *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Verze 3,2

* Emulátor úložiště teď podporuje verze 2014-02-14 služeb úložiště v koncových bodech blob, Queue a Table service. V emulátoru úložiště se aktuálně nepodporují koncové body souborové služby. Podrobnosti o verzi 2014-02-14 najdete v tématu [Správa verzí pro službu Azure Storage Services](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>Verze 3,1

* V emulátoru úložiště se teď podporuje geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). `Get Blob Service Stats` `Get Queue Service Stats` Rozhraní API, a `Get Table Service Stats` jsou podporovaná pro sekundární účet a vždycky vrátí hodnotu prvku LastSyncTime Response jako aktuální čas podle podkladové databáze SQL. Pro programový přístup k sekundárnímu nástroji pomocí emulátoru úložiště použijte klientskou knihovnu pro úložiště pro .NET verze 3,2 nebo novější. Podrobnosti najdete v referenčních informacích k klientské knihovně Microsoft Azure Storage pro .NET.

### <a name="version-30"></a>Verze 3,0

* Emulátor Azure Storage již není dodáván ve stejném balíčku jako emulátor pro výpočty.
* Grafické uživatelské rozhraní emulátoru úložiště je zastaralé. Byl nahrazen rozhraním skriptového příkazového řádku. Podrobnosti o rozhraní příkazového řádku najdete v tématu Command-Line – emulátor úložiště – Reference k nástroji. Grafické rozhraní bude nadále k dispozici ve verzi 3,0, ale je k němu možné přistupovat pouze v případě, že se emulátor služby COMPUTE nainstaluje kliknutím pravým tlačítkem na ikonu na hlavním panelu systému a výběrem možnosti zobrazit uživatelské rozhraní emulátoru úložiště.
* Služba Azure Storage je teď plně podporovaná ve verzi 2013-08-15. (Dřív byla tato verze podporovaná jenom emulátorem úložiště verze 2.2.1 Preview.)

## <a name="next-steps"></a>Další kroky

* Vyhodnoťte [Azurite](https://github.com/azure/azurite)emulátoru Open Source úložiště pro různé platformy, který komunita udržuje. 
* [Azure Storage ukázky používající rozhraní .NET](../storage-samples-dotnet.md) obsahují odkazy na několik ukázek kódu, které můžete použít při vývoji aplikace.
* [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com) můžete použít pro práci s prostředky ve vašem účtu cloudového úložiště a v emulátoru úložiště.

## <a name="see-also"></a>Viz také

* [Vývoj místních Azure Storage pomocí Azurite, sad Azure SDK a Průzkumník služby Azure Storage](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
