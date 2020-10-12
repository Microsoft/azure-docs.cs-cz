---
title: Možnosti přenosu dat Azure pro velké datové sady, střední až vysoké šířky pásma sítě | Microsoft Docs
description: Naučte se, jak zvolit řešení Azure pro přenos dat, když ve vašem prostředí máte střední až velkou šířku pásma sítě a plánujete přenos velkých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 900221bd27fe4020a927b3155c91aa8e494ca890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86203850"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Přenos dat rozsáhlých datových sad se střední až velkou šířkou pásma sítě
 
Tento článek obsahuje přehled řešení pro přenos dat, když máte střední až velkou šířku pásma sítě ve vašem prostředí a plánujete přenos velkých datových sad. Tento článek také popisuje doporučené možnosti přenosu dat a příslušnou klíčovou matrici pro tento scénář.

Přehled všech dostupných možností přenosu dat získáte, když přejdete na [vybrat řešení Azure Data Transfer](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Velké datové sady odkazují na velikosti dat v pořadí služby TBs na PBs. Střední pro vysokou šířku pásma sítě odkazuje na 100 MB/s až 10 GB/s.

## <a name="recommended-options"></a>Doporučené možnosti

Možnosti doporučené v tomto scénáři závisí na tom, jestli máte střední šířku pásma sítě nebo velkou šířku pásma sítě.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Střední šířka pásma sítě (100 MB/s – 1 GB/s)

U střední šířky pásma sítě je potřeba promítnout čas přenosu dat přes síť.

Pomocí následující tabulky můžete odhadnout čas a na základě toho vybrat mezi offline přenosem nebo přenosem přes síť. Tabulka zobrazuje plánovaný čas pro přenos síťových dat pro různé dostupné šířky pásma sítě (předpokládá 90% využití).  

![Přenos v síti nebo offline přenos](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Pokud je přenos v síti příliš pomalý, měli byste použít fyzické zařízení. Doporučené možnosti v tomto případě jsou offline přenosové zařízení z řady Azure Data Box nebo Azure import/export s použitím vlastních disků.

    - **Azure Data box rodina pro offline přenosy** – pomocí zařízení ze zařízení data box dodaných společností Microsoft můžete přesunout velké objemy dat do Azure, pokud jste omezeni časem, dostupností sítě nebo náklady. Zkopírujte místní data pomocí nástrojů, jako je například Robocopy. V závislosti na velikosti dat určeného pro přenos můžete zvolit z Data Box Disk, Data Box nebo Data Box Heavy.
    - **Import/export do Azure** – pomocí služby importu a exportu v Azure můžete prostřednictvím přenosu vlastních diskových jednotek bezpečně importovat velké objemy dat do služby Azure Blob Storage a souborů Azure. Tato služba se dá také použít k přenosu dat z úložiště objektů BLOB v Azure na diskové jednotky a k odeslání do vašich místních webů.

- Pokud je přenos v síti přihlášený jako rozumný, můžete použít některý z následujících nástrojů popsaných ve [vysoké šířce pásma sítě](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Vysoká šířka pásma sítě (1 GB/s až 100 GB/s)

Pokud je dostupná šířka pásma sítě vysoká, použijte jeden z následujících nástrojů.

- **AzCopy** – pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data z a do objektů blob, souborů a tabulkového úložiště Azure s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a možnost obnovit operace kopírování při přerušení.
- **Azure Storage rozhraní REST API/sady SDK** – při sestavování aplikace můžete vyvíjet aplikace proti Azure Storage rozhraní REST API a používat sady Azure SDK nabízené v různých jazycích.
- **Azure Data box rodina pro online přenosy** – Data Box Edge a data box Gateway jsou online síťová zařízení, která mohou přesouvat data do a z Azure. Data Box Edge fyzické zařízení použijte v případě, že je k dispozici souběžná potřeba nepřetržitého příjmu a předběžného zpracování dat před nahráním. Data Box Gateway je virtuální verze zařízení se stejnými možnostmi přenosu dat. V každém případě je přenos dat spravován zařízením.
- **Azure Data Factory** – Data Factory by se měly používat ke škálování operace přenosu, a pokud je potřeba Orchestrace a možnosti monitorování podnikové úrovně. Použijte Data Factory k pravidelnému přenosu souborů mezi několika službami Azure, místním prostředím nebo kombinací těchto dvou. pomocí Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály), které ingestují data z různorodých úložišť dat a automatizují přesun dat a transformaci dat.

## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

V následujících tabulkách najdete Souhrn rozdílů v klíčových funkcích pro doporučené možnosti.

### <a name="moderate-network-bandwidth"></a>Střední šířka pásma sítě

Pokud používáte offline přenos dat, použijte následující tabulku pro pochopení rozdílů v klíčových funkcích.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Velikost dat**                    |    Až 35 TBs                 |    Až 80 TBs na zařízení                       |    Až 800 TB na zařízení               |    Proměnná                            |
|    **Datový typ**                    |    Objekty blob Azure                  |    Objekty blob Azure<br>Soubory Azure                    |    Objekty blob Azure<br>Soubory Azure            |    Objekty blob Azure<br>Soubory Azure          |
|    **Formulářový faktor**                  |    5 SSD na objednávku             |    1 X 50-kg zařízení velikosti stolního počítače na jednu objednávku    |    1 X ~ 500-kg velké zařízení na jednu objednávku    |    Až 10 HDD/SSD na objednávku        |
|    **Čas prvotního nastavení**               |    Nízká <br>(15 minut)            |    Nízká až střední <br> (<30 minut)               |    Pokročilé<br>(1-2 hodin)               |    Střední až obtížné<br>variabilní |
|    **Odesílání dat do Azure**           |    Yes                          |    Yes                                           |    Yes                                   |    Yes                                 |
|    **Export dat z Azure**           |    No                           |    No                                            |    No                                    |    Yes                                 |
|    **Šifrování**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Hardware**                     |     Dodán Microsoft          |    Dodán Microsoft                            |    Dodán Microsoft                    |    Dodán zákazník                   |
|    **Síťové rozhraní**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Integrace partnerských řešení**          |    Některé                         |    [Vysoké](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Vysoká](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Některé                                |
|    **Expedice**                     |    Spravované Microsoftem            |    Spravované Microsoftem                             |    Spravované Microsoftem                     |    Spravované zákazníkem                    |
| **Použít při přesunu dat**     |V rámci hranice obchodu|V rámci hranice obchodu|V rámci hranice obchodu|Mezi geografickými hranicemi, např. US až EU|
|    **Ceny**                          |    [Ceny](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Ceny](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Pokud používáte online přenos dat, použijte tabulku v následující části pro velkou šířku pásma sítě.

### <a name="high-network-bandwidth"></a>Velká šířka pásma sítě

|                                     |    Nástroje AzCopy, <br>Azure PowerShell <br>Azure CLI             |    Azure Storage rozhraní REST API, sady SDK                   |    Data Box Gateway nebo Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Datový typ**              |    Objekty blob Azure, soubory Azure, tabulky Azure    |    Objekty blob Azure, soubory Azure, tabulky Azure    |    Objekty blob Azure, soubory Azure                           |   Podporuje 70 + datových konektorů pro úložiště a formáty dat    |
|    **Formulářový faktor**            |    Nástroje příkazového řádku                        |    Programové rozhraní                    |    Microsoft poskytuje virtuální <br>nebo fyzické zařízení     |    Služba v Azure Portal                                            |
|    **Počáteční nastavení jednorázového času** |    Snadné               |    Pokročilé                       |    Snadné (<30 minut) až střední (1-2 hodin)            |    Množství                                                          |
|    **Předběžné zpracování dat**          |    No                                        |    No                                        |    Ano (s využitím hraničních výpočtů)                               |    Yes                                                                |
|    **Přenos z jiných cloudů**   |    No                                        |    No                                        |    No                                                    |    Yes                                                                |
|    **Typ uživatele**                    |    IT specialisté a vývoj                                       |    Vývoj                                       |    IT profesionál                                                |    IT profesionál                                                             |
|    **Ceny**                      |    Bezplatné použití poplatků za výstup dat         |    Bezplatné použití poplatků za výstup dat         |    [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Ceny](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak přenést data pomocí importu a exportu](/azure/storage/common/storage-import-export-data-to-blobs).
- Informace o tom, jak

    - [Přenos dat pomocí data box disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Přenos dat pomocí data box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Přenos dat pomocí AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Informace o tom, jak:
    - [Přenos dat pomocí data box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Umožňuje transformovat data pomocí data box Edge před odesláním do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Naučte se, jak přenést data pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Použití rozhraní REST API k přenosu dat

    - [V .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [V Javě](https://docs.microsoft.com/java/api/overview/azure/storage)
