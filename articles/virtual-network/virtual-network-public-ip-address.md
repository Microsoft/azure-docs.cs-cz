---
title: Vytvořit, změnit nebo odstranit veřejnou IP adresu Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit, změnit nebo odstranit veřejnou IP adresu.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 3e0b664c14cba845c43e3b0202de5fe033bf2186
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434627"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Vytvořit, změnit nebo odstranit veřejnou IP adresu

Další informace o veřejné IP adresy a jak vytvořit, změnit a toku nějaký tok odstranit. Veřejná IP adresa je prostředek s vlastní konfigurovatelné nastavení. Veřejnou IP adresu přiřadit prostředku Azure, která podporuje veřejné IP adresy umožňuje:
- Příchozí komunikace ze sítě Internet k prostředku, jako jsou Azure Virtual Machines (VM), služby Azure Application Gateway, nástroje pro vyrovnávání zatížení Azure, Azure VPN Gateway a dalších. Můžete dál komunikovat s některými prostředky, jako jsou virtuální počítače z Internetu, pokud virtuální počítač nemá veřejnou IP adresu přiřazenou jako virtuální počítač je součástí fondu back-end nástroje pro vyrovnávání zatížení a nástroje pro vyrovnávání zatížení je přiřazena veřejná IP adresa. Pokud chcete zjistit, jestli prostředek pro konkrétní službu Azure je možné přiřadit veřejnou IP adresu, nebo určuje, zda jej lze komunikovat s prostřednictvím veřejné IP adresy z různých prostředků Azure, najdete v dokumentaci pro službu. 
- Odchozí připojení k Internetu pomocí předvídatelné IP adresy. Virtuální počítač může například komunikovat odchozí k Internetu bez veřejné IP adresy přiřazené k ní, ale její adresa je adresa sítě přeložit pomocí Azure na veřejnou adresu nepředvídatelné ve výchozím nastavení. Veřejnou IP adresu přiřadit prostředku umožňuje vědět, které IP adresa se používá pro odchozí připojení. I když je předvídatelné, můžete změnit na adresu, v závislosti na zvolené metodě přiřazení. Další informace najdete v tématu [vytvoření veřejné IP adresy](#create-a-public-ip-address). Další informace o odchozích připojení z prostředků Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Před zahájením

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

Veřejné IP adresy účtovat nominální poplatek. Chcete-li zobrazit ceny, přečtěte si [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. 

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. V horní, v levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
2. Zadejte *veřejnou ip adresu* v *Hledat na Marketplace* pole. Když **veřejnou IP adresu** se zobrazí ve výsledcích hledání vyberte ji.
3. V části **veřejnou IP adresu**vyberte **vytvořit**.
4. Zadejte nebo vyberte hodnoty pro následující nastavení v části **vytvoření veřejné IP adresy**a pak vyberte **vytvořit**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Název|Ano|Název musí být jedinečný v rámci skupiny prostředků, kterou vyberete.|
    |Skladová jednotka (SKU)|Ano|Všechny veřejné IP adresy vytvořené před zavedením skladových položek jsou **základní** SKU veřejné IP adresy.  Skladovou Položku nelze změnit po vytvoření veřejné IP adresy. Samostatný virtuální počítač, virtuální počítače v rámci skupinu dostupnosti nebo škálovací sady virtuálních počítačů můžete použít základní nebo standardní SKU.  Kombinování skladové položky virtuálních počítačů v rámci skupiny dostupnosti nebo škálovací sady se nepovoluje. **Základní** SKU: Pokud vytvoříte veřejnou IP adresu v oblasti, která podporují zóny dostupnosti **zóna dostupnosti** nastavená na *žádný* ve výchozím nastavení. Můžete vybrat zóně dostupnosti zaručit konkrétní zóně pro veřejnou IP adresu. **Standardní** SKU: Může být přidružený k virtuálnímu počítači nebo front-endu nástroje pro vyrovnávání zatížení veřejnou IP adresu standardních SKU. Pokud vytváříte veřejnou IP adresu v oblasti, která podporují zóny dostupnosti **zóna dostupnosti** nastavená na *zónově redundantní* ve výchozím nastavení. Další informace o zónách dostupnosti najdete v tématu **zóna dostupnosti** nastavení. Standardní skladová jednotka je vyžadována, pokud přidružte adresu nástroji pro vyrovnávání zatížení Standard. Další informace o nástroji pro vyrovnávání zatížení standard najdete v tématu [nástroji Azure load balancer standardní SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.|
    |Verze protokolu IP|Ano| Vyberte IPv4 nebo IPv6. Během veřejné IPv4 adresy můžete přiřadit k několika prostředkům Azure, veřejnou IP adresu protokolu IPv6 je přiřadit pouze nástroje pro vyrovnávání zatížení přístupem k Internetu. Nástroje pro vyrovnávání zatížení můžete zatížení můžete vyrovnávat přenosy protokolu IPv6 pro virtuální počítače Azure. Další informace o [vyrovnáváním zatížení IPv6 provozu do virtuálních počítačů](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud jste vybrali **standardní SKU**, nemáte možnost vybrat si *IPv6*. Adresu IPv4 přístupnou lze vytvořit pouze při použití **standardní SKU**.|
    |Přiřazení IP adresy|Ano|**Dynamické:** Dynamické adresy se přiřazují pouze po veřejná IP adresa je přidružená k prostředku Azure, a první spuštění prostředku. Dynamické adresy se mohou měnit, pokud máte přiřazenou k prostředku, jako je například virtuální počítač a virtuální počítač je zastaveno (přidělení zrušeno) a potom znovu spustit. Adresa zůstala stejná, pokud virtuální počítač restartovat nebo zastavení (ale není uvolněný.). Dynamické adresy se vydávají při prostředek veřejné IP adresy je oddělen od prostředků, který je přidružen k. **Statické:** Statické adresy se přiřazují při vytvoření veřejné IP adresy. Statické adresy se uvolní až do odstranění prostředku veřejné IP adresy. Pokud adresa není přidružená k prostředku, metodu přiřazování můžete změnit po vytvoření adresu. Pokud je adresa přidružená k prostředku, nebudete moci změnit metodu přiřazení. Pokud vyberete *IPv6* pro **verze protokolu IP**, je metoda přiřazení *dynamické*. Pokud vyberete *standardní* pro **SKU**, je metoda přiřazení *statické*.|
    |Časový limit nečinnosti (minuty)|Ne|Kolik minut nechat připojení TCP nebo HTTP otevřené bez nutnosti spoléhat se na klientských počítačích k odesílání zpráv keep-alive. Pokud vyberete IPv6 pro **verze protokolu IP**, tato hodnota nemůže být změněna. |
    |Popisek názvu DNS|Ne|Musí být jedinečný v rámci Azure location vytvoření názvu v (v rámci všech předplatných a všechny zákazníky). Azure automaticky zaregistruje názvem a IP adresou ve své službě DNS, abyste se mohli připojit k prostředku s názvem. Azure připojí, jako výchozí podsíť *location.cloudapp.azure.com* (Pokud je umístění je vyberete) k názvu zadáte, chcete-li vytvořit plně kvalifikovaný název DNS. Pokud budete chtít vytvořit obě verze adresu, se stejným názvem DNS přiřazená adresy IPv4 i IPv6. Výchozí Azure DNS obsahuje název záznamy IPv4 A a IPv6 AAAA a odpoví oba záznamy, když se hledá název DNS. Klient vybere která adresa (IPv4 nebo IPv6) pro komunikaci s. Místo (nebo kromě) použití popisku názvu DNS s výchozí příponou můžete pomocí služby Azure DNS nakonfigurovat název DNS s vlastní příponou, který se přeloží na veřejnou IP adresu. Další informace najdete v tématu věnovaném [použití Azure DNS s veřejnou IP adresou Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Vytvořit adresu IPv6 (nebo adresa IPv4)|Ne| Určuje, zda je zobrazena IPv6 nebo adresa IPv4 je závislá na výběru pro **verze protokolu IP**. Pokud vyberete třeba **IPv4** pro **verze protokolu IP**, **IPv6** se tady zobrazí. Pokud vyberete *standardní* pro **SKU**, není nutné možnost vytvořit adresu IPv6.
    |Název (pouze viditelné, pokud jste zaškrtli možnost **vytvořit adresu IPv6 (nebo adresa IPv4)** zaškrtávací políčko)|Ano, pokud jste vybrali **vytvořit IPv6** (nebo adresa IPv4) zaškrtávací políčko.|Název musí být jiný než název zadáte pro první **název** v tomto seznamu. Pokud budete chtít vytvořit adresu IPv4 i adresu IPv6, portál vytvoří dvě samostatné prostředky veřejné IP adresy adresu, jeden u každé verze IP adresy přiřazené k němu.|
    |Přiřazení IP adresy (jenom viditelné, pokud jste zaškrtli možnost **vytvořit adresu IPv6 (nebo adresa IPv4)** zaškrtávací políčko)|Ano, pokud jste vybrali **vytvořit IPv6** (nebo adresa IPv4) zaškrtávací políčko.|Pokud je zobrazeno zaškrtávací políčko **vytvořit adresu IPv4 přístupnou**, můžete vybrat metodu přiřazení. Pokud je zobrazeno zaškrtávací políčko **vytvořit adresu IPv6**, nelze vybrat metodu přiřazení, musí být **dynamické**.|
    |Předplatné|Ano|Musí existovat ve stejném [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako prostředek, který chcete přidružit k veřejnou IP adresu.|
    |Skupina prostředků|Ano|Může existovat ve stejné nebo různé [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako prostředek, který chcete přidružit k veřejnou IP adresu.|
    |Umístění|Ano|Musí existovat ve stejném [umístění](https://azure.microsoft.com/regions), která se také označují jako oblast, jako prostředek a přidružte veřejnou IP adresu.|
    |Zóna dostupnosti| Ne | Toto nastavení se zobrazí, jenom Pokud vyberete podporovaném umístění. Seznam podporovaných umístění najdete v tématu [Přehled zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud jste vybrali **základní** SKU, *žádný* se vybere automaticky za vás. Pokud chcete zaručit konkrétní zónu, můžete vybrat konkrétní zónu. Jednu z těchto možností není zónově redundantní. Pokud jste vybrali **standardní** SKU: Zónově redundantní se vybere automaticky za vás a vaše cesta k datům díky odolné vůči selhání zóny. Pokud chcete zaručit konkrétní zóně, což není odolné vůči selhání zóny, můžete vybrat konkrétní zónu.

**Příkazy**

I když portál nabízí možnost vytvořit dvě veřejné IP adresy prostředků (jeden IPv4 a jedna IPv6), následující příkazy rozhraní příkazového řádku a Powershellu vytvoří jeden prostředek s adresou pro jednu verzi protokolu IP, nebo druhé. Pokud chcete, aby dva prostředky veřejné IP adresy adresu, jeden pro každou verzi protokolu IP je musí spustit příkaz dvakrát, zadáním jiné názvy a verze pro prostředky veřejné adresy IP adresy.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Zobrazit, změnit nastavení nebo odstranit veřejnou IP adresu

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *veřejnou ip adresu*. Když **veřejné IP adresy** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte název veřejné IP adresy chcete zobrazit, změnit nastavení, nebo odstranit ze seznamu.
3. Proveďte jeden z následujících možností podle toho, jestli chcete zobrazit, odstranit nebo změnit veřejnou IP adresu.
    - **Zobrazení**: **Přehled** část ukazuje nastavení klíče pro veřejnou IP adresu, jako jsou síťové rozhraní je přidružená ke (Pokud je adresa přidružená k síťovému rozhraní). Na portálu nezobrazí verze adresy (IPv4 nebo IPv6). Chcete-li zobrazit informace o verzi, použijte příkaz prostředí PowerShell nebo rozhraní příkazového řádku zobrazíte veřejnou IP adresu. Pokud je verze IP adresy protokolu IPv6, přiřazenou adresu se nezobrazí na portálu, Powershellu nebo rozhraní příkazového řádku.
    - **Odstranit**: Pokud chcete odstranit veřejnou IP adresu, vyberte **odstranit** v **přehled** oddílu. Pokud je aktuálně přidružených ke konfiguraci IP adresu, nelze odstranit. Pokud je adresa aktuálně přidružen ke konfiguraci, vyberte **zrušte aktuální přidružení** zrušení přidružení adresu z konfigurace protokolu IP.
    - **Změna**: vyberte **konfigurace**. Změnit nastavení, použijte informace uvedené v kroku 4 [vytvoření veřejné IP adresy](#create-a-public-ip-address). Chcete-li změnit přiřazení pro adresu IPv4 přístupnou ze statické na dynamickou, musíte nejprve zrušit přidružení veřejnou IPv4 adresu z konfigurace protokolu IP, která je přidružená. Metodu přiřazování můžete změnit na dynamické a vyberte **přidružit** přiřadit IP adresu, která se stejnou konfigurací IP nebo jinou konfiguraci nebo se to můžete nechat zrušení přidružení. Chcete zrušit přidružení veřejné IP adresy v **přehled** vyberte **zrušte aktuální přidružení**.

    >[!WARNING]
    >Když změníte metodu přiřazení ze statické na dynamickou, ztratíte IP adresu, která byla přiřazena veřejná IP adresa. Zatímco Azure veřejné servery DNS Udržovat mapování mezi adresami statické nebo dynamické a libovolný popisek názvu DNS (Pokud jste definovali jeden), dynamickou IP adresu můžete změnit při spuštění virtuálního počítače po je ve stavu Zastaveno (přidělení zrušeno). Chcete-li zabránit ve změně adresu, přiřadíte statickou IP adresu.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) na seznamu veřejné IP adresy [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) zobrazíte nastavení. [az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) aktualizovat; [az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) odstranit|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) načíst objekt veřejné IP adresy a zobrazte její nastavení [Set-AzureRmPublicIpAddress](/powershell/module/azurerm.network/set-azurermpublicipaddress) aktualizovat nastavení. [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) odstranit|

## <a name="assign-a-public-ip-address"></a>Přiřazení veřejné IP adresy

Zjistěte, jak přiřadit veřejnou IP adresu na následujících odkazech:

- A [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálního počítače (při vytváření), nebo [existujícího virtuálního počítače](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Nástroj pro vyrovnávání zatížení s přístupem k Internetu](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Připojení Site-to-site s použitím služby Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Škálovací sada virtuálních počítačů Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

K provádění úloh na veřejné IP adresy, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                                             | Název                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Přečtěte si veřejné IP adresy                                          |
| Microsoft.Network/publicIPAddresses/write                          | Vytvořit nebo aktualizovat veřejnou IP adresu                           |
| Microsoft.Network/publicIPAddresses/delete                         | Odstranit veřejnou IP adresu                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Přidružit veřejnou IP adresu prostředku                    |

## <a name="next-steps"></a>Další postup

- Vytvoření a veřejné IP adresy s použitím [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure policy](policy-samples.md) za veřejné IP adresy
