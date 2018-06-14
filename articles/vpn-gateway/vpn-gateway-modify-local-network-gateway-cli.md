---
title: Úprava předpon adres IP brány místní sítě a adresa brány VPN | Azure | ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU | Microsoft Docs
description: Tento článek vás provede změna předpony IP adresy pro bránu místní sítě pomocí rozhraní příkazového řádku Azure.
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
ms.openlocfilehash: d62fa14ea24a9b62e793ef7022a761897984df32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "25990618"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Upravit nastavení brány místní sítě pomocí rozhraní příkazového řádku Azure

Někdy se změnit nastavení pro bránu místní sítě předpony adres nebo IP adresu brány. Tento článek ukazuje, jak upravit nastavení brány místní sítě. Můžete také upravit tato nastavení pomocí různých metod výběrem jinou možnost z následujícího seznamu:

> [!div class="op_single_selector"]
> * [portál Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rozhraní příkazového řádku příkazy (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Úprava předpon adres IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Upravit IP adresu brány

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. V tématu [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).

