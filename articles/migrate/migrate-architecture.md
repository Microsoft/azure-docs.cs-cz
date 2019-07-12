---
title: Architektura služby Azure Migrate | Dokumentace Microsoftu
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811424"
---
# <a name="azure-migrate-architecture-and-processes"></a>Architektura služby Azure Migrate a procesy

[Azure Migrate](migrate-overview.md) představuje Centrum nástrojů, které vám umožní zjišťovat, posuzovat a migrovat aplikace, infrastruktury a úlohy Microsoft Azure. Centra zahrnuje nástroje Azure Migrate a nezávislé výrobce softwaru třetích stran výrobce (ISV) nabídky. 

 Tento článek shrnuje posouzení, migrace architektury a procesy pro posouzení migrace serveru Azure a Azure Migrate serveru migrace.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Posouzení pomocí Azure Migrate Server Assessment

Azure Migrate Server Assessment můžete posoudit virtuální počítače Hyper-V a virtuálních počítačů VMware pro migraci do Azure. Posouzení funguje takto:

1. **Příprava na posouzení**: On-premises můžete nastavit zjednodušenou Azure Migrate zařízení v místním jako virtuální počítač VMware nebo virtuálních počítačů Hyper-V a zaregistrovat zařízení pomocí služby Azure Migrate.
2. **Zjištění virtuálních počítačů**: Zařízení Azure Migrate se spustí zjišťování místních virtuálních počítačů. 
    - Nic musí být nainstalovaný na zjištěných virtuálních počítačů.
    - Metadata virtuálního počítače obsahuje informace o jader, paměti, disků, velikosti disků a síťových adaptérů.
    - Informace o procesoru a paměti, využití, IOPS disku, propustnost disku (MB/s) a výstupu (MB/s) sítě obsahuje údaje o výkonu
- **Shromažďování a posouzení počítačů**: Po dokončení zjišťování zjistí portál, který bude shromažďovat v Azure virtuální počítače do skupin, které se obvykle skládají z virtuálních počítačů, které chcete migrovat společně. Spustit posouzení pro skupinu.


## <a name="vmware-assessment"></a>Posouzení VMware 

Diagram shrnuje, jak funguje posouzení virtuálních počítačů VMware pomocí Azure Migrate Server Assessment.

![Architektura posouzení VMware](./media/migrate-architecture/sas-architecture-vmware.png)

Probíhá takto:

1. **Nasazení zařízení Azure Migrate**:

    a. Stáhněte šablonu (OVA) na webu Azure Portal.

    b. Importujte do počítače vCenter serveru vytvořte virtuální počítač.

    c. Připojení k tomuto virtuálnímu počítači, konfigurace základního nastavení pro něj a zaregistrujte ho pomocí služby Azure Migrate.

2. **Inicializace zjišťování**:

    a. Připojte se k kolekcí aplikaci spuštěnou na zařízení, aby se zahájilo zjišťování.

    b. Zařízení průběžně odesílá data metadat a výkonu z virtuálních počítačů do Azure.

    - Zařízení je připojen ke službě Azure Migrate.
    - Změny prostředí během průběžná zjišťování jsou zachyceny. Například přidáním virtuálních počítačů v rámci rozsahu vyhledávání nebo přidání disků virtuálních počítačů nebo síťových karet.

3. **Posouzení počítačů**:

    a. Po dokončení zjišťování zjistí portál, který bude shromažďovat v Azure virtuální počítače do skupin.  Skupinu se obvykle skládá z virtuálních počítačů, které chcete migrovat společně.

    b. Spusťte posouzení pro každou skupinu.

4. **Projděte si hodnocení**: 

    a. Po dokončení posouzení zobrazte na portálu.

    b. Pro další analýzu stáhněte si posouzení ve formátu aplikace Excel.



### <a name="vmware-ports"></a>Porty VMware
Azure Migrate používá následující porty pro připojení pro replikaci z VMware:

**Zdroj** | **Cíl** | **Port** | **Podrobnosti**
--- | --- | --- | ---
Zařízení Azure Migrate | Služba Azure Migrate | Target-TCP 443 | Odesílání metadat a výkonu dat do Azure Migrate.
Zařízení Azure Migrate | vCenter Server | Target-TCP 443 | Připojte k vCenter serveru pro data metadat a výkonu. Výchozí hodnota je 443, ale můžete upravit pomocí vCenter naslouchá na jiném portu. 
Klienta protokolu RDP | Zařízení Azure Migrate | Target-TCP3389 | Připojením ke vzdálené ploše zjišťování aktivace ze zařízení.

### <a name="collected-vmware-metadata"></a>Shromáždila se metadata VMware

Zařízení odesílá metadata a data související s výkonem a z virtuálních počítačů do Azure.

**Akce** | **Podrobnosti**
--- | ---
**Shromáždila se metadata** | Název vCenter virtuálního počítače<br/> cesty virtuálního počítače vCenter (složka hostiteli/clusteru)<br/> Adresy IP a MAC<br/> Operační systém<br/> Počet jader a disků nebo síťových adaptérů<br/> Velikost paměti a disku.
**Data shromážděná výkonu** | Využití procesoru/paměti<br/> Za data na disku (čtení a zápis propustnost disku; čtení/Zápisy na disk za sekundu)<br/> Data síťové karty (síťové zařízení v síti out).<br/><br/> Údaje o výkonu se shromažďují průběžně, jakmile se zařízení připojí k serveru vCenter. Historická data nejsou shromažďována.

## <a name="hyper-v-assessment"></a>Posouzení Hyper-V

Diagram shrnuje, jak funguje technologie Hyper-V posouzení sing posouzení migrace serveru Azure.

![Architektura posouzení Hyper-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

Probíhá takto:

1. **Vytvoření zařízení Azure Migrate**:

    a. Stáhněte si virtuální počítač v komprimovaném formátu na webu Azure Portal.

    b. Importujte do hostitele Hyper-V.

    c. Připojte se k zařízení virtuálního počítače. Konfigurace základního nastavení pro něj a zaregistrujte ho pomocí služby Azure Migrate.

2. **Inicializace zjišťování**:

    a. Připojte se k zařízení Azure Migrate k zahájení zjišťování. Nic musí být nainstalovaný na zjištěných virtuálních počítačů.

    b. Zařízení průběžně odesílá data metadat a výkonu virtuálních počítačů do Azure Migrate.

    - Zařízení je připojen ke službě Azure Migrate (použití relací CIM).
    - Změny prostředí během průběžná zjišťování jsou zachyceny. Například přidáním virtuálních počítačů v rámci rozsahu vyhledávání nebo přidání disků virtuálních počítačů nebo síťových karet.


3. **Posouzení počítačů**:

    a. Po dokončení zjišťování zjistí portál, který bude shromažďovat v Azure virtuální počítače do skupin.  Skupinu se obvykle skládá z virtuálních počítačů, které chcete migrovat společně.

    b. Spusťte posouzení pro každou skupinu.

4. **Projděte si hodnocení**:

    a. Po dokončení posouzení zobrazte na portálu.

    b. Pro další analýzu stáhněte si posouzení ve formátu aplikace Excel.

### <a name="hyper-v-ports"></a>Porty Hyper-V

Služba Azure Migrate používá následující porty pro připojení pro Hyper-V:

**Zdroj** | **Cíl** | **Port** | **Podrobnosti**
--- | --- | --- | ---
Zařízení Azure Migrate | Služba Azure Migrate | Target-TCP 443 | Odesílání metadat a výkonu dat do Azure Migrate.
Zařízení Azure Migrate | Hostitele nebo clusteru Hyper-V | Výchozí cíl WinRM porty HTTP:5985; HTTPS:5986 | Připojení k hostiteli dat metadata a výkonu. Relaci CIM používá pro připojení
Klienta protokolu RDP | Zařízení Azure Migrate | Target-TCP3389 | Připojením ke vzdálené ploše zjišťování aktivace ze zařízení.

## <a name="collected-hyper-v-vm-metadata"></a>Shromáždila se metadata virtuálního počítače Hyper-V

Zařízení odesílá metadata a data související s výkonem a z virtuálních počítačů do Azure.


**Akce** | **Podrobnosti**
--- | ---
**Shromáždila se metadata** | název virtuálního počítače<br/> Cesty virtuálního počítače (složka hostiteli/clusteru)<br/> Adresy IP a MAC<br/> Operační systém<br/> Počet jader a disků nebo síťových adaptérů<br/> Velikost paměti a disku<br/> VSTUPNĚ-výstupních diskových, propustnost disku (MB/s), výstup sítě (MB/s)
**Data shromážděná výkonu** |  Využití procesoru/paměti<br/> Za data na disku (čtení a zápis propustnost disku; čtení/Zápisy na disk za sekundu)<br/> Data síťové karty (síťové zařízení v síti out).<br/><br/> Údaje o výkonu se shromažďují průběžně, jakmile se zařízení připojí k hostiteli Hyper-V. Historická data nejsou shromažďována.




## <a name="migration-with-azure-migrate-server-migration"></a>Migrace se službou Azure Migrate migrace serveru

Pomocí Azure Migrate serveru migrace, můžete migrovat následující do Azure:

- Místní virtuální počítače VMware
- Místní virtuální počítače Hyper-V
- Místní fyzické servery
- Instance virtuálního počítače AWS Windows
- Instance virtuálních počítačů Windows GCP

Proces migrace se mírně liší, v závislosti na tom, co Probíhá migrace.


## <a name="migrate-vmware-vms"></a>Migrace virtuálních počítačů VMware

Migrace serveru do Azure Migrate nabízí dvě metody pro migraci místních virtuálních počítačů VMware:

- **Bez agentů replikace**: Migrace virtuálních počítačů, aniž by bylo potřeba nic instalovat na ně.
- **Replikace na základě agenta**. Instalace agenta na virtuální počítač pro replikaci.

I když je snazší z hlediska nasazení bez agentů replikace, aktuálně má několik omezení. [Další informace](server-migrate-overview.md) o těchto omezeních.
 

### <a name="agent-based-migration"></a>Migrace na základě agenta

Migrace virtuálních počítačů VMware na základě agenta vyžaduje několik součástí nasazení, dle souhrnu v tabulce.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Konfigurace počítače serveru** | Jeden místní virtuální počítač VMware, který je nasazen z ve stažené šabloně OVF.<br/><br/> Počítači běží všechny místní komponenty Site Recovery, mezi které patří konfigurační server a procesový server. | **Konfigurační server**: Koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.<br/><br/> **Procesový server:** Obvykle se instaluje na konfigurační server. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odesílá je do Azure. Procesový server také nainstaluje služba Mobility Azure Site Recovery na virtuální počítače a provádí automatické zjišťování místních počítačů.
**Služba Mobility** | Na každý virtuální počítač VMware, které replikujete musí nainstalovat službu Mobility. | Doporučujeme vám, že povolíte automatické instalace z procesového serveru. Alternativně můžete ručně nainstalovat službu nebo použít metody automatického nasazení, jako je System Center Configuration Manager.





### <a name="agent-based-replication-process"></a>Proces replikace založené na agentovi

![Proces replikace](./media/migrate-architecture/v2a-architecture-henry.png)

1. Když povolíte replikaci pro virtuální počítač, začíná počáteční replikace do Azure. 
    - Replikace je na úrovni bloku, téměř nepřetržité, pomocí agenta služby Mobility spuštěného na virtuálním počítači.
    - Použijí se nastavení zásady replikace:
        - **Prahová hodnota cíle bodu obnovení**. Toto nastavení nemá vliv na replikaci. Pomáhá s monitorováním. Vyvolá událost, a volitelně odešle e-mail, pokud aktuální cíl bodu obnovení překročí limit prahové hodnoty, který zadáte.
        - **Uchování bodu obnovení**. Toto nastavení určuje, jak daleko zpět v čase, který chcete vrátit, když dojde k přerušení. Maximální doba uchování ve službě premium storage je 24 hodin. Na úložiště úrovně standard je 72 hodin. 
        - **Snímky konzistentní s**. Snímky konzistentní s aplikací může trvat každých 1 až 12 hodin v závislosti na potřebách aplikace. Snímky jsou snímky objektů blob v Azure standardní. Agent Mobility spuštěný na virtuálním počítači požádá o snímek služby VSS v souladu se toto nastavení a záložek, které odkazují bodu v čase jako aplikace konzistentní vzhledem k aplikacím ve streamu replikace.

2. Provoz replikuje do služby Azure storage veřejné koncové body přes internet.

    - Alternativně můžete použít Azure ExpressRoute s [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - Přenos replikačních dat přes virtuální privátní síť site-to-site (VPN) z místní lokality do Azure se nepodporuje.
3. Po dokončení počáteční replikace začne probíhat rozdílová replikace do Azure. Sledované změny se pro počítač se odesílají na procesní server.
2. Komunikace se stane, následujícím způsobem:

    - Virtuální počítače komunikovat s místní konfigurační server na portu HTTPS 443 příchozí kvůli správě replikace.
    - Konfigurační server orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
    - Příchozí data replikace k procesového serveru (běžícího na počítači serveru configuration) na příchozím portu HTTPS 9443 poslat virtuálních počítačů. Tento port je možné upravit.
    - Procesový server přijímá data replikace, optimalizuje je zašifruje a odesílá je do úložiště Azure přes port 443 odchozí.
5. Data replikace zaznamená první pozemního v účtu úložiště mezipaměti v Azure. Tyto protokoly se zpracují a jsou data uložená v Azure spravovaného disku (disk Azure Site Recovery počáteční hodnota). Body obnovení jsou vytvářeny na tomto disku.







## <a name="next-steps"></a>Další postup

- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
- Pomoc od komunity, přejděte [fóra Azure Migrate MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) nebo [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

