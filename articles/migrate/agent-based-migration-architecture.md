---
title: Migrace na základě agenta architektura v Azure Migrate migrace serveru
description: Poskytuje přehled založené na agentovi migrace virtuálních počítačů VMware pomocí Azure Migrate serveru migrace.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811151"
---
# <a name="agent-based-migration-architecture"></a>Architektury založené na agentovi migrace

Tento článek obsahuje přehled architektury a procesy používané při replikaci na základě agenta pomocí nástroje Azure Migrate serveru migrace.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a instancí virtuálního počítače AWS a GCP, do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).

## <a name="agent-based-replication"></a>Replikace na základě agenta

Replikace na základě agenta replikace migrovat Server Azure nástroj se používá k migraci místních virtuálních počítačů VMware a fyzických serverů do Azure. Lze ji také použít k migraci jiných virtualizované na místních serverech, jakož i privátních a veřejných cloudových virtuálních počítačích, včetně instancí AWS a GCP virtuálních počítačů.

Migrace VMware nástroj pro migraci serveru migrace Azure nabízí několik možností:

- Migrace pomocí replikace na základě agenta, jak je popsáno v tomto článku.
- Bez agentů replikace, aniž by bylo potřeba nic instalovat na nich při migraci virtuálních počítačů.

Další informace o [výběru metody migrace pro replikaci z VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migrace serveru a Azure Site Recovery

Migrace serveru do Azure migrovat je nástroj pro migraci místních i veřejných cloudových úloh do Azure. Je optimalizovaný pro migraci. Site Recovery je nástroj pro obnovení po havárii. Migrace serveru a Azure Site Recovery sdílet některé běžné součásti technologii používanou k replikaci dat, ale slouží k jiným účelům.

## <a name="architectural-components"></a>Komponenty architektury

![Architektura](./media/agent-based-replication-architecture/architecture.png)

V tabulce najdete souhrn komponenty používané pro migraci založené na agentovi.

**Komponenta** | **Podrobnosti** | **Instalace**
--- | --- | ---
**Replikace zařízení** | Zařízení replikace (konfigurační server) je místní počítač, který funguje jako most mezi místním prostředím a nástroj pro migraci serveru migrace Azure. Zařízení vyhledá místní virtuální počítač inventáře, tak, aby migrace serveru Azure můžete orchestrovat replikace a migrace. Zařízení má dvě součásti:<br/><br/> **Konfigurační server**: Se připojí k Azure Migrate migrace serveru a koordinuje replikaci.<br/> **Procesový server:** Zpracovává replikační data. Ji přijímá data virtuálního počítače, komprimuje a zašifruje a odesílá do předplatného Azure. Migrace serveru existuje, zapíše data do managed disks. | Ve výchozím nastavení je na procesovém serveru nainstalované společně se sadou konfigurační server na zařízení replikace.
**Služba Mobility** | Služba Mobility je agent nainstalovaný na každém počítači, který chcete replikovat a migrovat. Odesílání dat replikace z počítače k procesového serveru. Nejsou k dispozici řadu různých agentů služby Mobility. | Instalační soubory služby Mobility se nacházejí v zařízení replikace. Stažení a instalace agenta, které potřebujete, v souladu s operačním systémem a verzi na počítači, který chcete replikovat.

### <a name="mobility-service-installation"></a>Instalace služby Mobility

Můžete nasadit službu Mobility pomocí následujících metod:

- **Nabízená instalace**: Služba Mobility je nainstalovaná procesový server při povolení ochrany pro počítač. 
- **Ruční instalace**: Můžete nainstalovat službu Mobility ručně na každém počítači prostřednictvím uživatelského rozhraní nebo na příkazovém řádku.

Služba Mobility komunikuje se službou replikace zařízení a replikované počítače. Pokud máte spuštěný na zařízení replikace, procesových serverů nebo počítačů, se replikuje antivirový software, by se z kontroly vyloučit následující složky:


- Agent C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (počítačích s Windows se službou Mobility nainstalovanou)

## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač, začíná počáteční replikace do Azure.
2. Během počáteční replikace služby Mobility čte data z disků počítačů a odesílá je na procesní server.
3. Tato data se používají pro počáteční hodnoty kopie disku ve vašem předplatném Azure. 
4. Po dokončení počáteční replikace začne probíhat rozdílová replikace do Azure. Replikace je blokových a téměř trvalý.
4. Zachycuje služby Mobility se zapíše do paměti disku virtuálního počítače, díky integraci s subsystému úložiště operačního systému. Tato metoda zabraňuje vstupně-výstupních operací disku na replikujícím počítači pro přírůstková replikace. 
5. Sledované změny se pro počítač se odesílají k procesového serveru na příchozím portu HTTPS 9443 příchozí. Tento port je možné upravit. Procesový server komprimuje a zašifruje a odesílá je do Azure. 

## <a name="ports"></a>Porty

**zařízení** | **připojení**
--- | --- 
Virtuální počítače | Služby Mobility spuštěné na virtuálních počítačích komunikuje se místní replikace zařízení na port HTTPS 443 příchozí kvůli správě replikace.<br/><br/> Virtuální počítače odesílají data do procesového serveru (spuštěného na zařízení replikace ve výchozím nastavení) na příchozím portu HTTPS 9443 příchozí. Tento port je možné upravit.
Replikace zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový server | Procesový server přijímá data replikace, optimalizuje je zašifruje a odesílá je do úložiště Azure přes port 443 odchozí.


## <a name="performance-and-scaling"></a>Výkon a škálování

Ve výchozím nastavení nasazením jedné replikace zařízení, na kterém běží konfigurační server a procesový server. Pokud replikujete pouze několik počítačů, toto nasazení je dostačující. Ale pokud jste replikaci a migrace stovek počítačů, jeden procesový server nemusí být schopná zpracovat veškerý provoz replikace. V takovém případě můžete nasadit další, horizontální navýšení kapacity procesových serverů.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Deployment Planner pro replikaci z VMware

Pokud provádíte replikaci virtuálních počítačů VMware, můžete použít [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) pro VMware sloužící k určení požadavků na výkon, včetně dat o denním změnit rychlost a procesových serverů, které potřebujete.

### <a name="replication-appliance-capacity"></a>Replikace kapacity zařízení

Hodnoty v této tabulce je možné zjistit, jestli nepotřebujete další procesový server v nasazení.

- Pokud vaší denní frekvenci změn (četnost změn dat) je více než 2 TB, nasaďte další procesový server.
- Pokud replikujete více než 200 počítačů, nasaďte další replikace zařízení.

**CPU** | **Paměť** | **Volné místo pro ukládání dat do mezipaměti** | **Četnost změn dat** | **Omezení replikace**
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | < 100 počítačů 
12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | 100 150 počítačů.
16 virtuálních procesorů (2 sockets * 8 jader \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB na 2 TB | 151 200 počítačů.

### <a name="scale-out-process-server-sizing"></a>Nastavení velikosti serveru škálování na více instancí procesu

Pokud je potřeba nasadit horizontální navýšení kapacity procesového serveru, tato tabulka vám mohou pomoci při zjistit velikosti serveru.

**Procesový server** | **Volné místo pro ukládání dat do mezipaměti** | **Četnost změn dat** | **Omezení replikace**
--- | --- | --- | --- 
4 virtuální procesory (2 sockets * 2 jádra \@ 2,5 GHz), 8 GB paměti | 300 GB | Aby se 250 GB nebo méně | 85 počítače 
8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 251 GB až 1 TB    | 86 150 počítačů.
12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz), 24 GB paměti | 1 TB | 1-2 TB | 151 225 počítače.

## <a name="control-upload-throughput"></a>Propustnost odesílání ovládacího prvku

Můžete omezit šířku pásma používaného k odesílání dat do Azure na každém hostiteli Hyper-V. Dej si pozor. Pokud nastavíte příliš nízké hodnoty bude mít nepříznivý dopad replikace a zpoždění migrace.


1. Přihlaste se k uzlu hostitele nebo clusteru Hyper-V.
2. Spustit **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, chcete-li otevřít modul snap-in Windows Azure Backup konzoly MMC.
3. V modulu snap-in, vyberte **změnit vlastnosti**.
4. V **omezování**vyberte **Povolit omezování šířky pásma Internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou 512 kB/s na 1,023 MB/s.
I

### <a name="influence-upload-efficiency"></a>Vliv nahrávání efektivity

Pokud máte přebytečné šířky pásma pro replikaci a chcete zvýšit nahrávání, můžete zvýšit počet vláken přidělené pro úlohu nahrávání následujícím způsobem:

1. Otevřete registr s Regedit.
2. Přejděte na klíč HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zvyšte hodnotu pro počet vláken pro nahrávání dat pro každý replikující virtuální počítač. Výchozí hodnota je 4 a maximální hodnota je 32. 

## <a name="next-steps"></a>Další postup

Vyzkoušejte si založené na agentovi [migrace virtuálních počítačů VMware](tutorial-migrate-vmware-agent.md) pomocí Azure Migrate serveru migrace.
