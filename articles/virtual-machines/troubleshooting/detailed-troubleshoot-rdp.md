---
title: Podrobné vzdálené plochy, řešení potíží v Azure | Dokumentace Microsoftu
description: Projděte si podrobný postup řešení potíží pro vzdálené plochy chyby, kde nelze do virtuálních počítačů Windows v Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nejde se připojit k vzdálené ploše, řešit problémy se vzdálenou plochu, nemůže připojit ke vzdálené ploše, vzdálené plochy chyby, řešení potíží vzdálené plochy, potížích se vzdálenou plochou
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: af36f033dbca6c9f594b3568bfe7567a959e2d2f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237148"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Podrobný Poradce při potížích kroky pro připojení ke vzdálené ploše pro virtuální počítače s Windows v Azure
Tento článek obsahuje podrobný postup řešení potíží, Diagnostika a řešení složitých chybami ke vzdálené ploše pro založené na Windows Azure virtual machines.

> [!IMPORTANT]
> Chcete-li odstranit více běžných chyb vzdálené plochy, nezapomeňte si přečíst [základní článku Poradce při potížích pro vzdálenou plochu](troubleshoot-rdp-connection.md) než budete pokračovat.

Může dojít ke vzdálené ploše chybovou zprávu, která není vypadat specifické chybové zprávy uvedené v [základní Průvodce odstraňováním potíží vzdálené plochy](troubleshoot-rdp-connection.md). Postupujte podle následujících kroků zjistěte, proč se nemůže připojit ke službě RDP na virtuálních počítačích Azure klienta Remote Desktop (RDP).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na tlačítko **získat podporu**. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Součásti připojení ke vzdálené ploše
V připojení ke vzdálené ploše se podílejí následující komponenty:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Než budete pokračovat, může pomoct duševně zkontrolujte, co se změnil od posledního úspěšného připojení vzdálené plochy k virtuálnímu počítači. Příklad:

* Veřejnou IP adresu virtuálního počítače nebo cloudové služby, který obsahuje virtuální počítač (neboli virtuální IP adresu [virtuálních IP adres](https://en.wikipedia.org/wiki/Virtual_IP_address)) se změnil. Protokol RDP selhání může být, že klientské mezipaměti DNS má stále *starou IP adresu* zaregistrovaný pro název DNS. Vyprázdnění mezipaměti klienta DNS a zkuste se znovu připojit virtuální počítač. Nebo se připojte přímo s novou virtuální IP adresy.
* Správa připojení ke vzdálené ploše namísto použití připojení vygenerovaný na webu Azure portal pomocí aplikací třetích stran. Ověřte, že konfigurace aplikace obsahuje správný port TCP pro provoz vzdálené plochy. Tento port pro klasický virtuální počítač v můžete zkontrolovat [webu Azure portal](https://portal.azure.com), kliknutím na nastavení Virtuálního počítače > Koncové body.

## <a name="preliminary-steps"></a>Přípravné kroky
Před pokračováním podrobné řešení potíží

* Zkontrolujte stav virtuálního počítače na webu Azure Portal pro všechny zřejmé problémy.
* Postupujte podle [Rychlá oprava kroky pro běžné chyby protokol RDP v základní Průvodce odstraňováním potíží](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Pro vlastní Image Ujistěte se, že virtuální pevný disk je správně připravena před odesláním ho. Další informace najdete v tématu [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](../windows/prepare-for-upload-vhd-image.md).


Zkuste se znovu připojit k virtuálnímu počítači přes vzdálenou plochu po provedení těchto kroků.

## <a name="detailed-troubleshooting-steps"></a>Podrobný poradce při potížích
Klient vzdálené plochy nemusí být schopný připojit službu Vzdálená plocha na virtuálním počítači Azure z důvodu problémů v následujících zdrojích:

* [Klient služby Vzdálená plocha počítače](#source-1-remote-desktop-client-computer)
* [Organizace intranetu hraničního zařízení](#source-2-organization-intranet-edge-device)
* [Koncový bod služby v cloudu a přístup k seznamu ovládacího prvku (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Na základě Windows virtuální počítač Azure](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>1. zdroj: Klient služby Vzdálená plocha počítače
Ověřte, že počítač může vytvořit připojení vzdálené plochy do jiného v místním počítači se systémem Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Pokud není, zkontrolujte následující nastavení v počítači:

* Nastavení místní brány firewall, které blokuje provoz vzdálené plochy.
* Místně nainstalovaný klientský software proxy server, který brání připojení ke vzdálené ploše.
* Místně nainstalovaný software, který brání připojení ke vzdálené ploše pro monitorování sítě.
* Jiné druhy zabezpečovací programy, které monitorování provozu nebo povolit/zakázat konkrétní typy provozu, který brání připojení ke vzdálené ploše.

V těchto případech se dočasně zakázat software a pokusí připojit k místním počítači přes vzdálenou plochu. Pokud můžete zjistit příčinu skutečného tímto způsobem, pracujete se správcem vaší sítě, chcete-li nastavení softwaru, které povolí připojení ke vzdálené ploše.

## <a name="source-2-organization-intranet-edge-device"></a>Zdroj 2: Organizace intranetu hraničního zařízení
Ověřte, že počítače přímo připojené k Internetu může vytvořit připojení ke vzdálené ploše na virtuálním počítači Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvořte a testování pomocí Azure nový virtuální počítač v prostředku skupiny nebo cloudovou službu. Další informace najdete v tématu [vytvořit virtuální počítač s Windows v Azure](../virtual-machines-windows-hero-tutorial.md). Virtuální počítač a skupinu prostředků nebo cloudovou službu, můžete odstranit po testu.

Pokud vytvoříte připojení ke vzdálené ploše s počítačem přímo připojené k Internetu, zkontrolujte zařízení edge intranetu organizace pro:

* Interní firewall blokující připojení HTTPS k Internetu.
* Proxy serveru brání připojení ke vzdálené ploše.
* Zjišťování neoprávněných vniknutí nebo softwaru, která běží na zařízeních ve vaší hraniční síti brání připojení ke vzdálené ploše pro monitorování sítě.

Práce se správcem vaší sítě, chcete-li nastavení intranetu hraniční zařízení naprogramujete organizace umožňující připojení ke vzdálené ploše založený na protokolu HTTPS do Internetu.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: Koncový bod služby v cloudu a seznam ACL
Virtuální počítače vytvořené pomocí modelu nasazení Classic ověřte, že jiného virtuálního počítače Azure, který je ve stejné cloudové službě nebo virtuální síti můžete provádět připojení ke vzdálené ploše virtuálního počítače Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Pro virtuální počítače vytvořené v Resource Manageru, přejděte k [zdroj 4: skupiny zabezpečení sítě](#source-4-network-security-groups).

Pokud nemáte jiného virtuálního počítače ve stejné cloudové službě nebo virtuální sítě, vytvořte si ho. Postupujte podle kroků v [vytvořit virtuální počítač s Windows v Azure](../virtual-machines-windows-hero-tutorial.md). Odstraňte testovací virtuální počítač po dokončení testu.

Pokud se k virtuálnímu počítači ve stejné cloudové službě nebo virtuální síti se můžete připojit přes vzdálenou plochu, zkontrolujte tato nastavení:

* Konfigurace koncového bodu pro provoz vzdálené plochy na cílovém virtuálním počítači: privátní port TCP koncového bodu musí odpovídat portu TCP, na kterém naslouchá služby Vzdálená plocha Virtuálního počítače (výchozí hodnota je 3389).
* Seznam ACL pro koncový bod provozu vzdálené plochy na cílovém virtuálním počítači: seznamy řízení přístupu vám umožňují určit povolené nebo zakázané příchozí přenosy z Internetu na základě zdrojové IP adresy. Nesprávně nakonfigurované seznamy ACL můžete zabránit příchozí provoz vzdálené plochy ke koncovému bodu. Zkontrolujte vaše seznamy ACL zajistit, že příchozí provoz z veřejné IP adresy vašeho proxy serveru nebo jiný server edge není povolen. Další informace najdete v tématu [co je seznamu pro řízení přístupu sítě (ACL)?](../../virtual-network/virtual-networks-acl.md)

Zkontrolujte, jestli je koncový bod příčiny problému, odstraňte aktuální koncový bod a vytvořte novou, zvolte náhodný port v rozsahu 49152 až 65535 pro externí port číslo. Další informace najdete v tématu [postup nastavení koncových bodů pro virtuální počítač](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Zdroj 4: Skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují přesněji řídit povoleného příchozího a odchozího provozu. Můžete vytvořit pravidla pokrývání uzlů podsítě a cloudové služby ve službě Azure virtual network.

Použijte [Ověření toku protokolu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) a ověřte, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat platná pravidla skupin zabezpečení a ověřte, že příchozí "skupina zabezpečení sítě existuje pravidlo a je nastaveno jako prioritní pro port RDP (ve výchozím nastavení 3389). Další informace najdete v tématu [přenosy dat pomocí efektivní pravidla zabezpečení pro řešení potíží s virtuálním počítači](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Zdroj: 5: Založené na Windows virtuální počítač Azure
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postupujte podle pokynů v [v tomto článku](../windows/reset-rdp.md). Tento článek obnoví služby Vzdálená plocha na virtuálním počítači:

* Povolte výchozí pravidlo brány Firewall na "Vzdálená plocha" Windows (TCP port 3389).
* Povolte připojení ke vzdálené ploše tak, že nastavíte hodnotu registru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na hodnotu 0.

Zkuste připojení znovu z vašeho počítače. Pokud jste pořád nejste schopni připojit přes vzdálenou plochu, zkontrolujte následující možné potíže:

* Na cílovém virtuálním počítači neběží služba Vzdálená plocha.
* Služba Vzdálená plocha nenaslouchá na portu TCP 3389.
* Brána Windows Firewall nebo jiné místní brány firewall má výstupní pravidlo, které brání provozu vzdálené plochy.
* Zjišťování neoprávněných vniknutí nebo software spuštěný na virtuálním počítači Azure pro monitorování sítě brání připojení ke vzdálené ploše.

Pro virtuální počítače vytvořené pomocí modelu nasazení classic můžete použít vzdálenou relaci Azure Powershellu na virtuálním počítači Azure. Nejprve potřebujete k instalaci certifikátu pro virtuální počítač hostující cloudovou službu. Přejděte na [nakonfigurovat zabezpečený vzdálený PowerShell přístup k Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) a stáhněte si **InstallWinRMCertAzureVM.ps1** soubor skriptu do místního počítače.

V dalším kroku nainstalujte Azure PowerShell, pokud jste tak již neučinili. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

Dále otevřete příkazový řádek prostředí Azure PowerShell a změňte aktuální složku na umístění **InstallWinRMCertAzureVM.ps1** souboru skriptu. Pro spuštění skriptu Azure Powershellu, musíte nastavit zásady spouštění správné. Spustit **Get-ExecutionPolicy** příkaz k určení aktuální úroveň zásad. Informace o nastavení odpovídající úroveň najdete v tématu [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Dále zadejte název vašeho předplatného Azure, název cloudové služby a název vašeho virtuálního počítače (odebrání < a > znaků), a potom spusťte tyto příkazy.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Můžete získat název správné předplatné z *SubscriptionName* vlastnost zobrazení **Get-AzureSubscription** příkazu. Název cloudové služby pro virtuální počítač z můžete získat *ServiceName* sloupce v zobrazení **Get-AzureVM** příkaz.

Zaškrtněte, pokud máte nový certifikát. Otevřete modul snap-in Certifikáty pro aktuálního uživatele a podívejte se **důvěryhodné kořenové certifikační autority\Certifikáty** složky. Měli byste vidět certifikát s názvem DNS ve sloupci vystaveno pro cloudové služby (Příklad: cloudservice4testing.cloudapp.net).

V dalším kroku spustit vzdálenou relaci Azure Powershellu pomocí těchto příkazů.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po zadání přihlašovacích údajů správce platný, by měl vypadat podobně jako následující příkazový řádek Azure Powershellu:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

První část této výzvy je název cloudové služby, který obsahuje cílový virtuální počítač, který může být odlišné od "cloudservice4testing.cloudapp.net". Nyní můžete zadávat příkazy pro tuto cloudovou službu k prozkoumání problémů uvedené a opravte konfiguraci prostředí Azure PowerShell.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Chcete-li ručně služby Vzdálená plocha naslouchání TCP port
Na řádku vzdálené relace prostředí Azure PowerShell spusťte tento příkaz.

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

Ukončete relaci vzdálené prostředí Azure PowerShell pomocí tohoto příkazu.

```powershell
Exit-PSSession
```

Ověřte, že koncový bod vzdálené plochy pro virtuální počítač Azure je rovněž na portu TCP. 3398 jako jeho interní port. Restartujte virtuální počítač Azure a zkuste to znovu připojení ke vzdálené ploše.

## <a name="additional-resources"></a>Další zdroje informací:
[Jak obnovit heslo nebo službu Vzdálená plocha pro virtuální počítače s Windows](../windows/reset-rdp.md)

[Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

[Řešení potíží s připojením Secure Shell (SSH) k virtuálnímu počítači s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

