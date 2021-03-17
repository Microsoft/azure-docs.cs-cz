---
title: 'Připojení virtuální sítě mezi klienty k centru: PowerShell'
titleSuffix: Azure Virtual WAN
description: Tento článek vám pomůže připojit virtuální sítě mezi klienty k virtuálnímu rozbočovači pomocí PowerShellu.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: c49a85c71c9b877be7e143f5caf27dc307fe0c12
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381265"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Připojení virtuální sítě mezi klienty k virtuálnímu centru WAN

Tento článek vám pomůže použít virtuální síť WAN pro připojení virtuální sítě k virtuálnímu rozbočovači v jiném tenantovi. Tato architektura je užitečná, pokud máte klientské úlohy, které musí být připojené ke stejné síti, ale jsou v různých klientech. Například, jak je znázorněno v následujícím diagramu, můžete připojit virtuální síť mimo contoso (vzdálený tenant) k virtuálnímu rozbočovači contoso (nadřazený tenant).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Nastavení konfigurace směrování" :::

V tomto článku získáte informace o těchto tématech:

* Přidejte do svého předplatného Azure jiného tenanta jako přispěvatele.
* Připojení virtuální sítě mezi klienty k virtuálnímu rozbočovači.

Postup pro tuto konfiguraci se provádí pomocí kombinace Azure Portal a PowerShellu. Tato funkce je však k dispozici pouze v PowerShellu a rozhraní příkazového řádku.

## <a name="before-you-begin"></a>Než začnete

### <a name="prerequisites"></a>Požadavky

Pokud chcete použít kroky v tomto článku, musíte mít ve svém prostředí již nastavenou následující konfiguraci:

* Virtuální síť WAN a virtuální rozbočovač v nadřazeném předplatném.
* Virtuální síť konfigurovaná v rámci předplatného v jiném (vzdáleném) tenantovi.
* Ujistěte se, že adresní prostor virtuální sítě ve vzdáleném tenantovi se nepřekrývá s žádným jiným adresním prostorem v žádné jiné virtuální sítě, který už není připojený k nadřazenému virtuálnímu rozbočovači.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Přiřazení oprávnění

Aby nadřazené předplatné s virtuálním rozbočovačem mohl upravovat a přistupovat k virtuálním sítím ve vzdáleném tenantovi, je třeba přiřadit oprávnění **přispěvatele** k vašemu nadřazenému předplatnému ze vzdáleného tenanta.

1. Přidejte přiřazení role **přispěvatele** k nadřazenému účtu (s virtuálním rozbočovačem WAN). K přiřazení této role můžete použít buď PowerShell, nebo Azure Portal. Postup najdete v následujících článcích **Přidat nebo odebrat přiřazení rolí** :

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Azure Portal](../role-based-access-control/role-assignments-portal.md)

1. Dále do aktuální relace prostředí PowerShell přidejte předplatné vzdáleného tenanta a nadřazeného tenanta klienta. Spusťte následující příkaz. Pokud jste přihlášeni k nadřazenému, stačí spustit příkaz pro vzdáleného tenanta.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Ověřte, jestli je přiřazení role úspěšné, přihlášením k Azure PowerShell pomocí nadřazených přihlašovacích údajů a spuštěním následujícího příkazu:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Pokud byla oprávnění úspěšně šířena do nadřazené položky a byla přidána do relace, předplatné vlastněné vzdáleným klientem se zobrazí ve výstupu příkazu.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Připojení virtuální sítě k centru

V následujících krocích přepínáte mezi kontextem těchto dvou odběrů při propojení virtuální sítě s virtuálním rozbočovačem. Nahraďte ukázkové hodnoty tak, aby odrážely vaše vlastní prostředí.

1. Ujistěte se, že jste v kontextu vzdáleného účtu, a to spuštěním následujícího příkazu:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Vytvořte místní proměnnou pro uložení metadat virtuální sítě, ke které se chcete připojit, do centra.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Přepněte zpátky na nadřazený účet.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Připojte virtuální síť k centru.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. Nové připojení můžete zobrazit buď v PowerShellu, nebo v Azure Portal.

   * **PowerShell:** Metadata z nově vytvořeného připojení se zobrazí v konzole PowerShellu v případě, že připojení bylo úspěšně vytvořeno.
   * **Azure Portal:** Přejděte do virtuálního centra **> připojení Virtual Network připojení**. Můžete zobrazit ukazatel na připojení. Chcete-li zobrazit skutečný prostředek, budete potřebovat příslušná oprávnění.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Řešení potíží

* Ověřte, že metadata v $remote (z předchozí [části](#connect)) odpovídají informacím z Azure Portal.
* Oprávnění můžete ověřit pomocí nastavení IAM skupiny prostředků vzdálené tenanta nebo pomocí Azure PowerShellch příkazů (Get-AzSubscription).
* Ujistěte se, že jsou zahrnuté uvozovky kolem názvů skupin prostředků nebo jiných proměnných specifických pro prostředí (např. "VirtualHub1" nebo "VirtualNetwork1").

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v těchto tématech:

* [Nejčastější dotazy](virtual-wan-faq.md) k virtuální síti WAN
