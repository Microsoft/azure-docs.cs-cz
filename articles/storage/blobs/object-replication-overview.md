---
title: Přehled replikace objektů
titleSuffix: Azure Storage
description: Replikace objektů asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Replikaci objektů můžete použít k minimalizaci latence při čtení požadavků, ke zvýšení efektivity výpočetních úloh, k optimalizaci distribuce dat a k minimalizaci nákladů.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4105698198e6fb7f4e3d3526ff9590ebca4898f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612162"
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

## <a name="object-replication-policies-and-rules"></a>Zásady a pravidla replikace objektů

Když konfigurujete replikaci objektů, vytvoříte zásadu replikace, která určuje zdrojový a cílový účet úložiště. Zásada replikace obsahuje jedno nebo více pravidel, která určují zdrojový kontejner a cílový kontejner, a označuje, které objekty blob bloku ve zdrojovém kontejneru budou replikovány.

Po nakonfigurování replikace objektů Azure Storage pravidelně kontrolovat kanál změny pro zdrojový účet a asynchronně replikuje všechny operace zápisu nebo odstranění do cílového účtu. Latence replikace závisí na velikosti replikované objekty blob bloku.

> [!IMPORTANT]
> Vzhledem k tomu, že jsou data objektů blob bloku replikována asynchronně, není zdrojový účet a cílový účet hned synchronizován. V tuto chvíli není k dispozici žádná smlouva SLA, jak dlouho trvá replikace dat do cílového účtu.

### <a name="replication-policies"></a>Zásady replikace

Při konfiguraci replikace objektů se na zdrojovém účtu a cílovém účtu vytvoří zásada replikace prostřednictvím poskytovatele prostředků Azure Storage. Zásady replikace se identifikují podle ID zásad. Aby mohla replikace probíhat, musí mít zásady na zdrojovém a cílovém účtu stejné ID zásad.

Účet úložiště může sloužit jako zdrojový účet pro až dva cílové účty. Zdrojové a cílové účty můžou být ve stejné oblasti nebo v různých oblastech. Můžou se také nacházet v různých předplatných a v různých klientech Azure Active Directory (Azure AD). Pro každou dvojici zdrojového účtu nebo cílového účtu se dá vytvořit jenom jedna zásada replikace.

### <a name="replication-rules"></a>Pravidla replikace

Pravidla replikace určují, jak bude Azure Storage replikovat objekty blob ze zdrojového kontejneru do cílového kontejneru. Pro každou zásadu replikace můžete zadat až 10 pravidel replikace. Každé pravidlo replikace definuje jeden zdrojový a cílový kontejner a každý zdrojový a cílový kontejner se dá použít jenom v jednom pravidle.

Když vytvoříte pravidlo replikace, zkopírují se ve výchozím nastavení jenom nové objekty blob bloku, které jsou následně přidané do zdrojového kontejneru. Můžete určit, že se zkopírují nové i existující objekty blob bloku, nebo můžete definovat vlastní obor kopírování, který kopíruje objekty blob bloku vytvořené ze zadaného času.

Můžete také zadat jeden nebo více filtrů jako součást pravidla replikace pro filtrování objektů blob bloku podle předpony. Když zadáte předponu, zkopírují se do cílového kontejneru jenom objekty blob odpovídající této předponě ve zdrojovém kontejneru.

Zdrojové a cílové kontejnery musí existovat, aby bylo možné je zadat v pravidle. Po vytvoření zásad replikace bude cílový kontejner jen pro čtení. Jakékoli pokusy o zápis do cílového kontejneru selžou s kódem chyby 409 (Konflikt). Můžete ale zavolat operaci [nastavit vrstvu objektů BLOB](/rest/api/storageservices/set-blob-tier) u objektu BLOB v cílovém kontejneru a přesunout ho do archivní úrovně. Další informace o archivní úrovni najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="billing"></a>Fakturace

Replikace objektů má za následek dodatečné náklady na transakce čtení a zápisu proti zdrojovému a cílovému účtu, jakož i poplatky za přenos dat ze zdrojového účtu do cílového účtu a poplatky za čtení za účelem zpracování kanálu změn.

## <a name="next-steps"></a>Další kroky

- [Konfigurace replikace objektů](object-replication-configure.md)
- [Změna podpory kanálu v Azure Blob Storage](storage-blob-change-feed.md)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
