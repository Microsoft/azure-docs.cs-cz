---
title: Zvolte řešení Azure pro přenos dat | Dokumentace Microsoftu
description: Zjistěte, jak zvolit řešení Azure pro přenos dat na základě velikosti dat a dostupnou šířku pásma sítě ve vašem prostředí
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: b90e84082eb73dde5a2303f166e9aa254c6f3101
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263815"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Zvolte řešení Azure pro přenos dat

Tento článek obsahuje přehled některých běžných přenos dat do Azure řešení. Článek taky obsahuje odkazy na doporučené možnosti v závislosti na šířku pásma sítě ve vašem prostředí a množství dat, kterou chcete převést.

## <a name="types-of-data-movement"></a>Typy přesun dat

Přenos dat může být offline nebo přes síťové připojení. Vyberte řešení v závislosti na vaší:

- **Velikost dat** – velikost dat určená pro přenos,
- **Frekvence přenosu** -jednorázové nebo pravidelné ingestování a
- **Síť** – šířka pásma dostupná pro data přenosu ve vašem prostředí.

Přesouvání dat může být z následujících typů:

- **Offline převod pomocí expedovatelném stavu zařízení** – použití fyzických expedovatelném stavu zařízení, pokud chcete provést offline jednorázově datové přenosy. Microsoft vám pošle na disk nebo zabezpečené specializované zařízení. Alternativně můžete zakoupit a odeslání vlastní disky. Kopírování dat do zařízení a potom ho odešlete do Azure kterého se nahrávají data.  Dostupné možnosti pro tento případ jsou Data Box Disk, zařízení Data Box, Heavy pole Data a Import/Export (použít vlastní disky).

- **Síťové přenosy** – přenos dat do Azure přes síťové připojení. To můžete udělat mnoha způsoby.

    - **Grafické rozhraní** – Pokud čas od času přenos několika souborů a není potřeba automatizovat přenos dat, můžete vybrat nástroje grafického rozhraní, jako je Průzkumník služby Azure Storage nebo nástroj pro zkoumání založeného na webu na webu Azure portal.
    - **Skriptované nebo programové přenos** – optimalizované softwarové nástroje, které poskytují nebo přímo volat naše rozhraní API REST/SDK můžete použít. Jsou k dispozici skriptovatelný nástroje AzCopy, Azure Powershellu a rozhraní příkazového řádku Azure. Pro programové rozhraní použijte jednu ze sad SDK pro .NET, Java, Python, uzlu/JS, C++, Go, PHP nebo Ruby.
    - **Místní zařízení** – My dodáme fyzický nebo virtuální zařízení, které se nachází ve vašem datovém centru a optimalizuje přenos dat přes síť. Tato zařízení také poskytují místní mezipaměti často používaných souborů. Fyzické zařízení je okraj pole Data a virtuální zařízení je brána dat pole. Spustit trvale v místním i připojení k Azure přes síť.
    - **Spravované datové kanály** – můžete nastavit kanál cloudu pravidelně přenášet soubory mezi několik služeb Azure, místní nebo kombinace dvou. Azure Data Factory můžete nastavit a spravovat datové kanály a přesun a transformaci dat pro analýzu.

Následující vizuál znázorňuje pokyny k výběru různých přenos dat do Azure nástroje v závislosti na šířku pásma sítě dostupnou pro přenos, velikost dat určená pro přenosu a četnost přenos.

![Nástroje pro přenos dat Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Horní omezení zařízení offline převodu – Data Box Disk, zařízení Data Box a Data Box náročné je možné rozšířit tak, že více objednávek typu zařízení.*

## <a name="selecting-a-data-transfer-solution"></a>Výběr řešení pro přenos dat

Odpovězte na následující otázky vám pomohou s výběrem řešení pro přenos dat:

- Je vaše šířka pásma sítě k dispozici omezené nebo neexistující a chcete převést velkých datových sad?
  
    Pokud ano, přečtěte si: [Scénář 1: Přenos velkých datových sad bez nebo malou šířku pásma sítě](storage-solution-large-dataset-low-network.md).
- Chcete přes síť přenášet velké datové sady a máte střední pro velká šířka pásma sítě?

    Pokud ano, přečtěte si: [Scénář 2: Přenos velkých datových sad s střední velká šířka pásma sítě](storage-solution-large-dataset-moderate-high-network.md).
- Chcete být čas od času přenos přes síť, několika souborů?

    Pokud ano, přečtěte si téma [scénář 3: Převést malé datové sady s omezením na střední šířka pásma sítě](storage-solution-small-dataset-low-moderate-network.md).
- Hledáte přenos dat v daném okamžiku v pravidelných intervalech?

    Pokud ano, použít skripty programové možnosti uvedené v [scénář 4: Přenosy dat pravidelné](storage-solution-periodic-data-transfer.md).
- Hledáte probíhající, průběžné datové přenosy

    Pokud ano, pomocí možností v [scénář 4: Přenosy dat pravidelné](storage-solution-periodic-data-transfer.md).

## <a name="next-steps"></a>Další postup

- [Úvod k Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Přečtěte si základní informace o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Použití Azure Powershellu s Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Použití Azure CLI s Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Další informace o:

    - [Azure Data Box Azure Data Box Disk a Azure Data Box náročné na offline převody](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box brány a Azure Data Box Edge pro online převody](https://docs.microsoft.com/azure/databox-online/).
- [Zjistěte, co je Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Použití rozhraní REST API pro přenos dat

    - [V rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [V jazyce Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
