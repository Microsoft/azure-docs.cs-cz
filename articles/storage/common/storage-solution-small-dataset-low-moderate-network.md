---
title: Možnosti přenosu dat Azure pro malé datové sady s nízkou a střední šířkou sítě | Microsoft Docs
description: Naučte se, jak zvolit řešení Azure pro přenos dat, pokud máte ve svém prostředí malou šířku pásma sítě a plánujete přenos malých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023223"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Přenos dat malých datových sad s malou až střední šířkou pásma sítě
 
Tento článek obsahuje přehled řešení pro přenos dat, pokud máte ve svém prostředí slabý a střední šířku pásma sítě a plánujete přenos malých datových sad. Tento článek také popisuje doporučené možnosti přenosu dat a příslušnou klíčovou matrici pro tento scénář.

Přehled všech dostupných možností přenosu dat získáte, když přejdete na [vybrat řešení Azure Data Transfer](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Malé datové sady odkazují na velikosti dat v řádu GB až několik TB. Nízká až střední šířka pásma sítě znamená 45 Mb/s (připojení T3 v datacentru) až 1 Gb/s.

- Pokud přenášíte pouze několik souborů a nemusíte provádět automatizaci přenosu dat, zvažte nástroje s grafickým rozhraním.
- Pokud jste obeznámeni se správou systému, zvažte použití příkazového řádku, případně programových nebo skriptovacích nástrojů.

## <a name="recommended-options"></a>Doporučené možnosti

V tomto scénáři jsou doporučené tyto možnosti:

- **Nástroje grafického rozhraní** , jako je například Průzkumník služby Azure Storage a Azure Storage v Azure Portal. Poskytují tak snadný způsob, jak zobrazit data a rychle přenést několik souborů.

    - **Průzkumník služby Azure Storage** – tento nástroj pro různé platformy umožňuje spravovat obsah účtů úložiště Azure. Umožňuje nahrávat, stahovat a spravovat objekty blob, soubory, fronty, tabulky a entity služby Azure Cosmos DB. Při použití se službou Blob Storage umožňuje spravovat objekty blob a složky a také nahrávat a stahovat objekty blob mezi místním systémem souborů a službou Blob Storage nebo mezi účty úložiště.
    - **Azure Portal** – Azure Storage v Azure Portal poskytuje webové rozhraní pro prozkoumávání souborů a nahrávání nových souborů po jednom. Tato možnost je vhodná v případě, že nechcete instalovat žádné nástroje ani vydávat příkazy, ale chcete pouze rychle procházet soubory nebo jednoduše nahrát pár nových.

- **Skriptovací a programové nástroje** , jako je AzCopy/PowerShell, Azure CLI a rozhraní REST API Azure Storage.

    - **AzCopy** – pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data z a do objektů blob, souborů a tabulkového úložiště Azure s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a možnost obnovení operací kopírování v případě přerušení.
    - **Azure PowerShell** – pro uživatele, kteří chtějí pracovat se správou systému, použijte modul Azure Storage v Azure PowerShell k přenosu dat.
    - **Azure CLI** – pomocí tohoto nástroje pro různé platformy spravujte služby Azure a nahrajte data do Azure Storage.
    - **Azure Storage rozhraní REST API/sady SDK** – při sestavování aplikace můžete aplikaci vyvíjet pro Azure Storage REST API/sady SDK a použít klientské knihovny Azure nabízené v několika jazycích.


## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulka shrnuje rozdíly v klíčových funkcích.

| Funkce | Azure Storage Explorer | portál Azure | AzCopy<br>Azure PowerShell<br>Azure CLI | Rozhraní REST API nebo sady SDK pro Azure Storage |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Dostupnost | Stažení a instalace <br>Samostatný nástroj | Webové nástroje pro průzkum v Azure Portal | Nástroj příkazového řádku |Programovatelné rozhraní v jazycích .NET, Java, Python, JavaScript, C++, přejít, Ruby a PHP |
| Grafické rozhraní | Yes | Yes | No | No |
| Podporované platformy | Windows, Mac, Linux | Založené na webu |Windows, Mac, Linux |Všechny platformy |
| Povolené operace úložiště objektů BLOB<br>pro objekty BLOB a složky | Nahrávání<br>Stáhnout<br>Spravovat | Nahrávání<br>Stáhnout<br>Spravovat |Nahrávání<br>Stáhnout<br>Spravovat | Ano, přizpůsobit |
| Povolené Data Lake Gen1 úložiště<br>operace se soubory a složkami | Nahrávání<br>Stáhnout<br>Spravovat | No |Nahrávání<br>Stáhnout<br>Spravovat                   | No |
| Povolené operace úložiště souborů<br>pro soubory a adresáře | Nahrávání<br>Stáhnout<br>Spravovat | Nahrávání<br>Stáhnout<br>Spravovat   |Nahrávání<br>Stáhnout<br>Spravovat | Ano, přizpůsobit |
| Povolené operace úložiště tabulek<br>pro tabulky |Spravovat | No |Podpora tabulek v AzCopy v7 |Ano, přizpůsobit|
| Povolené úložiště fronty | Spravovat | No  |No | Ano, je přizpůsobitelný|


## <a name="next-steps"></a>Další kroky

- Naučte se, jak [přenést data pomocí Průzkumník služby Azure Storage](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md).
- [Přenos dat pomocí nástroje AzCopy](./storage-use-azcopy-v10.md)