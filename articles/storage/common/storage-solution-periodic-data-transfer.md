---
title: Zvolit řešení Azure pro pravidelný přenos dat | Microsoft Docs
description: Naučte se, jak zvolit řešení Azure pro přenos dat, když pravidelně přenášíte data.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 9ffa35e158d34a1fc6945ee2730dcf136d13edb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85504333"
---
# <a name="solutions-for-periodic-data-transfer"></a>Řešení pro pravidelné přenosy dat
 
Tento článek poskytuje přehled řešení přenosu dat při pravidelném přenosu dat. Pravidelný přenos dat prostřednictvím sítě je možné v pravidelných intervalech nebo nepřetržitém přesunu dat kategorizovat jako periodický. Tento článek také popisuje doporučené možnosti přenosu dat a příslušnou klíčovou matrici pro tento scénář.

Přehled všech dostupných možností přenosu dat získáte, když přejdete na [vybrat řešení Azure Data Transfer](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Doporučené možnosti

Doporučené možnosti pro pravidelný přenos dat spadají do dvou kategorií v závislosti na tom, jestli je přenos opakovaný nebo souvislý.

- **Skriptované/programové nástroje** – pro přenos dat, ke kterým dochází v pravidelných intervalech, použijte skriptované a programové nástroje, jako je AzCopy a rozhraní REST api pro Azure Storage. Tyto nástroje jsou zaměřené na odborníky na IT a vývojáře.

    - **AzCopy** – pomocí tohoto nástroje příkazového řádku můžete snadno kopírovat data z a do objektů blob, souborů a tabulkového úložiště Azure s optimálním výkonem. AzCopy podporuje souběžnost a paralelismus a možnost obnovit operace kopírování při přerušení.
    - **Azure Storage rozhraní REST API/sady SDK** – při sestavování aplikace můžete vyvíjet aplikace proti Azure Storage rozhraní REST API a používat sady Azure SDK nabízené v různých jazycích. Rozhraní REST API mohou také využívat Azure Storage knihovnu pro přesun dat, která je navržena hlavně pro vysoce výkonné kopírování dat do a z Azure.

- **Průběžné** ingestování dat – pro průběžné přijímání probíhajících dat můžete vybrat jednu z data box přenosové zařízení online nebo Azure Data Factory. Tyto nástroje jsou nastavené odborníky na IT a můžou transparentně automatizovat přenos dat.

    - **Azure Data Factory** – Data Factory by se měly používat ke škálování operace přenosu, a pokud je potřeba Orchestrace a možnosti monitorování podnikové úrovně. Pomocí Azure Data Factory můžete nastavit kanál cloudu, který pravidelně přenáší soubory mezi několika službami Azure, místním prostředím nebo kombinací těchto dvou. Azure Data Factory umožňuje orchestrovat pracovní postupy řízené daty, které ingestují data z různorodých úložišť dat a automatizují přesuny dat a transformaci dat.
    - **Azure Data box rodina pro online přenosy** – Data Box Edge a data box Gateway jsou online síťová zařízení, která mohou přesouvat data do a z Azure. Data Box Edge používá k předzpracování dat před nahráním hraniční výpočetní prostředí s povolenou funkcí umělé Intelligence (AI). Data Box Gateway je virtuální verze zařízení se stejnými možnostmi přenosu dat.


## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulka shrnuje rozdíly v klíčových funkcích.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Přenos dat prostřednictvím skriptu nebo programové sítě

| Schopnost                  | AzCopy                                 | Rozhraní REST API pro Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Formulářový faktor                 | Nástroj příkazového řádku od Microsoftu       | Zákazníci, kteří se dovíjejí k úložišti <br> Rozhraní REST API pomocí klientských knihoven Azure |
| Počáteční nastavení jednorázového času     | Minimální                                | Střední, úsilí při vývoji proměnných    |
| Formát dat                 | Objekty blob Azure, soubory Azure, tabulky Azure | Objekty blob Azure, soubory Azure, tabulky Azure   |
| Výkon                 | Už optimalizované                      | Optimalizace při vývoji                  |
| Ceny                     | Bezplatné použití poplatků za výstup dat      | Bezplatné použití poplatků za výstup dat        |

### <a name="continuous-data-ingestion-over-network"></a>Nepřetržité přijímání dat přes síť

| Příznak                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Formulářový faktor                                   | Virtuální zařízení             | Fyzické zařízení          | Služba v Azure Portal, místní Agent                                                            |
| Hardware                                      | Váš hypervisor            | Dodává se společností Microsoft    | Není k dispozici                                                            |
| Počáteční úsilí při nastavení                          | Nízká (<30 minut)            | Střední (~ pár hodin) | Velký (počet dní: ~)                                                 |
| Formát dat                                   | Objekty blob Azure, soubory Azure   | Objekty blob Azure, soubory Azure | [Podporuje 70 + datových konektorů pro úložiště a formáty dat](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Předběžné zpracování dat                           | No                         | Ano, přes hranice výpočetní technologie    | Yes                                                           |
| Místní mezipaměť<br>(ukládat místní data)    | Yes                        | Yes                      | No                                                            |
| Přenos z jiných cloudů                    | No                         | No                       | Yes                                                           |
| Ceny                                       | [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Ceny](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Další kroky

- [Přenos dat pomocí AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Další informace o přenosu dat pomocí rozhraní REST API pro úložiště](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet)
- Informace o tom, jak:
    - [Přenos dat pomocí data box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Umožňuje transformovat data pomocí data box Edge před odesláním do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Naučte se, jak přenést data pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
