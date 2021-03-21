---
title: Připojit přední dveře Azure ke zdroji webové aplikace pomocí privátního odkazu
titleSuffix: Azure Private Link
description: Přečtěte si, jak připojit přední dveře Azure do WebApp privátního prostředí.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030575"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Připojit přední dveře Azure ke zdroji webové aplikace pomocí privátního odkazu

Tento článek vás provede postupem konfigurace SKU Azure front-end Premium pro soukromou připojení k vaší webové aplikaci pomocí služby privátního propojení Azure.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Vytvořte službu [privátního propojení](../../private-link/create-private-link-service-portal.md) pro vaše zdrojové webové servery.

> [!Note]
> Privátní koncový bod je k dispozici ve veřejných oblastech pro PremiumV2 webové aplikace PremiumV3 Windows, Linux Web Apps a plán služby Azure Functions Premium (někdy se mu říká plán elastické Premium).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Povolení privátního odkazu na webovou aplikaci v Azure front-dveří Premium
 
V této části namapujete službu privátního propojení na privátní koncový bod vytvořený v privátní síti služby Azure front-dveří. 

1. V rámci vašeho profilu služby Azure front-Premium zvolte v části *Nastavení* možnost **skupiny původ**.

1. Vyberte skupinu původní, která obsahuje počátek webové aplikace, pro který chcete povolit privátní propojení.

1. Vyberte **+ Přidat počátek** a přidejte nový počátek webové aplikace nebo vyberte dříve vytvořený počátek webové aplikace ze seznamu.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Snímek obrazovky s povolením privátního odkazu na webovou aplikaci":::

1. V seznamu **Vyberte prostředek Azure** vyberte **v mém adresáři**. Vyberte nebo zadejte následující nastavení, abyste mohli nakonfigurovat lokalitu, kterou požadujete pro Azure front-dveře Premium pro připojení s soukromou.

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Oblast | Vyberte oblast, která je stejná nebo nejbližší vašemu zdroji. |
    | Typ prostředku | Vyberte **Microsoft. Web/weby**. |
    | Prostředek | Vyberte **myPrivateLinkService**. |
    | Cílový dílčí prostředek | místa |
    | Zpráva požadavku | Upravte zprávu nebo vyberte výchozí. |

1. Pak vyberte **Přidat** a uložte konfiguraci.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Schvalování připojení privátního koncového bodu služby Azure front-Premium z webové aplikace

1. V poslední části můžete přejít na webovou aplikaci, na kterou nakonfigurujete privátní odkaz. V části **Nastavení** vyberte **sítě** .

1. V **síťové síti** vyberte **Konfigurovat připojení privátních koncových bodů**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Snímek obrazovky s nastavením sítě ve webové aplikaci":::

1. Vyberte *nedokončený* požadavek na privátní koncový bod z Azure front-Branch Premium a pak vyberte **schválit**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Snímek obrazovky nedokončené žádosti o privátní koncový bod":::

1. Po schválení by mělo vypadat jako na snímku obrazovky níže. Aby se připojení plně navázalo, bude trvat několik minut. K webové aplikaci teď můžete přistupovat z Azure front-dveří Premium. Po povolení privátního koncového bodu se zablokuje přímý přístup k webové aplikaci z veřejného Internetu.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Snímek obrazovky schváleného požadavku koncového bodu":::

## <a name="next-steps"></a>Další kroky

Přečtěte si o [službě privátního propojení pomocí webové aplikace Azure](../../app-service/networking/private-endpoint.md).
