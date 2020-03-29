---
title: Přehled účtu úložiště
titleSuffix: Azure Storage
description: Seznamte se s možnostmi vytváření a používání účtu Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371545"
---
# <a name="storage-account-overview"></a>Přehled účtu úložiště

Účet úložiště Azure obsahuje všechny datové objekty Azure Storage: objekty BLOB, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro vaše data azure storage, která je přístupná z libovolného místa na světě přes HTTP nebo HTTPS. Data ve vašem účtu úložiště Azure jsou trvanlivá a vysoce dostupná, zabezpečená a masivně škálovatelná.

Informace o tom, jak vytvořit účet úložiště Azure, najdete [v tématu Vytvoření účtu úložiště](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Účty úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporují nejnovější funkce Azure Storage a zahrnují všechny funkce účtů úložiště pro obecné účely v1 a Blob. Účty pro obecné účely v2 poskytují nejnižší ceny kapacity za gigabajt pro Azure Storage, stejně jako ceny transakcí konkurenceschopné v oboru. Účty úložiště pro obecné účely v2 podporují tyto služby úložiště Azure:

- Objekty BLOB (všechny typy: Blok, Připojit, Stránka)
- Data Lake Gen2
- Soubory
- Disky
- Fronty
- Tabulky

> [!NOTE]
> Společnost Microsoft doporučuje používat účet úložiště pro obecné účely v2 pro většinu scénářů. Účet úložiště pro obecné účely v1 nebo Blob můžete snadno upgradovat na účet pro obecné účely v2 bez prostojů a bez nutnosti kopírování dat.
>
> Další informace o upgradu na účet pro obecné účely v2 najdete v [tématu Upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md).

Účty úložiště pro obecné účely v2 nabízejí více úrovní přístupu pro ukládání dat na základě vašich vzorců využití. Další informace najdete [v tématu Vrstvy Accessu pro data objektů blob bloku](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Účty pro obecné účely verze 1

Účty úložiště pro obecné účely v1 poskytují přístup ke všem službám Azure Storage, ale nemusí mít nejnovější funkce nebo nejnižší ceny za gigabajt. Účty úložiště pro obecné účely v1 podporují tyto služby úložiště Azure:

- Objekty BLOB (všechny typy)
- Soubory
- Disky
- Fronty
- Tabulky

Ve většině případů byste měli používat účty pro obecné účely v2. Účty pro obecné účely v1 můžete použít pro tyto scénáře:

- Vaše aplikace vyžadují klasický model nasazení Azure. Účty pro obecné účely v2 a účty úložiště objektů Blob podporují jenom model nasazení Azure Resource Manageru.

- Vaše aplikace jsou náročné na transakce nebo používají významnou šířku pásma geografické replikace, ale nevyžadují velkou kapacitu. V tomto případě může být nejekonomičtější volbou pro všeobecné použití v1.

- Můžete použít verzi [rozhraní REST API služby Storage Services,](https://msdn.microsoft.com/library/azure/dd894041.aspx) která je starší než 2014-02-14 nebo klientská knihovna s verzí nižší než 4.x. Aplikaci nelze upgradovat.

### <a name="blockblobstorage-accounts"></a>Účty BlockBlobStorage

Účet BlockBlobStorage je účet specializovaného úložiště v úrovni výkonu premium pro ukládání nestrukturovaných objektových dat jako objektů BLOB bloku nebo připojovacích objektů BLOB. Ve srovnání s účty v2 a BlobStorage pro obecné účely poskytují účty BlockBlobStorage nízkou, konzistentní latenci a vyšší transakční sazby.

Účty BlockBlobStorage momentálně nepodporují vrstvení na horké, studené nebo archivní úrovně přístupu. Tento typ účtu úložiště nepodporuje objekty BLOB stránky, tabulky nebo fronty.

### <a name="filestorage-accounts"></a>Účty FileStorage

Účet FileStorage je specializovaný účet úložiště používaný k ukládání a vytváření sdílených složek souborů premium. Tento druh účtu úložiště podporuje soubory, ale ne blokovat objekty BLOB, připojit objekty BLOB, objekty BLOB stránky, tabulky nebo fronty.

FileStorage účty nabízejí jedinečný výkon vyhrazené vlastnosti, jako je například vipos prasknutí. Další informace o těchto charakteristikách najdete v části [Vrstvy úložiště sdílení souborů](../files/storage-files-planning.md#storage-tiers) v průvodci plánováním souborů.

## <a name="naming-storage-accounts"></a>Pojmenování účtů úložiště

Při pojmenování účtu úložiště mějte na paměti tato pravidla:

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Váš název účtu úložiště musí být jedinečný v rámci Azure. Žádné dva účty úložiště nemohou mít stejný název.

## <a name="performance-tiers"></a>Úrovně výkonu

V závislosti na typu účtu úložiště, který vytvoříte, si můžete vybrat mezi úrovněmi standardního a prémiového výkonu.

### <a name="general-purpose-storage-accounts"></a>Účty úložiště pro obecné účely

Účty úložiště pro obecné účely mohou být nakonfigurovány pro jednu z následujících úrovní výkonu:

- Standardní úroveň výkonu pro ukládání objektů BLOB, souborů, tabulek, front a disků virtuálních počítačů Azure. Další informace o cílech škálovatelnosti pro účty standardního úložiště naleznete [v tématu Škálovatelnost cíle pro účty standardníúložiště](scalability-targets-standard-account.md).
- Úroveň výkonu premium pro ukládání nespravovaných disků virtuálních počítačů. Společnost Microsoft doporučuje používat spravované disky s virtuálními počítači Azure namísto nespravovaných disků. Další informace o cílech škálovatelnosti pro úroveň výkonu úrovně výkonu úrovně premium, najdete v [tématu škálovatelnost cíle pro účty úložiště objektů blob stránky premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Účty úložiště BlockBlobStorage

Účty úložiště BlockBlobStorage poskytují úroveň výkonu premium pro ukládání objektů BLOB bloku a připojit objekty BLOB. Další informace najdete v tématu [škálovatelnost cíle pro účty úložiště objektů blob bloku premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Účty úložiště Úložiště souborů

Účty úložiště úložiště filestorage poskytují úroveň výkonu premium pro sdílené složky Azure. Další informace najdete [v tématu Azure Files scalability and performance targets](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Přístup vrstvy pro data objektu blob bloku

Azure Storage poskytuje různé možnosti pro přístup k datům objektů blob bloku na základě vzorců využití. Každá úroveň přístupu ve Službě Azure Storage je optimalizovaná pro konkrétní vzor využití dat. Výběrem správné úrovně přístupu pro vaše potřeby můžete data objektů blob bloku ukládat nákladově nejefektivnějším způsobem.

Dostupné úrovně přístupu jsou:

- Úroveň **přístupu Hot.** Tato úroveň je optimalizována pro častý přístup k objektům v účtu úložiště. Přístup k datům v horké vrstvě je nákladově nejefektivnější, zatímco náklady na úložiště jsou vyšší. Ve výchozím nastavení se v horké vrstvě vytvářejí nové účty úložiště.
- Úroveň **přístupu Cool.** Tato úroveň je optimalizována pro ukládání velkého množství dat, ke kterým se zřídka přistupuje a ukládají po dobu nejméně 30 dnů. Ukládání dat v chladné vrstvě je nákladově efektivnější, ale přístup k těmto datům může být dražší než přístup k datům v horké vrstvě.
- **Archivní** úroveň. Tato úroveň je k dispozici pouze pro objekty BLOB s jednotlivými bloky. Archivní vrstva je optimalizována pro data, která mohou tolerovat několik hodin latence načítání a která zůstanou v archivní vrstvě po dobu nejméně 180 dnů. Úroveň archivu je nákladově nejefektivnější možností pro ukládání dat. Přístup k těmto datům je však dražší než přístup k datům v horké nebo studené vrstvy.

Pokud dojde ke změně způsobu využití dat, můžete mezi těmito úrovněmi přístupu kdykoli přepínat. Další informace o úrovních přístupu najdete v [tématu Úložiště objektů blob Azure: horké, studené a archivní úrovně přístupu](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Změna úrovně přístupu pro existující účet úložiště nebo objekt blob může mít za následek další poplatky. Další informace najdete v [části Fakturace účtu úložiště](#storage-account-billing).

## <a name="redundancy"></a>Redundance

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Šifrování

Všechna data v účtu úložiště jsou šifrována na straně služby. Další informace o šifrování najdete v [tématu Šifrování služby Azure Storage Service pro data v klidovém stavu](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Koncové body účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který uložíte ve službě Azure Storage, má adresu, která obsahuje váš jedinečný název účtu. Kombinace názvu účtu a koncového bodu služby Azure Storage tvoří koncové body pro váš účet úložiště.

Například pokud váš účet úložiště pro obecné účely se nazývá *mystorageaccount*, pak výchozí koncové body pro tento účet jsou:

- Úložiště objektů blob:`https://*mystorageaccount*.blob.core.windows.net`
- Úložiště stolu:`https://*mystorageaccount*.table.core.windows.net`
- Úložiště fronty:`https://*mystorageaccount*.queue.core.windows.net`
- Soubory Azure:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Účty úložiště objektů blob bloku a objektů blob zveřejňují pouze koncový bod služby objektů blob.

Vytvořte adresu URL pro přístup k objektu v účtu úložiště připojením umístění objektu v účtu úložiště ke koncovému bodu. Například adresa účtu pro objekty blob může mít tento formát: http://*můj_účet_úložiště*.blob.core.windows.net/*můj_kontejner*/*můj_objekt_blob*.

Účet úložiště můžete také nakonfigurovat tak, aby používal vlastní doménu pro objekty BLOB. Další informace najdete [v tématu Konfigurace vlastního názvu domény pro váš účet Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Řízení přístupu k datům účtu

Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu. Máte kontrolu nad tím, kdo má přístup k vašim datům a jaká oprávnění mají.

Každá žádost poslaná proti vašemu účtu úložiště musí být autorizována. Na úrovni služby musí požadavek obsahovat platnou hlavičku *autorizace.* Konkrétně tato hlavička obsahuje všechny informace potřebné pro službu ověřit požadavek před jeho spuštěním.

Přístup k datům v účtu úložiště můžete udělit pomocí některého z následujících přístupů:

- **Služba Azure Active Directory:** Pomocí přihlašovacích údajů služby Azure Active Directory (Azure AD) ověřte uživatele, skupinu nebo jinou identitu pro přístup k datům objektů blob a fronty. Pokud je ověření identity úspěšné, azure ad vrátí token použít při autorizaci požadavku na úložiště objektů blob Azure nebo fronty úložiště. Další informace najdete [v tématu Ověřování přístupu k Úložišti Azure pomocí Služby Azure Active Directory](storage-auth-aad.md).
- **Autorizace sdíleného klíče:** Pomocí přístupového klíče účtu úložiště vytvořte připojovací řetězec, který vaše aplikace používá za běhu pro přístup k Azure Storage. Hodnoty v připojovacím řetězci se používají k vytvoření hlavičky *autorizace,* která je předána do úložiště Azure. Další informace najdete [v tématu Konfigurace připojovacích řetězců služby Azure Storage](storage-configure-connection-string.md).
- **Sdílený přístupový podpis:** Pokud nepoužíváte autorizaci Azure AD, použijte sdílený přístupový podpis k delegování přístupu k prostředkům ve vašem účtu úložiště. Sdílený přístupový podpis je token, který zapouzdřuje všechny informace potřebné k autorizaci požadavku na Azure Storage na adrese URL. Můžete určit prostředek úložiště, udělená oprávnění a interval, ve kterém jsou oprávnění platná jako součást sdíleného přístupového podpisu. Další informace naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](storage-sas-overview.md)

> [!NOTE]
> Ověřování uživatelů nebo aplikací pomocí přihlašovacích údajů Azure AD poskytuje vynikající zabezpečení a snadné použití oproti jiným prostředkům autorizace. Zatímco můžete pokračovat v používání autorizace sdíleného klíče s vašimi aplikacemi, pomocí Azure AD obchází potřebu ukládat přístupový klíč účtu s vaším kódem. Můžete také nadále používat sdílené přístupové podpisy (SAS) k udělení jemně odstupňovaného přístupu k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo se obávat zrušení ohrožené SAS.
>
> Společnost Microsoft doporučuje používat autorizaci Azure AD pro vaše aplikace blob azure storage a fronty, pokud je to možné.

## <a name="copying-data-into-a-storage-account"></a>Kopírování dat do účtu úložiště

Společnost Microsoft poskytuje nástroje a knihovny pro import dat z místních úložných zařízení nebo poskytovatelů cloudových úložišť jiných výrobců. Které řešení používáte, závisí na množství přenášených dat.

Když upgradujete na účet pro obecné účely v2 z účtu úložiště pro obecné účely v1 nebo Blob, vaše data se automaticky migrují. Společnost Microsoft doporučuje tuto cestu pro upgrade účtu. Pokud se ale rozhodnete přesunout data z účtu pro obecné účely v1 do účtu úložiště objektů Blob, budete data migrovat ručně pomocí níže popsaných nástrojů a knihoven.

### <a name="azcopy"></a>AzCopy

AzCopy je nástroj Windows, který se spouští z příkazového řádku a který je určený pro vysoce výkonné kopírování dat do Azure Storage a z Azure Storage. AzCopy můžete použít ke kopírování dat do účtu úložiště objektů Blob z existujícího účtu úložiště pro obecné účely nebo k nahrání dat z místních úložných zařízení. Další informace najdete v tématu [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Knihovna pro přesun dat

Knihovna pro přesun dat v Azure Storage pro .NET je založená na základní platformě pro přesun dat, kterou využívá nástroj AzCopy. Knihovna je určená pro vysoce výkonné, spolehlivé a snadné operace přenosu dat, podobně jako AzCopy. Knihovnu přesunů dat můžete použít k tomu, abyste mohli nativně využívat funkce AzCopy. Další informace najdete v tématu [Azure Storage Data Movement Library for .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>Rozhraní REST API nebo klientská knihovna

Můžete vytvořit vlastní aplikaci pro migraci dat z účtu úložiště pro obecné účely v1 do účtu úložiště objektů Blob. Použijte jednu z klientských knihoven Azure nebo rozhraní REST API služby Azure storage Services. Azure Storage poskytuje množství knihoven klienta pro různé jazyky a platformy, jako například .NET, Java, C++, Node.JS, PHP, Ruby nebo Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Další informace o rozhraní API Azure Storage REST najdete v [tématu Azure Storage Services REST API Reference](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Objekty blob šifrované na straně klienta ukládají metadata šifrování s objektem blob. Pokud kopírujete objekt blob šifrovaný na straně klienta, ujistěte se, že operace kopírování zachová metadata objektu blob, zejména metadata šifrování. Pokud objekt blob zkopírujete bez metadat šifrování, obsah objektu blob bude nenávratně ztracený. Další informace o metadatech šifrování najdete v tématu [Azure Storage a šifrování na straně klienta](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fakturace účtu úložiště

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](storage-account-create.md)
- [Vytvoření účtu úložiště objektů blob bloku](../blobs/storage-blob-create-account-block-blob.md)
