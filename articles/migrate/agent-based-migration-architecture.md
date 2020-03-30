---
title: Migrace založená na agentovi v migraci serveru Azure
description: Obsahuje přehled migrace virtuálních zařízení VMware založených na agentovi v Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425849"
---
# <a name="agent-based-migration-architecture"></a>Architektura migrace založené na agentech

Tento článek obsahuje přehled architektury a procesů používaných pro replikaci virtuálních počítačích VMware na základě agenta pomocí nástroje [Migrace: Migrace serveru Azure.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Pomocí migrace Azure: Migrace serveru můžete replikovat virtuální počítače VMware s několika možnostmi:

- Migrujte virtuální chod pomocí replikace založené na agentovi, jak je popsáno v tomto článku.
- Migrujte virtuální chod v systému VMware pomocí replikace bez agenta. To migruje virtuální chod, aniž by bylo nutné nic instalovat na ně.

Přečtěte si další informace o [výběru a porovnání](server-migrate-overview.md) metod migrace pro virtuální vytažné oblasti VMware. 


## <a name="agent-based-migration"></a>Migrace založená na agentovi

Migrace založená na agentovi se používá k migraci místních virtuálních počítačů VMware a fyzických serverů do Azure. Dá se také použít k migraci dalších místních virtualizovaných serverů, stejně jako virtuálních počítačích privátního a veřejného cloudu, včetně instancí AWS, a virtuálních počítačích GCP. Migrace založená na agentovi v Azure Migrate používá některé back-endové funkce ze služby [Azure Site Recovery.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Komponenty architektury

Diagram znázorňuje součásti, které se účastní migrace založené na agentovi.

![Architektura](./media/agent-based-replication-architecture/architecture.png)

Tabulka shrnuje součásti používané pro migraci založenou na agentovi.

**Komponenta** | **Podrobnosti** | **Instalace**
--- | --- | ---
**Zařízení pro replikaci** | Zařízení pro replikaci (konfigurační server/procesní server) je místní počítač, který funguje jako most mezi místním prostředím a migrací serveru. Zařízení zjišťuje inventář místního počítače, takže migrace serveru může organizovat replikaci a migraci. Přístroj má dvě součásti:<br/><br/> **Konfigurační server**: Připojuje se k migraci serveru a koordinuje replikaci.<br/> **Procesní server**: Zpracovává replikaci dat. Procesní server přijímá počítačová data, komprimuje je a šifruje a odesílá do Azure. V Azure migrace serveru zapíše data na spravované disky. | Ve výchozím nastavení je procesní server nainstalován společně s konfiguračním serverem v zařízení replikace.
**Služba Mobility** | Služba Mobility je agent nainstalovaný v každém počítači, který chcete replikovat a migrovat. Odešle data replikace ze zařízení na procesní server. | Instalační soubory pro různé verze služby Mobility jsou umístěny na zařízení replikace. Můžete stáhnout a nainstalovat agenta, který potřebujete, v souladu s operačním systémem a verzí počítače, který chcete replikovat.

## <a name="mobility-service-installation"></a>Instalace služby Mobility

Službu mobility můžete nasadit pomocí následujících metod:

- **Nabízená instalace**: Služba Mobility je nainstalována procesním serverem, když povolíte ochranu počítače. 
- **Ruční instalace**: Službu mobility můžete nainstalovat ručně na každý počítač prostřednictvím hlavního nastavení nebo příkazového řádku.

Služba Mobility komunikuje se zařízením pro replikaci a replikovanými počítači. Pokud máte antivirový software spuštěný na replikačním zařízení, procesních serverech nebo počítačích, které jsou replikovány, měly by být z prohledávání vyloučeny následující složky:


- C:\Program Files\Agent služby Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Obnovení webu Microsoft Azure
- C:\Program Files (x86)\Obnovení webu Microsoft Azure
- C:\ProgramData\ASR\agent (na počítačích se systémem Windows s nainstalovanou službou Mobility)

## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro počítač, začne počáteční replikace do Azure.
2. Během počáteční replikace služba Mobility čte data z disků počítače a odesílá je na procesní server.
3. Tato data se používají k osivu kopii disku v předplatném Azure. 
4. Po dokončení počáteční replikace začne replikace rozdílových změn do Azure. Replikace je na úrovni bloku a téměř nepřetržitá.
4. Služba Mobility zachycuje zápisy do paměti disku integrací se subsystémem úložiště operačního systému. Tato metoda zabraňuje vstupně-tovitých operací disku v replikačním počítači pro přírůstkovou replikaci. 
5. Sledované změny pro počítač jsou odesílány na procesní server na příchozím portu HTTPS 9443. Tento port lze upravit. Procesní server ho komprimuje a šifruje a odesílá do Azure. 

## <a name="ports"></a>Porty

**Zařízení** | **Připojení**
--- | --- 
**Replikační stroje** | Služba Mobility spuštěná na virtuálních počítačích komunikuje s místním replikačním zařízením na příchozím portu HTTPS 443 pro správu replikace.<br/><br/> Počítače odesílají data replikace na procesní server na příchozím portu HTTPS 9443. Tento port lze upravit.
**Zařízení pro replikaci** | Replikační zařízení orchestruje replikaci s Azure přes port HTTPS 443 odchozí.
**Procesní server** | Procesní server přijímá replikační data, optimalizuje je a šifruje a odesílá je do úložiště Azure přes odchozí port 443.


## <a name="performance-and-scaling"></a>Výkon a škálování

Ve výchozím nastavení nasadíte jedno replikační zařízení, které spouští konfigurační i procesní server. Pokud replikujete jenom několik počítačů, toto nasazení je dostačující. Pokud však replikujete a migrujete stovky počítačů, jeden procesní server nemusí být schopen zpracovat všechny přenosy replikace. V takovém případě můžete nasadit další, horizontální navýšení kapacity procesu servery.

### <a name="plan-vmware-deployment"></a>Plán nasazení vmware

Pokud replikujete virtuální počítače VMware, můžete použít [Plánovač nasazení site recovery pro společnost VMware](../site-recovery/site-recovery-deployment-planner.md), který vám pomůže určit požadavky na výkon, včetně denní rychlosti změny dat a potřebných procesních serverů.

### <a name="replication-appliance-capacity"></a>Kapacita replikačního zařízení

Pomocí hodnot v této tabulce zjistíte, zda potřebujete další procesní server ve vašem nasazení.

- Pokud je vaše denní míra změn (rychlost změn) vyšší než 2 TB, nasaďte další procesní server.
- Pokud replikujete více než 200 počítačů, nasaďte další replikační zařízení.

**Cpu** | **Paměti** | **Ukládání volných prostorových dat do mezipaměti** | **Rychlost konve** | **Replikační limity**
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 zásuvky \@ * 4 jádra 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | < 100 strojů 
12 virtuálních procesorů (2 zásuvky \@ * 6 jader 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | 100-150 strojů.
16 virtuálních procesorů (2 zásuvky \@ * 8 jader 2,5 GHz) | 32 G1 |  1 TB | 1 TB až 2 TB | 151-200 strojů.

### <a name="sizing-scale-out-process-servers"></a>Dimenzování horizontálních navýšení kapacity procesních serverů

Pokud potřebujete nasadit horizontální navýšení kapacity procesu serveru, použijte tuto tabulku zjistit velikost serveru.

**Procesní server** | **Volné místo pro ukládání dat do mezipaměti** | **Rychlost konve** | **Replikační limity**
--- | --- | --- | --- 
4 vCPU (2 sokety \@ * 2 jádra 2,5 GHz), 8 GB paměti | 300 GB | 250 GB nebo méně | Až 85 strojů 
8 virtuálních procesorů (2 sokety * 4 jádra \@ 2,5 GHz), 12GB paměť | 600 GB | 251 GB až 1 TB    | 86-150 strojů.
12 virtuálních procesorů (2 sokety * 6 jader \@ 2,5 GHz), 24GB paměť | 1 TB | 1-2 TB | 151-225 strojů.

## <a name="throttle-upload-bandwidth"></a>Šířka pásma nahrávání plynu.

Provoz vmware, který se replikuje do Azure, prochází konkrétním procesním serverem. Propustnost odesílání můžete omezit omezením šířky pásma na počítačích, které jsou spuštěny jako procesní servery. Šířku pásma můžete ovlivnit pomocí tohoto klíče registru:

- Hodnota registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM určuje počet vláken, které se používají pro přenos dat (počáteční nebo delta replikace) disku. Vyšší hodnota zvyšuje šířku pásma sítě, která se používá pro replikaci. Výchozí hodnota je čtyři. Maximální hodnota je 32. Monitorováním provozu hodnotu optimalizujte.
- Kromě toho můžete omezit šířku pásma na počítači procesního serveru následujícím způsobem:

    1. Na procesu procesu otevřete modul snap-in Azure Backup MMC. Zástupce je na ploše nebo ve složce C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 
    2. V modulu snap-in vyberte **Změnit vlastnosti**.
    3. V **seznamu Omezení**vyberte možnost **Povolit omezení využití šířky pásma internetu pro operace zálohování**. Nastavte limity pro pracovní dobu a nepracovní dobu. Platné rozsahy jsou od 512 kb/s do 1 023 Mb/s.


## <a name="next-steps"></a>Další kroky

Vyzkoušejte [migraci založenou na agentech](tutorial-migrate-vmware-agent.md) pro [servery VMware](tutorial-migrate-vmware-agent.md) nebo [fyzické servery](tutorial-migrate-physical-virtual-machines.md).
