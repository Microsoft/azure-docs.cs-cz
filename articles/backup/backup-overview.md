---
title: Co je Azure Backup?
description: Poskytuje přehled služby Azure Backup a jejich podíl na strategii pro provozní kontinuitu a zotavení po havárii (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 537605498fe166b24bd4e24673cfa1542bc449ea
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172470"
---
# <a name="what-is-the-azure-backup-service"></a>Co je služba Azure Backup?

Služba Azure Backup poskytuje jednoduchá, zabezpečená a cenově výhodná řešení pro zálohování vašich dat a jejich obnovení z cloudu Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Co můžu zálohovat?

- **Místní** zálohování souborů, složek, stavu systému pomocí [agenta Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md). Nebo použijte agenta DPM nebo Azure Backup Server (MABS) k ochraně místních virtuálních počítačů ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) a [VMware](backup-azure-backup-server-vmware.md)) a dalších [místních úloh](backup-mabs-protection-matrix.md) .
- **Virtuální počítače Azure**  -  [Zazálohujte všechny virtuální počítače s Windows/Linux](backup-azure-vms-introduction.md) (pomocí rozšíření zálohování) nebo zálohujte soubory, složky a stav systému pomocí [agenta Mars](backup-azure-manage-mars.md).
- **Sdílené složky**  -  Azure Files [Zálohování sdílených složek Azure do účtu úložiště](backup-afs.md)
- **SQL Server ve virtuálních počítačích Azure**  -   [Zálohování SQL Server databází běžících na virtuálních počítačích Azure](backup-azure-sql-database.md)
- **SAP HANA databáze na virtuálních počítačích Azure**  -  [Zálohování SAP HANA databází běžících na virtuálních počítačích Azure](backup-azure-sap-hana-database.md)
- **Servery Azure Database for PostgreSQL (Preview)**  -   [Zálohování databází Azure PostgreSQL a uchovávání záloh po dobu až 10 let](backup-azure-database-postgresql.md)

![Přehled Azure Backup](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Proč používat Azure Backup?

Azure Backup přináší tyto klíčové výhody:

- **Přesměrování místního zálohování**: Azure Backup nabízí jednoduché řešení pro zálohování místních prostředků do cloudu. Získejte krátkodobé a dlouhodobé zálohování bez nutnosti nasazovat složitá místní řešení pro zálohování.
- **Zálohování virtuálních počítačů Azure IaaS**: Azure Backup poskytuje nezávislé a izolované zálohy pro ochranu před náhodným zničením původních dat. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení. Konfigurace a škálovatelnost jsou jednoduché, zálohy jsou optimalizované a můžete je snadno obnovit podle potřeby.
- **Snadné škálování** – Azure Backup využívá základní výkon a neomezené škálování cloudu Azure pro zajištění vysoké dostupnosti bez režie údržby a monitorování.
- **Získání neomezeného přenosu dat**: Azure Backup neomezuje množství příchozích a odchozích dat, která přenášíte, nebo poplatky za přenesená data.
  - Odchozí data označují data přenášená z trezoru služby Recovery Services během operace obnovení.
  - Pokud provedete offline prvotní zálohování pomocí služby Azure import/export k importu velkých objemů dat, budou se vám pro příchozí data přidružit náklady.  [Další informace](backup-azure-backup-import-export.md).
- **Zabezpečení dat**: Azure Backup poskytuje řešení pro zabezpečení dat [při přenosu](backup-azure-security-feature.md) a [v klidovém](backup-azure-security-feature-cloud.md)provozu.
- **Centralizované monitorování a Správa**: Azure Backup poskytují [integrované funkce monitorování a upozorňování](backup-azure-monitoring-built-in-monitor.md) v trezoru Recovery Services. Tyto možnosti jsou k dispozici bez jakékoli další infrastruktury pro správu. Můžete také zvýšit měřítko monitorování a vytváření sestav [pomocí Azure monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Získání záloh konzistentních vzhledem**k aplikacím: záloha konzistentní vzhledem k aplikacím znamená, že bod obnovení obsahuje všechna požadovaná data pro obnovení záložní kopie. Azure Backup poskytuje zálohy konzistentní s aplikacemi, které zajistí, že se pro obnovení dat nevyžadují další opravy. Obnovování dat konzistentních s aplikací zkracuje čas obnovení, což vám umožní rychle se vrátit do funkčního stavu.
- **Uchování krátkodobých a dlouhodobých dat**: [Recovery Services trezory](backup-azure-recovery-services-vault-overview.md) můžete použít k krátkodobému a dlouhodobému uchovávání dat.
- **Automatická správa úložiště** – Hybridní prostředí často vyžadují heterogenní úložiště – některá místní a některá cloudová. S Azure Backup se neúčtují žádné náklady na používání místních úložných zařízení. Azure Backup automaticky přiděluje a spravuje úložiště záloh a používá model založený na průběžných platbách. Platíte jenom za úložiště, které spotřebováváte. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/backup) o cenách.
- **Více možností úložiště** – Azure Backup nabízí dva typy replikace k zajištění vysoké dostupnosti úložiště/dat.
  - [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datacentru. Všechny kopie dat existují ve stejné oblasti. Místně redundantní úložiště nabízí cenově úsporný způsob ochrany dat před selháním místního hardwaru.
  - [Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). GRS náklady jsou větší než LRS, ale GRS poskytuje vyšší úroveň trvanlivosti vašich dat, i když dojde k oblastnímu výpadku.
  - [Zóna – redundantní úložiště (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikuje vaše data do [zón dostupnosti](../availability-zones/az-overview.md#availability-zones)a zaručuje jejich započet a odolnost dat ve stejné oblasti. ZRS nemá žádný výpadek. Takže vaše důležité úlohy, které vyžadují zajistěení [dat](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)a musí být bez výpadků, se dají zálohovat v ZRS.

## <a name="next-steps"></a>Další kroky

- [Projděte si](backup-architecture.md) architekturu a součásti pro různé scénáře zálohování.
- [Ověřte](backup-support-matrix.md) požadavky na podporu a omezení pro zálohování a [zálohování virtuálních počítačů Azure](backup-support-matrix-iaas.md).