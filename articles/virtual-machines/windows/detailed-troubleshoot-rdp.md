---
title: Podrobné vzdálené plochy, řešení potíží s v Azure | Microsoft Docs
description: Přečtěte si podrobné řešení problémů s kroky pro vzdálené plochy chyby, kde nemůžete virtuálních počítačích s Windows v Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Nelze se připojit k vzdálené ploše, řešení potíží s vzdálené plochy, nemůže připojit ke vzdálené ploše, vzdálené plochy chyby, řešení potíží vzdálené plochy, potížích se vzdálenou plochou
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2017
ms.author: genli
ms.openlocfilehash: 1485bc5ac7ae47df9a1a36c8b88d6515b5624360
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Podrobné kroky pro řešení potíží připojení ke vzdálené ploše na Windows virtuálních počítačů v Azure
Tento článek obsahuje podrobný postup řešení potíží diagnostikovat a opravit chyby komplexní vzdálené plochy pro systém Windows Azure virtuální počítače.

> [!IMPORTANT]
> K odstranění více běžných chyb vzdálené plochy, nezapomeňte si přečíst [základní článku Poradce při potížích pro vzdálenou plochu](troubleshoot-rdp-connection.md) než budete pokračovat.

Může dojít ke vzdálené ploše chybovou zprávu, která není vypadat žádné specifické chybové zprávy popsané v [základní Průvodci odstraňováním potíží vzdálené plochy](troubleshoot-rdp-connection.md). Postupujte podle těchto kroků, chcete-li zjistit, proč klient vzdálené plochy (RDP) se nemůže připojit ke službě protokolu RDP na virtuální počítač Azure.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [MSDN Azure a fóra Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a klikněte na tlačítko **získat podporu**. Informace o používání podporu Azure, najdete v tématu [Microsoft Azure Support – nejčastější dotazy](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Součásti připojení vzdálené plochy
Následující součásti jsou součástí připojení ke vzdálené ploše:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Než budete pokračovat, může pomoci duševně zkontrolovat, co se změnil od posledního úspěšného připojení vzdálené plochy k virtuálnímu počítači. Příklad:

* Veřejnou IP adresu virtuálního počítače nebo cloudové služby, která obsahuje virtuální počítač (také nazývané virtuální IP adresy [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) se změnila. Selhání RDP může být způsobeno vaší mezipaměti klienta DNS má stále *starou IP adresu* zaregistrovat pro název DNS. Vyprázdnění mezipaměti klienta DNS a zkuste se znovu připojit virtuální počítač. Nebo se připojte přímo s novou virtuální IP adresu.
* Aplikace třetích stran používají ke správě vašeho připojení ke vzdálené ploše místo použití připojení vygenerované portálu Azure. Ověřte, že konfigurace aplikace obsahuje správný port TCP pro přenosy vzdálené plochy. Tento port pro klasické virtuální počítač v můžete zkontrolovat [portál Azure](https://portal.azure.com), kliknutím na nastavení Virtuálního počítače > Koncové body.

## <a name="preliminary-steps"></a>Předběžné kroky
Před pokračováním podrobné řešení potíží

* Zkontrolujte stav virtuálního počítače na portálu Azure pro všechny zřejmé problémy.
* Postupujte podle [oprava rychlé kroky pro běžné chyby RDP v Průvodci základní řešení problémů s](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Pro vlastní Image Ujistěte se, když vaše virtuální pevný disk je správně připravená před nahrajte ho. Další informace najdete v tématu [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](prepare-for-upload-vhd-image.md).


Zkuste se znovu připojit k virtuálnímu počítači prostřednictvím vzdálené plochy po provedení těchto kroků.

## <a name="detailed-troubleshooting-steps"></a>Podrobný poradce při potížích
Klienta vzdálené plochy není možné kontaktovat službu Vzdálená plocha na virtuální počítač Azure kvůli problémům v následujících zdrojích:

* [Klient služby Vzdálená plocha počítače](#source-1-remote-desktop-client-computer)
* [Hraniční zařízení intranetu organizace](#source-2-organization-intranet-edge-device)
* [Koncový bod služby v cloudu a přístup k seznamu řízení (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Systém Windows Azure virtuálních počítačů](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Zdroj 1: Klient služby Vzdálená plocha počítače
Ověřte, že váš počítač může vytvořit připojení vzdálené plochy jiné místně, počítače se systémem Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Pokud není, zkontrolujte následující nastavení v počítači:

* Nastavení místní brány firewall, které blokuje provoz vzdálené plochy.
* Místně nainstalován klientský software proxy serveru, který brání připojení ke vzdálené ploše.
* Místně nainstalován software, který brání připojení ke vzdálené ploše monitorování sítě.
* Jiné typy zabezpečení softwaru, které monitorování provozu nebo povolí nebo zakáže určité typy přenosů, který brání připojení ke vzdálené ploše.

V těchto případech dočasně zakázat softwaru a pokuste se připojit k místním počítači přes vzdálenou plochu. Pokud můžete zjistit skutečný příčina tímto způsobem, pracujete se správcem vaší sítě a opravte nastavení softwaru, které povolí připojení ke vzdálené ploše.

## <a name="source-2-organization-intranet-edge-device"></a>Zdroj 2: Organizace intranetu hraniční zařízení
Ověřte, že počítače přímo připojené k Internetu může vytvořit připojení vzdálené plochy k virtuálnímu počítači Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvoření a testování pomocí Azure nového virtuálního počítače v prostředku skupiny nebo cloudovou službu. Další informace najdete v tématu [vytvoření virtuálního počítače se systémem Windows v Azure](../virtual-machines-windows-hero-tutorial.md). Virtuální počítač a skupině prostředků nebo cloudovou službu, můžete odstranit po testu.

Pokud vytvoříte připojení vzdálené plochy s počítačem přímo připojené k Internetu, zkontrolujte vaši organizaci intranetu hraniční zařízení pro:

* K interní firewall blokující připojení prostřednictvím protokolu HTTPS k Internetu.
* Proxy server, který brání připojení ke vzdálené ploše.
* Software spuštěný na zařízení ve vaší hraniční síti, která brání připojení ke vzdálené ploše monitorování sítě nebo zjišťování neoprávněných vniknutí.

Práce se správcem vaší sítě a opravte nastavení vaší organizace intranetu hraniční zařízení umožňuje založený na protokolu HTTPS vzdálené plochy připojení k Internetu.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: Koncový bod služby Cloud a seznamu ACL
Pro virtuální počítače vytvořené pomocí modelu nasazení Classic ověřte, že jiného virtuálního počítače Azure, který je ve stejné cloudové služby nebo virtuální sítě můžete provádět připojení ke vzdálené ploše virtuálního počítače Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Pro virtuální počítače vytvořené ve službě Správce prostředků, přejděte k [zdroj 4: skupin zabezpečení sítě](#source-4-network-security-groups).

Pokud nemáte jiného virtuálního počítače ve stejné cloudové služby nebo virtuální sítě, vytvořte. Postupujte podle kroků v [vytvoření virtuálního počítače se systémem Windows v Azure](../virtual-machines-windows-hero-tutorial.md). Odstraňte testovací virtuální počítač po dokončení testu.

Pokud se můžete připojit přes vzdálenou plochu do virtuálního počítače ve stejné cloudové služby nebo virtuální sítě, zkontrolujte tato nastavení:

* Konfigurace koncového bodu pro přenosy vzdálené plochy na cílovém virtuálním počítači: privátní port TCP koncového bodu musí odpovídat portu TCP, na kterém naslouchá služby Vzdálená plocha Virtuálního počítače (výchozí hodnota je 3389).
* Seznam ACL pro koncový bod provoz vzdálené plochy na cílovém virtuálním počítači: seznamy řízení přístupu umožňuje určit povolené nebo zakázané příchozí přenosy z Internetu na základě jeho zdrojové IP adresy. Nesprávně nakonfigurované seznamy ACL můžete zabránit příchozí provoz vzdálené plochy ke koncovému bodu. Zkontrolujte vaše seznamy ACL zajistit, aby příchozí provoz z veřejné IP adresy vašeho proxy serveru nebo jiné hraniční server je povolen. Další informace najdete v tématu [co je seznamu pro řízení přístupu sítě (ACL)?](../../virtual-network/virtual-networks-acl.md)

Pokud chcete zkontrolovat, pokud je zdroj problému, odeberte aktuální koncový bod a vytvořte novou, výběr náhodný port v rozsahu 49152 až 65535 pro externí port číslo. Další informace najdete v tématu [jak nastavit koncové body k virtuálnímu počítači](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Zdroje 4: Skupiny zabezpečení sítě
Skupiny zabezpečení sítě povolit podrobnější řízení povolené příchozí a odchozí přenosy. Můžete vytvořit pravidla pokrývání uzlů podsítě a cloudových služeb v virtuální sítě Azure.

Použijte [Ověření toku protokolu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) a ověřte, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat pravidel skupiny zabezpečení efektivní zajistit příchozí "Povolit" NSG pravidlo existuje a prioritu pro RDP port (standardně 3389). Další informace najdete v tématu [tok provozu pomocí efektivní pravidla zabezpečení pro řešení potíží s virtuálních počítačů](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

## <a name="source-5-windows-based-azure-vm"></a>Zdroj 5: Založené na systému Windows virtuálního počítače Azure
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postupujte podle pokynů v [v tomto článku](reset-rdp.md). Tento článek resetuje služby Vzdálená plocha na virtuálním počítači:

* Povolte pravidlo brány Firewall systému Windows "Vzdálené plochy" výchozí (TCP port 3389).
* Povolte připojení ke vzdálené ploše tak HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections hodnotu registru na 0.

Zkuste připojení znovu z vašeho počítače. Pokud jste ještě nebylo možné se připojit přes vzdálenou plochu, zkontrolujte následující možné potíže:

* Služba Vzdálená plocha není spuštěna na cílovém virtuálním počítači.
* Služba Vzdálená plocha není naslouchá na portu TCP 3389.
* Brána Windows Firewall nebo jiné místní brány firewall má odchozí pravidlo, které brání provoz vzdálené plochy.
* Zjišťování neoprávněných vniknutí nebo monitorování software spuštěný na virtuálním počítači Azure sítě brání připojení ke vzdálené ploše.

Pro virtuální počítače vytvořené pomocí modelu nasazení classic můžete použít vzdálené relace prostředí Azure PowerShell na virtuálním počítači Azure. Nejdřív musíte nainstalovat certifikát pro hostování Cloudová služba virtuálního počítače. Přejděte na [konfigurace zabezpečeného vzdáleného prostředí PowerShell přístupu na virtuálních počítačích Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) a stáhnout **InstallWinRMCertAzureVM.ps1** soubor skriptu do místního počítače.

Pokud jste to ještě neudělali, potom nainstalujte Azure PowerShell. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

Dále otevřete příkazový řádek prostředí Azure PowerShell a změňte aktuální složku na umístění **InstallWinRMCertAzureVM.ps1** souboru skriptu. Pro spuštění skriptu prostředí Azure PowerShell, musíte nastavit zásadu spouštění správné. Spustit **Get-ExecutionPolicy** příkaz, abyste zjistili vaše aktuální úroveň zásad. Informace o nastavení odpovídající úroveň najdete v tématu [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

V dalším kroku zadat název vašeho předplatného Azure, název cloudové služby a název vašeho virtuálního počítače (odebrání < a > znaky), a pak spusťte tyto příkazy.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Můžete získat název správné předplatné z *Název_předplatného* vlastnost zobrazení **Get-AzureSubscription** příkaz. Můžete získat název cloudové služby pro virtuální počítač z *ServiceName* sloupce v zobrazení **Get-AzureVM** příkaz.

Zkontrolujte, pokud máte nový certifikát. Otevřete modul snap-in Certifikáty pro aktuálního uživatele a hledat v **Trusted Root Certification Authorities\Certificates** složky. Měli byste vidět certifikát s názvem DNS ve sloupci vystaveno pro cloudové služby (Příklad: cloudservice4testing.cloudapp.net).

V dalším kroku spuštění vzdálené relace prostředí Azure PowerShell pomocí těchto příkazů.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po zadání přihlašovacích údajů správce platný, měli byste vidět něco podobného do příkazového řádku Azure PowerShell následující:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

První část tuto výzvu je název vaší cloudové služby, který obsahuje cílový virtuální počítač, který může lišit od "cloudservice4testing.cloudapp.net". Teď můžete použít Azure PowerShell uvedených příkazů pro tuto cloudovou službu k prozkoumání problémů a opravte konfiguraci.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Chcete-li ručně opravit služby Vzdálená plocha naslouchání portu TCP
Tento příkaz spusťte na řádku vzdálené relace prostředí Azure PowerShell.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Vlastnost číslo_portu zobrazuje aktuální číslo portu. V případě potřeby změnit vzdálené plochy port číslo zpět na výchozí hodnotu (3389) pomocí tohoto příkazu.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Ověřte, že port se změnil na 3389 pomocí tohoto příkazu.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Ukončení vzdálené relace prostředí Azure PowerShell pomocí tohoto příkazu.

```powershell
Exit-PSSession
```

Ověřte, že koncový bod vzdálené plochy pro virtuální počítač Azure také používá port TCP. 3398 jako svůj vnitřní port. Restartujte virtuální počítač Azure a znovu se připojit ke vzdálené ploše.

## <a name="additional-resources"></a>Další zdroje informací:
[Postup resetování hesla nebo služby Vzdálená plocha pro virtuální počítače s Windows](reset-rdp.md)

[Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

[Řešení potíží s Secure Shell (SSH) připojení k virtuálnímu počítači systémem Linux Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

