---
title: Zvolte řešení Azure pro přenos dat| Dokumenty společnosti Microsoft
description: Přečtěte si, jak vybrat řešení Azure pro přenos dat na základě velikosti dat a dostupné šířky pásma sítě ve vašem prostředí.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ada96d0d4b167e30cb6e271aa02d0d399a9ae7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303082"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Výběr řešení Azure pro přenos dat

Tento článek obsahuje přehled některých běžných řešení pro přenos dat Azure. Článek také odkazuje na doporučené možnosti v závislosti na šířce pásma sítě ve vašem prostředí a velikosti dat, která chcete přenést.

## <a name="types-of-data-movement"></a>Typy přesunů dat

Přenos dat může být offline nebo přes síťové připojení. Vyberte si řešení v závislosti na:

- **Velikost dat** - Velikost dat určených k přenosu,
- **Frekvence přenosu** - Jednorázové nebo pravidelné požití dat a
- **Síť** – šířka pásma dostupná pro přenos dat ve vašem prostředí.

Přesun dat může být následujících typů:

- **Offline přenos pomocí přepravitelných zařízení** – Pokud chcete provést jednorázový hromadný přenos dat offline, použijte fyzická přepravitelná zařízení. Společnost Microsoft vám zašle disk nebo zabezpečené specializované zařízení. Případně můžete zakoupit a dobýt vlastní disky. Zkopírujete data do zařízení a pak je odešlete do Azure, kde se data nahrají.  Dostupné možnosti pro tento případ jsou Disk datové schránky, Datová schránka, Velká datová schránka a Import/Export (použijte vlastní disky).

- **Přenos sítě** – data můžete přenést do Azure přes síťové připojení. To lze provést mnoha způsoby.

    - **Grafické rozhraní** – Pokud příležitostně přenášíte jen několik souborů a nepotřebujete automatizovat přenos dat, můžete zvolit nástroj grafického rozhraní, jako je Azure Storage Explorer nebo webový průzkumný nástroj na webu Azure Portal.
    - **Skriptovaný nebo programový přenos** - Můžete použít optimalizované softwarové nástroje, které poskytujeme, nebo volat naše REST API / SDK přímo. Dostupné skriptovatelné nástroje jsou AzCopy, Azure PowerShell a Azure CLI. Pro programové rozhraní použijte jednu z sad SDK pro .NET, Java, Python, Node/JS, C++, Go, PHP nebo Ruby.
    - **Místní zařízení** – poskytujeme vám fyzické nebo virtuální zařízení, které se nachází ve vašem datovém centru a optimalizuje přenos dat po síti. Tato zařízení také poskytují místní mezipaměť často používaných souborů. Fyzické zařízení je Data Box Edge a virtuální zařízení je Brána datové schránky. Obě běží trvale ve vašich prostorách a připojují se k Azure přes síť.
    - **Kanál spravovaných dat** – můžete nastavit cloudový kanál pro pravidelný přenos souborů mezi několika službami Azure, místními nebo kombinací dvou. Azure Data Factory slouží k nastavení a správě datových kanálů a přesouvání a transformaci dat pro analýzu.

Následující vizuál ilustruje pokyny pro výběr různých nástrojů pro přenos dat Azure v závislosti na šířce pásma sítě, která je k dispozici pro přenos, velikosti dat určených pro přenos a četnosti přenosu.

![Nástroje pro přenos dat Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Horní hranice offline přenosových zařízení - Data Box Disk, Data Box a Data Box Heavy lze rozšířit zadáním více objednávek typu zařízení.*

## <a name="selecting-a-data-transfer-solution"></a>Výběr řešení pro přenos dat

Odpovězte na následující otázky, které vám pomohou vybrat řešení pro přenos dat:

- Je dostupná šířka pásma sítě omezená nebo neexistuje a chcete přenášet velké datové sady?
  
    Pokud ano, přečtěte si: [Scénář 1: Přenos velkých datových sad bez šířky pásma sítě nebo s malou šířkou pásma sítě](storage-solution-large-dataset-low-network.md).
- Chcete přenést velké datové sady přes síť a máte střední až vysokou šířku pásma sítě?

    Pokud ano, přečtěte si: [Scénář 2: Přenos velkých datových sad se střední až vysokou šířkou pásma sítě](storage-solution-large-dataset-moderate-high-network.md).
- Chcete občas přenést jen několik souborů přes síť?

    Pokud ano, [přečtěte si scénář 3: Přenos malých datových sad s omezenou na střední šířku pásma sítě](storage-solution-small-dataset-low-moderate-network.md).
- Hledáte přenos dat v čase v pravidelných intervalech?

    Pokud ano, použijte skriptované/programové možnosti popsané ve [scénáři 4: Periodické přenosy dat](storage-solution-periodic-data-transfer.md).
- Hledáte nepřetržitý přenos dat?

    Pokud ano, použijte možnosti ve [scénáři 4: Periodické přenosy dat](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Funkce přenosu dat na webu Azure Portal

Můžete taky přejít na svůj účet Azure Storage na webu Azure Portal a vybrat funkci **přenosu dat.** Zadejte šířku pásma sítě ve vašem prostředí, velikost dat, která chcete přenést, a frekvenci přenosu dat. Zobrazí se optimální řešení přenosu dat odpovídající informacím, které jste poskytli. 

## <a name="next-steps"></a>Další kroky

- [Získejte úvod do Průzkumníka úložiště Azure](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Přečtěte si přehled AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Použití Azure PowerShellu s Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Úvodní příručka: Vytváření, stahování a seznam objektů BLOB pomocí rozhraní příkazového příkazového příkazu Azure](../blobs/storage-quickstart-blobs-cli.md)
- Projdeme si tyto možnosti:

    - [Azure Data Box, Disk datové schránky Azure a Azure Data Box Heavy pro offline přenosy](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box Gateway a Azure Data Box Edge pro online přenosy](https://docs.microsoft.com/azure/databox-online/).
- [Zjistěte, co je Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Použití rest API k přenosu dat

    - [V rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [V Javě](https://docs.microsoft.com/java/api/overview/azure/storage)
