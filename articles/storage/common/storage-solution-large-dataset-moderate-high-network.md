---
title: Možnosti pro velké datové sady, středně velká šířka pásma sítě pro přenos dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak zvolit řešení Azure pro přenos dat, pokud máte na vysoká Střední šířka pásma sítě ve vašem prostředí a máte v úmyslu přenosu velkých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: bc5668d826395fb71ee70907f095303a43f1ec7f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214313"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Přenos dat pro rozsáhlé datové sady s střední pro velká šířka pásma sítě
 
Tento článek obsahuje přehled přenosu dat řešení Pokud máte na vysoká Střední šířka pásma sítě ve vašem prostředí a máte v úmyslu přenosu velkých datových sad. Tento článek také popisuje možnosti převodu doporučené dat a odpovídajících klíčová dovednost matice pro tento scénář.

Přehled o všech pochopit přenos možnosti dostupných dat, přejděte na [volba řešení přenosu dat Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Velké datové sady odkazují na velikosti dat v TB pro PBs pořadí. Střední pro velká šířka pásma sítě odkazuje na 100 MB/s na 10 GB/s.

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti, doporučení v tomto scénáři závisí na tom, jestli máte střední šířka pásma sítě nebo velká šířka pásma sítě.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Střední šířka pásma sítě (100 MB/s - 1 GB/s)

Střední šířka pásma sítě je nutné do projektu čas pro přenos dat přes síť.

V následující tabulce použijte k odhadu čas a na základě na, si volili mezi offline převodu nebo přes přenos po síti. V tabulce jsou uvedeny plánovaný čas pro přenos dat sítě, pro různé šířky pásma sítě k dispozici (za předpokladu, že 90 % využití).  

![Přenos v síti nebo offline převodu](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Pokud je přenos po síti plánovaných příliš pomalý, měli byste použít fyzické zařízení. Doporučené možnosti v tomto případě je zařízení offline převodu z Azure Data Box řady nebo Azure Import/Export pomocí vlastní disky.

    - **Azure Data Box řady pro offline převody** – použít k přesunu velkých objemů dat do Azure při co vás omezuje čas dostupnosti sítě a náklady na zařízení ze zařízení Data Box dodané společností Microsoft. Kopírování místních dat pomocí nástrojů, jako je například Robocopy. V závislosti na velikost dat určená pro přenos můžete vybrat z disku Data Box, zařízení Data Box nebo Data Box náročné.
    - **Azure Import/Export** – použijte Azure Import/Export služby přenosem diskových jednotek bezpečně Import velkých objemů dat do úložiště objektů Blob v Azure a službou soubory Azure. Tato služba také umožňuje přenášet data z úložiště objektů Blob Azure do diskové jednotky a odešlete ji do vašich místních lokalit.

- Pokud je přenos po síti plánovaných přiměřené, pak můžete použít některý z následujících nástrojů, které jsou podrobně popsané v [velká šířka pásma sítě](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Velká šířka pásma sítě (1 GB/s - 100 GB/s)

Pokud dostupné šířky pásma je vysoké, použijte jednu z následujících nástrojů.

- **AzCopy** – snadno kopírovat data do a z Azure BLOB, soubory, pomocí tohoto nástroje příkazového řádku a Table storage poskytovaly optimální výkon. AzCopy podporuje souběžnost a paralelismu a možnost obnovit operace kopírování, když k přerušení.
- **Azure Storage REST API a sad SDK** – při vytváření aplikace, můžete vyvíjet aplikace proti REST API služby Azure Storage a používat sady SDK Azure nabízí v různých jazycích.
- **Azure Data Box řady pro online převody** – okraj pole Data a brána pole dat jsou online síťová zařízení, která může přesun dat do a z Azure. Použijte fyzické zařízení, když je současné muset pro průběžné ingestování a předběžné zpracování dat před nahrát okraj pole Data. Brána pole dat je virtuální verzi zařízení pomocí stejné funkce přenosu dat. V obou případech spravuje přenos dat zařízení.
- **Azure Data Factory** – Data Factory by měla sloužit k horizontálnímu navýšení kapacity operace přenosu, a pokud je potřeba pro orchestraci a enterprise třída možnosti monitorování. Pomocí datové továrny můžete pravidelně přenášet soubory mezi několik služeb Azure, místní nebo kombinaci obojího. pomocí služby Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály), které ingestují data z různorodých úložišť dat a automatizace přesunu a transformace dat.

## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulky shrnují rozdíly mezi klíčové funkce pro doporučené možnosti.

### <a name="moderate-network-bandwidth"></a>Střední šířka pásma sítě

Pokud používáte přenos dat offline, vysvětluje rozdíly mezi klíčové funkce pomocí následující tabulky.

|                                     |    Data Box Disk (preview)    |    Data Box                                      |    Silná pole dat (preview)              |    Import/export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Velikost dat                        |    Až 35 TB                 |    Až 80 TB za zařízení                       |    Až 800 TB na zařízení               |    Proměnná                            |
|    Typ dat                        |    Objekty BLOB Azure                  |    Objekty BLOB Azure<br>Soubory Azure                    |    Objekty BLOB Azure<br>Soubory Azure            |    Objekty BLOB Azure<br>Soubory Azure          |
|    Uspořádání formuláře                      |    5 SSD disků na pořadí             |    1 × 50-lbs. velikost plochy zařízení podle pořadí    |    1 X ~ 500-lbs. velká zařízení podle pořadí    |    Až 10 pevné disky nebo disky SSD jednu objednávku        |
|    Počáteční instalace               |    Nízká <br>(15 minut)            |    S nízkou až střední <br> (< 30 minut)               |    Střední<br>(1 – 2 hodiny)               |    Střední na složité<br>(proměnné) |
|    Odesílání dat do Azure               |    Ano                          |    Ano                                           |    Ano                                   |    Ano                                 |
|    Export dat z Azure           |    Ne                           |    Ne                                            |    Ne                                    |    Ano                                 |
|    Šifrování                       |    AES 128-bit                  |    AES 256 bitů                                   |    AES 256 bitů                           |    AES 128-bit                         |
|    Hardware                         |     Microsoft zadaný          |    Microsoft zadaný                            |    Microsoft zadaný                    |    Zákazníka                   |
|    Síťové rozhraní                |    USB 3.1/SATA                 |    RJ-45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    Integrace partnerských řešení              |    Někteří                         |    [Vysoká](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Vysoká](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Někteří                                |
|    Expedice                         |    Spravovaná Microsoftem            |    Spravovaná Microsoftem                             |    Spravovaná Microsoftem                     |    Spravovaná zákazníkem                    |
| Použijte v případě přesunu dat         |V rámci obchodní oblasti|V rámci obchodní oblasti|V rámci obchodní oblasti|Přes geografické hranice, například nám EU|
|    Ceny                          |    [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Ceny](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Pokud používáte online datové přenosy, použijte v tabulce v části pro velká šířka pásma sítě.

### <a name="high-network-bandwidth"></a>Velká šířka pásma sítě

|                                     |    Nástroje AzCopy <br>Prostředí Azure PowerShell <br>Azure CLI             |    Rozhraní REST API, sady SDK služby Azure Storage                   |    Brána pole dat nebo dat pole Edge (preview)           |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Typ dat                  |    Objekty BLOB Azure, služba soubory Azure, tabulky Azure    |    Objekty BLOB Azure, služba soubory Azure, tabulky Azure    |    Objekty BLOB Azure, služba soubory Azure                           |   Podporuje 70 datové konektory pro úložiště dat a formáty    |
|    Uspořádání formuláře                |    Nástroje příkazového řádku                        |    Programové rozhraní                    |    Microsoft poskytuje virtuální <br>fyzické zařízení nebo     |    Služby na webu Azure portal                                            |
|    Počáteční jednorázová nastavení     |    Jednoduchost               |    Střední                       |    Jednoduché (< 30 minut), obscénnost (1 – 2 hodiny)            |    Rozsáhlé                                                          |
|    Předběžné zpracování dat              |    Ne                                        |    Ne                                        |    Ano (Edge s výpočty)                               |    Ano                                                                |
|    Přenos z ostatních cloudů       |    Ne                                        |    Ne                                        |    Ne                                                    |    Ano                                                                |
|    Typ uživatele                        |    IT Pro nebo vývoj                                       |    Vývoj                                       |    Profesionál v oblasti IT                                                |    Profesionál v oblasti IT                                                             |
|    Ceny                          |    Bezplatné, vztahují poplatky za odchozí přenos dat         |    Bezplatné, vztahují poplatky za odchozí přenos dat         |    [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Ceny](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Další postup

- [Zjistěte, jak přenášet data prostřednictvím importu/exportu](/azure/storage/common/storage-import-export-data-to-blobs).
- Pochopit postup

    - [Přenos dat pomocí disku Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Přenos dat pomocí zařízení Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Přenos dat pomocí AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Pochopit postup:
    - [Přenos dat pomocí brány dat pole](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformace dat pomocí služby Data Box Edge před odesláním do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Zjistěte, jak přenos dat pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Použití rozhraní REST API pro přenos dat

    - [V rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [V jazyce Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)