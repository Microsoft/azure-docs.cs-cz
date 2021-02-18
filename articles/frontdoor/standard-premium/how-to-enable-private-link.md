---
title: Připojení služby Azure front-dveře ke zdroji pomocí privátního odkazu
description: Přečtěte si, jak pomocí Azure Portal připojit své špičkové dveře Azure ke svému zdroji pomocí služby soukromé vazby.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098884"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Připojení služby Azure front-dveře ke zdroji pomocí privátního odkazu

Tento článek vás provede postupem konfigurace SKU Azure front-in Premium pro připojení k vašim aplikacím hostovaným ve virtuální síti pomocí služby privátního propojení Azure.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Vytvořte službu [privátního propojení](../../private-link/create-private-link-service-portal.md) pro vaše zdrojové webové servery.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Povolit privátní koncový bod ve službě Azure front-dveří

V této části namapujete službu privátního propojení Azure na privátní koncový bod, který se vytvořil v privátní síti SKU služby Azure front dveří Premium. 

1. V rámci vašeho profilu služby Azure front-Premium zvolte v části *Nastavení* možnost **skupiny původ**.

1. Vyberte skupinu původní, která obsahuje zdroj, pro který chcete povolit privátní propojení.

1. Vyberte **+ Přidat počátek** a přidejte nový počátek nebo vyberte dříve vytvořený počátek ze seznamu. Potom zaškrtnutím políčka **povolíte službu privátního propojení**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Snímek obrazovky s povolením privátního odkazu v části přidat zdrojovou stránku":::

1. V seznamu **Vyberte prostředek Azure** vyberte **v mém adresáři**. Vyberte nebo zadejte následující nastavení pro konfiguraci prostředku, pro který chcete, aby se aplikace Azure front-dveří Premium připojovala k privátnímu.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Oblast | Vyberte oblast, která je stejná nebo nejbližší vašemu zdroji. |
    | Typ prostředku | Vyberte **Microsoft. Network/privateLinkServices**. |
    | Prostředek | Vyberte **myPrivateLinkService**. |
    | Cílový dílčí prostředek | Toto pole nechejte prázdné. |
    | Zpráva požadavku | Upravte zprávu nebo vyberte výchozí zprávu. |

## <a name="next-steps"></a>Další kroky

Seznamte se s [privátním odkazem na službu Azure front dveří Premium](concept-private-link.md).
