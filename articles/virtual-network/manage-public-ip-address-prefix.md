---
title: Vytvoření, změna nebo odstranění předpony veřejných IP adres Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o předponách veřejných IP adres a o tom, jak je vytvořit, změnit nebo odstranit. Podívejte se, kde najít další informace.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 90fc35249daea51a08cb83143c6be024e78964a7
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804006"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Vytvoření, změna nebo odstranění předpony veřejné IP adresy

Přečtěte si o předponě veřejných IP adres a o tom, jak je vytvořit, změnit a odstranit. Předpona veřejné IP adresy je souvislý rozsah adres na základě počtu veřejných IP adres, které zadáte. Adresy jsou přiřazeny k vašemu předplatnému. Při vytváření prostředku veřejné IP adresy můžete z předpony přiřadit statickou veřejnou IP adresu a přidružit ji k virtuálním počítačům, nástrojům pro vyrovnávání zatížení nebo jiným prostředkům, aby bylo možné povolit připojení k Internetu. Pokud neznáte předpony veřejných IP adres, přečtěte si téma [Přehled předpon veřejných IP adres](public-ip-address-prefix.md) .

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.41 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

Předpony veřejných IP adres mají poplatek. Podrobnosti najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Vytvoření předpony veřejné IP adresy

1. V levém horním rohu portálu vyberte **+ vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte *předponu veřejné IP adresy* . Pokud se ve výsledcích hledání zobrazí **předpona veřejných IP adres** , vyberte ji.
3. V části **předpona veřejných IP adres**vyberte **vytvořit**.
4. Zadejte nebo vyberte hodnoty pro následující nastavení v části **vytvořit předponu veřejné IP adresy**a pak vyberte **vytvořit**:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Předplatné|Ano|Musí existovat ve stejném [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako prostředek, ke kterému chcete přidružit veřejnou IP adresu.|
   |Skupina prostředků|Ano|Může existovat ve stejné nebo jiné [skupině prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako prostředek, ke kterému chcete přidružit veřejnou IP adresu.|
   |Název|Ano|Název musí být jedinečný v rámci vybrané skupiny prostředků.|
   |Oblast|Ano|Musí existovat ve stejné [oblasti](https://azure.microsoft.com/regions)jako veřejné IP adresy, které přiřadíte adresám z rozsahu.|
   |Velikost předpony|Ano| Velikost potřebné předpony. Výchozí hodnota je/28 nebo 16 IP adres.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Vytvoření statické veřejné IP adresy z předpony
Po vytvoření předpony musíte z předpony vytvořit statické IP adresy. Chcete-li to provést, postupujte podle následujících kroků.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *předponu veřejné IP adresy*. Pokud se ve výsledcích hledání zobrazí **předpony veřejných IP adres** , vyberte ji.
2. Vyberte předponu, ze které chcete vytvořit veřejné IP adresy.
3. Jakmile se zobrazí ve výsledcích hledání, vyberte ji a klikněte na **+ Přidat IP adresu** v části Přehled.
4. V části **vytvořit veřejnou IP adresu**zadejte nebo vyberte hodnoty pro následující nastavení. Vzhledem k tomu, že je prefix pro standardní SKU, IPv4 a statický, stačí zadat jenom tyto informace:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Název|Ano|Název veřejné IP adresy musí být v rámci vybrané skupiny prostředků jedinečný.|
   |Časový limit nečinnosti (minuty)|No|Kolik minut má být otevřené připojení TCP nebo HTTP, aniž by se museli spoléhat na to, že klienti odesílají zprávy Keep-Alive. |
   |Popisek názvu DNS|No|Musí být jedinečný v rámci oblasti Azure, ve které jste vytvořili název (mezi všemi předplatnými a všemi zákazníky). Azure automaticky registruje název a IP adresu ve svém DNS, abyste se mohli připojit k prostředku s názvem. Azure připojí k názvu, který zadáte, výchozí podsíť, jako je například *Location.cloudapp.Azure.com* (umístění, kde je vybrané umístění), a vytvoří plně kvalifikovaný název DNS. Další informace najdete v tématu [použití Azure DNS s veřejnou IP adresou Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternativně můžete použít příkazy CLI a PS níže s parametry--Public-IP-prefix (CLI) a-PublicIpPrefix (PS) k vytvoření prostředku veřejné IP adresy. 

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Zobrazení nebo odstranění předpony

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *předponu veřejné IP adresy*. Pokud se ve výsledcích hledání zobrazí **předpony veřejných IP adres** , vyberte ji.
2. Vyberte název předpony veřejné IP adresy, kterou chcete zobrazit, změňte nastavení pro nebo odstranit ze seznamu.
3. Proveďte jednu z následujících možností v závislosti na tom, zda chcete zobrazit, odstranit nebo změnit předponu veřejné IP adresy.
   - **Zobrazení**: v části **Přehled** se zobrazují klíčová nastavení předpony veřejných IP adres, jako je například prefix.
   - **Odstranit**: Pokud chcete odstranit předponu veřejné IP adresy, vyberte v části **Přehled** možnost **Odstranit** . Pokud jsou adresy v rámci předpony přidružené k prostředkům veřejné IP adresy, musíte nejdřív odstranit prostředky veřejné IP adresy. Viz [odstranění veřejné IP adresy](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network Public-IP prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) k vypsání veřejných IP adres, [AZ Network Public-IP prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) k zobrazení nastavení; [AZ Network Public-IP prefix Update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) to Update; [AZ Network Public-IP prefix Delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) to Delete|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) pro načtení objektu veřejné IP adresy a zobrazení jeho nastavení [nastavte-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) na aktualizovat nastavení; [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) k odstranění|

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy s předponami veřejných IP adres, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

| Akce                                                            | Název                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft. Network/publicIPPrefixes/Read                           | Přečíst předponu veřejné IP adresy                                |
| Microsoft. Network/publicIPPrefixes/Write                          | Vytvoří nebo aktualizuje předponu veřejné IP adresy.                    |
| Microsoft. Network/publicIPPrefixes/DELETE                         | Odstranění předpony veřejné IP adresy                              |
|Microsoft. Network/publicIPPrefixes/JOIN/Action                     | Vytvoření veřejné IP adresy z předpony |

## <a name="next-steps"></a>Další kroky

- Další informace o scénářích a výhodách použití [předpony veřejných IP adres](public-ip-address-prefix.md)
