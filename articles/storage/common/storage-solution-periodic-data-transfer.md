---
title: Zvolte řešení Azure pro pravidelný přenos dat| Dokumenty společnosti Microsoft
description: Přečtěte si, jak vybrat řešení Azure pro přenos dat při pravidelném přenosu dat.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67357040"
---
# <a name="solutions-for-periodic-data-transfer"></a>Řešení pro pravidelné přenosy dat
 
Tento článek poskytuje přehled řešení přenosu dat při pravidelném přenosu dat. Periodický přenos dat po síti lze v pravidelných intervalech kategorizovat jako opakující se nebo nepřetržitý pohyb dat. Článek také popisuje doporučené možnosti přenosu dat a matice příslušných klíčových schopností pro tento scénář.

Pokud chcete pochopit přehled všech dostupných možností přenosu dat, přejděte na [výběr řešení pro přenos dat Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Doporučené možnosti

Doporučené možnosti pravidelného přenosu dat spadají do dvou kategorií v závislosti na tom, zda je přenos opakovaný nebo nepřetržitý.

- **Skriptované/programové nástroje** – pro přenos dat, ke kterému dochází v pravidelných intervalech, použijte skriptované a programové nástroje, jako je AzCopy a Azure Storage REST API. Tyto nástroje jsou zaměřeny na odborníky v oblasti IT a vývojáře.

    - **AzCopy** – Pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data do a z Azure Blobs, Files a Table storage s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a schopnost pokračovat v operacích kopírování při přerušení.
    - **Azure Storage REST API nebo SDK –** Při vytváření aplikace můžete vyvíjet aplikaci proti Azure Storage REST API a používat sady Azure SDK nabízené ve více jazycích. Rest API můžete také využít Azure Storage Data Movement Library určené speciálně pro vysoce výkonné kopírování dat do a z Azure.

- **Nástroje pro nepřetržité probíhání dat** – pro nepřetržité průběžné a průběžné přilnavosti dat si můžete vybrat jedno z online přenosových zařízení Data Box nebo Azure Data Factory. Tyto nástroje jsou nastaveny odborníky v oblasti IT a mohou transparentně automatizovat přenos dat.

    - **Azure Data Factory** – Data Factory by se měl použít k horizontálnímu navýšení kapacity operace přenosu a pokud je potřeba pro orchestraci a možnosti monitorování podnikové třídy. Azure Data Factory slouží k nastavení cloudového kanálu, který pravidelně přenáší soubory mezi několika službami Azure, místními nebo jejich kombinací. Azure Data Factory umožňuje organizovat pracovní postupy řízené daty, které ingestují data z různorodých úložišť dat a automatizují přesun dat a transformaci dat.
    - **Rodina Azure Data Box pro online přenosy** – Data Box Edge a Data Box Gateway jsou online síťová zařízení, která můžou přesouvat data do A z Azure. Data Box Edge používá k předběžnému zpracování dat před nahráním výpočetní prostředky Edge s podporou umělé inteligence (AI). Brána datové schránky je virtuální verze zařízení se stejnými možnostmi přenosu dat.


## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových schopností

Následující tabulka shrnuje rozdíly v klíčových možnostech.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Skriptovaný/programatický síťový přenos dat

| Schopnost                  | AzCopy                                 | Azure Storage REST API       |
|-----------------------------|----------------------------------------|-------------------------------|
| Tvarový faktor                 | Nástroj příkazového řádku od společnosti Microsoft       | Zákazníci se vyvíjejí proti úložišti <br> REST API pomocí klientských knihoven Azure |
| Počáteční jednorázové nastavení     | Minimální                                | Mírné, variabilní vývojové úsilí    |
| Formát dat                 | Objekty BLOB Azure, soubory Azure, tabulky Azure | Objekty BLOB Azure, soubory Azure, tabulky Azure   |
| Výkon                 | Již optimalizované                      | Optimalizujte vývoj                  |
| Ceny                     | Zdarma, poplatky za odchozí data      | Zdarma, poplatky za odchozí data        |

### <a name="continuous-data-ingestion-over-network"></a>Nepřetržité přijímání dat v síti

| Funkce                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Tvarový faktor                                   | Virtuální zařízení             | Fyzické zařízení          | Služba na portálu Azure, místní agent                                                            |
| Hardware                                      | Váš hypervisor            | Dodává společnost Microsoft    | Není k dispozici                                                            |
| Počáteční úsilí o nastavení                          | Nízká (<30 min.)            | Střední (~ pár hodin) | Velké (~dny)                                                 |
| Formát dat                                   | Objekty BLOB Azure, soubory Azure   | Objekty BLOB Azure, soubory Azure | [Podporuje více než 70 datových konektorů pro úložiště a formáty dat](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Předběžné zpracování dat                           | Ne                         | Ano, přes edge compute    | Ano                                                           |
| Místní mezipaměť<br>(pro ukládání místních dat)    | Ano                        | Ano                      | Ne                                                            |
| Přenos z jiných cloudů                    | Ne                         | Ne                       | Ano                                                           |
| Ceny                                       | [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Ceny](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Další kroky

- [Přenos dat pomocí AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Další informace o přenosu dat pomocí úložišť REST API](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Pochopit, jak:
    - [Přenos dat pomocí brány datové schránky](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformujte data pomocí data boxu Edge před odesláním do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Přečtěte si, jak přenášet data pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
