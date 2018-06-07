---
title: Vytvoření, změnit nebo odstranit Azure veřejnou IP adresu | Microsoft Docs
description: Naučte se vytvářet, měnit nebo odstranit veřejnou IP adresu.
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
ms.openlocfilehash: 30b4a7ea0d3f68e48d02e5cb72e70de74dc2addf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658685"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Vytvoření, změnit nebo odstranit veřejnou IP adresu

Další informace o veřejné IP adresy a jak vytvářet, měnit a odstraňovat jeden. Veřejná IP adresa je prostředek s vlastním konfigurovatelným nastavením. Přiřazení veřejnou IP adresu pro prostředek služby Azure, který podporuje veřejných IP adres umožňuje:
- Příchozí komunikaci z Internetu k prostředku, například Azure virtuální počítače (VM), Azure Application Gateway, nástroje pro vyrovnávání zatížení Azure, Azure VPN Gateway a dalších. Můžete stále komunikovat s některým prostředkům, jako jsou virtuální počítače z Internetu, pokud virtuální počítač nemá veřejnou IP adresu k němu přiřazen, dokud virtuální počítač je součástí fondu back-end pro vyrovnávání zatížení a nástroje pro vyrovnávání zatížení je přiřazen veřejnou IP adresu. Pokud chcete zjistit, zda prostředek pro konkrétní službu Azure lze přiřadit veřejnou IP adresu, nebo jestli může být přenášena přes veřejnou IP adresu z různých prostředků Azure s, najdete v dokumentaci pro službu. 
- Odchozí připojení k Internetu pomocí předvídatelný IP adresy. Virtuální počítač může například komunikovat odchozí k Internetu bez veřejnou IP adresu přiřadit k němu, ale jeho adresa je adresa sítě přeložit v Azure na nepředvídatelným veřejnou adresu, ve výchozím nastavení. Přiřazení veřejnou IP adresu prostředku umožňuje vědět, která IP adresa se používá pro odchozí připojení. Když předvídatelný, můžete změnit adresu, v závislosti na metodě přiřazení vybrané. Další informace najdete v tématu [vytvoření veřejné IP adresy](#create-a-public-ip-address). Další informace o odchozí připojení z prostředků Azure, najdete v části [pochopit odchozí připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

Účet přihlásit nebo připojit k Azure, musí být přiřazená k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

Veřejné IP adresy mají nominální poplatků. Zobrazit cenách, přečtěte si téma [IP adresu ceny](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. 

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. V horní, v levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
2. Zadejte *veřejnou ip adresu* v *vyhledávání na webu Marketplace* pole. Když **veřejnou IP adresu** se zobrazí ve výsledcích hledání, vyberte ho.
3. V části **veřejnou IP adresu**, vyberte **vytvořit**.
4. Zadejte nebo vyberte hodnoty pro následující nastavení v části **vytvoření veřejné IP adresy**, pak vyberte **vytvořit**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Název|Ano|Název musí být jedinečný v rámci skupiny prostředků, kterou vyberete.|
    |Skladová jednotka (SKU)|Ano|Všechny veřejné IP adresy, které vytvořili předtím, než jsou uvedení SKU **základní** SKU veřejné IP adresy.  Verze SKU nelze změnit po vytvoření veřejné IP adresy. Základní nebo standardní SKU, můžete použít samostatný virtuální počítač, virtuální počítače v rámci skupiny dostupnosti nebo sady škálování virtuálního počítače.  Kombinování SKU mezi virtuálními počítači v rámci skupiny dostupnosti nebo sady škálování není povoleno. **Základní** SKU: při vytváření veřejnou IP adresu v oblasti, která podporuje dostupnost zóny **dostupnost zóny** nastavení je *žádné* ve výchozím nastavení. Můžete vybrat zóny dostupnost zaručit konkrétní zónu pro svoji veřejnou IP adresu. **Standardní** SKU: veřejnou IP adresu standardní SKU A může být přidružen k virtuálnímu počítači nebo uživatelského rozhraní nástroje pro vyrovnávání zatížení. Pokud vytváříte veřejnou IP adresu v oblasti, která podporuje dostupnost zóny **dostupnost zóny** nastavení je *Zónově redundantní* ve výchozím nastavení. Další informace o dostupnosti zóny, najdete v článku **dostupnost zóny** nastavení. Pokud přidružíte adresu nástroji pro vyrovnávání zatížení standardní, je nutné standardní SKU. Další informace o vyrovnávání zatížení standardní, najdete v části [pro vyrovnávání zatížení Azure standardní SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.|
    |Verze protokolu IP|Ano| Vyberte IPv4 nebo IPv6. Při veřejné IPv4 adresy můžete přiřadit několik prostředky Azure, veřejnou IP adresu IPv6 můžete přiřadit pouze Vyrovnávání zatížení, internetové brány. Nástroje pro vyrovnávání zatížení můžete vyrovnávat přenosy protokolu IPv6 na virtuálních počítačích Azure zatížení. Další informace o [přenosy protokolu IPv6 k virtuálním počítačům Vyrovnávání zatížení](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud jste vybrali **standardní SKU**, nemáte možnost vybrat *IPv6*. Adresu IPv4 lze vytvořit pouze při použití **standardní SKU**.|
    |Přiřazení IP adresy|Ano|**Dynamické:** dynamické adresy jsou přiřazeny pouze po veřejná IP adresa je přidružená k síťové rozhraní připojené k virtuálnímu počítači a virtuální počítač běží poprvé. Dynamické adresy se mohou změnit, pokud je virtuální počítač, který síťové rozhraní je připojen k zastavena (deallocated). Adresa zůstává stejná, pokud virtuální počítač restartovat nebo zastavena (ale není navrácena). **Statické:** při vytvoření veřejné IP adresy jsou přiřazené statické adresy. Statické adresy neměňte i v případě, že virtuální počítač uveden do stavu ukončeno (deallocated). Adresu pouze vydání při odstranění rozhraní sítě. Přiřazení metodu můžete změnit po vytvoření síťové rozhraní. Pokud vyberete *IPv6* pro **IP verze**, je metoda přiřazení *dynamické*. Pokud vyberete *standardní* pro **SKU**, je metoda přiřazení *statické*.|
    |Časový limit nečinnosti (minuty)|Ne|Kolik minut pro uchování připojení TCP nebo HTTP otevřené bez nutnosti spoléhat se na klientům posílat zprávy keep-alive. Pokud vyberete IPv6 pro **IP verze**, tato hodnota nemůže být změněna. |
    |Popisek názvu DNS|Ne|Musí být jedinečný v rámci umístění Azure, vytvořte název v (v rámci všech odběrů a všem zákazníkům). Azure automaticky registruje názvu a IP adresy v jeho DNS, aby se mohli připojit k prostředku s názvem. Azure připojí, jako výchozí podsíť *location.cloudapp.azure.com* (kde umístění je umístění vyberete) k názvu zadáte, chcete-li vytvořit plně kvalifikovaný název DNS. Pokud se rozhodnete vytvořit obě verze adresu, se stejným názvem DNS přiřazen adresy IPv4 a IPv6. Výchozí Azure DNS obsahuje záznamy názvového IPv4 A a IPv6 AAAA a odpovědí se oba záznamy, pokud se hledá název DNS. Klient zvolí které adresy (IPv4 nebo IPv6) ke komunikaci s. Místo (nebo kromě) použití popisku názvu DNS s výchozí příponou můžete pomocí služby Azure DNS nakonfigurovat název DNS s vlastní příponou, který se přeloží na veřejnou IP adresu. Další informace najdete v tématu věnovaném [použití Azure DNS s veřejnou IP adresou Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Vytvořit adresu IPv6 (nebo IPv4)|Ne| Jestli je zobrazena IPv6 a IPv4 je závislý na výběru pro **IP verze**. Například, pokud vyberete **IPv4** pro **IP verze**, **IPv6** se zobrazí tady. Pokud vyberete *standardní* pro **SKU**, nemáte možnost vytvořit adresu IPv6.
    |Název (viditelné v případě, že je zaškrtnuté **vytvořit adresu IPv6 (nebo IPv4)** políčko)|Ano, pokud jste vybrali **vytvořit IPv6** (nebo IPv4) zaškrtávací políčko.|Název musí být jiný než název zadejte pro první **název** v tomto seznamu. Pokud vyberete možnost vytvořit IPv4 i adresu IPv6, portálu vytvoří dvě samostatné prostředky veřejné IP adresy adresu, jeden s verze jednotlivých IP adresy přiřazené.|
    |Přiřazení IP adresy (viditelné v případě, že je zaškrtnuté **vytvořit adresu IPv6 (nebo IPv4)** políčko)|Ano, pokud jste vybrali **vytvořit IPv6** (nebo IPv4) zaškrtávací políčko.|Pokud políčko uvádí **vytvořit adresu IPv4**, můžete vybrat metodu přiřazení. Pokud políčko uvádí **vytvořit adresu IPv6**, nelze vybrat metodu přiřazení jako musí být **dynamické**.|
    |Předplatné|Ano|Musí existovat ve stejné [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako prostředek, který chcete přidružit veřejnou IP adresu, která.|
    |Skupina prostředků|Ano|Může existovat ve stejné nebo jiné, [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako prostředek, který chcete přidružit veřejnou IP adresu, která.|
    |Umístění|Ano|Musí existovat ve stejné [umístění](https://azure.microsoft.com/regions), také nazývaný jako oblasti jako prostředek, který chcete přidružit veřejnou IP adresu, které se.|
    |Zóna dostupnosti| Ne | Toto nastavení se zobrazí, pouze pokud jste vybrali podporovaných umístění. Seznam podporovaných umístění najdete v tématu [dostupnost zóny přehled](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud jste vybrali **základní** SKU, *žádné* je automaticky vybrána pro vás. Pokud dáváte přednost zaručit konkrétní zónu, můžete vybrat konkrétní zónu. Jednu z těchto možností není zónově redundantní. Pokud jste vybrali **standardní** SKU: Zónově redundantní se vybere automaticky pro vás a provede cestu k datům odolné selhání zóny. Pokud chcete zaručit konkrétní zóně, což není odolný proti selhání zóny, můžete vybrat konkrétní zónu.

**Příkazy**

Když na portál poskytuje možnost vytvořit dva veřejné IP adresy prostředky (jedna adresa IPv4 a IPv6 jeden), následující příkazy rozhraní příkazového řádku a prostředí PowerShell vytvořit jeden prostředek s adresou pro jeden IP verze nebo na druhý. Pokud chcete, aby dva veřejné IP adresy prostředky, jeden pro každou verzi protokolu IP, musí spustit příkaz dvakrát, zadání odlišné názvy a verze pro prostředky veřejné IP adresy.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Zobrazení, změňte nastavení pro nebo odstranit veřejnou IP adresu

1. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *veřejnou ip adresu*. Když **veřejné IP adresy** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte název veřejné IP adresy chcete zobrazit, změnit nastavení, nebo odstranit ze seznamu.
3. Použijte některý z následujících možností, v závislosti na tom, jestli chcete zobrazit, odstranit nebo změnit veřejnou IP adresu.
    - **Zobrazení**: **přehled** část ukazuje nastavení klíče pro veřejnou IP adresu, například síťové rozhraní je přidružená (Pokud je adresa přidružená k síťovému rozhraní). Na portálu nezobrazí verze adresy (IPv4 nebo IPv6). Chcete-li zobrazit informace o verzi, použijte příkaz prostředí PowerShell nebo rozhraní příkazového řádku zobrazíte veřejnou IP adresu. Pokud verze IP adresy IPv6, se nezobrazí přiřazenou adresu, na portálu, prostředí PowerShell nebo rozhraní příkazového řádku.
    - **Odstranit**: Chcete-li odstranit veřejnou IP adresu, vyberte **odstranit** v **přehled** části. Pokud je aktuálně přidružené ke konfiguraci IP adresa, nelze jej odstranit. Pokud je adresa aktuálně přidružen ke konfiguraci, vyberte **zrušte aktuální přidružení** zrušení přidružení z konfigurace IP adresu.
    - **Změna**: vyberte **konfigurace**. Změňte nastavení pomocí informací v kroku 4 v [vytvoření veřejné IP adresy](#create-a-public-ip-address). Pokud chcete změnit přiřazení pro adresu IPv4 ze statické na dynamickou, musíte nejprve zrušit přidružení na veřejnou IPv4 adresu z konfigurace protokolu IP, které je přidružené k. Můžete změnit metodu přiřazení dynamické a vyberte **přidružit** přidružit IP adresu, na stejnou konfiguraci IP, jinou konfiguraci nebo můžete nechat ji zrušit její přidružení. Chcete-li oddělit veřejnou IP adresu v **přehled** vyberte **zrušte aktuální přidružení**.

    >[!WARNING]
    >Pokud změníte přiřazení metoda ze statické na dynamickou, ztratíte IP adresu, která byla přiřazena veřejnou IP adresu. Zatímco Azure veřejné servery DNS Udržovat mapování mezi statické nebo dynamické adresy a všechny Popisek názvu DNS (Pokud jste definovali jeden), dynamickou IP adresu můžete změnit, když se virtuální počítač spustí po je v zastaveném stavu (deallocated). Chcete-li zabránit změna adresu, přiřadíte statickou IP adresu.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[seznam veřejné ip sítě az](/cli/azure/network/public-ip#az-network-public-ip-list) do seznamu veřejné IP adresy, [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az-network-public-ip-show) zobrazíte nastavení; [aktualizace veřejné ip sítě az](/cli/azure/network/public-ip#az-network-public-ip-update) aktualizovat; [odstranit veřejné sítě az-ip](/cli/azure/network/public-ip#az-network-public-ip-delete) odstranit|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) načíst objekt veřejných IP adres a zobrazit její nastavení [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress) se aktualizovat nastavení; [Odebrat AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) odstranit|

## <a name="permissions"></a>Oprávnění

K provádění úloh na veřejné IP adresy, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                                             | Název                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Přečtěte si veřejnou IP adresu                                          |
| Microsoft.Network/publicIPAddresses/write                          | Vytvořit nebo aktualizovat veřejnou IP adresu                           |
| Microsoft.Network/publicIPAddresses/delete                         | Odstranit veřejnou IP adresu                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Přidružte veřejnou IP adresu na prostředek                    |

## <a name="next-steps"></a>Další postup

- Vytvoření veřejné IP adresy pomocí [prostředí PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablony Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure zásad](policy-samples.md) pro veřejné IP adresy
- Při vytváření přiřadit veřejné IP adresy [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálního počítače Azure, [nástroj pro vyrovnávání zatížení Azure internetové Azure](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Aplikační brána](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [připojení Site-to-site pomocí služby Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo [sadu škálování virtuálního počítače Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
