---
title: Podrobné řešení potíží SSH pro virtuální počítač Azure | Dokumentace Microsoftu
description: Podrobnější SSH řešení potíží pro problémy s připojením k virtuálnímu počítači Azure
keywords: SSH připojení se odmítlo, ssh chyba, azure ssh, připojení SSH se nezdařilo
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a75a0e8c31934ff001674830bfe3d3d1c9e6beb1
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413308"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Podrobné kroky pro problémy s připojením k virtuálnímu počítači s Linuxem v Azure pro řešení potíží SSH
Existuje mnoho možných příčin, které nemusí být schopný připojit službu SSH na virtuálním počítači klienta SSH. Pokud jste postupovali podle prostřednictvím více [obecné SSH kroků pro řešení potíží](troubleshoot-ssh-connection.md), potřebujete-li dále řešit potíže s připojením. Tento článek vás provede podrobný postup řešení potíží k určení, kde se nedaří připojení SSH a o tom, jak problém vyřešit.

## <a name="take-preliminary-steps"></a>Předběžné kroky
Následující diagram znázorňuje komponenty, které se podílejí.

![Diagram zobrazující komponenty služby SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Následující kroky umožňují izolovat příčiny chyby a zjistit, řešení či alternativní řešení.

1. Zkontrolujte stav virtuálního počítače na portálu.
   V [webu Azure portal](https://portal.azure.com)vyberte **virtuálních počítačů** > *název virtuálního počítače*.

   V podokně Stav virtuálního počítače by se měla zobrazit **systémem**. Posuňte se dolů zobrazit poslední aktivitu pro výpočty, úložiště a síťové prostředky.

2. Vyberte **nastavení** prozkoumat koncové body IP adresy, skupiny zabezpečení sítě a další nastavení.

   Virtuální počítač by měl mít koncový bod definovaný pro provoz SSH, který si můžete prohlédnout v **koncové body** nebo  **[skupinu zabezpečení sítě](../../virtual-network/security-overview.md)**. Koncové body do virtuálních počítačů, které byly vytvořeny pomocí Resource Manageru jsou uložené ve skupině zabezpečení sítě. Ověřte, že pravidla skupiny zabezpečení sítě se použily a je odkazováno v podsíti.

Pokud chcete ověřit připojení k síti, zkontrolujte nakonfigurované koncové body a zobrazit, pokud se můžete připojit k virtuálnímu počítači pomocí jiného protokolu, jako je například HTTP, nebo jinou službu.

Po provedení těchto kroků zkuste to znovu připojení SSH.

## <a name="find-the-source-of-the-issue"></a>Najít zdroj problému
Klient SSH na počítači se nemusí podařit připojit ke službě SSH na virtuálním počítači Azure z důvodu problémů a nesprávné konfigurace v těchto oblastech:

* [SSH klientského počítače](#source-1-ssh-client-computer)
* [Organizace hraničního zařízení](#source-2-organization-edge-device)
* [Koncový bod služby v cloudu a přístup k seznamu ovládacího prvku (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Linuxovým virtuálním počítači Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Zdroj 1: SSH klientského počítače
K odstranění počítače jako zdroj chyby, ověřte, že může být připojení SSH k jiné v místním počítači se systémem Linux.

![Diagram zvýrazňuje komponenty počítače klienta SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Pokud se nepovede, zkontrolujte následující problémy ve vašem počítači:

* Nastavení místní brány firewall, která blokuje příchozí nebo odchozí provoz SSH (TCP 22)
* Místně nainstalovaný klientský software proxy server, který brání připojení SSH
* Místně nainstalovaný software, který brání připojení SSH pro monitorování sítě
* Jiné typy zabezpečovací programy, které monitorování provozu nebo povolit/zakázat konkrétní typy provozu

Pokud jedna z těchto podmínek použití, dočasně zakázat softwaru a zkuste připojení SSH ke v místním počítači a zjistěte důvod, proč blokuje připojení ve vašem počítači. Poté pracujte se správcem vaší sítě, chcete-li nastavení softwaru umožňující připojení SSH.

Pokud používáte ověřování pomocí certifikátu, ověřte, že máte tato oprávnění ke složce .ssh ve svém domovském adresáři:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (nebo jiné soubory, které mají vaše privátní klíče uložené v nich)
* Chmod 644 ~/.ssh/known_hosts (obsahuje hostitele, které jste se připojili přes protokol SSH)

## <a name="source-2-organization-edge-device"></a>Zdroj 2: Organizace hraničního zařízení
Chcete-li odstranit hraniční zařízení naprogramujete organizace jako zdroj chyby, ověřte, že počítače přímo připojené k Internetu může vytvořit připojení SSH k virtuálnímu počítači Azure. Při přístupu k virtuálnímu počítači přes síť site-to-site VPN nebo připojení Azure ExpressRoute, přejděte k [zdroj 4: skupiny zabezpečení sítě](#nsg).

![Diagram, který zvýrazňuje organizace hraničního zařízení](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, ve vlastní skupině prostředků vytvořte nový virtuální počítač Azure nebo Cloudová služba a použít tento nový virtuální počítač. Další informace najdete v tématu [vytvoření virtuálního počítače s Linuxem v Azure](../linux/quick-create-cli.md). Až budete hotovi s testování, odstraňte skupinu prostředků nebo virtuálního počítače a cloudové služby.

Pokud připojení SSH je možné vytvořit s počítačem, který je přímo připojený k Internetu, zkontrolujte vaši organizaci hraniční zařízení pro:

* Vnitřní brána firewall, která je blokuje provoz SSH s Internetem
* Proxy server, který brání připojení SSH
* Zjišťování neoprávněných vniknutí nebo softwaru, která běží na zařízeních ve vaší hraniční síti brání připojení SSH pro monitorování sítě

Práce se správcem vaší sítě, chcete-li nastavení hraničních zařízení organizace, které povolí provoz SSH s Internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: Koncový bod služby v cloudu a seznam ACL
> [!NOTE]
> Tento zdroj platí jenom pro virtuální počítače, které byly vytvořeny pomocí modelu nasazení classic. Pro virtuální počítače, které byly vytvořeny pomocí Resource Manageru, přejděte k [zdroje 4: skupiny zabezpečení sítě](#nsg).

Odstranit koncový bod služby v cloudu a seznam ACL jako zdroj chyby, ověřte, zda jiného virtuálního počítače Azure ve stejné virtuální síti můžete připojit pomocí protokolu SSH.

![Diagram, který zvýrazňuje koncový bod služby v cloudu a seznamu ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Pokud nemáte jiného virtuálního počítače ve stejné virtuální síti, můžete snadno vytvořit jednu. Další informace najdete v tématu [vytvoření virtuálního počítače s Linuxem v Azure pomocí rozhraní příkazového řádku](../linux/quick-create-cli.md). Jakmile budete hotovi s testování, odstranění navíc virtuálního počítače.

Pokud vytvoříte připojení SSH k virtuálnímu počítači ve stejné virtuální síti, zkontrolujte následující oblasti:

* **Konfigurace koncového bodu pro provoz SSH na cílovém virtuálním počítači.** Privátní port TCP koncového bodu musí odpovídat portu TCP, na kterém naslouchá službu SSH na virtuálním počítači. (Výchozí port je 22). Ověřte číslo portu SSH TCP na webu Azure Portal tak, že vyberete **virtuálních počítačů** > *název virtuálního počítače* > **nastavení**  >   **Koncové body**.
* **Seznam ACL pro koncový bod SSH provoz na cílovém virtuálním počítači.** Seznam ACL umožňuje určit povolené nebo zakázané příchozí přenosy z Internetu, na základě zdrojové IP adresy. Nesprávně nakonfigurované seznamy ACL můžete zabránit příchozí provoz SSH ke koncovému bodu. Zkontrolujte vaše seznamy ACL zajistit, že příchozí provoz z veřejné IP adresy vašeho proxy serveru nebo jiný server edge není povolen. Další informace najdete v tématu [o přístup k síti ovládací prvek seznamy ACL](../../virtual-network/virtual-networks-acl.md).

Chcete-li odstranit koncový bod jako zdroj problému, odstraňte aktuální koncový bod, vytvořte další koncový bod a zadejte název SSH (port TCP 22 pro veřejný i privátní port číslo). Další informace najdete v tématu [nastavení koncových bodů na virtuálním počítači v Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Zdroj 4: Skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují mít podrobnější kontrolu povoleného příchozího a odchozího provozu. Můžete vytvořit pravidla, která span podsítě a cloudové služby ve službě Azure virtual network. Zkontrolujte vaše pravidla skupin zabezpečení sítě k zajištění, že je povolený provoz SSH do a z Internetu.
Další informace najdete v tématu [o skupinách zabezpečení sítě](../../virtual-network/security-overview.md).

Ověření IP můžete také použít k ověření konfigurace skupiny zabezpečení sítě. Další informace najdete v tématu [Přehled monitorování Azure network](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Zdroj: 5: Linuxovým virtuálním počítači Azure
Poslední zdroje možných problémů je virtuální počítač Azure, samotného.

![Diagram, který zvýrazňuje linuxovým virtuálním počítači Azure](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Pokud jste tak ještě neučinili, postupujte podle pokynů [virtuálních počítačů založených na Linuxu heslo resetovat](../linux/reset-password.md).

Zkuste se znovu připojit z počítače. Pokud stále selže, následují některé z možných problémech:

* Na cílovém virtuálním počítači není spuštěna služba SSH.
* Služba SSH nenaslouchá na portu TCP 22. Chcete-li otestovat, nainstalujte klient služby telnet v místním počítači a spusťte "telnet *cloudServiceName*. cloudapp.net 22". Tento krok určuje, pokud je virtuální počítač umožňuje příchozí a odchozí komunikaci na koncový bod SSH.
* Místní brány firewall na cílovém virtuálním počítači má pravidla, která brání příchozí nebo odchozí provoz SSH.
* Zjišťování neoprávněných vniknutí nebo software, který běží na virtuálním počítači Azure pro monitorování sítě brání připojení SSH.

## <a name="additional-resources"></a>Další zdroje informací:
Další informace o řešení potíží s přístupu k aplikacím, najdete v části [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md)
