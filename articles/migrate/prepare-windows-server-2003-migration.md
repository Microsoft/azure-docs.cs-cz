---
title: Příprava serverů Windows Server 2003 na migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit servery Windows Server 2003 na migraci pomocí Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753739"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Příprava počítačů s Windows serverem 2003 k migraci

Tento článek popisuje, jak připravit počítače s Windows serverem 2003 pro migraci do Azure. 


> [!NOTE]
> [Rozšířená podpora Windows serveru 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) skončila 14. července 2015.  Tým podpory Azure bude dál pomáhat při řešení problémů, které se týkají spuštění Windows serveru 2003 v Azure. Tato podpora je však omezená na problémy, které nevyžadují řešení potíží nebo opravy na úrovni operačního systému. Doporučený postup je migrace vašich aplikací do instancí Azure, na kterých běží novější verze Windows serveru, abyste měli jistotu, že budete efektivně využívat flexibilitu a spolehlivost cloudu Azure. Pokud se ale pořád rozhodnete migrovat Windows Server 2003 do Azure, můžete použít nástroj Azure Migrate: Server pro migraci, pokud je Windows Server virtuální počítač, který běží na VMware nebo Hyper-V.


- Migraci bez agentů můžete použít k migraci [virtuálních počítačů Hyper-V](tutorial-migrate-hyper-v.md) a [virtuálních počítačů VMware](tutorial-migrate-vmware.md) do Azure.
- Aby bylo možné se připojit k virtuálním počítačům Azure po migraci, musí být na virtuálním počítači Azure nainstalované integrační služby technologie Hyper-V. Počítače se systémem Windows Server 2003 nemají ve výchozím nastavení nainstalovány.
- Neexistuje žádný přímý odkaz ke stažení pro instalaci integračních služeb technologie Hyper-V, takže je třeba provést následující akce:
    - U virtuálních počítačů Hyper-V, které nemají nainstalované, extrahujete instalační soubory pro integrační služby na počítači se systémem Windows Server 2012 R2/Windows Server 2012 s rolí Hyper-V a pak zkopírujete instalační program na počítač se systémem Windows Server 2003. Instalační soubory nejsou na počítačích se systémem Windows Server 2016 k dispozici.
    - U virtuálních počítačů VMware vytvoříte úlohu po spuštění, která nainstaluje integrační služby, když se virtuální počítač Azure spustí po migraci.


## <a name="install-on-hyper-v-vms"></a>Instalace na virtuální počítače Hyper-V

Před migrací zkontrolujte, jestli je nainstalovaná služba Hyper-V Integration Services, a pak v případě potřeby nainstalujte.

1. Postupujte podle [těchto pokynů](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) a ověřte, zda je nainstalována.
2. Pokud není nainstalovaný, přihlaste se k počítači se systémem Windows Server 2012 R2/Windows Server 2012 s rolí Hyper-V.
3. Přejděte do instalačního souboru na adrese **C:\Windows\System32\vmguest.ISO** a soubor připojte.
2. Zkopírujte instalační složku na počítač s Windows serverem 2003 a nainstalujte integrační služby.
4. Po instalaci můžete ve službě Integration Services ponechat výchozí nastavení. 

## <a name="install-on-vmware-vms"></a>Instalace na virtuální počítače VMware

1. Přihlaste se k počítači se systémem Windows Server 2012 R2/Windows Server 2012 pomocí role Hyper-V.
2. Přejděte do instalačního souboru na adrese **C:\Windows\System32\vmguest.ISO** a soubor připojte.
3. Zkopírujte instalační složku na virtuální počítač VMware.
4. Z příkazového řádku na virtuálním počítači spusťte příkaz ```gpedit.msc``` .
5. Otevřete okno **Konfigurace počítače**  >  skripty **nastavení systému Windows**  >  **(spuštění nebo vypnutí)**.
6. Do pole **po spuštění**  >  **Přidat**  >  **název skriptu** zadejte adresu setup.exe.
7. Po migraci do Azure se skript spustí při prvním spuštění virtuálního počítače Azure.
8. Ručně restartujte virtuální počítač Azure. V diagnostice spouštění je automaticky otevírané okno s oznámením, že je potřeba restartovat počítač.
9. Po spuštění skriptu a instalaci integračních služeb technologie Hyper-V na virtuálním počítači Azure můžete skript odebrat z jeho spuštění.
10. Po instalaci můžete ve službě Integration Services ponechat výchozí nastavení. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si požadavky na migraci pro virtuální počítače [VMware](migrate-support-matrix-vmware-migration.md) a [Hyper-V](migrate-support-matrix-hyper-v-migration.md) .
- Migrujte virtuální počítače [VMware](server-migrate-overview.md) a [Hyper-V](tutorial-migrate-hyper-v.md) .
