---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 23abf13aca84bde3a9e4a82722051689cc177a2b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371265"
---
Při nesmí začínat ani připojit k aplikaci spuštěné v Azure virtuální počítač (VM) se z různých důvodů. Z těchto důvodů aplikace není spuštěna nebo naslouchá na očekávaných portech, port pro naslouchání blokované nebo sítě pravidla není správně předávání provoz do aplikace. Tento článek popisuje metodický přístup k vyhledání a odstranění problému.

Pokud máte problémy s připojením k virtuálnímu počítači pomocí protokolu RDP nebo SSH, najdete v jednom z následujících článků nejdřív:

* [Řešení potíží s připojení ke vzdálené ploše do systému Windows Azure virtuálního počítače](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)
* [Řešení potíží s Secure Shell (SSH) připojení k virtuálnímu počítači systémem Linux Azure](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../articles/resource-manager-deployment-model.md). Tento článek popisuje použití obou modelů, ale Microsoft doporučuje, aby většina nových nasazení používala model Resource Manager.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [MSDN Azure a fóra Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="quick-start-troubleshooting-steps"></a>Úvodní při řešení potíží
Pokud máte potíže s připojením k aplikaci, vyzkoušejte následující obecné řešení potíží. Po dokončení každého kroku zkuste se připojit k aplikaci znovu:

* Restartujte virtuální počítač
* Znovu vytvořte koncový bod / pravidla brány firewall / sítě pravidel zabezpečení skupiny (NSG)
  * [Model Resource Manager - spravovat skupiny zabezpečení sítě](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
  * [Klasického modelu – koncové body spravovat cloudové služby](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
* Připojení z různých umístění, jako je například jinou virtuální síť Azure
* Znovu nasadit virtuální počítač
  * [Znovu nasaďte Windows virtuálního počítače.](../articles/virtual-machines/windows/redeploy-to-new-node.md)
  * [Opětovné nasazení virtuálního počítače s Linuxem](../articles/virtual-machines/linux/redeploy-to-new-node.md)
* Znovu vytvořte virtuální počítač

Další informace najdete v tématu [řešení potíží s připojením koncový bod (RDP/SSH/HTTP, selhání atd.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Podrobný přehled řešení potíží
Existují čtyři hlavní oblasti k řešení potíží s přístupem aplikace, která běží na virtuálním počítači Azure.

![řešení potíží s nelze spustit aplikaci](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikace běžící na virtuálním počítači Azure.
   * Vlastní aplikace funguje správně?
2. Virtuální počítač Azure.
   * Virtuální počítač je správně spuštěn a reagovat na požadavky?
3. Koncové body Azure sítě.
   * Koncové body služby cloudu pro virtuální počítače ve model nasazení Classic.
   * Skupiny zabezpečení sítě a příchozích pravidel NAT pro virtuální počítače v modelu nasazení Resource Manager.
   * Tok od uživatelů do virtuálního počítače nebo aplikace na očekávaných portech můžete provozu?
4. Hraniční zařízení Internetu.
   * Pravidla brány firewall na místě brání provoz předávaných správně?

Pro klientské počítače, které mají přístup k aplikaci prostřednictvím připojení site-to-site VPN nebo ExpressRoute hlavní oblasti, které může způsobit problémy jsou aplikace a virtuální počítač Azure.

Pokud chcete zjistit příčinu problému a jeho oprava, postupujte podle těchto kroků.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: Přístup k aplikaci z cílového virtuálního počítače
Došlo k pokusu o přístup k aplikaci s programem příslušného klienta z virtuálního počítače, na kterém je spuštěný. Použijte název místního hostitele, místní IP adresu nebo adresu zpětné smyčky (127.0.0.1).

![spustit aplikaci přímo z virtuálního počítače](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Například pokud aplikace je webový server, otevřete prohlížeč na virtuálním počítači a snaží o přístup k webové stránky hostované na virtuálním počítači.

Pokud přístup k aplikaci, přejděte na [kroku 2](#step2).

Pokud máte přístup k aplikaci, ověřte následující nastavení:

* Aplikace běží na cílový virtuální počítač.
* Aplikace naslouchá na očekávaných portech TCP a UDP.

V systému Windows a virtuálních počítačích se systémem Linux, použijte **netstat - a** příkazu zobrazte aktivní naslouchající porty. Prohlédněte si výstup pro očekávané porty, na kterých by vaše aplikace naslouchá. Restartování aplikace nebo nakonfigurujte ho podle potřeby použijte očekávaných portech a pokuste se znovu přístup k aplikaci místně.

## <a id="step2"></a>Krok 2: Přístup k aplikaci z jiného virtuálního počítače ve stejné virtuální síti
Došlo k pokusu o přístup k aplikaci z různých virtuálního počítače, ale ve stejné virtuální síti, pomocí názvu hostitele Virtuálního počítače nebo přiřazené Azure veřejné, privátní nebo poskytovatele IP adresu. Pro virtuální počítače vytvořené pomocí modelu nasazení classic nepoužívejte veřejnou IP adresu cloudové služby.

![spustit aplikaci z různých virtuálního počítače](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Například pokud aplikace je webový server, pokusu o přístup k webové stránky z prohlížeče na jiný virtuální počítač ve stejné virtuální síti.

Pokud přístup k aplikaci, přejděte na [krok 3](#step3).

Pokud máte přístup k aplikaci, ověřte následující nastavení:

* Brána firewall hostitele na cílovém virtuálním počítači umožňuje příchozí žádosti a odpovědi odchozí provoz.
* Zjišťování neoprávněných vniknutí nebo monitorování softwaru na cílovém virtuálním počítači spuštěna sítě umožňuje provoz.
* Koncové body cloudové služby nebo skupiny zabezpečení sítě stále umožňují provoz:
  * [Klasického modelu – koncové body spravovat cloudové služby](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model Resource Manager - spravovat skupiny zabezpečení sítě](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
* Samostatná komponenta běžících ve vašem virtuálním počítači v cestě mezi testovací virtuální počítač a virtuální počítač, jako je nástroj pro vyrovnávání zatížení nebo brány firewall, je povolení provoz.

Na virtuálním počítači systému Windows pomocí brány Windows Firewall s pokročilým zabezpečením k určení, zda pravidla brány firewall vyloučit příchozí a odchozí provoz vaší aplikace.

## <a id="step3"></a>Krok 3: Přístup k aplikaci z mimo virtuální síť
Došlo k pokusu o přístup k aplikaci z počítače mimo virtuální síť jako virtuální počítač, na kterém je aplikace spuštěna. Použijte jinou síť jako původní klientském počítači.

![Spusťte aplikaci z počítače mimo virtuální síť](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Například pokud aplikace je webový server, pokusu o přístup k webové stránky z prohlížeče na počítači, který není ve virtuální síti.

Pokud máte přístup k aplikaci, ověřte následující nastavení:

* Pro virtuální počítače vytvořené pomocí modelu nasazení classic:
  
  * Ověřte, že konfigurace koncového bodu pro virtuální počítač umožňuje příchozí provoz, zejména protokol (TCP nebo UDP) a čísly veřejné a privátní port.
  * Zkontrolujte, zda seznamy řízení přístupu (ACL) pro koncový bod nebrání příchozí provoz z Internetu.
  * Další informace najdete v tématu [jak nastavit koncové body k virtuálnímu počítači](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Pro virtuální počítače vytvořené pomocí modelu nasazení Resource Manager:
  
  * Ověřte, že příchozí pravidlo NAT konfiguraci pro virtuální počítač umožňuje příchozí provoz, zejména protokol (TCP nebo UDP) a čísly veřejné a privátní port.
  * Ověřte, že jsou skupiny zabezpečení sítě umožňuje příchozí žádosti a odpovědi odchozí provoz.
  * Další informace najdete v tématu [co je skupina zabezpečení sítě?](../articles/virtual-network/security-overview.md)

Pokud je virtuální počítač nebo koncový bod členem sadu Vyrovnávání zatížení sítě:

* Ověřte správnost testu protokol (TCP nebo UDP) a číslo portu.
* Pokud se test protokol a port se liší od sady s vyrovnáváním zatížení protokol a port:
  * Ověřte, že aplikace naslouchá na test protokol (TCP nebo UDP) a číslo portu (použijte **netstat-a** na cílovém virtuálním počítači).
  * Ověřte, že brána firewall hostitele na cílovém virtuálním počítači umožňuje příchozí zkušebního požadavku a provoz odpovědi odchozí testu.

Pokud přístup k aplikaci, ověřte, že je umožňuje vaše Internet hraniční zařízení:

* Odchozí aplikace požadavek provoz z klientského počítače na virtuální počítač Azure.
* Příchozí aplikace odpovědi provoz z virtuálního počítače Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 Pokud nemají přístup k aplikaci, pomocí ověření IP zkontrolujte nastavení. 

Další informace najdete v tématu [síť Azure Přehled monitorování](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Další zdroje informací:
[Řešení potíží s připojení ke vzdálené ploše do systému Windows Azure virtuálního počítače](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)

[Řešení potíží s Secure Shell (SSH) připojení k virtuálnímu počítači systémem Linux Azure](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)

