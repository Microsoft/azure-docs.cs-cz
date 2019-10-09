---
title: Příprava místních počítačů na migraci do Azure pomocí Azure Migrate
description: Tento článek popisuje, jak připravit místní počítače na migraci do Azure pomocí Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d2eeada500de0f174178234ab92b51bcf16b3502
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176734"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Příprava místních počítačů na migraci do Azure

Tento článek popisuje, jak připravit místní a cloudový počítač před tím, než začnete s migrací do Azure pomocí [migrace Azure Migrate serveru](migrate-services-overview.md#azure-migrate-server-migration).


V tomto článku:
> [!div class="checklist"]
> * Ověřte omezení migrace.
> * Ověřte požadavky na operační systém a omezení podpory.
> * Zkontrolujte přístup k adrese URL nebo portu pro počítače, které chcete migrovat.
> * Zkontrolujte změny, které může být třeba provést před zahájením migrace.
> * Nakonfigurujte nastavení tak, aby se po migraci zachovala písmena jednotek.
> * Připravte počítače, abyste se mohli po migraci připojit k virtuálním počítačům Azure.


## <a name="verify-migration-limitations"></a>Ověřit omezení migrace

- Pomocí migrace Azure Migrate serveru můžete posoudit až 35 000 virtuálních počítačů VMware nebo virtuálních počítačů Hyper-V v jednom Azure Migrate projektu. Projekt může kombinovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do omezení pro každý z nich.
- Pro migraci můžete vybrat až 10 virtuálních počítačů najednou. Pokud potřebujete replikovat víc, replikujte je ve skupinách po 10.
- Pro migraci bez agentů VMware můžete současně spustit až 100 replikace.

## <a name="verify-operating-system-requirements"></a>Ověřit požadavky na operační systém

- Ověřte, jestli jsou [operační systémy Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) podporované v Azure.
- Ověřte, jestli jsou vaše [distribuce pro Linux](../virtual-machines/linux/endorsed-distros.md) v Azure podporované.


## <a name="check-whats-supported"></a>Zjistit, co je podporováno

- Pro virtuální počítače VMware Azure Migrate migrace serveru podporuje [migraci bez agentů nebo na základě agenta](server-migrate-overview.md). Ověřte požadavky na virtuální počítače VMware a podporu pro migrace bez [agentů](migrate-support-matrix-vmware.md#migration---limitations) a [na základě agentů](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) .
- Ověřte [požadavky na migraci a podporu](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) pro virtuální počítače Hyper-V.
- Ověřte [požadavky na migraci a podporu](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) místních fyzických počítačů nebo jiných virtualizovaných serverů. Tyto požadavky jsou podobné požadavkům na virtuální počítače VMware.





## <a name="review-urlport-access"></a>Kontrola přístupu k adrese URL/portu

Počítače můžou během migrace potřebovat přístup k Internetu.

- Zkontrolujte adresy URL, které musí virtuální počítače VMware používat při migraci bez [agentů](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) nebo [na základě agentů](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) .
- Zkontrolujte adresy URL, které hostitelé Hyper-V potřebují k přístupu během migrace. Virtuální počítače Hyper-V nepotřebují přístup k Internetu.
- [Zkontrolujte adresy URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) , které fyzické počítače nebo jiné virtualizované servery potřebují k přístupu během migrace.
- V rámci migrace na základě agenta virtuálních počítačů VMware nebo fyzických serverů potřebuje služba mobility běžící na počítačích přístup k součástem Azure Migrate. V případě správy replikací služba spuštěná v počítači komunikuje s místním Azure Migratem zařízením replikace na portu HTTPS 443 příchozí. Počítače odesílají replikační data na Azure Migrate procesový Server na portu HTTPS 9443 příchozí. Tento port lze změnit.


## <a name="verify-required-changes-before-migration"></a>Před migrací ověřit požadované změny

Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Azure Migrate provede tyto změny automaticky pro virtuální počítače s těmito operačními systémy:
- Red Hat Enterprise Linux 6.5 +, 7.0 +
- CentOS 6.5 +, 7.0 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS
- Debian 7, 8

Pro jiné operační systémy musíte počítače před migrací ručně připravit. 

### <a name="prepare-windows-machines"></a>Příprava počítačů s Windows

Pokud migrujete počítač se systémem Windows, proveďte tyto změny před migrací. Pokud před provedením změn migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.

1. [Povolte konzolu sériového přístupu Azure](../virtual-machines/troubleshooting/serial-console-windows.md) pro virtuální počítač Azure. To pomáhá při řešení potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí s použitím bitové kopie disku. Jedná se o ekvivalent restartování nového virtuálního počítače. 
2. Pokud migrujete počítače se systémem Windows Server 2003, nainstalujte integrační služby technologie Hyper-V hosta do operačního systému virtuálního počítače. [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)

### <a name="prepare-linux-machines"></a>Příprava počítačů se systémem Linux

1. Nainstalujte integrační služby Hyper-V Linux. Ve výchozím nastavení zahrnuje Většina nových verzí systému Linux.
2. Znovu sestavte inicializační bitovou kopii systému Linux, aby obsahovala potřebné ovladače technologie Hyper-V. Tím se zajistí, že se virtuální počítač spustí v Azure a vyžaduje se jenom u některých distribucí.
3. [Povolí protokolování sériové konzole Azure](../virtual-machines/troubleshooting/serial-console-linux.md). To pomáhá při řešení potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí s použitím bitové kopie disku. Jedná se o ekvivalent restartování nového virtuálního počítače.
4. Aktualizujte soubor mapování zařízení s názvem zařízení na přidružení svazků, aby se používaly trvalé identifikátory zařízení.
5. Aktualizujte položky fstab tak, aby používaly trvalé identifikátory svazků.
6. Odeberte všechna pravidla udev, která vyhradí názvy rozhraní na základě adresy MAC atd.
7. Aktualizujte síťová rozhraní tak, aby přijímala IP adresu od DHCP.
8. [Přečtěte si další informace](../virtual-machines/linux/create-upload-generic.md) o krocích potřebných ke spuštění virtuálního počítače se systémem Linux v Azure a pokyny pro některé z oblíbených distribucí pro Linux.

## <a name="preserve-drive-letters-after-migration"></a>Po migraci zachovat písmena jednotek

Při migraci místního počítače na Microsoft Azure se může změnit písmeno jednotky dalších datových disků z předchozích hodnot. Ve výchozím nastavení jsou virtuální počítače Azure přiřazené jednotce D pro použití jako dočasné úložiště. Přiřazení této jednotky způsobí, že se všechna ostatní připojená přiřazení jednotek úložiště zvýší o jedno písmeno.

Například pokud vaše místní instalace používá datový disk, který je přiřazený k jednotce D pro instalace aplikací, přiřazení této jednotky se po migraci virtuálního počítače do Azure zvýší na jednotku E. Abyste zabránili tomuto automatickému přiřazení a zajistili, že Azure přiřadí k dočasnému svazku další písmeno volné jednotky, nastavte zásady sítě SAN (Storage Area Network) na OnlineAll následujícím způsobem:

1. Na místním počítači (ne na hostitelském serveru) otevřete příkazový řádek se zvýšenými oprávněními.
2. Zadejte text **DiskPart**.
3. Zadejte **San**. Pokud není zachováno písmeno jednotky hostovaného operačního systému, vrátí se offline **sdílená nebo offline** .
4. Do příkazového řádku **DiskPart** zadejte **SANPOLICY = ONLINEALL**. Toto nastavení zajišťuje, že se disky přenesou do režimu online a jsou čitelné i zapisovatelné.
5. Během migrace testu můžete ověřit, že jsou písmena jednotek zachovaná.


## <a name="check-azure-vm-requirements"></a>Ověřit požadavky na virtuální počítače Azure

Místní počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítače Azure pro operační systém a architekturu, disky, nastavení sítě a pojmenovávání virtuálních počítačů. Před migrací ověřte požadavky na [virtuální počítače nebo fyzické servery VMware](migrate-support-matrix-vmware.md#azure-vm-requirements)a [virtuální počítače Hyper-V](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) .


## <a name="prepare-to-connect-after-migration"></a>Příprava na připojení po migraci

Virtuální počítače Azure se vytváří během migrace do Azure. Po dokončení migrace musíte být schopni se připojit k novým virtuálním počítačům Azure. K úspěšnému připojení se vyžaduje několik kroků.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Příprava na připojení k virtuálním počítačům Windows Azure

Na místních počítačích s Windows udělejte toto:

1. Nakonfigurujte nastavení systému Windows. Patří mezi ně odebrání statických trvalých tras nebo proxy WinHTTP.
2. Ujistěte se, že [tyto služby](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) běží.
3. Pokud chcete povolit vzdálená připojení k místnímu počítači, povolte vzdálenou plochu (RDP). [Naučte](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) se, jak povolit protokol RDP pomocí PowerShellu.
4. Pokud po migraci chcete získat přístup k virtuálnímu počítači Azure přes Internet, v bráně Windows Firewall na místním počítači Povolte protokol TCP a UDP ve veřejném profilu a nastavte RDP jako povolenou aplikaci pro všechny profily.
5. Pokud chcete po migraci získat přístup k virtuálnímu počítači Azure přes síť VPN typu Site-to-site, v bráně Windows Firewall na místním počítači Povolte protokol RDP pro doménu a privátní profily. [Naučte](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) se, jak povolíte provoz protokolu RDP. 
6. Ujistěte se, že při migraci nečekají žádné aktualizace Windows na místním virtuálním počítači. V takovém případě můžou aktualizace po migraci začít instalovat na virtuálním počítači Azure a až do dokončení aktualizace se nebudete moct přihlásit k virtuálnímu počítači.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Příprava na připojení k virtuálním počítačům Azure se systémem Linux

Na místních počítačích se systémem Linux proveďte tyto kroky:

1. Ověřte, že je služba Secure Shell nastavená tak, aby se spouštěla automaticky při spuštění systému.
2. Ověřte, že pravidla brány firewall umožňují připojení SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurace virtuálních počítačů Azure po migraci

Po dokončení migrace proveďte následující na virtuálních počítačích Azure, které se vytvořily.

1. Pokud se chcete připojit k virtuálnímu počítači přes Internet, přiřaďte virtuálnímu počítači veřejnou IP adresu. Nemůžete použít stejnou veřejnou IP adresu pro virtuální počítač Azure, který jste použili pro místní počítač. [Další informace](../virtual-network/virtual-network-public-ip-address.md)
2. Ověřte, že pravidla skupiny zabezpečení sítě (NSG) na virtuálním počítači povolují příchozí připojení k portu RDP nebo SSH.
3. Zkontrolujte [diagnostiku spouštění](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) a zobrazte si virtuální počítač.

> [!NOTE]
> Služba Azure bastionu nabízí privátní připojení RDP a SSH k virtuálním počítačům Azure. [Přečtěte si další informace](../bastion/bastion-overview.md) o této službě.



## <a name="next-steps"></a>Další kroky

Rozhodněte, kterou metodu chcete použít pro [migraci virtuálních počítačů VMware](server-migrate-overview.md) do Azure, nebo začněte migrovat virtuální [počítače Hyper-V](tutorial-migrate-hyper-v.md) nebo [fyzické servery nebo virtualizované nebo cloudové virtuální počítače](tutorial-migrate-physical-virtual-machines.md).
