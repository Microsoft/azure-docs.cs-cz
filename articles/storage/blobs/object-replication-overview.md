---
title: Přehled replikace objektů
titleSuffix: Azure Storage
description: Replikace objektů asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Replikaci objektů můžete použít k minimalizaci latence při čtení požadavků, ke zvýšení efektivity výpočetních úloh, k optimalizaci distribuce dat a k minimalizaci nákladů.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 391c33e72f45e7c0c0b56128b32a8e73399e417a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834319"
---
# <a name="object-replication-for-block-blobs"></a>Replikace objektů pro objekty blob bloku

Replikace objektů asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Mezi scénáře podporované replikací objektů patří:

- **Minimalizace latence.** Replikace objektů může snížit latenci pro požadavky na čtení tím, že klientům umožní využívat data z oblasti, která je blíže fyzické blízkosti.
- **Zvyšte efektivitu výpočetních úloh.** Díky replikaci objektů můžou výpočetní úlohy zpracovat stejné sady objektů blob bloku v různých oblastech.
- **Optimalizace distribuce dat.** Můžete zpracovávat nebo analyzovat data v jednom umístění a potom replikovat pouze výsledky do dalších oblastí.
- **Optimalizace nákladů.** Po dokončení replikace dat můžete snížit náklady přesunutím na úroveň archivu pomocí zásad správy životního cyklu.

Následující diagram znázorňuje, jak replikace objektů replikuje objekty blob bloku z účtu zdrojového úložiště v jedné oblasti do cílových účtů ve dvou různých oblastech.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram znázorňující, jak funguje replikace objektů":::

Informace o tom, jak nakonfigurovat replikaci objektů, najdete v tématu [Konfigurace replikace objektů](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Požadavky na replikaci objektů

Replikace objektů vyžaduje, aby byly také povoleny následující funkce Azure Storage:

- [Změna kanálu](storage-blob-change-feed.md): musí být povolená ve zdrojovém účtu. Informace o tom, jak povolit kanál změn, najdete v tématu [povolení a zakázání kanálu změn](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Správa verzí objektů BLOB](versioning-overview.md): musí být povolená jak na zdrojovém, tak na cílovém účtu. Informace o tom, jak povolit správu verzí, najdete v tématu [povolení a správa verzí objektů BLOB](versioning-enable.md).

Povolení změny kanálu a správy verzí objektů BLOB může mít za následek další náklady. Další podrobnosti najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Replikace objektů je podporovaná jenom pro účty úložiště pro obecné účely v2. Zdrojové i cílové účty musí být pro obecné účely v2. 

## <a name="how-object-replication-works"></a>Jak funguje replikace objektů

Replikace objektů asynchronně kopíruje objekty blob bloku v kontejneru podle pravidel, která nakonfigurujete. Obsah objektu blob, všechny verze přidružené k objektu BLOB a metadata a vlastnosti objektu BLOB se zkopírují ze zdrojového kontejneru do cílového kontejneru.

> [!IMPORTANT]
> Vzhledem k tomu, že jsou data objektů blob bloku replikována asynchronně, není zdrojový účet a cílový účet hned synchronizován. V tuto chvíli není k dispozici žádná smlouva SLA, jak dlouho trvá replikace dat do cílového účtu. Stav replikace zdrojového objektu blob můžete zjistit, abyste zjistili, jestli je replikace dokončená. Další informace najdete v tématu o [kontrole stavu replikace objektu BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Správa verzí objektů BLOB

Replikace objektů vyžaduje, aby byla ve zdrojovém i cílovém účtu povolená Správa verzí objektů BLOB. Když se upraví replikovaný objekt BLOB ve zdrojovém účtu, vytvoří se ve zdrojovém účtu nová verze objektu blob, která odráží předchozí stav objektu BLOB před úpravou. Aktuální verze (nebo základní objekt BLOB) ve zdrojovém účtu odráží nejnovější aktualizace. Aktualizovaná aktuální verze i nová předchozí verze jsou replikovány do cílového účtu. Další informace o tom, jak operace zápisu ovlivňují verze objektů blob, najdete v tématu [Správa verzí při operacích zápisu](versioning-overview.md#versioning-on-write-operations).

Když je objekt BLOB ve zdrojovém účtu odstraněný, aktuální verze objektu BLOB se zachytí v předchozí verzi a pak se odstraní. Všechny předchozí verze objektu BLOB zůstanou zachované i po odstranění aktuální verze. Tento stav je replikován do cílového účtu. Další informace o tom, jak operace odstranění ovlivňují verze objektů blob, najdete v tématu [Správa verzí při operacích odstranění](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Snímky

Replikace objektů nepodporuje snímky objektů BLOB. Žádné snímky v objektu BLOB ve zdrojovém účtu se nereplikují do cílového účtu.

### <a name="blob-tiering"></a>Vrstvení objektů BLOB

Replikace objektů je podporovaná, když jsou zdrojové a cílové účty na horké nebo studené úrovni. Zdrojové a cílové účty můžou být v různých úrovních. Replikace objektů ale selže, pokud se objekt BLOB v buď zdrojovém nebo cílovém účtu přesunul do archivní úrovně. Další informace o úrovních objektů BLOB najdete v tématu [úrovně přístupu pro Azure Blob Storage – horká, studená a archivní](storage-blob-storage-tiers.md).

### <a name="immutable-blobs"></a>Neměnné objekty blob

Replikace objektů nepodporuje neměnné objekty blob. Pokud má zdrojový nebo cílový kontejner zásady uchovávání informací na základě času nebo právní blokování, replikace objektu se nezdařila. Další informace o neměnných objektech blob najdete v tématu [ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm](storage-blob-immutable-storage.md).

## <a name="object-replication-policies-and-rules"></a>Zásady a pravidla replikace objektů

Když konfigurujete replikaci objektů, vytvoříte zásadu replikace, která určuje zdrojový a cílový účet úložiště. Zásada replikace obsahuje jedno nebo více pravidel, která určují zdrojový kontejner a cílový kontejner, a označuje, které objekty blob bloku ve zdrojovém kontejneru budou replikovány.

Po nakonfigurování replikace objektů Azure Storage pravidelně kontrolovat kanál změny pro zdrojový účet a asynchronně replikuje všechny operace zápisu nebo odstranění do cílového účtu. Latence replikace závisí na velikosti replikované objekty blob bloku.

### <a name="replication-policies"></a>Zásady replikace

Při konfiguraci replikace objektů se na zdrojovém účtu a cílovém účtu vytvoří zásada replikace prostřednictvím poskytovatele prostředků Azure Storage. Zásady replikace se identifikují podle ID zásad. Aby mohla replikace probíhat, musí mít zásady na zdrojovém a cílovém účtu stejné ID zásad.

Účet úložiště může sloužit jako zdrojový účet pro až dva cílové účty. Zdrojové a cílové účty můžou být ve stejné oblasti nebo v různých oblastech. Můžou se také nacházet v různých předplatných a v různých klientech Azure Active Directory (Azure AD). Pro každou dvojici zdrojového účtu nebo cílového účtu se dá vytvořit jenom jedna zásada replikace.

### <a name="replication-rules"></a>Pravidla replikace

Pravidla replikace určují, jak bude Azure Storage replikovat objekty blob ze zdrojového kontejneru do cílového kontejneru. Pro každou zásadu replikace můžete zadat až 10 pravidel replikace. Každé pravidlo replikace definuje jeden zdrojový a cílový kontejner a každý zdrojový a cílový kontejner se dá použít jenom v jednom pravidle.

Když vytvoříte pravidlo replikace, zkopírují se ve výchozím nastavení jenom nové objekty blob bloku, které jsou následně přidané do zdrojového kontejneru. Můžete určit, že se zkopírují nové i existující objekty blob bloku, nebo můžete definovat vlastní obor kopírování, který kopíruje objekty blob bloku vytvořené ze zadaného času.

Můžete také zadat jeden nebo více filtrů jako součást pravidla replikace pro filtrování objektů blob bloku podle předpony. Když zadáte předponu, zkopírují se do cílového kontejneru jenom objekty blob odpovídající této předponě ve zdrojovém kontejneru.

Zdrojové a cílové kontejnery musí existovat, aby bylo možné je zadat v pravidle. Po vytvoření zásady replikace se operace zápisu do cílového kontejneru nepovolují. Jakékoli pokusy o zápis do cílového kontejneru selžou s kódem chyby 409 (Konflikt). Pokud chcete zapisovat do cílového kontejneru, pro který je nakonfigurované pravidlo replikace, musíte buď odstranit pravidlo, které je pro tento kontejner nakonfigurované, nebo odebrat zásady replikace. Operace čtení a odstranění do cílového kontejneru jsou povolené, když jsou zásady replikace aktivní.

Můžete zavolat operaci [nastavit vrstvu objektů BLOB](/rest/api/storageservices/set-blob-tier) u objektu BLOB v cílovém kontejneru a přesunout ho do archivní úrovně. Další informace o archivní úrovni najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="replication-status"></a>Stav replikace

Můžete kontrolovat stav replikace objektu BLOB ve zdrojovém účtu. Další informace najdete v tématu o [kontrole stavu replikace objektu BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

Pokud stav replikace objektu BLOB ve zdrojovém účtu indikuje chybu, prozkoumejte následující možné příčiny:

- Ujistěte se, že jsou v cílovém účtu nakonfigurované zásady replikace objektů.
- Ověřte, zda cílový kontejner stále existuje.
- Pokud byl zdrojový objekt BLOB v rámci operace zápisu zašifrovaný pomocí klíče poskytnutého zákazníkem, replikace objektu se nezdaří. Další informace o klíčích poskytovaných zákazníkem najdete v tématu [poskytnutí šifrovacího klíče pro požadavek na úložiště objektů BLOB](encryption-customer-provided-keys.md).

## <a name="billing"></a>Fakturace

Replikace objektů má za následek dodatečné náklady na transakce čtení a zápisu proti zdrojovému a cílovému účtu, jakož i poplatky za přenos dat ze zdrojového účtu do cílového účtu a poplatky za čtení za účelem zpracování kanálu změn.

## <a name="next-steps"></a>Další kroky

- [Konfigurace replikace objektů](object-replication-configure.md)
- [Změna podpory kanálu v Azure Blob Storage](storage-blob-change-feed.md)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
