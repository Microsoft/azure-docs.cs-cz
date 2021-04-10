---
title: Migrace založená na agentech v migraci serveru Azure Migrate
description: Poskytuje přehled migrace virtuálních počítačů VMware založených na agentech v Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: f4f79725d0eda65ba00a44e9e7fc2a51c024eccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864053"
---
# <a name="agent-based-migration-architecture"></a>Architektura migrace založené na agentech

Tento článek poskytuje přehled architektury a procesů používaných pro replikaci virtuálních počítačů VMware na základě agentů pomocí nástroje [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Pomocí Azure Migrate: Migrace serveru můžete replikovat virtuální počítače VMware několika možnostmi:

- Migrujte virtuální počítače pomocí replikace založené na agentech, jak je popsáno v tomto článku.
- Migrujte virtuální počítače VMware pomocí replikace bez agentů. Tím migrujete virtuální počítače, aniž byste museli instalovat cokoli.

Přečtěte si další informace o [výběru a porovnání](server-migrate-overview.md) metod migrace pro virtuální počítače VMware. 


## <a name="agent-based-migration"></a>Migrace založená na agentovi

Migrace založená na agentech se používá k migraci místních virtuálních počítačů VMware a fyzických serverů do Azure. Dá se taky použít k migraci dalších místních virtualizovaných serverů a virtuálních počítačů privátních a veřejných cloudů, včetně instancí AWS a virtuálních počítačů s GCP. Migrace založená na agentech v Azure Migrate používá některé funkce back-endu ze služby [Azure Site Recovery](../site-recovery/site-recovery-overview.md) .


## <a name="architectural-components"></a>Komponenty architektury

Diagram znázorňuje komponenty, které jsou součástí migrace na základě agenta.

![Diagram zobrazuje komponenty pro migraci založené na agentech, které jsou vysvětleny v tabulce.](./media/agent-based-replication-architecture/architecture.png)

Tabulka shrnuje součásti používané pro migraci na základě agenta.

**Komponenta** | **Podrobnosti** | **Instalace**
--- | --- | ---
**Replikační zařízení** | Zařízení replikace (konfigurační server/procesový Server) je místní server, který funguje jako most mezi místním prostředím a migrací serveru. Zařízení zjistí místní inventář serveru, aby migrace serveru mohla orchestrovat replikaci a migraci. Zařízení má dvě komponenty:<br/><br/> **Konfigurační server**: připojení k migraci serveru a koordinuje replikaci.<br/> **Procesový Server**: zpracovává replikaci dat. Procesový server přijímá data serveru, komprimuje je a šifruje a odesílá je do Azure. Migrace serveru v Azure zapisuje data na spravované disky. | Ve výchozím nastavení se procesový server instaluje společně s konfiguračním serverem na zařízení replikace.
**Služba Mobility** | Služba mobility je agent nainstalovaný na každém serveru, který chcete replikovat a migrovat. Odesílá data replikace ze serveru na procesový Server. | Instalační soubory pro různé verze služby mobility jsou umístěné na zařízení replikace. Potřebný Agent můžete stáhnout a nainstalovat v souladu s operačním systémem a verzí serveru, který chcete replikovat.

## <a name="mobility-service-installation"></a>Instalace služby Mobility

Službu mobility můžete nasadit pomocí následujících metod:

- **Nabízená instalace**: Služba mobility je nainstalovaná procesovým serverem, když povolíte ochranu pro server. 
- **Ruční instalace**: službu mobility můžete nainstalovat ručně na každý server prostřednictvím uživatelského rozhraní nebo příkazového řádku.

Služba mobility komunikuje se zařízením replikace a replikovanými servery. Pokud máte antivirový software spuštěný na zařízení replikace, procesových serverech nebo serverech, které se replikují, měly by se z kontroly vyloučit následující složky:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (na serverech Windows s nainstalovanou službou mobility)

## <a name="replication-process"></a>Proces replikace

1. Pokud povolíte replikaci pro server, spustí se počáteční replikace do Azure.
2. Během počáteční replikace služba mobility načítá data z serverových disků a odesílá je na procesový Server.
3. Tato data se používají k osazení kopie disku v předplatném Azure. 
4. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Replikace je na úrovni bloku a téměř nepřetržitá.
4. Služba mobility zachycuje zápisy do paměti na disku integruje se s využitím subsystému úložiště v operačním systému. Tato metoda zabrání vstupně-výstupním operacím disku na replikačním serveru pro přírůstkovou replikaci. 
5. Sledované změny serveru se odesílají na procesový Server na příchozím portu HTTPS 9443. Tento port lze změnit. Procesový Server ho zkomprimuje a zašifruje a pošle ho do Azure. 

## <a name="ports"></a>Porty

**Zařízení** | **Připojení**
--- | --- 
**Replikace serverů** | Služba mobility spuštěná na virtuálních počítačích komunikuje s místním zařízením replikace na portu HTTPS 443 příchozí, pro správu replikací.<br/><br/> Servery odesílají replikační data na procesový Server na portu HTTPS 9443 příchozí. Tento port lze změnit.
**Replikační zařízení** | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
**Procesový Server** | Procesový server přijímá data replikace, optimalizuje je a šifruje a odesílá je do Azure Storage přes odchozí port 443.


## <a name="performance-and-scaling"></a>Výkon a škálování

Ve výchozím nastavení nasadíte jediné zařízení replikace, na kterém běží konfigurační server a procesový Server. Pokud pouze provádíte replikaci několika serverů, toto nasazení je dostatečné. Pokud ale provádíte replikaci a migrujete stovky serverů, jeden procesový server nemusí být schopný zpracovat veškerý provoz replikace. V takovém případě můžete nasadit další procesové servery se škálováním na více instancí.

### <a name="plan-vmware-deployment"></a>Plánování nasazení VMware

Pokud provádíte replikaci virtuálních počítačů VMware, můžete použít [Plánovač nasazení Site Recovery pro VMware](../site-recovery/site-recovery-deployment-planner.md), což vám pomůže určit požadavky na výkon, včetně denní frekvence změny dat a procesových serverů, které potřebujete.

### <a name="replication-appliance-capacity"></a>Kapacita zařízení replikace

Pomocí hodnot v této tabulce můžete zjistit, jestli v nasazení potřebujete další procesový Server.

- Pokud je frekvence denních změn (četnost změn) větší než 2 TB, nasaďte další procesový Server.
- Pokud provádíte replikaci více než 200 serverů, nasaďte další replikační zařízení.

**Procesor** | **Memory (Paměť)** | **Volné místo – ukládání dat do mezipaměti** | **Míra četnosti změn** | **Omezení replikace**
--- | --- | --- | --- | ---
8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | Servery < 100 
12 vCPU (2 sokety × 6 jader \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | servery 100-150.
16 vCPU (2 sokety × 8 jader \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB až 2 TB | servery 151-200.

### <a name="sizing-scale-out-process-servers"></a>Změna velikosti procesových serverů se škálováním na více instancí

Pokud potřebujete nasadit procesový Server se škálováním na více instancí, použijte tuto tabulku k tomu, abyste zjistili velikost serveru.

**Procesový Server** | **Volné místo pro ukládání dat do mezipaměti** | **Míra četnosti změn** | **Omezení replikace**
--- | --- | --- | --- 
4 vCPU (2 sokety × 2 jádra \@ 2,5 GHz), 8 GB paměti | 300 GB | 250 GB nebo méně | Až 85 serverů 
8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 251 GB až 1 TB | servery 86-150.
12 vCPU (2 sokety × 6 jader \@ 2,5 GHz), 24 GB paměti | 1 TB | 1-2 TB | servery 151-225.

## <a name="throttle-upload-bandwidth"></a>Omezí šířku pásma nahrávání.

Provoz VMware, který se replikuje do Azure, prochází přes konkrétní procesový Server. Propustnost nahrávání můžete omezit omezením šířky pásma na serverech, na kterých běží jako procesové servery. Šířku pásma můžete ovlivnit pomocí tohoto klíče registru:

- Hodnota registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM určuje počet vláken, která se používají pro přenos dat (počáteční nebo rozdílovou replikaci) disku. Vyšší hodnota zvyšuje šířku pásma sítě, která se používá pro replikaci. Výchozí hodnota je 4. Maximální hodnota je 32. Monitorováním provozu hodnotu optimalizujte.
- Kromě toho můžete na procesovém serveru omezit šířku pásma následujícím způsobem:

    1. Na procesovém serveru otevřete modul snap-in Azure Backup MMC. Je k dispozici zástupce na ploše nebo ve složce C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. V modulu snap-in vyberte **změnit vlastnosti**.
    3. V případě **omezení** vyberte možnost **Povolit omezování šířky pásma internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou od 512 do 1 023 MB/s.


## <a name="next-steps"></a>Další kroky

Vyzkoušejte [migraci na základě agenta](tutorial-migrate-vmware-agent.md) pro [VMware](tutorial-migrate-vmware-agent.md) nebo [fyzické servery](tutorial-migrate-physical-virtual-machines.md).
