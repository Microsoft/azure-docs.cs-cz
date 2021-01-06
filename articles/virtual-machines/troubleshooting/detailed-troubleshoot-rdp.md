---
title: Podrobné řešení potíží se vzdálenou plochou v Azure | Microsoft Docs
description: Přečtěte si podrobný postup řešení potíží s chybami vzdálené plochy, kde nemůžete na virtuální počítače s Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nejde se připojit ke vzdálené ploše, řešit potíže se vzdálenou plochou. Vzdálená plocha se nemůže připojit, chyby vzdálené plochy, řešení potíží vzdálené plochy, problémy vzdálené plochy.
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 5782765504eb1e0cb57558d3d4772d08de6b4d25
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913103"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Podrobné kroky pro řešení problémů s připojením ke vzdálené ploše virtuálních počítačů s Windows v Azure
Tento článek poskytuje podrobné kroky pro řešení potíží pro diagnostiku a opravu složitých chyb vzdálené plochy pro virtuální počítače Azure se systémem Windows.

> [!IMPORTANT]
> Abyste vyloučili častější chyby vzdálené plochy, před pokračováním si přečtěte [základní článek věnované řešení potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md) .

Může se zobrazit chybová zpráva vzdálené plochy, která se neshoduje s žádnou z konkrétních chybových zpráv popsaných v [Průvodci řešením potíží se základní vzdálenou plochou](troubleshoot-rdp-connection.md). Pomocí těchto kroků zjistíte, proč se klient vzdálené plochy (RDP) nemůže připojit ke službě RDP na virtuálním počítači Azure.


Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a ve Stack Overflowch fórech](https://azure.microsoft.com/support/forums/). Případně můžete také použít incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Součásti připojení ke vzdálené ploše
Připojení RDP se účastní tyto komponenty:

![Diagram znázorňující součásti, které jsou součástí připojení vzdálené plochy (RDP).](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Než budete pokračovat, může to znamenat, že se od posledního úspěšného připojení vzdálené plochy k virtuálnímu počítači může zobrazit co nejdřív. Například:

* Změnila se veřejná IP adresa virtuálního počítače nebo cloudové služby, která obsahuje virtuální počítač (označovaný také jako [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)virtuální IP adresa). Selhání protokolu RDP může být způsobeno tím, že mezipaměť klienta DNS má stále zaregistrovanou *starou IP adresu* pro název DNS. Vyprázdněte mezipaměť klienta DNS a zkuste znovu připojit virtuální počítač. Nebo se zkuste připojit přímo k nové virtuální IP adrese.
* Používáte aplikaci třetí strany ke správě připojení ke vzdálené ploše místo použití připojení vygenerovaného Azure Portal. Ověřte, že konfigurace aplikace zahrnuje správný port TCP pro přenosy vzdálené plochy. Tento port můžete pro klasický virtuální počítač v [Azure Portal](https://portal.azure.com)ověřit kliknutím na nastavení virtuálního počítače > koncové body.

## <a name="preliminary-steps"></a>Předběžné kroky
Než budete pokračovat na podrobné řešení potíží,

* Ověřte stav virtuálního počítače v Azure Portal pro jakékoli zjevné problémy.
* Postupujte podle [kroků pro rychlé opravy běžných chyb protokolu RDP v základní příručce pro odstraňování potíží](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* U vlastních imagí se ujistěte, že je virtuální pevný disk správně připravený před nahráním. Další informace najdete v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](../windows/prepare-for-upload-vhd-image.md).


Zkuste se znovu připojit k virtuálnímu počítači přes vzdálenou plochu po provedení těchto kroků.

## <a name="detailed-troubleshooting-steps"></a>Podrobný poradce při potížích
Klient vzdálené plochy nemusí být schopný kontaktovat službu Vzdálená plocha na VIRTUÁLNÍm počítači Azure kvůli problémům v následujících zdrojích:

* [Klientský počítač vzdálené plochy](#source-1-remote-desktop-client-computer)
* [Intranetové hraniční zařízení organizace](#source-2-organization-intranet-edge-device)
* [Koncový bod cloudové služby a seznam řízení přístupu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Virtuální počítač Azure se systémem Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Zdroj 1: klientský počítač vzdálené plochy
Ověřte, že počítač může vytvořit připojení vzdálené plochy k jinému místnímu počítači se systémem Windows.

![Diagram komponent v připojení vzdálené plochy (RDP) s klientem protokolu RDP byl zvýrazněn a šipka ukazující na jiný místní počítač, který označuje připojení.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Pokud nemůžete, v počítači ověřte následující nastavení:

* Místní nastavení brány firewall blokující provoz vzdálené plochy.
* Místně instalovaný klientský proxy software, který znemožňuje připojení ke vzdálené ploše.
* Místně nainstalovaný software pro monitorování sítě, který znemožňuje připojení ke vzdálené ploše.
* Další typy zabezpečovacího softwaru, které buď monitorují provoz, nebo povolují určité typy přenosů, které zabraňují připojení ke vzdálené ploše.

Ve všech těchto případech software dočasně zakažte a pokuste se připojit k místnímu počítači přes vzdálenou plochu. Pokud zjistíte skutečnou příčinu tímto způsobem, spolupracujte se správcem sítě a opravte nastavení softwaru pro povolení připojení ke vzdálené ploše.

## <a name="source-2-organization-intranet-edge-device"></a>Zdrojový 2: firemní hraniční zařízení v síti
Ověřte, že počítač, který je přímo připojený k Internetu, může vytvořit připojení ke vzdálené ploše na virtuálním počítači Azure.

![Diagram komponent v připojení vzdálené plochy (RDP) s klientem RDP připojeným k Internetu je zvýrazněný a šipka ukazující na virtuální počítač Azure, který označuje připojení.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvořte a otestujte pomocí nového virtuálního počítače Azure ve skupině prostředků nebo v cloudové službě. Další informace najdete v tématu [Vytvoření virtuálního počítače s Windows v Azure](../windows/quick-create-portal.md). Po dokončení testu můžete virtuální počítač a skupinu prostředků nebo cloudovou službu odstranit.

Pokud můžete vytvořit připojení ke vzdálené ploše přímo na počítači připojeném k Internetu, podívejte se na intranetové hraniční zařízení organizace pro:

* Interní brána firewall blokující připojení HTTPS k Internetu.
* Proxy server znemožňuje připojení ke vzdálené ploše.
* Zjišťování neoprávněných vniknutí nebo software pro monitorování sítě běžící na zařízeních ve vaší hraniční síti, který znemožňuje připojení ke vzdálené ploše.

Spolupracujte se správcem sítě a opravte nastavení podnikového internetového hraničního zařízení, které umožňuje připojení ke vzdálené ploše založené na protokolu HTTPS k Internetu.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: koncový bod cloudové služby a seznam ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

U virtuálních počítačů vytvořených pomocí modelu nasazení Classic ověřte, že se k VIRTUÁLNÍmu počítači Azure může připojit jiný virtuální počítač Azure, který je ve stejné cloudové službě nebo ve virtuální síti.

![Diagram komponent v připojení vzdálené plochy (RDP) s jedním virtuálním počítačem Azure se zvýrazní a šipka ukazující na jiný virtuální počítač Azure v rámci stejné cloudové služby, která označuje připojení.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> U virtuálních počítačů vytvořených v Správce prostředků přejděte na [zdroj 4: skupiny zabezpečení sítě](#source-4-network-security-groups).

Pokud nemáte jiný virtuální počítač ve stejné cloudové službě nebo virtuální síti, vytvořte ho. Postupujte podle kroků v části [Vytvoření virtuálního počítače s Windows v Azure](../windows/quick-create-portal.md). Po dokončení testu odstraňte testovací virtuální počítač.

Pokud se můžete připojit přes vzdálenou plochu k virtuálnímu počítači ve stejné cloudové službě nebo virtuální síti, vyhledejte tato nastavení:

* Konfigurace koncového bodu pro provoz vzdálené plochy na cílovém virtuálním počítači: privátní port TCP koncového bodu se musí shodovat s portem TCP, na kterém naslouchá služba vzdálené plochy virtuálního počítače (výchozí hodnota je 3389).
* Seznam řízení přístupu pro koncový bod vzdálené plochy na cílovém virtuálním počítači: seznamy řízení přístupu (ACL) umožňují zadat povolený nebo zakázaný příchozí provoz z Internetu na základě jeho zdrojové IP adresy. Nesprávně nakonfigurované seznamy řízení přístupu (ACL) můžou zabránit příchozímu provozu vzdálené plochy do koncového bodu. Zkontrolujte seznamy ACL, abyste měli jistotu, že příchozí provoz z vašich veřejných IP adres vašeho proxy serveru nebo jiného hraničního serveru je povolený. Další informace najdete v tématu [co je seznam ACL (Network Access Control List)?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Chcete-li zjistit, zda je koncový bod zdrojem problému, odeberte aktuální koncový bod a vytvořte nový a vyberte náhodný port v rozsahu 49152 – 65535 pro číslo externího portu. Další informace najdete v tématu [nastavení koncových bodů na virtuální počítač](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

## <a name="source-4-network-security-groups"></a>Zdroj 4: skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují podrobnější kontrolu nad povoleným příchozím a odchozím provozem. Můžete vytvořit pravidla pro podsítě a cloudové služby ve službě Azure Virtual Network.

Pomocí [ověření toku protokolu IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) můžete potvrdit, jestli pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat účinná pravidla skupiny zabezpečení, abyste měli jistotu, že příchozí pravidlo "Povolit" NSG existuje a že má nastavenou prioritu pro port RDP (standardně 3389). Další informace najdete v tématu [použití platných pravidel zabezpečení k řešení potíží s tokem provozu virtuálních počítačů](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Zdroj 5: virtuální počítač Azure založený na systému Windows
![Diagram komponent v připojení vzdálené plochy (RDP) se zvýrazněným VIRTUÁLNÍm počítačem Azure v rámci cloudové služby a zprávou, že se může jednat o "možný zdroj problémů". Modrá čára označuje, že pravidla skupiny zabezpečení sítě by mohla blokovat provoz do nebo z virtuálního počítače Azure.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postupujte podle pokynů v [tomto článku](./reset-rdp.md). Tento článek obnoví na virtuálním počítači službu Vzdálená plocha:

* Povolte výchozí pravidlo brány Windows Firewall pro vzdálenou plochu (TCP port 3389).
* Povolte připojení ke vzdálené ploše nastavením hodnoty registru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na hodnotu 0.

Opakujte pokus o připojení z počítače. Pokud se stále nemůžete připojit přes vzdálenou plochu, Projděte si následující možné problémy:

* Na cílovém virtuálním počítači není spuštěná služba Vzdálená plocha.
* Služba Vzdálená plocha nenaslouchá na portu TCP 3389.
* Brána Windows Firewall nebo jiná místní brána firewall má odchozí pravidlo, které brání provozu vzdálené plochy.
* Zjišťování neoprávněných vniknutí nebo software pro monitorování sítě běžící na virtuálním počítači Azure znemožňuje připojení ke vzdálené ploše.

U virtuálních počítačů vytvořených pomocí modelu nasazení Classic můžete použít relaci vzdálené Azure PowerShell k virtuálnímu počítači Azure. Nejdřív musíte nainstalovat certifikát pro hostující cloudovou službu virtuálního počítače. Přejděte na [Konfigurace zabezpečeného vzdáleného powershellového přístupu k Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) a Stáhněte si soubor **InstallWinRMCertAzureVM.ps1** skriptu do svého místního počítače.

V dalším kroku nainstalujte Azure PowerShell, pokud jste to ještě neudělali. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/).

Potom otevřete příkazový řádek Azure PowerShell a změňte aktuální složku na umístění souboru skriptu **InstallWinRMCertAzureVM.ps1** . Chcete-li spustit skript Azure PowerShell, je nutné nastavit správné zásady spouštění. Spuštěním příkazu **Get-ExecutionPolicy** určete aktuální úroveň zásad. Informace o nastavení příslušné úrovně najdete v tématu [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-5.1&preserve-view=true).

V dalším kroku zadejte název předplatného Azure, název cloudové služby a název virtuálního počítače (odebráním < a >ch znaků) a pak tyto příkazy spusťte.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Správný název předplatného můžete získat z vlastnosti *Subscription* pro zobrazení příkazu **Get-AzureSubscription** . Název cloudové služby pro virtuální počítač můžete získat ze sloupce *ServiceName* v zobrazení příkazu **Get-AzureVM** .

Ověřte, zda máte nový certifikát. Otevřete modul snap-in Certifikáty pro aktuálního uživatele a podívejte se do složky **důvěryhodných kořenových certifikačních autorit Authorities\Certificates** . V poli vydáno do sloupce (příklad: cloudservice4testing.cloudapp.net) by se měl zobrazit certifikát s názvem DNS vaší cloudové služby.

Dále inicializujte relaci vzdálené Azure PowerShell pomocí těchto příkazů.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po zadání platných přihlašovacích údajů správce by se měla zobrazit zpráva podobná následujícímu Azure PowerShell výzvě:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

První část této výzvy je název cloudové služby, který obsahuje cílový virtuální počítač, který se může lišit od "cloudservice4testing.cloudapp.net". Nyní můžete pro tuto cloudovou službu vystavovat Azure PowerShell příkazy, abyste prozkoumali zmíněné problémy a opravili konfiguraci.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Ruční oprava portu TCP služby Vzdálená plocha naslouchání
Na příkazovém řádku relace vzdáleného Azure PowerShell spusťte tento příkaz.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Vlastnost číslo_portu zobrazuje aktuální číslo portu. V případě potřeby změňte číslo portu vzdálené plochy zpátky na výchozí hodnotu (3389) pomocí tohoto příkazu.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Pomocí tohoto příkazu ověřte, že port byl změněn na 3389.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Pomocí tohoto příkazu ukončete relaci vzdálené Azure PowerShell.

```powershell
Exit-PSSession
```

Ověřte, že koncový bod vzdálené plochy pro virtuální počítač Azure používá také port TCP 3398 jako svůj interní port. Restartujte virtuální počítač Azure a pokuste se znovu připojit ke vzdálené ploše.

## <a name="additional-resources"></a>Další zdroje
[Postup resetování hesla nebo služby Vzdálená plocha pro virtuální počítače s Windows](./reset-rdp.md)

[Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/)

[Řešení potíží s připojením Secure Shell (SSH) k virtuálnímu počítači Azure se systémem Linux](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
