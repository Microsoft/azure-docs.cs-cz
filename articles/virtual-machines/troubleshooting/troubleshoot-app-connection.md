---
title: Řešení potíží s přístupu k aplikacím virtuálních počítačů v Azure | Dokumentace Microsoftu
description: Podrobný postup řešení použijte k izolování problémů s připojením k aplikacím spuštěným na virtuálních počítačích v Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nelze spustit aplikaci, program neotevře, naslouchání portu blokované, nelze spustit program, zablokuje port pro naslouchání
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 81535d51617a419174331dbf9b18ea558913dfa9
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413158"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Řešení problémů s připojením aplikace na virtuálních počítačích v Azure

Existují různé důvody, kdy nelze spustit nebo připojit k aplikaci spuštěné na virtuálním počítači Azure (VM). Příčinám patří aplikace není spuštěna nebo naslouchá na očekávaných portech, port pro naslouchání blokované nebo sítě pravidla není správně předávání provoz do aplikace. Tento článek popisuje metodický přístup k vyhledání a opravě problému.

Pokud máte problémy s připojením k virtuálnímu počítači pomocí RDP nebo SSH, přečtěte si následující články nejdřív:

* [Řešení potíží s připojeními vzdálené plochy na na základě Windows virtuálním počítači Azure](troubleshoot-rdp-connection.md)
* [Řešení potíží s připojením Secure Shell (SSH) k virtuálnímu počítači s Linuxem v Azure](troubleshoot-ssh-connection.md).

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="quick-start-troubleshooting-steps"></a>Rychlý start kroků pro řešení potíží
Pokud máte potíže s připojením k aplikaci, zkuste následující postup řešení potíží. Po provedení každého kroku zkuste se připojit k vaší aplikaci znovu spustit:

* Restartujte virtuální počítač
* Znovu vytvořte koncový bod / pravidla brány firewall / network pravidla skupiny zabezpečení (NSG)
  * [Model Resource Manager – Správa skupin zabezpečení sítě](../../virtual-network/manage-network-security-group.md)
  * [Model Classic - koncové body spravovat Cloud Services](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Připojení z jiného umístění, jako je například jinou virtuální síť Azure
* Opětovné nasazení virtuálního počítače
  * [Opětovné nasazení virtuálního počítače Windows](redeploy-to-new-node-windows.md)
  * [Opětovné nasazení virtuálního počítače s Linuxem](redeploy-to-new-node-linux.md)
* Znovu vytvořte virtuální počítač

Další informace najdete v tématu [řešení potíží s připojením koncového bodu (RDP/SSH/HTTP, chyby atd.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Podrobný Poradce při potížích přehled
Existují čtyři hlavní oblasti pro řešení potíží s přístupem aplikaci, která běží na virtuálním počítači Azure.

![řešení potíží s aplikaci nelze spustit.](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikace běžící na virtuálním počítači Azure.
   * Běží správně vlastní aplikace?
2. Virtuální počítač Azure.
   * Je virtuální počítač správně spuštěn a reagovat na požadavky?
3. Koncové body sítě Azure.
   * Koncové body služeb cloud pro virtuální počítače v modelu nasazení Classic.
   * Skupiny zabezpečení sítě a pravidla příchozího překladu adres pro virtuální počítače v modelu nasazení Resource Manager.
   * Můžete provoz od uživatele do virtuálního počítače nebo aplikace na očekávaných portech?
4. Hraniční zařízení naprogramujete Internet.
   * Pravidla brány firewall na místě brání provoz směřující správně?

Hlavní oblasti, které mohou způsobovat problémy pro klientské počítače, které mají přístup k aplikaci prostřednictvím připojení site-to-site VPN nebo ExpressRoute, jsou aplikace a virtuální počítač Azure.

K určení příčiny problému a jeho opravy, postupujte podle těchto kroků.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: Přístup k aplikacím z cílového virtuálního počítače
Došlo k pokusu o přístup k aplikacím s programem příslušného klienta z virtuálního počítače, na kterém je spuštěný. Použijte název místního hostitele, IP adresu místního nebo adresu zpětné smyčky (127.0.0.1).

![Spusťte aplikaci přímo z virtuálního počítače](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Například pokud aplikace je webový server, otevřete prohlížeč na virtuálním počítači a pokusí o přístup k webové stránky hostované na virtuálním počítači.

Pokud přístup k aplikaci, přejděte na [kroku 2](#step2).

Pokud nelze získat přístup k aplikaci, ověřte následující nastavení:

* Aplikace běží na cílovém virtuálním počítači.
* Aplikace naslouchá na očekávaných portech TCP a UDP.

Ve Windows a virtuální počítače se systémem Linux, použijte **netstat - a** příkazu zobrazte aktivní naslouchající porty. Prohlédněte si výstup pro očekávaných portech, na kterých by vaše aplikace naslouchá. Restartování aplikace nebo nakonfigurovat, aby podle potřeby použijte očekávaných portech a pokusí o přístup k aplikaci můžete lokálně spustit znovu.

## <a id="step2"></a>Krok 2: Přístup k aplikacím z jiného virtuálního počítače ve stejné virtuální síti
Došlo k pokusu o přístup k aplikaci z různých virtuálních počítačů, ale ve stejné virtuální síti pomocí názvu hostitele Virtuálního počítače nebo jeho Azure přiřazené veřejné, privátní nebo poskytovatele IP adresu. Pro virtuální počítače vytvořené pomocí modelu nasazení classic nepoužívejte veřejnou IP adresu z cloudové služby.

![Spusťte aplikaci z různých virtuálních počítačů](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Například pokud aplikace je webový server, pokusí otevřít webovou stránku v prohlížeči na odlišném virtuálním počítači ve stejné virtuální síti.

Pokud přístup k aplikaci, přejděte na [kroku 3](#step3).

Pokud nelze získat přístup k aplikaci, ověřte následující nastavení:

* Hostitelské brány firewall na cílovém virtuálním počítači je povolení příchozího požadavku a odpovědi na odchozí provoz.
* Zjišťování neoprávněných vniknutí nebo softwaru, která běží na cílovém virtuálním počítači pro monitorování sítě povoluje provoz.
* Koncové body cloudových služeb nebo skupiny zabezpečení sítě povolují přenos:
  * [Model Classic - koncové body spravovat Cloud Services](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model Resource Manager – Správa skupin zabezpečení sítě](../../virtual-network/manage-network-security-group.md)
* Samostatná součást spuštěná ve virtuálním počítači v cestě mezi testovacího virtuálního počítače a virtuálního počítače, jako je například nástroj pro vyrovnávání zatížení nebo brány firewall, je povolení provozu.

Na virtuálním počítači na základě Windows pomocí brány Windows Firewall s pokročilým zabezpečením k určení, zda pravidla brány firewall vyloučit příchozí a odchozí provoz vaší aplikace.

## <a id="step3"></a>Krok 3: Přístup k aplikaci dostat zvnějšku virtuální sítě
Došlo k pokusu o přístup k aplikaci z počítače mimo virtuální síť jako virtuální počítač, na kterém je aplikace spuštěna. Použijte jinou síť jako původní klientský počítač.

![Spusťte aplikaci z počítače mimo virtuální síť](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Například pokud aplikace je webový server, pokusí otevřít webovou stránku v prohlížeči spuštěného na počítači, který není ve virtuální síti.

Pokud nelze získat přístup k aplikaci, ověřte následující nastavení:

* Pro virtuální počítače vytvořené pomocí modelu nasazení classic:
  
  * Ověřte, že konfigurace koncového bodu pro virtuální počítač povoluje příchozí provoz, zejména protokol (TCP nebo UDP) a čísla veřejných a privátních portů.
  * Zkontrolujte, zda seznamy řízení přístupu (ACL) v koncovém bodě nebrání příchozí provoz z Internetu.
  * Další informace najdete v tématu [jak nastavit koncové body k virtuálnímu počítači](../windows/classic/setup-endpoints.md).
* Pro virtuální počítače vytvořené pomocí modelu nasazení Resource Manager:
  
  * Ověřte, že příchozí konfigurace pravidla NAT pro virtuální počítač povoluje příchozí provoz, zejména protokol (TCP nebo UDP) a čísla veřejných a privátních portů.
  * Ověřte, že skupiny zabezpečení sítě povolují příchozí žádosti a odpovědi na odchozí provoz.
  * Další informace najdete v tématu [co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)

Pokud je virtuální počítač nebo koncový bod členem sady s vyrovnáváním zatížení:

* Ověřte správnost protokol testu (TCP nebo UDP) a číslo portu.
* Pokud se liší od sady s vyrovnáváním zatížení protokolu a portu sondy protokol a port:
  * Ověřte, že aplikace naslouchá na protokol testu (TCP nebo UDP) a číslo portu (použijte **netstat – a** na cílovém virtuálním počítači).
  * Ověřte, že hostitelské brány firewall na cílovém virtuálním počítači se povoluje příchozí zkušebního požadavku a test odchozí přenosy odpovědí.

Pokud přístup k aplikaci, ujistěte se, že povoluje hraniční zařízení naprogramujete Internetu:

* Odchozí žádost o provozu aplikace z klientského počítače na virtuálním počítači Azure.
* Aplikace příchozí přenosy odpovědí z virtuálních počítačů Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 nemůže získat přístup k aplikaci, můžete ověřit IP zkontrolujte nastavení. 

Další informace najdete v tématu [Přehled monitorování Azure network](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Další zdroje informací:
[Řešení potíží s připojeními vzdálené plochy na na základě Windows virtuálním počítači Azure](troubleshoot-rdp-connection.md)

[Řešení potíží s připojením Secure Shell (SSH) k virtuálnímu počítači s Linuxem v Azure](troubleshoot-ssh-connection.md)


