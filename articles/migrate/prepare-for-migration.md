---
title: Příprava počítačů na migraci pomocí Migrace Azure
description: Zjistěte, jak připravit místní počítače pro migraci pomocí Migrace Azure.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927475"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Příprava místních počítačů pro migraci do Azure

Tento článek popisuje, jak připravit místní počítače, než začnete s migrací do Azure pomocí [Migrace Azure: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool).

V tomto článku:
> [!div class="checklist"]
> * Ověřte omezení migrace.
> * Zkontrolujte požadavky na operační systém a omezení podpory.
> * Zkontrolujte adresu URL a přístup k portům pro počítače, které chcete migrovat.
> * Zkontrolujte změny, které je třeba provést před zahájením migrace.
> * Nakonfigurujte nastavení tak, aby písmena jednotek byla po migraci zachována.
> * Připravte počítače, abyste se po migraci mohli připojit k virtuálním počítačům Azure.

## <a name="verify-migration-limitations"></a>Ověření omezení migrace

- Pomocí migrace serveru Azure můžete vyhodnotit až 35 000 virtuálních počítačů VMware nebo virtuálních počítačích Hyper-V v jednom projektu migrace Azure. Projekt můžete kombinovat virtuální chodů VMware a Hyper-V virtuálních terminálů, až do limitů pro každý.
- Pro migraci můžete vybrat až 10 virtuálních<amů najednou. Pokud potřebujete replikovat více, replikovat ve skupinách po 10.
- U migrace bez agentů společnosti VMware můžete spustit až 100 replikací současně.

## <a name="verify-operating-system-requirements"></a>Ověření požadavků na operační systém

- Ověřte, že vaše [operační systémy Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) jsou podporované v Azure.
- Ověřte, že vaše [distribuce Linuxu](../virtual-machines/linux/endorsed-distros.md) jsou podporované v Azure.

## <a name="see-whats-supported"></a>Podívejte se, co je podporováno

U virtuálních měn VMware podporuje migrace serveru [bez agenta nebo migrace založená na agentech](server-migrate-overview.md).

- **Virtuální jádra VMware**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-vmware-migration.md) virtuálních měn VMware.
- Virtuální aplikace **Hyper-V**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-hyper-v-migration.md) pro virtuální aplikace Hyper-V.
- **Fyzické počítače**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-physical-migration.md) pro místní fyzické počítače a další virtualizované servery. 

## <a name="review-url-and-port-access"></a>Zkontrolovat adresu URL a přístup k portu

Počítače mohou během migrace potřebovat přístup k internetu.

- **Zařízení migrace Azure:** Zkontrolujte adresy URL a [porty,](migrate-appliance.md#url-access) ke kterým zařízení Azure Migrate potřebuje přístup během migrace bez agenta. [ports](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Migrace založená na agentech virtuálních zařízení VMware:** Zkontrolujte adresy URL a [porty,](migrate-replication-appliance.md#port-access) [které](migrate-replication-appliance.md#url-access) replikační zařízení používá během migrace na základě agenta virtuálního zařízení VMware. 
- **Hostitelé technologie Hyper-V:** Zkontrolujte [adresy URL a porty,](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) ke nini hostitelé technologie Hyper-V potřebují přístup během migrace. 
- **Fyzické servery**: Zkontrolujte adresy URL a [porty,](migrate-replication-appliance.md#port-access) [které](migrate-replication-appliance.md#url-access) replikační zařízení používá během fyzické migrace serveru.

## <a name="verify-required-changes-before-migrating"></a>Ověření požadovaných změn před migrací

Některé virtuální počítače můžou vyžadovat změny, aby je můžou běžet v Azure. Azure Migrate provádí tyto změny automaticky pro virtuální počítače, které běží tyto operační systémy:

- Red Hat Enterprise Linux 7.0+, 6.5+
- Centos 7,0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

U jiných operačních systémů je nutné ručně připravit počítače před migrací. 

### <a name="prepare-windows-machines"></a>Příprava počítačů se systémem Windows

Pokud migrujete počítač se systémem Windows, proveďte před migrací následující změny. Pokud migrujete virtuální počítač před prováděním změn, virtuální počítač nemusí spustit v Azure.

1. [Povolte službu Azure Serial Console](../virtual-machines/troubleshooting/serial-console-windows.md) pro virtuální počítač Azure. Povolení konzoly vám pomůže vyřešit potíže. Není nutné restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí bitové kopie disku. Spuštění bitové kopie disku je ekvivalentní restartování nového virtuálního počítače. 
2. Pokud migrujete počítače se systémem Windows Server 2003, nainstalujte službu Integrace hosta Hyper-V do operačního systému virtuálního počítače. [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Příprava linuxových strojů

1. Nainstalujte služby integrace Hyper-V Linuxu. Většina nových verzí linuxových distribucí ve výchozím nastavení obsahuje služby Integrace Hyper-V Linuxu.
2. Znovu sestavte bitovou kopii Linuxinitu tak, aby obsahovala potřebné ovladače Hyper-V. Opětovné sestavení image init zajišťuje, že virtuální počítač se spustí v Azure (vyžadované pouze na některé distribuce).
3. [Povolte protokolování služby Azure Serial Console](../virtual-machines/troubleshooting/serial-console-linux.md). Povolení protokolování konzoly vám pomůže vyřešit potíže. Není nutné restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí bitové kopie disku. Spuštění bitové kopie disku je ekvivalentní restartování nového virtuálního počítače.
4. Aktualizujte soubor mapy zařízení pomocí přidružení názvu zařízení ke svazku, abyste používali trvalé identifikátory zařízení.
5. Aktualizujte položky v souboru fstab tak, aby používaly trvalé identifikátory svazků.
6. Odeberte všechna pravidla udev, která vyhrazují názvy rozhraní na základě adresy MAC a tak dále.
7. Aktualizujte síťová rozhraní tak, aby z protokolu DHCP přijímali adresu IP.

Přečtěte si další informace o [postupech spuštění virtuálního počítače s Linuxem v Azure](../virtual-machines/linux/create-upload-generic.md)a získejte pokyny pro některé oblíbené distribuce Linuxu.

## <a name="preserve-drive-letters-after-migration"></a>Zachovat písmena jednotek po migraci

Když migrujete místní počítač do Microsoft Azure, písmena jednotek dalších datových disků se mohou změnit z původních hodnot. 

Ve výchozím nastavení jsou virtuálním můře Azure přiřazena jednotka D, která se používá jako dočasné úložiště. Toto přiřazení jednotky způsobí, že všechna ostatní přiřazení připojených jednotek úložiště se zvětší o jedno písmeno. Například pokud vaše místní instalace používá datový disk, který je přiřazen k jednotce D pro instalace aplikací, přiřazení pro tuto jednotku se přírůstky řídit E po migraci virtuálního počítače do Azure. Chcete-li tomuto automatickému přiřazení zabránit a zajistit, aby Azure přiřadil další písmeno volné jednotky k dočasnému svazku, nastavte zásady sítě SAN (Storage Area Network) na **OnlineAll**:

1. V místním počítači (nikoli na hostitelském serveru) otevřete příkazový řádek se zvýšenými oprávněními.
2. Zadejte **diskpart**.
3. Zadejte **san**. Pokud není zachováno písmeno jednotky hostovaného operačního systému, je **vráceno vše offline** nebo **sdílené offline.**
4. Na výzvu **DISKPART** zadejte **zásady sítě SAN=OnlineAll**. Toto nastavení zajišťuje, že disky jsou přepnuty do režimu online, a zajišťuje, že můžete číst a zapisovat na oba disky.
5. Během migrace testu můžete ověřit, zda jsou písmena jednotek zachována.

## <a name="check-azure-vm-requirements"></a>Kontrola požadavků na virtuální počítač Azure

Místní počítače, které replikujete do Azure, musí splňovat požadavky na virtuální počítače Azure pro operační systém a architekturu, disky, nastavení sítě a pojmenování virtuálních počítačů. Před migrací ověřte požadavky na [virtuální zařízení v systému VMware a fyzické servery](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)a [virtuální aplikace Hyper-V.](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)

## <a name="prepare-to-connect-after-migration"></a>Příprava na připojení po migraci

Virtuální počítače Azure se vytvářejí během migrace do Azure. Po migraci se musíte moct připojit k novým virtuálním počítačům Azure. Pro úspěšné připojení je zapotřebí více kroků.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Příprava na připojení k virtuálním počítačům Azure pro Windows

V místních počítačích s Windows:

1. Konfigurace nastavení systému Windows. Nastavení zahrnují odebrání všech statických trvalých tras nebo serveru proxy winhttp.
2. Ujistěte se, že jsou [spuštěny požadované služby.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
3. Povolte vzdálenou plochu (RDP) a povolte vzdálená připojení k místnímu počítači. Přečtěte si, jak [pomocí powershellu povolit přístup k prd](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Chcete-li získat přístup k virtuálnímu počítači Azure přes internet po migraci, v bráně Windows Firewall v místním počítači povolte protokolt tcp a UDP ve veřejném profilu a nastavte protokol RDP jako povolenou aplikaci pro všechny profily.
5. Pokud chcete po migraci přistupovat k virtuálnímu počítači Azure přes síť VPN mezi lokalitami, v bráně Windows Firewall v místním počítači povolte protokol RDP pro profily domény a soukromé. Přečtěte si, jak [povolit provoz v rdp](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Ujistěte se, že neexistují žádné aktualizace systému čekající na místní virtuální počítač při migraci. Pokud existují, aktualizace může začít instalovat na virtuálním počítači Azure po migraci a nebudete moci přihlásit do virtuálního počítače až do dokončení aktualizací.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Příprava na připojení k virtuálním počítačům Linux Azure

Na místních počítačích s Linuxem:

1. Zkontrolujte, zda je služba Zabezpečené prostředí nastavena na automatické spuštění při spuštění systému.
2. Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurace virtuálních počítačů Azure po migraci

Po migraci proveďte tyto kroky na virtuálních počítačích Azure, které jsou vytvořeny:

1. Chcete-li se připojit k virtuálnímu virtuálnímu počítačům přes internet, přiřaďte virtuálnímu hoprotokolu veřejnou IP adresu. Pro virtuální počítač Azure musíte použít jinou veřejnou IP adresu, než jste použili pro místní počítač. [Další informace](../virtual-network/virtual-network-public-ip-address.md).
2. Zkontrolujte, zda pravidla skupiny zabezpečení sítě (NSG) na virtuálním počítači umožňují příchozí připojení k portu RDP nebo SSH.
3. Zkontrolujte [diagnostiku spuštění](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) a zobrazte virtuální ho.

> [!NOTE]
> Služba Azure Bastion nabízí privátní přístup RDP a SSH k virtuálním počítačům Azure. [Další informace](../bastion/bastion-overview.md) o této službě.

## <a name="next-steps"></a>Další kroky

Rozhodněte se, kterou metodu chcete použít k [migraci virtuálních počítačů VMware](server-migrate-overview.md) do Azure, nebo začněte migrovat [virtuální počítače Hyper-V](tutorial-migrate-hyper-v.md) nebo [fyzické servery nebo virtualizované nebo cloudové virtuální počítače](tutorial-migrate-physical-virtual-machines.md).
