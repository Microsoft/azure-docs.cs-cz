---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 37dc96cf965181c5acba74449c684a08035e37b2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264267"
---
Tento článek se zabývá některými běžnými dotazy uživatelů k virtuálním počítačům Azure vytvořeným pomocí modelu nasazení Classic.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Můžu migrovat virtuální počítač vytvořený v modelu nasazení Classic na nový model Resource Manager?
Ano. Pokyny k migraci najdete tady:

* [Migrace z modelu Classic na Azure Resource Manager pomocí Azure PowerShellu](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migrace z modelu Classic na Azure Resource Manager pomocí Azure CLI](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Můžete spouštět nejnovější verze Windows Serveru i celou řadu linuxových distribucí. Podrobnosti o podpoře najdete tady:

• Virtuální počítače s Windows – [Podpora serverového softwaru Microsoft pro virtuální počítače Azure](https://go.microsoft.com/fwlink/p/?LinkId=393550)

• Virtuální počítače s Linuxem – [Linux v distribucích schválených pro Azure](https://go.microsoft.com/fwlink/p/?LinkId=393551)

V případě imagí klienta Windows jsou pro předplatitele MSDN Azure Benefit a předplatitele MSDN s průběžnými platbami podle aktuálního využití pro vývoj/testování dostupné určité verze Windows 7 a Windows 8.1 pro úlohy vývoje a testování. Podrobnosti, včetně pokynů a omezení, najdete v tématu [Image klienta Windows pro předplatitele MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>Proč se skupiny vztahů přestávají používat jako zastaralé?
Skupiny vztahů jsou starším konceptem geografického seskupování zákaznických nasazení cloudových služeb a účtů úložiště v rámci Azure. Původně byly poskytovány za účelem vylepšení výkonu sítě mezi virtuálními počítači v raných návrzích sítě Azure. Podporovaly také počáteční verzi virtuálních sítí, které byly omezené na malou sadu hardwaru v oblasti.

Aktuální síť Azure v rámci oblasti je navržená tak, že skupiny vztahů už nejsou potřeba. Virtuální sítě navíc mají regionální rozsah, takže skupina vztahů už není při používání virtuální sítě potřeba. Kvůli těmto vylepšením už zákazníkům nedoporučujeme používat skupiny vztahů, protože v některých scénářích můžou být omezující. Používání skupin vztahů zbytečně přidruží vaše virtuální počítače ke konkrétnímu hardwaru, který omezuje výběr dostupných velikostí virtuálních počítačů. Může to také vést k chybám souvisejícím s kapacitou při pokusu o přidání nových virtuálních počítačů, pokud se konkrétní hardware přidružený ke skupině vztahů blíží svojí kapacitě.

Funkce skupiny vztahů už se nepoužívají v modelu nasazení Azure Resource Manager ani na webu Azure Portal. Na portálu Azure Classic přestáváme podporovat vytváření skupin vztahů a vytváření prostředků úložiště připnutých ke skupině vztahů. Stávající cloudové služby, které používají skupinu vztahů, není nutné upravovat. Pro nové cloudové služby byste však skupiny vztahů používat neměli, pokud to nedoporučí specialista podpory Azure.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může mít velikost až 1 TB. Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Účet úložiště Azure poskytuje úložiště pro disk operačního systému a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Jaké typy virtuálních pevných disků můžu použít?
Azure podporuje pouze virtuální pevné disky s pevnou velikostí a ve formátu VHD. Pokud máte disk ve formátu VHDX, který chcete použít v Azure, musíte ho nejprve převést pomocí Správce technologie Hyper-V nebo rutiny [convert-VHD](https://go.microsoft.com/fwlink/p/?LinkId=393656). Až to uděláte, pomocí rutiny [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (v režimu správy služby) nahrajte disk VHD do účtu úložiště v Azure, abyste ho mohli použít s virtuálními počítači.

* Pokyny pro Linux najdete v tématu [Vytvoření a nahrání virtuálního pevného disku obsahujícího operační systém Linux](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Jsou tyto virtuální počítače stejné jako virtuální počítače Hyper-V?
V mnoha ohledech jsou podobné 1. generaci virtuálních počítačů Hyper-V, ale nejsou úplně stejné. Oba typy poskytují virtualizovaný hardware a virtuální pevné disky ve formátu VHD jsou kompatibilní. To znamená, že je můžete přesouvat mezi Hyper-V a Azure. Tři hlavní rozdíly, které někdy překvapí uživatele Hyper-V, jsou:

* Azure neposkytuje přístup k virtuálnímu počítači prostřednictvím konzoly. Neexistuje žádná možnost přístupu k virtuálnímu počítači, dokud se nespustí.
* Virtuální počítače Azure ve většině [velikostí](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mají pouze 1 virtuální síťový adaptér, což znamená, že také můžou mít pouze 1 externí IP adresu. (Velikosti A8 a A9 používají druhý síťový adaptér pro komunikaci aplikací mezi instancemi v omezených scénářích.)
* Virtuální počítače Azure nepodporují funkce 2. generace virtuálních počítačů Hyper-V. Podrobnosti o těchto funkcích najdete v tématech [Specifikace virtuálních počítačů pro Hyper-V](https://technet.microsoft.com/library/dn592184.aspx) a [Přehled virtuálních počítačů 2. generace](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Můžou tyto virtuální počítače používat moji stávající místní síťovou infrastrukturu?
Pro virtuální počítače vytvořené v modelu nasazení Classic můžete pomocí služby Azure Virtual Network rozšířit vaši stávající infrastrukturu. Přístup je podobný jako při zakládání firemní pobočky. Můžete zřídit a spravovat virtuální privátní sítě (VPN) v Azure nebo je bezpečně připojit k místní IT infrastruktuře. Podrobnosti najdete v článku [Přehled služby Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

Při vytváření virtuálního počítače budete muset určit síť, do které má patřit. Existující virtuální počítač není možné připojit k virtuální síti. Můžete to však obejít tak, že z existujícího virtuálního počítače odpojíte virtuální pevný disk (VHD) a použijete ho k vytvoření nového virtuálního počítače s požadovanou konfigurací sítě.

## <a name="how-can-i-access--my-virtual-machine"></a>Jak můžu přistupovat k virtuálnímu počítači?
Budete muset vytvořit vzdálené připojení k přihlášení k virtuálnímu počítači pomocí připojení ke vzdálené ploše virtuálního počítače s Windows nebo Secure Shell (SSH) pro virtuální počítač s Linuxem. Pokyny najdete tady:

* [Jak se přihlásit k virtuálnímu počítači s Windows Serverem](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Podporují se maximálně 2 souběžná připojení, pokud server není nakonfigurovaný jako hostitel relace vzdálené plochy.  
* [Jak se přihlásit k virtuálnímu počítači s Linuxem](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). SSH ve výchozím nastavení umožňuje maximálně 10 souběžných připojení. Toto číslo můžete navýšit upravením konfiguračního souboru.

Pokud máte problémy se Vzdálenou plochou nebo SSH, nainstalujte rozšíření [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a použijte ho k opravení problému.

Pro virtuální počítače s Windows další možnosti zahrnují:

* Na webu Azure Portal vyhledejte virtuální počítač a potom klikněte na **resetovat vzdálený přístup** na panelu příkazů.
* Přečtěte si článek [Řešení problémů s připojením ke vzdálené ploše na virtuálním počítači Azure s Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Připojte se k virtuálnímu počítači prostřednictvím vzdálené komunikace prostředí Windows PowerShell nebo vytvořte další koncové body, aby se k virtuálnímu počítači mohly připojit další prostředky. Podrobnosti najdete v článku [Nastavení koncových bodů pro virtuální počítač](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Pokud už znáte technologii Hyper-V, možná hledáte podobný nástroj jako VMConnect. Azure žádný podobný nástroj nenabízí, protože přístup k virtuálnímu počítači prostřednictvím konzoly nepodporuje.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Můžu použít dočasný disk (jednotku D: ve Windows nebo adresář /dev/sdb1 v Linuxu) k ukládání dat?
Dočasný disk (jednotku D: ve Windows nebo adresář /dev/sdb1 v Linuxu) byste neměli používat k ukládání dat. Jsou to pouze dočasná úložiště, takže byste riskovali nenávratnou ztrátu dat. K tomu může dojít při přesunu virtuálního počítače na jiného hostitele. Mezi důvody možného přesunu virtuálního počítače patří změna velikosti virtuálního počítače, aktualizace hostitele nebo selhání hardwaru.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak můžu změnit písmeno jednotky dočasného disku?
Na virtuálním počítači s Windows můžete písmeno jednotky změnit přesunutím stránkovacího souboru a novým přiřazením písmen jednotek, ale budete se muset ujistit, že kroky provádíte v určitém pořadí. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Jak můžu upgradovat hostovaný operační systém?
Termín upgrade obecně znamená přesun na aktuálnější verzi operačního systému při zachování stejného hardwaru. U virtuálních počítačů Azure se proces přesunu na aktuálnější verzi pro Linux a Windows liší:

* U virtuálních počítačů s Linuxem použijte vhodné nástroje pro správu balíčků a postupy pro příslušnou distribuci.
* U virtuálních počítačů s Windows je potřeba migrovat server pomocí nějakého nástroje, jako jsou například Nástroje pro migraci systému Windows Server. Nepokoušejte se upgradovat hostovaný operační systém, který se nachází v Azure. Nepodporuje se to kvůli riziku ztráty přístupu k virtuálnímu počítači. Kdyby během upgradu došlo k problémům, mohli byste ztratit možnost spustit relaci vzdálené plochy a nemohli byste problémy odstranit.

Obecné podrobnosti o nástrojích a postupech pro migraci Windows Serveru najdete v tématu [Migrace rolí a funkcí na Windows Server](https://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Jaké je výchozí uživatelské jméno a heslo na virtuálním počítači?
Image poskytované Azure nemají předkonfigurované uživatelské jméno a heslo. Při vytváření virtuálního počítače pomocí některé z takových imagí budete muset zadat uživatelské jméno a heslo, které budete používat k přihlášení k virtuálnímu počítači.

Pokud jste uživatelské jméno nebo heslo zapomněli a máte nainstalovaného agenta virtuálního počítače, můžete nainstalovat rozšíření [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a použít ho k opravení problému.

Další podrobnosti:

* Pro Linuxové Image Pokud použijete Azure portal, "azureuser" je zadána jako výchozí uživatelské jméno, ale toto můžete změnit pomocí 'Z Galerie' místo 'Vytvořit' jako způsob, jak vytvořit virtuální počítač. Při použití možnosti Z galerie se také můžete rozhodnout, jestli se k přihlášení má používat heslo, klíč SSH nebo obojí. Uživatelský účet je neprivilegovaný uživatel s přístupem k příkazu sudo pro spouštění privilegovaných příkazů. Účet root je zakázaný.
* V případě imagí Windows bude potřeba zadat uživatelské jméno a heslo při vytváření virtuálního počítače. Účet se přidá do skupiny Administrators.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Může Azure na mém virtuálním počítači spouštět antivirové produkty?
Azure nabízí několik možností antivirových řešení, ale jejich správa je na vás. Například můžete potřebovat samostatné předplatné pro antimalwarový software a budete se muset rozhodnout, kdy spouštět prohledávání a instalovat aktualizace. Podporu antivirových produktů můžete přidat při vytváření virtuálního počítače s Windows nebo později pomocí rozšíření virtuálního počítače pro Microsoft Antimalware, Symantec Endpoint Protection nebo agenta TrendMicro Deep Security Agent. Rozšíření Symantec a TrendMicro umožňují použít bezplatnou, časově omezenou zkušební verzi předplatného nebo stávající podnikové předplatné. Microsoft Antimalware je zdarma. Podrobnosti najdete tady:

* [Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači Azure](https://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Jak nainstalovat a nakonfigurovat Trend Micro Deep Security jako službu na virtuálním počítači Azure](https://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Jaké mám možnosti zálohování a obnovení?
V určitých oblastech je dostupná služba Azure Backup jako verze Preview. Podrobnosti najdete v článku [Zálohování virtuálních počítačů Azure](../articles/backup/backup-azure-arm-vms.md). Další řešení jsou k dispozici od certifikovaných partnerů. Pokud chcete zjistit, co je právě k dispozici, prohledejte web Azure Marketplace.

Další možností je využít možnosti snímků úložiště objektů blob. Pokud to chcete provést, před každou operací, která se spoléhá na snímek objektu blob, budete muset vypnout virtuální počítač. Tím se uloží probíhající zápisy dat a systém souborů přejde do konzistentního stavu.

## <a name="how-does-azure-charge-for-my-vm"></a>Jak se v Azure účtují virtuální počítače?
Azure účtuje hodinovou cenu na základě velikosti virtuálního počítače a na jeho operačním systému. V případě neúplných hodin Azure účtuje jenom využité minuty. Pokud vytvoříte virtuální počítač pomocí image virtuálního počítače, která obsahuje určitý předinstalovaný software, můžou se uplatňovat další hodinové sazby za tento software. Azure účtuje úložiště pro operační systém virtuálního počítače a pro datové disky zvlášť. Dočasné diskové úložiště je zdarma.

Virtuální počítač se vám účtuje ve spuštěném nebo zastaveném stavu, ale ne ve stavu Zastaveno (přidělení zrušeno). Pokud chcete změnit stav virtuálního počítače na Zastaveno (přidělení zrušeno), proveďte jednu z následujících akcí:

* Vypnutí nebo odstranění virtuálního počítače z portálu Azure portal.
* Použijte rutinu Stop-AzureVM, která je dostupná v modulu Azure PowerShellu.
* Použijte operaci Shutdown Role (Vypnutí role) v rozhraní REST API pro správu služeb a pro element PostShutdownAction zadejte StoppedDeallocated.

Další podrobnosti najdete v článku [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Bude Azure restartovat můj virtuální počítač kvůli údržbě?
Azure někdy virtuální počítač restartuje jako součást pravidelných a plánovaných aktualizací údržby v datových centrech Azure.

K neplánovaným událostem údržby může docházet, když Azure zjistí závažný problém s hardwarem, který má vliv na váš virtuální počítač. V případě neplánovaných událostí Azure automaticky migruje virtuální počítač na hostitele, který je v pořádku, a restartuje virtuální počítač.

U každého samostatného virtuálního počítače (tj. virtuální počítače, které nejsou součástí žádné sady dostupnosti) Azure e-mailem upozorní správce služeb předplatného alespoň týden před plánovanou údržbou, protože během aktualizace může dojít k restartování virtuálních počítačů. U aplikací spouštěných na virtuálních počítačích může dojít k výpadku.

Chcete-li zobrazit protokoly restartování, pokud dojde k restartování z důvodu plánované údržby můžete použít také na webu Azure portal nebo Azure Powershellu. Podrobnosti najdete v tématu [Zobrazení protokolů restartování virtuálního počítače](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Pokud chcete zajistit redundanci, umístěte do stejné skupiny dostupnosti dva nebo více podobně nakonfigurovaných virtuálních počítačů. Tím pomůžete zajistit, že během plánované nebo neplánované údržby bude dostupný alespoň jeden virtuální počítač. Azure pro tuto konfiguraci zaručuje určité úrovně dostupnosti virtuálních počítačů. Podrobnosti najdete v článku [Správa dostupnosti virtuálních počítačů](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Další zdroje informací:
[Informace o virtuálních počítačích Azure](../articles/virtual-machines/virtual-machines-linux-about.md)

[Vytvoření a správa virtuálních počítačů s Linuxem pomocí Azure CLI](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Vytvoření a správa virtuálních počítačů s Windows pomocí Azure PowerShellu](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

