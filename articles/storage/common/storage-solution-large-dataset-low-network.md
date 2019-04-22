---
title: Možnosti pro velké datové sady s nízkým nebo žádné šířky pásma sítě pro přenos dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak zvolit řešení Azure pro přenos dat, když máte omezený na žádný šířky pásma sítě ve vašem prostředí a máte v úmyslu přenosu velkých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58849606"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Přenos dat pro rozsáhlé datové sady s nízkým nebo žádné šířky pásma sítě
 
Tento článek obsahuje přehled přenosu dat ve vašem prostředí, máte omezenou na žádné šířky pásma sítě a máte v úmyslu přenosu velkých datových sad řešení. Tento článek také popisuje možnosti převodu doporučené dat a odpovídajících klíčová dovednost matice pro tento scénář.

Přehled o všech pochopit přenos možnosti dostupných dat, přejděte na [volba řešení přenosu dat Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline převodu přenos nebo síť

Velké datové sady znamenají, že máte několik TB na několik PBs data. Máte omezený na žádný šířky pásma sítě, je pomalé síti nebo nespolehlivá. Navíc:

- Od vašich poskytovatelů služeb Internetu (ISP) se uplatňuje limit vycházející náklady na přenos v síti.
- Zabezpečení nebo zásady organizace neumožňují odchozí připojení při práci s důvěrnými osobními údaji.

Ve všech výše uvedených instancích proveďte jednorázově přenosu dat pomocí fyzického zařízení. Vyberte si z disku Data Box, zařízení Data Box Data Box náročné zařízení, které jsou poskytovány společností Microsoft nebo Import/Export pomocí vlastní disky.

Abyste se ujistili, zda fyzického zařízení té správné volby, použijte následující tabulku. Zobrazuje plánovaný čas pro přenos dat sítě, pro různé dostupné šířky pásma (za předpokladu, že 90 % využití). Pokud je přenos v síti plánovaných příliš pomalý, měli byste použít fyzické zařízení.  

![Přenos v síti nebo offline převodu](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti dostupné v tomto scénáři jsou zařízení pro zařízení Azure Data Box offline převod nebo Azure Import/Export.

- **Azure Data Box řady pro offline převody** – použít k přesunu velkých objemů dat do Azure při co vás omezuje čas dostupnosti sítě a náklady na zařízení ze zařízení Data Box dodané společností Microsoft. Kopírování místních dat pomocí nástrojů, jako je například Robocopy. V závislosti na velikost dat určená pro přenos můžete vybrat z disku Data Box, zařízení Data Box nebo Data Box náročné.
- **Azure Import/Export** – použijte Azure Import/Export služby přenosem diskových jednotek bezpečně Import velkých objemů dat do úložiště objektů Blob v Azure a službou soubory Azure. Tato služba také umožňuje přenášet data z úložiště objektů Blob Azure do diskové jednotky a odešlete ji do vašich místních lokalit.

## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulka shrnuje rozdíly mezi klíčové funkce.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Velikost dat                        |    Až 35 TB                 |    Až 80 TB za zařízení                       |    Až 800 TB na zařízení               |    Proměnná                            |
|    Typ dat                        |    Azure Blobs                  |    Azure Blobs<br>Soubory Azure                    |    Azure Blobs<br>Soubory Azure            |    Azure Blobs<br>Soubory Azure          |
|    Uspořádání formuláře                      |    5 SSD disků na pořadí             |    1 × 50-lbs. velikost plochy zařízení podle pořadí    |    1 X ~500-lbs. velká zařízení podle pořadí    |    Až 10 pevné disky nebo disky SSD jednu objednávku        |
|    Počáteční instalace               |    Nízká <br>(15 minut)            |    S nízkou až střední <br> (< 30 minut)               |    Střední<br>(1 – 2 hodiny)               |    Střední na složité<br>(proměnné) |
|    Odesílání dat do Azure               |    Ano                          |    Ano                                           |    Ano                                   |    Ano                                 |
|    Export dat z Azure           |    Ne                           |    Ne                                            |    Ne                                    |    Ano                                 |
|    Šifrování                       |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    Hardware                         |     Microsoft zadaný          |    Microsoft zadaný                            |    Microsoft zadaný                    |    Zákazníka                   |
|    Síťové rozhraní                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integrace partnerských řešení              |    Někteří                         |    [Vysoká](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Vysoká](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Někteří                                |
|    Expedice                         |    Spravovaná Microsoftem            |    Spravovaná Microsoftem                             |    Spravovaná Microsoftem                     |    Spravovaná zákazníkem                    |
| Použijte v případě přesunu dat         |V rámci obchodní oblasti|V rámci obchodní oblasti|V rámci obchodní oblasti|Přes geografické hranice, například nám EU|
|    Ceny                          |    [Ceny](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Ceny](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Další postup

- Pochopit postup

    - [Přenos dat pomocí disku Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Přenos dat pomocí zařízení Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Přenos dat pomocí importu/exportu](/azure/storage/common/storage-import-export-data-to-blobs).
