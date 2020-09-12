---
title: Správa veřejných IP adres | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Spravujte veřejné IP adresy.  Také se dozvíte, jak je veřejná IP adresa prostředkem s vlastním konfigurovatelným nastavením.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 7beff39ed2c37eeb0f07571ba6d611d23a3221e7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89292023"
---
# <a name="manage-public-ip-addresses"></a>Správa veřejných IP adres

Přečtěte si o veřejné IP adrese a o tom, jak ji vytvořit, změnit a odstranit. Veřejná IP adresa je prostředek s vlastním konfigurovatelným nastavením. Přiřazení veřejné IP adresy prostředku Azure, který podporuje veřejné IP adresy, umožňuje:
- Příchozí komunikace z Internetu k prostředku, jako je například Azure Virtual Machines (VM), služby Azure Application Gateway, služby Azure Load Balancer, brány Azure VPN a další. I nadále můžete komunikovat s některými prostředky, jako jsou virtuální počítače, z Internetu, pokud k virtuálnímu počítači není přiřazená veřejná IP adresa, pokud je tento virtuální počítač součástí fondu back-end nástroje pro vyrovnávání zatížení a nástroj pro vyrovnávání zatížení má přiřazenou veřejnou IP adresu. Pokud chcete zjistit, jestli se prostředku pro konkrétní službu Azure má přiřadit veřejná IP adresa nebo jestli se dá prostřednictvím veřejné IP adresy jiného prostředku Azure komunikovat přes veřejnou IP adresu, přečtěte si dokumentaci ke službě.
- Odchozí připojení k Internetu pomocí předvídatelné IP adresy. Virtuální počítač může například komunikovat odchozí na Internet bez přiřazené veřejné IP adresy, ale jeho adresa je síťová adresa přeložená službou Azure na nepředvídatelné veřejné adresy, ve výchozím nastavení. Přiřazení veřejné IP adresy prostředku vám umožní zjistit, která IP adresa se používá pro odchozí připojení. I když je předvídatelné, adresa se může změnit v závislosti na zvolené metodě přiřazení. Další informace najdete v tématu [Vytvoření veřejné IP adresy](#create-a-public-ip-address). Další informace o odchozích připojeních z prostředků Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

Veřejné IP adresy mají nominální poplatek. Pokud si chcete zobrazit ceny, přečtěte si stránku [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte *veřejnou IP adresu* . Pokud se ve výsledcích hledání zobrazí **Veřejná IP adresa** , vyberte ji.
3. V části **Veřejná IP adresa**vyberte **vytvořit**.
4. Zadejte nebo vyberte hodnoty pro následující nastavení v části **vytvořit veřejnou IP adresu**a pak vyberte **vytvořit**:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Verze protokolu IP|Yes| Vyberte IPv4 nebo IPv6 nebo obojí. Výběr obou možností znamená, že budou vytvořeny 2 veřejné IP adresy – 1 IPv4 adresa a jedna IPv6 adresa. Přečtěte si další informace o [protokolu IPv6 v Azure virtuální sítě](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Yes|Všechny veřejné IP adresy vytvořené před zavedením SKU jsou veřejné IP adresy SKU **Basic** . Po vytvoření veřejné IP adresy už SKU nemůžete změnit. Samostatné virtuální počítače, virtuální počítače v rámci skupiny dostupnosti nebo sady škálování virtuálních počítačů můžou používat základní nebo standardní SKU. Nepovoluje se kombinování SKU mezi virtuálními počítači v rámci skupin dostupnosti nebo sad škálování nebo samostatných virtuálních počítačů. **Základní** SKU: Pokud vytváříte veřejnou IP adresu v oblasti, která podporuje zóny dostupnosti, nastavení **zóny dostupnosti** je ve výchozím nastavení nastaveno na *hodnotu žádné* . Základní veřejné IP adresy nepodporují zóny dostupnosti. **Standardní** SKU: veřejná IP adresa Standard SKU může být přidružená k virtuálnímu počítači nebo front-endu nástroje pro vyrovnávání zatížení. Pokud vytváříte veřejnou IP adresu v oblasti, která podporuje zóny dostupnosti, nastavení **zóny dostupnosti** je ve výchozím nastavení nastaveno na možnost *zóna – redundantní* . Další informace o zónách dostupnosti najdete v tématu nastavení **zóny dostupnosti** . Pokud přiřadíte adresu ke standardnímu nástroji pro vyrovnávání zatížení, vyžaduje se standardní SKU. Další informace o standardních nástrojích pro vyrovnávání zatížení najdete v tématu [standardní SKU nástroje pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.|
   |Name|Yes|Název musí být jedinečný v rámci vybrané skupiny prostředků.|
   |Přiřazení IP adresy|Yes|**Dynamické:** Dynamické adresy se přiřazují až po přidružení veřejné IP adresy k prostředku Azure a prostředek se spustí poprvé. Dynamické adresy se mohou změnit, pokud jsou přiřazeny k prostředku, jako je například virtuální počítač, a virtuální počítač je zastaven (přidělení zrušeno) a pak restartován. Adresa zůstává stejná, i když se virtuální počítač restartuje nebo zastaví (ale neprovádí se jeho přidělení). Dynamické adresy jsou uvolněny, pokud je prostředek veřejné IP adresy zrušení přidružení z prostředku, ke kterému je přidružen. **Statická:** Při vytvoření veřejné IP adresy se přiřazují statické adresy. Statické adresy se neuvolňují, dokud se neodstraní prostředek veřejné IP adresy. Pokud adresa není přidružena k prostředku, můžete po vytvoření adresy změnit metodu přiřazení. Pokud je adresa přidružená k prostředku, možná nebudete moct změnit metodu přiřazení. Pokud pro **verzi protokolu IP**vyberete *protokol IPv6* , musí být metoda přiřazení pro základní skladovou položku *Dynamická* .  Standardní adresy SKU jsou *statické* pro IPv4 i IPv6. |
   |Časový limit nečinnosti (minuty)|No|Kolik minut má být otevřené připojení TCP nebo HTTP, aniž by se museli spoléhat na to, že klienti odesílají zprávy Keep-Alive. Pokud pro **verzi protokolu IP**vyberete protokol IPv6, tato hodnota se nedá změnit. |
   |Popisek názvu DNS|No|Musí být jedinečné v rámci umístění Azure, ve kterém jste vytvořili název (v rámci všech předplatných a všech zákazníků). Azure automaticky registruje název a IP adresu ve svém DNS, abyste se mohli připojit k prostředku s názvem. Azure připojí k názvu, který zadáte, výchozí podsíť, jako je například *Location.cloudapp.Azure.com* (umístění, kde je vybrané umístění), a vytvoří plně kvalifikovaný název DNS. Pokud se rozhodnete vytvořit obě verze adres, bude stejný název DNS přiřazený k adresám IPv4 i IPv6. Výchozí DNS Azure obsahuje záznamy protokolu IPv4 a i IPv6 AAAA a při hledání názvu DNS reaguje na oba záznamy. Klient zvolí, s jakou adresou (IPv4 nebo IPv6) komunikuje. Místo (nebo kromě) použití popisku názvu DNS s výchozí příponou můžete pomocí služby Azure DNS nakonfigurovat název DNS s vlastní příponou, který se přeloží na veřejnou IP adresu. Další informace najdete v tématu věnovaném [použití Azure DNS s veřejnou IP adresou Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Název (zobrazí se pouze v případě, že vyberete verzi protokolu IP pro **obojí**)|Ano, pokud vyberete verzi protokolu IP **obou**|Název musí být jiný než název, který zadáte pro **jméno v tomto** seznamu. Pokud se rozhodnete vytvořit adresu IPv4 i IPv6, portál vytvoří dva samostatné prostředky veřejné IP adresy, jednu s přiřazenou verzí IP adresy.|
   |Přiřazení IP adresy (viditelné pouze v případě, že vyberete verzi protokolu IP pro **obojí**)|Ano, pokud vyberete verzi protokolu IP **obou**|Stejná omezení jako přiřazení IP adresy výše|
   |Předplatné|Yes|Musí existovat ve stejném [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako prostředek, ke kterému přiřadíte veřejnou IP adresu.|
   |Skupina prostředků|Yes|Může existovat ve stejné nebo jiné [skupině prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako prostředek, ke kterému přiřadíte veřejnou IP adresu.|
   |Umístění|Ano|Musí existovat ve stejném [umístění](https://azure.microsoft.com/regions), které se označuje také jako oblast, jako prostředek, ke kterému budete přidružit veřejnou IP adresu.|
   |Zóna dostupnosti| No | Toto nastavení se zobrazí pouze v případě, že vyberete podporované umístění. Seznam podporovaných umístění najdete v tématu [Přehled zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud jste vybrali položku **základní** skladová jednotka (SKU), není pro vás automaticky vybraná *žádná* . Pokud dáváte přednost zaručení konkrétní zóny, můžete vybrat konkrétní zónu. Buď volba není redundantní v zóně. Pokud jste vybrali možnost **standardní** SKU: zóna – redundantní, je pro vás automaticky vybraná a vaše cesta k datům je odolná vůči selhání zóny. Pokud dáváte přednost zaručení konkrétní zóny, která není odolná vůči selhání zóny, můžete vybrat konkrétní zónu.

**Příkazy**

I když portál nabízí možnost vytvořit dva prostředky veřejné IP adresy (jeden protokol IPv4 a jeden protokol IPv6), následující příkazy CLI a PowerShell vytvoří jeden prostředek s adresou pro jednu nebo druhou verzi protokolu IP. Pokud chcete dva prostředky veřejné IP adresy, jednu pro každou verzi protokolu IP, musíte spustit příkaz dvakrát a zadat různé názvy a verze IP pro prostředky veřejné IP adresy.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Zobrazení, změna nastavení nebo odstranění veřejné IP adresy

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *veřejnou IP adresu*. Pokud se ve výsledcích hledání zobrazí **veřejné IP adresy** , vyberte ji.
2. Vyberte název veřejné IP adresy, kterou chcete zobrazit, změňte nastavení pro nebo odstranit ze seznamu.
3. V závislosti na tom, zda chcete zobrazit, odstranit nebo změnit veřejnou IP adresu, proveďte jednu z následujících možností.
   - **Zobrazení**: v části **Přehled** se zobrazují klíčová nastavení pro veřejnou IP adresu, jako je například síťové rozhraní, ke kterému je přidruženo (Pokud je adresa přidružena k síťovému rozhraní). Na portálu se nezobrazí verze adresy (IPv4 nebo IPv6). Chcete-li zobrazit informace o verzi, použijte příkaz PowerShell nebo CLI k zobrazení veřejné IP adresy. Pokud je verze IP adresy IPv6, přiřazená adresa se nezobrazuje na portálu, PowerShellu nebo rozhraní příkazového řádku.
   - **Odstranit**: Pokud chcete odstranit veřejnou IP adresu, vyberte v části **Přehled** možnost **Odstranit** . Pokud je adresa aktuálně přidružena ke konfiguraci protokolu IP, nelze ji odstranit. Pokud je adresa aktuálně přidružená ke konfiguraci, vyberte možnost **zrušit, pokud se má** adresa oddělit od konfigurace protokolu IP.
   - **Změnit**: vyberte **Konfigurace**. Změňte nastavení pomocí informací v kroku 4 [Vytvoření veřejné IP adresy](#create-a-public-ip-address). Chcete-li změnit přiřazení adresy IPv4 ze statické na dynamickou, je nutné nejprve zrušit přidružení veřejné IPv4 adresy z konfigurace protokolu IP, ke které je přidruženo. Pak můžete metodu přiřazení změnit na dynamickou a vybrat **přidružit** pro přidružení IP adresy ke stejné konfiguraci protokolu IP, jiné konfiguraci, nebo můžete ponechat zrušení přidružení. Pokud chcete zrušit přidružení veřejné IP adresy, v části **Přehled** vyberte zrušit **přidružení**.

   >[!WARNING]
   >Když změníte metodu přiřazení ze statické na dynamickou, ztratíte IP adresu, která byla přiřazena k veřejné IP adrese. I když veřejné servery DNS Azure udržují mapování mezi statickými nebo dynamickými adresami a popisek názvu DNS (Pokud jste ho definovali), může se dynamická IP adresa změnit, když se virtuální počítač spustí po zastavení (navráceném) stavu. Chcete-li zabránit změně adresy, přiřaďte statickou IP adresu.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network Public-IP list](/cli/azure/network/public-ip#az-network-public-ip-list) pro výpis veřejných IP adres, [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show) pro zobrazení nastavení; [AZ Network Public-IP Update](/cli/azure/network/public-ip#az-network-public-ip-update) to Update; [AZ Network Public-IP Delete](/cli/azure/network/public-ip#az-network-public-ip-delete) to Delete|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pro načtení objektu veřejné IP adresy a zobrazení jeho nastavení [nastavte-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) na aktualizovat nastavení; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) k odstranění|

## <a name="assign-a-public-ip-address"></a>Přiřazení veřejné IP adresy

Přečtěte si, jak přiřadit veřejnou IP adresu následujícím prostředkům:

- Virtuální počítač se [systémem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (při vytváření) nebo k [existujícímu virtuálnímu počítači](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Load Balancer směřující na Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Připojení Site-to-site pomocí VPN Gateway Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Sada škálování virtuálních počítačů Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy na veřejných IP adresách, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

| Akce                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft. Network/publicIPAddresses/Read                           | Čtení veřejné IP adresy                                          |
| Microsoft. Network/publicIPAddresses/Write                          | Vytvoření nebo aktualizace veřejné IP adresy                           |
| Microsoft. Network/publicIPAddresses/DELETE                         | Odstranění veřejné IP adresy                                     |
| Microsoft. Network/publicIPAddresses/JOIN/Action                    | Přidružení veřejné IP adresy k prostředku                    |

## <a name="next-steps"></a>Další kroky

- Vytvoření veřejné IP adresy pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo používání [šablon Azure správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](policy-samples.md) pro veřejné IP adresy
