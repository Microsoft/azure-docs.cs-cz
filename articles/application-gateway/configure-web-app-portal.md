---
title: Správa provozu do aplikací víceklientské aplikace pomocí portálu
titleSuffix: Azure Application Gateway
description: V tomto článku najdete pokyny, jak nakonfigurovat službu Azure App Service Web Apps jako členy ve fondu back-endu v existující nebo nové službě Application Gateway.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: c538b6e4cf27b992500c079ad9c914c05d55eb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854906"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurace App Service s využitím Application Gateway

Vzhledem k tomu, že je App Service víceklientská služba, a ne služba s vyhrazeným nasazením, využívá k překladu požadavku na správný koncový bod služby App Service hlavičku hostitele v příchozím požadavku. Název DNS aplikace, který je zase název DNS přidružený ke službě Application Gateway front-App Service, se obvykle liší od názvu domény služby back-end App Service. Proto Hlavička hostitele v původní žádosti přijatá aplikační bránou není stejná jako název hostitele back-end služby. Z tohoto důvodu se v případě, že Hlavička hostitele v požadavku od služby Application Gateway do back-endu změní na název hostitele back-end služby, nedokáže aplikace back-end s více klienty přeložit požadavek na správný koncový bod.

Application Gateway poskytuje přepínač s názvem, `Pick host name from backend target` který Přepisuje hlavičku hostitele v žádosti názvem hostitele back-endu při směrování požadavku z Application Gateway do back-endu. Tato schopnost povoluje podporu pro back-endové back-endové služby, jako je Azure App Service a API Management. 

V tomto článku získáte informace o těchto tématech:

- Upravte back-end fond a přidejte do něj App Service.
- Upravit nastavení HTTP s povoleným přepínačem vybrat hostitele

## <a name="prerequisites"></a>Požadavky

- Application Gateway: Vytvořte Aplikační bránu bez cíle back-end fondu. Další informace najdete v tématu [rychlý Start: směrování webového provozu pomocí Azure Application Gateway-Azure Portal](quick-create-portal.md)

- App Service: Pokud nemáte existující službu App Service, přečtěte si téma [dokumentace ke službě App Service](../app-service/index.yml).

## <a name="add-app-service-as-backend-pool"></a>Přidat službu App Service jako back-end fond

1. V Azure Portal vyberte svou Aplikační bránu.

2. V části **fondy back-endu** vyberte fond back-end.

4. V části **cílový typ** vyberte **App Services**.

5. V části **cíl** vyberte svůj App Service.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="Back-end App Service":::
   
   > [!NOTE]
   > Rozevírací seznam naplní jenom ty služby App Services, které jsou ve stejném předplatném jako vaše Application Gateway. Pokud chcete použít službu App Service, která je v jiném předplatném, než je ta, ve které je Application Gateway, vyberte možnost **App Services** v rozevíracím seznamu **cíle** , zvolte **IP adresu nebo název hostitele** a zadejte název hostitele (příklad. azurewebsites.net) služby App Service.
1. Vyberte **Uložit**.

## <a name="edit-http-settings-for-app-service"></a>Upravit nastavení HTTP pro App Service

1. V části **nastavení protokolu HTTP** vyberte existující nastavení http.

2. V části **přepsat novým názvem hostitele** vyberte **Ano**.
3. V části **přepsat název hostitele** vyberte vybrat **název hostitele z back-endu cíle**.
4. Vyberte **Uložit**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Vyberte název hostitele z nastavení http back-endu.":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Další konfigurace v případě přesměrování na relativní cestu služby App Service

Když služba App Service pošle klientovi odezvu přesměrování na přesměrování na jeho relativní cestu (například přesměrování z `contoso.azurewebsites.net/path1` na `contoso.azurewebsites.net/path2` ), používá stejný název hostitele v hlavičce umístění odpovědi jako ten v požadavku, který obdržel od služby Application Gateway. Proto klient provede požadavek přímo na místo průchodu `contoso.azurewebsites.net/path2` přes Aplikační bránu ( `contoso.com/path2` ). Obcházení aplikační brány není žádoucí.

Pokud v případu použití existují situace, kdy bude služba App Service potřebovat poslat klientovi odezvu přesměrování, proveďte [Další kroky, abyste přepsali hlavičku umístění](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration).

## <a name="restrict-access"></a>Omezení přístupu

Webové aplikace nasazené v těchto příkladech používají veřejné IP adresy, ke kterým lze získat přímý pøístup z Internetu. Pomůže vám to při řešení potíží, když se naučíte o nové funkci a zkoušíme nové věci. Pokud ale máte v úmyslu nasadit funkci do produkčního prostředí, budete chtít přidat další omezení.

Jedním ze způsobů, jak omezit přístup k webovým aplikacím, je použít [Azure App Service omezení statických IP adres](../app-service/app-service-ip-restrictions.md). Můžete například omezit webovou aplikaci tak, aby přijímala pouze provoz z aplikační brány. Pomocí funkce omezení IP adres služby App Service můžete zobrazit seznam VIP adres Application Gateway jako jedinou adresu s přístupem.

## <a name="next-steps"></a>Další kroky

Další informace o službě App Service a dalších víceklientské podpoře k aplikační bráně najdete v tématu [Podpora víceklientské služby s aplikační bránou](./application-gateway-web-app-overview.md).
