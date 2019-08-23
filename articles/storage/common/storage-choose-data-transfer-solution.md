---
title: Zvolit řešení Azure pro přenos dat | Microsoft Docs
description: Naučte se, jak zvolit řešení Azure pro přenos dat na základě velikostí dat a dostupné šířky pásma sítě ve vašem prostředí.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 838d65da90ec0daef69375e5a75bcb497a0c3512
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900390"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Zvolit řešení Azure pro přenos dat

Tento článek poskytuje přehled některých běžných řešení pro přenos dat Azure. Článek také odkazuje na Doporučené možnosti v závislosti na šířce pásma sítě ve vašem prostředí a velikosti dat, která chcete přenést.

## <a name="types-of-data-movement"></a>Typy přesunu dat

Přenos dat může být offline nebo přes síťové připojení. Vyberte své řešení v závislosti na vašich:

- **Velikost dat** – velikost dat, která se mají přenést,
- **Frekvence přenosu** – jednorázová nebo pravidelná příjem dat a
- **Síť** – šířka pásma dostupná pro přenos dat ve vašem prostředí.

Pohyb dat může být z následujících typů:

- **Offline přenos pomocí zařízení** s podporou přepravení – použití fyzických zařízení s podporou přenesených dat v případě, že chcete provést offline přenos hromadných přenosů za provozu. Microsoft vám pošle disk nebo zabezpečené specializované zařízení. Případně můžete zakoupit a dodávat vlastní disky. Data do zařízení zkopírujete a pak ho dodáte do Azure, kde se data nahrají.  Dostupné možnosti pro tento případ jsou Data Box Disk, Data Box, Data Box Heavy a import/export (použijte vlastní disky).

- **Síťový přenos** – data do Azure přenášíte přes síťové připojení. To lze provést mnoha způsoby.

    - **Grafické rozhraní** – Pokud občas přenášíte jenom pár souborů a nepotřebujete provádět automatizaci přenosu dat, můžete zvolit nástroj grafického rozhraní, například Průzkumník služby Azure Storage nebo webový nástroj pro průzkum v Azure Portal.
    - **Skriptovaná nebo programová migrace** – můžete používat optimalizované softwarové nástroje, které poskytujeme nebo voláme přímo rozhraní REST API/sady SDK. Dostupné nástroje pro skriptování jsou AzCopy, Azure PowerShell a Azure CLI. Pro programové rozhraní použijte jednu ze sad SDK pro .NET, Java, Python, Node/JS, C++, přejít, php nebo Ruby.
    - **Místní zařízení** – poskytujeme fyzické nebo virtuální zařízení, které se nachází ve vašem datovém centru a optimalizuje přenos dat přes síť. Tato zařízení také poskytují místní mezipaměť často používaných souborů. Fyzickým zařízením je Data Box Edge a virtuálním zařízením je Data Box Gateway. Oba se spustí trvale ve vašich prostorách a připojí se k Azure přes síť.
    - **Spravovaný datový kanál** – můžete nastavit kanál cloudu k pravidelnému přenosu souborů mezi několika službami Azure, místním prostředím nebo kombinací dvou. Pomocí Azure Data Factory můžete nastavit a spravovat datové kanály a přesouvat a transformovat data pro analýzu.

Následující vizuál znázorňuje pokyny pro výběr různých nástrojů pro přenos dat Azure v závislosti na šířce pásma, která je dostupná pro přenos, velikost dat určená pro přenos a frekvence přenosů.

![Nástroje pro přenos dat Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Horní meze zařízení offline přenosu – Data Box Disk, Data Box a Data Box Heavy se dají rozšířit tak, že se do něj umístí víc objednávek typu zařízení.*

## <a name="selecting-a-data-transfer-solution"></a>Výběr řešení pro přenos dat

Odpovězte na následující otázky, které vám pomůžou vybrat řešení pro přenos dat:

- Je dostupná šířka pásma sítě, která je omezená nebo neexistující, a chcete přenést velké datové sady?
  
    Pokud ano, přečtěte si: [Scénář 1: Přenos velkých datových sad bez nebo nízké šířky pásma](storage-solution-large-dataset-low-network.md)sítě.
- Chcete přenášet velké datové sady přes síť a máte střední až velkou šířku pásma sítě?

    Pokud ano, přečtěte si: [Scénář 2: Přenos velkých datových sad se střední a vysokou šířkou](storage-solution-large-dataset-moderate-high-network.md)pásma sítě.
- Chcete občas přenést jenom několik souborů přes síť?

    Pokud ano, podívejte [se na scénář 3: Přenos malých datových sad s omezenou na střední šířku pásma](storage-solution-small-dataset-low-moderate-network.md)sítě.
- Hledáte v pravidelných intervalech přenos dat k bodu v čase?

    Pokud ano, použijte skriptované/programové možnosti popsané ve [scénáři 4: Periodické datové přenosy](storage-solution-periodic-data-transfer.md).
- Hledáte průběžný přenos dat?

    Pokud ano, použijte možnosti ve [scénáři 4: Periodické datové přenosy](storage-solution-periodic-data-transfer.md).
 

## <a name="data-transfer-feature-in-azure-portal"></a>Funkce přenosu dat v Azure Portal

Můžete také přejít na účet Azure Storage v Azure Portal a vybrat funkci pro **přenos dat** . Zadejte šířku pásma sítě ve vašem prostředí, velikost dat, která chcete přenést, a četnost přenosu dat. Budou se vám zobrazovat optimální řešení pro přenos dat, která odpovídají zadaným informacím. 

## <a name="next-steps"></a>Další postup

- Seznámení [se Průzkumník služby Azure Storage](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Přečtěte si přehled AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Použití Azure PowerShell s Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Použití Azure CLI s Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Další informace:

    - [Azure Data box, Azure Data box disk a Azure Data box Heavy pro offline přenosy](https://docs.microsoft.com/azure/databox/).
    - [Azure Data box Gateway a Azure Data box Edge pro online přenosy](https://docs.microsoft.com/azure/databox-online/).
- [Přečtěte si, co je Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Použití rozhraní REST API k přenosu dat

    - [V .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [V jazyce Java](https://docs.microsoft.com/java/api/overview/azure/storage)
