---
title: 'VPN Gateway: Úprava nastavení IP adresy brány: Azure CLI'
description: Tento článek vás provede změnou předpon IP adres pro bránu místní sítě pomocí Azure CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 26462f73680d35371c6a03c3224742626e8f2090
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014970"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Úprava nastavení místní síťové brány pomocí Azure CLI

Někdy se nastavení pro předponu adresy brány místní sítě nebo IP adresy brány změní. V tomto článku se dozvíte, jak upravit nastavení místní síťové brány. Tato nastavení můžete také upravit pomocí jiné metody výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Než začnete

Nainstalujte nejnovější verzi příkazů rozhraní příkazového řádku (2,0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Upravit předpony IP adres

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Úprava IP adresy brány

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).

