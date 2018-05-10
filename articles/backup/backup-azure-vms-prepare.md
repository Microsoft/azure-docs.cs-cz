---
title: Příprava prostředí pro zálohování virtuálních počítačů Azure | Microsoft Docs
description: Ujistěte se, že vaše prostředí je připravený pro zálohování virtuálních počítačů v Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonn
editor: ''
keywords: zálohování; zálohování;
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2017
ms.author: cwatson
ms.openlocfilehash: 03bb4c3e68643ded686c20b7ba0d5d079a9d4057
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Příprava prostředí pro zálohování virtuálních počítačů Azure
> [!div class="op_single_selector"]
> * [Model Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Klasického modelu](backup-azure-vms-prepare.md)
>
>

Předtím, než můžete zálohovat virtuální počítač Azure (VM), existují tři podmínky, které musí existovat.

* Budete muset vytvořit úložiště záloh nebo identifikovat existující trezor záloh *ve stejné oblasti jako virtuální počítač*.
* Vytvoření připojení k síti mezi Azure veřejné internetové adresy a koncové body Azure úložiště.
* Do virtuálního počítače nainstalujte agenta virtuálního počítače.

Pokud znáte tyto podmínky již neexistuje ve vašem prostředí poté pokračujte [zálohování váš článek virtuálních počítačů](backup-azure-vms.md). Jinak přečtěte si, tento článek vás provede kroky při přípravě vašeho prostředí a zálohovat virtuální počítač Azure.

##<a name="supported-operating-system-for-backup"></a>Podporovaný operační systém pro zálohování
 * **Linux**: Azure Backup podporuje [seznam distribucí schválených pro Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), kromě základního OS Linux. _Další přineste-vaše – vlastní-Linuxových distribucích také může fungovat, dokud agent virtuálního počítače je k dispozici na virtuálním počítači a podpora pro Python existuje. Jsme však není neschvaluje těchto distribuce pro zálohování._
 * **Windows Server**: Verze starší než Windows Server 2008 R2 nejsou podporovány.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Omezení při zálohování a obnovení virtuálních počítačů
> [!NOTE]
> Azure má dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Následující seznam obsahuje omezení při nasazení v klasickém modelu.
>
>

* Zálohování virtuálních počítačů s více než 16 datových disků není podporována.
* Zálohování virtuálních počítačů s vyhrazenou IP adresu a žádný definovaný koncový bod není podporována.
* Zálohovaná data neobsahuje sítě připojené jednotky připojené k virtuálnímu počítači.
* Nahrazení existujícího virtuálního počítače během obnovení se nepodporuje. Nejprve odstranit existující virtuální počítač a všechny přidružené disky a potom obnovte data ze zálohy.
* Mezi oblastmi zálohování a obnovení není podporována.
* Zálohování virtuálních počítačů pomocí služby zálohování Azure se podporuje ve všech oblastech veřejný Azure (viz [kontrolní seznam](https://azure.microsoft.com/regions/#services) z podporovaných oblastí). Pokud oblast, kterou hledáte, není podporován dnes, nezobrazí se v rozevíracím seznamu při vytváření trezoru.
* Zálohování virtuálních počítačů pomocí služby Azure Backup je podporována pouze pro verze vyberte operační systém:
* Obnovení řadiče domény (DC) virtuálního počítače, který je součástí konfigurace více – řadič domény je možné pouze pomocí prostředí PowerShell. Další informace o [obnovení řadiče domény, řadiče domény služby více](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Obnovení virtuálních počítačů, které mají následující zvláštní síťové konfigurace je podporována pouze pomocí prostředí PowerShell. Po dokončení operace obnovení, nebude mít virtuální počítače, které vytvoříte pomocí pracovního postupu obnovení v uživatelském rozhraní tyto konfigurace sítě. Další informace najdete v tématu [obnovení virtuálních počítačů s konfigurací speciální síťových](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Virtuální počítače v části Konfigurace služby Vyrovnávání zatížení (interních a externích)
  * Virtuální počítače s více vyhrazené IP adresy
  * Virtuální počítače s více síťových adaptérů

## <a name="create-a-backup-vault-for-a-vm"></a>Vytvořte trezor záloh pro virtuální počítač
Trezor záloh je entita, která ukládá všechny vytvořené zálohy a body obnovení. Trezor záloh obsahuje také zásady zálohování, které se použijí na zálohované virtuální počítače.

> [!IMPORTANT]
> Od března 2017 již nelze k vytvoření trezorů služby Backup použít portál Classic. Existující trezory služby Backup jsou stále podporovány a je možné [k vytvoření trezorů služby Backup použít Azure PowerShell](./backup-client-automation-classic.md#create-a-backup-vault). Společnost Microsoft ale doporučuje pro všechna nasazení vytvořit trezory služby Recovery Services, protože všechna budoucí vylepšení se budou vztahovat výhradně na trezory služby Recovery Services.


Tento obrázek znázorňuje vztahy mezi různými entitami Azure Backup: ![Azure Backup entity a vztahy](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>Připojení k síti
Abyste mohli spravovat snímky virtuálních počítačů, potřebuje rozšíření zálohování připojení k Azure veřejné IP adresy. Bez správné připojení k Internetu vypršení časového limitu pro požadavky HTTP virtuálního počítače a operaci zálohování se nezdaří. Pokud vaše nasazení má omezení přístupu na místě (prostřednictvím skupinu zabezpečení sítě (NSG), např.), zvolte jednu z těchto možností pro poskytování zrušte cestu pro provoz zálohování:

* [Seznam povolených adres Azure datacenter IP rozsahy](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -najdete v článku pokyny na tom, jak povolených IP adres.
* Nasazení proxy server HTTP pro směrování provozu.

Při rozhodování, kterou možnost použít, jsou kompromisy mezi spravovatelnosti, přesná kontrola a náklady.

| Možnost | Výhody | Nevýhody |
| --- | --- | --- |
| Rozsahy povolených IP adres |Žádné dodatečné poplatky.<br><br>Pro přístup k otevření v skupinu NSG, použijte <i>Set-AzureNetworkSecurityRule</i> rutiny. |Komplexní správa jako ovlivněné časem změnit rozsahy IP adres.<br><br>Poskytuje přístup k celé Azure a ne jenom úložiště. |
| Server proxy protokolu HTTP |Přesná kontrola v proxy přes úložiště povolené adresy URL. Instalační program granulární ovládací prvek v proxy https://\*.blob.core.windows.net/\* vzor adresy URL musí být seznam povolených adres. Do seznamu povolených IP adres pouze účet úložiště používané virtuálním počítačem https://\<storageAccount\>.blob.core.windows.net/\* vzor adresy URL musí být seznam povolených adres. <br>Jeden bod Internet přístup k virtuálním počítačům.<br>Není v souladu změny Azure IP adresy. |Další náklady pro spuštění virtuálního počítače s proxy software. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Seznam povolených adres Azure datacenter rozsahy IP adres
Seznam povolených adres rozsahy IP datové centrum Azure, najdete [webu Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) podrobnosti o rozsahy IP adres a pokyny.

### <a name="using-an-http-proxy-for-vm-backups"></a>Pomocí proxy serveru HTTP pro zálohování virtuálních počítačů
Při zálohování virtuálních počítačů, rozšíření zálohování na virtuální počítač odešle snímek příkazy pro správu do služby Azure Storage pomocí rozhraní API HTTPS. Směrujte provoz rozšíření zálohování přes server proxy protokolu HTTP vzhledem k tomu, že je jedinou komponentou nakonfigurovaný pro přístup do veřejného Internetu.

> [!NOTE]
> Neexistuje žádná doporučení pro proxy software, který se má použít. Ujistěte se, že vyberete proxy server, který má odchozí věrnosti a který je kompatibilní s níže uvedené kroky konfigurace. Ujistěte se, že softwary třetích stran neupravujte nastavení proxy serveru
>
>

Následující příklad obrázek ukazuje tři konfiguračních kroků nezbytných k používání proxy serveru HTTP:

* Virtuální počítač aplikace směrovat veškerý provoz protokolu HTTP vázaný veřejného Internetu prostřednictvím proxy serveru virtuálního počítače.
* Proxy virtuálních počítačů umožňuje příchozí provoz z virtuálních počítačů ve virtuální síti.
* Skupina zabezpečení sítě (NSG), s názvem NFP uzamčení musí zabezpečení pravidlo povolení odchozí internetové přenosy z virtuálního počítače proxy serveru.

![Skupina NSG s diagram nasazení proxy serveru HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Proxy server HTTP komunikaci do veřejného Internetu, postupujte podle těchto kroků:

#### <a name="step-1-configure-outgoing-network-connections"></a>Krok 1. Konfigurace odchozích síťových připojení
###### <a name="for-windows-machines"></a>Pro počítače s Windows
To se nastavit konfiguraci proxy serveru pro místní systémový účet.

1. Stáhněte si [nástroje PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Z řádku se zvýšenými oprávněními spusťte následující příkaz

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Otevře se okno internet Exploreru.
3. Přejděte na Nástroje -> Možnosti Internetu -> připojení -> Nastavení místní sítě.
4. Ověřte nastavení proxy serveru pro účet System. Nastavení proxy serveru IP adresy a portu.
5. Zavřete Internet Explorer.

To se nastavit konfiguraci proxy celého systému a se použije pro všechny odchozí přenosy HTTP/HTTPS.

Pokud jste nastavili proxy server na aktuální uživatelský účet (ne místní účet systému), použijte následující skript je aplikovat na SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Pokud zjistíte v protokolu serveru proxy "(407) vyžadováno ověření proxy serveru", zkontrolujte, že ověřování je správně nastavená.
>
>

###### <a name="for-linux-machines"></a>Pro počítače se systémem Linux
Přidejte následující řádek na ```/etc/environment``` souboru:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Přidejte následující řádky, které se ```/etc/waagent.conf``` souboru:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Krok 2. Povolit příchozí připojení na proxy serveru:
1. Na serveru proxy otevřete bránu Windows Firewall. Nejjednodušší způsob, jak získat přístup k bráně firewall je pro vyhledávání pro bránu Windows Firewall s pokročilým zabezpečením.

    ![Otevření brány Firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. V dialogovém okně brány Windows Firewall, klikněte pravým tlačítkem na **příchozí pravidla** a klikněte na tlačítko **nové pravidlo...** .

    ![Vytvořit nové pravidlo](./media/backup-azure-vms-prepare/firewall-02.png)
3. V **pravidla Průvodce vytvořením nového příchozího**, vyberte **vlastní** možnost **typ pravidla** a klikněte na tlačítko **Další**.
4. Na stránce vybrat **programu**, zvolte **všechny programy** a klikněte na tlačítko **Další**.
5. Na **protokol a porty** stránky, zadejte následující informace a klikněte na **Další**:

    ![Vytvořit nové pravidlo](./media/backup-azure-vms-prepare/firewall-03.png)

   * pro *protokolu typ* zvolte *TCP*
   * pro *místního portu* zvolte *specifické porty*, do následujícího pole zadejte ```<Proxy Port>``` který byl nakonfigurovaný.
   * pro *vzdálených portů* vyberte *všechny porty*

     Pro zbytek průvodce klikněte na úplně na konec a pojmenujte toto pravidlo.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Krok 3. Přidejte pravidlo výjimky k této skupině:
V příkazovém řádku prostředí Azure PowerShell zadejte následující příkaz:

Následující příkaz přidá výjimku NSG. Tato výjimka umožňuje TCP provoz z jakéhokoli portu na 10.0.0.5 všechny internetové adresy na portu 80 (HTTP) nebo 443 (HTTPS). Pokud budete potřebovat specifického portu v veřejného Internetu, je nutné přidat tento port k ```-DestinationPortRange``` také.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Ujistěte se, že nahradíte názvy v příkladu s informacemi, které jsou vhodné pro vaše nasazení.*

## <a name="vm-agent"></a>Agent virtuálního počítače
Předtím, než můžete zálohovat virtuální počítač Azure, se ujistěte, že agenta virtuálního počítače Azure je správně nainstalovaný na virtuálním počítači. Vzhledem k tomu, že agent virtuálního počítače je volitelná součást v čase, který se vytvoří virtuální počítač, ujistěte se, že je zaškrtnuté políčko pro agenta virtuálního počítače, před zřízení virtuálního počítače.

### <a name="manual-installation-and-update"></a>Ruční instalace a aktualizace
Agent virtuálního počítače již existuje ve virtuálních počítačích, které jsou vytvořené z Galerie Azure. Virtuální počítače, které jsou migrované z místních datových center však nebude mít agent virtuálního počítače nainstalovaný. Pro tyto virtuální počítače musí být explicitně nainstalovaný agent virtuálního počítače. 

| **Operace** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalace agenta virtuálního počítače |Stáhněte si a nainstalujte [MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace budete potřebovat oprávnění správce.<li>[Aktualizujte vlastnost virtuálního počítače](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), aby indikovala, že je agent nainstalovaný. |<li> Nainstalujte si nejnovější verzi [agenta systému Linux](../virtual-machines/extensions/agent-linux.md). K dokončení instalace budete potřebovat oprávnění správce. Doporučujeme nainstalovat agenta z distribuční úložiště. Jsme **nedoporučujeme** instalaci agenta virtuálního počítače s Linuxem přímo z githubu.  |
| Aktualizace agenta virtuálního počítače |Aktualizace agenta virtuálního počítače je stejně jednoduchá, jako přeinstalace [binárních souborů agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |Postupujte podle pokynů v tématu [Aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Doporučujeme aktualizovat agenta z distribuční úložiště. Jsme **nedoporučujeme** aktualizace agenta virtuálního počítače s Linuxem přímo z githubu.<br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |
| Ověření instalace agenta virtuálního počítače |<li>Ve virtuálním počítači Azure přejděte do složky *C:\WindowsAzure\Packages*. <li>Měl by být přítomný soubor WaAppAgent.exe.<li> Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. Pole Verze produktu by mělo být 2.6.1198.718 nebo vyšší. |neuvedeno |

Další informace o [agenta virtuálního počítače](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) a [k jeho instalaci](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Rozšíření zálohování
Pokud chcete zálohovat virtuální počítač, služba Azure Backup nainstaluje rozšíření do agenta virtuálního počítače. Služba Azure Backup bezproblémově upgraduje a opravuje rozšíření zálohování bez dalšího zásahu uživatele.

Rozšíření zálohování je nainstalován, pokud je virtuální počítač spuštěný. Spuštěný virtuální počítač poskytuje největší šanci získání bod obnovení konzistentních s aplikací. Ale Azure Backup service bude nadále zálohovat virtuální počítač – i když je vypnutý a rozšíření nebylo možné nainstalovat (neboli Offline virtuálního počítače). V takovém případě bude bod obnovení *zhroutí konzistentní* jak je popsáno výše.

## <a name="questions"></a>Máte dotazy?
Máte-li nějaké dotazy nebo pokud víte o funkci, kterou byste uvítali, [odešlete nám svůj názor](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Další postup
Teď, když připravíte prostředí pro zálohování virtuálního počítače, vaše další logické krokem je vytvoření zálohy. Plánování článek poskytuje podrobnější informace o zálohování virtuálních počítačů.

* [Zálohování virtuálních počítačů](backup-azure-vms.md)
* [Plánování vaší infrastruktury zálohování virtuálních počítačů](backup-azure-vms-introduction.md)
* [Správa záloh virtuálních počítačů](backup-azure-manage-vms.md)
