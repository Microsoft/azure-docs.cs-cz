---
title: 'Azure Backup: Příprava na zálohování virtuálních počítačů'
description: Ujistěte se, že je vaše prostředí připravené pro zálohování virtuálních počítačů v Azure.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: zálohy. zálohování;
ms.service: backup
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: dd11c50940dc35524b6d10c6043e906cc813498d
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748285"
---
# <a name="prepare-to-back-up-azure-vms"></a>Příprava na zálohování virtuálních počítačů Azure

Tento článek popisuje kroky pro přípravu prostředí pro zálohování nasazení Azure Resource Manageru virtuálních počítačů (VM). Kroky uvedené v postupech pomocí webu Azure portal. Při zálohování virtuálního počítače se ukládají body obnovení, nebo data záloh v trezoru služby Recovery Services Backup.



Předtím, než ochrana (a zpět) virtuálního počítače nasazených pomocí Resource Manageru, ujistěte se, že být splněny tyto požadavky:

* Vytvořte nebo Identifikujte trezor služby Recovery Services *ve stejné oblasti jako virtuální počítač*.
* Výběru scénáře, definovat zásady zálohování a určení položek k ochraně.
* Kontrola instalace agenta virtuálního počítače (rozšíření) na virtuálním počítači.
* Zkontrolujte síťové připojení.
* Pro virtuální počítače s Linuxem, pokud chcete přizpůsobit zálohování prostředí pro zálohy konzistentní s aplikací, postupujte [postup pro konfiguraci předsnímkového a posnímkových skriptů](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Pokud tyto podmínky se již existují ve vašem prostředí, pokračujte [zálohování vašich virtuálních počítačů](backup-azure-arm-vms.md) článku. Pokud je potřeba nastavit nebo zkontrolujte některá z těchto požadovaných součástí, tento článek vás provede kroky.

## <a name="supported-operating-systems-for-backup"></a>Podporované operační systémy pro zálohování

 * **Linux**: Azure Backup podporuje [seznam distribucí, které Azure schvaluje](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), s výjimkou CoreOS Linux. Seznam operačních systémů Linux, které podporují obnovování souborů najdete v tématu [obnovení souborů ze záloh virtuálních počítačů](backup-azure-restore-files-from-vm.md#for-linux-os).

    > [!NOTE]
    > Další používání your vlastní distribuce Linuxu mohou práce, za předpokladu, že agent virtuálního počítače je k dispozici na virtuálním počítači a podporovaný Python. Tyto distribuce ale podporované nejsou.
    >
 * **Windows Server**, **klienta Windows**: verze starší než Windows Server 2008 R2 nebo Windows 7, nejsou podporovány.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Omezení při zálohování a obnovení virtuálního počítače
Předtím, než je připravit vaše prostředí, nezapomeňte seznámit s těmito omezeními:

* Zálohování virtuálních počítačů s více než 16 datových disků se nepodporuje.
* Zálohování virtuálních počítačů s Linuxem zašifrovaná pomocí šifrování na Linuxu Unified klíč instalační program (LUKS) se nepodporuje.
* Nedoporučujeme zálohování virtuálních počítačů, které obsahují konfiguraci sdílené svazky clusteru (CSV) nebo souborového serveru se Škálováním. Pokud budete hotovi, očekává se selhání zapisovačů sdíleného svazku clusteru. Vyžadují zahrnující všechny virtuální počítače, které jsou součástí konfigurace clusteru během úlohu snímku. Azure Backup nepodporuje konzistence více virtuálních počítačů.
* Zálohovaná data neobsahuje síti připojené jednotky připojené k virtuálnímu počítači.
* Nahrazení existujícího virtuálního počítače během obnovení se nepodporuje. Pokud se pokusíte obnovit virtuální počítač, virtuální počítač existuje, operaci obnovení se nezdaří.
* Mezi různými oblastmi zálohování a obnovení nejsou podporovány.
* Při konfiguraci back up, ujistěte se, **virtuální sítí a bran firewall** nastavení účtu úložiště povolit přístup ze všech sítí.
* Pro vybrané sítě, po dokončení konfigurace brány firewall a nastavení virtuální sítě pro váš účet úložiště, vyberte **Povolit důvěryhodné služby Microsoftu pro přístup k tomuto účtu úložiště** jako výjimku do Povolit službě Azure Backup přístup k účtu úložiště s omezenou sítí. Obnovení na úrovni položek se nepodporuje pro účty úložiště s omezenou sítí.
* Můžete zálohovat virtuální počítače ve všech veřejných oblastech Azure. (Viz [kontrolní seznam](https://azure.microsoft.com/regions/#services) z oblasti jsou podporované.) Pokud dnes není podporován oblasti, kterou hledáte, nezobrazí se v rozevíracím seznamu při vytváření trezoru.
* Obnovení řadiče domény (DC) virtuálního počítače, který je součástí konfigurace s více řadiči domény se podporuje jenom přes PowerShell. Další informace najdete v tématu [obnovení řadiče domény s více řadiči domény](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Snímek na akcelerátor zápisu disku se nepodporuje. Toto omezení blokuje schopnost služby Azure Backup provádět snímek konzistentní vzhledem k v aplikaci všech disků virtuálního počítače.
* Obnovení virtuálních počítačů, které mají následující konfigurace speciální sítě je podporována pouze prostřednictvím prostředí PowerShell. Virtuální počítače vytvořené v pracovním postupu obnovení v uživatelském rozhraní nebudou mít tyto konfigurace sítě, po dokončení operace obnovení. Další informace najdete v tématu [obnovení virtuálních počítačů se speciální konfigurací sítě](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Virtuální počítače v rámci konfigurace služby Vyrovnávání zatížení (interní a externí)
  * Virtuální počítače s víc vyhrazených IP adres
  * Virtuální počítače s více síťovými adaptéry

  > [!NOTE]
  > Azure Backup podporuje [Standard Managed SSD Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), nový typ trvalého úložiště pro virtuální počítače Microsoft Azure. Je podporované u spravovaných disků na [zásobníku zálohování virtuálních počítačů Azure V2](backup-upgrade-to-vm-backup-stack-v2.md).

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Vytvoření trezoru služeb zotavení pro virtuální počítač
Trezor služby Recovery Services je entita, která ukládá zálohy a body obnovení, které byly vytvořeny v čase. Trezor služby Recovery Services obsahuje také zásady zálohování, které jsou spojeny s chráněných virtuálních počítačů.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Na **centra** nabídce vyberte možnost **Procházet**a pak zadejte **služby Recovery Services**. Když začnete psát, váš vstup filtruje seznam prostředků. Vyberte **trezory služby Recovery Services**.

    ![Zadáním textu do pole a výběrem "Trezory služby Recovery Services" ve výsledcích](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Objeví se seznam trezorů Služeb zotavení.
1. Na **trezory služby Recovery Services** nabídce vyberte možnost **přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Trezory služby Recovery Services** se otevře podokno. To vás vyzve k zadání informací pro **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Podokno "Trezory služby recovery Services"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
1. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný v rámci předplatného Azure. Zadejte název, který obsahuje 2 až 50 znaků. Musí začínat písmenem a může obsahovat jenom písmena, číslice a pomlčky.
1. Vyberte **předplatné** zobrazíte seznam dostupných předplatných. Pokud si nejste jistí, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Existují možnosti více pouze tehdy, pokud váš pracovní nebo školní účet je přidružený k více předplatným Azure.
1. Vyberte **skupiny prostředků** zobrazit seznam dostupných skupin prostředků nebo vyberte **nový** vytvořit novou skupinu prostředků. Kompletní informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).
1. Vyberte **umístění** vyberte zeměpisnou oblast trezoru. Trezor *musí* být ve stejné oblasti jako virtuální počítače, které chcete chránit.

   > [!IMPORTANT]
   > Pokud si nejste jisti umístění, ve kterém jsou vaše virtuální počítače, zavřete dialogové okno Vytvoření trezoru a přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, musíte vytvořit trezor služby Recovery Services v každé oblasti. Vytvořte trezor nejprve v první oblasti, poté přejděte k další oblasti. Není potřeba specifikovat účty úložiště pro ukládání dat záloh. Trezor služby Recovery Services a služby Azure Backup, která umožňují automaticky zpracovat.
   >
   >

1. Vyberte **Vytvořit**. Vytvoření trezoru Služeb zotavení může chvíli trvat. Sledujte oznámení o stavu v oblasti pravém horním rohu portálu. Poté, co je trezor vytvořený, zobrazí se v seznamu trezorů služby Recovery Services. Pokud svůj trezor nevidíte, vyberte **aktualizovat**.

    ![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Teď, když jste vytvořili trezor, se naučte, jak nastavit replikaci úložiště.

## <a name="set-storage-replication"></a>Nastavení replikace úložiště
Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantní úložiště. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Ponechte možnost nastavení jako geograficky redundantní úložiště pro vaši primární zálohu. Pokud chcete levnější možnost, která není tak Trvanlivá, vyberte místně redundantní úložiště.

Chcete-li upravit nastavení replikace úložiště:

1. Na **trezory služby Recovery Services** podokně, vyberte svůj trezor.
    Po výběru trezoru, **nastavení** podokno (který obsahuje název trezoru v horní části) a v podokně Podrobnosti trezoru otevřete.

   ![Vyberte trezor ze seznamu trezorů záloh](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

1. Na **nastavení** podokně pomocí svislém posuvníku přejděte dolů k položce **spravovat** a vyberte **infrastruktura zálohování**. V **Obecné** vyberte **konfigurace zálohování**. Na **konfigurace zálohování** podokně zvolte pro svůj trezor možnost replikace úložiště. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště.

   ![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Pokud používáte Azure jako primární úložiště záloh koncový bod, pokračujte v používání geograficky redundantní úložiště. Pokud používáte Azure jako úložiště pro zálohování není váš primární koncový bod, vyberte místně redundantní úložiště. Další informace o možnosti úložiště v [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

1. Pokud jste změnili typ replikace úložiště, vyberte **Uložit**.

Po výběru možnosti úložiště pro svůj trezor jste připraveni přidružení virtuálního počítače v trezoru. Chcete-li začít přidružení, měli byste vyhledat a zaregistrovat virtuální počítače Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Výběr cíle zálohování, nastavení zásad a určení položek k ochraně
Předtím, než zaregistrujete virtuálních počítačů s trezorem služby Recovery Services, spusťte proces vyhledávání pro identifikaci všechny nové virtuální počítače k předplatnému. Proces zjišťování dotazuje na seznam virtuálních počítačů v rámci předplatného Azure. Pokud se najde nové virtuální počítače, na portálu zobrazí název cloudové služby a přidruženou oblastí. Na webu Azure Portal *scénář* je zadat v trezoru služby Recovery Services. *Zásady* je plán frekvence a doby pořizování bodů obnovení. Zásada také obsahuje rozsah uchování bodů obnovení.

1. Pokud již máte otevřený trezor Služeb zotavení, pokračujte ke kroku 2. Pokud nemáte trezor služby Recovery Services, otevřít, otevřete [webu Azure portal](https://portal.azure.com/). Na **centra** nabídce vyberte možnost **další služby**.

   a. V seznamu prostředků zadejte **Recovery Services**. Když začnete psát, váš vstup, vyfiltruje seznam. Když se zobrazí **trezory služby Recovery Services**, vyberte ji.

      ![Zadáním textu do pole a výběrem "Trezory služby Recovery Services" ve výsledcích](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Objeví se seznam trezorů Služeb zotavení. Pokud ve vašem předplatném nejsou žádné trezory, tento seznam je prázdný.

      ![Zobrazení seznamu trezorů služby Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Ze seznamu trezorů Služeb zotavení vyberte trezor.

      **Nastavení** podokně a v řídicím panelu trezoru pro zvolený trezor otevřete.

      ![Nastavení řídicího panelu trezoru a podokno](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
1. V nabídce řídicího panelu trezoru, vyberte **zálohování**.

   ![Zálohování tlačítko](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Zálohování** a **cíl zálohování** otevření podokna.

1. Na **cíle zálohování** podokně nastavte **ve kterém je spuštěná vaše úloha?** jako **Azure** a **co chcete zálohovat?** jako  **Virtuální počítač**. Pak vyberte **OK**.

   ![Zálohování a cíl zálohování podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Tento krok zaregistruje rozšíření virtuálního počítače v trezoru. **Cíl zálohování** podokno se zavře a **zásady zálohování** se otevře podokno.

   !["Zálohování" a "Zásady zálohování" podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
1. Na **zásady zálohování** podokně, vyberte zásadu zálohování, který chcete použít pro trezor.

   ![Výběr zásady zálohování](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou. Chcete-li vytvořit novou zásadu, vyberte z rozevírací nabídky **Vytvořit novou**. Pokyny k definování zásad zálohování naleznete v tématu [Definování zásad zálohování](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Vyberte **OK** přidružení zásady zálohování k trezoru.

   **Zásady zálohování** podokno se zavře a **výběr virtuálních počítačů** se otevře podokno.
1. Na **výběr virtuálních počítačů** podokně, vyberte virtuální počítače, které chcete přidružit k určené zásadě a vyberte **OK**.

   ![Podokno "Výběr virtuálních počítačů"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Vybraný virtuální počítač se ověří. Pokud nevidíte očekávaných virtuálních počítačů, zkontrolujte, že jsou virtuální počítače ve stejné oblasti Azure jako trezor služby Recovery Services. Pokud virtuální počítače stále nevidíte, zkontrolujte, že nejsou již chráněny pomocí jiného trezoru. Na řídicím panelu trezoru zobrazuje oblast tam, kde existuje trezoru služby Recovery Services.

1. Teď, když jste definovali všechna nastavení trezoru, **zálohování** vyberte **povolit zálohování**. V tomto kroku nasadíte tuto zásadu pro trezor a virtuální počítače. Tento krok nevytvoří prvotní bod obnovení pro virtuální počítač.

   ![Tlačítko "Povolit zálohování"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po úspěšném povolení zálohování, zásady zálohování se spouštět podle plánu. Pokud chcete generovat úlohu zálohování na vyžádání pro zálohování virtuálních počítačů teď vidět [aktivace úlohy zálohování](./backup-azure-vms-first-look-arm.md#initial-backup).

Pokud máte problémy registraci virtuálního počítače, viz následující informace o instalaci agenta virtuálního počítače a na připojení k síti. Pravděpodobně není nutné tyto informace Pokud chráníte virtuální počítače vytvořené v Azure. Ale pokud jste migrovali virtuální počítače do Azure, ujistěte se, že agent virtuálního počítače správně nainstalována a že váš virtuální počítač může komunikovat s virtuální sítí.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Nainstalujte agenta virtuálního počítače na virtuálním počítači
Pro fungování, Azure Backup rozšíření [agenta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) musí být nainstalovaný na virtuálním počítači Azure. Pokud byl váš virtuální počítač vytvořen z Azure Marketplace, agent virtuálního počítače je již nainstalován na virtuálním počítači.

Následující informace jsou poskytovány pro situace, ve kterém jsou *není* pomocí virtuálního počítače vytvořena z Azure Marketplace. **Například jste migrovali virtuální počítač z místního datacentra. V takovém případě se agent virtuálního počítače potřeba nainstalovat z důvodu ochrany virtuálního počítače.**

**Poznámka:**: Po instalaci agenta virtuálního počítače, musíte také pomocí Azure Powershellu k aktualizaci vlastností ProvisionGuestAgent tak zná Azure je nainstalovaný agent virtuálního počítače.

Pokud máte problémy se zálohováním virtuálních počítačů Azure, zkontrolujte, že je na virtuálním počítači správně nainstalovaný agent virtuálního počítače Azure pomocí následující tabulky. Tabulka poskytuje další informace o virtuální počítač agenta pro Windows a virtuální počítače s Linuxem.

| **Operace** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalace agenta virtuálního počítače |Stáhněte si a nainstalujte [MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace budete potřebovat oprávnění správce. |<li> Nainstalujte nejnovější [agenta pro Linux](../virtual-machines/extensions/agent-linux.md). K dokončení instalace budete potřebovat oprávnění správce. Doporučujeme vám však nainstalovat agenta z úložiště distribuce. Jsme **není vhodné řešit podobné** instalace agenta virtuálního počítače s Linuxem přímo z githubu.  |
| Aktualizace agenta virtuálního počítače |Aktualizace agenta virtuálního počítače je stejně jednoduchá, jako přeinstalace [binárních souborů agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |Postupujte podle pokynů v tématu [Aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Doporučujeme aktualizovat agenta ze svého úložiště distribuce. Jsme **není vhodné řešit podobné** aktualizace agenta virtuálního počítače s Linuxem přímo z githubu.<br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |
| Ověření instalace agenta virtuálního počítače |<li>Ve virtuálním počítači Azure přejděte do složky *C:\WindowsAzure\Packages*. <li>Měl by být přítomný soubor WaAppAgent.exe.<li> Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. Pole Verze produktu by mělo být 2.6.1198.718 nebo vyšší. |neuvedeno |

### <a name="backup-extension"></a>Rozšíření zálohování
Po instalaci agenta virtuálního počítače na virtuálním počítači, služba Azure Backup nainstaluje rozšíření zálohování s agentem virtuálního počítače. Služba Backup bezproblémově upgraduje a opravuje rozšíření zálohování.

Služba Backup nainstaluje rozšíření zálohování, jestli je virtuální počítač spuštěný. Spuštěný virtuální počítač poskytuje největší šanci získání bodu obnovení, který je konzistentní v rámci aplikace. Ale služba Backup i nadále zálohování virtuálního počítače, i když je vypnutý a rozšíření nebylo možné nainstalovat. To se označuje jako *offline virtuálního počítače*. V takovém případě bude bod obnovení *konzistentní pro případ chyby*.

## <a name="establish-network-connectivity"></a>Vytvoření připojení k síti
Spravovat snímky virtuálních počítačů, rozšíření zálohování vyžaduje připojení k Azure veřejné IP adresy. Bez správné připojení k Internetu vypršení časového limitu žádostí HTTP virtuálního počítače a selže celá operace zálohování. Pokud má vaše nasazení omezení přístupu na místě – pomocí skupiny zabezpečení sítě (NSG), například – zvolte jednu z těchto možností se poskytne jasný pro provoz zálohování:

* [Seznam povolených adres Azure datacenter IP rozsahy](http://www.microsoft.com/download/details.aspx?id=41653).
* Nasazení proxy server HTTP pro směrování provozu.

Když jste rozhodování o tom, kterou možnost použít, kompromisy konfigurace jsou mezi možnosti správy, podrobnou kontrolu a náklady.

| Možnost | Výhody | Nevýhody |
| --- | --- | --- |
| Rozsahy seznamu povolených IP adres |Žádné další náklady.<br><br>Pro otevření přístup v skupinu zabezpečení sítě, použijte **Set-AzureNetworkSecurityRule** rutiny. |Složitou správu jako ovlivněný rozsahy IP adres v průběhu času měnit.<br><br>Poskytuje přístup k celé Azure, a ne jenom úložiště. |
| Použít proxy server HTTP |Detailní kontrola v proxy serveru úložiště je povolené adresy URL.<br><br>Jeden bod internetový přístup k virtuálním počítačům.<br><br>Není v souladu s Azure IP adresa změní. |Další náklady pro spuštění virtuálního počítače se softwarem proxy serveru. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Rozsahy IP adres seznamu povolených IP adres datacentra Azure
Seznam povolených rozsahů IP adres datacentra Azure najdete v článku [web Azure](http://www.microsoft.com/download/details.aspx?id=41653) podrobné informace o rozsahy IP adres a pokyny.

Připojení ke službě storage konkrétní oblasti, můžete povolit pomocí [značky služeb](../virtual-network/security-overview.md#service-tags). Ujistěte se, že pravidlo, které umožňuje přístup k účtu úložiště má vyšší prioritu než pravidla, která blokuje přístup k Internetu.

![Skupina zabezpečení sítě se značky úložiště pro oblast](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Následující video vás provede krok za krokem postupem konfigurace značky služeb:

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!NOTE]
> Seznam značek služeb úložiště a oblastí najdete v tématu [značky služeb pro úložiště](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Použít proxy server HTTP pro zálohy virtuálních počítačů
Pokud zálohujete virtuální počítač, rozšíření zálohování na virtuálním počítači odešle snímku příkazy pro správu do služby Azure Storage pomocí rozhraní API protokolu HTTPS. Směrování provozu linka záložního telefonu na server proxy protokolu HTTP, protože se jedná o jedinou komponentou nakonfigurovaný pro přístup k veřejnému Internetu.

> [!NOTE]
> Nedoporučujeme ale software specifický proxy server, který byste měli použít. Ujistěte se, že vyberete proxy server, který je kompatibilní s kroky konfigurace, které následují.
>
>

Na následujícím obrázku ukazuje tři konfiguračních kroků nezbytných k používání proxy serveru HTTP:

* Cesty virtuálního počítače aplikace všechen provoz protokolu HTTP mez pro veřejný internet přes proxy server virtuálního počítače.
* Proxy virtuálních počítačů umožňuje příchozí provoz z virtuálních počítačů ve virtuální síti.
* Skupina zabezpečení sítě s názvem NSF uzamčení vyžaduje pravidlo zabezpečení, která umožňuje odchozí internetový provoz z virtuálního počítače proxy serveru.

Chcete-li použít proxy server HTTP pro komunikaci s veřejným Internetem, proveďte následující kroky.

> [!NOTE]
> Tyto kroky používají konkrétní názvy a hodnoty v tomto příkladu. Už při zadání (nebo vkládání) podrobnosti do kódu, použijte názvy a hodnoty pro vaše nasazení.

#### <a name="step-1-configure-outgoing-network-connections"></a>Krok 1: Konfigurace odchozí síťová připojení
###### <a name="for-windows-machines"></a>U počítačů s Windows
Tento postup nastaví konfiguraci proxy serveru pro místní systémový účet.

1. Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
1. Otevřete aplikaci Internet Explorer z řádku se zvýšenými oprávněními spustíte tento příkaz:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

1. V Internet Exploreru přejděte na **nástroje** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě**.
1. Ověřte nastavení proxy serveru pro systémový účet. Nastavte proxy IP a portu.
1. Zavřete aplikaci Internet Explorer.

Následující skript nastaví konfiguraci proxy pro celý počítač a použije ho k veškerý odchozí provoz protokolu HTTP nebo HTTPS. Pokud jste nastavili proxy server na aktuální uživatelský účet (není účet místního systému), a aplikovat je na SYSTEMACCOUNT pomocí tohoto skriptu.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Pokud zjistíte "(407) vyžadováno ověřování proxy serveru" v protokolu proxy serveru, zkontrolujte, že je správně nastavené ověření.
>
>

###### <a name="for-linux-machines"></a>Pro počítače s Linuxem
Přidejte následující řádek, který ```/etc/environment``` souboru:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Přidejte následující řádky do ```/etc/waagent.conf``` souboru:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Krok 2: Povolte příchozí připojení na proxy serveru
1. Na proxy serveru otevřete bránu Windows Firewall. Nejjednodušší způsob, jak přistupovat k bráně firewall je vyhledání **brány Windows Firewall s pokročilým zabezpečením**.
1. V **brány Windows Firewall s pokročilým zabezpečením** dialogové okno, klikněte pravým tlačítkem na **příchozí pravidla** a vyberte **nové pravidlo**.
1. V nové příchozí pravidlo průvodci na **typ pravidla** stránky, vyberte **vlastní** možnost a vyberte **Další**.
1. Na **Program** stránce **všechny programy** a vyberte **Další**.
1. Na **protokol a porty** stránky, zadejte následující informace a vyberte **Další**:
   * Pro **protokol typu**vyberte **TCP**.
   * Pro **místní port**vyberte **specifické porty protokolu**. Do následujícího pole zadejte číslo portu proxy serveru, který byl nakonfigurován.
   * Pro **vzdálený port**vyberte **všechny porty**.

Pro zbývající část průvodce přijměte výchozí nastavení, dokud se nedostanete na konec. Toto pravidlo potom zadejte název.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Krok 3: Přidejte pravidlo výjimky pro skupiny zabezpečení sítě
Následující příkaz přidá výjimku do skupiny zabezpečení sítě. Tato výjimka umožňuje přenos TCP z jakéhokoli portu na 10.0.0.5 na jakoukoli adresu v Internetu na portu 80 (HTTP) nebo 443 (HTTPS). Pokud budete potřebovat konkrétní port na veřejný internet, je potřeba přidat tento port k ```-DestinationPortRange```.

V příkazovém řádku prostředí Azure PowerShell zadejte následující příkaz:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Máte dotazy?
Pokud máte dotazy nebo pokud všechny funkce, které chcete zobrazit zahrnuty, [pošlete nám svůj názor](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Další postup
Teď připravíte prostředí pro zálohování virtuálního počítače, dalším logickým krokem je vytvoření zálohy. Plánování článek obsahuje podrobné informace o zálohování virtuálních počítačů.

* [Zálohování virtuálních počítačů](backup-azure-arm-vms.md)
* [Plánování infrastruktury zálohování virtuálních počítačů](backup-azure-vms-introduction.md)
* [Správa záloh virtuálních počítačů](backup-azure-manage-vms.md)
