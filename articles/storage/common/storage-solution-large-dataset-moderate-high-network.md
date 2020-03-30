---
title: Možnosti přenosu dat Azure pro velké datové sady, střední až velká šířka pásma sítě| Dokumenty společnosti Microsoft
description: Zjistěte, jak zvolit řešení Azure pro přenos dat, když máte střední až vysokou šířku pásma sítě ve vašem prostředí a plánujete přenos velkých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f7177a95bdd585ff2822c9ac8c94a85d12f9259b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900368"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Přenos dat rozsáhlých datových sad se střední až velkou šířkou pásma sítě
 
Tento článek poskytuje přehled řešení přenosu dat, pokud máte střední až vysokou šířku pásma sítě ve vašem prostředí a plánujete přenos velkých datových sad. Článek také popisuje doporučené možnosti přenosu dat a matice příslušných klíčových schopností pro tento scénář.

Pokud chcete pochopit přehled všech dostupných možností přenosu dat, přejděte na [výběr řešení pro přenos dat Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Velké datové sady odkazují na velikosti dat v pořadí tbs na PBs. Střední až vysoká šířka pásma sítě označuje 100 Mb/s až 10 Gb/s.

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti doporučené v tomto scénáři závisí na tom, zda máte střední šířku pásma sítě nebo velkou šířku pásma sítě.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Mírná šířka pásma sítě (100 Mb/s – 1 Gb/s)

S mírnou šířkou pásma sítě je třeba promítnout čas pro přenos dat po síti.

V následující tabulce můžete odhadnout čas a na základě toho si vyberte mezi offline přenosem nebo přenosem v síti. V tabulce je uveden předpokládaný čas pro přenos síťových dat pro různé dostupné šířky pásma sítě (za předpokladu 90% využití).  

![Síťový přenos nebo offline přenos](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Pokud je síťový přenos promítnut jako příliš pomalý, měli byste použít fyzické zařízení. Doporučené možnosti v tomto případě jsou offline přenosová zařízení z řady Azure Data Box nebo Azure Import/Export pomocí vlastních disků.

    - **Azure Data Box řady pro offline přenosy** – Pomocí zařízení ze zařízení Data Box dodané Microsoft přesunout velké množství dat do Azure, když jste omezeni časem, dostupnost sítě nebo náklady. Kopírování místních dat pomocí nástrojů, jako je Robocopy. V závislosti na velikosti dat určených k přenosu si můžete vybrat z disků datové schránky, datové schránky nebo datové schránky Heavy.
    - **Import a export Azure** – Pomocí služby Import/Export Azure odesláním vlastních diskových jednotek můžete bezpečně importovat velké objemy dat do úložiště objektů Blob Azure a souborů Azure. Tuto službu lze také použít k přenosu dat z úložiště objektů Blob Azure na diskové jednotky a dodávat do místních webů.

- Pokud se předpokládá, že síťový přenos je přiměřený, můžete použít některý z následujících nástrojů podrobně popsaných ve [vysoké šířce pásma sítě](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Vysoká šířka pásma sítě (1 Gbps - 100 Gbps)

Pokud je dostupná šířka pásma sítě vysoká, použijte jeden z následujících nástrojů.

- **AzCopy** – Pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data do a z Azure Blobs, Files a Table storage s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a schopnost pokračovat v operacích kopírování při přerušení.
- **Azure Storage REST API nebo SDK –** Při vytváření aplikace můžete vyvíjet aplikaci proti Azure Storage REST API a používat sady Azure SDK nabízené ve více jazycích.
- **Rodina Azure Data Box pro online přenosy** – Data Box Edge a Data Box Gateway jsou online síťová zařízení, která můžou přesouvat data do A z Azure. Fyzické zařízení Data Box Edge používejte v případě současné potřeby nepřetržitého příjemu a předběžného zpracování dat před nahráním. Brána datové schránky je virtuální verze zařízení se stejnými možnostmi přenosu dat. V každém případě je přenos dat spravován zařízením.
- **Azure Data Factory** – Data Factory by se měl použít k horizontálnímu navýšení kapacity operace přenosu a pokud je potřeba pro orchestraci a možnosti monitorování podnikové třídy. Pomocí data factory pravidelně přenášet soubory mezi několika službami Azure, místní nebo kombinace těchto dvou. pomocí data factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály), které ingestují data z různorodých úložišť dat a automatizují přesun dat a transformaci dat.

## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových schopností

Následující tabulky shrnují rozdíly v klíčových možnostech doporučených možností.

### <a name="moderate-network-bandwidth"></a>Mírná šířka pásma sítě

Pokud používáte přenos dat offline, použijte následující tabulku k pochopení rozdílů v klíčových možnostech.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/export                       |
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
|    Integrace partnerských řešení              |    Některé                         |    [Vysoké](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Vysoké](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Některé                                |
|    Shipping                         |    Microsoft se podařilo            |    Microsoft se podařilo                             |    Microsoft se podařilo                     |    Zákazník řízený                    |
| Použít při přesunu dat         |V rámci hranice obchodu|V rámci hranice obchodu|V rámci hranice obchodu|Přes zeměpisné hranice, např.|
|    Ceny                          |    [Ceny](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Ceny](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Pokud používáte online přenos dat, použijte tabulku v následující části pro vysokou šířku pásma sítě.

### <a name="high-network-bandwidth"></a>Vysoká šířka pásma sítě

|                                     |    Nástroje AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    Azure Storage REST API, Sady SDK                   |    Brána datové schránky nebo okraj datové schránky          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Datový typ                  |    Objekty BLOB Azure, soubory Azure, tabulky Azure    |    Objekty BLOB Azure, soubory Azure, tabulky Azure    |    Objekty BLOB Azure, soubory Azure                           |   Podporuje více než 70 datových konektorů pro úložiště a formáty dat    |
|    Tvarový faktor                |    Nástroje příkazového řádku                        |    Programové rozhraní                    |    Společnost Microsoft dodává virtuální <br>nebo fyzické zařízení     |    Služba na portálu Azure                                            |
|    Počáteční jednorázové nastavení     |    Snadné               |    Střední                       |    Snadné (<30 minut) až středně (1-2 hodiny)            |    Rozsáhlé                                                          |
|    Předběžné zpracování dat              |    Ne                                        |    Ne                                        |    Ano (s výpočtem Edge)                               |    Ano                                                                |
|    Přenos z jiných cloudů       |    Ne                                        |    Ne                                        |    Ne                                                    |    Ano                                                                |
|    Typ uživatele                        |    IT Pro nebo dev                                       |    Vývoj                                       |    IT profesionál                                                |    IT profesionál                                                             |
|    Ceny                          |    Zdarma, poplatky za odchozí data         |    Zdarma, poplatky za odchozí data         |    [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Ceny](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak přenášet data pomocí funkce Import nebo Export](/azure/storage/common/storage-import-export-data-to-blobs).
- Pochopit, jak

    - [Přenos dat pomocí disku datové schránky](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Přenos dat pomocí datové schránky](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Přenos dat pomocí AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Pochopit, jak:
    - [Přenos dat pomocí brány datové schránky](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformujte data pomocí data boxu Edge před odesláním do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Přečtěte si, jak přenášet data pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Použití rest API k přenosu dat

    - [V rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [V Javě](https://docs.microsoft.com/java/api/overview/azure/storage)
