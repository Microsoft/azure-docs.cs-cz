---
title: Přehled účtu úložiště Azure | Dokumentace Microsoftu
description: Vysvětlení možností pro vytváření a používání účtu služby Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/13/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: d7dbb808205c78b53277c6d916f5166a41c7e93d
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638421"
---
# <a name="azure-storage-account-overview"></a>Přehled účtu Azure storage

Účet úložiště Azure obsahuje všechny datových objektů Azure Storage: objekty BLOB, soubory, fronty, tabulky a disky. Data ve vašem účtu úložiště Azure je odolné a vysoce dostupných a zabezpečené, rozsáhle škálovatelné a přístupný z kdekoli na světě prostřednictvím protokolu HTTP nebo HTTPS. 

Zjistěte, jak vytvořit účet úložiště Azure, najdete v článku [vytvořit účet úložiště](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Azure Storage nabízí tři typy účtů úložiště. Každý typ podporuje různé funkce a má vlastní cenový model. Než vytvoříte účet úložiště a určit typ účtu, který je nejvhodnější pro vaše aplikace, zvažte následující rozdíly. Typy účtů úložiště jsou:

* **[Účty pro obecné účely v2](#general-purpose-v2-accounts)**  (doporučeno pro většinu scénářů)
* **[Účty pro obecné účely v1](#general-purpose-v1-accounts)**
* **[Účty BLOB storage](#blob-storage-accounts)** 

Následující tabulka popisuje typy účtů úložiště a jejich funkce:

| Typ účtu úložiště | Podporované služby                       | Úrovně výkonu podporované | Podporované přístupu               | Možnosti replikace                                                | Model nasazení<sup>1</sup>  | Šifrování<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Pro obecné účely V2   | Objekt BLOB, soubor, fronty, tabulky a Disk       | Standard, Premium           | Horká, studená, archivní<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager | Šifrované  |
| Pro obecné účely V1   | Objekt BLOB, soubor, fronty, tabulky a Disk       | Standard, Premium           | neuvedeno                                  | LRS, GRS, RA-GRS                                                   | Resource Manager, Classic  | Šifrované  |
| Blob Storage         | Objekt BLOB (objekty BLOB bloku a doplňovacích objektů BLOB jenom) | Standard                    | Horká, studená, archivní<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Resource Manager  | Šifrované  |

<sup>1</sup>doporučuje se použít model nasazení Azure Resource Manageru. Účty úložiště pomocí modelu nasazení classic je možné vytvořit i nadále v některých umístěních, a nadále podporovat existující klasické účty. Další informace najdete v tématu [Azure Resource Manageru a klasického nasazení: vysvětlení modelů nasazení a stavu prostředků](../../azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>všechny účty úložiště jsou šifrované pomocí šifrování služby Storage (SSE) pro neaktivní uložená data. Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](storage-service-encryption.md).

<sup>3</sup>archivní úrovni není k dispozici na úrovni pouze jednotlivých objektů blob na úrovni účtu úložiště. Pouze objekty BLOB bloku a doplňovacích objektů blob je možné archivovat. Další informace najdete v tématu [Azure Blob storage: horká, studená a archivní úroveň úložiště](../blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zónově redundantní úložiště (ZRS) je k dispozici pouze pro účty úložiště standard pro obecné účely v2. Další informace o ZRS, naleznete v tématu [zónově redundantní úložiště (ZRS): aplikace služby Azure Storage s vysokou dostupností](storage-redundancy-zrs.md). Další informace o dalších možnostech replikace najdete v tématu [replikace Azure Storage](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Účty úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporuje nejnovější funkce služby Azure Storage a zapracovali všechny funkce pro obecné účely v1 a účty Blob storage. Účty pro obecné účely v2 doručování nejnižší podle sazby za gigabajt kapacity ceny pro Azure Storage, jakož i průmysl konkurenceschopných cen za transakce. Účty úložiště pro obecné účely v2 podporují tyto služby Azure Storage:

- Objekty BLOB (všechny typy: bloku, doplňovací, stránka)
- Soubory
- Disky
- Fronty
- Tabulky

> [!NOTE]
> Microsoft doporučuje používat účet úložiště pro obecné účely verze 2 pro většinu scénářů. Pro obecné účely v1 nebo účtu služby Blob storage můžete snadno upgradovat na účet pro obecné účely v2 došlo k výpadku a bez nutnosti kopírovat data.
>
> Další informace o upgradu na účet pro obecné účely v2 najdete v tématu [upgradovat na účet úložiště pro obecné účely v2](storage-account-upgrade.md). 

Účty úložiště pro obecné účely verze 2 nabízí několik úrovní přístupu pro ukládání dat na základě vaší způsobů využití. Další informace najdete v tématu [data v objektech blob úrovně přístupu pro blok](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Účty pro obecné účely verze 1

Účty pro obecné účely v1 umožňují přístup ke všem službám Azure Storage, ale nemusí zahrnovat nejnovější funkce nebo nejnižší ceny za gigabajt. Účty úložiště pro obecné účely v1 podporují tyto služby Azure Storage:

- Objekty BLOB (všechny typy)
- Soubory
- Disky
- Fronty
- Tabulky

Ve většině případů se doporučuje účty pro obecné účely v2, účty pro obecné účely v1 jsou nejvhodnější pro tyto scénáře: 

* Vaše aplikace vyžadují model nasazení Azure classic. Účty pro obecné účely v2 a účty úložiště Blob podporují jenom modelu nasazení Azure Resource Manageru.

* Vaše aplikace jsou náročné na transakci nebo využívat šířku pásma významné geografickou replikaci, ale nevyžadují velké kapacity. Pro obecné účely v1 v takovém případě může být nejhospodárnějším volbou.

* Používáte verzi rozhraní [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx), která je starší než 14.2.2014, nebo klientskou knihovnu verze nižší než 4.x a nemůžete svoji aplikaci upgradovat.

### <a name="blob-storage-accounts"></a>Účty úložiště Blob

Účet Blob storage je specializovaný účet úložiště pro ukládání nestrukturovaných dat objektů jako objekty BLOB bloku. Účty BLOB storage poskytuje stejnou odolnost, dostupnost, škálovatelnost a funkce výkonu, které jsou k dispozici s účty úložiště pro obecné účely v2. Účty úložiště BLOB podporují ukládání objektů BLOB bloku a doplňovací objekty BLOB, ale ne objekty BLOB stránky.

Účty BLOB storage nabízí několik úrovní přístupu pro ukládání dat na základě vaší způsobů využití. Další informace najdete v tématu [data v objektech blob úrovně přístupu pro blok](#access-tiers-for-block-blob-data).

## <a name="naming-storage-accounts"></a>Pojmenování účtů úložiště

Při pojmenování účtu úložiště mějte na paměti tato pravidla:

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Název vašeho účtu úložiště musí být jedinečný v rámci Azure. Žádné dva účty úložiště nemůžou mít stejný název.

## <a name="performance-tiers"></a>Úrovně výkonu

Účty úložiště pro obecné účely lze nakonfigurovat pro některý z následujících úrovní výkonu:

* Úroveň výkonu standard pro ukládání objektů BLOB, soubory, tabulky, fronty a disky virtuálních počítačů Azure.
* Úroveň výkonu premium pro ukládání jenom disky virtuálních počítačů Azure. Podrobný popis služby Premium Storage najdete v článku [Úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="access-tiers-for-block-blob-data"></a>Úrovně přístupu pro data objektů blob bloku

Azure Storage nabízí různé možnosti pro přístup k datům objektu blob bloku na základě způsobů využití. Každá úroveň přístupu ve službě Azure Storage je optimalizovaná pro konkrétní vzor data využití. Vyberte úroveň přístupu pro vaše potřeby, můžete ukládat data objektů blob bloku cenově nejvýhodnější způsobem.

K dispozici přístup úrovně jsou:

> [!NOTE]
> [Přístup na úrovni Premium](../blobs/storage-blob-storage-tiers.md#premium-access-tier) je k dispozici ve verzi limited preview jako účet místně redundantního úložiště (LRS) v oblastech severní Evropa, USA – východ 2, USA – střed a USA – západ. Zjistěte, jak zaregistrovat verzi preview, najdete v článku [Úvod do Azure Blob Storage úrovně Premium](http://aka.ms/premiumblob).

* **Hot** úrovně přístupu, která je optimalizovaná pro časté přístup k objektům v účtu úložiště. Přístup k datům v horké úrovni je cenově nejvýhodnější, zatímco jsou o něco vyšší náklady na úložiště. Nové účty úložiště jsou vytvořené v horké vrstvy ve výchozím nastavení.
* **Cool** úrovně přístupu, která je optimalizovaná pro ukládání velkých objemů dat, která se nevyužívají často a ukládají nejméně na 30 dnů. Ukládání dat do studené vrstvy začne být cenově výhodnější, ale přístup k těmto datům může být trochu nákladnější než přístup k datům v horké úrovni.
* **Archivu** úroveň, která je dostupná jenom pro objekty BLOB bloku jednotlivé. Archivní úroveň je optimalizovaná pro data, která se toleruje latence načtení několik hodin a zůstanou v archivní úrovni po dobu nejméně na 180 dnů. Archivní úroveň je cenově nejvýhodnější možnost pro ukládání dat, ale přístup k těmto datům je nákladnější než přístup k datům v horké nebo studené úrovně. 


Pokud dojde ke změně vzoru využití vašich dat, můžete přepínat mezi úrovněmi přístupu kdykoli. Další informace o úrovních přístupu najdete v části [Azure Blob storage: úrovně Premium (preview), horká, studená a archivní úroveň úložiště](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Změna úrovně přístupu pro existující účet úložiště nebo objekt blob může mít za následek další poplatky. Další informace najdete v tématu [účtu úložiště fakturační části](#storage-account-billing).



## <a name="replication"></a>Replikace

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Další informace o replikaci úložiště najdete v tématu [replikace Azure Storage](storage-redundancy.md).

## <a name="encryption"></a>Šifrování

Všechna data ve vašem účtu úložiště se šifrují na straně služby. Další informace o šifrování najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Koncové body účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, ve kterém je uložený ve službě Azure Storage má adresu, která obsahuje název vaší jedinečný účet. Kombinace názvu účtu a koncový bod služby Azure Storage tvoří koncové body účtu úložiště.

Například, pokud je název vašeho účtu úložiště pro obecné účely *mystorageaccount*, pak jsou výchozí koncové body pro tento účet:

* Úložiště objektů blob: http://*mystorageaccount*. blob.core.windows.net
* Table storage: http://*mystorageaccount*. table.core.windows.net
* Fronty úložiště: http://*mystorageaccount*. queue.core.windows.net
* Služba soubory Azure: http://*mystorageaccount*. file.core.windows.net

> [!NOTE]
> Účet Blob storage zpřístupňuje pouze objektů Blob koncový bod služby.

Adresa URL pro přístup k objektu v účtu úložiště je vytvořená připojením umístění objektu v účtu úložiště ke koncovému bodu. Například adresa účtu pro objekty blob může mít tento formát: http://*můj_účet_úložiště*.blob.core.windows.net/*můj_kontejner*/*můj_objekt_blob*.

Můžete také nakonfigurovat účtu úložiště používat vlastní doménu pro objekty BLOB. Další informace najdete v tématu [konfigurace vlastního názvu domény pro váš účet Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Řízení přístupu k datům v účtu

Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu. Budete mít kontrolu nad kdo může přistupovat k datům a jaká oprávnění máte.

Každý požadavek směřovaný vašeho účtu úložiště musí být autorizován. Na úrovni služby, musí obsahovat platný požadavek *autorizace* hlavičky, která obsahuje všechny informace potřebné pro službu pro ověření žádosti před jeho provedením.

Můžete udělit přístup k datům v účtu úložiště pomocí kteréhokoli z následujících postupů:

- **Azure Active Directory:** přihlašovacích údajů pomocí služby Azure Active Directory (Azure AD) k ověření uživatele, skupiny nebo jinou identitu pro přístup k datům objektu blob a frontu (preview). Pokud je úspěšné ověření identity, Azure AD vrátí token používané k autorizaci žádost o Azure Blob storage nebo Queue storage. Další informace najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory (preview)](storage-auth-aad.md).
- **Sdílený klíč autorizace:** přístupový klíč účtu úložiště můžete vytvořit připojovací řetězec, který vaše aplikace používá pro přístup k Azure Storage za běhu. Hodnoty v připojovacím řetězci se používají k vytvoření *autorizace* záhlaví, který je předán do služby Azure Storage. Další informace najdete v tématu [připojovacích řetězců Azure Storage konfigurace](storage-configure-connection-string.md).
- **Sdílený přístupový podpis:** pomocí sdíleného přístupového podpisu pro delegování přístupu k prostředkům ve vašem účtu úložiště, pokud nepoužíváte ověřování Azure AD. Sdílený přístupový podpis je token, který zapouzdřuje všechny informace potřebné k autorizaci žádost do služby Azure Storage na adresu URL. Můžete zadat úložiště prostředků, oprávnění udělená a interval, přes která oprávnění jsou platné jako součást sdílený přístupový podpis. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Ověřování uživatelů a aplikací s použitím přihlašovacích údajů Azure AD poskytuje nejvyšší zabezpečení a snadné použití přes jiným způsobem autorizace. Když můžete nadále používat povolení sdíleného klíče s vašimi aplikacemi, používání služby Azure AD obchází potřebou ukládání přístupový klíč k účtu s vaším kódem. Můžete taky dál používat sdílené přístupové podpisy (SAS) k udělení velice přesně kontrolovat přístup k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo si dělat starosti o odvolání SAS ohrožení zabezpečení. 
>
> Microsoft doporučuje používat ověřování Azure AD pro Azure Storage blob a fronty aplikace Pokud je to možné.

## <a name="copying-data-into-a-storage-account"></a>Kopírování dat do účtu úložiště

Společnost Microsoft poskytuje nástroje a knihovny pro import dat z místních úložných zařízení nebo poskytovatelé cloudu jiného dodavatele úložiště. Řešení, které použijete, závisí na množství dat, která jsou přenosu. 

Při upgradu na účet pro obecné účely v2 pro obecné účely v1 nebo účtu služby Blob storage, vaše data jsou migrována automaticky. Společnost Microsoft doporučuje tuto cestu k upgradu vašeho účtu. Ale pokud se rozhodnete pro přesun dat z účtu pro obecné účely v1 do účtu úložiště Blob, pak budete muset ručně migrovat data, pomocí nástrojů a knihoven, které jsou popsané níže. 

### <a name="azcopy"></a>AzCopy

AzCopy je nástroj Windows, který se spouští z příkazového řádku a který je určený pro vysoce výkonné kopírování dat do Azure Storage a z Azure Storage. Pomocí AzCopy můžete kopírovat data do účtu úložiště Blob z existující účet úložiště pro obecné účely nebo nahrávat data z místních úložných zařízení. Další informace najdete v tématu [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Knihovna pro přesun dat

Knihovna pro přesun dat v Azure Storage pro .NET je založená na základní platformě pro přesun dat, kterou využívá nástroj AzCopy. Knihovna je určená pro vysoce výkonné, spolehlivé a snadné operace přenosu dat, podobně jako AzCopy. Pomocí této knihovny můžete využívat výhody funkcí AzCopy ve svojí aplikaci nativně, bez nutnosti spouštět a sledovat externí instance nástroje AzCopy. Další informace najdete v tématu [Knihovna pro přesun dat v Azure Storage pro .Net](https://github.com/Azure/azure-storage-net-data-movement).

### <a name="rest-api-or-client-library"></a>Rozhraní REST API nebo klientská knihovna

Pomocí některé z knihoven klienta Azure nebo rozhraní REST API služeb úložiště Azure můžete vytvořit vlastní aplikaci pro migraci dat do účtu úložiště Blob. Azure Storage poskytuje množství knihoven klienta pro různé jazyky a platformy, jako například .NET, Java, C++, Node.JS, PHP, Ruby nebo Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Další informace o REST API služby Azure Storage najdete v tématu [referenčních informacích k Azure Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Objekty blob šifrované na straně klienta ukládají metadata šifrování s objektem blob. Pokud kopírujete objekt blob šifrovaný na straně klienta, ujistěte se, že operace kopírování zachová metadata objektu blob, zejména metadata šifrování. Pokud objekt blob zkopírujete bez metadat šifrování, obsah objektu blob bude nenávratně ztracený. Další informace o metadatech šifrování najdete v tématu [Azure Storage a šifrování na straně klienta](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Služba Azure Import/Export

Pokud máte velké množství dat pro import do vašeho účtu úložiště, zvažte službu Azure Import/Export. Službu Import/Export je bezpečně Import velkých objemů dat do úložiště objektů Blob v Azure a službou soubory Azure používá přesouvání disků do datacentra Azure. 

Služba Import/Export lze použít také k přenosu dat z úložiště objektů Blob v Azure diskových jednotek a odešlete ji do vašich místních lokalit. Můžete importovat data z jedné nebo víc diskových jednotek, buď do Azure Blob storage nebo Azure Files. Další informace najdete v tématu [co je služba Azure Import/Export?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Fakturace účtu úložiště

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Další postup

* Zjistěte, jak vytvořit účet úložiště Azure, najdete v článku [vytvořit účet úložiště](storage-quickstart-create-account.md).
* Pokud chcete spravovat nebo odstranit existující účet úložiště, přečtěte si téma [spravovat Azure storage účty](storage-account-manage.md).
