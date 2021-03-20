---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93041540"
---
>[!NOTE]
>Na klientském počítači s Windows, že kterého se připojujete, musíte mít oprávnění správce.
>

1. Pokud se chcete připojit k virtuální síti, v klientském počítači přejděte na nastavení sítě VPN a vyhledejte připojení VPN, které jste vytvořili. Má název stejný jako název vaší virtuální sítě. Vyberte **Connect** (Připojit). Může se zobrazit místní zpráva týkající se použití certifikátu. Pokud chcete používat zvýšená oprávnění, vyberte **pokračovat** .

1. Na stránce Stav **připojení** vyberte **připojit** a spusťte připojení. Pokud uvidíte obrazovku **Výběr certifikátu**, ujistěte se, že zobrazený klientský certifikát je ten, který chcete pro připojení použít. Pokud není, vyberte pomocí šipky rozevíracího seznamu správný certifikát a pak vyberte **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Připojení z počítače se systémem Windows":::

1. Vaše připojení bylo vytvořeno.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Připojení počítače k virtuální síti Azure – diagram připojení typu Point-to-site":::
