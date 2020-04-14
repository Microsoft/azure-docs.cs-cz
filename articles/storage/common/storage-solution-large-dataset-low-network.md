---
title: Možnosti přenosu dat Azure pro velké datové sady s nízkou nebo žádnou šířkou pásma sítě| Dokumenty společnosti Microsoft
description: Zjistěte, jak zvolit řešení Azure pro přenos dat, když máte omezenou šířku pásma sítě ve vašem prostředí a plánujete přenos velkých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: be1f74dcccc654dbdd0a743d1da2da89071045f1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253131"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Přenos dat rozsáhlých datových sad s malou nebo žádnou šířkou pásma sítě
 
Tento článek poskytuje přehled řešení přenosu dat, pokud máte omezeno na žádnou šířku pásma sítě ve vašem prostředí a plánujete přenos velkých datových sad. Článek také popisuje doporučené možnosti přenosu dat a matice příslušných klíčových schopností pro tento scénář.

Pokud chcete pochopit přehled všech dostupných možností přenosu dat, přejděte na [výběr řešení pro přenos dat Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline přenos nebo síťový přenos

Velké datové sady naznačují, že máte málo TBs na několik PBs dat. Máte omezeno na žádnou šířku pásma sítě, síť je pomalá nebo je nespolehlivá. Navíc:

- Jste omezeni náklady na přenos sítě od poskytovatelů internetových služeb (ISP).
- Zásady zabezpečení nebo organizace neumožňují odchozí připojení při práci s citlivými daty.

Ve všech výše uvedených případech použijte fyzické zařízení k jednorázovému hromadnému přenosu dat. Vyberte si z disků s datovou schránkou, datových schránka, zařízení Data Box Heavy dodávaných společností Microsoft nebo importujte/exportujte pomocí vlastních disků.

Chcete-li ověřit, zda je fyzické zařízení správnou volbou, použijte následující tabulku. Zobrazuje předpokládaný čas pro přenos síťových dat pro různé dostupné šířky pásma (za předpokladu 90% využití). Pokud je přenos sítě promítnut jako příliš pomalý, měli byste použít fyzické zařízení.  

![Síťový přenos nebo offline přenos](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti, které jsou k dispozici v tomto scénáři jsou zařízení pro přenos Azure Data Box offline nebo Azure Import/Export.

- **Azure Data Box řady pro offline přenosy** – Pomocí zařízení ze zařízení Data Box dodané Microsoft přesunout velké množství dat do Azure, když jste omezeni časem, dostupnost sítě nebo náklady. Kopírování místních dat pomocí nástrojů, jako je Robocopy. V závislosti na velikosti dat určených k přenosu si můžete vybrat z disků datové schránky, datové schránky nebo datové schránky Heavy.
- **Import a export Azure** – Pomocí služby Import/Export Azure odesláním vlastních diskových jednotek můžete bezpečně importovat velké objemy dat do úložiště objektů Blob Azure a souborů Azure. Tuto službu lze také použít k přenosu dat z úložiště objektů Blob Azure na diskové jednotky a dodávat do místních webů.

## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových schopností

Následující tabulka shrnuje rozdíly v klíčových možnostech.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Velikost dat                        |    Až 35 tis                 |    Až 80 TBs na zařízení                       |    Až 800 TB na jedno zařízení               |    Proměnná                            |
|    Datový typ                        |    Objekty blob Azure                  |    Objekty blob Azure<br>Soubory Azure                    |    Objekty blob Azure<br>Soubory Azure            |    Objekty blob Azure<br>Soubory Azure          |
|    Tvarový faktor                      |    5 SSD na objednávku             |    1 x 50 liber. zařízení velikosti plochy na jednu objednávku    |    1 X ~ 500-lbs. velké zařízení na objednávku    |    Až 10 pevných disků/SSD na jednu objednávku        |
|    Počáteční čas nastavení               |    Nízká <br>(15 min)            |    Nízká až střední <br> (<30 min)               |    Střední<br>(1-2 hodiny)               |    Střední až obtížné<br>(variabilní) |
|    Odesílání dat do Azure               |    Ano                          |    Ano                                           |    Ano                                   |    Ano                                 |
|    Export dat z Azure           |    Ne                           |    Ne                                            |    Ne                                    |    Ano                                 |
|    Šifrování                       |    AES 128bitová                  |    AES 256bitový                                   |    AES 256bitový                           |    AES 128bitová                         |
|    Hardware                         |     Společnost Microsoft dodala          |    Společnost Microsoft dodala                            |    Společnost Microsoft dodala                    |    Zákazník dodán                   |
|    Síťové rozhraní                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integrace partnerských řešení              |    Některé                         |    [Vysoké](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Vysoké](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Některé                                |
|    Shipping                         |    Microsoft se podařilo            |    Microsoft se podařilo                             |    Microsoft se podařilo                     |    Zákazník řízený                    |
| Použít při přesunu dat         |V rámci hranice obchodu|V rámci hranice obchodu|V rámci hranice obchodu|Přes zeměpisné hranice, např.|
|    Ceny                          |    [Ceny](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Ceny](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Další kroky

- Pochopit, jak

    - [Přenos dat pomocí disku datové schránky](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Přenos dat pomocí datové schránky](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Přenos dat pomocí importu nebo exportu](/azure/storage/common/storage-import-export-data-to-blobs).
