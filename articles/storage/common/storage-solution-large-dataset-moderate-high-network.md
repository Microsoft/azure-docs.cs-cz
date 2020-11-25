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
ms.openlocfilehash: 962bb7a4484f28d52ffd4f0cae985140ec2f0d28
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006026"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Přenos dat rozsáhlých datových sad se střední až velkou šířkou pásma sítě
 
Tento článek obsahuje přehled řešení pro přenos dat, když máte střední až velkou šířku pásma sítě ve vašem prostředí a plánujete přenos velkých datových sad. Tento článek také popisuje doporučené možnosti přenosu dat a příslušnou klíčovou matrici pro tento scénář.

Přehled všech dostupných možností přenosu dat získáte, když přejdete na [vybrat řešení Azure Data Transfer](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Popis scénáře

Velké datové sady odkazují na velikosti dat v pořadí služby TBs na PBs. Střední pro vysokou šířku pásma sítě odkazuje na 100 MB/s až 10 GB/s.

## <a name="recommended-options"></a>Doporučené možnosti

Doporučené možnosti v tomto scénáři závisí na tom, jestli máte střední nebo velkou šířku pásma sítě.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Střední šířka pásma sítě (100 Mb/s až 1 Gb/s)

U střední šířky pásma sítě je potřeba promítnout čas přenosu dat přes síť.

Pomocí následující tabulky můžete odhadnout čas a na základě toho vybrat mezi offline přenosem nebo přenosem přes síť. Tabulka zobrazuje plánovaný čas pro přenos síťových dat pro různé dostupné šířky pásma sítě (předpokládá 90% využití).  

![Přenos v síti nebo offline přenos](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Pokud je přenos v síti příliš pomalý, měli byste použít fyzické zařízení. Doporučené možnosti v tomto případě jsou offline přenosové zařízení z řady Azure Data Box nebo Azure import/export s použitím vlastních disků.

    - **Azure Data box rodina pro offline přenosy** – pomocí zařízení ze zařízení data box dodaných společností Microsoft můžete přesunout velké objemy dat do Azure, pokud jste omezeni časem, dostupností sítě nebo náklady. Ke kopírování místních dat můžete použít nástroje, jako je například Robocopy. V závislosti na velikosti dat, která chcete přenést, si můžete vybrat Data Box Disk, Data Box nebo Data Box Heavy.
    - **Import/export do Azure** – pomocí služby importu a exportu v Azure můžete prostřednictvím přenosu vlastních diskových jednotek bezpečně importovat velké objemy dat do služby Azure Blob Storage a souborů Azure. Pomocí této služby je také možné přenést data ze služby Azure Blob Storage na diskové jednotky a odeslat je do místních lokalit.

- Pokud je přenos v síti přihlášený jako rozumný, můžete použít některý z následujících nástrojů popsaných ve [vysoké šířce pásma sítě](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Velká šířka pásma sítě (1 Gb/s až 100 Gb/s)

Pokud je dostupná šířka pásma sítě vysoká, použijte jeden z následujících nástrojů.

- **AzCopy** – pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data z a do objektů blob, souborů a tabulkového úložiště Azure s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a možnost obnovení operací kopírování v případě přerušení.
- **Azure Storage rozhraní REST API/sady SDK** – při sestavování aplikace můžete vyvíjet aplikace proti Azure Storage rozhraní REST API a používat sady Azure SDK nabízené v různých jazycích.
- **Azure Data box rodina pro online přenosy** – Data Box Edge a data box Gateway jsou online síťová zařízení, která mohou přesouvat data do a z Azure. Fyzické zařízení Data Box Edge použijte v případě, že potřebujete průběžný příjem dat a zároveň předběžné zpracování dat před nahráním. Data Box Gateway je virtuální verze tohoto zařízení, ale se stejnými funkcemi pro přenos dat. Správu přenosu dat v každém případě zajišťuje zařízení.
- **Azure Data Factory** – Data Factory by se měly používat ke škálování operace přenosu, a pokud je potřeba Orchestrace a možnosti monitorování podnikové úrovně. Data Factory umožňuje pravidelný přenos souborů mezi několika službami Azure, místními prostředími nebo jejich kombinací. Pomocí služby Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (označované jako kanály), které ingestují data z různorodých úložišť dat a automatizují přesun a transformaci dat.

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

- [Přečtěte si, jak přenést data pomocí importu a exportu](./storage-import-export-data-to-blobs.md).
- Informace o tom, jak

    - [Přenos dat pomocí data box disk](../../databox/data-box-disk-quickstart-portal.md).
    - [Přenos dat pomocí data box](../../databox/data-box-quickstart-portal.md).
- [Přenos dat pomocí AzCopy](./storage-use-azcopy-v10.md).
- Informace o tom, jak:
    - [Přenos dat pomocí data box Gateway](../../databox-online/data-box-gateway-deploy-add-shares.md).
    - [Umožňuje transformovat data pomocí data box Edge před odesláním do Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Naučte se, jak přenést data pomocí Azure Data Factory](../../data-factory/quickstart-create-data-factory-portal.md).
- Použití rozhraní REST API k přenosu dat

    - [V .NET](/dotnet/api/overview/azure/storage)
    - [V Javě](/java/api/overview/azure/storage)