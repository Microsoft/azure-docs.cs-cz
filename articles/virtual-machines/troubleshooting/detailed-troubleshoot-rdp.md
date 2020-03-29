---
title: Podrobné řešení potíží se vzdálenou plochou v Azure | Dokumenty společnosti Microsoft
description: Projděte si podrobné kroky řešení potíží s chybami vzdálené plochy, ve kterých se virtuální počítače s Windows v Azure nedopoují.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Nelze se připojit ke vzdálené ploše, odstranit potíže se vzdálenou plochou, vzdálená plocha se nemůže připojit, chyby vzdálené plochy, řešení potíží se vzdálenou plochou, problémy se vzdálenou plochou
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea448b87f9e6954abecead2934bfb7f4ed04a9c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920140"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Podrobné kroky pro řešení problémů s připojením ke vzdálené ploše virtuálních počítačů s Windows v Azure
Tento článek obsahuje podrobné kroky pro řešení potíží s diagnostikou a opravou složitých chyb vzdálené plochy pro virtuální počítače Azure se systémem Windows.

> [!IMPORTANT]
> Chcete-li odstranit běžnější chyby vzdálené plochy, přečtěte si [před pokračováním základní článek o řešení potíží pro vzdálenou plochu.](troubleshoot-rdp-connection.md)

Může se zobrazit chybová zpráva Vzdálená plocha, která se nepodobá žádné konkrétní chybové zprávě, na které se vztahuje [základní příručka pro řešení potíží se vzdálenou plochou](troubleshoot-rdp-connection.md). Podle těchto kroků zjistěte, proč se klient vzdálené plochy (RDP) nemůže připojit ke službě RDP na virtuálním počítači Azure.


Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete také soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na Získat **podporu**. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Součásti připojení ke vzdálené ploše
Do připojení RDP se podílejí následující součásti:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Než budete pokračovat, může to pomoci psychicky zkontrolovat, co se změnilo od posledního úspěšného připojení ke vzdálené ploše k virtuálnímu počítači. Například:

* Veřejná IP adresa virtuálního počítače nebo cloudové služby obsahující virtuální počítač (označovaná také jako virtuální IP adresa [VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)se změnila. Selhání protokolu RDP může být způsobeno tím, že mezipaměť klienta DNS má stále *starou adresu IP registrovanou* pro název DNS. Vyprázdněte mezipaměť klienta DNS a zkuste virtuální počítač připojit znovu. Nebo zkuste spojit přímo s novým VIP.
* Používáte aplikaci jiného výrobce ke správě připojení ke vzdálené ploše namísto použití připojení generovaného portálem Azure. Ověřte, zda konfigurace aplikace obsahuje správný port TCP pro provoz vzdálené plochy. Tento port můžete zkontrolovat pro klasický virtuální počítač na [webu Azure Portal](https://portal.azure.com), kliknutím na nastavení virtuálního počítače > koncových bodů.

## <a name="preliminary-steps"></a>Předběžné kroky
Než přistoují k podrobnému řešení

* Zkontrolujte stav virtuálního počítače na webu Azure Portal, kde najdete všechny zjevné problémy.
* Postupujte podle [rychlých kroků pro běžné chyby PRV v základní příručce pro řešení potíží](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* U vlastních obrázků se před nahráním ujistěte, že je virtuální pevný disk správně připraven. Další informace najdete [v tématu Příprava virtuálního pevného disku nebo vhdx windows pro nahrání do Azure](../windows/prepare-for-upload-vhd-image.md).


Zkuste se znovu připojit k virtuálnímu počítači přes vzdálenou plochu po těchto krocích.

## <a name="detailed-troubleshooting-steps"></a>Podrobný poradce při potížích
Klient vzdálené plochy nemusí být schopen dosáhnout služby Vzdálená plocha na virtuálním počítači Azure kvůli problémům z následujících zdrojů:

* [Klientský počítač vzdálené plochy](#source-1-remote-desktop-client-computer)
* [Hraniční zařízení sítě organizace](#source-2-organization-intranet-edge-device)
* [Koncový bod cloudové služby a seznam řízení přístupu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Virtuální počítač Azure založený na Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Zdroj 1: Klientský počítač vzdálené plochy
Ověřte, zda může počítač navázat připojení ke vzdálené ploše k jinému místnímu počítači se systémem Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Pokud nemůžete, zkontrolujte v počítači následující nastavení:

* Nastavení místní brány firewall, které blokuje provoz vzdálené plochy.
* Místně nainstalovaný software proxy klienta, který brání připojení vzdálené plochy.
* Místně nainstalovaný software pro sledování sítě, který brání připojení vzdálené plochy.
* Jiné typy zabezpečovacího softwaru, které buď sledují provoz, nebo povolují nebo zakážou určité typy přenosů, které brání připojení vzdálené plochy.

Ve všech těchto případech dočasně zakažte software a pokuste se připojit k místnímu počítači prostřednictvím vzdálené plochy. Pokud zjistíte skutečnou příčinu tímto způsobem, obraťte se na správce sítě a opravte nastavení softwaru a povolte připojení ke vzdálené ploše.

## <a name="source-2-organization-intranet-edge-device"></a>Zdroj 2: Organizační intranetové hraniční zařízení
Ověřte, že počítač přímo připojený k Internetu může provádět připojení ke vzdálené ploše k vašemu virtuálnímu počítači Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvořte a otestujte pomocí nového virtuálního počítače Azure ve skupině prostředků nebo cloudové službě. Další informace najdete [v tématu Vytvoření virtuálního počítače se systémem Windows v Azure](../virtual-machines-windows-hero-tutorial.md). Po testu můžete odstranit virtuální počítač a skupinu prostředků nebo cloudovou službu.

Pokud můžete vytvořit připojení ke vzdálené ploše s počítačem přímo připojeným k Internetu, zkontrolujte zařízení intranetová síť vaší organizace, kde najdete:

* Interní brána firewall blokující připojení HTTPS k Internetu.
* Proxy server bránící připojení vzdálené plochy.
* Software pro zjišťování vniknutí nebo sledování sítě spuštěný na zařízeních v hraniční síti, která brání připojení vzdálené plochy.

Spolupracujte se správcem sítě a opravte nastavení zařízení intranet ová sítě intranet, které umožňuje připojení vzdálené plochy založené na protokolu HTTPS k Internetu.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: Koncový bod cloudové služby a acl

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

U virtuálních počítačů vytvořených pomocí modelu klasického nasazení ověřte, že jiný virtuální počítač Azure, který je ve stejné cloudové službě nebo virtuální síti, může vytvořit připojení ke vzdálené ploše k vašemu virtuálnímu počítači Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> U virtuálních počítačů vytvořených ve Správci prostředků přejděte na [zdroj 4: Skupiny zabezpečení sítě](#source-4-network-security-groups).

Pokud nemáte jiný virtuální počítač ve stejné cloudové službě nebo virtuální síti, vytvořte ho. Postupujte podle pokynů v [části Vytvoření virtuálního počítače se systémem Windows v Azure](../virtual-machines-windows-hero-tutorial.md). Po dokončení testu odstraňte testovací virtuální počítač.

Pokud se můžete připojit přes vzdálenou plochu k virtuálnímu počítači ve stejné cloudové službě nebo virtuální síti, zkontrolujte tato nastavení:

* Konfigurace koncového bodu pro provoz vzdálené plochy na cílovém virtuálním počítači: Privátní port TCP koncového bodu musí odpovídat portu TCP, na kterém naslouchá služba Vzdálená plocha virtuálního počítače (výchozí hodnota je 3389).
* Seznam ACL pro koncový bod přenosu vzdálené plochy na cílovém virtuálním počítači: seznamy ACL umožňují určit povolený nebo odepřený příchozí provoz z Internetu na základě jeho zdrojové adresy IP. Chybně nakonfigurované počet ní přístupové konfigurované mohou zabránit příchozímu přenosu vzdálené plochy do koncového bodu. Zkontrolujte počet adres AC, abyste se ujistili, že je povolen příchozí provoz z veřejných IP adres serveru proxy nebo jiného hraničního serveru. Další informace naleznete [v tématu Co je seznam řízení přístupu k síti (ACL)?](../../virtual-network/virtual-networks-acl.md)

Chcete-li zkontrolovat, zda je koncový bod zdrojem problému, odeberte aktuální koncový bod a vytvořte nový a zvolte náhodný port v rozsahu 49152–65535 pro číslo externího portu. Další informace najdete v tématu [Jak nastavit koncové body pro virtuální počítač](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Zdroj 4: Skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují podrobnější řízení povolených příchozích a odchozích přenosů. Můžete vytvořit pravidla zahrnující podsítě a cloudové služby ve virtuální síti Azure.

Pomocí [ověření toku IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) můžete ověřit, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat účinná pravidla skupiny zabezpečení, abyste zajistili, že příchozí pravidlo skupiny zabezpečení zabezpečení existuje a má prioritu pro port RDP (výchozí 3389). Další informace naleznete [v tématu Použití efektivních pravidel zabezpečení k řešení potíží s tokem provozu virtuálních montovací](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Zdroj 5: Virtuální počítač Azure založený na Windows
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postupujte podle pokynů v [tomto článku](../windows/reset-rdp.md). Tento článek obnoví službu Vzdálená plocha ve virtuálním počítači:

* Povolte výchozí pravidlo brány Windows Firewall "Vzdálená plocha" (port TCP 3389).
* Povolte připojení ke vzdálené ploše nastavením hodnoty registru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na hodnotu registru 0.

Zkuste připojení z počítače znovu. Pokud se stále nemůžete připojit přes vzdálenou plochu, zkontrolujte následující možné problémy:

* Služba Vzdálená plocha není spuštěna na cílovém virtuálním počítači.
* Služba Vzdálená plocha nenaslouchá na portu TCP 3389.
* Brána Windows Firewall nebo jiná místní brána firewall má odchozí pravidlo, které brání přenosu vzdálené plochy.
* Software pro zjišťování vniknutí nebo monitorování sítě spuštěný ve virtuálním počítači Azure brání připojením ke vzdálené ploše.

Pro virtuální počítače vytvořené pomocí klasického modelu nasazení můžete použít vzdálenou relaci Azure PowerShellu k virtuálnímu počítači Azure. Nejprve je třeba nainstalovat certifikát pro hostingovou cloudovou službu virtuálního počítače. Přejděte na [konfigurace zabezpečeného vzdáleného přístupu k prostředí PowerShell k virtuálním počítačům Azure](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) a stáhněte si soubor skriptu **InstallWinRMCertAzureVM.ps1** do místního počítače.

Dále nainstalujte Azure PowerShell, pokud jste tak ještě neučinili. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

Dále otevřete příkazový řádek Prostředí Azure PowerShell a změňte aktuální složku na umístění souboru skriptu **InstallWinRMCertAzureVM.ps1.** Chcete-li spustit skript Azure PowerShell, musíte nastavit správné zásady provádění. Spusťte příkaz **Get-ExecutionPolicy** a určete aktuální úroveň zásad. Informace o nastavení příslušné úrovně naleznete v [tématu Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Dále vyplňte název předplatného Azure, název cloudové služby a název virtuálního počítače (odebrání < a > znaků) a pak spusťte tyto příkazy.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Můžete získat správný název předplatného z *SubscriptionName* vlastnost zobrazení **get-AzureSubscription** příkazu. Název cloudové služby pro virtuální počítač můžete získat ve sloupci *ServiceName* v zobrazení příkazu **Get-AzureVM.**

Zkontrolujte, zda máte nový certifikát. Otevřete modul snap-in Certifikáty pro aktuálního uživatele a vyhledejte složku **Důvěryhodné kořenové certifikační úřady\Certifikáty.** Certifikát s názvem DNS cloudové služby byste měli vidět ve sloupci Vystaveno na (například: cloudservice4testing.cloudapp.net).

Dále spusťte vzdálenou relaci Azure PowerShellu pomocí těchto příkazů.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po zadání platných přihlašovacích údajů správce byste měli vidět něco podobného jako na následující výzvu Azure PowerShellu:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

První část této výzvy je název cloudové služby, která obsahuje cílový virtuální počítač, který se může lišit od "cloudservice4testing.cloudapp.net". Teď můžete vydávat příkazy Azure PowerShell pro tuto cloudovou službu prozkoumat uvedené problémy a opravit konfiguraci.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Ruční oprava portu TCP služby Vzdálená plocha
Na vzdáleném řádku relace Azure PowerShell spusťte tento příkaz.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Vlastnost PortNumber zobrazuje aktuální číslo portu. V případě potřeby změňte číslo portu vzdálené plochy zpět na výchozí hodnotu (3389) pomocí tohoto příkazu.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Pomocí tohoto příkazu ověřte, zda byl port změněn na 3389.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Ukončete vzdálenou relaci Azure PowerShellu pomocí tohoto příkazu.

```powershell
Exit-PSSession
```

Ověřte, že koncový bod vzdálené plochy pro virtuální počítač Azure používá jako interní port také port TCP 3398. Restartujte virtuální počítač Azure a zkuste připojení ke vzdálené ploše znovu.

## <a name="additional-resources"></a>Další zdroje
[Jak resetovat heslo nebo službu Vzdálená plocha pro virtuální počítače s Windows](../windows/reset-rdp.md)

[Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)

[Poradce při potížích s připojením zabezpečeného prostředí (SSH) k virtuálnímu počítači Azure založenému na Linuxu](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

