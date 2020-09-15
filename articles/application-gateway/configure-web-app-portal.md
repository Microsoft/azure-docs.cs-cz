---
title: Správa provozu do aplikací víceklientské aplikace pomocí portálu
titleSuffix: Azure Application Gateway
description: V tomto článku najdete pokyny, jak nakonfigurovat službu Azure App Service Web Apps jako členy ve fondu back-endu v existující nebo nové službě Application Gateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: absha
ms.openlocfilehash: dbaad0f6639d65d88da6847886d3aa3d39b93e82
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563749"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurace App Service s využitím Application Gateway

Vzhledem k tomu, že App Service je víceklientské služby místo nasazení, používá se v příchozím požadavku Hlavička hostitele k vyřešení požadavku na správný koncový bod služby App Service. Název DNS aplikace, který je zase název DNS přidružený ke službě Application Gateway front-App Service, se obvykle liší od názvu domény služby back-end App Service. Proto Hlavička hostitele v původní žádosti přijatá aplikační bránou není stejná jako název hostitele back-end služby. Z tohoto důvodu se v případě, že Hlavička hostitele v požadavku od služby Application Gateway do back-endu změní na název hostitele back-end služby, nedokáže aplikace back-end s více klienty přeložit požadavek na správný koncový bod.

Application Gateway poskytuje přepínač s názvem, `Pick host name from backend address` který Přepisuje hlavičku hostitele v žádosti názvem hostitele back-endu při směrování požadavku z Application Gateway do back-endu. Tato schopnost povoluje podporu pro back-endové back-endové služby, jako je Azure App Service a API Management. 

V tomto článku získáte informace o těchto tématech:

- Vytvořte back-end fond a přidejte do něj App Service.
- Vytvoření nastavení HTTP a vlastního testu s povolenými přepínači vybrat hostitele

## <a name="prerequisites"></a>Požadavky

- Aplikační brána: Pokud nemáte existující Aplikační bránu, přečtěte si téma Postup [Vytvoření aplikační brány](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) .
- App Service: Pokud nemáte existující službu App Service, přečtěte si téma [dokumentace ke službě App Service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Přidat službu App Service jako back-end fond

1. V Azure Portal otevřete zobrazení konfigurace služby Application Gateway.

2. V části **fondy back-endu**klikněte na **Přidat** a vytvořte nový fond back-end.

3. Zadejte vhodný název do fondu back-end. 

4. V části **cíle**klikněte na rozevírací seznam a jako možnost vyberte **App Services** .

5. Zobrazí se rozevírací seznam hned pod rozevíracím seznamem **cílů**  , který bude obsahovat seznam App Services. V tomto rozevíracím seznamu vyberte App Service, který chcete přidat jako člena fondu back-end, a klikněte na Přidat.

   ![Back-end App Service](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > V rozevíracím seznamu se naplní jenom ty služby App Services, které jsou ve stejném předplatném jako vaše Application Gateway. Pokud chcete použít službu App Service, která je v jiném předplatném, než je ta, ve které je Application Gateway, vyberte možnost **App Services** v rozevíracím seznamu **cíle** , zvolte **IP adresu nebo název hostitele** a zadejte název hostitele (příklad. azurewebsites.net) služby App Service.

## <a name="create-http-settings-for-app-service"></a>Vytvoření nastavení HTTP pro službu App Service

1. V části **Nastavení http**klikněte na **Přidat** a vytvořte nové nastavení http.

2. Zadejte název nastavení HTTP a můžete povolit nebo zakázat spřažení na základě souborů cookie podle vašich požadavků.

3. Jako na základě vašeho případu použití vyberte protokol HTTP nebo HTTPS. 

   > [!NOTE]
   > Pokud vyberete HTTPS, nemusíte nahrávat žádný ověřovací certifikát ani důvěryhodný kořenový certifikát, aby bylo možné službu App Service povolit, protože App Service je důvěryhodná služba Azure.

4. Zaškrtněte políčko pro **použití pro App Service** . Všimněte si, že přepínače  `Create a probe with pick host name from backend address` a `Pick host name from backend address` budou automaticky povoleny.`Pick host name from backend address` přepíše hlavičku hostitele v žádosti s názvem hostitele back-endu, pokud je požadavek směrován z Application Gateway do back-endu.  

   `Create a probe with pick host name from backend address` automaticky vytvoří sondu stavu a přidruží ho k tomuto nastavení HTTP. Pro toto nastavení HTTP nemusíte vytvářet žádné další testy stavu. Můžete ověřit, že se <HTTP Setting name> <Unique GUID> v seznamu sond stavu přidala nová sonda s názvem a že tento přepínač už má `Pick host name from backend http settings enabled` .

   Pokud už máte jedno nebo víc nastavení HTTP, které se používá pro App Service, a pokud tato nastavení HTTP používají stejný protokol jako ten, který používáte v jednom vytvořeném, pak místo `Create a probe with pick host name from backend address` přepínače získáte rozevírací seznam pro výběr jedné z vlastních sond. Důvodem je to, že už existuje nastavení HTTP ve službě App Service, proto by existovala také sonda stavu, která má přepínač `Pick host name from backend http settings enabled` . Z rozevíracího seznamu vyberte tento vlastní test paměti.

5. Kliknutím na tlačítko **OK** vytvořte nastavení protokolu HTTP.

   ![Snímek obrazovky se zobrazí podokno nastavení přidat H t T T, které se používá pro App Service a možnost OK.](./media/configure-web-app-portal/http-setting1.png)

   ![Snímek obrazovky ukazuje test stavu s vybraným názvem hostitele ve formátu back-endu http nastavení.](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Vytvořit pravidlo pro provázání naslouchacího procesu, back-end fondu a nastavení HTTP

1. V části **pravidla**klikněte na **Basic** a vytvořte nové základní pravidlo.

2. Zadejte vhodný název a vyberte naslouchací proces, který bude přijímat příchozí žádosti pro službu App Service.

3. V rozevíracím seznamu **fond back-endu** vyberte back-end fond, který jste vytvořili výše.

4. V rozevíracím seznamu **Nastavení http** vyberte nastavení HTTP, které jste vytvořili výše.

5. Kliknutím na tlačítko **OK** toto pravidlo uložte.

   ![Snímek obrazovky se zvýrazněným nastavením pro okno Přidat základní pravidlo s vybraným modulem pro naslouchání, back-end fondem a H T T P](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Další konfigurace v případě přesměrování na relativní cestu služby App Service

Když služba App Service pošle klientovi odezvu přesměrování na přesměrování na jeho relativní cestu (například přesměrování z contoso.azurewebsites.net/path1 na contoso.azurewebsites.net/path2), používá stejný název hostitele v hlavičce umístění odpovědi jako ten v požadavku, který obdržel od služby Application Gateway. Proto klient provede požadavek přímo na contoso.azurewebsites.net/path2 namísto průchodu přes Aplikační bránu (contoso.com/path2). Obcházení aplikační brány není žádoucí.

Pokud v případu použití existují situace, kdy bude služba App Service potřebovat poslat klientovi odezvu přesměrování, proveďte [Další kroky, abyste přepsali hlavičku umístění](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Omezení přístupu

Webové aplikace nasazené v těchto příkladech používají veřejné IP adresy, ke kterým lze získat přímý pøístup z Internetu. Pomůže vám to při řešení potíží, když se naučíte o nové funkci a zkoušíme nové věci. Pokud ale máte v úmyslu nasadit funkci do produkčního prostředí, budete chtít přidat další omezení.

Jedním ze způsobů, jak omezit přístup k webovým aplikacím, je použít [Azure App Service omezení statických IP adres](../app-service/app-service-ip-restrictions.md). Můžete například omezit webovou aplikaci tak, aby přijímala pouze provoz z aplikační brány. Pomocí funkce omezení IP adres služby App Service můžete zobrazit seznam VIP adres Application Gateway jako jedinou adresu s přístupem.

## <a name="next-steps"></a>Další kroky

Další informace o službě App Service a dalších víceklientské podpoře k aplikační bráně najdete v tématu [Podpora víceklientské služby s aplikační bránou](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
