---
title: 'Rychlý Start: vytvoření profilu pro HA aplikací – Azure Portal – Azure Traffic Manager'
description: Tento článek Rychlý start popisuje, jak vytvořit profil služby Traffic Manager umožňující vytváření vysoce dostupných webových aplikací.
services: traffic-manager
author: rohinkoul
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: rohink
ms.openlocfilehash: 559ed0a134bb6db78d1e89634138b4025e04152b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "76934768"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Rychlý Start: vytvoření profilu Traffic Manager pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost vaší webové aplikace.

V tomto rychlém startu si přečtete o dvou instancích webové aplikace. Každý z nich je spuštěný v jiné oblasti Azure. Vytvoříte profil Traffic Manager na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz do primární lokality, na které běží webová aplikace. Traffic Manager nepřetržitě monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto rychlého startu budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure (*východní USA* a *západní Evropa*). Každý bude sloužit jako primární koncová body a koncové body převzetí služeb při selhání pro Traffic Manager

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **Webová** > **Webová aplikace**.

1. V části **vytvořit webovou aplikaci**zadejte nebo vyberte následující hodnoty na kartě **základy** :

   - **Subscription** > **Skupina prostředků**předplatného: vyberte **vytvořit novou** a potom zadejte **myResourceGroupTM1**.
   - **Instance Details** > **Název**podrobností instance: zadejte *myWebAppEastUS*.
   - **Instance Details** > **Publikování**podrobností instance: vyberte **kód**.
   - **Zásobník podrobností** > instance**modulu runtime**: vyberte **ASP.NET v 4.7** .
   - **Podrobnosti** > instance**operační systém**: vyberte **Windows**.
   - **Instance Details** > **Oblast**podrobností instance: vyberte **východní USA**.
   - **App Service plán** > **Windows Plan (východní USA)**: vyberte **vytvořit novou** a potom zadejte **myAppServicePlanEastUS** .
   - **App Service Plan** > **SKU a velikost**plánu App Service: vyberte **standardní S1**.
   
3. Vyberte kartu **monitorování** nebo vyberte **Další: monitorování**.  V části **monitorování**nastavte **Application Insights** > **Povolit Application Insights** na **ne**.

4. Vybrat **kontrolu a vytvořit**

5. Zkontrolujte nastavení a klikněte na **vytvořit**.  Po úspěšném nasazení webové aplikace se vytvoří výchozí web.

6. Postupujte podle pokynů k vytvoření druhé webové aplikace s názvem *myWebAppWestEurope*s názvem **skupiny prostředků** *myResourceGroupTM2*, **oblastí** *západní Evropa*, názvem **App Service plánu** **myAppServicePlanWestEurope**a všemi ostatními nastaveními stejnými jako *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager, který přesměruje přenosy uživatelů na základě priority koncových bodů.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **sítě** > **Traffic Manager profil**.
2. V **profilu vytvořit Traffic Manager**zadejte nebo vyberte Tato nastavení:

    | Nastavení | Hodnota |
    | --------| ----- |
    | Název | Zadejte jedinečný název profilu Traffic Manager.|
    | Metoda směrování | Vyberte **Priorita**.|
    | Předplatné | Vyberte předplatné, pro které chcete profil Traffic Manageru použít. |
    | Skupina prostředků | Vyberte *myResourceGroupTM1*.|
    | Umístění |Toto nastavení odkazuje na umístění skupiny prostředků. Nemá žádný vliv na profil Traffic Manager, který se globálně nasadí.|

3. Vyberte **Vytvořit**.

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte web v oblasti *USA – východ* jako primární koncový bod, do kterého se bude směrovat veškerý provoz uživatelů. Přidejte web do *západní Evropa* jako koncový bod převzetí služeb při selhání. Když je primární koncový bod nedostupný, provoz se automaticky směruje na koncový bod převzetí služeb při selhání.

1. Na panelu hledání na portálu zadejte název profilu Traffic Manager, který jste vytvořili v předchozí části.
2. Vyberte profil z výsledků hledání.
3. V **Traffic Manager profil**v části **Nastavení** vyberte **koncové body**a pak vyberte **Přidat**.
4. Zadejte nebo vyberte Tato nastavení:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Typ | Vyberte **koncový bod Azure**. |
    | Název | Zadejte *myPrimaryEndpoint*. |
    | Typ cílového prostředku | Vyberte **App Service**. |
    | Cílový prostředek | Vyberte možnost zvolit**východní USA** **App Service** > . |
    | Priorita | Vyberte **1**. Veškerý provoz směřuje do tohoto koncového bodu, pokud je v pořádku. |

    ![Snímek obrazovky s přidáním koncového bodu do profilu Traffic Manager.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Vyberte **OK**.
6. Pokud chcete pro druhou oblast Azure vytvořit koncový bod převzetí služeb při selhání, opakujte kroky 3 a 4 s těmito nastaveními:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Typ | Vyberte **koncový bod Azure**. |
    | Název | Zadejte *myFailoverEndpoint*. |
    | Typ cílového prostředku | Vyberte **App Service**. |
    | Cílový prostředek | Vyberte možnost zvolit**západní Evropa** **App Service** > . |
    | Priorita | Vyberte **2**. Veškerý provoz přechází na tento koncový bod převzetí služeb při selhání, pokud primární koncový bod není v pořádku. |

7. Vyberte **OK**.

Až dokončíte přidávání těchto dvou koncových bodů, zobrazí se v **profilu Traffic Manager**. Všimněte si, že stav monitorování je nyní **online** .

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části zkontrolujete název domény vašeho profilu Traffic Manager. Také nakonfigurujete primární koncový bod jako nedostupný. Nakonec se zobrazí, že je webová aplikace stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

### <a name="check-the-dns-name"></a>Ověřte název DNS.

1. Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části.
2. Vyberte profil Traffic Manageru. Zobrazí se **Přehled** .
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager.
  
   ![Snímek obrazovky s umístěním názvu Traffic Manager DNS](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manager pro zobrazení výchozího webu webové aplikace.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrují do primárního koncového bodu. Je nastavená na **priority 1**.

    ![Snímek webové stránky s potvrzením dostupnosti profilu Traffic Manager](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Pokud chcete zobrazit Traffic Manager převzetí služeb při selhání v akci, zakažte svoji primární lokalitu:
    1. Na stránce Traffic Manager profil vyberte v části **Přehled** možnost **myPrimaryEndpoint**.
    2. V *myPrimaryEndpoint*vyberte **zakázané** > **Uložit**.
    3. Zavřete **myPrimaryEndpoint**. Všimněte si, že stav je teď **zakázaný** .
3. Zkopírujte název DNS profilu Traffic Manager z předchozího kroku a zobrazte si web v nové relaci webového prohlížeče.
4. Ověřte, zda je webová aplikace stále k dispozici.

Primární koncový bod není k dispozici, takže jste byli přesměrováni na koncový bod převzetí služeb při selhání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, odstraňte skupiny prostředků, webové aplikace a všechny související prostředky. Provedete to tak, že na řídicím panelu vyberete každou jednotlivou položku a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manager. Umožňuje směrovat uživatelský provoz pro webové aplikace s vysokou dostupností. Pokud se chcete dozvědět víc o provozu směrování, přejděte k Traffic Manager kurzům.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
