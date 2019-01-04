---
title: Příprava zálohování virtuálních počítačů Azure pomocí Azure Backup
description: Popisuje postup přípravy virtuálních počítačů Azure pro zálohování pomocí služby Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: ee7a9c407a26f9334a854c98793db8fc01244e2a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994670"
---
# <a name="prepare-to-back-up-azure-vms"></a>Příprava na zálohování virtuálních počítačů Azure

Tento článek popisuje postup přípravy na zálohování virtuálních počítačů Azure pomocí [Azure Backup](backup-introduction-to-azure-backup.md) trezor služby Recovery Services. Příprava pro zálohy zahrnuje:


> [!div class="checklist"]
> * Než začnete, projděte si Podporované scénáře a omezení.
> * Ověřte požadavky, včetně požadavků na virtuální počítač Azure a připojení k síti.
> * Vytvořte trezor.
> * Vyberte, jak úložiště replikuje.
> * Zjištění virtuálních počítačů, nakonfigurujte nastavení zálohování a zásady.
> * Povolení zálohování pro vybrané virtuální počítače


> [!NOTE]
   > Tento článek popisuje, jak zálohovat virtuální počítače Azure s nastavením úložiště a vyberete virtuální počítače k zálohování. To je užitečné, pokud chcete zálohovat několik virtuálních počítačů. Virtuální počítač Azure můžete také zálohovat přímo z jeho nastavení virtuálního počítače. [Další informace](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Než začnete

1. [Získejte přehled](backup-azure-vms-introduction.md) služby Azure Backup pro virtuální počítače Azure.
2. Projděte si podrobnosti o podpoře a omezení níže.

   **Podpora a omezení** | **Podrobnosti**
   --- | ---
   **Operační systém Windows** | Windows Server 2008 R2 64-bit nebo novější.<br/><br/> Klient Windows 7 64-bit nebo novější.
   **Operační systém Linux** | Můžete zálohovat 64-bit Linuxových distribucí [nepodporuje v Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), s výjimkou CoreOS Linux.<br/><br/> Zkontrolujte operačních systémů Linux, který [podporu obnovení souboru](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Ostatní Linuxové distribuce může pracovat, za předpokladu, že agent virtuálního počítače je k dispozici na virtuálním počítači a podporovaný Python. Tyto distribuce ale podporované nejsou.
   **Oblast** | Můžete zálohovat virtuální počítače Azure ve všech [podporované oblasti](https://azure.microsoft.com/regions/#services). Pokud v oblasti se nepodporuje, nebudete moci vybrat při vytváření trezoru.<br/><br/> Nemůžete zálohovat a obnovovat v různých oblastech Azure. Pouze v rámci jedné oblasti.
   **Omezení dat na disku** | Nelze zálohovat virtuální počítače s více než 16 datových disků.
   **Sdílené úložiště** | Nedoporučujeme zálohování virtuálních počítačů pomocí sdíleného svazku clusteru nebo souborového serveru se Škálováním. Zapisovače sdíleného svazku clusteru se pravděpodobně nezdaří.
   **Šifrování pro Linux** | Zálohování virtuálních počítačů s Linuxem šifrované pomocí systému Linux Unified klíč instalační program (LUKS) se nepodporuje.
   **Konzistence virtuálních počítačů** | Azure Backup nepodporuje konzistence více virtuálních počítačů.
   **Sítě** | Zálohovat data neobsahuje síti připojené jednotky připojené k virtuálnímu počítači.<br/><br/>
   **Snímky** | Pořizování snímků na disku s povoleným akcelerátorem zápisu se nepodporuje. Blokuje zálohování Azure tak konzistentní snímek všech disků virtuálního počítače.
   **PowerShell** | Existuje několik akcí, které jsou k dispozici v prostředí PowerShell:<br/><br/> -Obnovování virtuálních počítačů spravovaných interní/externí nástroje pro vyrovnávání zatížení, nebo s více rezervovaných IP adres nebo adaptéry. [Další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> – Obnovení řadiče domény virtuálních počítačů v konfiguraci s více řadič domény. [Další informace](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
   **Systémový čas** | Azure Backup nepodporuje úpravu automatické hodiny pro letní čas – změny pro zálohy virtuálních počítačů Azure. Upravte zásady zálohování ručně podle potřeby.
   **Účty úložiště** | Pokud používáte účet úložiště s omezenou sítí, ujistěte se, že povolíte **Povolit důvěryhodné služby Microsoftu pro přístup k tomuto účtu úložiště** tak, aby služba Azure Backup můžete přístup k účtu. Obnovení na úrovni položek se nepodporuje pro účty úložiště s omezenou sítí.<br/><br/> V účtu úložiště, ujistěte se, že **virtuální sítí a bran firewall** nastavení Povolit přístup z **všechny sítě**.


## <a name="prerequisites"></a>Požadavky

- Trezor je nutné vytvořit ve stejné oblasti jako virtuální počítače Azure, které chcete zálohovat.
- Než začnete, zkontrolujte oblastech virtuálního počítače Azure.
    - Pokud máte virtuální počítače v několika oblastech, vytvořte trezor v jednotlivých oblastech.
    - Není potřeba specifikovat účty úložiště pro ukládání dat záloh. Trezor a služba Azure Backup, která umožňují automaticky zpracovat.
- Ověřte, zda je agent virtuálního počítače nainstalovaný na virtuálních počítačích Azure, které chcete zálohovat.



### <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače

Pokud chcete povolit zálohování, Azure Backup nainstaluje rozšíření zálohování (snímek virtuálního počítače nebo virtuálního počítače Linux snímku) do agenta virtuálního počítače, na kterém běží na virtuálním počítači Azure.
    -  Agent virtuálního počítače Azure je nainstalovaný ve výchozím nastavení na virtuálních počítačích Windows nasazené z image Azure Marketplace. Při nasazování image Azure Marketplace z portálu, Powershellu, CLI nebo šablony Azure Resource Manageru, je také nainstalován Agent virtuálního počítače Azure.
    - Pokud jste migrovali virtuální počítač v místním, není nainstalován agent nástroje a potřebujete ji nainstalovat před povolením zálohování pro virtuální počítač.

V případě potřeby nainstalujte agenta následujícím způsobem.

**VIRTUÁLNÍ POČÍTAČ** | **Podrobnosti**
--- | ---
**Virtuální počítače s Windows** | [Stáhněte a nainstalujte](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agenta má oprávnění správce na počítači.<br/><br/> Ověření instalace – nachází se v *C:\WindowsAzure\Packages* na virtuálním počítači, klikněte pravým tlačítkem myši WaAppAgent.exe > **vlastnosti**, > **podrobnosti** kartu. **Verze produktu** by mělo být 2.6.1198.718 nebo vyšší.
**Virtuální počítače s Linuxem** | Instalace pomocí RPM nebo DEB balíček z úložiště balíčků vaší distribuce je preferovanou metodu instalace a upgradování agenta Azure Linux. Všechny [poskytovatelé distribuce schválené pro](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) balíček agenta Azure Linux integrovat do své Image a úložiště. Agent je k dispozici na [Githubu](https://github.com/Azure/WALinuxAgent), ale doporučujeme nejprve nainstalovat z něj.
Pokud máte problémy se zálohováním virtuálních počítačů Azure, zkontrolujte, že je na virtuálním počítači správně nainstalovaný agent virtuálního počítače Azure pomocí následující tabulky. Tabulka poskytuje další informace o virtuální počítač agenta pro Windows a virtuální počítače s Linuxem.

### <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Rozšíření zálohování spuštěná na virtuálním počítači musí mít odchozí přístup k veřejným IP adresám Azure. Chcete-li povolit přístup můžete:


- **Pravidla NSG**: Povolit [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Můžete přidat pravidlo povolující přístup s použitím služby Azure Backup [značka služby](../virtual-network/security-overview.md#service-tags), namísto samostatně povolení každý rozsah adres a jejich správě v čase.
- **Proxy**: Nasazení proxy server HTTP pro směrování provozu.
- **Brány Firewall Azure**: Povolte přenos přes bránu Firewall služby Azure ve virtuálním počítači, pomocí značku plně kvalifikovaný název domény pro službu Azure Backup.

Při rozhodování mezi možnostmi, zvažte nevýhody.

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
**SKUPINA ZABEZPEČENÍ SÍTĚ** | Žádné další náklady. Jednoduchá správa pomocí značky služeb | Poskytuje přístup k celé Azure, a ne jenom úložiště. |
**Proxy server HTTP** | Podrobnou kontrolu nad adresy URL úložiště je povolený.<br/><br/> Přístup k jedné bodu Internetu pro virtuální počítače.<br/><br/> Další náklady pro proxy server.
**Plně kvalifikovaný název domény značky** | Snadno se používá, pokud máte brány Firewall Azure nastavit v podsíti virtuální sítě | Nejde vytvořit vlastní značky plně kvalifikovaný název domény nebo upravit plně kvalifikovaných názvů domén ve značce.



Pokud používáte Azure Managed Disks, může být nutné počáteční další portu (port 8443) na bránu firewall.



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Nastavit pravidlo skupiny zabezpečení sítě a povolit odchozí přístup k Azure

Pokud váš virtuální počítač Azure má přístup spravuje skupinu zabezpečení sítě, povolí odchozí přístup pro úložiště záloh na požadované rozsahy a porty.



1. Ve virtuálním počítači > **sítě**, klikněte na tlačítko **přidat pravidlo portu pro odchozí**.
- Pokud máte pravidlo odepírající přístup, nová Povolit pravidlo musí být vyšší. Pokud máte například **Deny_All** s prioritou 1 000, nové pravidlo sady pravidel musí být nastavena na méně než 1000.
2. V **přidat odchozí pravidlo zabezpečení**, klikněte na tlačítko **Upřesnit**.
3. Ve zdroji, vyberte **VirtualNetwork**.
4. V **zdrojové rozsahy portů**, zadejte hvězdičku (*) povolit přístup z jakéhokoli portu pro odchozí připojení.
5. V **cílové**vyberte **značka služby**. V seznamu vyberte úložiště. <region>. Oblast je oblast, ve které se nacházejí v úložišti a virtuální počítače, které chcete zálohovat.
6. V **rozsahy cílových portů**, vyberte port.

    - Virtuální počítač s nespravovanými disky a nešifrovaného účtu úložiště: 80
    - Virtuální počítač s nespravovanými disky a zašifrovaném účtu úložiště: 443 (výchozí nastavení)
    - Spravovaný virtuální počítač: 8443.
1. V **protokol**vyberte **TCP**.
2. V **Priority**, přiřaďte jí hodnotu priority menší než pravidla odmítnutí výše.
3. Zadejte název a popis pravidla a klikněte na tlačítko **OK**.

Pravidlo skupiny zabezpečení sítě můžete použít k více virtuálním počítačům povolit odchozí přístup k Azure pro službu Azure Backup.

Toto video vás provede procesem.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>Zálohování směrování provozu přes proxy server

Můžete směrovat provoz zálohování prostřednictvím proxy serveru a poté poskytnout proxy přístup k požadované oblasti Azure.

Měli byste nakonfigurovat váš proxy server, virtuální počítač povolit následující:

- Virtuální počítač Azure by měl směrovat všechen provoz protokolu HTTP mez pro veřejný internet přes proxy server.
- Proxy server by měl povolit příchozí provoz z virtuálních počítačů v příslušné virtuální sítě (VNet).
- Skupiny zabezpečení sítě **NSF uzamčení** potřebuje pravidlo, které umožňuje odchozí internetový provoz z virtuálního počítače proxy serveru.

Zde je, jak budete muset nastavit proxy server. Můžeme použít ukázkové hodnoty. By měl nahraďte je vlastními.

#### <a name="set-up-a-system-account-proxy"></a>Nastavení proxy účtu systému
Pokud nemáte účet proxy systému, nastavte jeden následujícím způsobem:

1. Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Spustit **PsExec.exe -i -s cmd.exe** spustit příkazový řádek pod účtem system.
3. Spusťte prohlížeč v kontextu systému. Příklad: **ProgramFiles%\Internet Explorer\iexplore.exe** pro aplikaci Internet Explorer.  
4. Definujte nastavení proxy serveru.
    - Na počítačích s Linuxem:
        - Přidejte tento řádek, který **/etc/prostředí** souboru:
            - **http_proxy =http://proxy IP adresa: proxy port**
        - Přidejte tyto řádky do **/etc/waagent.conf** souboru:
            - **HttpProxy.Host=proxy IP adresa**
            - **HttpProxy.Port=proxy port**
    - U počítačů s Windows, v nastavení prohlížeče určí, že má být použit proxy server. Pokud aktuálně používáte proxy server na uživatelský účet, můžete použít tento skript použijte nastavení na úrovni systému účet.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>Povolte příchozí připojení na proxy serveru

1. Povolte příchozí připojení v nastavení proxy serveru.
2. Například otevřete **brány Windows Firewall s pokročilým zabezpečením**.
    - Klikněte pravým tlačítkem na **příchozí pravidla** > **nové pravidlo**.
    - V **typ pravidla** vyberte **vlastní** > **Další**.
    - V **Program**vyberte **všechny programy** > **Další**.
    - V **protokoly a porty** nastavte typ **TCP**, **místní porty** k **specifické porty protokolu**, a **vzdálený port**k **všechny porty**.
    - Dokončete průvodce a zadejte název pravidla.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Přidejte pravidlo výjimky pro skupiny zabezpečení sítě

Na skupiny zabezpečení sítě **NSF uzamčení**, povolit provoz z jakéhokoli portu na 10.0.0.5 na jakoukoli adresu v Internetu na portu 80 (HTTP) nebo 443 (HTTPS).

- Následující skript prostředí PowerShell poskytuje příklad pro povolení provozu.
- Místo povolení odchozích všechny veřejné internetové adresy, můžete určit rozsah IP adres (-DestinationPortRange), nebo použijte značku storage.region služby.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>Povolit přístup přes bránu firewall značkou plně kvalifikovaný název domény

Můžete nastavit brány Firewall Azure umožňující přístup pro odchozí připojení pro síťový provoz do služby Azure Backup.

- [Další informace o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) nasazení Brána Firewall služby Azure.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/firewall/fqdn-tags) značky plně kvalifikovaný název domény.


## <a name="create-a-vault"></a>Vytvoření trezoru

Trezor služby Recovery Services pro zálohování ukládá zálohy a body obnovení vytvořené v čase a ukládá zásady zálohování přidružené k zálohovaného počítače. Vytvořte trezor následujícím způsobem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Na **centra** nabídce vyberte možnost **Procházet**a typ **služby Recovery Services**. Když začnete psát, váš vstup filtruje seznam prostředků. Vyberte **trezory služby Recovery Services**.

    ![Zadáním textu do pole a výběrem "Trezory služby Recovery Services" ve výsledcích](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Objeví se seznam trezorů Služeb zotavení.
3. Na **trezory služby Recovery Services** nabídce vyberte možnost **přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Trezory služby Recovery Services** se otevře podokno. To vás vyzve k zadání informací pro **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Podokno "Trezory služby recovery Services"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Jako **Název** zadejte popisný název pro identifikaci trezoru.
    - Název musí být jedinečný v rámci předplatného Azure.
    - Může obsahovat 2 až 50 znaků.
    - Musí začínat písmenem a může obsahovat jenom písmena, číslice a pomlčky.
5. Vyberte **předplatné** zobrazíte seznam dostupných předplatných. Pokud si nejste jistí, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Existují možnosti více pouze tehdy, pokud váš pracovní nebo školní účet je přidružený k více předplatným Azure.
6. Vyberte **skupiny prostředků** zobrazit seznam dostupných skupin prostředků nebo vyberte **nový** vytvořit novou skupinu prostředků. [Další informace](../azure-resource-manager/resource-group-overview.md) o skupinách prostředků.
7. Vyberte **umístění** vyberte zeměpisnou oblast trezoru. Trezor *musí* být ve stejné oblasti jako virtuální počítače, které chcete zálohovat.
8. Vyberte **Vytvořit**.
    - Může trvat nějakou dobu vytvoření trezoru.
    - Sledujte oznámení o stavu v oblasti pravém horním rohu portálu.
    ![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Poté, co je trezor vytvořený, zobrazí se v seznamu trezorů služby Recovery Services. Pokud svůj trezor nevidíte, vyberte **aktualizovat**.

## <a name="set-up-storage-replication"></a>Nastavení replikace úložiště

Ve výchozím nastavení, má váš trezor nastavené [geograficky redundantní úložiště (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Doporučujeme pro vaši primární zálohu GRS, ale můžete použít[místně redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro levnější možnost. 

Úprava replikace úložiště následujícím způsobem:

1. V trezoru > **infrastruktura zálohování**, klikněte na tlačítko **konfigurace zálohování**

   ![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. V **konfigurace zálohování**, upravte metodu redundance úložiště jako povinné a vyberte **Uložit**.


## <a name="configure-backup"></a>Konfigurace zálohování

Zjištění virtuálních počítačů v předplatném a konfiguraci zálohování.

1. V trezoru > **přehled**, klikněte na tlačítko **+ zálohování**

   ![Zálohování tlačítko](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Zálohování** a **cíl zálohování** otevření podokna.

2. V **cíl zálohování**> **ve kterém je spuštěná vaše úloha?** vyberte **Azure**. V **co chcete zálohovat?** vyberte **virtuálního počítače** >  **OK**. To v trezoru zaregistruje rozšíření virtuálního počítače.

   ![Zálohování a cíl zálohování podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Tento krok zaregistruje rozšíření virtuálního počítače v trezoru. **Cíl zálohování** podokno se zavře a **zásady zálohování** se otevře podokno.

3. V **zásady zálohování**, vyberte zásadu, kterou chcete přidružit k trezoru. Pak klikněte na **OK**.
    - Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou.
    - Klikněte na tlačítko **vytvořit nový** můžete vytvořit zásadu. [Další informace](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) o definování zásad.

    !["Zálohování" a "Zásady zálohování" podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. V **výběr virtuálních počítačů** podokně, vyberte virtuální počítače, které budou používat zadaných zásadách zálohování > **OK**.

    - Vybraný virtuální počítač se ověří.
    - Virtuální počítače můžete vybrat pouze ve stejné oblasti jako trezor. Virtuální počítače můžete zálohovat jenom v jednom trezoru.

   ![Podokno "Výběr virtuálních počítačů"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. V **zálohování**vyberte **povolit zálohování**.

   - To se zásada nasadí do trezoru a virtuálním počítačům a nainstaluje rozšíření zálohování na virtuální počítač agenta spuštěného na virtuálním počítači Azure.
   - Tento krok nevytvoří prvotní bod obnovení pro virtuální počítač.

   ![Tlačítko "Povolit zálohování"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po povolení zálohování:

- Zásada zálohování spustí podle vašeho plánu zálohování.
- Služba Backup nainstaluje rozšíření zálohování, jestli je virtuální počítač spuštěný.
    - Spuštěný virtuální počítač poskytuje největší šanci získání bodu obnovení, který je konzistentní v rámci aplikace.
    -  Ale i v případě, že je vypnutý a rozšíření nejde nainstalovat je zálohování virtuálního počítače. To se označuje jako *offline virtuálního počítače*. V takovém případě bude bod obnovení *konzistentní pro případ chyby*.
- Pokud chcete vytvořit zálohu na vyžádání okamžitě, pro virtuální počítač v **zálohované položky**, klikněte na tlačítko se třemi tečkami (...) vedle virtuálního počítače > **zálohovat nyní**.


## <a name="next-steps"></a>Další postup

- Řešení potíží, ke kterým dochází s [Azure VM agents](/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) nebo [zálohování virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).
- [Zálohování virtuálních počítačů Azure](backup-azure-vms-first-look-arm.md)
