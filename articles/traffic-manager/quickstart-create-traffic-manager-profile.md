---
title: Úvodní příručka:Vytvoření profilu pro informace o projektu HA aplikací – portál Azure – Azure Traffic Manager
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934768"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Úvodní příručka: Vytvoření profilu Traffic Manageru pomocí portálu Azure

Tento rychlý start popisuje, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost pro webovou aplikaci.

V tomto rychlém startu si přečtete o dvou instancích webové aplikace. Každý z nich běží v jiné oblasti Azure. Vytvoříte profil Traffic Manageru na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz na primární web se spuštěnou webovou aplikací. Traffic Manager průběžně monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Pro tento rychlý start budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure *(Východní USA* a *Západní Evropa).* Každý bude sloužit jako primární a převzetí služeb při selhání koncové body pro Traffic Manager.

1. Na levé horní straně obrazovky vyberte Vytvořit > **webovou** > **webovou aplikaci** **pro prostředky**.

1. Do **pole Vytvořit webovou aplikaci**zadejte nebo vyberte na kartě **Základy** následující hodnoty:

   - **Skupina** > **prostředků odběru**: Vyberte **vytvořit nový** a zadejte **myResourceGroupTM1**.
   - **Název podrobností instance** > **Name**: Zadejte *myWebAppEastUS*.
   - **Podrobnosti instance** > **Publikování**: Vyberte **kód**.
   - **Instancí Podrobnosti** > **Runtime zásobníku**: Výběr **ASP.NET V4.7**
   - **Podrobnosti o** > **instanci Operační systém**: Vyberte systém **Windows**.
   - **Instance Details** > **Oblast**podrobností instance : Vyberte **možnost Východ USA**.
   - **Plán služby App Service** > **Plán Windows (Východní USA):** Vyberte **Vytvořit nový** a zadejte **myAppServicePlanEastUS**
   - **Sku** > **a velikost**plánu služeb aplikace : Vyberte **standardní S1**.
   
3. Vyberte kartu **Monitorování** nebo vyberte **Další:Sledování**.  V části **Monitorování**nastavte **přehledy** > aplikací**povolit přehledy aplikací** na **ne**.

4. Vyberte **Zkontrolovat a vytvořit**

5. Zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**.  Když webová aplikace úspěšně nasadí, vytvoří výchozí web.

6. Podle pokynů vytvořte druhou webovou aplikaci s názvem *myWebAppWestEurope*s názvem **Skupiny prostředků** *myResourceGroupTM2*, **oblast** *západní Evropy*, název **plánu služby App Service** **myAppServicePlanWestEurope**a všechna ostatní nastavení stejná jako *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manageru, který směruje provoz uživatelů na základě priority koncového bodu.

1. Na levé horní straně obrazovky vyberte Vytvořit profil**správce** > **provozu** **sítě .** > 
2. V **profilu Vytvořit traffic manager**zadejte nebo vyberte tato nastavení:

    | Nastavení | Hodnota |
    | --------| ----- |
    | Name (Název) | Zadejte jedinečný název profilu traffic manageru.|
    | Metoda směrování | Vyberte **prioritu**.|
    | Předplatné | Vyberte předplatné, na které chcete použít profil správce provozu. |
    | Skupina prostředků | Vyberte *myResourceGroupTM1*.|
    | Umístění |Toto nastavení odkazuje na umístění skupiny prostředků. Nemá žádný vliv na profil Traffic Manager, který bude nasazen globálně.|

3. Vyberte **Vytvořit**.

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte web v oblasti *USA – východ* jako primární koncový bod, do kterého se bude směrovat veškerý provoz uživatelů. Přidejte web v *západní Evropě* jako koncový bod převzetí služeb při selhání. Pokud primární koncový bod není k dispozici, provoz automaticky směruje do koncového bodu převzetí služeb při selhání.

1. Na vyhledávacím panelu portálu zadejte název profilu Traffic Manageru, který jste vytvořili v předchozí části.
2. Ve výsledcích hledání vyberte profil.
3. V **profilu Traffic Manageru**vyberte v části **Nastavení** **koncové body**a pak vyberte **Přidat**.
4. Zadejte nebo vyberte tato nastavení:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Typ | Vyberte **koncový bod Azure**. |
    | Name (Název) | Zadejte *myPrimaryEndpoint*. |
    | Typ cílového prostředku | Vyberte **službu App Service**. |
    | Cílový prostředek | Vyberte **Zvolit službu aplikace** > **East US**. |
    | Priorita | Vyberte **1**. Veškerý provoz přejde do tohoto koncového bodu, když je v pořádku. |

    ![Snímek obrazovky s místem, kde přidáte koncový bod do profilu Traffic Manageru.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Vyberte **OK**.
6. Chcete-li vytvořit koncový bod převzetí služeb při selhání pro druhou oblast Azure, opakujte kroky 3 a 4 s těmito nastaveními:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Typ | Vyberte **koncový bod Azure**. |
    | Name (Název) | Zadejte *myFailoverEndpoint*. |
    | Typ cílového prostředku | Vyberte **službu App Service**. |
    | Cílový prostředek | Vyberte **Zvolit službu aplikace** > –**západní Evropa**. |
    | Priorita | Vyberte **2**. Veškerý provoz přejde do tohoto koncového bodu převzetí služeb při selhání, pokud primární koncový bod není v pořádku. |

7. Vyberte **OK**.

Po přidání dvou koncových bodů se zobrazí v **profilu Traffic Manageru**. Všimněte si, že jejich stav monitorování je nyní **online.**

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části zkontrolujete název domény vašeho profilu Traffic Manageru. Nakonfigurujete také primární koncový bod tak, aby nebyl k dispozici. Nakonec uvidíte, že webová aplikace je stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

### <a name="check-the-dns-name"></a>Kontrola názvu DNS

1. Na vyhledávacím panelu portálu vyhledejte název **profilu Traffic Manageru,** který jste vytvořili v předchozí části.
2. Vyberte profil správce provozu. Zobrazí se **přehled.**
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager.
  
   ![Snímek obrazovky s umístěním názvu DNS traffic manageru](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manageru a zobrazte výchozí web webové aplikace Web Appu.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrovat k primární koncový bod. Je nastavena na **prioritu 1**.

    ![Snímek obrazovky s webovou stránkou pro potvrzení dostupnosti profilu Traffic Manageru](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Chcete-li zobrazit převzetí služeb při selhání traffic manageru v akci, zakažte primární web:
    1. Na stránce Profil traffic manageru vyberte v části **Přehled** **myPrimaryEndpoint**.
    2. V *části myPrimaryEndpoint*vyberte **možnost Zakázáno** > **uložit**.
    3. Zavřete **myPrimaryEndpoint**. Všimněte si, že stav je nyní **zakázán.**
3. Zkopírujte název DNS profilu traffic manageru z předchozího kroku a zobrazte web v nové relaci webového prohlížeče.
4. Ověřte, zda je webová aplikace stále dostupná.

Primární koncový bod není k dispozici, takže jste byli směrováni do koncového bodu převzetí služeb při selhání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení odstraňte skupiny prostředků, webové aplikace a všechny související prostředky. Chcete-li tak učinit, vyberte jednotlivé položky z řídicího panelu a v horní části každé stránky vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manageru. To vám umožní řídit provoz uživatelů pro vysoce dostupnost webových aplikací. Další informace o směrování provozu, pokračujte v kurzech Traffic Manager.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
