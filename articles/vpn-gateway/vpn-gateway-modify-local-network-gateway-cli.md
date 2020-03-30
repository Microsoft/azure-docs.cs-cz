---
title: 'Brána VPN: Změna nastavení IP adres brány: Azure CLI'
description: Tento článek vás provede změnou předpony IP adres pro bránu místní sítě pomocí azure CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864039"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Úprava nastavení brány místní sítě pomocí příkazového příkazového příkazu Azure

Někdy se změní nastavení předpony adresy místní síťové brány nebo adresy IP brány. Tento článek ukazuje, jak změnit nastavení brány místní sítě. Tato nastavení můžete také upravit jinou metodou výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Nainstalujte nejnovější verzi příkazů příkazu příkazu příkazu CLI (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Změna předpon IP adres

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Změna IP adresy brány

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [Ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).

