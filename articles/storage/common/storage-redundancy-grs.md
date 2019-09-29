---
title: Geograficky redundantní úložiště (GRS) pro odolnost mezi různými regiony v Azure Storage | Microsoft Docs
description: Geograficky redundantní úložiště (GRS) replikuje vaše data mezi dvěma oblastmi, které se od sebe stovky kilometrů. GRS chrání před selháním hardwaru v datovém centru i v regionálních katastrofách.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 16a5f214495025d16d10ee01a7b2a40b78f7a17a
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670809"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Geograficky redundantní úložiště (GRS): Replikace mezi různými oblastmi pro Azure Storage
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Geograficky redundantní úložiště s přístupem pro čtení
Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) maximalizuje dostupnost svého účtu úložiště. RA-GRS poskytuje kromě geografické replikace ve dvou oblastech i přístup jen pro čtení k datům v sekundárním umístění.

Když povolíte přístup jen pro čtení k datům v sekundární oblasti, data jsou k dispozici v sekundárním koncovém bodu i na primárním koncovém bodu vašeho účtu úložiště. Sekundární koncový bod je podobný primárnímu koncovému bodu, ale připojuje příponu `–secondary` k názvu účtu. Pokud je `myaccount.blob.core.windows.net`například primárním koncovým bodem pro BLOB Service, pak je `myaccount-secondary.blob.core.windows.net`sekundárním koncovým bodem. Přístupové klíče pro váš účet úložiště jsou u primárních i sekundárních koncových bodů stejné.

Při použití RA-GRS se Pamatujte na tyto skutečnosti:

* Vaše aplikace musí spravovat, který koncový bod při práci s nástrojem RA-GRS pracuje.
* Vzhledem k tomu, že asynchronní replikace zahrnuje zpoždění, změny, které ještě nebyly replikovány do sekundární oblasti, mohou být ztraceny, pokud nelze obnovit data z primární oblasti.
* Můžete kontrolovat čas poslední synchronizace svého účtu úložiště. Čas poslední synchronizace je hodnota data a času GMT. Všechna primární zápisy před tím, než čas poslední synchronizace byla úspěšně zapsána do sekundárního umístění, což znamená, že jsou k dispozici pro čtení ze sekundárního umístění. Primární zápisy po čas poslední synchronizace se ještě nemusí k dispozici pro čtení. Tuto hodnotu můžete zadat dotazem pomocí [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md)nebo z jedné z Azure Storage klientských knihoven.
* Pokud zahájíte převzetí služeb při selhání (Preview) účtu GRS nebo RA-GRS do sekundární oblasti, bude po dokončení převzetí služeb při selhání obnoven přístup pro zápis k tomuto účtu. Další informace najdete v tématu [obnovení po havárii a převzetí služeb při selhání účtu úložiště (Preview)](storage-disaster-recovery-guidance.md).
* RA-GRS je určené pro účely vysoké dostupnosti. Pokyny k škálovatelnosti najdete v [kontrolním seznamu výkonu](storage-performance-checklist.md).
* Návrhy na návrh vysoké dostupnosti s využitím RA-GRS najdete v tématu [navrhování vysoce dostupných aplikací pomocí úložiště RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co je RPO a RTO s GRS?

**Cíl bodu obnovení (RPO):** Služba úložiště v GRS a RA-GRS asynchronně geograficky replikuje data z primární do sekundárního umístění. V případě, že primární oblast nebude k dispozici, můžete provést převzetí služeb při selhání (Preview) účtu do sekundární oblasti. Když zahájíte převzetí služeb při selhání, může dojít k výpadku nedávných změn, které ještě nebyly geograficky replikovány. Počet minut potenciálních dat, která jsou ztracena, se označuje jako RPO. RPO označuje bod v čase, do kterého lze obnovit data. Azure Storage obvykle má RPO méně než 15 minut, ale v současné době není k dispozici žádná smlouva SLA, jak dlouho trvá geografická replikace.

**Plánovaná doba obnovení (RTO):** RTO je měřítko, jak dlouho trvá provedení převzetí služeb při selhání a získání účtu úložiště zpátky do online režimu. Doba k provedení převzetí služeb při selhání zahrnuje tyto akce:

   * Čas, kdy zákazník iniciuje převzetí služeb při selhání účtu úložiště z primární databáze do sekundární oblasti.
   * Doba, kterou Azure vyžaduje k provedení převzetí služeb při selhání změnou primárních záznamů DNS tak, aby odkazovaly na sekundární umístění.

## <a name="paired-regions"></a>Spárované oblasti 
Při vytváření účtu úložiště vyberete primární oblast pro daný účet. Spárovaná sekundární oblast je určena v závislosti na primární oblasti a nelze ji změnit. Aktuální informace o oblastech podporovaných službou Azure najdete v tématech [provozní kontinuita a zotavení po havárii (BCDR): Spárované oblasti](../../best-practices-availability-paired-regions.md)Azure.

## <a name="see-also"></a>Viz také:
- [Účet replikace Azure Storage](storage-redundancy.md)
- [Místně redundantní úložiště (LRS): Redundance dat pro Azure Storage s nízkou cenou](storage-redundancy-lrs.md)
- [Redundantní úložiště zóny (ZRS): Vysoce dostupné Azure Storage aplikace](storage-redundancy-zrs.md)
