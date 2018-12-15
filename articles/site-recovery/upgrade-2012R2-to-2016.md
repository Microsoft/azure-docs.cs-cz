---
title: Aktualizace hostitelů Windows Server 2012 R2 a SCVMM nakonfigurován pomocí Azure Site Recovery na Windows Server 2016
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro virtuální počítače Azure Stack pomocí služby Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 351213749dcec2b4c16728c04230c75a12179118
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410953"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Aktualizace hostitelů Windows Server 2012 R2, SCVMM 2012 R2 nakonfigurovaný službou Azure Site Recovery na Windows serveru 2016 a SCVMM 2016

Tento článek ukazuje, jak upgradovat hostitele Windows serveru 2012 R2 a 2012 R2 SCVMM, které jsou konfigurovány pomocí služby Azure Site Recovery na Windows serveru 2016 a SCVMM 2016

Site Recovery přispívá ke strategii obchodní kontinuity podnikových procesů a po havárii (BCDR) zotavení. Služba zajišťuje, že váš virtuální počítač, byly zpracovávané úlohy k dispozici, když byl očekáván a neočekávaných výpadků.

> [!IMPORTANT]
> Při upgradu systému Windows Server 2012 R2 hostitelů, které jsou už nakonfigurovaná pro replikaci s využitím Azure Site Recovery, musíte provést kroky uvedené v tomto dokumentu. Žádné alternativní cesta zvolená pro upgrade může vést k nepodporované stavy a může vést k přerušení v replikaci nebo schopnost provádět převzetí služeb při selhání.


V tomto článku se dozvíte, jak upgradovat následující konfigurace ve vašem prostředí:

> [!div class="checklist"]
> * **Hostitelé systému Windows Server 2012 R2, které nejsou spravovány serverem SCVMM** 
> * **Hostitelé systému Windows Server 2012 R2, které jsou spravovány službou samostatný server SCVMM 2012 R2** 
> * **Hostitelé systému Windows Server 2012 R2, které jsou spravovány serverem SCVMM 2012 R2 s vysokou dostupností**


## <a name="prerequisites--factors-to-consider"></a>Požadavky a faktory ke zvážení

Před upgradem, pamatujte na Tyhle věci:-

- Pokud máte hostitele systému Windows Server 2012 R2, které nejsou spravovány serverem SCVMM, a je instalace samostatné prostředí, bude konec replikace při pokusu provést upgrade.
- Pokud jste vybrali "*není ve službě Active Directory v části Správa distribuovaných klíčů uložit Moje klíče*" při instalaci SCVMM 2012 R2 na prvním místě, je aktualizován nebude dokončena úspěšně.

- Pokud používáte System Center 2012 R2 VMM 

    - Zkontrolujte informace o databázi VMM: **Konzola VMM** -> **nastavení** -> **Obecné** -> **připojení k databázi**
    - Zkontrolujte účty služeb, které se používají pro službu System Center Virtual Machine Manager Agent
    - Ujistěte se, že máte zálohu databáze VMM.
    - Poznamenejte si název databáze používané servery SCVMM. To můžete udělat tak, že přejdete do **konzoly VMM** -> **nastavení** -> **Obecné** -> **připojení k databázi**
    - Poznamenejte si ID VMM 2012 R2 primární a obnovení serverů VMM. VMM ID nenašlo se z registru "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Zajistěte, aby vám nový SCVMMs, které přidáte do clusteru stejné názvy jako byl před. 

- Pokud jsou replikaci mezi dvěma z vás už vaší lokality spravované tímto SCVMMs na obou stranách, ujistěte se, že vaše na straně obnovení nejprve upgradujete před upgradem na primární straně.
> [!WARNING]
> Při upgradu SCVMM 2012 R2, v části Správa distribuovaných klíčů, vyberte na **ukládání šifrovacích klíčů ve službě Active Directory**. Pečlivě zvolte nastavení pro účet služby a správu distribuovaných klíčů. Na základě vašeho výběru, šifrovaná data, jako jsou hesla v šablonách nemusí být k dispozici po upgradu a potenciálně může mít vliv na replikaci pomocí Azure Site Recovery

> [!IMPORTANT]
> Najdete v podrobné dokumentaci SCVMM [požadavky](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hostitelé systému Windows Server 2012 R2, které nejsou spravovány serverem SCVMM 
Seznam kroků uvedených dole platí pro konfiguraci uživatele z [hostitele Hyper-V do Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) spuštěn pomocí této [kurz](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Jak je uvedeno v požadavcích, tento postup platí jenom pro scénář prostředí clusteru a ne v samostatné konfiguraci hostitele Hyper-V.

1. Postupujte podle kroků provést [postupného upgradu clusteru.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) Chcete-li spustit proces postupného upgradu clusteru.
2. S každou novou Windows serveru 2016 hostitele, který byl představen v clusteru odeberte pomocí následujících kroků uvedených [tady] odkaz na hostitele Windows Server 2012 R2 z Azure Site Recovery. To by měl být hostitel, kterého jste se rozhodli vyprázdnit & vyřadit z clusteru.
3. Jednou *aktualizace VMVersion* byl proveden příkaz pro všechny virtuální počítače, upgrady byly dokončeny. 
4. Pomocí kroků uvedených [tady](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) k registraci nového hostitele Windows serveru 2016 do Azure Site Recovery. Mějte prosím na paměti, že už je aktivní lokality Hyper-V a potřebujete jenom k registraci nového hostitele v clusteru. 
5.  Přejděte na web Azure portal a ověřte stav replikované do služby Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Aktualizace hostitelů Windows Server 2012 R2 spravuje samostatný server SCVMM 2012 R2
Před provedením upgradu hostitelů Windows Server 2012 R2, musíte upgradovat SCVMM 2012 R2 na SCVMM 2016. Postupujte podle následujících kroků:-

**Upgradujte samostatné SCVMM 2012 R2 na SCVMM 2016**

1.  Odinstalace Azure Site Recovery provider tak, že přejdete do ovládacích panelů -> programy -> programy a funkce -> Microsoft Azure Site Recovery a klikněte na odinstalovat
2. [Zachování databáze SCVMM a upgrade operačního systému](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. V **Přidat odebrat programy**vyberte **VMM** > **odinstalovat**. b. Vyberte **odebrat funkce**a potom vyberte V**MM serveru pro správu a konzolí VMM**. c. V **možnosti databáze**vyberte **zachovat databázi**. d. Zkontrolujte souhrn a klikněte na tlačítko **odinstalovat**.

4. [Nainstalujte VMM 2016.](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Spusťte SCVMM a zkontrolujte stav každého hostitele pod **Fabric** kartu. Klikněte na tlačítko **aktualizovat** získat nejnovější stav. Zobrazí se stav jako "Potřebuje pozornost". 
17. Nainstalujte nejnovější [zprostředkovatele Microsoft Azure Site Recovery](http://aka.ms/downloaddra) v SCVMM.
16. Nainstalujte nejnovější [agenta Microsoft Azure Recovery Service (MARS)](http://aka.ms/latestmarsagent) na každého hostitele clusteru. Aktualizujte a ujistěte se, že je schopný úspěšně dotazování hostitele SCVMM.

**Aktualizace hostitelů Windows Server 2012 R2 na Windows Server 2016**

1. Postupujte podle kroků uvedených [tady](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) ke spuštění procesu postupného upgradu clusteru. 
2. Po přidání nového hostitele do clusteru, aktualizujte hostitele z konzoly SCVMM nainstalovat agenta VMM na tomto hostiteli aktualizovaný.
3. Spustit *aktualizace VMVersion* k aktualizaci verze virtuálního počítače z virtuálních počítačů. 
4.  Přejděte na web Azure portal a ověřte stav replikované virtuální počítače v rámci trezor služby Recovery Services. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Upgrade hostitelů Windows Server 2012 R2 jsou spravovány serverem SCVMM 2012 R2 s vysokou dostupností
Před provedením upgradu hostitelů Windows Server 2012 R2, musíte upgradovat SCVMM 2012 R2 na SCVMM 2016. Při upgradu SCVMM 2012 R2 servery nakonfigurované s Azure Site Recovery - ve smíšeném režimu se žádné další servery VMM a ve smíšeném režimu se další servery VMM jsou podporovány následující režimy upgrade.

**Upgradovat SCVMM 2012 R2 na SCVMM 2016**

1.  Odinstalace Azure Site Recovery provider tak, že přejdete do ovládacích panelů -> programy -> programy a funkce -> Microsoft Azure Site Recovery a klikněte na odinstalovat
2. Postupujte podle kroků uvedených [tady](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) podle režimu upgradu, kterou chcete spustit.
3. Spuštění konzoly SCVMM a zkontrolujte stav každého hostitele pod **Fabric** kartu. Klikněte na tlačítko **aktualizovat** získat nejnovější stav. Zobrazí se stav jako "Potřebuje pozornost".
4. Nainstalujte nejnovější [zprostředkovatele Microsoft Azure Site Recovery](http://aka.ms/downloaddra) v SCVMM.
5. Aktualizovat na nejnovější verzi [agenta Microsoft Azure Recovery Service (MARS)](http://aka.ms/latestmarsagent) na každého hostitele clusteru. Aktualizujte a ujistěte se, že je schopný úspěšně dotazování hostitele SC VMM.


**Aktualizace hostitelů Windows Server 2012 R2 na Windows Server 2016**

1. Postupujte podle kroků uvedených [tady](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) ke spuštění procesu postupného upgradu clusteru.
2. Po přidání nového hostitele do clusteru, aktualizujte hostitele z konzoly SCVMM nainstalovat agenta VMM na tomto hostiteli aktualizovaný.
3. Spustit *aktualizace VMVersion* k aktualizaci verze virtuálního počítače z virtuálních počítačů. 
4.  Přejděte na web Azure portal a ověřte stav replikované virtuální počítače v rámci trezor služby Recovery Services. 

## <a name="next-steps"></a>Další postup
Jakmile se provádí upgrade z hostitelů, můžete provádět [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) otestovat stav recovey stav replikace a po havárii.

