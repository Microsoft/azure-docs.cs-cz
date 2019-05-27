---
title: Seznámení se službou Azure Storage – cloudové úložiště v Azure | Microsoft Docs
description: Azure Storage je řešení cloudového úložiště Microsoftu. Poskytuje vysoce dostupné, zabezpečené, odolné, široce škálovatelné a redundantní úložiště pro datové objekty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b03197fb0d4d97756e5a3fe88de78c11a816b5ec
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951270"
---
# <a name="introduction-to-azure-storage"></a>Seznámení se službou Azure Storage

Azure Storage je řešení cloudového úložiště Microsoftu pro scénáře moderního datového úložiště. Nabízí široce škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro cloud, úložiště pro přenos zpráv zajišťující spolehlivé zasílání zpráv a úložiště NoSQL. Azure Storage je:

- **Odolné a vysoce dostupné:** Redundance zajišťuje, že vaše data budou v případě krátkodobého selhání hardwaru v bezpečí. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná. 
- **Zabezpečené:** Všechna data a zapsaná do služby Azure Storage jsou touto službou šifrovaná. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Škálovatelné:** Azure Storage je navržené pro širokou škálovatelnost, aby splňovalo požadavky dnešních aplikací na datové úložiště a výkon. 
- **Spravované:** Microsoft Azure stará o údržbu hardwaru, aktualizace a kritické problémy za vás.
- **Přístupné:** Data ve službě Azure Storage jsou přístupná prostřednictvím protokolu HTTP nebo HTTPS odkudkoli na světě. Microsoft poskytuje sady SDK pro Azure Storage v mnoha různých jazycích – .NET, Java, Node.js, Python, PHP, Ruby, Go a další – a také vyspělé rozhraní REST API. Azure Storage podporuje skriptování v Azure PowerShellu nebo Azure CLI. A web Azure Portal a Průzkumník služby Azure Storage nabízí snadná vizuální řešení pro práci s daty.  

## <a name="azure-storage-services"></a>Služby Azure Storage

Azure Storage zahrnuje tyto datové služby: 

- [Azure Blobs](../blobs/storage-blobs-introduction.md): Široce škálovatelné úložiště objektů pro textové a binární data.
- [Služba soubory Azure](../files/storage-files-introduction.md): Spravované sdílené složky pro cloudové nebo místní nasazení.
- [Fronty Azure](../queues/storage-queues-introduction.md): Úložiště pro spolehlivé zasílání zpráv mezi součástmi aplikace. 
- [Tabulky Azure](../tables/table-storage-overview.md): Úložiště NoSQL pro úložiště strukturovaných dat bez schématu.

Ke každé službě získáte přístup z účtu uložiště. Než začnete, přečtěte si článek [Vytvoření účtu úložiště](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Úložiště objektů blob

Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Blob Storage je optimalizované pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data. 

Blob Storage je ideální pro:

* Poskytování obrázků nebo dokumentů přímo do prohlížeče
* Ukládání souborů pro distribuovaný přístup
* Streamování videa a zvuku
* Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

Přístup k objektům ve službě Blob Storage je prostřednictvím protokolů HTTP nebo HTTPS možný odkudkoli na světě. Uživatelé nebo klientské aplikace mohou získat přístup k objektům blob prostřednictvím adres URL, [rozhraní REST API služby Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShellu](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) nebo klientské knihovny služby Azure Storage. Klientské knihovny úložiště jsou dostupné pro řadu jazyků, mezi které patří [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) a [Ruby](https://azure.github.io/azure-storage-ruby).

Další informace o službě Blob storage najdete v tématu [seznámení se službou Blob storage](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Soubory Azure
Služba [Soubory Azure](../files/storage-files-introduction.md) umožňuje nastavit vysoce dostupné sdílené složky souborů sítě, ke kterým je možný přístup pomocí standardního protokolu SMB (Server Message Block). To znamená, že několik virtuálních počítačů může sdílet stejné soubory s oprávněním ke čtení i zápisu. Soubory můžete číst také pomocí rozhraní REST nebo klientských knihoven pro úložiště.

Jednou z věcí, která odlišuje Soubory Azure od souborů v podnikové sdílené složce je, že k souborům můžete přistupovat odkudkoli na světě pomocí adresy URL, která odkazuje na soubor a zahrnuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS, které po určitou dobu umožňují specifický přístup k privátním prostředkům.

Sdílené složky můžete použít pro řadu běžných scénářů:

* Mnoho místních aplikací používá sdílené složky. Tato funkce usnadňuje migraci aplikací sdílejících data do Azure. Pokud připojíte sdílenou složku ke stejnému písmenu jednotky, které používá místní aplikace, část aplikace pro přístup ke sdílené složce by měla fungovat s minimálními, pokud vůbec nějakými, změnami.

* Ve sdílené složce je možné ukládat konfigurační soubory, kde k nim bude mít přístup více virtuálních počítačů. Ve sdílené složce je možné ukládat nástroje používané více vývojáři ve skupině zajistit tak, že je všichni budou moci najít a že budou používat stejnou verzi.

* Diagnostické protokoly, metriky a výpisy stavu systému jsou jenom tři z příkladů dat, která je možné zapisovat do sdílené složky a zpracovávat nebo analyzovat později.

V současné době se nepodporuje ověřování založené na Active Directory ani seznamy ACL, někdy v budoucnu se ale podporovat budou. K ověřování přístupu ke sdílené složce se používají přihlašovací údaje účtu úložiště. To znamená, že všichni s připojenou sdílenou složkou k ní budou mít úplná oprávnění ke čtení i zápisu.

Další informace o službě Soubory Azure najdete v tématu [Seznámení se Soubory Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Služba front Azure se využívá k ukládání a načítání zpráv. Fronty zprávy mohou mít velikost až 64 kB a jedna fronta může obsahovat miliony zpráv. Fronty se obecně používají k ukládání seznamů zpráv, které mají být zpracovány asynchronně.

Řekněme například, že chcete zákazníkům umožnit odesílání obrázků a pro každý obrázek chcete vytvořit miniatury. Můžete nechat zákazníky při odesílání obrázků čekat na to, až tyto miniatury vytvoříte. Alternativou může být použití fronty. Když zákazník dokončí jejich nahrávání, zapsat zprávu do fronty. Potom nechte funkci Azure Functions, aby načetla tuto zprávu z fronty a vytvořila miniatury. Jednotlivé části tohoto zpracování je možné škálovat samostatně. Získáte tak větší kontrolu při jeho vylaďování pro vaše využití.

Další informace o službě Fronty Azure najdete v tématu o [seznámení s Frontami](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Úložiště tabulek

Azure Table Storage je teď součástí služby Azure Cosmos DB. Dokumentaci ke službě Azure Table Storage najdete v tématu [Přehled služby Azure Table Storage](../tables/table-storage-overview.md). Kromě existující služby Azure Table Storage je k dispozici také nabídka nového rozhraní API tabulky Azure Cosmos DB, které poskytuje tabulky s optimalizovanou propustností, globální distribuci a automatické sekundární indexy. Další informace a možnost vyzkoušet si nové prostředí úrovně Premium najdete na stránce [Rozhraní API tabulky Azure Cosmos DB](https://aka.ms/premiumtables).

Další informace o službě Table Storage získáte v tématu s [přehledem o službě Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Diskové úložiště

Azure Storage také zahrnuje funkce spravovaných a nespravovaných disků využívaných virtuálními počítači. Další informace o těchto funkcích najdete v [dokumentaci ke Compute Services](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Další informace o typech účtů úložiště najdete v tématu [přehled účtu Azure storage](storage-account-overview.md). 

## <a name="securing-access-to-storage-accounts"></a>Zabezpečení přístupu k účtům úložiště

Každý požadavek do služby Azure Storage se musí autorizovat. Azure Storage podporuje následující metody ověřování:

- **Integrace Azure Active Directory (Azure AD) pro data objektů blob a fronty.** Azure Storage podporuje ověřování a autorizace pomocí přihlašovacích údajů Azure AD pro objekt Blob a fronty služby prostřednictvím řízení přístupu na základě role (RBAC). Autorizace žádosti s Azure AD se doporučuje pro nejvyšší zabezpečení a snadné použití. Další informace najdete v tématu [ověřit přístup k Azure, objekty BLOB a fronty pomocí Azure Active Directory](storage-auth-aad.md).
- **Azure AD autorizace přes protokol SMB pro soubory Azure (preview).** Služba soubory Azure podporuje ověřování na základě identity přes protokol SMB (Server Message Block) pomocí Azure Active Directory Domain Services. Připojené k doméně Windows virtuálních počítačů (VM) můžete přistupovat sdílenými složkami Azure pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [autorizace přehled o Azure Active Directory přes protokol SMB pro soubory Azure (preview)](../files/storage-files-active-directory-overview.md).
- **Ověření pomocí sdíleného klíče.** Služby Azure Storage Blob, fronty a tabulky a službou soubory Azure podporu ověřování pomocí sdíleného Key.A klienta pomocí sdíleného klíče hlavičku při každé žádosti, která je podepsaná pomocí přístupového klíče účtu úložiště předá autorizace. Další informace najdete v tématu [autorizovat pomocí sdíleného klíče](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Povolení použití sdílených přístupových podpisů (SAS).** Sdílený přístupový podpis (SAS) je řetězec obsahující token zabezpečení, který lze připojit k identifikátoru URI pro prostředek úložiště. Token zabezpečení zapouzdřuje omezení, jako jsou oprávnění a interval přístup. Další informace najdete v [použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonymní přístup ke kontejnerům a objektům BLOB.** Kontejner a jeho objekty BLOB může být veřejně dostupný. Pokud určíte, že je kontejner nebo objekt blob veřejný, kdokoli ho mohou číst anonymně; není vyžadováno žádné ověřování. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Šifrování

Pro služby Storage jsou k dispozici dva základní druhy šifrování. Další informace o zabezpečení a šifrování najdete v [průvodci zabezpečením Azure Storage](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Šifrování služby Azure Storage (SSE) v klidovém stavu pomáhá zabezpečit a chránit vaše data pro splnění závazků organizace souvisejících se zabezpečením a dodržováním předpisů. Pomocí této funkce služba Azure Storage automaticky šifruje vaše data před zachováním v úložišti a dešifruje před jejich načtením. Šifrování, dešifrování a správa klíčů je pro uživatele zcela transparentní.


SSE automaticky šifruje data na všech úrovních výkonu (Standard a Premium), ve všech modelech nasazení (Azure Resource Manager a Classic) a ve všech službách Azure Storage (Blob, Queue, Table a File). SSE nemá vliv na výkon služby Azure Storage.

Další informace o šifrování SSE v klidovém stavu najdete v tématu[Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Klientské knihovny pro úložiště nabízejí metody, jejichž voláním můžete programově zašifrovat data, než je odešlete sítí z klienta do Azure. Uloží se zašifrovaná a to znamená, že jsou zašifrovaná, i když jsou neaktivní. Při načítání dat zpět informace po přijetí dešifrujete.

Další informace o šifrování na straně klienta najdete v tématu [Šifrování na straně klienta s .NET pro úložiště Microsoft Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundance

Aby se zajistila odolnost dat, služba Azure Storage replikuje několik kopií vašich dat. Při nastavování účtu úložiště, vyberte možnost redundance. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Další informace o zotavení po havárii najdete v tématu [po havárii pro obnovení a úložiště účtu převzetí služeb při selhání (preview) ve službě Azure Storage](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Přesun dat z Azure Storage a do Azure Storage

Máte několik možností pro přesun dat do nebo z úložiště Azure. Kterou možnost zvolíte, závisí na velikosti vaší datové sadě a šířka pásma sítě. Další informace najdete v tématu [zvolit řešení Azure pro přenos dat](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Ceny

Podrobné informace o cenách pro Azure Storage najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Rozhraní API, knihovny a nástroje služby Storage
Prostředky Azure Storage jsou dostupné přes jakýkoli jazyk, který umí vytvářet požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují spoustu aspektů práce s Azure Storage, protože se starají o drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek, automatické opakování pokusů, operační chování atd. Knihovny jsou aktuálně dostupné pro následující jazyky a platformy, další se připravují:

### <a name="azure-storage-data-api-and-library-references"></a>API pro data služby Azure Storage a odkazy na knihovny
* [REST API služby Storage](https://docs.microsoft.com/rest/api/storageservices/)
* [Klientská knihovna pro úložiště pro .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Klientská knihovna pro úložiště pro Javu/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Klientská knihovna pro úložiště pro Node.js](https://docs.microsoft.com/javascript/api/azure-storage)
* [Klientská knihovna pro úložiště pro Python](https://github.com/Azure/azure-storage-python)
* [Klientská knihovna pro úložiště pro PHP](https://github.com/Azure/azure-storage-php)
* [Klientská knihovna pro úložiště pro Ruby](https://github.com/Azure/azure-storage-ruby)
* [Klientská knihovna pro úložiště pro C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API pro správu služby Azure Storage a odkazy na knihovny
* [REST API pro poskytovatele prostředků úložiště](https://docs.microsoft.com/rest/api/storagerp/)
* [Klientská knihovna pro .NET pro poskytovatele prostředků úložiště](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [REST API pro správu služeb úložiště (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API pro přenos dat služby Azure Storage a odkazy na knihovny
* [REST API pro službu Import/export úložiště](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Klientská knihovna pro .NET pro přesun dat v úložišti](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Nástroje
* [Rutiny Azure PowerShell pro Azure Storage](https://docs.microsoft.com/powershell/module/az.storage)
* [Rutiny Azure CLI pro Azure Storage](https://docs.microsoft.com/cli/azure/storage)
* [Nástroj příkazového řádku AzCopy](https://aka.ms/downloadazcopy)
* [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Klientské nástroje pro Azure Storage](../storage-explorers.md)
* [Azure Developer Tools](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Další postup

Informace o zprovoznění s využitím Azure Storage najdete v tématu [Vytvoření účtu úložiště](storage-quickstart-create-account.md).
