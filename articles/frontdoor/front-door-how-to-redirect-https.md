---
title: Vytvoření front-dveří s použitím přesměrování HTTP na HTTPS pomocí Azure Portal
description: Naučte se, jak vytvořit front-dvířka s přesměrovaným provozem z HTTP na HTTPS pomocí Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91626618"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření front-dveří s použitím přesměrování HTTP na HTTPS pomocí Azure Portal

Pomocí Azure Portal můžete [vytvořit front-dveře](quickstart-create-front-door.md) s certifikátem pro ukončení protokolu TLS. Pravidlo směrování se používá k přesměrování provozu HTTP na HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Vytvoření front-dveří s existujícím prostředkem webové aplikace

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

1. Vyberte **vytvořit prostředek** , který najdete v levém horním rohu Azure Portal.

1. Vyhledejte **přední dveře** pomocí panelu hledání a po nalezení typu prostředku vyberte **vytvořit**.

1. Zvolte *předplatné* a pak buď použijte existující skupinu prostředků, nebo vytvořte novou. Výběrem **Další** zadejte kartu konfigurace.

    > [!NOTE]
    > Umístění, které se požaduje v uživatelském rozhraní, je pouze pro skupinu prostředků. Vaše konfigurace front-dveří se nasadí ve všech [umístěních pop služby Azure front-dveří](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Konfigurace základů pro nové přední dveře":::

1. Konfigurace pro přední dveře probíhá ve třech krocích – Přidání výchozího hostitele front-endu, přidání back-endy do back-endu a následným vytvořením pravidel směrování pro mapování chování směrování pro hostitele front-endu. Na **+** _hostitelích front-end_ vyberte ikonu ' ' a vytvořte hostitele se front-end.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Návrhář konfigurace front-dveří":::

1. Zadejte globálně jedinečný název vašeho výchozího hostitele front-endu pro vaše přední dveře. Vyberte **Přidat** a pokračujte k dalšímu kroku.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Přidat hostitele front-endu":::

### <a name="create-backend-pool"></a>Vytvořit back-end fond

1. Vyberte **+** ikonu ve _fondech back-end_ a vytvořte back-end fond. Zadejte název back-end fondu a pak vyberte **Přidat back-end**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Back-end fond návrháře konfigurace front":::

1. Jako _službu App Service_ vyberte typ hostitele back-endu. Vyberte předplatné, ve kterém je vaše webová aplikace hostovaná, a pak vyberte konkrétní webovou aplikaci z rozevíracího seznamu pro **název hostitele back-endu**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Přidání back-endu do back-endu fondu":::

1. Vyberte **Přidat** a uložte back-end a znovu vyberte **Přidat** a uložte konfiguraci fondu back-end. 

## <a name="create-http-to-https-redirect-rule"></a>Vytvořit pravidlo přesměrování HTTP na HTTPS

1. Vyberte **+** ikonu na základě *pravidel směrování* a vytvořte trasu. Zadejte název trasy, například "HttpToHttpsRedirect", a pak nastavte pole *přijatý protokol* pouze na hodnotu **http**. Ujistěte se, že je vybraný příslušný *front-end/domén* .  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Pravidlo směrování pro návrháře konfigurace front-dveří":::

1. V části *Podrobnosti o trasách* nastavte *typ trasy* pro **přesměrování**. Zajistěte, aby se *typ přesměrování* nastavil na **Nalezeno (302)** a *protokol přesměrování* se nastavil **jenom na https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Přidat trasu přesměrování HTTP na HTTPS":::

1. Vyberte **Přidat** a uložte pravidlo směrování pro přesměrování HTTP na https.

## <a name="create-forwarding-rule"></a>Vytvořit předávací pravidlo

1. Přidejte další pravidlo směrování pro zpracování provozu HTTPS. Vyberte možnost **+** přihlašovat *pravidla směrování* a zadejte název trasy, například "DefaultForwardingRoute". Pak nastavte pole *přijaté protokoly* jenom na **https**. Ujistěte se, že je vybraný příslušný *front-end/domén* .

1. V části Podrobnosti o trasách nastavte *typ směrování* na hodnotu **předávat**. Ujistěte se, že je vybraný správný fond back-end a že je *protokol předávání* nastavený **jenom na https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Přidat dopředný postup pro přenosy HTTPS" border="false":::

1. Vyberte **Přidat** a uložte pravidlo směrování pro přesměrování požadavků.

1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**, abyste vytvořili profil front-dveří. Po vytvoření přejít na prostředek.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
- Přečtěte si další informace o [přesměrování adresy URL na front-dveřích](front-door-url-redirect.md).
