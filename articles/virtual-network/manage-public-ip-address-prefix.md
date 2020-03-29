---
title: Vytvoření, změna nebo odstranění předpony veřejné IP adresy Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak vytvořit, změnit nebo odstranit předponu veřejné IP adresy.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 26d8ee34c735cab8f1033a9aad897ec0b1bed524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65952680"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Vytvoření, změna nebo odstranění předpony veřejné IP adresy

Přečtěte si o předponě veřejné IP adresy a o tom, jak ji vytvořit, změnit a odstranit. Předpona veřejné IP adresy je souvislý rozsah adres na základě počtu veřejných adres IP, které zadáte. Adresy jsou přiřazeny k vašemu předplatnému. Při vytváření prostředku veřejné IP adresy můžete přiřadit statickou veřejnou IP adresu z předpony a přidružit adresu k virtuálním počítačům, nástrojovi na vyrovnávání zatížení nebo jiným prostředkům, abyste povolili připojení k Internetu. Pokud nejste obeznámeni s předčíslími veřejných IP adres, přečtěte si informace [o přehledu předpony veřejné IP adresy.](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v libovolné části tohoto článku proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud pomocí příkazů Prostředí PowerShell k dokončení úloh v tomto článku, buď spustit příkazy v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.41 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.

Účet, ke kterému se přihlásíte nebo se s ním připojíte, musí být přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

Předpony veřejných IP adres mají poplatek. Podrobnosti naleznete v [tématu ceny](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Vytvoření předpony veřejné IP adresy

1. V levém horním rohu portálu vyberte **+ Vytvořit prostředek**.
2. Do pole Hledat na *marketplace* zadejte *předponu veřejné IP adresy.* Když se ve výsledcích hledání zobrazí **předpona veřejné IP adresy,** vyberte ji.
3. V části **Předpona Veřejné IP adresy**vyberte **Vytvořit**.
4. V části **Vytvořit předponu veřejné IP adresy**vyberte zadat nebo vybrat hodnoty pro následující nastavení a pak vyberte **Vytvořit**:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Předplatné|Ano|Musí existovat ve stejném [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako prostředek, ke kterém chcete přidružit veřejnou IP adresu.|
   |Skupina prostředků|Ano|Může existovat ve stejné nebo jiné [skupině prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako prostředek, ke kterém chcete přidružit veřejnou IP adresu.|
   |Name (Název)|Ano|Název musí být jedinečný v rámci vybrané skupiny prostředků.|
   |Region (Oblast)|Ano|Musí existovat ve stejné [oblasti](https://azure.microsoft.com/regions)jako veřejné IP adresy, které přiřadíte adresy z oblasti.|
   |Velikost předpony|Ano| Velikost předpony, kterou potřebujete. Výchozí adresou IP /28 nebo 16 je.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az sítě public-ip prefix vytvořit](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Nový-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Vytvoření statické veřejné IP adresy z předpony
Po vytvoření předpony je nutné vytvořit statické adresy IP z předpony. Chcete-li to provést, postupujte podle následujících kroků.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *předponu veřejné IP adresy*. Když se ve výsledcích hledání zobrazí **předpony veřejných IP adres,** vyberte ji.
2. Vyberte předponu, ze které chcete vytvořit veřejné IP adresy.
3. Až se zobrazí ve výsledcích hledání, vyberte ji a klikněte na **+Přidat IP adresu** v části Přehled.
4. Zadejte nebo vyberte hodnoty pro následující nastavení v části **Vytvořit veřejnou IP adresu**. Vzhledem k tomu, že předpona je pro standardní skladovou položku, IPv4 a statickou, stačí zadat pouze následující informace:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Name (Název)|Ano|Název veřejné IP adresy musí být jedinečný v rámci vybrané skupiny prostředků.|
   |Časový limit nečinnosti (minuty)|Ne|Kolik minut chcete zachovat otevřené připojení TCP nebo HTTP bez spoléhání se na klienty, kteří posílají zprávy keep-alive. |
   |Popisek názvu DNS|Ne|Musí být jedinečný v rámci oblasti Azure, ve které vytvoříte název (ve všech předplatných a všech zákaznících). Azure automaticky zaregistruje název a IP adresu ve svém DNS, takže se můžete připojit k prostředku s názvem. Azure připojí k zadaným názvu výchozí podsíť, například *location.cloudapp.azure.com* (kde je umístění, které vyberete), a vytvoří tak plně kvalifikovaný název DNS. Další informace najdete [v tématu Použití Azure DNS s veřejnou IP adresou Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternativně můžete použít příkazy CLI a PS níže s parametry --public-ip-prefix (CLI) a -PublicIpPrefix (PS) k vytvoření prostředku veřejné IP adresy. 

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Zobrazení nebo odstranění předpony

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *předponu veřejné IP adresy*. Když se ve výsledcích hledání zobrazí **předpony veřejných IP adres,** vyberte ji.
2. Vyberte název předpony veřejné IP adresy, kterou chcete zobrazit, změnit nastavení nebo odstranit ze seznamu.
3. V závislosti na tom, zda chcete zobrazit, odstranit nebo změnit předponu veřejné IP adresy, dokončete jednu z následujících možností.
   - **Zobrazení**: Část **Přehled** zobrazuje klíčová nastavení předpony veřejné IP adresy, například předponu.
   - **Odstranit**: Chcete-li odstranit předponu veřejné IP adresy, vyberte **odstranit** v části **Přehled.** Pokud jsou adresy v předponě přidruženy k veřejným prostředkům IP adresy, musíte nejprve odstranit prostředky veřejné IP adresy. Viz [odstranění veřejné IP adresy](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az sítě public-ip prefix seznam](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) veřejných IP adres, [az sítě public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) zobrazit nastavení; [az sítě public-ip prefix aktualizace](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) aktualizovat; [az sítě public-ip prefix odstranit](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) odstranit|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) načíst objekt veřejné IP adresy a zobrazit jeho nastavení, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) aktualizovat nastavení; [Odstranit-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) odstranit|

## <a name="permissions"></a>Oprávnění

Chcete-li provádět úkoly s předpony veřejných IP adres, musí být váš účet přiřazen k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, které jsou přiřazeny příslušné akce uvedené v následující tabulce:

| Akce                                                            | Name (Název)                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Čtení předpony veřejné IP adresy                                |
| Microsoft.Network/publicIPPrefixes/write                          | Vytvoření nebo aktualizace předpony veřejné IP adresy                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Odstranění předpony veřejné IP adresy                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Vytvoření veřejné IP adresy z předpony |

## <a name="next-steps"></a>Další kroky

- Informace o scénářích a výhodách používání [veřejné předpony IP](public-ip-address-prefix.md)
