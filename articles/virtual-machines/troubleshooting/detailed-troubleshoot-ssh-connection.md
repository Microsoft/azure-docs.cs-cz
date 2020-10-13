---
title: Podrobné řešení potíží s SSH pro virtuální počítač Azure | Microsoft Docs
description: Podrobnější postup řešení potíží se zabezpečením SSH pro problémy s připojením k virtuálnímu počítači Azure
keywords: odmítnuté připojení SSH, chyba SSH, Azure SSH, připojení SSH selhalo.
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
ms.openlocfilehash: c117f9697299b94a54d9184093e65c56822b8bd2
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975667"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Podrobný postup řešení potíží s SSH při připojování k virtuálnímu počítači s Linuxem v Azure
Je možné, že klient SSH nebude moci kontaktovat službu SSH na virtuálním počítači. Pokud jste provedli obecnější [kroky při řešení potíží](troubleshoot-ssh-connection.md)se službou SSH, budete muset vyřešit potíže s připojením. Tento článek vás provede podrobnými kroky pro řešení potíží, které určují, kde se připojení SSH nedaří a jak ho vyřešit.

## <a name="take-preliminary-steps"></a>Provedení předběžných kroků
Následující diagram znázorňuje komponenty, které jsou součástí.

![Diagram, který zobrazuje součásti služby SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Následující kroky vám pomůžou izolovat zdroj selhání a zjistit řešení nebo řešení.

1. Ověřte stav virtuálního počítače na portálu.
   V [Azure Portal](https://portal.azure.com)vyberte název virtuálního počítače **virtuálních počítačů**  >  *VM name*.

   V podokně stavu virtuálního počítače by se měla zobrazit **spuštěná**. Přejděte dolů a zobrazte si poslední aktivitu pro výpočetní prostředky, úložiště a síťové prostředky.

2. Vyberte **Nastavení** pro prošetření koncových bodů, IP adres, skupin zabezpečení sítě a dalších nastavení.

   Virtuální počítač by měl mít definovaný koncový bod pro provoz SSH, který můžete zobrazit v **koncových bodech** nebo **[skupině zabezpečení sítě](../../virtual-network/network-security-groups-overview.md)**. Koncové body virtuálních počítačů, které byly vytvořeny pomocí Správce prostředků, jsou uloženy ve skupině zabezpečení sítě. Ověřte, zda byla pravidla použita pro skupinu zabezpečení sítě a zda jsou odkazována v podsíti.

Pokud chcete ověřit připojení k síti, zkontrolujte nakonfigurované koncové body a podívejte se, jestli se k virtuálnímu počítači můžete připojit prostřednictvím jiného protokolu, například HTTP nebo jiné služby.

Po provedení těchto kroků zkuste připojení SSH znovu.

## <a name="find-the-source-of-the-issue"></a>Najděte zdroj problému.
Klient SSH v počítači se nemusí podařit připojit ke službě SSH na VIRTUÁLNÍm počítači Azure z důvodu problémů nebo chybných konfigurací v následujících oblastech:

* [Klientský počítač SSH](#source-1-ssh-client-computer)
* [Hraniční zařízení organizace](#source-2-organization-edge-device)
* [Koncový bod cloudové služby a seznam řízení přístupu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Skupiny zabezpečení sítě](#source-4-network-security-groups)
* [Virtuální počítač Azure se systémem Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Zdroj 1: klientský počítač SSH
Chcete-li odstranit počítač jako zdroj chyby, ověřte, zda může vytvořit připojení SSH k jinému místnímu počítači se systémem Linux.

![Diagram, který zvýrazňuje součásti klientského počítače SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Pokud se připojení nepovede, vyhledejte na svém počítači tyto problémy:

* Nastavení místní brány firewall blokující příchozí nebo odchozí přenosy SSH (TCP 22)
* Místně instalovaný klientský proxy software, který brání připojení SSH
* Místně nainstalovaný software pro monitorování sítě, který brání připojení SSH
* Další typy zabezpečovacího softwaru, které monitorují provoz nebo povolují nebo zakazují konkrétní typy provozu

Pokud platí jedna z těchto podmínek, dočasně zakažte software a zkuste připojení SSH k místnímu počítači, abyste zjistili důvod, proč je připojení v počítači zablokované. Potom Spolupracujte se správcem sítě a opravte nastavení softwaru, aby se povolilo připojení SSH.

Pokud používáte ověřování pomocí certifikátu, ověřte, že máte tato oprávnění ke složce. ssh v domovském adresáři:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/ \* . pub
* Chmod 600 ~/.ssh/id_rsa (nebo všechny jiné soubory, které mají v nich uložené soukromé klíče)
* Chmod 644 ~/.ssh/known_hosts (obsahuje hostitele, ke kterým jste se připojili přes SSH)

## <a name="source-2-organization-edge-device"></a>Zdrojový 2: hraniční zařízení organizace
Pokud chcete odstranit hraniční zařízení organizace jako zdroj chyby, ověřte, že počítač, který je přímo připojený k Internetu, může vytvořit připojení SSH k VIRTUÁLNÍmu počítači Azure. Pokud k virtuálnímu počítači přistupujete přes síť VPN typu Site-to-site nebo připojení Azure ExpressRoute, přejděte na [zdroj 4: skupiny zabezpečení sítě](#nsg).

![Diagram, který zvýrazňuje hraniční zařízení organizace](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Pokud nemáte počítač, který je přímo připojený k Internetu, vytvořte nový virtuální počítač Azure ve vlastní skupině prostředků nebo v cloudové službě a použijte tento nový virtuální počítač. Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux v Azure](../linux/quick-create-cli.md). Až budete s testováním hotovi, odstraňte skupinu prostředků nebo virtuální počítač a cloudovou službu.

Pokud můžete vytvořit připojení SSH k počítači, který je přímo připojený k Internetu, podívejte se na hraniční zařízení organizace pro:

* Interní brána firewall, která blokuje provoz SSH s internetem
* Proxy server, který brání připojení SSH
* Zjišťování neoprávněných vniknutí nebo software pro monitorování sítě běžící na zařízeních ve vaší hraniční síti, která brání připojení SSH

Spolupracujte se správcem sítě a opravte nastavení hraničních zařízení organizace, aby se povolil přenos přes protokol SSH s internetem.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Zdroj 3: koncový bod cloudové služby a seznam ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Tento zdroj se vztahuje jenom na virtuální počítače, které byly vytvořené pomocí modelu nasazení Classic. Pro virtuální počítače, které byly vytvořené pomocí Správce prostředků přejděte na [zdroj 4: skupiny zabezpečení sítě](#nsg).

Pokud chcete vyloučit koncový bod cloudové služby a seznam ACL jako zdroj chyby, ověřte, že se k připojení pomocí SSH může připojit jiný virtuální počítač Azure ve stejné virtuální síti.

![Diagram, který zvýrazňuje koncový bod cloudové služby a seznam ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Pokud nemáte jiný virtuální počítač ve stejné virtuální síti, můžete ho snadno vytvořit. Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux v Azure pomocí rozhraní](../linux/quick-create-cli.md)příkazového řádku. Po dokončení testování odstraňte virtuální počítač navíc.

Pokud můžete vytvořit připojení SSH s virtuálním počítačem ve stejné virtuální síti, podívejte se do těchto oblastí:

* **Konfigurace koncového bodu pro provoz SSH na cílovém virtuálním počítači.** Privátní port TCP koncového bodu by měl odpovídat portu TCP, na kterém naslouchá služba SSH na virtuálním počítači. (Výchozí port je 22). Ověřte číslo portu TCP SSH v Azure Portal tak, že vyberete **možnost virtuální počítače**  >  *název*virtuálního počítače  >  **Settings**  >  **koncové body**.
* **Seznam ACL pro koncový bod provozu SSH na cílovém virtuálním počítači.** Seznam ACL umožňuje zadat povolený nebo zakázaný příchozí provoz z Internetu na základě jeho zdrojové IP adresy. Nesprávně nakonfigurované seznamy řízení přístupu (ACL) můžou zabránit příchozímu provozu SSH do koncového bodu. Zkontrolujte seznamy ACL, abyste měli jistotu, že příchozí provoz z veřejných IP adres vašeho proxy serveru nebo jiného hraničního serveru je povolený. Další informace najdete v tématu [informace o seznamech řízení přístupu k síti (ACL)](/previous-versions/azure/virtual-network/virtual-networks-acl).

Chcete-li odstranit koncový bod jako zdroj problému, odeberte aktuální koncový bod, vytvořte jiný koncový bod a zadejte název SSH (port TCP 22 pro číslo veřejného a soukromého portu). Další informace najdete v tématu [nastavení koncových bodů na virtuálním počítači v Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Zdroj 4: skupiny zabezpečení sítě
Skupiny zabezpečení sítě umožňují podrobnější kontrolu nad povoleným příchozím a odchozím provozem. Můžete vytvořit pravidla, která budou zahrnovat podsítě a cloudové služby ve službě Azure Virtual Network. Zkontrolujte pravidla skupiny zabezpečení sítě, abyste měli jistotu, že je povolený provoz SSH do a z Internetu.
Další informace najdete v tématu [informace o skupinách zabezpečení sítě](../../virtual-network/network-security-groups-overview.md).

K ověření konfigurace NSG můžete také použít ověřování pomocí protokolu IP. Další informace najdete v tématu [Přehled monitorování sítě Azure](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Zdroj 5: virtuální počítač Azure založený na Linux
Poslední zdrojem možných problémů je samotný virtuální počítač Azure.

![Diagram, který zvýrazňuje virtuální počítač Azure se systémem Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Pokud jste to ještě neudělali, postupujte podle pokynů [pro resetování virtuálních počítačů se systémem Linux pomocí hesla](./reset-password.md).

Zkuste z počítače znovu připojit. Pokud se to pořád nepovede, jsou zde některé z možných problémů:

* Služba SSH není na cílovém virtuálním počítači spuštěná.
* Služba SSH nenaslouchá na portu TCP 22. Chcete-li provést test, nainstalujte klienta služby Telnet v místním počítači a spusťte příkaz "Telnet *cloudServiceName*. cloudapp.NET 22". Tento krok určuje, jestli virtuální počítač umožňuje příchozí a odchozí komunikaci s koncovým bodem SSH.
* Místní brána firewall na cílovém virtuálním počítači obsahuje pravidla, která zabraňují příchozímu nebo odchozímu provozu SSH.
* Zjišťování neoprávněných vniknutí nebo software pro monitorování sítě, který běží na virtuálním počítači Azure, brání připojení SSH.

## <a name="additional-resources"></a>Další zdroje informací
Další informace o řešení potíží s přístupem k aplikacím najdete v tématu [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](./troubleshoot-app-connection.md) .