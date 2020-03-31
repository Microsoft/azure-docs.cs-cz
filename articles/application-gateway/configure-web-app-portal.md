---
title: Správa provozu do víceklientských aplikací pomocí portálu
titleSuffix: Azure Application Gateway
description: Tento článek obsahuje pokyny, jak nakonfigurovat webové aplikace služby Azure App service jako členy v back-endovém fondu na existující nebo nové aplikační bráně.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075165"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurace služby App Service pomocí brány aplikace

Vzhledem k tomu, že služba aplikace je služba s více klienty namísto vyprazdňuje nasazení, používá záhlaví hostitele v příchozí žádosti k vyřešení požadavku na správný koncový bod služby aplikace. Název DNS aplikace, což je název DNS přidružený k aplikační bráně, která fronting app service, se obvykle liší od názvu domény back-endapp služby. Hlavička hostitele v původním požadavku přijatém aplikační bránou proto není stejná jako název hostitele back-endové služby. Z tohoto důvodu, pokud záhlaví hostitele v požadavku z aplikační brány do back-endu je změněn na název hostitele back-endové služby, víceklientské back-endy nejsou schopni přeložit požadavek na správný koncový bod.

Aplikační brána poskytuje `Pick host name from backend address` přepínač, který je volán, který přepíše hlavičku hostitele v požadavku s názvem hostitele back-endu při směrování požadavku z aplikační brány do back-endu. Tato funkce umožňuje podporu pro víceklientské back-endy, jako je služba aplikace Azure a správa rozhraní API. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> - Vytvoření back-endového fondu a přidání služby App Service
> - Vytvoření nastavení protokolu HTTP a vlastní sondy s povolenými přepínači "Vybrat název_hostitele"

## <a name="prerequisites"></a>Požadavky

- Aplikační brána: Pokud nemáte existující aplikační bránu, přečtěte si, jak [vytvořit aplikační bránu](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Služba App Service: Pokud nemáte existující službu App service, přečtěte si [dokumentaci k aplikační službě](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Přidat službu App jako back-endový fond

1. Na webu Azure Portal otevřete konfigurační zobrazení aplikační brány.

2. V části **Back-end ové fondy**klikněte na **Přidat** a vytvořte nový back-endový fond.

3. Zadejte vhodný název back-endového fondu. 

4. V části **Cíle**klikněte na rozevírací seznam a jako možnost zvolte **Služby aplikací.**

5. Zobrazí se rozbalovací seznam bezprostředně pod rozevíracím seznamem **Cíle,** který bude obsahovat seznam vašich aplikačních služeb. V tomto rozevíracím souboru vyberte službu App Service, kterou chcete přidat jako člena back-endového fondu, a klikněte na Přidat.

   ![Back-end služby Aplikace](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > Rozevírací seznam naplní pouze ty služby aplikací, které jsou ve stejném předplatném jako vaše aplikační brána. Pokud chcete používat službu aplikace, která je v jiném předplatném, než ve kterém je aplikační brána, pak místo výběru **služby App Services** v rozevíracím **seznamu Cíle** zvolte MOŽNOST IP adresy nebo názvu **hostitele** a zadejte název hostitele (příklad. azurewebsites.net) služby aplikace.

## <a name="create-http-settings-for-app-service"></a>Vytvoření nastavení PROTOKOLU HTTP pro službu App Service

1. V části **Nastavení protokolu HTTP**klepněte na tlačítko **Přidat** a vytvořte nové nastavení PROTOKOLU HTTP.

2. Zadejte název nastavení HTTP a můžete povolit nebo zakázat spřažení založenou na souborech cookie podle vašeho požadavku.

3. Zvolte protokol jako HTTP nebo HTTPS podle případu použití. 

   > [!NOTE]
   > Pokud vyberete HTTPS, nemusíte nahrát žádný ověřovací certifikát nebo důvěryhodný kořenový certifikát do whitelistu back-endu služby app service, protože služba app je důvěryhodná služba Azure.

4. Zaškrtněte políčko **Použít pro službu App Service** . Všimněte si, že přepínače `Create a probe with pick host name from backend address` a `Pick host name from backend address` automaticky se aktivuje.`Pick host name from backend address` přepíše hlavičku hostitele v požadavku s názvem hostitele back-endu, když je požadavek směrován z aplikační brány do back-endu.  

   `Create a probe with pick host name from backend address`automaticky vytvoří sondu stavu a přidruží ji k tomuto nastavení HTTP. Není nutné vytvářet žádné jiné sondy stavu pro toto nastavení HTTP. Můžete zkontrolovat, zda byla do <HTTP Setting name> <Unique GUID> seznamu sond stavaře přidána nová sonda `Pick host name from backend http settings enabled`s názvem a již přepínač má .

   Pokud již máte jedno nebo více nastavení HTTP, které se používají pro službu App service, a pokud tato nastavení HTTP `Create a probe with pick host name from backend address` používají stejný protokol jako ten, který používáte v tom, který vytváříte, pak místo přepínače získáte rozbalovací seznam pro výběr jedné z vlastních sond . Je to proto, že vzhledem k tomu, že již existuje nastavení HTTP s `Pick host name from backend http settings enabled` aplikací služby, proto by také existovat sonda stavu, který má přepínač . V rozevíracím seznamu zvolte tuto vlastní sondu.

5. Chcete-li vytvořit nastavení PROTOKOLU HTTP, klepněte na tlačítko **OK.**

   ![Nastavení protokolu HTTP1](./media/configure-web-app-portal/http-setting1.png)

   ![Nastavení HTTP2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Vytvořit pravidlo pro navázání naslouchací proces, back-endový fond a nastavení HTTP

1. V **části Pravidla**klikněte na **Základní** a vytvořte nové základní pravidlo.

2. Zadejte vhodný název a vyberte naslouchací proces, který bude přijímat příchozí požadavky na službu App.

3. V rozbalovacím **fondu back-endu** vyberte fond back-endů, který jste vytvořili výše.

4. V rozevíracím souboru **nastavení HTTP** zvolte nastavení HTTP, které jste vytvořili výše.

5. Chcete-li toto pravidlo uložit, klepněte na tlačítko **OK.**

   ![Pravidlo](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Další konfigurace v případě přesměrování na relativní cestu služby App Service

Když služba aplikace odešle klientovi odpověď přesměrování na jeho relativní cestu (například přesměrování z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2), použije stejný název hostitele v záhlaví umístění své odpovědi jako v požadavku, který obdržel z aplikační brány. Takže klient bude žádost přímo contoso.azurewebsites.net/path2 namísto prochází aplikační brány (contoso.com/path2). Vynechání brány aplikace není žádoucí.

Pokud v případě použití existují scénáře, kde služba App bude muset odeslat odpověď přesměrování klientovi, proveďte [další kroky k přepsání hlavičky umístění](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Omezení přístupu

Webové aplikace nasazené v těchto příkladech používají veřejné IP adresy, ke kterým lze přistupovat přímo z Internetu. To pomáhá při řešení potíží, když se učíte o nové funkci a zkoušíte nové věci. Ale pokud máte v úmyslu nasadit funkci do produkčního prostředí, budete chtít přidat další omezení.

Jedním ze způsobů, jak omezit přístup k webovým aplikacím, je použití [statických omezení IP služby Azure App Service](../app-service/app-service-ip-restrictions.md). Můžete například omezit webovou aplikaci tak, aby přijínala pouze provoz z aplikační brány. Pomocí funkce omezení IP služby aplikace můžete vypsat virtuální ip adresu brány aplikace jako jedinou adresu s přístupem.

## <a name="next-steps"></a>Další kroky

Další informace o službě App service a další víceklientské podpoře s aplikační bránou najdete v [tématu podpora víceklientských služeb s aplikační bránou](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
