---
title: Upgrade Windows serveru/System Center VMM 2012 R2 na Windows Server 2016-Azure Site Recovery
description: Naučte se upgradovat hostitele Windows Serveru 2012 R2 & SCVMM 2012 R2, které jsou nakonfigurované pomocí Azure Site Recovery, na Windows Server 2016 & SCVMM 2016.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b9869ae7dfbf5afd6b8d3b870a2ad4e56fd54c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250062"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Upgrade Windows Server serveru/System Center 2012 R2 VMM na Windows Server/VMM 2016 

V tomto článku se dozvíte, jak upgradovat hostitele Windows Serveru 2012 R2 & SCVMM 2012 R2, které jsou nakonfigurované pomocí Azure Site Recovery, na Windows Server 2016 & SCVMM 2016

Site Recovery přispívá ke strategii pro provozní kontinuitu a zotavení po havárii (BCDR). Služba zajišťuje, aby vaše úlohy virtuálních počítačů zůstaly k dispozici, i když se očekává a dojde k neočekávanému výpadku.

> [!IMPORTANT]
> Při upgradu hostitelů se systémem Windows Server 2012 R2, kteří jsou již nakonfigurováni pro replikaci pomocí Azure Site Recovery, je nutné postupovat podle kroků uvedených v tomto dokumentu. Jakákoli alternativní cesta zvolená pro upgrade může mít za následek nepodporované stavy a může způsobit přerušení replikace nebo schopnost provést převzetí služeb při selhání.


V tomto článku se dozvíte, jak upgradovat následující konfigurace ve vašem prostředí:

> [!div class="checklist"]
> * **Hostitelé Windows Serveru 2012 R2, kteří nejsou spravováni SCVMM** 
> * **Hostitelé Windows Serveru 2012 R2 spravované samostatným serverem SCVMM 2012 R2** 
> * **Hostitelé Windows Serveru 2012 R2 spravované pomocí vysoce dostupného serveru SCVMM 2012 R2**


## <a name="prerequisites--factors-to-consider"></a>Předpoklady & faktory, které je potřeba vzít v úvahu

Před upgradem Vezměte na vědomí následující: –

- Pokud máte hostitele s Windows Serverem 2012 R2, které nespravuje SCVMM, a jde o nastavení samostatného prostředí, při pokusu o provedení upgradu dojde k přerušení replikace.
- Pokud jste vybrali možnost*Neukládat moje klíče ve službě Active Directory pod správu distribuovaných klíčů*při instalaci SCVMM 2012 R2 na první místo, upgrade se neúspěšně dokončí.

- Pokud používáte System Center 2012 R2 VMM, 

    - Podívejte se na informace o databázi v nástroji VMM: nastavení **konzoly VMM**  ->  **settings**  ->  **Obecné**  ->  **připojení k databázi**
    - Ověřte účty služeb používané pro System Center Virtual Machine Manager službu agenta.
    - Ujistěte se, že máte zálohu databáze VMM.
    - Poznamenejte si název databáze zapojených serverů SCVMM. To se dá udělat tak, že přejdete na nastavení **konzoly VMM**  ->  **Settings**  ->  **Obecné**  ->  **připojení k databázi** .
    - Poznamenejte si ID služby VMM primárního serveru 2012R2 i serveru VMM pro obnovení. ID VMM můžete najít v registru "HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Ujistěte se, že nový SCVMMs, který přidáte do clusteru, má stejné názvy jako předtím. 

- Pokud provádíte replikaci mezi dvěma vašimi lokalitami, které jsou spravovány nástrojem SCVMMs na obou stranách, před upgradem primární strany se ujistěte, že jste nejdřív provedete upgrade služby Recovery.
  > [!WARNING]
  > Při upgradu SCVMM 2012 R2 můžete v části Správa distribuovaných klíčů vybrat možnost **ukládání šifrovacích klíčů ve službě Active Directory**. Vyberte nastavení pro účet služby a správu distribuovaných klíčů pečlivě. Na základě vašeho výběru nebudou možná po upgradu k dispozici šifrovaná data, například hesla v šablonách, a mohou mít pravděpodobně vliv na replikaci s Azure Site Recovery

> [!IMPORTANT]
> Další informace najdete v dokumentaci k nástroji SCVMM týkající se [požadavků](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations) .

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Hostitelé Windows Serveru 2012 R2, kteří nejsou spravováni SCVMM 
Níže uvedený seznam kroků se vztahuje na konfiguraci uživatele z [hostitelů Hyper-V do Azure](./hyper-v-azure-architecture.md) spouštěné pomocí následujícího [kurzu](./hyper-v-prepare-on-premises-tutorial.md) .

> [!WARNING]
> Jak je uvedeno v části požadavky, tyto kroky se vztahují pouze na scénář clusterového prostředí a nikoli na samostatné konfiguraci hostitele Hyper-V.

1. Postupujte podle kroků a proveďte [postupný upgrade clusteru.](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) k provedení procesu upgradu clusteru.
2. U každého nového hostitele se systémem Windows Server 2016, který je představený v clusteru, odeberte odkaz na hostitele se systémem Windows Server 2012 R2 z Azure Site Recovery podle kroků uvedených v části [zde]. Mělo by se jednat o hostitele, kterého jste se rozhodli vyprázdnit & vyřadit z clusteru.
3. Po spuštění příkazu *Update-VMVersion* pro všechny virtuální počítače se upgrady dokončily. 
4. Pomocí kroků uvedených [tady](./hyper-v-azure-tutorial.md#set-up-the-source-environment) zaregistrujete nového hostitele Windows serveru 2016, abyste mohli Azure Site Recovery. Upozorňujeme, že lokalita Hyper-V je už aktivní a Vy stačí zaregistrovat nového hostitele v clusteru. 
5.  Přejít na Azure Portal a ověřit stav replikovaného stavu v rámci Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Upgrade hostitelů se systémem Windows Server 2012 R2 spravovaných pomocí samostatného serveru SCVMM 2012 R2
Před upgradem hostitelů se systémem Windows Server 2012 R2 je třeba upgradovat SCVMM 2012 R2 na SCVMM 2016. Postupujte podle následujících kroků:-

**Upgrade samostatného SCVMM 2012 R2 na SCVMM 2016**

1.  Odinstalujte poskytovatele ASR tak, že přejdete na ovládací panel > programy-> programy a funkce – >Microsoft Azure Site Recovery a kliknete na odinstalovat.
2. [Uchování databáze SCVMM a upgrade operačního systému](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. V panelu **Přidat nebo odebrat programy**vyberte možnost odinstalovat **Nástroj VMM**  >  **Uninstall**. b. Vyberte **Odebrat funkce**a pak vyberte V**mm Server pro správu a konzolu VMM**. c. V **Možnosti databáze**vyberte **Zachovat databázi**. d. Zkontrolujte souhrn a klikněte na **odinstalovat**.

4. [Instalace nástroje VMM 2016](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Spusťte SCVMM a zaškrtněte na kartě **prostředky infrastruktury** stav všech hostitelů. Kliknutím na **aktualizovat** získáte nejnovější stav. Měl by se zobrazit stav "vyžaduje pozornost". 
17. Nainstalujte na SCVMM nejnovějšího [poskytovatele Microsoft Azure Site Recovery](https://aka.ms/downloaddra) .
16. Nainstalujte nejnovějšího [agenta služby Microsoft Azure Recovery Services (MARS)](https://aka.ms/latestmarsagent) na každého hostitele clusteru. Aktualizujte, aby se SCVMM mohl úspěšně dotazovat na hostitele.

**Upgrade hostitelů Windows Serveru 2012 R2 na Windows Server 2016**

1. Postupujte podle kroků uvedených [tady](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) a spusťte proces postupného upgradu clusteru. 
2. Po přidání nového hostitele do clusteru aktualizujte hostitele z konzoly SCVMM a nainstalujte agenta VMM do tohoto aktualizovaného hostitele.
3. Spusťte *Update-VMVersion* a aktualizujte verze virtuálních počítačů virtuálních počítačů. 
4.  Přejít na Azure Portal a ověřit replikovaný stav virtuálních počítačů v trezoru Recovery Services. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Upgrade hostitelů Windows Serveru 2012 R2 je spravovaný pomocí vysoce dostupného SCVMM 2012 R2 serveru.
Před upgradem hostitelů se systémem Windows Server 2012 R2 je třeba upgradovat SCVMM 2012 R2 na SCVMM 2016. Při upgradu serverů SCVMM 2012 R2 nakonfigurovaného s Azure Site Recovery kombinovaném režimu bez dalších serverů VMM & smíšený režim s dalšími servery VMM se podporuje následující režimy upgradu.

**Upgrade SCVMM 2012 R2 na SCVMM 2016**

1.  Odinstalujte poskytovatele ASR tak, že přejdete na ovládací panel > programy-> programy a funkce – >Microsoft Azure Site Recovery a kliknete na odinstalovat.
2. Postupujte podle kroků uvedených [tady](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) v závislosti na režimu upgradu, který chcete spustit.
3. Spusťte konzolu SCVMM a zaškrtněte na kartě **prostředky infrastruktury** stav všech hostitelů. Kliknutím na **aktualizovat** získáte nejnovější stav. Měl by se zobrazit stav "vyžaduje pozornost".
4. Nainstalujte na SCVMM nejnovějšího [poskytovatele Microsoft Azure Site Recovery](https://aka.ms/downloaddra) .
5. Aktualizujte nejnovějšího [agenta služby Microsoft Azure Recovery Services (MARS)](https://aka.ms/latestmarsagent) na každém hostiteli clusteru. Aktualizujte, aby se mohl SC VMM úspěšně dotazovat na hostitele.


**Upgrade hostitelů Windows Serveru 2012 R2 na Windows Server 2016**

1. Postupujte podle kroků uvedených [tady](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) a spusťte proces postupného upgradu clusteru.
2. Po přidání nového hostitele do clusteru aktualizujte hostitele z konzoly SCVMM a nainstalujte agenta VMM do tohoto aktualizovaného hostitele.
3. Spusťte *Update-VMVersion* a aktualizujte verze virtuálních počítačů virtuálních počítačů. 
4.  Přejít na Azure Portal a ověřit replikovaný stav virtuálních počítačů v trezoru Recovery Services. 

## <a name="next-steps"></a>Další kroky
Po dokončení upgradu hostitelů můžete provést [Test převzetí služeb při selhání](tutorial-dr-drill-azure.md) k otestování stavu replikace a stavu zotavení po havárii.

