---
title: 'Rychlý Start: vytvoření profilu pro HA aplikací – Azure Portal – Azure Traffic Manager'
description: Tento článek rychlý Start popisuje, jak vytvořit profil Traffic Manager pro sestavení webové aplikace s vysokou dostupností pomocí Azure Portal.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 10/15/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 60facae195abd2808195f776ac6eef54ab63752f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534929"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Rychlý Start: vytvoření profilu Traffic Manager pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost vaší webové aplikace.

V tomto rychlém startu si přečtete o dvou instancích webové aplikace. Každý z nich je spuštěný v jiné oblasti Azure. Vytvoříte profil Traffic Manager na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz do primární lokality, na které běží webová aplikace. Traffic Manager nepřetržitě monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto rychlého startu budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure (*východní USA* a *západní Evropa*). Každý bude sloužit jako primární koncová body a koncové body převzetí služeb při selhání pro Traffic Manager

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V levé horní části obrazovky vyberte **vytvořit prostředek**. Vyhledejte **webovou aplikaci** a vyberte **vytvořit**.

1. V části **vytvořit webovou aplikaci** zadejte nebo vyberte následující hodnoty na kartě **základy** :

    | Nastavení                 | Hodnota |
    | ---                     | --- |
    | Předplatné            | Vyberte své předplatné. |    
    | Skupina prostředků          | Vyberte **vytvořit nový** a do textového pole zadejte *myResourceGroupTM1* .|
    | Name                    | Zadejte jedinečný **název** vaší webové aplikace. V tomto příkladu se používá *myWebAppEastUS*. |
    | Publikovat                 | Vyberte **Kód**. |
    | Zásobník modulu runtime           | Vyberte **ASP.NET v 4.7**. |
    | Operační systém        | Vyberte možnost **Windows**. |
    | Oblast                  | Vyberte **USA – východ**. |
    | Plán Windows            | Vyberte **vytvořit nový** a do textového pole zadejte *myAppServicePlanEastUS* . |
    | SKU a velikost            | Vyberte **Standard S1 100 celkem ACU, 1,75-GB paměti**. |
   
1. Vyberte kartu **monitorování** nebo vyberte **Další: monitorování**.  V části **monitorování** nastavte **Application Insights**  >  **Povolit Application Insights** na **ne**.

1. Vyberte možnost **zkontrolovat a vytvořit**.

1. Zkontrolujte nastavení a pak vyberte **vytvořit**.  Po úspěšném nasazení webové aplikace se vytvoří výchozí web.

1. Postupujte podle kroků 1-6 a vytvořte druhou webovou aplikaci s názvem *myWebAppWestEurope*. Název **skupiny prostředků** je *MyResourceGroupTM2*, s **oblastí** *západní Evropa* a **App Service název plánu** **myAppServicePlanWestEurope**. Všechna ostatní nastavení jsou stejná jako *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager, který přesměruje přenosy uživatelů na základě priority koncových bodů.

1. V levé horní části obrazovky vyberte **vytvořit prostředek**. Pak vyhledejte **profil Traffic Manager** a vyberte **vytvořit**.
1. V **profilu vytvořit Traffic Manager** zadejte nebo vyberte Tato nastavení:

    | Nastavení | Hodnota |
    | --------| ----- |
    | Název | Zadejte jedinečný název profilu Traffic Manager.|
    | Metoda směrování | Vyberte **Priorita**.|
    | Předplatné | Vyberte předplatné, pro které chcete profil Traffic Manageru použít. |
    | Skupina prostředků | Vyberte *myResourceGroupTM1*.|
    | Umístění |Toto nastavení odkazuje na umístění skupiny prostředků. Nemá žádný vliv na profil Traffic Manager, který se globálně nasadí.|

1. Vyberte **Vytvořit**.

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte web v oblasti *USA – východ* jako primární koncový bod, do kterého se bude směrovat veškerý provoz uživatelů. Přidejte web do *západní Evropa* jako koncový bod převzetí služeb při selhání. Když je primární koncový bod nedostupný, provoz se automaticky směruje na koncový bod převzetí služeb při selhání.

1. Na panelu hledání na portálu zadejte název profilu Traffic Manager, který jste vytvořili v předchozí části.
1. Vyberte profil z výsledků hledání.
1. V **Traffic Manager profil** v části **Nastavení** vyberte **koncové body** a pak vyberte **Přidat**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Nastavení koncového bodu v profilu Traffic Manager":::

1. Zadejte nebo vyberte Tato nastavení:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Typ | Vyberte **koncový bod Azure**. |
    | Name | Zadejte *myPrimaryEndpoint*. |
    | Typ cílového prostředku | Vyberte **App Service**. |
    | Cílový prostředek | Vyberte možnost **zvolit východní USA App Service**  >  . |
    | Priorita | Vyberte **1**. Veškerý provoz směřuje do tohoto koncového bodu, pokud je v pořádku. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Snímek obrazovky s přidáním koncového bodu do profilu Traffic Manager":::
    
1. Vyberte **OK**.
1. Pokud chcete pro druhou oblast Azure vytvořit koncový bod převzetí služeb při selhání, opakujte kroky 3 a 4 s těmito nastaveními:

    | Nastavení | Hodnota |
    | ------- | ------|
    | Typ | Vyberte **koncový bod Azure**. |
    | Name | Zadejte *myFailoverEndpoint*. |
    | Typ cílového prostředku | Vyberte **App Service**. |
    | Cílový prostředek | Vyberte možnost **zvolit západní Evropa App Service**  >  . |
    | Priorita | Vyberte **2**. Veškerý provoz přechází na tento koncový bod převzetí služeb při selhání, pokud primární koncový bod není v pořádku. |

1. Vyberte **OK**.

Až dokončíte přidávání těchto dvou koncových bodů, zobrazí se v **profilu Traffic Manager**. Všimněte si, že stav monitorování je nyní **online** .

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části zkontrolujete název domény vašeho profilu Traffic Manager. Také nakonfigurujete primární koncový bod jako nedostupný. Nakonec se zobrazí, že je webová aplikace stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

### <a name="check-the-dns-name"></a>Ověřte název DNS.

1. Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části.
1. Vyberte profil Traffic Manageru. Zobrazí se **Přehled** .
1. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Snímek obrazovky s umístěním názvu Traffic Manager DNS":::

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manager pro zobrazení výchozího webu webové aplikace.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrují do primárního koncového bodu. Je nastavená na **priority 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Snímek webové stránky s potvrzením dostupnosti profilu Traffic Manager":::

1. Pokud chcete zobrazit Traffic Manager převzetí služeb při selhání v akci, zakažte svoji primární lokalitu:
    1. Na stránce Traffic Manager profil vyberte v části **Přehled** možnost **myPrimaryEndpoint**.
    1. V *myPrimaryEndpoint* vyberte **zakázané**  >  **Uložit**.
    1. Zavřete **myPrimaryEndpoint**. Všimněte si, že stav je teď **zakázaný** .
1. Zkopírujte název DNS profilu Traffic Manager z předchozího kroku a zobrazte si web v nové relaci webového prohlížeče.
1. Ověřte, zda je webová aplikace stále k dispozici.

Primární koncový bod není k dispozici, takže jste byli přesměrováni na koncový bod převzetí služeb při selhání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, odstraňte skupiny prostředků, webové aplikace a všechny související prostředky. Provedete to tak, že na řídicím panelu vyberete každou jednotlivou položku a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manager. Umožňuje směrovat uživatelský provoz pro webové aplikace s vysokou dostupností. Pokud se chcete dozvědět víc o provozu směrování, přejděte k Traffic Manager kurzům.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
