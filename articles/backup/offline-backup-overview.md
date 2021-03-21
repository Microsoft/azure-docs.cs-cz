---
title: Přehled zálohování offline
description: Přečtěte si informace o komponentách offline zálohování. Zahrnují zálohování offline na základě Azure Data Box a offline zálohování založeného na službě Azure import/export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704131"
---
# <a name="overview-of-offline-backup"></a>Přehled zálohování offline

Tento článek poskytuje přehled zálohování offline.

Počáteční úplné zálohování do Azure obvykle přenáší velké objemy dat online a při porovnání s dalšími zálohami, které přenášejí pouze přírůstkové změny, vyžadují větší šířku pásma sítě. Vzdálené pobočky nebo datová centra v určitých geografických oblastech nemají vždy dostatečnou šířku pásma sítě. Z tohoto důvodu budou tyto prvotní zálohy trvat několik dní. Během této doby budou zálohy průběžně používat stejnou síť, která byla zřízena pro aplikace spuštěné v místním datovém centru.

Azure Backup podporuje offline zálohování, které přenáší počáteční zálohovaná data offline bez použití šířky pásma sítě. Poskytuje mechanismus pro kopírování zálohovaných dat do fyzických úložných zařízení. Zařízení se pak dodávají do okolního datacentra Azure a nahrála se do trezoru Recovery Services. Tento proces zajistí robustní přenos zálohovaných dat bez použití šířky pásma sítě.

## <a name="offline-backup-options"></a>Možnosti zálohování offline

Offline zálohování se nabízí ve dvou režimech na základě vlastnictví úložných zařízení:

- Zálohování offline na základě Azure Data Box (Preview)
- Zálohování offline na základě služby Azure import/export

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Zálohování offline na základě Azure Data Box (Preview)

Tento režim se v současné době podporuje u agenta Microsoft Azure Recovery Services (MARS) ve verzi Preview. Tato možnost využívá výhod [Azure Data box](https://azure.microsoft.com/services/databox/) k dodávání zařízení pro přenos, zabezpečení a odolného proti falšování Microsoftu pomocí konektorů USB do vašeho datového centra nebo do vzdálené kanceláře. Zálohovaná data se do těchto zařízení napíší přímo. Tato možnost šetří úsilí potřebné k zajištění vlastních disků a konektorů, které jsou kompatibilní s Azure, nebo k zajištění dočasného úložiště jako pracovního umístění. Microsoft také zpracovává ucelenou logistiku přenosu, kterou můžete sledovat prostřednictvím Azure Portal.

Zde je uvedena architektura, která popisuje přesun zálohovaných dat pomocí této možnosti.

![Architektura Azure Backup Data Box](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Tady je souhrn architektury:

1. Azure Backup přímo kopíruje zálohovaná data do těchto předkonfigurovaných zařízení.
2. Pak můžete tato zařízení dodávat zpátky do datacentra Azure.
3. Azure Data Box zkopíruje data do účtu úložiště ve vlastnictví zákazníka.
4. Azure Backup automaticky kopíruje zálohovaná data z účtu úložiště do určeného trezoru Recovery Services. Probíhá plánování přírůstkových online záloh.

Pokud chcete použít offline zálohování na základě Azure Data Box, přečtěte si téma [zálohování do offline režimu pomocí Azure Data box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Zálohování offline na základě služby Azure import/export

Tuto možnost podporuje Microsoft Azure Backup Server (MABS), aplikace System Center Data Protection Manager (DPM) DPM-A a Agent MARS. Používá [službu Azure import/export](../import-export/storage-import-export-service.md). Data počátečního zálohování můžete přenést do Azure pomocí vlastních disků a konektorů, které jsou kompatibilní s Azure. Tento přístup vyžaduje zřízení dočasného úložiště označovaného jako pracovní umístění a použití předem připravených nástrojů k formátování a zkopírování zálohovaných dat na disky vlastněné zákazníkem.

Zde je uvedena architektura, která popisuje přesun zálohovaných dat pomocí této možnosti.

![Azure Backup architektura služby Import/export](./media/offline-backup-overview/azure-backup-import-export.png)

Tady je souhrn architektury:

1. Místo posílání zálohovaných dat v síti Azure Backup zapisuje zálohovaná data do pracovního umístění.
2. Data v pracovním umístění jsou zapsána do jednoho nebo více disků SATA pomocí vlastního nástroje.
3. V rámci přípravné práce nástroj vytvoří úlohu importu Azure. Jednotky SATA se dodávají do nejbližšího datacentra Azure a odkazují na úlohu importu, aby se tyto aktivity připojily.
4. V datovém centru Azure se data na discích zkopírují do účtu služby Azure Storage.
5. Azure Backup zkopíruje zálohovaná data z účtu úložiště do trezoru Recovery Services. Přírůstkové zálohování je naplánováno.

Pokud chcete použít offline zálohování založené na službě Azure import/export s agentem MARS, přečtěte si téma [pracovní postup offline zálohování v Azure Backup](./backup-azure-backup-import-export.md).

Chcete-li použít stejné společně s MABS nebo DPM-A, přečtěte si [pracovní postup offline zálohování pro DPM a Azure Backup Server](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Souhrn podpory offline zálohování

Následující tabulka porovnává dvě dostupné možnosti, abyste mohli vhodným způsobem vybrat na základě vašeho scénáře.

| **Aspekty**                                            | **Zálohování offline na základě Azure Data Box**                     | **Zálohování offline na základě služby Azure import/export**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup modely nasazení                              | Agent MARS (Preview)                                              | Agent MARS, MABS, DPM-A                                           |
| Maximální počet zálohovaných dat na server (MARS) nebo na skupinu ochrany (MABS, DPM-A) | [Azure Data box disk](../databox/data-box-disk-overview.md) – 7,2 TB <br> [Azure Data box](../databox/data-box-overview.md) – 80 TB       | 80 TB (až 10 disků o 8 TB)                          |
| Zabezpečení (data, zařízení a služba)                           | [Data](../databox/data-box-security.md#data-box-data-protection) -AES 256-bit šifrováno <br> [Zařízení](../databox/data-box-security.md#data-box-device-protection) – robustní – proprietární, rozhraní založené na přihlašovacích údajích ke kopírování dat <br> Funkce zabezpečení Azure, které jsou chráněné [službou](../databox/data-box-security.md#data-box-service-protection) | Data – šifrovaný BitLocker                                 |
| Dočasné zřízení pracovního umístění                     | Nevyžadováno                                                | Větší než nebo rovna odhadované velikosti zálohovaných dat        |
| Podporované oblasti                                           | [Azure Data Box oblasti disku](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box oblasti](../databox/data-box-disk-overview.md#region-availability) | [Oblasti služeb importu a exportu v Azure](../import-export/storage-import-export-service.md#region-availability) |
| Expedice mezi země                                     | Nepodporováno  <br>    Zdrojová adresa a cílové datacentrum Azure musí být ve stejné zemi nebo oblasti *. | Podporováno                                                    |
| Logistika přenosů (doručení, přeprava, vyzvednutí)           | Plně spravované Microsoftem                                     | Spravované zákazníkem                                            |
| Ceny                                                      | [Ceny Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Ceny Azure Data Box disku](https://azure.microsoft.com/pricing/details/databox/disk/) | [Ceny za službu import/export v Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Pokud vaše země nebo oblast nemá datové centrum Azure, musíte disky dodávat do datacentra Azure v jiné zemi nebo oblasti.

## <a name="next-steps"></a>Další kroky

- [Azure Backup offline zálohování pomocí Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Pracovní postup offline zálohování v Azure Backup](backup-azure-backup-import-export.md)
- [Pracovní postup offline zálohování pro DPM a Azure Backup Server](backup-azure-backup-server-import-export.md)
