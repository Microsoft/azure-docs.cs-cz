---
title: Připojit přední dveře Azure ke zdroji interního vyrovnávání zatížení pomocí privátního odkazu
titleSuffix: Azure Private Link
description: Přečtěte si, jak připojit přední dveře Azure do interního nástroje pro vyrovnávání zatížení.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803667"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Připojit přední dveře Azure ke zdroji interního vyrovnávání zatížení pomocí privátního odkazu

Tento článek vás provede postupem konfigurace SKU Azure front-SKU pro připojení ke svému zdroji interního nástroje pro vyrovnávání zatížení pomocí služby privátního propojení Azure.

## <a name="prerequisites"></a>Požadavky

Vytvořte [službu privátního propojení](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Povolit privátní odkaz na interní nástroj pro vyrovnávání zatížení
 
V této části namapujete službu privátního propojení na privátní koncový bod vytvořený v privátní síti služby Azure front-dveří. 

1. V rámci vašeho profilu služby Azure front-Premium zvolte v části *Nastavení* možnost **skupiny původ**.

1. Vyberte skupinu původu, pro kterou chcete povolit privátní propojení pro interní nástroj pro vyrovnávání zatížení.

1. Vyberte **+ Přidat počátek** a přidejte počátek interního nástroje pro vyrovnávání zatížení.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Snímek obrazovky s povolením privátního odkazu na interní nástroj pro vyrovnávání zatížení.":::

1. V seznamu **Vyberte prostředek Azure** vyberte **v mém adresáři**. Vyberte nebo zadejte následující nastavení, abyste mohli nakonfigurovat lokalitu, kterou požadujete pro Azure front-dveře Premium pro připojení s soukromou.

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Oblast | Vyberte oblast, která je stejná nebo nejbližší vašemu zdroji. |
    | Typ prostředku | Vyberte **Microsoft. Network/privateLinkServices**. |
    | Prostředek | Vyberte privátní odkaz vázaný na interní nástroj pro vyrovnávání zatížení. |
    | Cílový dílčí prostředek | Ponechte prázdné. |
    | Zpráva požadavku | Upravte zprávu nebo vyberte výchozí. |

1. Pak vyberte **Přidat** a potom **aktualizovat** a uložte konfiguraci.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Schválit připojení privátního koncového bodu z účtu úložiště

1. Otevřete centrum privátních odkazů a vyberte **služby privátních odkazů**. Pak vyberte název vašeho privátního odkazu.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Snímek obrazovky se seznamem privátních odkazů":::

1. V části *Nastavení* vyberte **připojení privátního koncového bodu** .

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Snímek obrazovky se stránkou s přehledem privátního odkazu":::

1. Vyberte *nedokončený* požadavek na privátní koncový bod z Azure front-Branch Premium a pak vyberte **schválit**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Snímek obrazovky s nedokončeným schválením pro soukromý odkaz":::

1. Po schválení by mělo vypadat jako na snímku obrazovky níže. Aby se připojení plně navázalo, bude trvat několik minut. Teď můžete přistupovat k internímu nástroji pro vyrovnávání zatížení z Azure front-dveří Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Snímek obrazovky se schválenou žádostí o soukromý odkaz.":::

## <a name="next-steps"></a>Další kroky

Přečtěte si o [službě privátního propojení](../../private-link/private-link-service-overview.md).
