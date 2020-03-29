---
title: Podrobné řešení potíží s SSH pro virtuální počítač Azure | Dokumenty společnosti Microsoft
description: Podrobnější kroky řešení potíží SSH pro problémy s připojením k virtuálnímu počítači Azure
keywords: Ssh připojení odmítnuto, ssh error,azure ssh,SSH,SSH connection failed
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920123"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Podrobný postup řešení potíží s SSH při připojování k virtuálnímu počítači s Linuxem v Azure
Existuje mnoho možných důvodů, že klient SSH nemusí být schopen dosáhnout služby SSH na virtuálním počítači. Pokud jste provedli [obecnější kroky řešení potíží SSH](troubleshoot-ssh-connection.md), je třeba dále řešit problém s připojením. Tento článek vás provede podrobnými kroky pro řešení potíží, abyste zjistili, kde se nedaří připojení SSH a jak ho vyřešit.

## <a name="take-preliminary-steps"></a>Provést předběžné kroky
Následující diagram znázorňuje součásti, které jsou zapojeny.

![Diagram, který zobrazuje součásti služby SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Následující kroky vám pomohou izolovat zdroj selhání a zjistit řešení nebo řešení.

1. Zkontrolujte stav virtuálního virtuálního soudu na portálu.
   Na [webu Azure Portal](https://portal.azure.com)vyberte*Název virtuálního* **počítače** > .

   Podokno stavu virtuálního virtuálního provozu by mělo zobrazovat **spuštěno**. Posunutím dolů zobrazíte poslední aktivitu pro výpočetní prostředky, úložiště a síťové prostředky.

2. Výběrem **možnosti Nastavení** zkontrolujte koncové body, adresy IP, skupiny zabezpečení sítě a další nastavení.

   Virtuální virtuální ms by měl mít koncový bod definovaný pro provoz SSH, který můžete zobrazit v **koncových bodech** nebo **[skupině zabezpečení sítě](../../virtual-network/security-overview.md)**. Koncové body ve virtuálních záznamech, které byly vytvořeny pomocí Správce prostředků, jsou uloženy ve skupině zabezpečení sítě. Ověřte, zda byla pravidla použita pro skupinu zabezpečení sítě a zda jsou odkazována v podsíti.

Chcete-li ověřit připojení k síti, zkontrolujte nakonfigurované koncové body a zjistěte, jestli se můžete připojit k virtuálnímu počítače prostřednictvím jiného protokolu, jako je http nebo jiná služba.

Po těchto krocích zkuste připojení SSH znovu.

## <a name="find-the-source-of-the-issue"></a>Najít zdroj problému
Klient SSH ve vašem počítači se může selhat při připojení ke službě SSH na virtuálním počítači Azure kvůli problémům nebo chybné konfiguraci v následujících oblastech:

* [Klientský počítač SSH](#source-1-ssh-client-computer)
* [Hraniční zařízení organizace](#source-2-organization-edge-device)
* [Koncový bod cloudové služby a seznam řízení přístupu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Virtuální počítač Azure založený na Linuxu](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Zdroj 1: Klientský počítač SSH
Chcete-li odstranit počítač jako zdroj selhání, ověřte, zda může vytvořit připojení SSH k jinému místnímu počítači se systémem Linux.

![Diagram, který zvýrazňuje součásti klientského počítače SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Pokud se připojení nezdaří, zkontrolujte v počítači následující problémy:

* Nastavení místní brány firewall, které blokuje příchozí nebo odchozí přenosy SSH (TCP 22)
* Místně nainstalovaný klientský proxy software, který brání připojení SSH
* Místně nainstalovaný software pro sledování sítě, který brání připojení SSH
* Jiné typy zabezpečovacího softwaru, které buď sledují provoz, nebo povolují/zakáže určité typy provozu

Pokud platí jedna z těchto podmínek, dočasně zakažte software a zkuste připojení SSH k místnímu počítači zjistit důvod, proč je připojení blokováno v počítači. Poté spolupracujte se správcem sítě na opravě nastavení softwaru a povolte připojení SSH.

Pokud používáte ověřování certifikátů, ověřte, zda máte tato oprávnění ke složce SSH v domovském adresáři:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~ / .ssh / id_rsa (nebo jiné soubory, které mají své soukromé klíče uložené v nich)
* Chmod 644 ~/.ssh/known_hosts (obsahuje hostitele, ke kterým jste se připojili přes SSH)

## <a name="source-2-organization-edge-device"></a>Zdroj 2: Okrajové zařízení organizace
Chcete-li odstranit vaše organizace okraj zařízení jako zdroj selhání, ověřte, že počítač přímo připojený k Internetu můžete vytvořit připojení SSH k virtuálnímu počítači Azure. Pokud přistupujete k virtuálnímu počítači přes síť VPN od webu nebo připojení Azure ExpressRoute, přejděte na [zdroj 4: skupiny zabezpečení sítě](#nsg).

![Diagram, který zvýrazňuje hraniční zařízení organizace](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvořte nový virtuální počítač Azure ve vlastní skupině prostředků nebo cloudové službě a použijte tento nový virtuální počítač. Další informace najdete [v tématu Vytvoření virtuálního počítače se systémem Linux v Azure](../linux/quick-create-cli.md). Po dokončení testování odstraňte skupinu prostředků nebo virtuální počítač a cloudovou službu.

Pokud můžete vytvořit připojení SSH s počítačem, který je přímo připojený k Internetu, zkontrolujte, zda zařízení edge organizace:

* Interní brána firewall, která blokuje provoz SSH s Internetem
* Proxy server, který brání připojení SSH
* Software pro detekci vniknutí nebo sledování sítě spuštěný na zařízeních v hraniční síti, která brání připojení SSH

Spolupracujte se správcem sítě na opravě nastavení hraničních zařízení organizace a povolte přenos SSH s Internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: Koncový bod cloudové služby a acl

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Tento zdroj platí jenom pro virtuální počítače, které byly vytvořeny pomocí klasického modelu nasazení. U virtuálních uživatelů vytvořených pomocí Správce prostředků přejděte na [zdroj 4: Skupiny zabezpečení sítě](#nsg).

Chcete-li eliminovat koncový bod cloudové služby a ACL jako zdroj selhání, ověřte, že jiný virtuální počítač Azure ve stejné virtuální síti se můžete připojit pomocí SSH.

![Diagram, který zvýrazňuje koncový bod cloudové služby a acl](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Pokud nemáte jiný virtuální počítač ve stejné virtuální síti, můžete ho snadno vytvořit. Další informace najdete [v tématu Vytvoření virtuálního počítače s Linuxem v Azure pomocí příkazového příkazového příkazu](../linux/quick-create-cli.md). Odstraňte další virtuální počítač po dokončení testování.

Pokud můžete vytvořit připojení SSH s virtuálním počítačem ve stejné virtuální síti, zkontrolujte následující oblasti:

* **Konfigurace koncového bodu pro provoz SSH na cílovém virtuálním počítači.** Privátní port TCP koncového bodu by měl odpovídat portu TCP, na kterém naslouchá služba SSH na virtuálním počítači. (Výchozí port je 22). Ověřte číslo portu TCP SSH na portálu Azure výběrem**koncových bodů****nastavení** > *názvu* >  **virtuálních počítačů virtuálních počítačů** > .
* **ACL pro koncový bod provozu SSH na cílovém virtuálním počítači.** Seznam ACL umožňuje zadat povolený nebo odepřený příchozí provoz z Internetu na základě jeho zdrojové adresy IP. Chybně nakonfigurované počet návrže mohou zabránit příchozímu přenosu SSH do koncového bodu. Zkontrolujte počet adres AC, abyste se ujistili, že je povolen příchozí provoz z veřejných IP adres serveru proxy nebo jiného hraničního serveru. Další informace naleznete v tématu [O seznamech řízení přístupu k síti (ACLs)](../../virtual-network/virtual-networks-acl.md).

Chcete-li vyloučit koncový bod jako zdroj problému, odeberte aktuální koncový bod, vytvořte jiný koncový bod a zadejte název SSH (port TCP 22 pro číslo veřejného a soukromého portu). Další informace najdete v tématu [Nastavení koncových bodů na virtuálním počítači v Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Zdroj 4: Skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují podrobnější řízení povoleného příchozího a odchozího provozu. Můžete vytvořit pravidla, která zahrnuje podsítě a cloudové služby ve virtuální síti Azure. Zkontrolujte pravidla skupiny zabezpečení sítě, abyste se ujistili, že je povolen provoz SSH do internetu a z Internetu.
Další informace naleznete v tématu [O skupinách zabezpečení sítě](../../virtual-network/security-overview.md).

You can also use IP Verify to validate the NSG configuration. Další informace najdete v tématu [Přehled monitorování sítě Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Zdroj 5: Virtuální počítač Azure založený na Linuxu
Posledním zdrojem možných problémů je samotný virtuální počítač Azure.

![Diagram, který zvýrazňuje virtuální počítač Azure založený na Linuxu](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Pokud jste tak ještě neučinili, postupujte podle pokynů [k resetování hesla virtuálních počítačů založených na Linuxu](../linux/reset-password.md).

Zkuste se připojit z počítače znovu. Pokud se stále nezdaří, jsou následující některé z možných problémů:

* Služba SSH není spuštěna v cílovém virtuálním počítači.
* Služba SSH nenaslouchá na portu TCP 22. Chcete-li otestovat, nainstalujte klienta telnet do místního počítače a spusťte "telnet *cloudServiceName*.cloudapp.net 22". Tento krok určuje, pokud virtuální počítač umožňuje příchozí a odchozí komunikaci do koncového bodu SSH.
* Místní brána firewall na cílovém virtuálním počítači má pravidla, která brání příchozímu nebo odchozímu provozu SSH.
* Software pro zjišťování vniknutí nebo monitorování sítě, který běží na virtuálním počítači Azure, brání připojení MSH.

## <a name="additional-resources"></a>Další zdroje
Další informace o řešení potíží s přístupem k aplikacím [najdete v tématu Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure.](../linux/troubleshoot-app-connection.md)
