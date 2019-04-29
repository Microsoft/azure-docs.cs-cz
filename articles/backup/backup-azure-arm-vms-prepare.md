---
title: Zálohování virtuálních počítačů Azure na trezor služby Recovery Services pomocí služby Azure Backup
description: Popisuje, jak zálohovat virtuální počítače Azure v trezoru služby Recovery Services pomocí služby Azure Backup
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 98934216c0860c79575874df26603b1187e35978
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647540"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Zálohování virtuálních počítačů Azure na trezor služby Recovery Services

Tento článek popisuje, jak k zálohování virtuálních počítačů Azure na trezor služby Recovery Services pomocí [Azure Backup](backup-overview.md) služby. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Příprava virtuálních počítačů Azure.
> * Vytvořte trezor.
> * Zjištění virtuálních počítačů a konfigurovat zásady zálohování.
> * Povolte zálohování pro virtuální počítače Azure.
> * Spuštění prvotního zálohování.


> [!NOTE]
> Tento článek popisuje, jak nastavit trezor a vyberte virtuální počítače k zálohování. To je užitečné, pokud chcete zálohovat několik virtuálních počítačů. Alternativně můžete [zálohování jednoho virtuálního počítače Azure](backup-azure-vms-first-look-arm.md) přímo z nastavení virtuálního počítače.

## <a name="before-you-start"></a>Než začnete


- [Kontrola](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura zálohování virtuálního počítače Azure.
- [Další informace o](backup-azure-vms-introduction.md) zálohování virtuálních počítačů Azure a linka záložního telefonu.
- [Zkontrolujte podporu](backup-support-matrix-iaas.md) před konfigurací zálohování.

Kromě toho existuje několik věcí, které možná bude potřeba provést v některých případech:

- **Nainstalujte agenta virtuálního počítače na virtuálním počítači**: Azure Backup zálohuje virtuální počítače Azure po instalaci rozšíření virtuálního počítače Azure agenta spuštěného na počítači. Pokud byl váš virtuální počítač vytvořen z image Azure marketplace, je agent nainstalován a spuštěn. Pokud vytváříte vlastní virtuální počítač nebo migrovat místní počítač, možná budete muset [nainstalujte agenta ručně](#install-the-vm-agent).
- **Výslovně povolit odchozí přístup**: Obecně platí není nutné výslovně povolit odchozí síťový přístup pro virtuální počítač Azure mohla komunikovat s Azure Backup. Však několik virtuálních počítačů setkat s problémy s připojením, zobrazuje **ExtensionSnapshotFailedNoNetwork** při pokusu o připojení došlo k chybě. Pokud k tomu dojde, měli byste [výslovně povolit odchozí přístup](#explicitly-allow-outbound-access), takže rozšíření Azure Backup může komunikovat s Azure veřejné IP adresy pro provoz zálohování.


## <a name="create-a-vault"></a>Vytvoření trezoru

 Trezor ukládá zálohy a body obnovení vytvořené v čase a ukládá zásady zálohování přidružené k zálohovaného počítače. Vytvořte trezor následujícím způsobem:    

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).    
2. Do pole hledání zadejte **služby Recovery Services**. V části **služby**, klikněte na tlačítko **trezory služby Recovery Services**.   

     ![Hledat trezory služby Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. V **trezory služby Recovery Services** nabídky, klikněte na tlačítko **+ přidat**.    

     ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. V **trezor služby Recovery Services**, zadejte popisný název pro identifikaci trezoru.   
    - Název musí být jedinečný v rámci předplatného Azure.   
    - Může obsahovat 2 až 50 znaků.    
    - Musí začínat písmenem a může obsahovat jenom písmena, číslice a pomlčky.   
5. Vyberte předplatné Azure, skupinu prostředků a zeměpisné oblasti, ve kterém se vytvoří v trezoru. Poté klikněte na **Vytvořit**.    
    - Může trvat nějakou dobu vytvoření trezoru.  
    - Sledujte oznámení o stavu v oblasti pravém horním rohu portálu.   


 Po vytvoření trezoru se zobrazí v seznamu trezorů služby Recovery Services. Pokud svůj trezor nevidíte, vyberte **aktualizovat**.
 
![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení, trezory Recovery Services použijte [geograficky redundantní úložiště (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Pokud se vaše primární záložní mechanismus úložiště, doporučujeme že použít GRS.
- Můžete použít [místně redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro levnější možnost.

Změňte typ replikace úložiště následujícím způsobem:

1. V novém trezoru, klikněte na tlačítko **vlastnosti** v **nastavení** oddílu.
2. V **vlastnosti**v části **konfigurace zálohování**, klikněte na tlačítko **aktualizace**.
3. Vyberte typ replikace úložiště a klikněte na tlačítko **Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > Typ replikace úložiště nelze změnit poté, co je nastavit trezor a obsahuje zálohované položky. Pokud chcete k tomu je potřeba znovu vytvořit trezor. 

## <a name="apply-a-backup-policy"></a>Použít zásady zálohování

Nakonfigurujte zásady zálohování pro trezor.

1. V trezoru, klikněte na tlačítko **+ zálohování** v **přehled** oddílu.

   ![Zálohování tlačítko](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. V **cíl zálohování** > **ve kterém je spuštěná vaše úloha?** vyberte **Azure**. V **co chcete zálohovat?** vyberte **virtuálního počítače** >  **OK**. To v trezoru zaregistruje rozšíření virtuálního počítače.

   ![Zálohování a cíl zálohování podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. V **zásady zálohování**, vyberte zásadu, kterou chcete přidružit k trezoru. 
    - Výchozí zásady zálohuje virtuální počítač jednou denně. Denní zálohy se uchovávají po dobu 30 dnů. Okamžité obnovení snímky zůstanou zachovány dva dny.
    - Pokud už nechcete používat výchozí zásadu, vyberte **vytvořit nový**a vytvořit vlastní zásadu, jak je popsáno v následujícím postupu.

      ![Výchozí zásady zálohování](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. V **výběr virtuálních počítačů**, vyberte virtuální počítače, které chcete zálohovat, která zásadu používá. Pak klikněte na **OK**.

   - Vybrané virtuální počítače se ověří.
   - Virtuální počítače můžete vybrat pouze ve stejné oblasti jako trezor.
   - Virtuální počítače můžete zálohovat jenom v jednom trezoru.

     ![Podokno "Výběr virtuálních počítačů"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. V **zálohování**, klikněte na tlačítko **povolit zálohování**. To se zásada nasadí do trezoru a virtuálním počítačům a nainstaluje rozšíření zálohování na virtuální počítač agenta spuštěného na virtuálním počítači Azure.
     
     ![Tlačítko "Povolit zálohování"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po povolení zálohování:

- Služba Backup nainstaluje rozšíření zálohování, jestli je virtuální počítač spuštěný.
- Prvotní zálohování se spustí podle vašeho plánu zálohování.
- Při spuštění zálohování, Všimněte si, že:
    - Virtuální počítač, na kterém běží mít největší šanci pro zachytávání jako bod obnovení konzistentní vzhledem k aplikaci.
    - Nicméně i v případě, že virtuální počítač je vypnutý, bude zálohován. Virtuální počítač se označuje jako offline virtuálního počítače. V takovém případě bude bod obnovení konzistentní při selhání.
    

### <a name="create-a-custom-policy"></a>Vytvoření vlastní zásady

Pokud jste se rozhodli vytvořit nové zásady zálohování, zadejte v nastavení zásad.

1. V **Název_zásady**, zadejte smysluplný název.
2. V **plán zálohování** zadejte pořizování zálohy. Můžete provádět denní nebo týdenní zálohování pro virtuální počítače Azure.
2. V **rychlé obnovení**, určete, jak dlouho chcete uchovat snímky místně pro rychlé obnovení.
    - Zálohy při obnovení, můžete virtuální počítač disky jsou zkopírovány ze služby storage, přes síť do umístění úložiště pro obnovení. S okamžitá obnova můžete využívat místně uložené snímkům pořízeným během úlohy zálohování, aniž byste museli čekat pro zálohování dat přenášených do trezoru.
    - Můžete zachovat snímků pro rychlé obnovení po dobu mezi 1 až 5 dnů. Dva dny se výchozí nastavení.
3. V **rozsah uchování**, určete, jak dlouho chcete uchovávat denní nebo týdenní zálohování bodů.
4. V **uchování měsíčního bodu zálohy**, určete, jestli chcete zachovat měsíční zálohu denní nebo týdenní zálohy. 
5. Klikněte na tlačítko **OK** zásadu uložte.

    ![Nové zásady zálohování](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nepodporuje úpravu automatické hodiny pro letní čas – změny pro zálohy virtuálních počítačů Azure. Čas změny se projeví, upravte zásady zálohování ručně podle potřeby.

## <a name="trigger-the-initial-backup"></a>Spustit prvotní zálohování

Prvotní zálohování se spustí podle plánu, ale může spouštět je okamžitě následujícím způsobem:

1. V nabídce trezoru klikněte na tlačítko **zálohování položek**.
2. V **zálohování položek** klikněte na tlačítko **virtuálního počítače Azure**.
3. V **zálohování položek** klikněte na symbol tří teček (...).
4. Klikněte na tlačítko **zálohovat nyní**.
5. V **zálohovat nyní**, pomocí ovládacího prvku kalendáře vyberte poslední den uchování bodu obnovení. Pak klikněte na **OK**.
6. Sledujte oznámení o portálu. Můžete sledovat průběh úlohy v řídicím panelu trezoru > **úlohy zálohování** > **probíhá**. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

## <a name="optional-steps-install-agentallow-outbound"></a>Volitelné kroky (Instalace agentů/povolit odchozí)
### <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače

Azure Backup zálohuje virtuální počítače Azure po instalaci rozšíření virtuálního počítače Azure agenta spuštěného na počítači. Pokud byl váš virtuální počítač vytvořen z image Azure Marketplace, je agent nainstalován a spuštěn. Pokud vytváříte vlastní virtuální počítač nebo migrovat místní počítač, můžete potřebovat nainstalovat agenta ručně, jak je uvedené v tabulce.

**VM** | **Podrobnosti**
--- | ---
**Windows** | 1. [Stáhněte a nainstalujte](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) soubor MSI agenta.<br/><br/> 2. Instalace pomocí oprávnění správce na počítači.<br/><br/> 3. Ověření instalace. V *C:\WindowsAzure\Packages* na virtuálním počítači, klikněte pravým tlačítkem na **WaAppAgent.exe** > **vlastnosti**. Na **podrobnosti** kartě **verze produktu** by mělo být 2.6.1198.718 nebo vyšší.<br/><br/> Pokud chcete aktualizovat agenta, ujistěte se, že žádná operace zálohování běží, a [znovu nainstalujte agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Nainstalujte s použitím RPM nebo DEB balíček z úložiště balíčků vaší distribuce. Toto je upřednostňovanou metodou pro instalaci a upgrade agenta Azure Linux. Všechny [poskytovatelé distribuce schválené pro](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) balíček agenta Azure Linux integrovat do své Image a úložiště. Agent je k dispozici na [Githubu](https://github.com/Azure/WALinuxAgent), ale doporučujeme nejprve nainstalovat z něj.<br/><br/> Pokud chcete aktualizovat agenta, ujistěte se, že žádná operace zálohování jsou spuštěné a aktualizovat binární soubory.

### <a name="explicitly-allow-outbound-access"></a>Výslovně povolit odchozí přístup

Rozšíření zálohování spuštěná na virtuálním počítači musí odchozí přístup k veřejným IP adresám Azure.

- Obvykle není nutné výslovně povolit odchozí síťový přístup pro virtuální počítač Azure mohla komunikovat s Azure Backup.
- Pokud narazíte na potíže s virtuálními počítači s připojením, nebo pokud se zobrazí chyba **ExtensionSnapshotFailedNoNetwork** při pokusu o připojení, byste měli explicitně povolit přístup, rozšíření zálohování sdělit Azure veřejnou IP adresu adresy pro provoz zálohování. Přístupové metody jsou shrnuty v následující tabulce.


**Možnost** | **Akce** | **Podrobnosti** 
--- | --- | --- 
**Nastavení pravidla skupiny zabezpečení sítě** | Povolit [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Místo povolení a správa každý rozsah adres, můžete přidat pravidlo povolující přístup s použitím služby Azure Backup [značka služby](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Další informace](../virtual-network/security-overview.md#service-tags) informace o značkách služby.<br/><br/> Značky služeb zjednodušit správu přístupu a zbytečně spojené další náklady.
**Nasazení proxy serveru** | Nasazení proxy server HTTP pro směrování provozu. | Poskytuje přístup k celé Azure, a ne jenom úložiště.<br/><br/> Podrobnou kontrolu nad adresy URL úložiště je povolený.<br/><br/> Přístup k jedné bodu Internetu pro virtuální počítače.<br/><br/> Další náklady pro proxy server.
**Nastavení brány Firewall Azure** | Povolit přenosy přes bránu Firewall služby Azure ve virtuálním počítači, pomocí značku plně kvalifikovaný název domény pro službu Azure Backup | Snadno se používá, pokud máte brány Firewall Azure nastavit v podsíti virtuální sítě.<br/><br/> Nejde vytvořit vlastní značky plně kvalifikovaný název domény nebo upravit plně kvalifikovaných názvů domén ve značce.<br/><br/> Pokud virtuální počítače Azure spravované disky, možná budete muset otevřít další porty (8443) na bránu firewall.

#### <a name="establish-network-connectivity"></a>Vytvoření připojení k síti

Navázat spojení s NSG, proxy server, nebo přes bránu firewall

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Nastavit pravidlo skupiny zabezpečení sítě a povolit odchozí přístup k Azure

Pokud skupina zabezpečení sítě spravuje přístup k virtuálnímu počítači, povolí odchozí přístup pro úložiště záloh na požadované rozsahy a porty.

1. Ve vlastnostech virtuálního počítače > **sítě**vyberte **přidat pravidlo portu pro odchozí**.
2. V **přidat odchozí pravidlo zabezpečení**vyberte **Upřesnit**.
3. V **zdroj**vyberte **VirtualNetwork**.
4. V **zdrojové rozsahy portů**, zadejte hvězdičku (*) a povolit přístup z jakéhokoli portu pro odchozí připojení.
5. V **cílové**vyberte **značka služby**. V seznamu vyberte **Storage.region**. Oblast je, kde se nachází v trezoru a virtuálních počítačů, které chcete zálohovat.
6. V **rozsahy cílových portů**, vyberte port.
    - Nespravovaného virtuálního počítače pomocí nešifrovaného účtu úložiště: 80
    - Nespravovaného virtuálního počítače s zašifrovaném účtu úložiště: 443 (výchozí nastavení)
    - Spravovaný virtuální počítač: 8443.
7. V **protokol**vyberte **TCP**.
8. V **Priority**, zadejte hodnotu priority menší než pravidla odmítnutí výše.
   
   Pokud máte pravidlo pro odepření přístupu, nové Povolit pravidlo musí být vyšší. Pokud máte například **Deny_All** s prioritou 1 000, nové pravidlo sady pravidel musí být nastavena na méně než 1000.
9. Zadejte název a popis pro pravidlo a vyberte **OK**.

Pravidlo skupiny zabezpečení sítě můžete použít k více virtuálním počítačům povolit odchozí přístup. Toto video vás provede procesem.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Zálohování směrování provozu přes proxy server

Můžete směrovat provoz zálohování prostřednictvím proxy serveru a poté poskytnout proxy přístup k požadované oblasti Azure. Konfigurace proxy serveru virtuálního počítače umožňuje následující:

- Virtuální počítač Azure by měl směrovat všechen provoz protokolu HTTP mez pro veřejný internet přes proxy server.
- Proxy server by měl povolit příchozí provoz z virtuálních počítačů v příslušné virtuální sítě.
- Skupiny zabezpečení sítě **NSF uzamčení** potřebuje pravidlo, které umožňuje odchozí internetový provoz z virtuálního počítače proxy serveru.

###### <a name="set-up-the-proxy"></a>Nastavení serveru proxy

Pokud nemáte účet proxy systému, nastavte jeden následujícím způsobem:

1. Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Spustit **PsExec.exe -i -s cmd.exe** spustit příkazový řádek pod účtem system.
3. Spusťte prohlížeč v kontextu systému. Například použít **%PROGRAMFILES%\Internet Explorer\iexplore.exe** pro aplikaci Internet Explorer.  
4. Definujte nastavení proxy serveru.
   - Na počítačích s Linuxem:
     - Přidejte tento řádek, který **/etc/prostředí** souboru:
       - **http_proxy=http:\//proxy IP address:proxy port**
     - Přidejte tyto řádky do **/etc/waagent.conf** souboru:
         - **HttpProxy.Host=proxy IP adresa**
         - **HttpProxy.Port=proxy port**
   - U počítačů s Windows, v nastavení prohlížeče určí, že má být použit proxy server. Pokud aktuálně používáte proxy server na uživatelský účet, můžete použít tento skript použijte nastavení na úrovni účtu system.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Povolte příchozí připojení na proxy serveru

Povolte příchozí připojení v nastavení proxy serveru.

1. V bráně Windows Firewall otevřete **brány Windows Firewall s pokročilým zabezpečením**.
2. Klikněte pravým tlačítkem na **příchozí pravidla** > **nové pravidlo**.
3. V **typ pravidla**vyberte **vlastní** > **Další**.
4. V **Program**vyberte **všechny programy** > **Další**.
5. V **protokoly a porty**:
   - Nastavte typ **TCP**.
   - Nastavte **místní porty** k **specifické porty protokolu**.
   - Nastavte **vzdálený port** k **všechny porty**.
  
6. Dokončete průvodce a zadejte název pravidla.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Přidejte pravidlo výjimky pro skupiny zabezpečení sítě pro proxy server

Na skupiny zabezpečení sítě **NSF uzamčení**, povolit provoz z jakéhokoli portu na 10.0.0.5 na jakoukoli adresu v Internetu na portu 80 (HTTP) nebo 443 (HTTPS).

Následující skript prostředí PowerShell poskytuje příklad pro povolení provozu.
Místo povolení odchozích všechny veřejné internetové adresy, můžete určit rozsah IP adres (`-DestinationPortRange`), nebo použijte značku storage.region služby.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Povolit přístup přes bránu firewall se značku plně kvalifikovaný název domény

Brána Firewall služby Azure můžete nastavit pro povolení odchozího přístupu pro síťový provoz do služby Azure Backup.

- [Další informace o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) nasazení Brána Firewall služby Azure.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/firewall/fqdn-tags) značky plně kvalifikovaný název domény.



## <a name="next-steps"></a>Další postup

- Vyřešte všechny problémy s [Azure VM agents](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) nebo [zálohování virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).
- [Obnovení](backup-azure-arm-restore-vms.md) virtuální počítače Azure.

