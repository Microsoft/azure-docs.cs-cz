---
title: Přehled účtu úložiště
titleSuffix: Azure Storage
description: Přečtěte si o různých typech účtů úložiště v Azure Storage. Zkontrolujte pojmenování účtů, úrovně výkonu, úrovně přístupu, redundanci, šifrování, koncové body a další.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 24d955b0d1c53f57f5927f9e893b6ecd75fb3ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561887"
---
# <a name="storage-account-overview"></a>Přehled účtu úložiště

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Data v účtu úložiště Azure jsou trvalá a vysoce dostupná, zabezpečená a rozsáhlá.

Informace o vytvoření účtu úložiště Azure najdete v tématu [Vytvoření účtu úložiště](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Před vytvořením účtu úložiště zvažte tyto rozdíly a určete typ účtu, který je pro vaše aplikace nejvhodnější. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: základní typ účtu úložiště pro objekty blob, soubory, fronty a tabulky. Doporučuje se ve většině scénářů pomocí Azure Storage.
- **Účty pro obecné účely v1**: starší typ účtu pro objekty blob, soubory, fronty a tabulky. Pokud je to možné, použijte místo toho účty pro obecné účely v2.
- **Účty BlockBlobStorage**: účty úložiště s charakteristikou výkonu Premium pro objekty blob bloku a doplňovací objekty blob. Doporučuje se u scénářů s vysokými sazbami transakcí nebo scénářů, které používají menší objekty nebo vyžadují konzistentně nízkou latenci úložiště.
- **Účty úložiště** souborů: jenom účty úložiště s charakteristikami výkonu Premium. Doporučuje se pro podnikové nebo vysoce výkonné škálování aplikace.
- **Účty BlobStorage**: starší účty úložiště jen pro objekty blob. Pokud je to možné, použijte místo toho účty pro obecné účely v2.

Následující tabulka popisuje typy účtů úložiště, služby, které podporují, a podporované modely nasazení pro každý typ účtu:

| Typ účtu úložiště | Podporované služby | Možnosti redundance | Model nasazení<sup>1</sup> |
|--|--|--|--|
| Obecné účely v2 | Objekt blob, soubor, fronta, tabulka, disk a Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Resource Manager |
| Obecné účely v1 | Objekt blob, soubor, fronta, tabulka a disk | LRS, GRS, RA-GRS | Správce prostředků, klasický |
| BlockBlobStorage | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | LRS, ZRS<sup>3</sup> | Resource Manager |
| Úložiště | Pouze soubor | LRS, ZRS<sup>3</sup> | Resource Manager |
| Blob Storage | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | LRS, GRS, RA-GRS | Resource Manager |

<sup>1</sup> Doporučuje se použít model nasazení Azure Resource Manager. Účty úložiště, které používají model nasazení Classic, se stále dají vytvořit v některých umístěních a stávající klasické účty se pořád podporují. Další informace najdete v tématu [Azure Resource Manager vs. Classic Deployment: Principy modelů nasazení a stavu vašich prostředků](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> . Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage. Data Lake Storage Gen2 se podporuje jenom pro účty úložiště pro obecné účely v2 s povoleným hierarchickým oborem názvů. Další informace o Data Lake Storage Gen2 najdete v tématu [Úvod do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> . Zóna – redundantní úložiště (ZRS) a geograficky redundantní úložiště (GZRS/RA-GZRS) jsou dostupné jenom pro účty standard pro obecné účely v2, BlockBlobStorage a úložiště v některých oblastech. Další informace o možnostech redundance Azure Storage najdete v tématu [Azure Storage redundance](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Redundance účtu úložiště

Mezi možnosti redundance účtu úložiště patří:

- **Místně redundantní úložiště (LRS)**: jednoduchá strategie redundance s nízkými náklady. Data se zkopírují synchronně třikrát v jednom fyzickém umístění v primární oblasti.
- **Zóna – redundantní úložiště (ZRS)**: redundance pro scénáře vyžadující vysokou dostupnost. Data se zkopírují synchronně v rámci tří zón dostupnosti Azure v primární oblasti.
- **Geograficky redundantní úložiště (GRS)**: meziregionální redundance pro ochranu před místními výpadky. Data se zkopírují synchronně třikrát v primární oblasti a zkopírují se asynchronně do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
- **Geograficky redundantní úložiště (GZRS)**: redundance pro scénáře, které vyžadují vysokou dostupnost a maximální odolnost. Data se zkopírují synchronně v rámci tří zón dostupnosti Azure v primární oblasti a pak se asynchronně zkopírovaly do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte přístup pro čtení Geo-Zone-redundantní úložiště (RA-GZRS).

Další informace o možnostech redundance v Azure Storage najdete v tématu [Azure Storage redundance](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Účty úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporují nejnovější funkce Azure Storage a zahrnují všechny funkce účtů pro obecné účely V1 a BLOB Storage. Účty pro obecné účely v2 poskytují nejnižší cenu za GB pro Azure Storage a také ceny za transakce z odvětví konkurenčních produktů. Účty úložiště pro obecné účely v2 podporují tyto služby Azure Storage:

- Objekty BLOB (všechny typy: blokovat, připojit, stránka)
- Data Lake Gen2
- Soubory
- Disky
- Fronty
- Tabulky

> [!NOTE]
> Microsoft doporučuje pro většinu scénářů používat účet úložiště pro obecné účely v2. Účet úložiště pro obecné účely v1 nebo blob můžete snadno upgradovat na účet pro obecné účely v2 bez výpadků a bez nutnosti kopírovat data. Upgrade však nelze vrátit zpět.
>
> Další informace o tom, jak upgradovat na účet pro obecné účely v2, najdete v tématu [upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md).

Účty úložiště pro obecné účely v2 nabízejí více úrovní přístupu pro ukládání dat na základě vašich způsobů použití. Další informace najdete v tématu [úrovně přístupu pro data objektů blob bloku](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Účty pro obecné účely verze 1

Účty úložiště pro obecné účely v1 poskytují přístup ke všem službám Azure Storage, ale nemusí mít nejnovější funkce nebo nejnižší ceny za gigabajt. Účty úložiště pro obecné účely v1 podporují tyto služby Azure Storage:

- Objekty BLOB (všechny typy)
- Soubory
- Disky
- Fronty
- Tabulky

Účty pro obecné účely v1 můžete použít pro tyto scénáře:

- Vaše aplikace vyžadují model nasazení Azure Classic. Účty pro obecné účely v2 a účty BLOB Storage podporují jenom model nasazení Azure Resource Manager.

- Vaše aplikace jsou náročné na transakce nebo využívají značnou šířku pásma geografické replikace, ale nevyžadují velkou kapacitu. V tomto případě může být obecným účelem v1 největší ekonomická volba.

- Používáte verzi [REST API služby úložiště](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) , která je starší než 2014-02-14 nebo Klientská knihovna s verzí nižší než 4. x. Nemůžete upgradovat svoji aplikaci.

> [!NOTE]
> I když společnost Microsoft doporučuje pro většinu scénářů použití účtů pro obecné účely verze 2, bude společnost Microsoft nadále podporovat účty pro obecné účely V1 pro nové i stávající zákazníky. Můžete vytvořit účty úložiště pro obecné účely v1 v nových oblastech, kdykoli je Azure Storage k dispozici v těchto oblastech. Společnost Microsoft v současné době nemá k dispozici plán pro vyřazení podpory pro účty pro obecné účely V1 a před vyřazením jakékoli Azure Storageé funkce poskytne předběžné oznámení alespoň na jeden rok. Microsoft bude dál poskytovat aktualizace zabezpečení pro účty pro obecné účely V1, ale pro tento typ účtu se neočekává žádný nový vývoj funkcí.
>
> Od 1. října 2020 se ceny pro účty pro obecné účely v1 v nových Azure Storagech oblastech rovnají s cenami pro účty pro obecné účely V2 v těchto oblastech. Ceny v existujících oblastech Azure Storage se nezměnily. Podrobnosti o cenách pro účty pro obecné účely v1 v konkrétní oblasti najdete na stránce s cenami Azure Storage. Zvolte oblast a potom klikněte na **nabídku ceny** a pak vyberte **Další**.

### <a name="blockblobstorage-accounts"></a>Účty BlockBlobStorage

Účet BlockBlobStorage je specializovaný účet úložiště v úrovni výkonu Premium pro ukládání nestrukturovaných dat objektu jako objekty blob bloku nebo doplňovací objekty blob. V porovnání s účty pro obecné účely v2 a BlobStorage účty BlockBlobStorage poskytují nízké a konzistentní sazby za latenci a vyšší transakce.

Účty BlockBlobStorage v současné době nepodporují vrstvy přístupu na horkou, studenou nebo archivní úroveň. Tento typ účtu úložiště nepodporuje objekty blob stránky, tabulky ani fronty.

### <a name="filestorage-accounts"></a>Účty úložiště

Účet úložiště souborů je specializovaný účet úložiště, který slouží k ukládání a vytváření sdílených složek Premium. Tento druh účtu úložiště podporuje soubory, ale ne objekty blob bloku, doplňovací objekty blob, objekty blob stránky, tabulky nebo fronty.

Účty úložiště obsahují jedinečné vyhrazené charakteristiky výkonu, jako je třeba shlukování IOPS. Další informace o těchto vlastnostech najdete v části [vrstvy úložiště sdílení souborů](../files/storage-files-planning.md#storage-tiers) v příručce pro plánování souborů.

## <a name="naming-storage-accounts"></a>Pojmenování účtů úložiště

Při pojmenování účtu úložiště mějte na paměti tato pravidla:

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Váš název účtu úložiště musí být jedinečný v rámci Azure. Žádné dva účty úložiště nemohou mít stejný název.

## <a name="performance-tiers"></a>Úrovně výkonu

V závislosti na typu vytvářeného účtu úložiště si můžete vybrat mezi úrovní výkonu Standard a Premium. Následující tabulka shrnuje, které úrovně výkonu jsou k dispozici pro daný typ účtu úložiště.

| Typ účtu úložiště | Podporované úrovně výkonu |
|--|--|
| Obecné účely v2 | Standard, Premium<sup>1</sup> |
| Obecné účely v1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| Úložiště | Premium |
| Blob Storage | Standard |

<sup>1</sup> K dispozici je výkon úrovně Premium pro účty pro obecné účely v2 a obecné účely v1 jenom pro objekty blob disku a stránky. Výkon služby Premium pro objekty blob bloku nebo Append je k dispozici pouze v účtech BlockBlobStorage. Premium Performance for Files je k dispozici pouze v účtech úložiště.

### <a name="general-purpose-storage-accounts"></a>Účty úložiště pro obecné účely

Účty úložiště pro obecné účely můžou být nakonfigurované pro jednu z následujících úrovní výkonu:

- Standardní úroveň výkonu pro ukládání objektů blob, souborů, tabulek, front a disků virtuálních počítačů Azure. Další informace o cílech škálovatelnosti pro účty úložiště úrovně Standard najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](scalability-targets-standard-account.md).
- Úroveň výkonu Premium pro ukládání nespravovaných disků virtuálních počítačů. Microsoft doporučuje místo nespravovaných disků používat spravované disky s virtuálními počítači Azure. Další informace o cílech škálovatelnosti pro úroveň výkonu Premium najdete v tématu [cíle škálovatelnosti pro účty úložiště objektů blob stránky úrovně Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Účty úložiště BlockBlobStorage

Účty úložiště BlockBlobStorage poskytují úroveň výkonu Premium pro ukládání objektů blob bloku a doplňovací objektů BLOB. Další informace najdete v tématu [cíle škálovatelnosti pro účty úložiště objektů blob bloku úrovně Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Účty úložiště úložiště

Účty úložiště úložiště obsahují úroveň výkonu Premium pro sdílené složky Azure. Další informace najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Úrovně přístupu pro data objektů blob bloku

Azure Storage poskytuje různé možnosti pro přístup k datům objektů blob bloku založených na vzorech použití. Každá úroveň přístupu v Azure Storage je optimalizovaná pro konkrétní vzor využití dat. Výběrem správné úrovně přístupu pro vaše potřeby můžete ukládat data objektů blob bloku v nejefektivnějším úsporném režimu.

Dostupné úrovně přístupu jsou:

- Vrstva **Hot** Access. Tato úroveň je optimalizovaná pro častější přístup k objektům v účtu úložiště. Přístup k datům v Hot úrovně je největší nákladově efektivní, zatímco náklady na úložiště jsou vyšší. Ve výchozím nastavení se v aktivní úrovni vytvoří nové účty úložiště.
- **Studená** úroveň přístupu. Tato úroveň je optimalizovaná pro ukládání velkých objemů dat, která se nepoužívají zřídka a ukládají se nejméně na 30 dní. Ukládání dat ve studené vrstvě je cenově výhodnější, ale přístup k nim může být dražší než přístup k datům v horké vrstvě.
- **Archivní** úroveň: Tato úroveň je k dispozici pouze pro jednotlivé objekty blob bloku. Archivní úroveň je optimalizovaná pro data, která můžou tolerovat několik hodin latence načítání a které zůstanou v archivní úrovni aspoň 180 dní. Úroveň archivace je nejefektivnější možností pro ukládání dat. Přístup k těmto datům je ale dražší než přístup k datům na horké nebo studené úrovni.

Pokud dojde ke změně ve vzoru používání vašich dat, můžete kdykoli přepínat mezi těmito úrovněmi přístupu. Další informace o úrovních přístupu najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md).

Následující tabulka ukazuje, které úrovně přístupu jsou k dispozici pro objekty BLOB v každém typu účtu úložiště.

| Typ účtu úložiště | Podporované úrovně přístupu |
|--|--|
| Obecné účely v2 | Horká, studená, Archivovaná<sup>1</sup> |
| Obecné účely v1 | – |
| BlockBlobStorage | – |
| Úložiště | – |
| Blob Storage | Horká, studená, Archivovaná<sup>1</sup> |

<sup>1</sup> archivní úložiště a vrstvení na úrovni objektů BLOB podporují jenom objekty blob bloku. Archivní úroveň je k dispozici pouze na úrovni jednotlivých objektů blob, nikoli na úrovni účtu úložiště. Další informace najdete v tématu [úrovně přístupu pro Azure Blob Storage – horká, studená a archivní](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Změna úrovně přístupu pro existující účet úložiště nebo objekt BLOB může mít za následek další poplatky. Další informace najdete v tématu [fakturace účtu úložiště](#storage-account-billing).

## <a name="encryption"></a>Šifrování

Všechna data v účtu úložiště se šifrují na straně služby. Další informace o šifrování najdete v tématu [šifrování služby Azure Storage pro](storage-service-encryption.md)neaktivní neaktivní data.

## <a name="storage-account-endpoints"></a>Koncové body účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který je uložen v Azure Storage má adresu, která obsahuje jedinečný název účtu. Kombinace názvu účtu a koncového bodu služby Azure Storage tvoří koncové body pro váš účet úložiště.

V následující tabulce je uveden seznam koncových bodů pro každou službu Azure Storage Services.

| Služba úložiště | Koncový bod |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> Objekty blob bloku a účty BLOB Storage zpřístupňují jenom Blob service koncový bod.

Vytvořte adresu URL pro přístup k objektu v účtu úložiště připojením umístění objektu v účtu úložiště ke koncovému bodu. Například adresa účtu pro objekty blob může mít tento formát: http://*můj_účet_úložiště*.blob.core.windows.net/*můj_kontejner*/*můj_objekt_blob*.

Svůj účet úložiště můžete také nakonfigurovat tak, aby používal vlastní doménu pro objekty blob. Další informace najdete v tématu [Konfigurace vlastního názvu domény pro účet Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Řízení přístupu k datům účtu

Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu. Máte kontrolu nad tím, kdo může mít přístup k vašim datům a jaká oprávnění mají.

Každý požadavek vytvořený na váš účet úložiště musí být autorizovaný. Na úrovni služby musí požadavek zahrnovat platnou *autorizační* hlavičku. Konkrétně tato hlavička obsahuje všechny informace potřebné k tomu, aby služba před spuštěním ověřila požadavek.

Přístup k datům ve vašem účtu úložiště můžete udělit pomocí některého z následujících přístupů:

- **Azure Active Directory:** Použijte přihlašovací údaje pro Azure Active Directory (Azure AD) k ověření uživatele, skupiny nebo jiné identity pro přístup k datům objektů BLOB a front. Pokud je ověření identity úspěšné, pak Azure AD vrátí token, který se použije při autorizaci žádosti do úložiště objektů BLOB v Azure nebo ve frontě. Další informace najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).
- **Autorizace sdíleného klíče:** Použijte přístupový klíč účtu úložiště k vytvoření připojovacího řetězce, který vaše aplikace používá za běhu pro přístup k Azure Storage. Hodnoty v připojovacím řetězci slouží k vytvoření hlavičky *autorizace* , která je předána Azure Storage. Další informace najdete v tématu [Konfigurace připojovacích řetězců Azure Storage](storage-configure-connection-string.md).
- **Sdílený přístupový podpis:** Sdílený přístupový podpis (SAS) je token, který umožňuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Token SAS zapouzdřuje všechny informace potřebné k autorizaci žádosti o Azure Storage na adrese URL. Když vytvoříte SAS, můžete určit, která oprávnění SAS udělí prostředku, a interval, po kterém jsou oprávnění platná. Token SAS se dá podepsat buď pomocí přihlašovacích údajů Azure AD, nebo pomocí sdíleného klíče. Další informace najdete v tématu [udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

> [!NOTE]
> Ověřování uživatelů nebo aplikací pomocí přihlašovacích údajů Azure AD zajišťuje zabezpečení a jednoduchost použití prostřednictvím jiných způsobů autorizace. I když můžete nadále používat autorizaci pomocí sdíleného klíče u svých aplikací, použití Azure AD obchází nutnost uložení přístupového klíče účtu k vašemu kódu. Můžete taky dál používat sdílené přístupové podpisy (SAS) pro udělení jemně odstupňovaného přístupu k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné možnosti, aniž by bylo potřeba spravovat tokeny SAS nebo se starat o odvolání napadeného SAS.
>
> Microsoft doporučuje použít autorizaci Azure AD pro Azure Storage objekty BLOB a aplikace fronty, pokud je to možné.

## <a name="copying-data-into-a-storage-account"></a>Kopírování dat do účtu úložiště

Microsoft poskytuje nástroje a knihovny pro import dat z místních úložných zařízení nebo poskytovatelů cloudového úložiště třetích stran. Které řešení použijete, záleží na množství převáděných dat.

Při upgradu na účet pro obecné účely v2 z obecného účtu úložiště v1 nebo BLOB se data automaticky migrují. Microsoft doporučuje tuto stáži k upgradu vašeho účtu. Pokud se ale rozhodnete přesunout data z účtu pro obecné účely V1 do účtu úložiště BLOB, budete data migrovat ručně pomocí nástrojů a knihoven popsaných níže.

### <a name="azcopy"></a>AzCopy

AzCopy je nástroj Windows, který se spouští z příkazového řádku a který je určený pro vysoce výkonné kopírování dat do Azure Storage a z Azure Storage. Pomocí AzCopy můžete kopírovat data do účtu BLOB Storage z existujícího účtu úložiště pro obecné účely nebo nahrávat data z místních úložných zařízení. Další informace najdete v tématu [Přenos dat pomocí nástroje příkazového řádku AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Knihovna pro přesun dat

Knihovna pro přesun dat v Azure Storage pro .NET je založená na základní platformě pro přesun dat, kterou využívá nástroj AzCopy. Knihovna je určená pro vysoce výkonné, spolehlivé a snadné operace přenosu dat, podobně jako AzCopy. Knihovnu pro přesun dat můžete použít k nativně využití funkcí AzCopy. Další informace najdete v tématu [Azure Storage knihovny pro přesun dat pro .NET](https://github.com/Azure/azure-storage-net-data-movement) .

### <a name="rest-api-or-client-library"></a>Rozhraní REST API nebo klientská knihovna

Můžete vytvořit vlastní aplikaci pro migraci dat z účtu úložiště pro obecné účely V1 na účet úložiště BLOB. Použijte jednu z klientských knihoven Azure nebo REST API služby Azure Storage. Azure Storage poskytuje množství knihoven klienta pro různé jazyky a platformy, jako například .NET, Java, C++, Node.JS, PHP, Ruby nebo Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Další informace o REST API Azure Storage najdete v tématu informace o [REST API služby Azure Storage Services](/rest/api/storageservices/).

> [!IMPORTANT]
> Objekty blob šifrované na straně klienta ukládají metadata šifrování s objektem blob. Pokud kopírujete objekt blob šifrovaný na straně klienta, ujistěte se, že operace kopírování zachová metadata objektu blob, zejména metadata šifrování. Pokud objekt blob zkopírujete bez metadat šifrování, obsah objektu blob bude nenávratně ztracený. Další informace o metadatech šifrování najdete v tématu [Azure Storage a šifrování na straně klienta](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fakturace účtu úložiště

VyAzure Storage účty na základě využití účtu úložiště. Všechny objekty v rámci účtu úložiště se fakturují společně jako skupina. Náklady na úložiště se počítají v závislosti na následujících faktorech:

- **Oblast** odkazuje na geografickou oblast, ve které je účet založený.
- **Typ účtu** odkazuje na typ účtu úložiště, který používáte.
- **Úroveň přístupu** odkazuje na model využití dat, který jste zadali pro účet úložiště pro obecné účely v2 nebo BLOB.
- **Kapacita** odkazuje na to, kolik z vašich účtů úložiště používáte k ukládání dat.
- **Replikace** určuje, kolik kopií vašich dat se uchovává najednou a v jakých umístěních.
- **Transakce** odkazují na všechny operace čtení a zápisu na Azure Storage.
- **Výstup dat** odkazuje na všechna data přenesená z oblasti Azure. Když k datům v účtu úložiště přistupovala aplikace, která neběží ve stejné oblasti, účtují se vám poplatky za výstup dat. Informace o použití skupin prostředků k seskupení dat a služeb ve stejné oblasti za účelem omezení nákladů na výstup najdete v tématu [co je skupina prostředků Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) najdete podrobné informace o cenách na základě typu účtu, kapacity úložiště, replikace a transakcí. Na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/) najdete podrobné informace o sazbách za odchozí data. Představu o nákladech můžete získat v [Cenové kalkulačce pro Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management).

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](storage-account-create.md)
- [Vytvoření účtu úložiště objektů blob bloku](../blobs/storage-blob-create-account-block-blob.md)
- [Upgrade na účet úložiště pro obecné účely verze 2](storage-account-upgrade.md)
- [Obnovení odstraněného účtu úložiště](storage-account-recover.md)