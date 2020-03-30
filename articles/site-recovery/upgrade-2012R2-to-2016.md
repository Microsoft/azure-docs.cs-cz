---
title: Upgrade Windows Server/System Center VMM 2012 R2 na Obnovení webu Windows Server 2016-Azure
description: Zjistěte, jak nastavit zotavení po havárii na virtuální počítače Azure for Azure Stack pomocí služby Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954408"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Upgrade programu Windows Server Server/System Center 2012 R2 VMM na systém Windows Server/VMM 2016 

Tento článek ukazuje, jak upgradovat hostitele Windows Serveru 2012 R2 & SCVMM 2012 R2, které jsou nakonfigurované pomocí Azure Site Recovery, na Windows Server 2016 & SCVMM 2016

Site Recovery přispívá k vaší obchodní kontinuity a zotavení po havárii (BCDR) strategie. Služba zajišťuje, že vaše úlohy virtuálních počítače zůstanou k dispozici, když dojde k očekávaným a neočekávaným výpadkům.

> [!IMPORTANT]
> Při upgradu hostitelů windows serveru 2012 R2, kteří jsou již nakonfigurovaní pro replikaci pomocí Azure Site Recovery, musíte postupovat podle kroků uvedených v tomto dokumentu. Jakákoli alternativní cesta zvolená pro upgrade může mít za následek nepodporované stavy a může mít za následek přerušení replikace nebo schopnost provádět převzetí služeb při selhání.


V tomto článku se dozvíte, jak upgradovat následující konfigurace ve vašem prostředí:

> [!div class="checklist"]
> * **Hostitelé systému Windows Server 2012 R2, které nespravuje scvmm** 
> * **Hostitelé systému Windows Server 2012 R2, které jsou spravovány samostatným serverem SCVMM 2012 R2** 
> * **Hostitelé systému Windows Server 2012 R2 spravované vysoce dostupným serverem SCVMM 2012 R2**


## <a name="prerequisites--factors-to-consider"></a>Předpoklady & faktory, které je třeba vzít v úvahu

Před upgradem si všimněte následujícího: -

- Pokud máte hostitele Windows Server 2012 R2, které nejsou spravovány scvmm a je to samostatné nastavení prostředí, dojde k přerušení replikace, pokud se pokusíte provést upgrade.
- Pokud jste při instalaci scvmm 2012 R2 vybrali možnost*Neukládat klíče ve službě Active Directory,* upgrady se nedokončí úspěšně.

- Pokud používáte System Center 2012 R2 VMM, 

    - Kontrola informací o databázi v systému VMM: -> **Nastavení** ->  **konzoly VMM****Obecné** -> **připojení databáze**
    - Zkontrolujte účty služeb používané pro službu Agent správce virtuálních počítačů system center.
    - Ujistěte se, že máte zálohu databáze VMM.
    - Poznamenejte si název databáze zapojených serverů SCVMM. To lze provést přechodem na**General** -> **připojení**  -> **konzoly** ->  **VMM**Obecné databáze
    - Poznamenejte si ID VMM primárních i obnovení serverů VMM 2012R2. ID v oblasti VMM naleznete v registru "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Ujistěte se, že nové scvmms, které přidáte do clusteru má stejné názvy jako předtím. 

- Pokud replikujete mezi dvěma weby spravovanými scvmnaty na obou stranách, před upgradem primární strany nejprve upgradujte stranu obnovení.
  > [!WARNING]
  > Při inovaci scvmm 2012 R2 vyberte v části Správa distribuovaných klíčů **ukládání šifrovacích klíčů ve službě Active Directory**. Pečlivě zvolte nastavení účtu služby a správu distribuovaného klíče. Na základě vašeho výběru nemusí být šifrovaná data, jako jsou hesla v šablonách, po upgradu dostupná a mohou potenciálně ovlivnit replikaci pomocí Azure Site Recovery.

> [!IMPORTANT]
> Podívejte se prosím na podrobnou dokumentaci SCVMM [o předpokladech](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hostitelé systému Windows Server 2012 R2, které nespravuje scvmm 
Níže uvedený seznam kroků se vztahuje na konfiguraci uživatele z [hostitelů Hyper-V do Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) provedené podle tohoto [kurzu](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Jak je uvedeno v požadavcích, tyto kroky platí pouze pro scénář clusterovaného prostředí a nikoli v samostatné konfiguraci hostitele Hyper-V.

1. Podle pokynů proveďte [postupnou inovaci clusteru.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) spuštění procesu upgradu postupného clusteru.
2. S každým novým hostitelem Windows Serveru 2016, který je zaveden v clusteru, odeberte odkaz na hostitele Windows Server 2012 R2 z Azure Site Recovery pomocí následujících kroků uvedených [zde]. To by měl být hostitel, který jste se rozhodli vyprázdnit & vyprázdnění z clusteru.
3. Po spuštění příkazu *Update-VMVersion* pro všechny virtuální počítače byly upgrady dokončeny. 
4. Pomocí [zde](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) uvedených kroků zaregistrujte nového hostitele Windows Serveru 2016 do Azure Site Recovery. Vezměte prosím na vědomí, že web Hyper-V je již aktivní a stačí zaregistrovat nového hostitele v clusteru. 
5.  Přejděte na portál Azure a ověřte stav replikovaného stavu ve službě Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Upgrade hostitelů systému Windows Server 2012 R2 spravovaných samostatným serverem SCVMM 2012 R2
Před upgradem hostitelů systému Windows Sever 2012 R2 je třeba upgradovat zařízení SCVMM 2012 R2 na SCVMM 2016. Postupujte podle následujících kroků:-

**Upgrade samostatného SCVMM 2012 R2 na SCVMM 2016**

1.  Odinstalujte zprostředkovatele ASR tak, že přejdete na Ovládací panely -> Programy -> programy a funkce ->Obnovení webu Microsoft Azure a klikněte na Odinstalovat
2. [Zachovat databázi SCVMM a inovovat operační systém](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. V **části Přidat odebrat programy**vyberte možnost**Odinstalace** **v programu VMM** > . b. Vyberte **Odebrat funkce**a potom vyberte**V MM management Server a Konzola VMM**. c. V **části Možnosti databáze**vyberte zachovat **databázi**. d. Projděte si souhrn a klepněte na tlačítko **Odinstalovat**.

4. [Instalace vmm 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Spusťte SCVMM a zkontrolujte stav jednotlivých hostitelů v části **Karty prostředků.** **Refresh** Měli byste vidět stav jako "Potřebuje pozornost". 
17. Nainstalujte nejnovějšího [poskytovatele obnovení webu Microsoft Azure](https://aka.ms/downloaddra) na SCVMM.
16. Nainstalujte [nejnovějšího agenta služby Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) na každého hostitele clusteru. Aktualizujte, aby se zajistilo, že SCVMM bude moci úspěšně dotazovat hostitele.

**Upgrade hostitelů systému Windows Server 2012 R2 na Systém Windows Server 2016**

1. Postupujte podle [zde](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) uvedených kroků a spusťte proces postupné inovace clusteru. 
2. Po přidání nového hostitele do clusteru aktualizujte hostitele z konzoly SCVMM a nainstalujte agenta VMM na tohoto aktualizovaného hostitele.
3. Spusťte *update-VMVersion* aktualizovat verze virtuálních počítačů virtuálních počítačů. 
4.  Přejděte na portál Azure a ověřte stav replikovaného stavu virtuálních počítačů v trezoru služby Recovery Services. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Upgrade hostitelů Windows Serveru 2012 R2 spravuje vysoce dostupný server SCVMM 2012 R2
Před upgradem hostitelů systému Windows Sever 2012 R2 je třeba upgradovat zařízení SCVMM 2012 R2 na SCVMM 2016. Při upgradu serverů SCVMM 2012 R2 nakonfigurovaných pomocí Azure Site Recovery – smíšený režim bez dalších serverů VMM bez dalších serverů VMM & režimu Smíšeného režimu s dalšími servery VMM jsou podporovány následující režimy upgradu.

**Upgrade SCVMM 2012 R2 na SCVMM 2016**

1.  Odinstalujte zprostředkovatele ASR tak, že přejdete na Ovládací panely -> Programy -> programy a funkce ->Obnovení webu Microsoft Azure a klikněte na Odinstalovat
2. Postupujte podle [zde](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) uvedených kroků na základě způsobu upgradu, který chcete provést.
3. Spusťte konzolu SCVMM a zkontrolujte stav **Refresh** jednotlivých hostitelů v části **Karty prostředků.** Měli byste vidět stav jako "Potřebuje pozornost".
4. Nainstalujte nejnovějšího [poskytovatele obnovení webu Microsoft Azure](https://aka.ms/downloaddra) na SCVMM.
5. Aktualizujte [nejnovějšího agenta služby Microsoft Azure Recovery Service (MARS)](https://aka.ms/latestmarsagent) na každém hostiteli clusteru. Aktualizujte, abyste zajistili, že sc VMM bude moci úspěšně dotazovat hostitele.


**Upgrade hostitelů systému Windows Server 2012 R2 na Systém Windows Server 2016**

1. Postupujte podle [zde](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) uvedených kroků a spusťte proces postupné inovace clusteru.
2. Po přidání nového hostitele do clusteru aktualizujte hostitele z konzoly SCVMM a nainstalujte agenta VMM na tohoto aktualizovaného hostitele.
3. Spusťte *update-VMVersion* aktualizovat verze virtuálních počítačů virtuálních počítačů. 
4.  Přejděte na portál Azure a ověřte stav replikovaného stavu virtuálních počítačů v trezoru služby Recovery Services. 

## <a name="next-steps"></a>Další kroky
Po provedení upgradu hostitelů můžete provést [zkušební převzetí služeb při selhání](tutorial-dr-drill-azure.md) a otestovat stav replikace a stavu zotavení po havárii.

