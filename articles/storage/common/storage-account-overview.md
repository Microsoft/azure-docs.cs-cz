---
title: Přehled účtu Azure Storage | Microsoft Docs
description: Seznamte se s možnostmi pro vytvoření a použití účtu Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 308809a9b78de9d3c0e77ed6028e62c42ff4e1c5
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882564"
---
# <a name="azure-storage-account-overview"></a>Přehled účtu Azure Storage

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Data v účtu úložiště Azure jsou trvalá a vysoce dostupná, zabezpečená a rozsáhlá.

Informace o tom, jak vytvořit účet úložiště Azure, najdete v tématu [Vytvoření účtu úložiště](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Účty úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporují nejnovější funkce Azure Storage a zahrnují všechny funkce účtů pro obecné účely V1 a BLOB Storage. Účty pro obecné účely v2 poskytují nejnižší cenu za GB pro Azure Storage a také ceny za transakce z odvětví konkurenčních produktů. Účty úložiště pro obecné účely v2 podporují tyto služby Azure Storage:

- Objekty BLOB (všechny typy: blokovat, připojit, stránka)
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

I když jsou ve většině případů doporučovány účty pro obecné účely verze 2, účty pro obecné účely V1 se nejlépe hodí pro tyto scénáře:

* Vaše aplikace vyžadují model nasazení Azure Classic. Účty pro obecné účely v2 a účty BLOB Storage podporují jenom model nasazení Azure Resource Manager.

* Vaše aplikace jsou náročné na transakce nebo využívají značnou šířku pásma geografické replikace, ale nevyžadují velkou kapacitu. V tomto případě může být obecným účelem v1 největší ekonomická volba.

* Používáte verzi rozhraní [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx), která je starší než 14.2.2014, nebo klientskou knihovnu verze nižší než 4.x a nemůžete svoji aplikaci upgradovat.

### <a name="blockblobstorage-accounts"></a>Účty BlockBlobStorage

Účet BlockBlobStorage je specializovaný účet úložiště, který slouží k ukládání nestrukturovaných dat objektu jako objektů blob bloku a k vytvoření objektů blob bloku úrovně Premium. Tento druh účtu úložiště podporuje objekty blob bloku a doplňovací objekty blob, ale ne objekty blob stránky, tabulky nebo fronty.

V porovnání s účty pro obecné účely v2 a BlobStorage účty BlockBlobStorage poskytují nízkou a konzistentní latenci a vyšší sazby za transakce.

Účty BlockBlobStorage v současné době nepodporují vrstvy přístupu na horkou, studenou nebo archivní úroveň.

### <a name="filestorage-accounts"></a>Účty úložiště

Účet úložiště souborů je specializovaný účet úložiště, který slouží k ukládání a vytváření sdílených složek Premium. Tento druh účtu úložiště podporuje soubory, ale ne objekty blob bloku, doplňovací objekty blob, objekty blob stránky, tabulky nebo fronty. 

Účty úložiště obsahují jedinečné vyhrazené charakteristiky výkonu, jako je třeba shlukování IOPS. Další informace o těchto vlastnostech najdete v části [výkonnostní vrstvy sdílení souborů](../files/storage-files-planning.md#file-share-performance-tiers) v příručce pro plánování souborů.

## <a name="naming-storage-accounts"></a>Pojmenování účtů úložiště

Při pojmenování účtu úložiště mějte na paměti tato pravidla:

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Název vašeho účtu úložiště musí být jedinečný v rámci Azure. Žádné dva účty úložiště nemůžou mít stejný název.

## <a name="performance-tiers"></a>Úrovně výkonu

Účty úložiště pro obecné účely můžou být nakonfigurované pro jednu z následujících úrovní výkonu:

* Standardní úroveň výkonu pro ukládání objektů blob, souborů, tabulek, front a disků virtuálních počítačů Azure.
* Úroveň výkonu Premium pro ukládání pouze nespravovaných disků virtuálního počítače.

Účty úložiště BlockBlobStorage poskytují úroveň výkonu Premium pro ukládání objektů blob bloku a doplňovací objektů BLOB.

Účty úložiště úložiště obsahují úroveň výkonu Premium pro sdílené složky Azure.

## <a name="access-tiers-for-block-blob-data"></a>Úrovně přístupu pro data objektů blob bloku

Azure Storage poskytuje různé možnosti pro přístup k datům objektů blob bloku založených na vzorech použití. Každá úroveň přístupu v Azure Storage je optimalizovaná pro konkrétní vzor využití dat. Výběrem správné úrovně přístupu pro vaše potřeby můžete ukládat data objektů blob bloku v nejefektivnějším úsporném režimu.

Dostupné úrovně přístupu jsou:

* Vrstva **Hot** Access, která je optimalizována pro častější přístup k objektům v účtu úložiště. Přístup k datům v Hot úrovně je největší nákladově efektivní, zatímco náklady na úložiště jsou vyšší. Ve výchozím nastavení se v aktivní úrovni vytvoří nové účty úložiště.
* **Studená** úroveň přístupu, která je optimalizovaná pro ukládání velkých objemů dat, která se zřídka používají a ukládají se aspoň na 30 dní. Ukládání dat ve studené vrstvě je cenově výhodnější, ale přístup k nim může být dražší než přístup k datům v horké vrstvě.
* **Archivní** vrstva, která je k dispozici pouze pro jednotlivé objekty blob bloku. Archivní úroveň je optimalizovaná pro data, která můžou tolerovat několik hodin latence načítání a zůstane v archivní úrovni aspoň 180 dní. Archivní úroveň je nejefektivnější možností pro ukládání dat, ale přístup k těmto datům je dražší než přístup k datům na horké nebo studené úrovni.

Pokud dojde ke změně ve vzoru používání vašich dat, můžete kdykoli přepínat mezi těmito úrovněmi přístupu. Další informace o úrovních přístupu najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Změna úrovně přístupu pro existující účet úložiště nebo objekt BLOB může mít za následek další poplatky. Další informace najdete v [části fakturace účtu úložiště](#storage-account-billing).

## <a name="replication"></a>Replikace

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Další informace o replikaci úložiště najdete v tématu [replikace Azure Storage](storage-redundancy.md).

## <a name="encryption"></a>Šifrování

Všechna data v účtu úložiště se šifrují na straně služby. Další informace o šifrování najdete v tématu [šifrování služby Azure Storage pro](storage-service-encryption.md)neaktivní neaktivní data.

## <a name="storage-account-endpoints"></a>Koncové body účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který je uložen v Azure Storage má adresu, která obsahuje jedinečný název účtu. Kombinace názvu účtu a koncového bodu služby Azure Storage tvoří koncové body pro váš účet úložiště.

Pokud má například účet úložiště pro obecné účely název *mystorageaccount*, výchozí koncové body tohoto účtu jsou následující:

* BLOB Storage: http://*mystorageaccount*. blob.Core.Windows.NET
* Table Storage: http://*mystorageaccount*. Table.Core.Windows.NET
* Queue Storage: http://*mystorageaccount*. Queue.Core.Windows.NET
* Soubory Azure: http://*mystorageaccount*. File.Core.Windows.NET

> [!NOTE]
> Účty objektů blob bloku a BLOB Storage zpřístupňují jenom koncový bod služby BLOB Service.

Adresa URL pro přístup k objektu v účtu úložiště je vytvořená připojením umístění objektu v účtu úložiště ke koncovému bodu. Například adresa účtu pro objekty blob může mít tento formát: http://*můj_účet_úložiště*.blob.core.windows.net/*můj_kontejner*/*můj_objekt_blob*.

Svůj účet úložiště můžete také nakonfigurovat tak, aby používal vlastní doménu pro objekty blob. Další informace najdete v tématu [Konfigurace vlastního názvu domény pro účet Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Řízení přístupu k datům účtu

Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu. Máte kontrolu nad tím, kdo může mít přístup k vašim datům a jaká oprávnění mají.

Každý požadavek vytvořený na váš účet úložiště musí být autorizovaný. Na úrovni služby musí požadavek zahrnovat platnou *autorizační* hlavičku, která obsahuje všechny informace potřebné k tomu, aby služba před spuštěním ověřila požadavek.

Přístup k datům ve vašem účtu úložiště můžete udělit pomocí některého z následujících přístupů:

- **Azure Active Directory:** Použijte přihlašovací údaje pro Azure Active Directory (Azure AD) k ověření uživatele, skupiny nebo jiné identity pro přístup k datům objektů BLOB a front. Pokud je ověření identity úspěšné, pak Azure AD vrátí token, který se použije při autorizaci žádosti do úložiště objektů BLOB v Azure nebo ve frontě. Další informace najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).
- **Autorizace sdíleného klíče:** Použijte přístupový klíč účtu úložiště k vytvoření připojovacího řetězce, který vaše aplikace používá za běhu pro přístup k Azure Storage. Hodnoty v připojovacím řetězci slouží k vytvoření hlavičky *autorizace* , která je předána Azure Storage. Další informace najdete v tématu [Konfigurace připojovacích řetězců Azure Storage](storage-configure-connection-string.md).
- **Sdílený přístupový podpis:** Pomocí sdíleného přístupového podpisu můžete delegovat přístup k prostředkům v účtu úložiště, pokud nepoužíváte autorizaci Azure AD. Sdílený přístupový podpis je token, který zapouzdřuje všechny informace potřebné k autorizaci žádosti o Azure Storage na adrese URL. Můžete zadat prostředek úložiště, udělená oprávnění a interval, ve kterém jsou oprávnění platná, jako součást sdíleného přístupového podpisu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

> [!NOTE]
> Ověřování uživatelů nebo aplikací pomocí přihlašovacích údajů Azure AD zajišťuje zabezpečení a jednoduchost použití prostřednictvím jiných způsobů autorizace. I když můžete nadále používat autorizaci pomocí sdíleného klíče u svých aplikací, použití Azure AD obchází nutnost uložení přístupového klíče účtu k vašemu kódu. Můžete taky dál používat sdílené přístupové podpisy (SAS) pro udělení jemně odstupňovaného přístupu k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné možnosti, aniž by bylo potřeba spravovat tokeny SAS nebo se starat o odvolání napadeného SAS. 
>
> Microsoft doporučuje použít autorizaci Azure AD pro Azure Storage objekty BLOB a aplikace fronty, pokud je to možné.

## <a name="copying-data-into-a-storage-account"></a>Kopírování dat do účtu úložiště

Microsoft poskytuje nástroje a knihovny pro import dat z místních úložných zařízení nebo poskytovatelů cloudového úložiště třetích stran. Které řešení použijete, závisí na množství přenášených dat. 

Při upgradu na účet pro obecné účely v2 z obecného účtu úložiště v1 nebo BLOB se data automaticky migrují. Microsoft doporučuje tuto stáži k upgradu vašeho účtu. Pokud se ale rozhodnete přesunout data z účtu pro obecné účely V1 do účtu úložiště BLOB, budete muset data migrovat ručně pomocí nástrojů a knihoven popsaných níže. 

### <a name="azcopy"></a>AzCopy

AzCopy je nástroj Windows, který se spouští z příkazového řádku a který je určený pro vysoce výkonné kopírování dat do Azure Storage a z Azure Storage. Pomocí AzCopy můžete kopírovat data do účtu BLOB Storage z existujícího účtu úložiště pro obecné účely nebo nahrávat data z místních úložných zařízení. Další informace najdete v tématu [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Knihovna pro přesun dat

Knihovna pro přesun dat v Azure Storage pro .NET je založená na základní platformě pro přesun dat, kterou využívá nástroj AzCopy. Knihovna je určená pro vysoce výkonné, spolehlivé a snadné operace přenosu dat, podobně jako AzCopy. Pomocí této knihovny můžete využívat výhody funkcí AzCopy ve svojí aplikaci nativně, bez nutnosti spouštět a sledovat externí instance nástroje AzCopy. Další informace najdete v tématu [Knihovna pro přesun dat v Azure Storage pro .Net](https://github.com/Azure/azure-storage-net-data-movement).

### <a name="rest-api-or-client-library"></a>Rozhraní REST API nebo klientská knihovna

Pomocí některé z knihoven klienta Azure nebo rozhraní REST API služeb úložiště Azure můžete vytvořit vlastní aplikaci pro migraci dat do účtu úložiště Blob. Azure Storage poskytuje množství knihoven klienta pro různé jazyky a platformy, jako například .NET, Java, C++, Node.JS, PHP, Ruby nebo Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Další informace o REST API Azure Storage najdete v tématu informace o [REST API služby Azure Storage Services](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Objekty blob šifrované na straně klienta ukládají metadata šifrování s objektem blob. Pokud kopírujete objekt blob šifrovaný na straně klienta, ujistěte se, že operace kopírování zachová metadata objektu blob, zejména metadata šifrování. Pokud objekt blob zkopírujete bez metadat šifrování, obsah objektu blob bude nenávratně ztracený. Další informace o metadatech šifrování najdete v tématu [Azure Storage a šifrování na straně klienta](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Služba import/export Azure

Pokud pro import do svého účtu úložiště máte velké množství dat, zvažte službu Azure import/export. Služba import/export slouží k bezpečnému importování velkých objemů dat do služby Azure Blob Storage a souborů Azure prostřednictvím přenosů diskových jednotek do datacentra Azure. 

Služba import/export se dá použít taky k přenosu dat z úložiště objektů BLOB v Azure na diskové jednotky a k odeslání do vašich místních webů. Data z jedné nebo více diskových jednotek můžete importovat buď do služby Azure Blob Storage, nebo do souborů Azure. Další informace najdete v tématu [co je služba Azure import/export?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Fakturace účtu úložiště

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit účet úložiště Azure pro obecné účely, najdete v tématu [Vytvoření účtu úložiště](storage-quickstart-create-account.md).
* Pokud se chcete dozvědět, jak vytvořit účet BlockBlobStorage, přečtěte si téma [Vytvoření účtu úložiště objektů blob bloku](../blobs/storage-blob-create-account-block-blob.md).
* Pokud chcete spravovat nebo odstranit existující účet úložiště, přečtěte si téma [Správa účtů Azure Storage](storage-account-manage.md).
