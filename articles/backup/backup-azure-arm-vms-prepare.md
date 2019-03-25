---
title: Zálohování virtuálních počítačů Azure na trezor služby Recovery Services pomocí služby Azure Backup
description: Popisuje, jak zálohovat virtuální počítače Azure v trezoru služby Recovery Services pomocí služby Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3133f22a4d9ecd8a0ee4bff9f8b0be9c1f4eb705
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403659"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Zálohování virtuálních počítačů Azure na trezor služby Recovery Services

Tento článek popisuje, jak zálohovat virtuální počítače Azure do trezorů služby Recovery Services se [Azure Backup](backup-overview.md) služby. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověřte podpory a požadavky pro zálohování.
> * Příprava virtuálních počítačů Azure. Nainstalujte agenta virtuálního počítače Azure v případě potřeby a ověření odchozího přístupu pro virtuální počítače.
> * Vytvořte trezor.
> * Zjištění virtuálních počítačů a konfigurovat zásady zálohování.
> * Povolte zálohování pro virtuální počítače Azure.


> [!NOTE]
   > Tento článek popisuje, jak nastavit trezor a vyberte virtuální počítače k zálohování. To je užitečné, pokud chcete zálohovat několik virtuálních počítačů. Případně můžete [zálohování jednoho virtuálního počítače Azure](backup-azure-vms-first-look-arm.md) přímo z nastavení virtuálního počítače.

## <a name="before-you-start"></a>Než začnete


- [Kontrola](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura zálohování virtuálního počítače Azure.
- [Další informace o](backup-azure-vms-introduction.md) zálohování virtuálních počítačů Azure a linka záložního telefonu.
- [Zkontrolujte podporu](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačů Azure.


## <a name="prepare-azure-vms"></a>Příprava virtuálních počítačů Azure

V některých případech můžete potřebovat nastavit agenta virtuálního počítače Azure na virtuálních počítačích Azure nebo výslovně povolit odchozí přístup na virtuálním počítači.

### <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače 

Azure Backup zálohuje virtuální počítače Azure po instalaci rozšíření virtuálního počítače Azure agenta spuštěného na počítači. Pokud byl váš virtuální počítač vytvořen z image Azure marketplace, je agent nainstalován a spuštěn. Pokud vytváříte vlastní virtuální počítač nebo migrovat místní počítač, můžete potřebovat nainstalovat agenta ručně, jak je uvedené v tabulce.

**VM** | **Podrobnosti**
--- | ---
**Virtuální počítače s Windows** | 1. [Stáhněte a nainstalujte](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) soubor MSI agenta.<br/><br/> 2. Instalace pomocí oprávnění správce na počítači.<br/><br/> 3. Ověření instalace. V *C:\WindowsAzure\Packages* na virtuálním počítači, klikněte pravým tlačítkem myši WaAppAgent.exe > **vlastnosti**, > **podrobnosti** kartu. **Verze produktu** by mělo být 2.6.1198.718 nebo vyšší.<br/><br/> Pokud chcete aktualizovat agenta, ujistěte se, že žádná operace zálohování jsou spuštěné, a [znovu nainstalujte agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Virtuální počítače s Linuxem** | Instalace s použitím RPM nebo DEB balíček z úložiště balíčků vaší distribuce. Toto je upřednostňovanou metodou pro instalaci a upgrade agenta Azure Linux. Všechny [poskytovatelé distribuce schválené pro](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) balíček agenta Azure Linux integrovat do své Image a úložiště. Agent je k dispozici na [Githubu](https://github.com/Azure/WALinuxAgent), ale doporučujeme nejprve nainstalovat z něj.<br/><br/> Pokud chcete aktualizovat agenta, ujistěte se, že žádná operace zálohování jsou spuštěné a aktualizovat binární soubory.


### <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Rozšíření zálohování spuštěná na virtuálním počítači musí odchozí přístup k veřejným IP adresám Azure.

- Obvykle není nutné výslovně povolit odchozí síťový přístup pro virtuální počítač Azure mohla komunikovat s Azure Backup.
- Pokud narazíte na potíže s virtuálními počítači s připojením a pokud se zobrazí chyba **ExtensionSnapshotFailedNoNetwork** při pokusu o připojení, byste měli explicitně povolit přístup tak rozšíření zálohování sdělit veřejný Azure IP adresy pro provoz zálohování.


#### <a name="explicitly-allow-outbound-access"></a>Výslovně povolit odchozí přístup

Pokud váš virtuální počítač nemůže připojit ke službě Backup, výslovně povolte odchozí přístup pomocí jedné z metod uvedené v tabulce.

**Možnost** | **Akce** | **Podrobnosti** 
--- | --- | --- 
**Nastavení pravidla skupiny zabezpečení sítě** | Povolit [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Místo povolení a správa každý rozsah adres, můžete přidat pravidlo povolující přístup s použitím služby Azure Backup [značka služby](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). [Další informace](../virtual-network/security-overview.md#service-tags).<br/><br/> Žádné další náklady.<br/><br/> Jednoduchá správa pomocí značky služeb.
**Nasazení proxy serveru** | Nasazení proxy server HTTP pro směrování provozu. | Poskytuje přístup k celé Azure, a ne jenom úložiště.<br/><br/> Podrobnou kontrolu nad adresy URL úložiště je povolený.<br/><br/> Přístup k jedné bodu Internetu pro virtuální počítače.<br/><br/> Další náklady pro proxy server.
**Nastavení brány Firewall Azure** | Povolte přenos přes bránu Firewall služby Azure ve virtuálním počítači, pomocí značku plně kvalifikovaný název domény pro službu Azure Backup. |  Snadno se používá, pokud máte brány Firewall Azure nastavit v podsíti virtuální sítě<br/><br/> Nejde vytvořit vlastní značky plně kvalifikovaný název domény nebo upravit plně kvalifikovaných názvů domén ve značce.<br/><br/> Pokud používáte Azure Managed Disks, může být nutné počáteční další portu (port 8443) na bránu firewall.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Nastavit pravidlo skupiny zabezpečení sítě a povolit odchozí přístup k Azure

Pokud skupina zabezpečení sítě spravuje přístup k virtuálnímu počítači, povolí odchozí přístup pro úložiště záloh na požadované rozsahy a porty.

1. Ve vlastnostech virtuálního počítače > **sítě**, klikněte na tlačítko **přidat pravidlo portu pro odchozí**.
2. V **přidat odchozí pravidlo zabezpečení**, klikněte na tlačítko **Upřesnit**.
3. V **zdroj**vyberte **VirtualNetwork**.
4. V **zdrojové rozsahy portů**, zadejte hvězdičku (*) povolit přístup z jakéhokoli portu pro odchozí připojení.
5. V **cílové**vyberte **značka služby**. V seznamu vyberte **Storage.region**. Oblast je oblast, ve které se nacházejí v úložišti a virtuální počítače, které chcete zálohovat.
6. V **rozsahy cílových portů**, vyberte port.
    - Nespravovaného virtuálního počítače pomocí nešifrovaného účtu úložiště: 80
    - Nespravovaného virtuálního počítače s zašifrovaném účtu úložiště: 443 (výchozí nastavení)
    - Spravovaný virtuální počítač: 8443.
7. V **protokol**vyberte **TCP**.
8. V **Priority**, zadejte hodnotu priority menší než pravidla odmítnutí výše.
   - Pokud máte pravidlo odepírající přístup, nová Povolit pravidlo musí být vyšší.
   - Pokud máte například **Deny_All** s prioritou 1 000, nové pravidlo sady pravidel musí být nastavena na méně než 1000.
9. Zadejte název a popis pravidla a klikněte na tlačítko **OK**.

Pravidlo skupiny zabezpečení sítě můžete použít k více virtuálním počítačům povolit odchozí přístup. Toto video vás provede procesem.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Zálohování směrování provozu přes proxy server

Můžete směrovat provoz zálohování prostřednictvím proxy serveru a poté poskytnout proxy přístup k požadované oblasti Azure. Konfigurace proxy serveru virtuálního počítače umožňuje následující:

- Virtuální počítač Azure by měl směrovat všechen provoz protokolu HTTP mez pro veřejný internet přes proxy server.
- Proxy server by měl povolit příchozí provoz z virtuálních počítačů v příslušné virtuální sítě (VNet).
- Skupiny zabezpečení sítě **NSF uzamčení** potřebuje pravidlo, které umožňuje odchozí internetový provoz z virtuálního počítače proxy serveru.

###### <a name="set-up-the-proxy"></a>Nastavení serveru proxy

Pokud nemáte účet proxy systému, nastavte jeden následujícím způsobem:

1. Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Spustit **PsExec.exe -i -s cmd.exe** spustit příkazový řádek pod účtem system.
3. Spusťte prohlížeč v kontextu systému. Příklad: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** pro aplikaci Internet Explorer.  
4. Definujte nastavení proxy serveru.
   - Na počítačích s Linuxem:
     - Přidejte tento řádek, který **/etc/prostředí** souboru:
       - **http_proxy=http:\//proxy IP address:proxy port**
     - Přidejte tyto řádky do **/etc/waagent.conf** souboru:
         - **HttpProxy.Host=proxy IP adresa**
         - **HttpProxy.Port=proxy port**
   - U počítačů s Windows, v nastavení prohlížeče určí, že má být použit proxy server. Pokud aktuálně používáte proxy server na uživatelský účet, můžete použít tento skript použijte nastavení na úrovni účtu system.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Povolte příchozí připojení na proxy serveru

Povolte příchozí připojení v nastavení proxy serveru.

1, v bráně Windows Firewall otevřete **brány Windows Firewall s pokročilým zabezpečením**.
2. Klikněte pravým tlačítkem na **příchozí pravidla** > **nové pravidlo**.
3. V **typ pravidla** vyberte **vlastní** > **Další**.
4. V **Program**vyberte **všechny programy** > **Další**.
5. V **protokoly a porty**:
   - Nastavte typ **TCP**
   - Nastavte **místní porty** k **specifické porty protokolu**
   - Nastavte **vzdálený port** k **všechny porty**.
  
6. Dokončete průvodce a zadejte název pravidla.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Přidejte pravidlo výjimky pro skupiny zabezpečení sítě pro proxy server

Na skupiny zabezpečení sítě **NSF uzamčení**, povolit provoz z jakéhokoli portu na 10.0.0.5 na jakoukoli adresu v Internetu na portu 80 (HTTP) nebo 443 (HTTPS).

- Následující skript prostředí PowerShell poskytuje příklad pro povolení provozu.
- Místo povolení odchozích všechny veřejné internetové adresy, můžete určit rozsah IP adres (-DestinationPortRange), nebo použijte značku storage.region služby.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Povolit přístup přes bránu firewall značkou plně kvalifikovaný název domény

Můžete nastavit brány Firewall Azure umožňující přístup pro odchozí připojení pro síťový provoz do služby Azure Backup.

- [Další informace o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) nasazení Brána Firewall služby Azure.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/firewall/fqdn-tags) značky plně kvalifikovaný název domény.

## <a name="modify-storage-replication-settings"></a>Upravit nastavení replikace úložiště

Ve výchozím nastavení, má váš trezor nastavené [geograficky redundantní úložiště (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Doporučujeme účet úložiště GRS pro vaši primární zálohu.
- Můžete použít [místně redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro levnější možnost.

Změňte typ replikace úložiště následujícím způsobem:

1. Na portálu klikněte na nový trezor. V části **nastavení** klikněte na tlačítko **vlastnosti**.
2. V **vlastnosti**v části **konfigurace zálohování**, klikněte na tlačítko **aktualizace**.
3. Vyberte typ replikace úložiště a klikněte na tlačítko **Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Nakonfigurujte zásady zálohování

Zjištění virtuálních počítačů v předplatném a konfiguraci zálohování.

1. V trezoru > **přehled**, klikněte na tlačítko **+ zálohování**

   ![Zálohování tlačítko](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Zálohování** a **cíl zálohování** otevření podokna.

2. V **cíl zálohování**> **ve kterém je spuštěná vaše úloha?** vyberte **Azure**. V **co chcete zálohovat?** vyberte **virtuálního počítače** >  **OK**. To v trezoru zaregistruje rozšíření virtuálního počítače.

   ![Zálohování a cíl zálohování podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Tento krok zaregistruje rozšíření virtuálního počítače v trezoru. **Cíl zálohování** podokno se zavře a **zásady zálohování** se otevře podokno.

3. V **zásady zálohování**, vyberte zásadu, kterou chcete přidružit k trezoru. Pak klikněte na **OK**.
    - Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou.
    - Klikněte na tlačítko **vytvořit nový** můžete vytvořit zásadu. [Další informace](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) o definování zásad.

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

- Prvotní zálohování spustí podle vašeho plánu zálohování.
- Služba Backup nainstaluje rozšíření zálohování, jestli je virtuální počítač spuštěný.
    - Spuštěný virtuální počítač poskytuje největší šanci získání bodu obnovení, který je konzistentní v rámci aplikace.
    -  Ale i v případě, že je vypnutý a rozšíření nejde nainstalovat je zálohování virtuálního počítače. To se označuje jako offline virtuálního počítače. V takovém případě bude bod obnovení konzistentní při selhání. [Další informace]() Všimněte si, že Azure Backup nepodporuje úpravu automatické hodiny pro letní čas – změny pro zálohy virtuálních počítačů Azure. Upravte zásady zálohování ručně podle potřeby.

## <a name="run-the-initial-backup"></a>Spusťte prvotní zálohování

Prvotní zálohování se spustí podle plánu, pokud jste ručně ho chcete ihned spustit. Ji spusťte ručně následujícím způsobem:

1. V nabídce trezoru klikněte na tlačítko **zálohování položek**.
2. V **zálohování položek** klikněte na tlačítko **virtuálního počítače Azure**.
3. V **zálohování položek** seznamu, klikněte na symbol tří teček **...** .
4. Klikněte na tlačítko **zálohovat nyní**.
5. V **zálohovat nyní**, pomocí ovládacího prvku kalendáře vyberte poslední den uchování bodu obnovení > **OK**.
6. Sledujte oznámení o portálu. Můžete sledovat průběh úlohy v řídicím panelu trezoru > **úlohy zálohování** > **probíhá**. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.



## <a name="next-steps"></a>Další postup

- Vyřešte všechny problémy s [Azure VM agents](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) nebo [zálohování virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).
- [Obnovení](backup-azure-arm-restore-vms.md) virtuální počítače Azure.

