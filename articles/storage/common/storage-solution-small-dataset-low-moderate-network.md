---
title: Azure data přenášet do střední šířka pásma sítě možnosti pro malé datové sady s nízkými | Dokumentace Microsoftu
description: Zjistěte, jak zvolit řešení Azure pro přenos dat při budete muset nízké střední šířka pásma sítě ve vašem prostředí a plánujete přenést malé datové sady.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: b9c19c4cd85b1ab2bc3ea010d029361957de3943
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53264109"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Přenos dat pro malé datové sady s nízkými do střední šířka pásma sítě
 
Tento článek obsahuje přehled o přenos dat při budete muset nízké střední šířka pásma sítě ve vašem prostředí a plánujete přenést malé datové sady řešení. Tento článek také popisuje možnosti převodu doporučené dat a odpovídajících klíčová dovednost matice pro tento scénář.

Přehled o všech pochopit přenos možnosti dostupných dat, přejděte na [volba řešení přenosu dat Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Malé datové sady odkazují na velikosti dat v pořadí podle GB na několik TB. S nízkou až střední šířka pásma sítě znamená 45 MB/s (T3 připojení v datovém centru) na 1 GB/s.

- Pokud přenášíte pouze na několik souborů a není nutné k automatizaci přenos dat, zvažte nástroje s grafickým rozhraním.
- Pokud jste obeznámeni s správu systému, zvažte možnost příkazového řádku nebo nástroje pro programové/skriptování.

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti doporučení v tomto scénáři jsou:

- **Nástroje grafického rozhraní** jako je Průzkumník služby Azure Storage a Azure Storage na webu Azure portal. Ty umožňují snadno zobrazit vaše data a rychle přenést několik souborů.

    - **Průzkumník služby Azure Storage** – tento nástroj pro různé platformy vám umožní spravovat obsah z vašich účtů úložiště Azure. Umožňuje vám odesílat, stáhněte si a spravovat objekty BLOB, soubory, fronty, tabulky a entity Azure Cosmos DB. Použití úložiště objektů Blob Správa objektů BLOB a složek, a také nahrávat a stahovat objekty BLOB mezi místním systému souborů a úložištěm objektů Blob, nebo mezi účty úložiště.
    - **Azure portal** – Azure Storage na webu Azure portal poskytuje webové rozhraní pro zkoumání souborů a nahrávání nových souborů postupně po jednom. Toto je dobrou volbou, pokud nechcete nainstalovat žádné nástroje nebo vydávat příkazy, jak rychle prozkoumat soubory nebo stačí nahrát několik nových struktur.

- **Nástroje pro skriptování programový** například rozhraní příkazového řádku AzCopy/PowerShell/Azure a REST API služby Azure Storage.

    - **AzCopy** – snadno kopírovat data do a z Azure BLOB, soubory, pomocí tohoto nástroje příkazového řádku a Table storage poskytovaly optimální výkon. AzCopy podporuje souběžnost a paralelismu a možnost obnovit operace kopírování, když k přerušení.
    - **Prostředí Azure PowerShell** – Uživatelé zvyklí správu systému, použít modul Azure Storage v prostředí Azure PowerShell k přenosu dat.
    - **Azure CLI** – pomocí tohoto nástroje více platforem zajišťující správu služeb Azure a nahrání dat do služby Azure Storage.
    - **Azure Storage REST API a sad SDK** – při vytváření aplikace, můžete vyvíjet aplikace pro Azure Storage REST API a sad SDK a použití klientské knihovny Azure nabízí v různých jazycích.


## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulka shrnuje rozdíly mezi klíčové funkce.

| Funkce                                                            | Azure Storage Explorer                    | portál Azure  | AzCopy<br>Azure PowerShell<br>Azure CLI            | Rozhraní REST API služby Azure Storage nebo sady SDK |
|----------------------------------------------|-------------------------------------------|--------------------------------------|-------------------|---------------------------------------|
| Dostupnost                                                       | Stažení a instalace <br>Samostatný nástroj | Nástroje na prozkoumávání založeného na webu na webu Azure portal           | Nástroj příkazového řádku |Programovatelný rozhraní v rozhraní .NET, Java, Python, JavaScript, C++, Go, Ruby a PHP                                                         |
| Grafické rozhraní                                              | Ano                                       | Ano                                                     | Ne                | Ne                                                      |
| Podporované platformy                                              | Windows, Mac, Linux                       | Webové     |Windows, Mac, Linux     |Všechny platformy                                                         |
| Povolené operace úložiště objektů Blob<br>pro objekty BLOB a složek            | Odeslat<br>Ke stažení<br>Spravovat    | Odeslat<br>Ke stažení<br>Spravovat  |Odeslat<br>Ke stažení<br>Spravovat             | Ano, přizpůsobitelné                                                        |
| Povolené úložiště Data Lake Gen1<br>operace pro soubory a složky  | Odeslat<br>Ke stažení<br>Spravovat                | Ne |Odeslat<br>Ke stažení<br>Spravovat                   |      Ne                                                   |
| Povolené operace služby File storage<br>pro soubory a adresáře        | Odeslat<br>Ke stažení<br>Spravovat       | Odeslat<br>Ke stažení<br>Spravovat   |Odeslat<br>Ke stažení<br>Spravovat |                                                         |Ano, přizpůsobitelné
| Povolená operace s tabulkou úložiště<br>pro tabulky                      |Spravovat                            | Ne            |Podpora tabulek v AzCopy v7 |Ano, přizpůsobitelné|
| Povolené Queue storage                                              | Spravovat                                    | Ne  |Ne | Ano, je přizpůsobitelný|


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [přenos dat pomocí Průzkumníka služby Azure Storage](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Přenos dat pomocí AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

