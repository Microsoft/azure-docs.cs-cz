---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978118"
---
1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak vyberte **připojit**. Z rozevíracího seznamu vyberte **bastionu** .

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Vybrat bastionu" border="false":::

1. Po výběru bastionu z rozevíracího seznamu se zobrazí postranní panel se třemi kartami: RDP, SSH a bastionu. Vzhledem k tomu, že bastionu byl zřízen pro virtuální síť, karta bastionu je ve výchozím nastavení aktivní. Vyberte **použít bastionu**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Vybrat bastionu" border="false":::

1. Na stránce **připojit pomocí Azure bastionu** zadejte uživatelské jméno a heslo pro váš virtuální počítač a pak vyberte **připojit**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Vybrat bastionu" border="false":::

1. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Vybrat bastionu" border="false":::
