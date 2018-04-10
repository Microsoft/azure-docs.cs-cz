---
title: Úvod do Azure Storage | Dokumentace Microsoftu
description: Úvod do Azure Storage, datového úložiště v cloudu od Microsoftu.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 18a8065bba8a4a0ec2025d6b9134fe9fab21eb5f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Úvod do Microsoft Azure Storage

Microsoft Azure Storage je cloudová služba spravovaná Microsoftem, která poskytuje vysoce dostupné, zabezpečené, odolné, škálovatelné a redundantní úložiště. Microsoft se stará o údržbu a řeší za vás kritické problémy.

Azure Storage se skládá ze tří datových služeb: Blob Storage, File Storage a Queue Storage. Blob Storage podporuje Storage úrovně Standard i Premium a Storage úrovně Premium využívají jenom SSD pro zajištění nejvyššího možného výkonu. Další funkcí je studené úložiště, které umožňuje ukládat velké objemy zřídka využívaných dat s nižšími náklady.

V tomto článku jsou probrána následující témata:
* služby Azure Storage
* typy účtů úložiště
* přístup k objektům blob, frontám a souborům
* šifrování
* replikace
* přenos dat do nebo z úložiště
* velký počet dostupných klientských knihoven pro úložiště

Informace o zprovoznění s využitím Azure Storage najdete v tématu [Vytvoření účtu úložiště](storage-quickstart-create-account.md).

## <a name="introducing-the-azure-storage-services"></a>Seznámení se službami Azure Storage

Pokud chcete použít některou ze služeb, které poskytuje Azure Storage (Blob Storage, File Storage nebo Queue Storage), musíte nejdřív vytvořit účet úložiště. Potom v tomto účtu úložiště můžete přenést data do nebo z konkrétní služby.

## <a name="blob-storage"></a>Blob Storage

Bloby jsou v podstatě soubory podobné těm, které ukládáte ve svém počítači (nebo tabletu, mobilním zařízení atd.). Mohou to být obrázky, soubory Microsoft Excelu, soubory HTML, virtuální pevné disky (VHD), velké objemy dat, jako jsou protokoly nebo zálohy databází – prakticky cokoli. Objekty blob se ukládají v kontejnerech, které jsou obdobou složek.

Když uložíte soubory ve službě Blob Storage, můžete k nim přistupovat z libovolného místa na světě pomocí adres URL, rozhraní REST nebo některé z klientských knihoven pro úložiště Azure SDK. Klientské knihovny pro úložiště jsou dostupné pro řadu jazyků, včetně Node.js, Javy, PHP, Ruby, Pythonu a .NET.

Existují tři typy objektů blob – objekty blob bloku, objekty blob stránky (používané pro soubory VHD) a doplňovací objekty blob.

* Objekty blob bloku se používají k uložení obyčejných souborů až do velikosti 4,7 TB.
* Objekty blob stránky se používají k uložení souborů s náhodným přístupem až do velikosti 8 TB. Používají se pro soubory VHD, které zálohují virtuální počítače.
* Doplňovací objekty blob jsou tvořené bloky podobně jako objekty blob bloku, ale jsou optimalizované pro doplňovací operace. Využívají se k takovým věcem, jako je protokolování informací z několika virtuálních počítačů do stejného objektu blob.

V případě velkých datových sad, kde stahování nebo ukládání dat do Blob Storage přes internet není vzhledem k síťovým omezením reálné, můžete sadu pevných disků zaslat společnosti Microsoft, která data exportuje nebo importuje přímo v datovém centru. Další informace najdete v tématu [Přenos dat do Blob Storage pomocí služby Microsoft Azure Import/Export](../storage-import-export-service.md).

## <a name="azure-files"></a>Soubory Azure
Služba [Soubory Azure](../files/storage-files-introduction.md) umožňuje nastavit vysoce dostupné sdílené složky souborů sítě, ke kterým je možný přístup pomocí standardního protokolu SMB (Server Message Block). To znamená, že několik virtuálních počítačů může sdílet stejné soubory s oprávněním ke čtení i zápisu. Soubory můžete číst také pomocí rozhraní REST nebo klientských knihoven pro úložiště.

Jednou z věcí, která odlišuje Soubory Azure od souborů v podnikové sdílené složce je, že k souborům můžete přistupovat odkudkoli na světě pomocí adresy URL, která odkazuje na soubor a zahrnuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS, které po určitou dobu umožňují specifický přístup k privátním prostředkům.

Sdílené složky můžete použít pro řadu běžných scénářů:

* Mnoho místních aplikací používá sdílené složky. Tato funkce usnadňuje migraci aplikací sdílejících data do Azure. Pokud připojíte sdílenou složku ke stejnému písmenu jednotky, které používá místní aplikace, část aplikace pro přístup ke sdílené složce by měla fungovat s minimálními, pokud vůbec nějakými, změnami.

* Ve sdílené složce je možné ukládat konfigurační soubory, kde k nim bude mít přístup více virtuálních počítačů. Ve sdílené složce je možné ukládat nástroje používané více vývojáři ve skupině zajistit tak, že je všichni budou moci najít a že budou používat stejnou verzi.

* Diagnostické protokoly, metriky a výpisy stavu systému jsou jenom tři z příkladů dat, která je možné zapisovat do sdílené složky a zpracovávat nebo analyzovat později.

V současné době se nepodporuje ověřování založené na Active Directory ani seznamy ACL, někdy v budoucnu se ale podporovat budou. K ověřování přístupu ke sdílené složce se používají přihlašovací údaje účtu úložiště. To znamená, že všichni s připojenou sdílenou složkou k ní budou mít úplná oprávnění ke čtení i zápisu.

## <a name="queue-storage"></a>Queue Storage

Služba front Azure se využívá k ukládání a načítání zpráv. Fronty zprávy mohou mít velikost až 64 kB a jedna fronta může obsahovat miliony zpráv. Fronty se obecně používají k ukládání seznamů zpráv, které mají být zpracovány asynchronně.

Řekněme například, že chcete zákazníkům umožnit odesílání obrázků a pro každý obrázek chcete vytvořit miniatury. Můžete nechat zákazníky při odesílání obrázků čekat na to, až tyto miniatury vytvoříte. Alternativou může být použití fronty. Když zákazník dokončí nahrávání, zapište zprávu do fronty. Potom nechte funkci Azure Functions, aby načetla tuto zprávu z fronty a vytvořila miniatury. Jednotlivé části tohoto zpracování je možné škálovat samostatně. Získáte tak větší kontrolu při jeho vylaďování pro vaše využití.

## <a name="table-storage"></a>Úložiště Table

Azure Table Storage je teď součástí služby Azure Cosmos DB. Dokumentaci ke službě Azure Table Storage najdete v tématu [Přehled služby Azure Table Storage](../../cosmos-db/table-storage-overview.md). Kromě existující služby Azure Table Storage je k dispozici také nabídka nového rozhraní API tabulky Azure Cosmos DB, které poskytuje tabulky s optimalizovanou propustností, globální distribuci a automatické sekundární indexy. Další informace a možnost vyzkoušet si nové prostředí úrovně Premium najdete na stránce [Rozhraní API tabulky Azure Cosmos DB](https://aka.ms/premiumtables).

## <a name="disk-storage"></a>Diskové úložiště

Azure Storage také zahrnuje funkce spravovaných a nespravovaných disků využívaných virtuálními počítači. Další informace o těchto funkcích najdete v [dokumentaci ke Compute Services](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Tato tabulka uvádí různé typy účtů úložiště a to, které objekty je pro ně možné využít.

|**Typ účtu úložiště**|**Standard pro obecné účely**|**Premium pro obecné účely**|**Blob Storage, horká a studená vrstva přístupu**|
|-----|-----|-----|-----|
|**Podporované služby**| Služby objektů blob, souborů a tabulek | Služba objektů blob | Služba objektů blob|
|**Typy podporovaných objektů blob**|Objekty blob bloku, objekty blob stránky a doplňovací objekty blob | Objekty blob stránky | Objekty blob bloku a doplňovací objekty blob|

### <a name="general-purpose-storage-accounts"></a>Účty úložiště pro obecné účely

Existují dva typy účtů úložiště pro obecné účely.

#### <a name="standard-storage"></a>Storage úrovně Standard

Nejčastěji používanými účty úložiště jsou standardní účty, které je možné použít pro všechny typy dat. Účty úložiště úrovně Standard k ukládání dat používají magnetická média.

#### <a name="premium-storage"></a>Storage úrovně Premium

Storage úrovně Premium poskytuje vysoce výkonné úložiště pro objekty blob stránky, které se primárně využívají pro soubory VHD. Účty úložiště úrovně Premium k ukládání dat používají SSD. Microsoft doporučuje používat Storage úrovně Premium pro všechny vaše virtuální počítače.

### <a name="blob-storage-accounts"></a>Účty služby Blob Storage

Účet služby Blob Storage je specializovaný účet úložiště používaný k ukládání objektů blob bloku a doplňovacích objektů blob. V těchto účtech nejde ukládat objekty blob stránky, a proto nemůžete ukládat soubory VHD. Tyto účty umožňují nastavit horkou nebo studenou vrstvu přístupu. Tuto vrstvu můžete kdykoli změnit.

Horká vrstva přístupu se používá pro soubory, ke kterým se přistupuje často – platíte vyšší náklady na úložiště, ale náklady na přístup k objektům blob jsou mnohem nižší. U objektů blob uložených ve studené vrstvě přístupu platíte vyšší náklady na přístup, ale náklady na úložiště jsou mnohem nižší.

## <a name="accessing-your-blobs-files-and-queues"></a>Přístup k objektům blob, frontám a souborům

Každý účet úložiště má dva ověřovací klíče a každý z nich je možné použít pro libovolnou operaci. Dva klíče se používají proto, abyste je mohli příležitostně prohodit pro zvýšení zabezpečení. Je velmi důležité, aby tyto klíče byly zabezpečené, protože jejich vlastnictví spolu s názvem účtu umožňuje neomezený přístup ke všem datům v účtu úložiště.

V této části se probírají dva způsoby, jak zabezpečit účet úložiště a jeho data. Podrobné informace o zabezpečení vašeho účtu úložiště a vašich dat najdete v [průvodci zabezpečením Azure Storage](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Zabezpečení přístupu k účtům úložiště pomocí služby Azure AD

Jedním ze způsobů zabezpečení přístupu k datům úložiště je řízení přístupu ke klíčům účtu úložiště. Pomocí řízení přístupu na základě role (RBAC) Resource Manageru můžete přiřazovat role uživatelů, skupinám nebo aplikacím. Tyto role jsou svázané s konkrétní sadou akcí, které jsou povolené nebo zakázané. Použití RBAC pro udělení přístupu k účtu úložiště zajišťuje pro daný účet úložiště jenom operace správy, jako je třeba změna vrstvy přístupu. RBAC se nedá použít pro udělení přístupu k datovým objektům, jako je třeba konkrétní kontejner nebo sdílená složka. Můžete ho ale použít pro udělení přístupu ke klíčům účtu úložiště, které se potom dají využít ke čtení datových objektů.

### <a name="securing-access-using-shared-access-signatures"></a>Zabezpečení přístupu pomocí sdílených přístupových podpisů

K zabezpečení vašich datových objektů můžete využít sdílené přístupové podpisy a uložené zásady přístupu. Sdílený přístupový podpis (SAS) je řetězec obsahující token zabezpečení, který je možné připojit k identifikátoru URI pro určitý prostředek, který umožňuje delegovat přístup ke konkrétním objektům úložiště a zadat omezení, jako je třeba oprávnění a datový/časový rozsah přístupu. Tato funkce má rozsáhlé možnosti. Podrobné informace najdete v tématu [Použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md).

### <a name="public-access-to-blobs"></a>Veřejný přístup k objektům blob

Služba objektů blob umožňuje poskytnout veřejný přístup ke kontejneru a jeho objektům blob nebo ke konkrétnímu objektu blob. Když nějaký kontejner nebo objekt blob označíte jako veřejně přístupný, kdokoli si ho může anonymně přečíst bez nutnosti ověření. Příkladem, kdy se to může hodit, je situace, kdy máte web využívající obrázky, video nebo dokumenty ze služby Blob Storage. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Šifrování

Pro služby Storage jsou k dispozici dva základní druhy šifrování. Další informace o zabezpečení a šifrování najdete v [průvodci zabezpečením Azure Storage](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Šifrování služby Azure Storage (SSE) v klidovém stavu pomáhá zabezpečit a chránit vaše data pro splnění závazků organizace souvisejících se zabezpečením a dodržováním předpisů. Pomocí této funkce služba Azure Storage automaticky šifruje vaše data před zachováním v úložišti a dešifruje před jejich načtením. Šifrování, dešifrování a správa klíčů je pro uživatele zcela transparentní.

SSE automaticky šifruje data na všech úrovních výkonu (Standard a Premium), ve všech modelech nasazení (Azure Resource Manager a Classic) a ve všech službách Azure Storage (Blob, Queue, Table a File). SSE nemá vliv na výkon služby Azure Storage.

Další informace o šifrování SSE v klidovém stavu najdete v tématu[Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Klientské knihovny pro úložiště nabízejí metody, jejichž voláním můžete programově zašifrovat data, než je odešlete sítí z klienta do Azure. Uloží se zašifrovaná a to znamená, že jsou zašifrovaná, i když jsou neaktivní. Při načítání dat zpět informace po přijetí dešifrujete.

Další informace o šifrování na straně klienta najdete v tématu [Šifrování na straně klienta s .NET pro úložiště Microsoft Azure](storage-client-side-encryption.md).

## <a name="replication"></a>Replikace

Aby se zajistila odolnost dat, služba Azure Storage replikuje několik kopií vašich dat. Když nastavujete účet úložiště, vybíráte typ replikace. Ve většině případů toto nastavení můžete po vytvoření účtu úložiště změnit. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Informace o zotavení po havárii najdete v tématu [Co dělat v případě výpadku služby Azure Storage](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Přesun dat z Azure Storage a do Azure Storage

Pomocí nástroje příkazového řádku AzCopy můžete kopírovat objekt blob a data souboru v rámci svého účtu úložiště nebo mezi různými účty úložiště. Nápovědu získáte v jednom z těchto článků:

* [Přenos dat pomocí nástroje AzCopy pro Windows](storage-use-azcopy.md)
* [Přenos dat pomocí nástroje AzCopy pro Linux](storage-use-azcopy-linux.md)

Nástroj AzCopy je postavený na [Knihovně pro přesun dat v Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), která je aktuálně dostupná v předběžné ukázkové verzi.

Službu Azure Import/Export můžete použít pro import nebo export velkých objemů dat objektů blob do nebo z účtu úložiště. Připravíte několik pevných disků a odešlete je poštou do datového centra Azure, kde se provede přenos dat z těchto pevných disků nebo na ně a odešlou se zpátky k vám. Další informace o službě Import/Export najdete v tématu [Přenos dat do Blob Storage pomocí služby Microsoft Azure Import/Export](../storage-import-export-service.md).

## <a name="pricing"></a>Ceny

Podrobné informace o cenách pro Azure Storage najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Rozhraní API, knihovny a nástroje služby Storage
Prostředky Azure Storage jsou dostupné přes jakýkoli jazyk, který umí vytvářet požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují spoustu aspektů práce s Azure Storage, protože se starají o drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek, automatické opakování pokusů, operační chování atd. Knihovny jsou aktuálně dostupné pro následující jazyky a platformy, další se připravují:

### <a name="azure-storage-data-services"></a>Datové služby Azure Storage
* [REST API služby Storage](/rest/api/storageservices/)
* [Klientská knihovna pro úložiště pro .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Klientská knihovna pro úložiště pro C++](https://github.com/Azure/azure-storage-cpp)
* [Klientská knihovna pro úložiště pro Javu/Android](https://azure.microsoft.com/develop/java/)
* [Klientská knihovna pro úložiště pro Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Klientská knihovna pro úložiště pro PHP](https://azure.microsoft.com/develop/php/)
* [Klientská knihovna pro úložiště pro Python](https://azure.microsoft.com/develop/python/)
* [Klientská knihovna pro úložiště pro Ruby](https://azure.microsoft.com/develop/ruby/)
* [Rutiny pro úložiště pro PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Příkazy úložiště pro CLI 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Další kroky

* [Další informace o službě Blob Storage](../blobs/storage-blobs-introduction.md)
* [Další informace o službě File Storage](../storage-files-introduction.md)
* [Další informace o službě Queue Storage](../queues/storage-queues-introduction.md)

Informace o zprovoznění s využitím Azure Storage najdete v tématu [Vytvoření účtu úložiště](storage-quickstart-create-account.md).

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Pro správce
* [Použití Azure Powershell s Azure Storage](storage-powershell-guide-full.md)
* [Použití Azure CLI s Azure Storage](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Pro vývojáře v rozhraní .NET
* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Vývoj pro Soubory Azure pomocí .NET](../files/storage-dotnet-how-to-use-files.md)
* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Pro vývojáře v Javě a Androidu
* [Používání úložiště Blob z Javy](../blobs/storage-java-how-to-use-blob-storage.md)
* [Vývoj pro Soubory Azure pomocí Javy](../files/storage-java-how-to-use-file-storage.md)
* [Používání úložiště Table z Javy](../../cosmos-db/table-storage-how-to-use-java.md)
* [Používání úložiště Queue z Javy](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Pro vývojáře v Node.js
* [Používání úložiště Blob z Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Používání úložiště Table z Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Používání úložiště Queue z Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Pro vývojáře v PHP
* [Používání úložiště Blob z PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Používání úložiště Table z PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Používání úložiště Queue z PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Pro vývojáře v Ruby
* [Používání úložiště Blob z Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Používání úložiště Table z Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Používání úložiště Queue z Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Pro vývojáře v Pythonu
* [Používání úložiště Blob z Pythonu](../blobs/storage-python-how-to-use-blob-storage.md)
* [Vývoj pro Soubory Azure pomocí Pythonu](../files/storage-python-how-to-use-file-storage.md)
* [Používání úložiště Table z Pythonu](../../cosmos-db/table-storage-how-to-use-python.md)
* [Používání úložiště Queue z Pythonu](../storage-python-how-to-use-queue-storage.md)
