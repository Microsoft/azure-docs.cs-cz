---
title: Co je Azure Backup?
description: Poskytuje přehled služby Azure Backup a její přispívá ke strategii kontinuity podnikání a zotavení po havárii (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240293"
---
# <a name="what-is-the-azure-backup-service"></a>Co je služba Azure Backup?

Služba Azure Backup poskytuje jednoduchá, zabezpečená a cenově výhodná řešení pro zálohování vašich dat a jejich obnovení z cloudu Microsoft Azure.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Co mohu zálohovat?

- **Místní** – Zálohování souborů, složek, stavu systému pomocí [agenta Microsoft Azure Recovery Services (MARS).](backup-support-matrix-mars-agent.md) Nebo použijte agenta DPM nebo Azure Backup Server (MABS) k ochraně místních virtuálních počítačů[(Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) a [VMWare)](backup-azure-backup-server-vmware.md)a [dalších místních úloh](backup-mabs-protection-matrix.md)
- **Virtuální počítače** - Azure[Zálohovat celé virtuální počítače windows/linux](backup-azure-vms-introduction.md) (pomocí rozšíření zálohování) nebo zálohovat soubory, složky a stav systému pomocí [agenta MARS](backup-azure-manage-mars.md).
- **Sdílené soubory Azure Zálohování** - [sdílených složek Azure na účet úložiště](backup-afs.md)
- **SQL Server ve virtuálních počítačích** -  Azure[Zálohování databází SQL Serveru spuštěných na virtuálních počítačích Azure](backup-azure-sql-database.md)
- **Databáze SAP HANA v databázích Zálohování virtuálních operátorů** - [Azure SAP HANA spuštěné na virtuálních počítačích Azure](backup-azure-sap-hana-database.md)

![Přehled zálohování Azure](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Proč používat Azure Backup?

Azure Backup přináší tyto klíčové výhody:

- **Přetížit místní zálohování**: Azure Backup nabízí jednoduché řešení pro zálohování místních prostředků do cloudu. Získejte krátkodobé a dlouhodobé zálohování bez nutnosti nasazovat komplexní místní řešení zálohování.
- **Zálohování virtuálních počítačů Azure IaaS**: Azure Backup poskytuje nezávislé a izolované zálohy, které chrání před náhodným zničením původních dat. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení. Konfigurace a škálovatelnost jsou jednoduché, zálohy jsou optimalizovány a můžete je snadno obnovit podle potřeby.
- **Snadné škálování** – Azure Backup využívá základní výkon a neomezené škálování cloudu Azure k poskytování vysoké dostupnosti bez režie na údržbu nebo monitorování.
- **Získejte neomezený přenos dat**: Azure Backup neomezuje množství příchozích nebo odchozích dat, která přenášíte, ani neúčtuje poplatky za přenesená data.
  - Odchozí data označují data přenášená z trezoru služby Recovery Services během operace obnovení.
  - Pokud provedete počáteční zálohu offline pomocí služby Azure Import/Export k importu velkého množství dat, budou s příchozími daty spojeny náklady.  [Další informace](backup-azure-backup-import-export.md).
- **Zabezpečení dat**: Azure Backup poskytuje řešení pro zabezpečení dat [při přenosu](backup-azure-security-feature.md) a [v klidovém stavu](backup-azure-security-feature-cloud.md).
- **Centralizované monitorování a správa**: Azure Backup poskytuje [integrované funkce monitorování a upozorňování](backup-azure-monitoring-built-in-monitor.md) v trezoru služby Recovery Services. Tyto funkce jsou k dispozici bez jakékoli další infrastruktury pro správu. Můžete také zvýšit rozsah monitorování a vytváření sestav [pomocí Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Získejte zálohy konzistentní s aplikací**: Záloha konzistentní s aplikací znamená, že bod obnovení má všechna požadovaná data k obnovení záložní kopie. Azure Backup poskytuje zálohy konzistentní s aplikacemi, které zajišťují, že k obnovení dat nejsou nutné další opravy. Obnovování dat konzistentních s aplikací zkracuje čas obnovení, což vám umožní rychle se vrátit do funkčního stavu.
- **Uchovávejte krátkodobá a dlouhodobá data**: [Trezory služby Recovery Services](backup-azure-recovery-services-vault-overview.md) můžete použít pro krátkodobé a dlouhodobé uchovávání dat.
- **Automatická správa úložiště** – Hybridní prostředí často vyžadují heterogenní úložiště – některá místní a některá cloudová. S Azure Backup se nestojí za používání místních úložných zařízení. Azure Backup automaticky přiděluje a spravuje úložiště záloh a používá model založený na průběžných platbách. Takže platíte pouze za úložiště, které konzumujete. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/backup) o cenách.
- **Více možností úložiště** – Azure Backup nabízí dva typy replikace, aby vaše úložiště nebo data vysoce dostupné.
  - [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) replikuje data třikrát (vytvoří tři kopie dat) v jednotce škálování úložiště v datovém centru. Všechny kopie dat existují ve stejné oblasti. Místně redundantní úložiště nabízí cenově úsporný způsob ochrany dat před selháním místního hardwaru.
  - [Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). GRS stojí více než LRS, ale GRS poskytuje vyšší úroveň odolnosti pro vaše data, i když dojde k regionálnímu výpadku.

## <a name="next-steps"></a>Další kroky

- [Zkontrolujte](backup-architecture.md) architekturu a součásti pro různé scénáře zálohování.
- [Ověřte](backup-support-matrix.md) požadavky na podporu a omezení pro zálohování a zálohování [virtuálních počítačích Azure](backup-support-matrix-iaas.md).
