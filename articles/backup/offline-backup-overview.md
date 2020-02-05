---
title: Přehled zálohování offline
description: Přečtěte si o různých součástech zálohování offline, včetně Azure Data Box zálohování v režimu offline a Azure import/export na základě offline zálohování.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027014"
---
# <a name="overview-of-offline-backup"></a>Přehled zálohování offline

Tento článek poskytuje přehled zálohování offline.

Počáteční úplné zálohování do Azure obvykle přenáší velké objemy dat online a při porovnání s dalšími zálohami, které přenášejí pouze přírůstkové změny, vyžadují větší šířku pásma sítě. Vzdálené pobočky nebo datová centra v určitých geografických oblastech nemají vždy dostatečnou šířku pásma sítě. Proto tyto prvotní zálohy trvá několik dní a během této doby nepřetržitě používají stejnou síť, která byla zřízena pro aplikace spuštěné v místním datovém centru.

Azure Backup podporuje "offline zálohování", které umožňuje přenos počátečních dat zálohování offline bez použití šířky pásma sítě. Poskytuje mechanismus pro kopírování zálohovaných dat do fyzických úložných zařízení, která se pak odesílají do okolního datacentra Azure a nahrála se do úložiště Recovery Services. Tento proces zajistí robustní přenos zálohovaných dat bez použití šířky pásma sítě.

## <a name="offline-backup-options"></a>Možnosti zálohování offline

Offline – zálohování se nabízí ve dvou režimech na základě vlastnictví úložných zařízení.

1. Zálohování offline založené na Azure Data Box (Preview)
2. Offline zálohování založené na importu a exportu Azure

## <a name="azure-data-box-based-offline-backup-preview"></a>Zálohování offline založené na Azure Data Box (Preview)

Tento režim se v současné době podporuje v agentovi MARS ve verzi Preview. Tato možnost využívá [službu Azure Data box](https://azure.microsoft.com/services/databox/) k dodávání kancelářských, zabezpečených a odolných přenosů zařízení s přímým zápisem pomocí konektorů USB, do vašeho datového centra nebo ze vzdáleného Office a data záloh se do těchto zařízení zapisují přímo. **Tato možnost šetří úsilí potřebné k zajištění vlastních disků a konektorů, které jsou kompatibilní s Azure, nebo zřízení dočasného úložiště jako pracovního umístění.** Společnost Microsoft navíc zpracovává ucelenou logistiku přenosu, kterou můžete sledovat prostřednictvím Azure Portal. Níže je uvedena architektura popisující přesun zálohovaných dat pomocí této možnosti.

![Architektura Azure Backup Data Box](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Tady je souhrn architektury:

1. Azure Backup přímo kopíruje zálohovaná data do těchto předem nakonfigurovaných zařízení.
2. Pak můžete tato zařízení dodávat zpátky do datacentra Azure.
3. Služba Azure Data Box zkopíruje data do účtu úložiště ve vlastnictví zákazníka.
4. Azure Backup automaticky kopíruje zálohovaná data z účtu úložiště do určeného trezoru Recovery Services a naplánuje se přírůstkové online zálohování.

Pokud chcete použít offline zálohování založené na Azure Data Box, přečtěte si [Tento článek](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Offline zálohování založené na importu a exportu Azure

Tuto možnost podporuje agent Azure Backup Server (MABS)/DPM-A/MARS. Využívá [službu Azure import/export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) , která umožňuje přenos počátečních zálohovaných dat do Azure pomocí vlastních disků a konektorů, které jsou kompatibilní s Azure. Tento přístup vyžaduje zřízení dočasného úložiště označovaného jako **pracovní umístění** a použití předem připravených nástrojů k formátování a zkopírování zálohovaných dat na disky vlastněné zákazníkem. Architektura, která popisuje přesun zálohovaných dat pomocí této možnosti, je uvedená níže:

![Azure Backup architektury importu/exportu](./media/offline-backup-overview/azure-backup-import-export.png)

Tady je souhrn architektury:

1. Místo posílání zálohovaných dat v síti Azure Backup zapisuje zálohovaná data do pracovního umístění.
2. Data v pracovním umístění se zapisují na jeden nebo více disků SATA pomocí vlastního nástroje.
3. V rámci přípravné práce nástroj vytvoří úlohu importu Azure. Jednotky SATA se dodávají do nejbližšího datacentra Azure a odkazují na úlohu importu pro propojení aktivit.
4. V datovém centru Azure se data na discích zkopírují do účtu služby Azure Storage.
5. Azure Backup zkopíruje zálohovaná data z účtu úložiště do trezoru Recovery Services a naplánují se přírůstkové zálohy.

Pokud chcete použít offline zálohování založené na importu a exportu Azure s agentem MARS, přečtěte si [Tento článek](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Chcete-li použít stejné společně s MABS/DPM-A, přečtěte si [Tento článek](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Souhrn podpory offline zálohování

Následující tabulka porovnává dvě dostupné možnosti, takže můžete v závislosti na svém scénáři provést příslušné volby.

| **Aspekty**                                            | **Zálohování offline založené na Azure Data Box**                     | **Offline zálohování založené na importu a exportu Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup modely nasazení                              | Agent MARS (Preview)                                              | Agent MARS, Azure Backup Server (MABS), DPM-A                                           |
| Maximální počet zálohovaných dat na server (MARS) nebo na skupinu ochrany (MABS, DPM-A) | [Azure Data box disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) – 7,2 TB <br> [Azure Data box](https://docs.microsoft.com/azure/databox/data-box-overview) – 80 TB       | 80 TB (až 10 disků o 8 TB)                          |
| Zabezpečení (data, služba & zařízení)                           | [Data](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256 bitová šifrování <br> [Zařízení](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) – robustní – proprietární rozhraní založené na přihlašovacích údajích ke kopírování dat <br> Funkce zabezpečení Azure, které jsou chráněné [službou](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | Data – šifrovaný BitLocker                                 |
| Dočasné zřízení pracovního umístění                     | Nepožadováno                                                | Větší než nebo rovna odhadované velikosti zálohovaných dat        |
| Podporované oblasti                                           | [Azure Data Box Disk oblasti](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box oblasti](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Oblasti importu a exportu v Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Expedice mezi země *                                     | **Nepodporováno**  <br>    *Zdrojová adresa & cílový datacentrum Azure musí být ve stejné zemi.* | Podporováno                                                    |
| Logistika přenosů (doručení, přeprava, výběr)           | Plně spravované Microsoftem                                     | Spravované zákazníkem                                            |
| Ceny                                                      | [Ceny Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Ceny Azure Data Box Disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Ceny za import/export Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Pokud vaše země nemá datové centrum Azure, musíte disky dodávat do datacentra Azure v jiné zemi.*

## <a name="next-steps"></a>Další kroky

* [Online zálohování na základě Azure Data Box pro agenta MARS](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure import/export založený na offline zálohování pro agenta MARS](backup-azure-backup-import-export.md)  
* [Azure import/export založený na offline zálohování pro MABS/DPM – A](backup-azure-backup-server-import-export-.md)
