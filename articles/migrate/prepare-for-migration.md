---
title: Příprava počítačů na migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit místní počítače na migraci pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927475"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Příprava místních počítačů na migraci do Azure

Tento článek popisuje, jak připravit místní počítače před tím, než je začnete migrovat do Azure pomocí [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool).

V tomto článku:
> [!div class="checklist"]
> * Ověřte omezení migrace.
> * Ověřte požadavky na operační systém a omezení podpory.
> * Zkontrolujte přístup k adrese URL a portu pro počítače, které chcete migrovat.
> * Zkontrolujte změny, které může být třeba provést před zahájením migrace.
> * Nakonfigurujte nastavení tak, aby se po migraci zachovala písmena jednotek.
> * Připravte počítače, abyste se mohli po migraci připojit k virtuálním počítačům Azure.

## <a name="verify-migration-limitations"></a>Ověřit omezení migrace

- Pomocí migrace Azure Migrate serveru můžete posoudit až 35 000 virtuálních počítačů VMware nebo virtuálních počítačů Hyper-V v jednom Azure Migrate projektu. Projekt může kombinovat virtuální počítače VMware a virtuální počítače Hyper-V, a to až do omezení pro každý z nich.
- Pro migraci můžete vybrat až 10 virtuálních počítačů najednou. Pokud potřebujete replikovat víc, replikujte je ve skupinách po 10.
- Pro migraci bez agentů VMware můžete současně spustit až 100 replikace.

## <a name="verify-operating-system-requirements"></a>Ověřit požadavky na operační systém

- Ověřte, jestli jsou [operační systémy Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) podporované v Azure.
- Ověřte, jestli jsou vaše [distribuce pro Linux](../virtual-machines/linux/endorsed-distros.md) v Azure podporované.

## <a name="see-whats-supported"></a>Podívejte se, co je podporováno

U virtuálních počítačů VMware podporuje migrace serverů bez [agentů nebo migraci na základě agenta](server-migrate-overview.md).

- **Virtuální počítače VMware**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-vmware-migration.md) pro virtuální počítače VMware.
- **Virtuální počítače Hyper-v**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-hyper-v-migration.md) pro virtuální počítače Hyper-v.
- **Fyzické počítače**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-physical-migration.md) místních fyzických počítačů a dalších virtualizovaných serverů. 

## <a name="review-url-and-port-access"></a>Zkontrolovat přístup k adrese URL a portu

Počítače můžou během migrace potřebovat přístup k Internetu.

- **Zařízení Azure Migrate**: Zkontrolujte [adresy URL](migrate-appliance.md#url-access) a [porty](migrate-support-matrix-vmware-migration.md#agentless-ports) , které Azure Migrate zařízení potřebuje k přístupu během migrace bez agenta.
- **Migrace založená na agentech virtuálních počítačů VMware**: Zkontrolujte [adresy URL](migrate-replication-appliance.md#url-access) a [porty](migrate-replication-appliance.md#port-access) , které zařízení replikace používá během migrace založené na agentovi virtuálního počítače VMware. 
- **Hostitelé Hyper-v**: Zkontrolujte [adresy URL a porty](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) , které hostitelé Hyper-v potřebují k přístupu během migrace. 
- **Fyzické servery**: Zkontrolujte [adresy URL](migrate-replication-appliance.md#url-access) a [porty](migrate-replication-appliance.md#port-access) , které zařízení replikace používá během migrace fyzického serveru.

## <a name="verify-required-changes-before-migrating"></a>Před migrací ověřit požadované změny

Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Azure Migrate provede tyto změny automaticky pro virtuální počítače, na kterých běží tyto operační systémy:

- Red Hat Enterprise Linux 7.0 +, 6.5 +
- CentOS 7.0 +, 6.5 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7

Pro jiné operační systémy musíte počítače před migrací ručně připravit. 

### <a name="prepare-windows-machines"></a>Příprava počítačů s Windows

Pokud migrujete počítač s Windows, udělejte před migrací tyto změny. Pokud před provedením změn migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.

1. Povolte pro virtuální počítač Azure [sériovou konzoli Azure](../virtual-machines/troubleshooting/serial-console-windows.md) . Povolení konzoly vám pomůže při odstraňování potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí image disku. Spuštění bitové kopie disku je ekvivalentní restartování nového virtuálního počítače. 
2. Pokud migrujete počítače se systémem Windows Server 2003, nainstalujte integrační služby Hyper-V host v operačním systému virtuálního počítače. [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Příprava počítačů se systémem Linux

1. Nainstalujte integrační služby Hyper-V Linux. Většina nových verzí systému Linux distribucí zahrnuje integrační služby Hyper-V Linux ve výchozím nastavení.
2. Znovu sestavte inicializační image Linux, aby obsahovala potřebné ovladače technologie Hyper-V. Když znovu sestavíte inicializační image, zajistíte, že se virtuální počítač spustí v Azure (vyžaduje se jenom u některých distribucí).
3. [Povolí protokolování sériové konzole Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Povolení protokolování konzoly vám pomůže při odstraňování potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí image disku. Spuštění bitové kopie disku je ekvivalentní restartování nového virtuálního počítače.
4. Aktualizujte soubor mapování zařízení pomocí přidružení typu název zařízení-svazek, aby se používaly trvalé identifikátory zařízení.
5. Aktualizujte položky v souboru fstab tak, aby používaly trvalé identifikátory svazků.
6. Odeberte všechna pravidla udev, která vyhradí názvy rozhraní na základě adresy MAC, a tak dále.
7. Aktualizujte síťová rozhraní tak, aby přijímala IP adresu od DHCP.

Přečtěte si další informace o [postupu spuštění virtuálního počítače se systémem Linux v Azure](../virtual-machines/linux/create-upload-generic.md)a pokyny pro některé z oblíbených distribucí pro Linux.

## <a name="preserve-drive-letters-after-migration"></a>Po migraci zachovat písmena jednotek

Při migraci místního počítače na Microsoft Azure se může stát, že se písmena jednotek dalších datových disků změní z původních hodnot. 

Ve výchozím nastavení jsou virtuálním počítačům Azure přiřazená jednotka D pro použití jako dočasné úložiště. Přiřazení této jednotky způsobí, že se všechna ostatní připojená přiřazení jednotek úložiště zvýší o jedno písmeno. Například pokud vaše místní instalace používá datový disk, který je přiřazený k jednotce D pro instalace aplikací, přiřazení této jednotky se po migraci virtuálního počítače do Azure zvýší na jednotku E. Abyste zabránili tomuto automatickému přiřazení a zajistili, že Azure přiřadí k dočasnému svazku další písmeno volné jednotky, nastavte zásady sítě SAN (Storage Area Network) na **OnlineAll**:

1. Na místním počítači (ne na hostitelském serveru) otevřete příkazový řádek se zvýšenými oprávněními.
2. Zadejte **DiskPart**.
3. Zadejte **San**. Pokud není zachováno písmeno jednotky hostovaného operačního systému, vrátí se offline **sdílená nebo offline** .
4. V příkazovém řádku **nástroje DiskPart** zadejte **zásady sítě San = OnlineAll**. Toto nastavení zajistí, že se disky budou do režimu online, a zajistíte tak čtení a zápis na oba disky.
5. Během migrace testu můžete ověřit, že jsou písmena jednotek zachovaná.

## <a name="check-azure-vm-requirements"></a>Ověřit požadavky na virtuální počítače Azure

Místní počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítače Azure pro operační systém a architekturu, disky, nastavení sítě a pojmenovávání virtuálních počítačů. Před migrací ověřte požadavky na [virtuální počítače VMware a fyzické servery](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)a [virtuální počítače Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) .

## <a name="prepare-to-connect-after-migration"></a>Příprava na připojení po migraci

Virtuální počítače Azure se vytváří během migrace do Azure. Po dokončení migrace musíte být schopni se připojit k novým virtuálním počítačům Azure. K úspěšnému připojení se vyžaduje více kroků.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Příprava na připojení k virtuálním počítačům Azure s Windows

Na místních počítačích s Windows:

1. Nakonfigurujte nastavení systému Windows. Mezi tato nastavení patří odebrání statických trvalých tras nebo proxy WinHTTP.
2. Ujistěte se, že jsou spuštěné [požadované služby](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) .
3. Pokud chcete povolit vzdálená připojení k místnímu počítači, povolte vzdálenou plochu (RDP). Naučte [se používat prostředí PowerShell k povolení protokolu RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Pokud po migraci chcete získat přístup k virtuálnímu počítači Azure přes Internet, v bráně Windows Firewall na místním počítači Povolte protokol TCP a UDP ve veřejném profilu a nastavte RDP jako povolenou aplikaci pro všechny profily.
5. Pokud chcete po migraci získat přístup k virtuálnímu počítači Azure přes síť VPN typu Site-to-site, v bráně Windows Firewall na místním počítači Povolte protokol RDP pro doménu a privátní profily. Naučte se, jak [povolíte provoz protokolu RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Ujistěte se, že při migraci nečekají žádné aktualizace Windows na místním virtuálním počítači. V takovém případě můžou aktualizace po migraci začít instalovat na virtuálním počítači Azure a až do dokončení aktualizace se nebudete moct přihlásit k virtuálnímu počítači.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Příprava na připojení k virtuálním počítačům Azure se systémem Linux

Na místních počítačích se systémem Linux:

1. Ověřte, že je služba Secure Shell nastavená tak, aby se spouštěla automaticky při spuštění systému.
2. Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurace virtuálních počítačů Azure po migraci

Po dokončení migrace proveďte tyto kroky na virtuálních počítačích Azure, které se vytvoří:

1. Pokud se chcete připojit k virtuálnímu počítači přes Internet, přiřaďte virtuálnímu počítači veřejnou IP adresu. Pro virtuální počítač Azure je nutné použít jinou veřejnou IP adresu, než jste použili pro místní počítač. [Další informace](../virtual-network/virtual-network-public-ip-address.md).
2. Ověřte, že pravidla skupiny zabezpečení sítě (NSG) na virtuálním počítači povolují příchozí připojení k portu RDP nebo SSH.
3. Zkontrolujte [diagnostiku spouštění](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) a zobrazte si virtuální počítač.

> [!NOTE]
> Služba Azure bastionu nabízí privátní připojení RDP a SSH k virtuálním počítačům Azure. [Přečtěte si další informace](../bastion/bastion-overview.md) o této službě.

## <a name="next-steps"></a>Další kroky

Rozhodněte, kterou metodu chcete použít k [migraci virtuálních počítačů VMware](server-migrate-overview.md) do Azure, nebo zahajte migraci [virtuálních počítačů Hyper-V](tutorial-migrate-hyper-v.md) nebo [fyzických serverů nebo virtualizovaných nebo cloudových virtuálních počítačů](tutorial-migrate-physical-virtual-machines.md).
