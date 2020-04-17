---
title: Seznámení se službou Azure Storage – cloudové úložiště v Azure | Microsoft Docs
description: Základní platforma Azure Storage je řešení cloudového úložiště microsoftu. Poskytuje vysoce dostupné, zabezpečené, odolné, široce škálovatelné a redundantní úložiště pro datové objekty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 6c75b9c2feeab32c139998f617cacf540733e3bd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456341"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Úvod do základních služeb Azure Storage

Platforma Azure Storage je cloudové úložiště společnosti Microsoft pro moderní scénáře ukládání dat. Služby základního úložiště nabízejí masivně škálovatelné úložiště objektů pro datové objekty, diskové úložiště pro virtuální počítače Azure, službu systému souborů pro cloud, úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a úložiště NoSQL. Služby jsou:

- **Odolné a vysoce dostupné:** Redundance zajišťuje, že vaše data budou v případě krátkodobého selhání hardwaru v bezpečí. Můžete se také rozhodnout data replikovat napříč několika datacentry nebo geografickými oblastmi, abyste se ochránili i proti místní pohromě nebo přírodní katastrofě. Data replikovaná tímto způsobem zůstávají v případě nečekaného výpadku vysoce dostupná.
- **Zabezpečené.** Všechna data zapsaná do účtu úložiště Azure je šifrovaná službou. Azure Storage poskytuje jemně odstupňované řízení přístupu k datům.
- **Škálovatelné:** Azure Storage je navržené pro širokou škálovatelnost, aby splňovalo požadavky dnešních aplikací na datové úložiště a výkon.
- **Spravované:** Azure zpracovává údržbu hardwaru, aktualizace a kritické problémy za vás.
- **Přístupné:** Data ve službě Azure Storage jsou přístupná prostřednictvím protokolu HTTP nebo HTTPS odkudkoli na světě. Microsoft poskytuje klientské knihovny pro Azure Storage v různých jazycích, včetně .NET, Java, Node.js, Python, PHP, Ruby, Go a další, stejně jako pro dospělé ROZHRANÍ REST API. Azure Storage podporuje skriptování v Azure PowerShellu nebo Azure CLI. A web Azure Portal a Průzkumník služby Azure Storage nabízí snadná vizuální řešení pro práci s daty.  

## <a name="core-storage-services"></a>Základní služby úložiště

Platforma Azure Storage zahrnuje následující datové služby:

- [Objekty blob Azure](../blobs/storage-blobs-introduction.md): široce škálovatelné úložiště objektů pro textová a binární data Zahrnuje také podporu pro analýzu velkých objemů dat prostřednictvím data lake storage gen2.
- [Soubory Azure](../files/storage-files-introduction.md): spravované sdílené složky pro nasazení v cloudu nebo místní nasazení.
- [Fronty Azure](../queues/storage-queues-introduction.md): spolehlivé úložiště pro přenos zpráv mezi součástmi aplikace.
- [Tabulky Azure](../tables/table-storage-overview.md): úložiště NoSQL pro úložiště strukturovaných dat bez schématu.
- [Disky Azure:](../../virtual-machines/windows/managed-disks-overview.md)Svazky úložiště na úrovni bloku pro virtuální počítače Azure.

Ke každé službě získáte přístup z účtu uložiště. Než začnete, přečtěte si článek [Vytvoření účtu úložiště](storage-account-create.md).

## <a name="example-scenarios"></a>Ukázkové scénáře

Následující tabulka porovnává soubory, objekty BLOB, disky, fronty a tabulky a zobrazuje ukázkové scénáře pro každou z nich.

| Funkce | Popis | Kdy je použít |
|--------------|-------------|-------------|
| **Soubory Azure** |Nabízí plně spravované sdílené složky cloudu, ke kterým máte přístup odkudkoli prostřednictvím standardního protokolu SMB (Standard Server Message Block).<br><br>Sdílené složky Azure můžete připojit z cloudových nebo místních nasazení Windows, Linuxu a macOS. | Chcete "zvednout a přesunout" aplikace do cloudu, který už používá nativní souborového systému API pro sdílení dat mezi ním a dalšími aplikacemi spuštěnými v Azure.<br/><br/>Chcete nahradit nebo doplnit místní souborové servery nebo zařízení NAS.<br><br> Chcete uložit nástroje pro vývoj a ladění, které je potřeba přistupovat z mnoha virtuálních počítačů. |
| **Objekty blob Azure** | Umožňuje nestrukturovaná data, která mají být uloženy a přístupné v masivním měřítku v blobů bloku.<br/><br/>Podporuje také [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pro podniková řešení pro analýzu velkých objemů dat. | Chcete, aby vaše aplikace podporovala streamování a scénáře náhodného přístupu.<br/><br/>Chcete mít přístup k datům aplikace odkudkoli.<br/><br/>Chcete v Azure vytvořit jezero podnikových dat a provádět analýzy velkých objemů dat. |
| **Disky Azure** | Umožňuje trvale ukládat data a přistupovat k nim z připojeného virtuálního pevného disku. | Chcete "výtah a posun" aplikace, které používají nativní souborového systému API pro čtení a zápis dat na trvalé disky.<br/><br/>Chcete ukládat data, která není nutné přistupovat z mimo virtuální počítač, ke kterému je připojen disk. |
| **Fronty Azure** | Umožňuje asynchronní fronty zpráv mezi součástmi aplikace. | Chcete oddělit součásti aplikace a používat asynchronní zasílání zpráv ke komunikaci mezi nimi.<br><br>Pokyny k použití fronty úložiště versus fronty Service Bus, najdete v [tématu fronty úložiště a fronty service bus – porovnání a kontrastní](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Tabulky Azure** | Umožňují ukládat strukturovaná data NoSQL v cloudu a poskytují úložiště klíčů a atributů s návrhem bez schématu. | Chcete ukládat flexibilní datové sady, jako jsou uživatelská data pro webové aplikace, adresáře, informace o zařízení nebo jiné typy metadat, které vaše služba vyžaduje. <br/><br/>Pokyny k použití table storage versus rozhraní API tabulky Azure Cosmos DB najdete v [tématu Vývoj pomocí rozhraní Azure Cosmos DB Table API a úložiště azure table.](../../cosmos-db/table-support.md) |

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

Další informace o službě Soubory Azure najdete v tématu [Seznámení se Soubory Azure](../files/storage-files-introduction.md).

Některé funkce SMB se nevztahují na cloud. Další informace najdete [v tématu Funkce, které nejsou podporovány službou Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Služba front Azure se využívá k ukládání a načítání zpráv. Fronty zprávy mohou mít velikost až 64 kB a jedna fronta může obsahovat miliony zpráv. Fronty se obecně používají k ukládání seznamů zpráv, které mají být zpracovány asynchronně.

Řekněme například, že chcete zákazníkům umožnit odesílání obrázků a pro každý obrázek chcete vytvořit miniatury. Můžete nechat zákazníky při odesílání obrázků čekat na to, až tyto miniatury vytvoříte. Alternativou může být použití fronty. Když zákazník dokončí nahrávání, napište zprávu do fronty. Potom nechte funkci Azure Functions, aby načetla tuto zprávu z fronty a vytvořila miniatury. Jednotlivé části tohoto zpracování je možné škálovat samostatně. Získáte tak větší kontrolu při jeho vylaďování pro vaše využití.

Další informace o službě Fronty Azure najdete v tématu o [seznámení s Frontami](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Úložiště Table

Azure Table Storage je teď součástí služby Azure Cosmos DB. Dokumentaci ke službě Azure Table Storage najdete v tématu [Přehled služby Azure Table Storage](../tables/table-storage-overview.md). Kromě existující služby Azure Table Storage je k dispozici také nabídka nového rozhraní API tabulky Azure Cosmos DB, které poskytuje tabulky s optimalizovanou propustností, globální distribuci a automatické sekundární indexy. Další informace a vyzkoušet nové prostředí premium najdete v [tématu Azure Cosmos DB Table API](https://aka.ms/premiumtables).

Další informace o službě Table Storage získáte v tématu s [přehledem o službě Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Diskové úložiště

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to myslet jako fyzický disk v místním serveru, ale virtualizované. Disky spravované Azure se ukládají jako objekty blob stránky, které jsou náhodným objektem úložiště IO v Azure. Nazýváme spravovaný disk "spravovaný", protože se jedná o abstrakci přes objekty BLOB stránky, kontejnery objektů blob a účty úložiště Azure. Se spravovanými disky stačí zřídit disk a Azure se postará o zbytek.

Další informace o spravovaných discích najdete [v tématu Úvod do spravovaných disků Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu úložiště Azure](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Zabezpečený přístup k účtům úložiště

Každý požadavek na Azure Storage musí být autorizovaný. Azure Storage podporuje následující metody autorizace:

- **Integrace Azure Active Directory (Azure AD) pro data objektů blob a fronty.** Azure Storage podporuje ověřování a autorizaci pomocí Azure AD pro služby objektů blob a fronty prostřednictvím řízení přístupu na základě rolí (RBAC). Autorizace požadavků pomocí Azure AD se doporučuje pro lepší zabezpečení a snadné použití. Další informace najdete [v tématu Autorizace přístupu k objektům BLOB azure a frontám pomocí služby Azure Active Directory](storage-auth-aad.md).
- **Autorizace Azure AD přes SMB pro soubory Azure.** Soubory Azure podporují autorizaci založenou na identitách přes Protokol SMB (Server Message Block) prostřednictvím služby Azure Active Directory Domain Services (Azure AD DS) nebo místní služby Active Directory Domain Services (preview). Virtuální počítače Windows pro připojení k doméně můžou přistupovat ke sdíleným souborům Azure pomocí přihlašovacích údajů Azure AD. Další informace najdete [v tématu Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](../files/storage-files-active-directory-overview.md) a [plánování pro nasazení souborů Azure](../files/storage-files-planning.md#identity).
- **Autorizace se sdíleným klíčem.** Služby Azure Storage Blob, Files, Queue a Table podporují autorizaci pomocí sdíleného klíče. Klient používající autorizaci sdíleného klíče předá záhlaví s každým požadavkem podepsaným pomocí přístupového klíče účtu úložiště. Další informace naleznete v [tématu Authorize with Shared Key](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorizace pomocí sdílených přístupových podpisů (SAS).** Sdílený přístupový podpis (SAS) je řetězec obsahující token zabezpečení, který lze připojit k identifikátoru URI pro prostředek úložiště. Token zabezpečení zapouzdřuje omezení, jako jsou oprávnění a interval přístupu. Další informace naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](storage-sas-overview.md)
- **Anonymní přístup k kontejnery a objekty BLOB.** Kontejner a jeho objekty BLOB mohou být veřejně dostupné. Když zadáte, že kontejner nebo objekt blob je veřejný, kdokoli může číst anonymně; není vyžadováno žádné ověřování. Další informace naleznete v [tématu Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Šifrování

Pro základní služby úložiště jsou k dispozici dva základní druhy šifrování. Další informace o zabezpečení a šifrování najdete v [průvodci zabezpečením Azure Storage](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Šifrování Azure Storage chrání a chrání vaše data, aby splnila vaše závazky v oblasti zabezpečení a dodržování předpisů vaší organizace. Azure Storage automaticky šifruje všechna data před uchování matem na účet úložiště a dešifruje je před načtením. Procesy šifrování, dešifrování a správy klíčů jsou pro uživatele transparentní. Zákazníci se také mohou rozhodnout spravovat své vlastní klíče pomocí služby Azure Key Vault. Další informace najdete v tématu [šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Klientské knihovny Azure Storage poskytují metody pro šifrování dat z klientské knihovny před odesláním přes drát a dešifrování odpovědi. Data šifrovaná pomocí šifrování na straně klienta je také šifrována v klidovém stavu službou Azure Storage. Další informace o šifrování na straně klienta naleznete v [tématu šifrování na straně klienta pomocí rozhraní .NET for Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundance

Chcete-li zajistit, aby vaše data byla trvalá, Azure Storage ukládá více kopií vašich dat. Při nastavujete účet úložiště, vyberete možnost redundance. Další informace najdete v článku [Možnosti redundance Azure Storage](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Přenos dat do a z Azure Storage

Máte několik možností pro přesun dat do nebo z Azure Storage. Kterou možnost zvolíte, závisí na velikosti datové sady a šířce pásma sítě. Další informace najdete [v tématu Výběr řešení Azure pro přenos dat](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Ceny

Při rozhodování o tom, jak jsou vaše data uložena a jak k nim máte přístup, byste měli také zvážit související náklady. Další informace najdete v tématu [Ceny azure storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>Rozhraní API, knihovny a nástroje služby Storage

K prostředkům v účtu úložiště můžete přistupovat libovolným jazykem, který může provádět požadavky HTTP/HTTPS. Základní služby Azure Storage navíc nabízejí programovací knihovny pro několik oblíbených jazyků. Tyto knihovny zjednodušují spoustu aspektů práce s Azure Storage, protože se starají o drobnosti jako synchronní a asynchronní vyvolání, dávkování operací, řízení výjimek, automatické opakování pokusů, operační chování atd. Knihovny jsou aktuálně dostupné pro následující jazyky a platformy, další se připravují:

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
- [Nástroje klienta úložiště Azure](../storage-explorers.md)
- [Azure Developer Tools](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Další kroky

Pokud jde o zprovoznění základních služeb Azure Storage, přečtěte [si tématu Vytvoření účtu úložiště](storage-account-create.md).
