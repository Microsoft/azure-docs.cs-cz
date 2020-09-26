---
title: Přehled účtu úložiště
titleSuffix: Azure Storage
description: Přečtěte si přehled účtů úložiště v Azure Storage. Zkontrolujte pojmenování účtů, úrovně výkonu, úrovně přístupu, redundanci, šifrování, koncové body a další.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c93508bebdcfce35a89b3d5e2a8abecc7ac84722
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280143"
---
# <a name="storage-account-overview"></a>Přehled účtu úložiště

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Data v účtu úložiště Azure jsou trvalá a vysoce dostupná, zabezpečená a rozsáhlá.

Informace o tom, jak vytvořit účet úložiště Azure, najdete v tématu [Vytvoření účtu úložiště](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Účty úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporují nejnovější funkce Azure Storage a zahrnují všechny funkce účtů pro obecné účely V1 a BLOB Storage. Účty pro obecné účely v2 poskytují nejnižší cenu za GB pro Azure Storage a také ceny za transakce z odvětví konkurenčních produktů. Účty úložiště pro obecné účely v2 podporují tyto služby Azure Storage:

- Objekty BLOB (všechny typy: blokovat, připojit, stránka)
- Data Lake Gen2
- Soubory
- Disky
- Fronty
- Tabulky

> [!NOTE]
> Microsoft doporučuje pro většinu scénářů používat účet úložiště pro obecné účely v2. Účet úložiště pro obecné účely v1 nebo blob můžete snadno upgradovat na účet pro obecné účely v2 bez výpadků a bez nutnosti kopírovat data.
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

Ve většině případů byste měli používat účty pro obecné účely v2. Účty pro obecné účely v1 můžete použít pro tyto scénáře:

- Vaše aplikace vyžadují model nasazení Azure Classic. Účty pro obecné účely v2 a účty BLOB Storage podporují jenom model nasazení Azure Resource Manager.

- Vaše aplikace jsou náročné na transakce nebo využívají značnou šířku pásma geografické replikace, ale nevyžadují velkou kapacitu. V tomto případě může být obecným účelem v1 největší ekonomická volba.

- Používáte verzi [REST API služby úložiště](https://msdn.microsoft.com/library/azure/dd894041.aspx) , která je starší než 2014-02-14 nebo Klientská knihovna s verzí nižší než 4. x. Nemůžete upgradovat svoji aplikaci.

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

V závislosti na typu vytvářeného účtu úložiště si můžete vybrat mezi úrovní výkonu Standard a Premium.

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

> [!IMPORTANT]
> Změna úrovně přístupu pro existující účet úložiště nebo objekt BLOB může mít za následek další poplatky. Další informace najdete v [části fakturace účtu úložiště](#storage-account-billing).

## <a name="redundancy"></a>Redundance

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Šifrování

Všechna data v účtu úložiště se šifrují na straně služby. Další informace o šifrování najdete v tématu [šifrování služby Azure Storage pro](storage-service-encryption.md)neaktivní neaktivní data.

## <a name="storage-account-endpoints"></a>Koncové body účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který je uložen v Azure Storage má adresu, která obsahuje jedinečný název účtu. Kombinace názvu účtu a koncového bodu služby Azure Storage tvoří koncové body pro váš účet úložiště.

Pokud má například účet úložiště pro obecné účely název *mystorageaccount*, výchozí koncové body tohoto účtu jsou následující:

- Úložiště objektů BLOB: `https://*mystorageaccount*.blob.core.windows.net`
- Úložiště tabulek: `https://*mystorageaccount*.table.core.windows.net`
- Úložiště fronty: `https://*mystorageaccount*.queue.core.windows.net`
- Soubory Azure: `https://*mystorageaccount*.file.core.windows.net`
- Azure Data Lake Storage Gen2: `https://*mystorageaccount*.dfs.core.windows.net` (používá [ovladač ABFS optimalizovaný speciálně pro velké](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction#key-features-of-data-lake-storage-gen2)objemy dat.)

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
- **Sdílený přístupový podpis:** Pokud nepoužíváte autorizaci Azure AD, můžete pomocí sdíleného přístupového podpisu delegovat přístup k prostředkům v účtu úložiště. Sdílený přístupový podpis je token, který zapouzdřuje všechny informace potřebné k autorizaci žádosti o Azure Storage na adrese URL. Můžete zadat prostředek úložiště, udělená oprávnění a interval, ve kterém jsou oprávnění platná, jako součást sdíleného přístupového podpisu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

> [!NOTE]
> Ověřování uživatelů nebo aplikací pomocí přihlašovacích údajů Azure AD zajišťuje zabezpečení a jednoduchost použití prostřednictvím jiných způsobů autorizace. I když můžete nadále používat autorizaci pomocí sdíleného klíče u svých aplikací, použití Azure AD obchází nutnost uložení přístupového klíče účtu k vašemu kódu. Můžete taky dál používat sdílené přístupové podpisy (SAS) pro udělení jemně odstupňovaného přístupu k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné možnosti, aniž by bylo potřeba spravovat tokeny SAS nebo se starat o odvolání napadeného SAS.
>
> Microsoft doporučuje použít autorizaci Azure AD pro Azure Storage objekty BLOB a aplikace fronty, pokud je to možné.

## <a name="copying-data-into-a-storage-account"></a>Kopírování dat do účtu úložiště

Microsoft poskytuje nástroje a knihovny pro import dat z místních úložných zařízení nebo poskytovatelů cloudového úložiště třetích stran. Které řešení použijete, záleží na množství převáděných dat.

Při upgradu na účet pro obecné účely v2 z obecného účtu úložiště v1 nebo BLOB se data automaticky migrují. Microsoft doporučuje tuto stáži k upgradu vašeho účtu. Pokud se ale rozhodnete přesunout data z účtu pro obecné účely V1 do účtu úložiště BLOB, budete data migrovat ručně pomocí nástrojů a knihoven popsaných níže.

### <a name="azcopy"></a>AzCopy

AzCopy je nástroj Windows, který se spouští z příkazového řádku a který je určený pro vysoce výkonné kopírování dat do Azure Storage a z Azure Storage. Pomocí AzCopy můžete kopírovat data do účtu BLOB Storage z existujícího účtu úložiště pro obecné účely nebo nahrávat data z místních úložných zařízení. Další informace najdete v tématu [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Knihovna pro přesun dat

Knihovna pro přesun dat v Azure Storage pro .NET je založená na základní platformě pro přesun dat, kterou využívá nástroj AzCopy. Knihovna je určená pro vysoce výkonné, spolehlivé a snadné operace přenosu dat, podobně jako AzCopy. Knihovnu pro přesun dat můžete použít k nativně využití funkcí AzCopy. Další informace najdete v tématu [Azure Storage knihovny pro přesun dat pro .NET](https://github.com/Azure/azure-storage-net-data-movement) .

### <a name="rest-api-or-client-library"></a>Rozhraní REST API nebo klientská knihovna

Můžete vytvořit vlastní aplikaci pro migraci dat z účtu úložiště pro obecné účely V1 na účet úložiště BLOB. Použijte jednu z klientských knihoven Azure nebo REST API služby Azure Storage. Azure Storage poskytuje množství knihoven klienta pro různé jazyky a platformy, jako například .NET, Java, C++, Node.JS, PHP, Ruby nebo Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Další informace o REST API Azure Storage najdete v tématu informace o [REST API služby Azure Storage Services](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Objekty blob šifrované na straně klienta ukládají metadata šifrování s objektem blob. Pokud kopírujete objekt blob šifrovaný na straně klienta, ujistěte se, že operace kopírování zachová metadata objektu blob, zejména metadata šifrování. Pokud objekt blob zkopírujete bez metadat šifrování, obsah objektu blob bude nenávratně ztracený. Další informace o metadatech šifrování najdete v tématu [Azure Storage a šifrování na straně klienta](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fakturace účtu úložiště

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](storage-account-create.md)
- [Vytvoření účtu úložiště objektů blob bloku](../blobs/storage-blob-create-account-block-blob.md)
