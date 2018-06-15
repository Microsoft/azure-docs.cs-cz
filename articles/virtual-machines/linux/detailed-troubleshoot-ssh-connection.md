---
title: Podrobné řešení potíží SSH pro virtuální počítač Azure | Microsoft Docs
description: Podrobnější SSH řešení potíží s kroky pro připojení k virtuální počítač Azure
keywords: SSH připojení odmítnuto, ssh chyby, azure ssh, připojení SSH se nezdařilo
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 88f3ca3202359f9f45f5b9a5054ab95b40558520
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365487"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Podrobné SSH řešení potíží s kroky pro připojení k virtuální počítač s Linuxem v Azure
Existuje mnoho možných příčin, které použije klient SSH nemusí být možné kontaktovat službu SSH ve virtuálním počítači. Pokud jste provedli prostřednictvím Čím více [obecné SSH při řešení potíží](troubleshoot-ssh-connection.md), budete muset dále řešit potíže s připojením. Tento článek vás provede podrobný postup řešení potíží k určení, kde se nedaří připojení SSH a jak je vyřešit.

## <a name="take-preliminary-steps"></a>Proveďte předběžnou kroky
Následující diagram znázorňuje komponenty, které se podílejí.

![Diagram zobrazující komponenty služby SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Následující kroky umožňují izolovat příčinu selhání a zjistěte řešení či alternativní řešení.

1. Zkontrolujte stav virtuálního počítače na portálu.
   V [portál Azure](https://portal.azure.com), vyberte **virtuální počítače** > *název virtuálního počítače*.

   V podokně Stav pro virtuální počítač by měl zobrazit **systémem**. Posuňte se dolů a zobrazit poslední aktivitu na účtu pro výpočty, úložiště a síťové prostředky.

2. Vyberte **nastavení** prozkoumat koncových bodů, IP adres, skupiny zabezpečení sítě a další nastavení.

   Virtuální počítač by měl mít koncový bod definované pro SSH provoz, který si můžete prohlédnout v **koncové body** nebo  **[skupinu zabezpečení sítě](../../virtual-network/security-overview.md)**. Koncové body ve virtuálních počítačích, které byly vytvořeny pomocí Resource Manageru se ukládají na skupinu zabezpečení sítě. Ověřte, zda pravidla platí pro skupinu zabezpečení sítě a se odkazuje v podsíti.

Chcete-li ověřit připojení k síti, kontrolu nakonfigurované koncové body a zjistěte, zda se může připojit k virtuálnímu počítači pomocí jiného protokolu, jako je například HTTP nebo jiné služby.

Po provedení těchto kroků zkuste to znovu připojení SSH.

## <a name="find-the-source-of-the-issue"></a>Najít zdroj problému
Klient SSH na váš počítač se nemusí podařit připojit ke službě SSH pro virtuální počítač Azure kvůli problémům nebo nesprávné konfigurace v těchto oblastech:

* [Klientský počítač SSH](#source-1-ssh-client-computer)
* [Hraniční zařízení organizace](#source-2-organization-edge-device)
* [Koncový bod služby v cloudu a přístup k seznamu řízení (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Na základě Linux virtuálního počítače Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Zdroj 1: SSH klientského počítače
Omezit počítače jako zdroj selhání, ověřte, že mohl zajistit připojení SSH pro jinou místní, počítač se systémem Linux.

![Diagram, který označuje SSH klientské počítače komponenty](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Pokud připojení nezdaří, zkontrolujte následující problémy ve vašem počítači:

* Nastavení místní brány firewall, která blokuje příchozí nebo odchozí SSH provoz (port TCP 22)
* Místně nainstalován klientský software proxy serveru, který brání připojení SSH.
* Místně nainstalovaný software, který brání připojení SSH monitorování sítě
* Jiné typy zabezpečení softwaru, které monitorování provozu nebo povolí nebo zakáže určité typy přenosů

Pokud platí jedna z těchto podmínek, dočasně zakázat softwaru a zkuste připojení SSH do místního počítače a zjistěte důvod, proč připojení je blokován v počítači. Pak se pokuste se na správce sítě a opravte nastavení softwaru, které povolí připojení SSH.

Pokud používáte ověřování pomocí certifikátu, ověřte zda máte tato oprávnění ke složce .ssh v domovském adresáři:

* Chmod – 700 ~/.ssh
* Chmod – 644 ~/.ssh/\*.pub
* Chmod – 600 ~/.ssh/id_rsa (nebo všechny soubory, které mají vaši privátní klíče uložené v nich)
* Chmod – 644 ~/.ssh/known_hosts (obsahuje hostitele, na které jste připojeni prostřednictvím SSH.)

## <a name="source-2-organization-edge-device"></a>Zdroj 2: Hraniční zařízení organizace
Vaše organizace hraniční zařízení odstranit jako zdroj selhání, ověřte, že počítač přímo připojený k Internetu může vytvořit připojení SSH k svého virtuálního počítače Azure. Pokud přistupujete k virtuálnímu počítači přes síť site-to-site VPN nebo připojení k Azure ExpressRoute, přejděte k [zdroj 4: skupin zabezpečení sítě](#nsg).

![Diagram, který označuje organizace hraniční zařízení](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvořte nový virtuální počítač Azure v vlastní skupině prostředků nebo Cloudová služba a pomocí tohoto nového virtuálního počítače. Další informace najdete v tématu [vytvoření virtuálního počítače s Linuxem v Azure](quick-create-cli.md). Odstraňte skupinu prostředků nebo virtuálního počítače a cloudové služby, když jste hotovi s testování.

Pokud vytvoříte připojení SSH s počítači, který je přímo připojený k Internetu, zkontrolujte vaši organizaci hraniční zařízení pro:

* Vnitřní brána firewall, která blokuje provoz SSH s Internetem
* Proxy server, který brání připojení SSH.
* Zjišťování neoprávněných vniknutí nebo software spuštěný na zařízeních ve vaší hraniční síť, která brání připojení SSH monitorování sítě

Práce se správcem vaší sítě a opravte nastavení vaší organizace hraniční zařízení umožňující SSH přenos přes Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: Koncový bod služby Cloud a seznamu ACL
> [!NOTE]
> Tento zdroj se vztahuje pouze na virtuální počítače, které byly vytvořeny pomocí modelu nasazení classic. Pro virtuální počítače, které byly vytvořeny pomocí Resource Manageru, přejděte k [zdroje 4: skupin zabezpečení sítě](#nsg).

Chcete-li vyloučit jako zdroj selhání koncového bodu služby cloud a seznamu ACL, ověřte, že další virtuální počítač Azure ve stejné virtuální síti připojit pomocí protokolu SSH.

![Diagram, který označuje koncového bodu služby cloud a seznamu ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Pokud nemáte jiného virtuálního počítače ve stejné virtuální síti, můžete snadno vytvořit jednu. Další informace najdete v tématu [vytvoření virtuálního počítače s Linuxem v Azure pomocí rozhraní příkazového řádku](quick-create-cli.md). Odstraňte další virtuální počítač, když jste hotovi s testování.

Pokud vytvoříte připojení SSH s virtuálního počítače ve stejné virtuální síti, zkontrolujte následující oblasti:

* **Konfigurace koncového bodu pro komunikaci SSH na cílovém virtuálním počítači.** Port TCP, na kterém naslouchá službě SSH na virtuální počítač by měl odpovídat privátní port TCP koncového bodu. (Výchozí port je 22.) Zkontrolujte číslo portu SSH TCP na portálu Azure tak, že vyberete **virtuální počítače** > *název virtuálního počítače* > **nastavení**  >   **Koncové body**.
* **Seznam ACL pro koncový bod SSH provoz na cílový virtuální počítač.** Seznam ACL umožňuje určit povolené nebo zakázané příchozí přenosy z Internetu, na základě jeho zdrojové IP adresy. Nesprávně nakonfigurované seznamy ACL můžete zabránit příchozí provoz SSH ke koncovému bodu. Zkontrolujte vaše seznamy ACL zajistit, aby příchozí provoz z veřejné IP adresy vašeho proxy serveru nebo jiné hraniční server je povolen. Další informace najdete v tématu [o přístup k síti řízení seznamy ACL](../../virtual-network/virtual-networks-acl.md).

Chcete odstranit koncový bod jako zdroj problému, odeberte aktuální koncový bod, vytvořte jiný koncový bod a zadejte název SSH (port TCP 22 pro veřejné a privátní port číslo). Další informace najdete v tématu [nastavení koncových bodů na virtuálním počítači v Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Zdroje 4: Skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují mít členitější řízení toho povolené příchozí a odchozí přenosy. Můžete vytvořit pravidla, která span podsítě a cloudových služeb v virtuální sítě Azure. Zkontrolujte vaše pravidel skupiny zabezpečení sítě pro zajistit, že je povolené SSH provoz do a z Internetu.
Další informace najdete v tématu [o skupinách zabezpečení sítě](../../virtual-network/security-overview.md).

Můžete taky ověřit IP pro ověření konfigurace NSG. Další informace najdete v tématu [síť Azure Přehled monitorování](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Zdroj 5: Systémem Linux virtuálního počítače Azure
Poslední zdroj možné problémy je virtuální počítač Azure, sám sebe.

![Diagram, který označuje systémem Linux virtuálního počítače Azure](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Pokud jste tak ještě neučinili, postupujte podle pokynů [virtuálních počítačů založených na systému Linux heslo resetovat](reset-password.md).

Zkuste se znovu připojit z vašeho počítače. Pokud stále selže, toto jsou některé z možných problémů:

* V cílovém virtuálním počítači není spuštěna služba SSH.
* Služba SSH nenaslouchá na portu TCP 22. K testování, nainstalujte klient služby telnet v místním počítači a spusťte "telnet *cloudServiceName*. cloudapp.net 22". Tento krok určuje, pokud je virtuální počítač umožňuje příchozí a odchozí komunikace na koncový bod SSH.
* V místní bráně firewall na cílový virtuální počítač obsahuje pravidla, která brání příchozích a odchozích přenosů SSH.
* Zjišťování neoprávněných vniknutí nebo software, který běží na virtuálním počítači Azure monitorování sítě brání připojení SSH.

## <a name="additional-resources"></a>Další zdroje informací:
Další informace o odstraňování potíží s přístup k aplikaci najdete v tématu [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](troubleshoot-app-connection.md)
