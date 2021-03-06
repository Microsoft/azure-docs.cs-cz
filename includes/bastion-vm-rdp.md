---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521525"
---
1. Otevřete [Azure Portal](https://portal.azure.com). Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak vyberte **připojit**. Z rozevíracího seznamu vyberte **bastionu** .

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Vybrat bastionu":::

1. Po výběru bastionu z rozevíracího seznamu se zobrazí postranní panel se třemi kartami: RDP, SSH a bastionu. Vzhledem k tomu, že bastionu byl zřízen pro virtuální síť, karta bastionu je ve výchozím nastavení aktivní. Vyberte **použít bastionu**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Vyberte použít bastionu":::

1. Na stránce **připojit pomocí Azure bastionu** zadejte uživatelské jméno a heslo pro váš virtuální počítač a pak vyberte **připojit**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Připojit":::

1. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Připojení pomocí portu 443":::
