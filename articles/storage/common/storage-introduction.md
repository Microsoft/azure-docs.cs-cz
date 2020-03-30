---
title: Seznámení se službou Azure Storage – cloudové úložiště v Azure | Microsoft Docs
description: Azure Storage je řešení cloudového úložiště Microsoftu. Poskytuje vysoce dostupné, zabezpečené, odolné, široce škálovatelné a redundantní úložiště pro datové objekty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256065"
---
# <a name="introduction-to-azure-storage"></a>Seznámení se službou Azure Storage

Azure Storage je řešení cloudového úložiště Microsoftu pro scénáře moderního datového úložiště. Nabízí široce škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro cloud, úložiště pro přenos zpráv zajišťující spolehlivé zasílání zpráv a úložiště NoSQL. Azure Storage je:

- **Odolné a vysoce dostupné:** Redundance zajišťuje, že vaše data budou v případě krátkodobého selhání hardwaru v bezpečí. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.
- **Zabezpečené.** Všechna data a zapsaná do služby Azure Storage jsou touto službou šifrovaná. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Škálovatelné:** Azure Storage je navržené pro širokou škálovatelnost, aby splňovalo požadavky dnešních aplikací na datové úložiště a výkon.
- **Spravované:** Microsoft Azure zpracovává údržbu hardwaru, aktualizace a kritické problémy za vás.
- **Přístupné:** Data ve službě Azure Storage jsou přístupná prostřednictvím protokolu HTTP nebo HTTPS odkudkoli na světě. Microsoft poskytuje klientské knihovny pro Azure Storage v různých jazycích, včetně .NET, Java, Node.js, Python, PHP, Ruby, Go a další, stejně jako pro dospělé ROZHRANÍ REST API. Azure Storage podporuje skriptování v Azure PowerShellu nebo Azure CLI. A web Azure Portal a Průzkumník služby Azure Storage nabízí snadná vizuální řešení pro práci s daty.  

## <a name="azure-storage-services"></a>Služby Azure Storage

Azure Storage zahrnuje tyto datové služby:

- [Objekty blob Azure](../blobs/storage-blobs-introduction.md): široce škálovatelné úložiště objektů pro textová a binární data
- [Soubory Azure](../files/storage-files-introduction.md): spravované sdílené složky pro nasazení v cloudu nebo místní nasazení.
- [Fronty Azure](../queues/storage-queues-introduction.md): spolehlivé úložiště pro přenos zpráv mezi součástmi aplikace.
- [Tabulky Azure](../tables/table-storage-overview.md): úložiště NoSQL pro úložiště strukturovaných dat bez schématu.

Ke každé službě získáte přístup z účtu uložiště. Než začnete, přečtěte si článek [Vytvoření účtu úložiště](storage-account-create.md).

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Blob Storage je optimalizované pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data.

Blob Storage je ideální pro:

- Poskytování obrázků nebo dokumentů přímo do prohlížeče
- Ukládání souborů pro distribuovaný přístup
- Streamování videa a zvuku
- Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
- Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

Přístup k objektům ve službě Blob Storage je prostřednictvím protokolů HTTP nebo HTTPS možný odkudkoli na světě. Uživatelé nebo klientské aplikace mohou získat přístup k objektům blob prostřednictvím adres URL, [rozhraní REST API služby Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShellu](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) nebo klientské knihovny služby Azure Storage. Klientské knihovny úložiště jsou dostupné pro řadu jazyků, mezi které patří [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) a [Ruby](https://azure.github.io/azure-storage-ruby).

Další informace o úložišti objektů Blob najdete [v tématu Úvod do úložiště objektů Blob](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Soubory Azure

Služba [Soubory Azure](../files/storage-files-introduction.md) umožňuje nastavit vysoce dostupné sdílené složky souborů sítě, ke kterým je možný přístup pomocí standardního protokolu SMB (Server Message Block). To znamená, že několik virtuálních počítačů může sdílet stejné soubory s oprávněním ke čtení i zápisu. Soubory můžete číst také pomocí rozhraní REST nebo klientských knihoven pro úložiště.

Jednou z věcí, která odlišuje Soubory Azure od souborů v podnikové sdílené složce je, že k souborům můžete přistupovat odkudkoli na světě pomocí adresy URL, která odkazuje na soubor a zahrnuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS, které po určitou dobu umožňují specifický přístup k privátním prostředkům.

Sdílené složky můžete použít pro řadu běžných scénářů:

- Mnoho místních aplikací používá sdílené složky. Tato funkce usnadňuje migraci aplikací sdílejících data do Azure. Pokud připojíte sdílenou složku ke stejnému písmenu jednotky, které používá místní aplikace, část aplikace pro přístup ke sdílené složce by měla fungovat s minimálními, pokud vůbec nějakými, změnami.

- Ve sdílené složce je možné ukládat konfigurační soubory, kde k nim bude mít přístup více virtuálních počítačů. Ve sdílené složce je možné ukládat nástroje používané více vývojáři ve skupině zajistit tak, že je všichni budou moci najít a že budou používat stejnou verzi.

- Diagnostické protokoly, metriky a výpisy stavu systému jsou jenom tři z příkladů dat, která je možné zapisovat do sdílené složky a zpracovávat nebo analyzovat později.

V současné době se nepodporuje ověřování založené na Active Directory ani seznamy ACL, někdy v budoucnu se ale podporovat budou. K ověřování přístupu ke sdílené složce se používají přihlašovací údaje účtu úložiště. To znamená, že všichni s připojenou sdílenou složkou k ní budou mít úplná oprávnění ke čtení i zápisu.

Další informace o službě Soubory Azure najdete v tématu [Seznámení se Soubory Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Služba front Azure se využívá k ukládání a načítání zpráv. Fronty zprávy mohou mít velikost až 64 kB a jedna fronta může obsahovat miliony zpráv. Fronty se obecně používají k ukládání seznamů zpráv, které mají být zpracovány asynchronně.

Řekněme například, že chcete zákazníkům umožnit odesílání obrázků a pro každý obrázek chcete vytvořit miniatury. Můžete nechat zákazníky při odesílání obrázků čekat na to, až tyto miniatury vytvoříte. Alternativou může být použití fronty. Když zákazník dokončí nahrávání, napište zprávu do fronty. Potom nechte funkci Azure Functions, aby načetla tuto zprávu z fronty a vytvořila miniatury. Jednotlivé části tohoto zpracování je možné škálovat samostatně. Získáte tak větší kontrolu při jeho vylaďování pro vaše využití.

Další informace o službě Fronty Azure najdete v tématu o [seznámení s Frontami](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Úložiště Table

Azure Table Storage je teď součástí služby Azure Cosmos DB. Dokumentaci ke službě Azure Table Storage najdete v tématu [Přehled služby Azure Table Storage](../tables/table-storage-overview.md). Kromě existující služby Azure Table Storage je k dispozici také nabídka nového rozhraní API tabulky Azure Cosmos DB, které poskytuje tabulky s optimalizovanou propustností, globální distribuci a automatické sekundární indexy. Další informace a možnost vyzkoušet si nové prostředí úrovně Premium najdete na stránce [Rozhraní API tabulky Azure Cosmos DB](https://aka.ms/premiumtables).

Další informace o službě Table Storage získáte v tématu s [přehledem o službě Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Diskové úložiště

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to myslet jako fyzický disk v místním serveru, ale virtualizované. Spravované disky Azure se ukládají jako objekty BLOB stránky, což je náhodný objekt úložiště Vo v Azure. Nazýváme spravovaný disk "spravovaný", protože se jedná o abstrakci přes objekty BLOB stránky, kontejnery objektů blob a účty úložiště Azure. Se spravovanými disky stačí zřídit disk a Azure se postará o zbytek.

Další informace o spravovaných discích najdete [v tématu Úvod do spravovaných disků Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Další informace o typech účtů úložiště najdete v tématu [Přehled účtu úložiště Azure](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Zabezpečení přístupu k účtům úložiště

Každý požadavek na Azure Storage musí být autorizovaný. Azure Storage podporuje následující metody autorizace:

- **Integrace Azure Active Directory (Azure AD) pro data objektů blob a fronty.** Azure Storage podporuje ověřování a autorizaci pomocí Azure AD pro služby objektů blob a fronty prostřednictvím řízení přístupu na základě rolí (RBAC). Autorizace požadavků pomocí Azure AD se doporučuje pro lepší zabezpečení a snadné použití. Další informace najdete [v tématu Autorizace přístupu k objektům BLOB azure a frontám pomocí služby Azure Active Directory](storage-auth-aad.md).
- **Autorizace Azure AD přes SMB pro soubory Azure (náhled).** Soubory Azure podporují autorizaci založenou na identitách přes Protokol SMB (Server Message Block) prostřednictvím služby Azure Active Directory Domain Services. Virtuální počítače windows (VM) spojené s doménou mají přístup ke sdíleným spodám Azure pomocí přihlašovacích údajů Azure AD. Další informace najdete v [tématu Přehled autorizace služby Azure Active Directory přes SMB pro soubory Azure (preview).](../files/storage-files-active-directory-overview.md)
- **Autorizace se sdíleným klíčem.** Služby blob, fronty a table služby úložiště Azure a soubory Azure podporují autorizaci pomocí sdíleného key.a klienta pomocí autorizace sdíleného klíče předá záhlaví s každým požadavkem, který je podepsaný pomocí přístupového klíče účtu úložiště. Další informace naleznete v [tématu Authorize with Shared Key](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorizace pomocí sdílených přístupových podpisů (SAS).** Sdílený přístupový podpis (SAS) je řetězec obsahující token zabezpečení, který lze připojit k identifikátoru URI pro prostředek úložiště. Token zabezpečení zapouzdřuje omezení, jako jsou oprávnění a interval přístupu. Další informace naleznete v části [Použití sdílených přístupových podpisů (SAS).](storage-sas-overview.md)
- **Anonymní přístup k kontejnery a objekty BLOB.** Kontejner a jeho objekty BLOB mohou být veřejně dostupné. Když zadáte, že kontejner nebo objekt blob je veřejný, kdokoli může číst anonymně; není vyžadováno žádné ověřování. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Šifrování

Pro služby Storage jsou k dispozici dva základní druhy šifrování. Další informace o zabezpečení a šifrování najdete v [průvodci zabezpečením Azure Storage](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Šifrování Azure Storage chrání a chrání vaše data, aby splnila vaše závazky v oblasti zabezpečení a dodržování předpisů vaší organizace. Azure Storage automaticky šifruje všechna data před uchování matem na účet úložiště a dešifruje je před načtením. Procesy šifrování, dešifrování a správy klíčů jsou pro uživatele zcela transparentní. Zákazníci se také mohou rozhodnout spravovat své vlastní klíče pomocí služby Azure Key Vault. Další informace najdete v tématu [šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Klientské knihovny Azure Storage poskytují metody pro šifrování dat z klientské knihovny před odesláním přes drát a dešifrování odpovědi. Data šifrovaná pomocí šifrování na straně klienta je také šifrována v klidovém stavu službou Azure Storage. Další informace o šifrování na straně klienta naleznete v [tématu šifrování na straně klienta pomocí rozhraní .NET for Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundance

Aby bylo zajištěno, že vaše data jsou trvalá, Azure Storage ukládá více kopií vašich dat. Při nastavujete účet úložiště, vyberete možnost redundance.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Přesun dat z Azure Storage a do Azure Storage

Máte několik možností pro přesun dat do nebo z Azure Storage. Kterou možnost zvolíte, závisí na velikosti datové sady a šířce pásma sítě. Další informace najdete [v tématu Výběr řešení Azure pro přenos dat](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Ceny

Podrobné informace o cenách pro Azure Storage najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Rozhraní API, knihovny a nástroje služby Storage

Prostředky Azure Storage jsou dostupné přes jakýkoli jazyk, který umí vytvářet požadavky HTTP/HTTPS. Azure Storage dále nabízí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují spoustu aspektů práce s Azure Storage, protože se starají o drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek, automatické opakování pokusů, operační chování atd. Knihovny jsou aktuálně dostupné pro následující jazyky a platformy, další se připravují:

### <a name="azure-storage-data-api-and-library-references"></a>API pro data služby Azure Storage a odkazy na knihovny

- [Rozhraní REST API pro službu Azure Storage](https://docs.microsoft.com/rest/api/storageservices/)
- [Klientská knihovna Azure Storage pro rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Klientská knihovna Azure Storage pro Javu/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Klientská knihovna Azure Storage pro soubor Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Klientská knihovna Azure Storage pro Python](https://github.com/Azure/azure-storage-python)
- [Klientská knihovna Azure Storage pro PHP](https://github.com/Azure/azure-storage-php)
- [Klientská knihovna Azure Storage pro Ruby](https://github.com/Azure/azure-storage-ruby)
- [Klientská knihovna Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API pro správu služby Azure Storage a odkazy na knihovny

- [REST API pro poskytovatele prostředků úložiště](https://docs.microsoft.com/rest/api/storagerp/)
- [Klientská knihovna pro .NET pro poskytovatele prostředků úložiště](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [REST API pro správu služeb úložiště (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API pro přenos dat služby Azure Storage a odkazy na knihovny

- [REST API pro službu Import/export úložiště](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Klientská knihovna pro .NET pro přesun dat v úložišti](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Nástroje

- [Rutiny Azure PowerShell pro Azure Storage](https://docs.microsoft.com/powershell/module/az.storage)
- [Rutiny Azure CLI pro Azure Storage](https://docs.microsoft.com/cli/azure/storage)
- [Nástroj příkazového řádku AzCopy](https://aka.ms/downloadazcopy)
- [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
- [Klientské nástroje pro Azure Storage](../storage-explorers.md)
- [Azure Developer Tools](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Další kroky

Informace o zprovoznění s využitím Azure Storage najdete v tématu [Vytvoření účtu úložiště](storage-account-create.md).
