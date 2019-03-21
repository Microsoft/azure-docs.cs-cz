---
title: Spravovat provoz do aplikace s více tenanty jako jsou App service web apps s Azure Application Gateway – portál
description: Tento článek obsahuje pokyny o tom, jak nakonfigurovat jako členy v back-endového fondu ve službě application gateway existujících nebo nových aplikací Azure App service web apps.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176204"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurace služby App Service pomocí služby Application Gateway

Služba Application gateway umožňuje mít jako člena fondu back endové webové aplikace Azure App service nebo jiné služby pro více tenantů. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> - Vytvoření back-endového fondu a přidejte do ní služby App Service
> - Vytvoření nastavení HTTP a vlastní sondy pomocí přepínače "Vybrat název hostitele" povoleno

## <a name="prerequisites"></a>Požadavky

- Služba Application gateway: Pokud nemáte existující aplikační bráně, naleznete v tématu Jak [vytvoření služby application gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Služby App service: Pokud nemáte stávající službu App service, najdete v článku [dokumentace ke službě App service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Přidání služby App service jako back-endový fond

1. Na webu Azure Portal otevřete zobrazení konfigurace služby application gateway.

2. V části **back-endové fondy**, klikněte na **přidat** k vytvoření nového back-endový fond.

3. Zadejte vhodný název pro back-endový fond. 

4. V části **cíle**, klikněte na rozevírací seznam a zvolte **App Services** jako možnost.

5. Bezprostředně pod rozevírací seznam **cíle** rozevíracího seznamu se zobrazí, která bude obsahovat seznam App Services. V tomto rozevíracím seznamu zvolte App Service, kterou chcete přidat jako člena fondu back-endu a klikněte na tlačítko Přidat.

   ![Back-end aplikace app service](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Vytvoření nastavení protokolu HTTP pro službu App service

1. V části **nastavení HTTP**, klikněte na tlačítko **přidat** chcete vytvořit nové nastavení protokolu HTTP.

2. Zadejte název pro nastavení HTTP a můžete povolit nebo zakázat spřažení na základě souborů Cookie podle vašich požadavků.

3. Vyberte protokol HTTP nebo HTTPS podle vašemu případu použití. 

4. Zaškrtněte políčko u **použití pro službu App Service** a bude zapnout **vytvořte sondu s názvem hostitele výběru z back-endová adresa** a **vybrat název hostitele z back-endová adresa** možnosti. Tento příkaz také automaticky vytvořte test s přepínačem povolená a přidružit ho k tomuto nastavení HTTP.

5. Klikněte na tlačítko **OK** vytváření nastavení protokolu HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Vytvořte pravidlo, které spojí naslouchací proces, back-Endového fondu a nastavení HTTP

1. V části **pravidla**, klikněte na tlačítko **základní** k vytvoření nové základní pravidlo.

2. Zadejte vhodný název a vyberte naslouchací proces, který bude přijímat příchozí požadavky pro službu App service.

3. V **back-endový fond** rozevíracím seznamu vyberte fond back-endu, kterou jste vytvořili výše.

4. V **nastavení HTTP** rozevírací seznam, vyberte HTTP nastavení, kterou jste vytvořili výše.

5. Klikněte na tlačítko **OK** toto pravidlo uložte.

   ![Pravidlo](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Omezení přístupu

Webové aplikace nasazené v těchto příkladech použít veřejné IP adresy, které lze přistupovat přímo z Internetu. To pomáhá při řešení potíží, když se budete učit o nové funkce a zkusit nové věci. Ale pokud máte v úmyslu nasadit funkci do produkčního prostředí, budete chtít přidat další omezení.

Jedním ze způsobů, můžete omezit přístup k webovým aplikacím je použití [omezení statických IP adres služby Azure App Service](../app-service/app-service-ip-restrictions.md). Například můžete omezit webové aplikace tak, aby pouze přijímá provoz z aplikační brány. Pomocí funkce app service IP omezení seznamu application gateway virtuální IP adresy jako adresu pouze s přístupem.

## <a name="next-steps"></a>Další postup

Další informace o App service a další podporou více tenantů pomocí služby application gateway najdete v tématu [podpora víceklientské služby pomocí služby application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

V případě, že odpověď ze služby App service je přesměrování na adresu URL služby App service, naleznete v tématu Jak [řešení potíží s chybou služby App service adresy URL přesměrování](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
