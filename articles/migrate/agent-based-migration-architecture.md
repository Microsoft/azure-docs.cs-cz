---
title: Migrace založená na agentech v migraci serveru Azure Migrate
description: Poskytuje přehled migrace virtuálních počítačů VMware založených na agentech pomocí migrace serveru Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a8477b4c10ccbc76f36eed4d64ac12e8bb648a28
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186104"
---
# <a name="agent-based-migration-architecture"></a>Architektura migrace založené na agentech

Tento článek poskytuje přehled architektury a procesů používaných pro replikaci založenou na agentech pomocí nástroje pro migraci serveru Azure Migrate.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a instancí virtuálních počítačů AWS/GCP do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

## <a name="agent-based-replication"></a>Replikace založená na agentovi

Replikace na základě agenta v nástroji replikace serveru Azure Migrate slouží k migraci místních virtuálních počítačů VMware a fyzických serverů do Azure. Dá se taky použít k migraci dalších místních virtualizovaných serverů a virtuálních počítačů privátních a veřejných cloudů, včetně instancí AWS a virtuálních počítačů s GCP.

Pro migraci VMware nabízí nástroj pro migraci Azure Migrate serveru několik možností:

- Migrace pomocí replikace založené na agentech, jak je popsáno v tomto článku.
- Replikace bez agenta, pro migraci virtuálních počítačů, aniž by bylo nutné instalovat cokoli.

Přečtěte si další informace o [výběru metody migrace pro VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migrace serveru a Azure Site Recovery

Migrace Azure Migrate serveru je nástroj pro migraci místních a veřejných cloudových úloh do Azure. Je optimalizovaná pro migraci. Site Recovery je nástroj pro zotavení po havárii. Migrace a Site Recovery v Azure serveru sdílejí některé společné technologické komponenty, které se používají k replikaci dat, ale slouží k různým účelům.

## <a name="architectural-components"></a>Komponenty architektury

![Architektura](./media/agent-based-replication-architecture/architecture.png)

Tabulka shrnuje součásti používané pro migraci na základě agenta.

**Komponenta** | **Podrobnosti** | **Instalace**
--- | --- | ---
**Replikační zařízení** | Zařízení replikace (konfigurační server) je místní počítač, který funguje jako most mezi místním prostředím a nástrojem pro migraci Azure Migrate serveru. Zařízení zjistí místní inventář virtuálních počítačů, aby mohla migrace serveru Azure orchestrovat replikaci a migraci. Zařízení má dvě komponenty:<br/><br/> **Konfigurační server**: připojuje se k migraci Azure Migrate serveru a koordinuje replikaci.<br/> **Procesový Server**: zpracovává replikaci dat. Přijímá data virtuálních počítačů, komprimuje je a šifruje a odesílá je do předplatného Azure. Migrace serveru zapisuje data na spravované disky. | Ve výchozím nastavení se procesový server instaluje společně s konfiguračním serverem na zařízení replikace.
**Služba Mobility** | Služba mobility je agent nainstalovaný na každém počítači, který chcete replikovat a migrovat. Odesílá data replikace z počítače na procesový Server. K dispozici je několik různých agentů služby mobility. | Instalační soubory služby mobility jsou umístěné na zařízení replikace. Můžete stáhnout a nainstalovat agenta, který potřebujete, v souladu s operačním systémem a verzí počítače, který chcete replikovat.

### <a name="mobility-service-installation"></a>Instalace služby Mobility

Službu mobility můžete nasadit pomocí následujících metod:

- **Nabízená instalace**: Služba mobility je nainstalovaná procesovým serverem, když povolíte ochranu počítače. 
- **Ruční instalace**: službu mobility můžete nainstalovat ručně na každém počítači prostřednictvím uživatelského rozhraní nebo příkazového řádku.

Služba mobility komunikuje se zařízením replikace a replikovanými počítači. Pokud máte antivirový software spuštěný na zařízení replikace, procesových serverech nebo počítačích, které se replikují, měly by se z kontroly vyloučit následující složky:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (na počítačích s Windows, ve kterých je nainstalovaná služba mobility)

## <a name="replication-process"></a>Proces replikace

1. Když pro virtuální počítač povolíte replikaci, spustí se počáteční replikace do Azure.
2. Během počáteční replikace služba mobility načítá data z disků počítače a odesílá je na procesový Server.
3. Tato data se používají k osazení kopie disku v předplatném Azure. 
4. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Replikace je na úrovni bloku a téměř nepřetržitá.
4. Služba mobility zachycuje zápisy do paměti disku virtuálního počítače integrací se subsystémem úložiště operačního systému. Tato metoda zabrání vstupně-výstupním operacím disku na replikačním počítači pro přírůstkovou replikaci. 
5. Sledované změny pro počítač se odesílají na procesový Server na portu HTTPS 9443 příchozí. Tento port lze změnit. Procesový Server ho zkomprimuje a zašifruje a pošle ho do Azure. 

## <a name="ports"></a>Porty

**Zařízení** | **Vázán**
--- | --- 
Virtuální počítače | Služba mobility spuštěná na virtuálních počítačích komunikuje s místním zařízením replikace na portu HTTPS 443 příchozí, pro správu replikací.<br/><br/> Virtuální počítače odesílají data replikace na procesový Server (ve výchozím nastavení běží na zařízení replikace) na portu HTTPS 9443 příchozí. Tento port lze změnit.
Replikační zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový Server | Procesový server přijímá data replikace, optimalizuje je a šifruje a odesílá je do Azure Storage přes odchozí port 443.


## <a name="performance-and-scaling"></a>Výkon a škálování

Ve výchozím nastavení nasadíte jediné zařízení replikace, na kterém běží konfigurační server a procesový Server. Pokud pouze provádíte replikaci několika počítačů, toto nasazení je dostatečné. Pokud ale provádíte replikaci a migrujete stovky počítačů, jeden procesový server nemusí být schopný zpracovat veškerý provoz replikace. V takovém případě můžete nasadit další procesové servery se škálováním na více instancí.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Plánovač nasazení Site Recovery pro VMware

Pokud provádíte replikaci virtuálních počítačů VMware, můžete použít [Plánovač nasazení Site Recovery](../site-recovery/site-recovery-deployment-planner.md) pro VMware, které vám pomůžou určit požadavky na výkon, včetně denní frekvence změny dat a procesových serverů, které potřebujete.

### <a name="replication-appliance-capacity"></a>Kapacita zařízení replikace

Hodnoty v této tabulce lze použít k zjištění, zda v nasazení potřebujete další procesový Server.

- Pokud je frekvence denních změn (četnost změn) větší než 2 TB, nasaďte další procesový Server.
- Pokud provádíte replikaci více než 200 počítačů, nasaďte další replikační zařízení.

**VČETNĚ** | **Rezident** | **Volné místo pro ukládání dat do mezipaměti** | **Míra četnosti změn** | **Omezení replikace**
--- | --- | --- | --- | ---
8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | počítače s < 100 
12 vCPU (2 sokety × 6 jader \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | 100-150 počítačů.
16 vCPU (2 sokety × 8 jader \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB až 2 TB | 151-200 počítačů.

### <a name="scale-out-process-server-sizing"></a>Velikost procesového serveru se škálováním na více instancí

Pokud potřebujete nasadit procesový Server se škálováním na více instancí, tato tabulka vám může pomáhat zjistit velikost serveru.

**Procesový server** | **Volné místo pro ukládání dat do mezipaměti** | **Míra četnosti změn** | **Omezení replikace**
--- | --- | --- | --- 
4 vCPU (2 sokety × 2 jádra \@ 2,5 GHz), 8 GB paměti | 300 GB | 250 GB nebo méně | Až 85 počítačů 
8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 251 GB až 1 TB    | 86-150 počítačů.
12 vCPU (2 sokety × 6 jader \@ 2,5 GHz), 24 GB paměti | 1 TB | 1-2 TB | 151-225 počítačů.

## <a name="control-upload-throughput"></a>Řízení propustnosti odesílání


 Provoz VMware, který se replikuje do Azure, prochází přes konkrétní procesový Server. Propustnost nahrávání můžete omezit omezením šířky pásma na počítačích, které jsou spuštěny jako procesové servery. Šířku pásma můžete ovlivnit pomocí tohoto klíče registru:

- Hodnota registru HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM určuje počet vláken, která se používají pro přenos dat (počáteční nebo rozdílovou replikaci) disku. Vyšší hodnota zvyšuje šířku pásma sítě, která se používá pro replikaci. Výchozí hodnota je 4. Maximální hodnota je 32. Monitorováním provozu hodnotu optimalizujte.
- Navíc můžete omezit šířku pásma na počítači procesového serveru následujícím způsobem:

    1. Na počítači procesového serveru otevřete modul snap-in Azure Backup MMC. Je k dispozici zástupce na ploše nebo ve složce C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. V modulu snap-in vyberte **změnit vlastnosti**.
    3. V případě **omezení**vyberte možnost **Povolit omezování šířky pásma internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou od 512 do 1 023 MB/s.


## <a name="next-steps"></a>Další kroky

Vyzkoušejte [migraci virtuálních počítačů VMware](tutorial-migrate-vmware-agent.md) založenou na agentech pomocí migrace serveru Azure Migrate.
