---
title: Přehled zálohování offline
description: Seznamte se s komponentami offline zálohování. Patří mezi ně offline zálohování založené na Azure Data Box a offline zálohování na základě služby Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196148"
---
# <a name="overview-of-offline-backup"></a>Přehled zálohování offline

Tento článek poskytuje přehled zálohování offline.

Počáteční úplné zálohy do Azure obvykle přenášejí velké objemy dat online a vyžadují větší šířku pásma sítě ve srovnání s následnými zálohami, které přenášejí pouze přírůstkové změny. Vzdálené kanceláře nebo datová centra v určitých zeměpisných oblastech nemají vždy dostatečnou šířku pásma sítě. Z tohoto důvodu tyto počáteční zálohy trvat několik dní. Během této doby zálohy nepřetržitě používat stejnou síť, která byla zřízena pro aplikace spuštěné v místním datovém centru.

Azure Backup podporuje zálohování offline, které přenáší počáteční záložní data offline bez použití šířky pásma sítě. Poskytuje mechanismus kopírování záložních dat do zařízení fyzického úložiště. Zařízení se pak dodávají do blízkého datového centra Azure a nahrají se do trezoru služby Recovery Services. Tento proces zajišťuje robustní přenos záložních dat bez použití libovolné šířky pásma sítě.

## <a name="offline-backup-options"></a>Možnosti zálohování offline

Zálohování offline je nabízeno ve dvou režimech založených na vlastnictví úložných zařízení:

- Offline zálohování založené na Azure Data Box (preview)
- Offline zálohování založené na službě Import a export Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offline zálohování založené na Azure Data Box (preview)

Tento režim je aktuálně podporován agentem Microsoft Azure Recovery Services (MARS) ve verzi Preview. Tato možnost využívá [Azure Data Box](https://azure.microsoft.com/services/databox/) k přepravě proprietárních, zabezpečených a nefalšovacích přenosových zařízení s USB konektory do vašeho datového centra nebo vzdálené kanceláře. Záložní data jsou přímo zapsána do těchto zařízení. Tato možnost šetří úsilí potřebné k získání vlastní disky a konektory kompatibilní s Azure nebo zřídit dočasné úložiště jako pracovní umístění. Microsoft také zpracovává logistiku přenosu od konce do konce, kterou můžete sledovat prostřednictvím portálu Azure. 

Zde je zobrazena architektura, která popisuje přesun záložních dat pomocí této možnosti.

![Architektura Azure Backup Data Box](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Zde je shrnutí architektury:

1. Azure Backup přímo zkopíruje záložní data do těchto předkonfigurovaných zařízení.
2. Potom můžete tato zařízení poslat zpět do datového centra Azure.
3. Azure Data Box zkopíruje data do účtu úložiště vlastněného zákazníkem.
4. Azure Backup automaticky zkopíruje záložní data z účtu úložiště do určeného trezoru služby Recovery Services. Jsou naplánovány přírůstkové zálohování online.

Pokud chcete použít offline zálohování založené na Azure Data Box, přečtěte si [tématu Zálohování offline pomocí Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline zálohování založené na službě Import a export Azure

Tato možnost je podporována Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) DPM-A a agent MARS. Používá [službu Azure Import/Export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service). Počáteční záložní data do Azure můžete přenést pomocí vlastních disků a konektorů kompatibilních s Azure. Tento přístup vyžaduje zřízení dočasného úložiště označované jako pracovní umístění a použití předem připravených nástrojů k formátování a kopírování záložních dat na disky vlastněné zákazníkem. 

Zde je zobrazena architektura, která popisuje přesun záložních dat pomocí této možnosti.

![Architektura služby Import a export záloh Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Zde je shrnutí architektury:

1. Namísto odesílání záložních dat po síti azure backup zapíše záložní data do pracovního umístění.
2. Data v pracovním umístění jsou zapsána na jeden nebo více disků SATA pomocí vlastního nástroje.
3. Jako součást přípravné práce nástroj vytvoří úlohu importu Azure. Jednotky SATA jsou dodávány do nejbližšího datového centra Azure a odkazují na úlohu importu pro připojení aktivit.
4. V datovém centru Azure se data na discích zkopírují do účtu úložiště Azure.
5. Azure Backup zkopíruje záložní data z účtu úložiště do trezoru služby Recovery Services. Přírůstkové zálohování jsou naplánovány.

Pokud chcete použít offline zálohování založené na službě Import a Export Azure s agentem MARS, přečtěte si [článek Pracovní postup zálohování offline v azure backupu](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Chcete-li použít totéž společně s MABS nebo DPM-A, přečtěte si [informace o pracovním postupu zálohování offline pro aplikace DPM a Server zálohování Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Souhrn podpory zálohování offline

Následující tabulka porovnává dvě dostupné možnosti, takže můžete provést příslušné volby na základě vašeho scénáře.

| **Aspekty**                                            | **Offline zálohování založené na Azure Data Boxu**                     | **Offline zálohování založené na službě Import a export Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modely nasazení Azure Backup                              | Agent MARS (náhled)                                              | MARS Agent, MABS, DPM-A                                           |
| Maximální počet záložních dat na server (MARS) nebo na skupinu ochrany (MABS, DPM-A) | [Disk Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) – 7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) – 80 TB       | 80 TB (až 10 disků po 8 TB)                          |
| Zabezpečení (data, zařízení a služba)                           | [Data](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) – 256bitová šifrovaná AES <br> [Zařízení](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Robustní pouzdro, proprietární rozhraní založené na pověření pro kopírování dat <br> [Služba](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) – chráněná funkcemi zabezpečení Azure | Data – zašifrováno nástroj BitLocker                                 |
| Dočasné zřizování dočasného pracovního umístění                     | Není požadováno                                                | Více než nebo rovno odhadované velikosti záložních dat        |
| Podporované oblasti                                           | [Oblasti disku Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Oblasti datové schránky Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Oblasti služby Import a export Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Přeshraniční doprava                                     | Nepodporuje se  <br>    Zdrojová adresa a cílové datové centrum Azure musí být ve stejné zemi* | Podporuje se                                                    |
| Logistika transferů (dodávka, doprava, vyzvednutí)           | Plně spravovaný Microsoft                                     | Zákazník řízený                                            |
| Ceny                                                      | [Ceny azure datové schránky](https://azure.microsoft.com/pricing/details/databox/) <br> [Ceny disku Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Ceny za služby Azure importu a exportu](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Pokud vaše země nemá datové centrum Azure, musíte je poslat do datového centra Azure v jiné zemi.

## <a name="next-steps"></a>Další kroky

* [Zálohování Azure offline zálohování pomocí Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Pracovní postup zálohování offline v Azure Backup](backup-azure-backup-import-export.md) 
* [Pracovní postup zálohování offline pro DPM a Azure Backup Server](backup-azure-backup-server-import-export-.md)
