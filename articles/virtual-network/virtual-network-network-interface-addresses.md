---
title: Konfigurace IP adres pro síťové rozhraní Azure | Microsoft Docs
description: Naučte se přidávat, měnit a odebírat privátní a veřejné IP adresy pro síťové rozhraní.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 3fd0cfe644ad78059e25d5386cd1a01f56ad9fba
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216984"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Přidání, změna nebo odebrání IP adres pro síťové rozhraní Azure

Naučte se přidávat, měnit a odebírat veřejné a privátní IP adresy pro síťové rozhraní. Privátní IP adresy přiřazené k síťovému rozhraní umožňují virtuálnímu počítači komunikovat s ostatními prostředky ve virtuální síti Azure a propojených sítích. Privátní IP adresa taky umožňuje odchozí komunikaci s Internetem pomocí nepředvídatelné IP adresy. [Veřejná IP adresa](virtual-network-public-ip-address.md) přiřazená síťovému rozhraní umožňuje příchozí komunikaci s virtuálním počítačem z Internetu. Adresa taky umožňuje odchozí komunikaci z virtuálního počítače s Internetem pomocí předvídatelné IP adresy. Podrobnosti najdete v tématu [Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pokud potřebujete vytvořit, změnit nebo odstranit síťové rozhraní, přečtěte si článek [Správa síťového rozhraní](virtual-network-network-interface.md) . Pokud potřebujete přidat síťová rozhraní nebo odebrat síťová rozhraní z virtuálního počítače, přečtěte si článek [Přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md) .

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních síťových rozhraní](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Přidat IP adresy

K síťovému rozhraní můžete podle potřeby přidat tolik [privátních](#private) a [veřejných](#public) [IPv4](#ipv4) adres, a to v rámci omezení uvedených v článku [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Můžete přidat privátní adresu IPv6 do jedné [sekundární konfigurace IP](#secondary) adresy (Pokud pro existující síťové rozhraní neexistují žádné existující sekundární konfigurace protokolu IP). Každé síťové rozhraní může mít maximálně jednu privátní adresu IPv6. Volitelně můžete přidat veřejnou IPv6 adresu do konfigurace síťového rozhraní IPv6. Podrobnosti o používání adres IPv6 najdete v tématu [protokol IPv6](#ipv6) .

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. V seznamu vyberte síťové rozhraní, ze kterého chcete přidat adresu IPv4.
3. V části **Nastavení** vyberte **Konfigurace protokolu IP**.
4. V části **Konfigurace protokolu IP** vyberte **+ Přidat**.
5. Zadejte následující příkaz a pak vyberte **OK**:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Name|Yes|Musí být pro síťové rozhraní jedinečné.|
   |Typ|Yes|Vzhledem k tomu, že přidáváte konfiguraci IP adres do stávajícího síťového rozhraní a každé síťové rozhraní musí mít konfiguraci [primárního](#primary) protokolu IP, je vaše jediná možnost **sekundární**.|
   |Metoda přiřazování privátních IP adres|Yes|[**Dynamické**](#dynamic): Azure přiřadí další dostupnou adresu pro rozsah adres podsítě, ve kterém je síťové rozhraní nasazené. [**Statické**](#static): přiřadíte nepoužitou adresu rozsahu adres podsítě, ve kterém je síťové rozhraní nasazeno.|
   |Veřejná IP adresa|No|**Zakázáno:** K této konfiguraci protokolu IP není aktuálně přidružen žádný prostředek veřejné IP adresy. **Povoleno:** Vyberte existující veřejnou IP adresu IPv4 nebo vytvořte novou. Pokud se chcete dozvědět, jak vytvořit veřejnou IP adresu, přečtěte si článek o [veřejných IP adresách](virtual-network-public-ip-address.md#create-a-public-ip-address) .|
6. Ručním přidáním sekundárních privátních IP adres do operačního systému virtuálního počítače dokončete pokyny v článku [přiřazení více IP adres k operačním systémům virtuálních počítačů](virtual-network-multiple-ip-addresses-portal.md#os-config) . Zvláštní informace najdete v tématu [soukromé](#private) IP adresy, které se týkají ručního přidávání IP adres do operačního systému virtuálního počítače. Do operačního systému virtuálního počítače nepřidávejte žádné veřejné IP adresy.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Změnit nastavení IP adresy

Možná budete muset změnit metodu přiřazení adresy IPv4, změnit statickou IPv4 adresu nebo změnit veřejnou IP adresu přiřazenou k síťovému rozhraní. Pokud měníte privátní IPv4 adresu sekundární konfigurace IP přidružené ke sekundárnímu síťovému rozhraní ve virtuálním počítači (Další informace o [primárních a sekundárních síťových rozhraních](virtual-network-network-interface-vm.md)), umístěte virtuální počítač do stavu Zastaveno (přidělení zrušeno) před provedením následujících kroků:

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. V seznamu vyberte síťové rozhraní, pro které chcete zobrazit nebo změnit nastavení IP adresy.
3. V části **Nastavení** vyberte **Konfigurace protokolu IP**.
4. V seznamu vyberte konfiguraci protokolu IP, kterou chcete upravit.
5. Podle potřeby změňte nastavení pomocí informací o nastavení v kroku 5 [Přidání konfigurace protokolu IP](#add-ip-addresses).
6. Vyberte **Uložit**.

>[!NOTE]
>Má-li primární síťové rozhraní více konfigurací protokolu IP a měníte privátní IP adresu primární konfigurace protokolu IP, je nutné ručně znovu přiřadit primární a sekundární IP adresy k síťovému rozhraní v rámci systému Windows (není vyžadováno pro Linux). Postup ručního přiřazení IP adres k síťovému rozhraní v rámci operačního systému najdete v tématu [přiřazení více IP adres k virtuálním počítačům](virtual-network-multiple-ip-addresses-portal.md#os-config). Speciální informace před ručním přidáním IP adres do operačního systému virtuálního počítače najdete v tématu [privátní](#private) IP adresy. Do operačního systému virtuálního počítače nepřidávejte žádné veřejné IP adresy.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Odebrat IP adresy

[Privátní](#private) a [veřejné](#public) IP adresy můžete ze síťového rozhraní odebrat, ale k síťovému rozhraní je potřeba mít přiřazenou aspoň jednu privátní IPv4 adresu.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. Vyberte síťové rozhraní, které chcete ze seznamu odebrat IP adresy.
3. V části **Nastavení** vyberte **Konfigurace protokolu IP**.
4. Klikněte pravým tlačítkem na konfiguraci [sekundární](#secondary) IP adresy ( [primární](#primary) konfiguraci nemůžete odstranit), vyberte **Odstranit** a pak vyberte **Ano** a potvrďte odstranění. Pokud má konfigurace přiřazen prostředek veřejné IP adresy, prostředek se zrušení přidružení z konfigurace protokolu IP, ale prostředek se neodstraní.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic IP-config DELETE](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfigurace protokolu IP

[Privátní](#private) a (volitelně) [veřejné](#public) IP adresy jsou přiřazeny k jedné nebo více konfiguracím protokolu IP, které jsou přiřazeny k síťovému rozhraní. Existují dva typy konfigurací protokolu IP:

### <a name="primary"></a>Primární

Každému síťovému rozhraní je přiřazena jedna primární konfigurace IP adresa. Konfigurace primární IP adresy:

- Má přiřazenou [privátní](#private) [IPv4](#ipv4) adresu. Privátní adresu [IPv6](#ipv6) nelze přiřadit k primární konfiguraci protokolu IP.
- Může mít také přiřazenou [veřejnou](#public) adresu IPv4. Veřejné adrese IPv6 nelze přiřadit k primární konfiguraci protokolu IPv4. 

### <a name="secondary"></a>Sekundární

Kromě konfigurace primárního protokolu IP může být k síťovému rozhraní přiřazeno nula nebo více sekundárních konfigurací IP adres. Sekundární konfigurace IP adresy:

- Musí mít přiřazenou privátní adresu protokolu IPv4 nebo IPv6. Pokud je adresa IPv6, může být v síťovém rozhraní jenom jedna sekundární konfigurace IP adresy. Pokud je adresa IPv4, síťové rozhraní může mít přiřazeno několik sekundárních konfigurací IP adres. Další informace o tom, kolik privátních a veřejných adres IPv4 je možné přiřadit k síťovému rozhraní, najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Může mít taky přiřazenou veřejnou adresu protokolu IPv4 nebo IPv6. Přiřazení více IPv4 adres k síťovému rozhraní je užitečné ve scénářích, jako jsou:
  - Hostování více webů nebo služeb s různými IP adresami a certifikáty TLS/SSL na jednom serveru.
  - Virtuální počítač, který slouží jako síťové virtuální zařízení, jako je brána firewall nebo nástroj pro vyrovnávání zatížení.
  - Možnost přidat kteroukoli z privátních IPv4 adres pro všechna síťová rozhraní do fondu Azure Load Balancer back-end. V minulosti se do fondu back-endu mohla přidat jenom primární adresa IPv4 pro primární síťové rozhraní. Další informace o tom, jak vyrovnávat zatížení více konfigurací IPv4, najdete v článku věnovaném [Vyrovnávání zatížení více konfigurací protokolu IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 
  - Možnost vyrovnávat zatížení jedné IPv6 adresy přiřazené síťovému rozhraní. Další informace o tom, jak vyrovnávat zatížení na privátní adrese IPv6, najdete v článku o [adresách IPv6 pro vyrovnávání zatížení](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

## <a name="address-types"></a>Typy adres

[Konfiguraci protokolu IP](#ip-configurations)můžete přiřadit následující typy IP adres:

### <a name="private"></a>Privátní

Privátní adresy [IPv4](#ipv4) nebo IPv6 umožňují virtuálnímu počítači komunikovat s ostatními prostředky ve virtuální síti nebo v jiných propojených sítích. 

Ve výchozím nastavení servery DHCP Azure přiřadí privátní IPv4 adresu pro [primární konfiguraci IP](#primary) rozhraní sítě Azure síťové rozhraní v operačním systému virtuálního počítače. V případě potřeby byste nikdy neměli ručně nastavit IP adresu síťového rozhraní v operačním systému virtuálního počítače.

> [!WARNING]
> Pokud se adresa IPv4 nastavená jako primární IP adresa síťového rozhraní v operačním systému virtuálního počítače liší od privátní adresy IPv4, která je přiřazená k primární konfiguraci IP rozhraní primárního síťového rozhraní připojeného k virtuálnímu počítači v Azure, ztratíte připojení k virtuálnímu počítači.

Existují situace, kdy je nutné ručně nastavit IP adresu síťového rozhraní v operačním systému virtuálního počítače. Například při přidávání více IP adres do virtuálního počítače Azure musíte ručně nastavit primární a sekundární IP adresu operačního systému Windows. U virtuálních počítačů se systémem Linux můžete pouze ručně nastavit sekundární IP adresy. Podrobnosti najdete v tématu [Přidání IP adres do operačního systému virtuálního počítače](virtual-network-multiple-ip-addresses-portal.md#os-config) . Pokud někdy potřebujete změnit adresu přiřazenou ke konfiguraci protokolu IP, doporučujeme vám:

1. Ujistěte se, že virtuální počítač přijímá adresu ze serverů DHCP Azure. Jakmile budete mít, změňte přiřazení IP adresy zpátky na DHCP v operačním systému a restartujte virtuální počítač.
2. Zastavit (zrušit přidělení) virtuálnímu počítači.
3. Změňte IP adresu pro konfiguraci IP adresy v rámci Azure.
4. Spusťte virtuální počítač.
5. [Ručně nakonfigurujte](virtual-network-multiple-ip-addresses-portal.md#os-config) sekundární IP adresy v operačním systému (a také v primární IP adrese ve Windows) tak, aby odpovídaly vašemu nastavení v Azure.

Pomocí předchozích kroků zůstane privátní IP adresa přiřazená síťovému rozhraní v rámci Azure a v operačním systému virtuálního počítače stejná. Pokud chcete sledovat, které virtuální počítače v rámci vašeho předplatného jste ručně nastavili IP adresy v operačním systému pro, zvažte přidání [značky](../azure-resource-manager/management/tag-resources.md) Azure do virtuálních počítačů. Můžete například použít "přiřazení IP adresy: statické". Tímto způsobem můžete snadno najít virtuální počítače v rámci předplatného, které jste ručně nastavili IP adresu v rámci operačního systému.

Kromě toho, že virtuální počítač může komunikovat s jinými prostředky v rámci stejné nebo připojené virtuální sítě, privátní IP adresa taky umožňuje virtuálnímu počítači komunikovat odchozí připojení k Internetu. Odchozí připojení jsou zdrojová síťová adresa přeložená v Azure na nepředvídatelné veřejné IP adresy. Další informace o odchozím připojení k Internetu pro Azure najdete v článku věnovaném [odchozímu připojení k Internetu Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Příchozí připojení k privátní IP adrese virtuálního počítače nemůžete z Internetu komunikovat. Pokud vaše odchozí připojení vyžadují předvídatelné veřejné IP adresy, přidružte prostředek veřejné IP adresy k síťovému rozhraní.

### <a name="public"></a>Veřejná

Veřejné IP adresy přiřazené prostřednictvím prostředku veřejné IP adresy umožňují příchozí připojení k virtuálnímu počítači z Internetu. Odchozí připojení k Internetu používají předvídatelné IP adresy. Podrobnosti najdete [v tématu Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Veřejnou IP adresu můžete přiřadit konfiguraci protokolu IP, ale není to nutné. Pokud nepřiřazujete veřejnou IP adresu k virtuálnímu počítači tím, že přidružíte prostředek veřejné IP adresy, může virtuální počítač nadále komunikovat odchozí připojení k Internetu. V takovém případě je privátní IP adresa Zdrojová síťová adresa přeložená Azure na nepředvídatelné veřejné IP adresy. Další informace o prostředcích veřejných IP adres najdete v tématu [prostředek veřejné IP adresy](virtual-network-public-ip-address.md).

Existují omezení pro počet privátních a veřejných IP adres, které můžete přiřadit k síťovému rozhraní. Podrobnosti najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

> [!NOTE]
> Azure překládá privátní IP adresu virtuálního počítače na veřejnou IP adresu. V důsledku toho operační systém virtuálního počítače neví, že je k ní přiřazená žádná veřejná IP adresa, takže nemusíte nikdy ručně přiřazovat veřejnou IP adresu v operačním systému.

## <a name="assignment-methods"></a>Metody přiřazení

Veřejné a privátní IP adresy se přiřazují pomocí jedné z následujících metod přiřazení:

### <a name="dynamic"></a>Dynamická

Ve výchozím nastavení jsou přiřazovány dynamické privátní adresy IPv4 a IPv6 (volitelně).

- **Pouze veřejný**: Azure přiřadí adresu z rozsahu, který je jedinečný pro každou oblast Azure. Informace o tom, které rozsahy jsou přiřazeny ke každé oblasti, najdete v tématu [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Adresa se může změnit, když je virtuální počítač zastavený (přidělení zrušeno), a pak se znovu spustí. Veřejné adrese IPv6 nelze přiřadit ke konfiguraci protokolu IP pomocí metody přiřazení.
- **Pouze soukromý**: Azure si vyhrazuje první čtyři adresy v každém rozsahu adres podsítě a adresy nepřiřazují. Azure přiřadí prostředku další dostupnou adresu z rozsahu adres podsítě. Pokud je například rozsah adres podsítě 10.0.0.0/16 a adresy 10.0.0.4-10.0.0.14 jsou již přiřazeny (. 0-.3 jsou rezervované), Azure přiřadí 10.0.0.15 k prostředku. Dynamická metoda přidělování je výchozí metoda. Jakmile jsou dynamické IP adresy přiřazené, uvolní se pouze v případě odstranění síťového rozhraní, jeho přiřazení k jiné podsíti ve stejné virtuální síti nebo změně metody přidělování na statickou a zadání jiné IP adresy. Když změníte metodu přidělování z dynamické na statickou, Azure ve výchozím nastavení jako statickou IP adresu přiřadí dříve dynamicky přiřazenou adresu. 

### <a name="static"></a>Static

K konfiguraci protokolu IP můžete (volitelně) přiřadit veřejnou nebo soukromou statickou adresu IPv4 nebo IPv6. Další informace o tom, jak Azure přiřazuje statické veřejné adresy IPv4, najdete v tématu [veřejné IP adresy](virtual-network-public-ip-address.md).

- **Pouze veřejný**: Azure přiřadí adresu z rozsahu, který je jedinečný pro každou oblast Azure. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064). Adresa se nemění, dokud se neodstraní prostředek veřejné IP adresy, ke kterému je přiřazený, nebo se metoda přiřazení změní na dynamickou. Pokud je prostředek veřejné IP adresy přidružený ke konfiguraci protokolu IP, musí být před změnou metody přiřazení zrušení přidružení z konfigurace protokolu IP.
- **Pouze soukromé**: vyberete a přiřadíte adresu z rozsahu adres podsítě. Adresa, kterou přiřadíte, může být jakákoli adresa v rozsahu adres podsítě kromě prvních čtyř adres, která aktuálně není přiřazená k žádnému jinému prostředku v této podsíti. Statické adresy se uvolní pouze v případě odstranění síťového rozhraní. Pokud změníte metodu přidělování na statickou, Azure dynamicky přiřadí dříve přiřazenou dynamickou IP adresu jako statickou adresu, a to i v případě, že tato adresa není další dostupnou adresou v rozsahu adres podsítě. Adresa se změní také v případě přiřazení síťového rozhraní k jiné podsíti ve stejné virtuální síti. Pokud však chcete síťové rozhraní přiřadit k jiné podsíti, musíte nejprve změnit metodu přidělování ze statické na dynamickou. Jakmile přiřadíte síťové rozhraní k jiné podsíti, můžete metodu přidělování změnit zpět na statickou a přiřadit IP adresu z rozsahu adres nové podsítě.

## <a name="ip-address-versions"></a>Verze IP adres

Při přiřazování adres můžete zadat následující verze:

### <a name="ipv4"></a>IPv4

Každé síťové rozhraní musí mít jednu [primární](#primary) konfiguraci IP adresy s přiřazenou [privátní](#private) adresou [IPv4](#ipv4) . Můžete přidat jednu nebo více [sekundárních](#secondary) konfigurací IP adres, které mají privátní IPv4, a (volitelně) [veřejnou](#public) IP adresu IPv4.

### <a name="ipv6"></a>IPv6

Jedné konfiguraci sekundární IP adresy síťového rozhraní můžete přiřadit nulovou nebo jednu privátní adresu [IPv6](#ipv6) . Síťové rozhraní nemůže mít žádnou existující sekundární konfiguraci protokolu IP. Každé síťové rozhraní může mít maximálně jednu privátní adresu IPv6. Volitelně můžete přidat veřejnou IPv6 adresu do konfigurace síťového rozhraní IPv6.

> [!NOTE]
> I když můžete vytvořit síťové rozhraní s adresou IPv6 pomocí portálu, nemůžete přidat existující síťové rozhraní k novému nebo existujícímu virtuálnímu počítači pomocí portálu. Pomocí PowerShellu nebo rozhraní příkazového řádku Azure vytvořte síťové rozhraní s privátní adresou IPv6 a pak připojte síťové rozhraní při vytváření virtuálního počítače. Nemůžete připojit síťové rozhraní s přiřazenou privátní adresou IPv6 k existujícímu virtuálnímu počítači. Privátní adresu IPv6 nelze přidat k konfiguraci protokolu IP pro jakékoli síťové rozhraní připojené k virtuálnímu počítači pomocí nástrojů (portálu, rozhraní příkazového řádku nebo PowerShellu).

K primární nebo sekundární konfiguraci IP adres nemůžete přiřadit veřejnou adresu IPv6.

## <a name="skus"></a>Skladové položky

Veřejná IP adresa se vytvoří se základní nebo standardní SKU. Další informace o rozdílech SKU najdete v tématu [Správa veřejných IP adres](virtual-network-public-ip-address.md).

> [!NOTE]
> Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](./network-security-groups-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.

## <a name="next-steps"></a>Další kroky
Pokud chcete vytvořit virtuální počítač s různými konfiguracemi IP adres, přečtěte si následující články:

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými rozhraními|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s privátní adresou IPv6 (za Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager šablona](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|