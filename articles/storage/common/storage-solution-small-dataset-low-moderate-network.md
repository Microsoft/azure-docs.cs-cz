---
title: Možnosti přenosu dat Azure pro malé datové sady s nízkou až střední šířkou pásma sítě| Dokumenty společnosti Microsoft
description: Zjistěte, jak zvolit řešení Azure pro přenos dat, když máte nízkou až střední šířku pásma sítě ve vašem prostředí a plánujete přenos malých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60397273"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Přenos dat malých datových sad s malou až střední šířkou pásma sítě
 
Tento článek poskytuje přehled řešení přenosu dat, pokud máte nízkou až střední šířku pásma sítě ve vašem prostředí a plánujete přenos malých datových sad. Článek také popisuje doporučené možnosti přenosu dat a matice příslušných klíčových schopností pro tento scénář.

Pokud chcete pochopit přehled všech dostupných možností přenosu dat, přejděte na [výběr řešení pro přenos dat Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Malé datové sady odkazují na velikosti dat v pořadí GBs na několik TBs. Nízká až střední šířka pásma sítě znamená 45 Mb/s (připojení T3 v datovém centru) až 1 Gb/s.

- Pokud přenášíte pouze několik souborů a nepotřebujete automatizovat přenos dat, zvažte nástroje s grafickým rozhraním.
- Pokud jste spokojeni se správou systému, zvažte příkazový řádek nebo programové/skriptovací nástroje.

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti doporučené v tomto scénáři jsou:

- **Nástroje grafického rozhraní,** jako je Azure Storage Explorer a Azure Storage na webu Azure Portal. Ty poskytují snadný způsob zobrazení dat a rychlé přenos několika souborů.

    - **Azure Storage Explorer** – tento nástroj pro různé platformy umožňuje spravovat obsah vašich účtů úložiště Azure. Umožňuje odesílat, stahovat a spravovat objekty BLOB, soubory, fronty, tabulky a entity Azure Cosmos DB. Použijte ho s úložištěm objektů Blob ke správě objektů BLOB a složek a také k odesílání a stahování objektů BLOB mezi místním systémem souborů a úložištěm objektů blob nebo mezi účty úložiště.
    - **Azure Portal** – Azure Storage na azure portálu poskytuje webové rozhraní prozkoumat soubory a nahrát nové soubory jeden po druhém. To je dobrá volba, pokud nechcete instalovat žádné nástroje nebo vydávat příkazy, abyste rychle prozkoumali soubory nebo jednoduše nahráli několik nových.

- **Skriptovací/programové nástroje,** jako je AzCopy/PowerShell/Azure CLI a Azure Storage REST API.

    - **AzCopy** – Pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data do a z Azure Blobs, Files a Table storage s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a schopnost pokračovat v operacích kopírování při přerušení.
    - **Azure PowerShell** – pro uživatele, kteří mají pohodlí se správou systému, použijte k přenosu dat modul Azure Storage v Azure PowerShellu.
    - **Azure CLI** – pomocí tohoto nástroje pro různé platformy můžete spravovat služby Azure a nahrávat data do Azure Storage.
    - **Azure Storage REST API nebo SDKs** – Při vytváření aplikace, můžete vyvinout aplikaci proti Azure Storage REST API/SDKa a používat klientské knihovny Azure nabízené ve více jazycích.


## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových schopností

Následující tabulka shrnuje rozdíly v klíčových možnostech.

| Funkce | Azure Storage Explorer | portál Azure | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST API nebo SDKs |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Dostupnost | Stažení a instalace <br>Samostatný nástroj | Webové nástroje průzkumu na webu Azure Portal | Nástroj příkazového řádku |Programovatelná rozhraní v .NET, Java, Pythonu, JavaScriptu, C++, Go, Ruby a PHP |
| Grafické rozhraní | Ano | Ano | Ne | Ne |
| Podporované platformy | Windows, Mac, Linux | Webové stránky |Windows, Mac, Linux |Všechny platformy |
| Povolené operace úložiště objektů Blob<br>pro objekty BLOB a složky | Odeslat<br>Stáhnout<br>Správa | Odeslat<br>Stáhnout<br>Správa |Odeslat<br>Stáhnout<br>Správa | Ano, přizpůsobitelné |
| Povolené úložiště Data Lake Gen1<br>operace pro soubory a složky | Odeslat<br>Stáhnout<br>Správa | Ne |Odeslat<br>Stáhnout<br>Správa                   | Ne |
| Povolené operace ukládání souborů<br>pro soubory a adresáře | Odeslat<br>Stáhnout<br>Správa | Odeslat<br>Stáhnout<br>Správa   |Odeslat<br>Stáhnout<br>Správa | Ano, přizpůsobitelné |
| Povolené operace úložiště tabulky<br>pro tabulky |Správa | Ne |Podpora tabulek v AzCopy v7 |Ano, přizpůsobitelné|
| Povolené úložiště fronty | Správa | Ne  |Ne | Ano, je přizpůsobitelné|


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [přenášet data pomocí Průzkumníka úložiště Azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Přenos dat pomocí nástroje AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

