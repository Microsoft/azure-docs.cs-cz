---
title: Připojení front-bran Azure k původnímu účtu úložiště pomocí privátního odkazu
titleSuffix: Azure Private Link
description: Přečtěte si, jak připojit front-dvířka Azure do účtu úložiště soukromě.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201664"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Připojení front-bran Azure k původnímu účtu úložiště pomocí privátního odkazu

Tento článek vás provede postupem konfigurace SKU Azure front-end Premium pro připojení k původnímu účtu úložiště pomocí služby privátního propojení Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Povolení privátního odkazu na účet úložiště
 
V této části namapujete službu privátního propojení na privátní koncový bod vytvořený v privátní síti služby Azure front-dveří. 

1. V rámci vašeho profilu služby Azure front-Premium zvolte v části *Nastavení* možnost **skupiny původ**.

1. Vyberte skupinu původní, která obsahuje počátek účtu úložiště, pro který chcete povolit privátní propojení.

1. Vyberte **+ Přidat počátek** a přidejte nový počátek účtu úložiště nebo ze seznamu vyberte dřív vytvořený počátek účtu úložiště.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Snímek obrazovky s povolením privátního odkazu na účet úložiště":::

1. V seznamu **Vyberte prostředek Azure** vyberte **v mém adresáři**. Vyberte nebo zadejte následující nastavení, abyste mohli nakonfigurovat lokalitu, kterou požadujete pro Azure front-dveře Premium pro připojení s soukromou.

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Oblast | Vyberte oblast, která je stejná nebo nejbližší vašemu zdroji. |
    | Typ prostředku | Vyberte **Microsoft. Storage/storageAccounts**. |
    | Prostředek | Vyberte svůj účet úložiště. |
    | Cílový dílčí prostředek | Můžete vybrat *objekt BLOB* nebo *Web*. |
    | Zpráva požadavku | Upravte zprávu nebo vyberte výchozí. |

1. Pak vyberte **Přidat** a uložte konfiguraci.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Schválit připojení privátního koncového bodu z účtu úložiště

1. V poslední části můžete přejít na účet úložiště, pro který konfigurujete privátní odkaz. V části **Nastavení** vyberte **sítě** .

1. V článku **sítě** vyberte **připojení privátního koncového bodu**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Snímek obrazovky s nastavením sítě ve webové aplikaci":::

1. Vyberte *nedokončený* požadavek na privátní koncový bod z Azure front-Branch Premium a pak vyberte **schválit**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Snímek obrazovky s požadavkem na soukromý koncový bod úložiště čeká na vyřízení.":::

1. Po schválení by mělo vypadat jako na snímku obrazovky níže. Aby se připojení plně navázalo, bude trvat několik minut. Teď můžete získat přístup k účtu úložiště z Azure front-dveří Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Snímek obrazovky se schváleným požadavkem na koncový bod úložiště.":::

## <a name="next-steps"></a>Další kroky

Přečtěte si o [službě privátního propojení s účtem úložiště](../../storage/common/storage-private-endpoints.md).
