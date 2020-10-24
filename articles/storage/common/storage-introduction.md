---
title: Seznámení se službou Azure Storage – cloudové úložiště v Azure | Microsoft Docs
description: Základní Azure Storage platformou je řešení cloudového úložiště od Microsoftu. Poskytuje vysoce dostupné, zabezpečené, odolné, široce škálovatelné a redundantní úložiště pro datové objekty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 73b48f8bcb4ec6facfebfc62d03ee5cd8237f504
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490794"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Seznámení se základními Azure Storage službami

Azure Storage platformou je řešení cloudového úložiště Microsoftu pro scénáře moderních úložišť dat. Služba Core Storage nabízí rozsáhle škálovatelné úložiště objektů pro datové objekty, diskové úložiště pro virtuální počítače Azure, službu systému souborů pro Cloud, úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a NoSQL úložiště. Služby jsou:

- **Odolné a vysoce dostupné:** Redundance zajišťuje, že vaše data budou v případě krátkodobého selhání hardwaru v bezpečí. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.
- **Požadavk.** Všechna data zapsaná do účtu služby Azure Storage jsou službou šifrována. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Měřítk.** Služba Azure Storage je navržená pro širokou škálovatelnost, aby splňovala požadavky dnešních aplikací na datové úložiště a výkon.
- **Starosti.** Azure zpracovává údržbu, aktualizace a kritické problémy s hardwarem.
- **Přístupné:** Data ve službě Azure Storage jsou přístupná prostřednictvím protokolu HTTP nebo HTTPS odkudkoli na světě. Microsoft poskytuje klientské knihovny pro Azure Storage v různých jazycích, včetně .NET, Java, Node.js, Pythonu, PHP, Ruby, přechodu a dalších, a také na vyspělé REST API. Azure Storage podporuje skriptování v Azure PowerShellu nebo Azure CLI. A web Azure Portal a Průzkumník služby Azure Storage nabízí snadná vizuální řešení pro práci s daty.  

## <a name="core-storage-services"></a>Základní služby úložiště

Platforma Azure Storage zahrnuje následující datové služby:

- [Objekty blob Azure](../blobs/storage-blobs-introduction.md): široce škálovatelné úložiště objektů pro textová a binární data Zahrnuje také podporu pro analýzy velkých objemů dat prostřednictvím Data Lake Storage Gen2.
- [Soubory Azure](../files/storage-files-introduction.md): spravované sdílené složky pro cloudová nebo místní nasazení.
- [Fronty Azure](../queues/storage-queues-introduction.md): spolehlivé úložiště pro přenos zpráv mezi součástmi aplikace.
- [Tabulky Azure](../tables/table-storage-overview.md): úložiště NoSQL pro úložiště strukturovaných dat bez schématu.
- [Disky Azure](../../virtual-machines/managed-disks-overview.md): svazky úložiště na úrovni bloku pro virtuální počítače Azure.

Ke každé službě získáte přístup z účtu uložiště. Než začnete, přečtěte si článek [Vytvoření účtu úložiště](storage-account-create.md).

## <a name="example-scenarios"></a>Příklady scénářů

Následující tabulka porovnává soubory, objekty blob, disky, fronty a tabulky a obsahuje příklady scénářů pro každý z nich.

| Funkce | Popis | Kdy je použít |
|--------------|-------------|-------------|
| **Soubory Azure** |Nabízí plně spravované cloudové sdílené složky, ke kterým můžete přistupovat odkudkoli prostřednictvím standardního standardního protokolu SMB (Server Message Block).<br><br>Sdílené složky Azure můžete připojit z cloudových nebo místních nasazení systémů Windows, Linux a macOS. | Chcete "nazvednutí a posunutí" aplikace do cloudu, který už používá nativní rozhraní API systému souborů ke sdílení dat mezi IT a dalšími aplikacemi běžícími v Azure.<br/><br/>Chcete nahradit nebo doplnit místní souborové servery nebo zařízení NAS.<br><br> Chcete uložit nástroje pro vývoj a ladění, které potřebují být dostupné z mnoha virtuálních počítačů. |
| **Objekty blob Azure** | Umožňuje ukládání nestrukturovaných dat a jejich použití v obrovských škálováních objektů blob bloku.<br/><br/>Také podporuje [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pro řešení pro analýzy velkých objemů dat v podniku. | Chcete, aby vaše aplikace podporovala scénáře streamování a náhodného přístupu.<br/><br/>Chcete mít přístup k datům aplikací odkudkoli.<br/><br/>Chcete vytvořit službu data Lake pro podniky v Azure a provádět analýzy velkých objemů dat. |
| **Disky Azure** | Umožňuje, aby se data trvale ukládala a získala z připojeného virtuálního pevného disku. | Chcete použít aplikace "zvedá a Shift", které používají rozhraní API nativního systému souborů ke čtení a zápisu dat na trvalé disky.<br/><br/>Chcete ukládat data, která se nevyžadují pro použití mimo virtuální počítač, ke kterému je disk připojený. |
| **Fronty Azure** | Umožňuje asynchronní řízení front zpráv mezi součástmi aplikace. | Chcete oddělit součásti aplikace a použít asynchronní zasílání zpráv ke komunikaci mezi nimi.<br><br>Pokyny týkající se použití úložiště front a Service Bus front najdete v tématu [fronty úložiště a Service Bus fronty – porovnání a kontrast](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Tabulky Azure** | Umožňuje ukládat strukturovaná data NoSQL do cloudu a poskytovat tak úložiště klíčů a atributů s návrhem bez schématu. | Chcete ukládat flexibilní datové sady, jako jsou uživatelská data pro webové aplikace, adresáře, informace o zařízení nebo jiné typy metadat, které vaše služba vyžaduje. <br/><br/>Pokyny týkající se použití služby Table Storage a Azure Cosmos DB rozhraní API pro tabulky najdete v tématu [vývoj v Azure Cosmos DB rozhraní API pro tabulky a v úložišti tabulek Azure](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage je řešení úložiště objektů pro cloud od Microsoftu. Blob Storage je optimalizované pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data.

Blob Storage je ideální pro:

- Poskytování obrázků nebo dokumentů přímo do prohlížeče
- Ukládání souborů pro distribuovaný přístup
- Streamování videa a zvuku
- Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
- Ukládání dat, která bude analyzovat místní nebo v Azure hostovaná služba

Přístup k objektům ve službě Blob Storage je prostřednictvím protokolů HTTP nebo HTTPS možný odkudkoli na světě. Uživatelé nebo klientské aplikace mohou získat přístup k objektům blob prostřednictvím adres URL, [rozhraní REST API služby Azure Storage](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShellu](/powershell/module/azure.storage), [Azure CLI](/cli/azure/storage) nebo klientské knihovny služby Azure Storage. Klientské knihovny úložiště jsou dostupné pro řadu jazyků, mezi které patří [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) a [Ruby](https://azure.github.io/azure-storage-ruby).

Další informace o službě BLOB Storage najdete v tématu [Úvod do úložiště objektů BLOB](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Soubory Azure

Služba [soubory Azure](../files/storage-files-introduction.md) umožňuje nastavit vysoce dostupné sdílené síťové složky, ke kterým lze získat přístup pomocí standardního protokolu SMB (Server Message Block). To znamená, že několik virtuálních počítačů může sdílet stejné soubory s oprávněním ke čtení i zápisu. Soubory můžete číst také pomocí rozhraní REST nebo klientských knihoven pro úložiště.

Jednou z věcí, která odlišuje Soubory Azure od souborů v podnikové sdílené složce je, že k souborům můžete přistupovat odkudkoli na světě pomocí adresy URL, která odkazuje na soubor a zahrnuje token sdíleného přístupového podpisu (SAS). Můžete generovat tokeny SAS, které po určitou dobu umožňují specifický přístup k privátním prostředkům.

Sdílené složky můžete použít pro řadu běžných scénářů:

- Mnoho místních aplikací používá sdílené složky. Tato funkce usnadňuje migraci aplikací sdílejících data do Azure. Pokud připojíte sdílenou složku ke stejnému písmenu jednotky, které používá místní aplikace, část aplikace pro přístup ke sdílené složce by měla fungovat s minimálními, pokud vůbec nějakými, změnami.

- Ve sdílené složce je možné ukládat konfigurační soubory, kde k nim bude mít přístup více virtuálních počítačů. Ve sdílené složce je možné ukládat nástroje používané více vývojáři ve skupině zajistit tak, že je všichni budou moci najít a že budou používat stejnou verzi.

- Protokoly prostředků, metriky a výpisy stavu systému jsou jenom tři příklady dat, která se dají zapisovat do sdílené složky a zpracovávat nebo analyzovat později.

Další informace o službě Soubory Azure najdete v tématu [Seznámení se Soubory Azure](../files/storage-files-introduction.md).

Některé funkce protokolu SMB se nevztahují na Cloud. Další informace najdete v tématu [funkce nepodporované službou Azure File Service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Služba front Azure se využívá k ukládání a načítání zpráv. Fronty zprávy mohou mít velikost až 64 kB a jedna fronta může obsahovat miliony zpráv. Fronty se obecně používají k ukládání seznamů zpráv, které mají být zpracovány asynchronně.

Řekněme například, že chcete zákazníkům umožnit odesílání obrázků a pro každý obrázek chcete vytvořit miniatury. Můžete nechat zákazníky při odesílání obrázků čekat na to, až tyto miniatury vytvoříte. Alternativou může být použití fronty. Když zákazník dokončí nahrávání, zapište do fronty zprávu. Potom nechte funkci Azure Functions, aby načetla tuto zprávu z fronty a vytvořila miniatury. Jednotlivé části tohoto zpracování je možné škálovat samostatně. Získáte tak větší kontrolu při jeho vylaďování pro vaše využití.

Další informace o službě Fronty Azure najdete v tématu o [seznámení s Frontami](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Úložiště Table

Azure Table Storage je teď součástí služby Azure Cosmos DB. Dokumentaci ke službě Azure Table Storage najdete v tématu [Přehled služby Azure Table Storage](../tables/table-storage-overview.md). Kromě existující služby Azure Table Storage je k dispozici také nabídka nového rozhraní API tabulky Azure Cosmos DB, které poskytuje tabulky s optimalizovanou propustností, globální distribuci a automatické sekundární indexy. Pokud se chcete dozvědět víc a vyzkoušet nové prostředí Premium, přečtěte si téma [Azure Cosmos DB rozhraní API pro tabulky](https://aka.ms/premiumtables).

Další informace o službě Table Storage získáte v tématu s [přehledem o službě Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Diskové úložiště

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to představit jako fyzický disk na místním serveru, ale virtualizované. Disky spravované Azure se ukládají jako objekty blob stránky, což jsou v Azure náhodný objekt úložiště v/v. Zavoláme spravovaný spravovaný disk, protože se jedná o abstrakci přes objekty blob stránky, kontejnery objektů BLOB a účty Azure Storage. U spravovaných disků stačí zřídit disk a Azure se postará o zbytek.

Další informace o spravovaných discích najdete v tématu [Úvod do služby Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Zabezpečený přístup k účtům úložiště

Každý požadavek na Azure Storage musí být autorizován. Azure Storage podporuje následující autorizační metody:

- **Integrace služby Azure Active Directory (Azure AD) pro data objektů BLOB a front** Azure Storage podporuje ověřování a autorizaci ve službě Azure AD pro objekty BLOB a Queue prostřednictvím řízení přístupu na základě role Azure (Azure RBAC). Pro zajištění vysokého zabezpečení a snadného použití se doporučuje autorizovat žádosti pomocí Azure AD. Další informace najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md).
- **Ověřování Azure AD prostřednictvím protokolu SMB pro soubory Azure.** Soubory Azure podporují autorizaci založenou na identitě přes protokol SMB (Server Message Block) prostřednictvím Azure Active Directory Domain Services (Azure služba AD DS) nebo místní Active Directory Domain Services (Preview). Vaše virtuální počítače s Windows připojené k doméně mají přístup ke sdíleným složkám Azure pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](../files/storage-files-active-directory-overview.md) a [plánování pro nasazení souborů Azure](../files/storage-files-planning.md#identity).
- **Autorizace se sdíleným klíčem.** Azure Storage Blob, soubory, fronty a služby Table Services podporují autorizaci pomocí sdíleného klíče. Klient používající autorizaci pomocí sdíleného klíče předává hlavičku s každým požadavkem, který je podepsaný pomocí přístupového klíče účtu úložiště. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key).
- **Autorizaci pomocí sdílených přístupových podpisů (SAS).** Sdílený přístupový podpis (SAS) je řetězec, který obsahuje token zabezpečení, který se dá připojit k identifikátoru URI pro prostředek úložiště. Token zabezpečení zapouzdřuje omezení jako oprávnění a interval přístupu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).
- **Anonymní přístup k kontejnerům a objektům blob.** Kontejner a jeho objekty blob můžou být veřejně dostupné. Pokud určíte, že je kontejner nebo objekt BLOB veřejný, kdokoli ho může anonymně číst; není vyžadováno žádné ověření. Další informace najdete v tématu [Správa anonymního přístupu pro čtení do kontejnerů a objektů BLOB](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Šifrování

Pro základní služby úložiště jsou k dispozici dva základní druhy šifrování. Další informace o zabezpečení a šifrování najdete v [průvodci zabezpečením Azure Storage](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat

Azure Storage šifrování chrání a chrání vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Azure Storage automaticky šifruje všechna data před uložením do účtu úložiště a před načtením ji dešifruje. Procesy šifrování, dešifrování a správy klíčů jsou pro uživatele transparentní. Zákazníci si také můžou pomocí Azure Key Vault spravovat vlastní klíče. Další informace najdete v tématu [Azure Storage šifrování pro](storage-service-encryption.md)neaktivní neaktivní data.

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Klientské knihovny Azure Storage poskytují metody pro šifrování dat z klientské knihovny před jejich odesláním napříč kabelem a dešifrováním odpovědi. Data zašifrovaná pomocí šifrování na straně klienta jsou také šifrována v klidovém stavu Azure Storage. Další informace o šifrování na straně klienta najdete v tématu [šifrování na straně klienta s rozhraním .NET pro Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundance

Aby bylo zajištěno, že vaše data jsou trvalá, Azure Storage ukládá více kopií vašich dat. Při nastavování účtu úložiště vyberte možnost redundance. Další informace najdete v článku [Možnosti redundance Azure Storage](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Přenos dat do a z Azure Storage

K přesunu dat do nebo z Azure Storage máte několik možností. Vámi zvolená možnost závisí na velikosti datové sady a šířce pásma vaší sítě. Další informace najdete v tématu [Volba řešení Azure pro přenos dat](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Ceny

Při rozhodování o způsobu, jakým se data ukládají a jsou k nim přistupované, byste měli vzít v úvahu také příslušné náklady. Další informace najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>Rozhraní API, knihovny a nástroje služby Storage

K prostředkům v účtu úložiště můžete přistupovat pomocí libovolného jazyka, který může provádět požadavky HTTP/HTTPS. Základní Azure Storage služby navíc nabízejí programové knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují spoustu aspektů práce s Azure Storage, protože se starají o drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek, automatické opakování pokusů, operační chování atd. Knihovny jsou aktuálně dostupné pro následující jazyky a platformy, další se připravují:

### <a name="azure-storage-data-api-and-library-references"></a>API pro data služby Azure Storage a odkazy na knihovny

- [Rozhraní REST API pro službu Azure Storage](/rest/api/storageservices/)
- [Klientská knihovna Azure Storage pro .NET](/dotnet/api/overview/azure/storage)
- [Klientská knihovna Azure Storage pro Java/Android](/java/api/overview/azure/storage)
- [Klientská knihovna Azure Storage pro Node.js](/javascript/api/overview/azure/storage-overview)
- [Klientská knihovna Azure Storage pro Python](https://github.com/Azure/azure-storage-python)
- [Klientská knihovna Azure Storage pro PHP](https://github.com/Azure/azure-storage-php)
- [Klientská knihovna Azure Storage pro Ruby](https://github.com/Azure/azure-storage-ruby)
- [Klientská knihovna Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API pro správu služby Azure Storage a odkazy na knihovny

- [REST API pro poskytovatele prostředků úložiště](/rest/api/storagerp/)
- [Klientská knihovna pro .NET pro poskytovatele prostředků úložiště](/dotnet/api/overview/azure/storage/management)
- [REST API pro správu služeb úložiště (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API pro přenos dat služby Azure Storage a odkazy na knihovny

- [REST API pro službu Import/export úložiště](/rest/api/storageimportexport/)
- [Klientská knihovna pro .NET pro přesun dat v úložišti](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Nástroje

- [Rutiny Azure PowerShell pro Azure Storage](/powershell/module/az.storage)
- [Rutiny Azure CLI pro Azure Storage](/cli/azure/storage)
- [Nástroj příkazového řádku AzCopy](https://aka.ms/downloadazcopy)
- [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
- [Šablony Azure Resource Manager pro Azure Storage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít pracovat se službou Core Azure Storage Services, přečtěte si téma [Vytvoření účtu úložiště](storage-account-create.md).
