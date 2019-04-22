---
title: Přepište hlaviček žádostí a odpovědí HTTP pomocí Azure Application Gateway – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal ke konfiguraci služby Azure Application Gateway pro přepsání hlavičky protokolu HTTP v požadavky a odpovědi prostřednictvím brány
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 9d52114f5d01beca53ed48ee2114dc98cc20f3a2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682420"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Přepište hlaviček žádostí a odpovědí HTTP pomocí Azure Application Gateway – Azure portal

V tomto článku se dozvíte, jak pomocí webu Azure portal ke konfiguraci [SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) přepsání hlavičky protokolu HTTP v požadavky a odpovědi.

> [!IMPORTANT]
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

Musíte mít v2 s Application Gateway, skladovou Položku, protože možnost přepisování záhlaví není podporována pro v1 SKU. Pokud nemáte k dispozici v2 SKU, vytvořte [SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) předtím, než začnete.

## <a name="what-is-required-to-rewrite-a-header"></a>Co je potřeba přepsat hlavičku

Pokud chcete nakonfigurovat přepsání hlavičky protokolu HTTP, budete muset:

1. Vytvořte nové objekty požadované pro přepsání hlavičky protokolu http:

   - **Přepsat akci**: používá se k určení žádosti a pole hlavičky požadavku, které chcete přepsat a nové hodnoty, které musí být přepsány, aby původní záhlaví. Můžete přidružit jeden nebo další revize podmínku akce přepsání.

   - **Přepsání stavu**: To je volitelné konfigurace. Když je přidána podmínka revize, se vyhodnotí obsahu žádosti HTTP (S) a odpovědi. Rozhodnutí o přepsání akci spojené s podmínkou revize se budou zakládat, zda požadavek HTTP (S) nebo odpověď shoda s podmínkou revize. 

     Pokud více než jedné podmínky jsou přidružené k akci a potom akci spustí jenom v případě, že jsou splněny všechny podmínky, to znamená, logické a operace se provede.

   - **Přepsat pravidla**: pravidlo pro přepis adres obsahuje více akce přepsání - přepsat kombinace podmínku.

   - **Pravidlo pořadí**: pomáhá určit, jsou provedeny v pořadí, ve kterém různých pravidla pro přepis adres. To je užitečné, pokud existuje více pravidla pro přepis adres v sadě revize. Pravidlo pro přepis adres s nižší hodnotou pořadí pravidlo se nejprve provede. Pokud poskytují stejnou sekvenci pravidlo na dvě pravidla pro přepis adres budou pořadí provádění není deterministický.

   - **Přepsat nastavení**: obsahuje více přepsání pravidla, která bude přidružen k pravidlo směrování požadavku.

2. Budete muset připojit přepsání sady pomocí pravidel směrování. Je to proto, že konfigurace přepsání je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky je přidružený zdroj naslouchací proces a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. Ano platí jen pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad přepsání hlavičky protokolu http a každou sadu přepsání lze použít na víc naslouchacích procesů. Můžete však použít jen jeden přepsat nastavenou na konkrétním posluchačem.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="configure-header-rewrite"></a>Konfigurace hlaviček revize

V tomto příkladu upravíme přesměrovací adresu URL pomocí přepsání hlavičky location v odpovědi http odeslané aplikací back-endu. 

1. Vyberte **všechny prostředky**a pak vyberte vaši bránu application gateway.

2. Vyberte **přepíše** v levé nabídce.

3. Klikněte na **+ přepsání sady**. 

   ![Přidat sadu revize](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Zadejte název do sady revize a přidružte jej k pravidlo směrování:

   - Zadejte název nastavena v přepsání **název** textového pole.
   - Vyberte jedno nebo více pravidel uvedených v **přidružená pravidla směrování** seznamu. Můžete vybrat pouze tato pravidla, které nebyly byla přidružena k jiné sady revize. Pravidla, která již byly přidruženy k jiné sady revize zobrazí šedě.
   - Klikněte na Další.
   
     ![Přidejte název a přidružení](media/rewrite-http-headers-portal/name-and-association.png)

5. Vytvořte pravidlo pro přepis adres:

   - Klikněte na **+ přidat pravidlo pro přepis adres**.![ Přidat pravidlo pro přepis adres](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Zadejte název pro pravidlo pro přepis adres do textového pole Název pravidla přepsání a zadejte pravidlo pořadí.![Přidat název pravidla](media/rewrite-http-headers-portal/rule-name.png)

6. V tomto příkladu jsme přepsat hlavičku location pouze v případě, že obsahuje odkaz na "azurewebsites.net". Chcete-li to provést, přidejte podmínku, která vyhodnotit, jestli azurewebsites.net obsahuje hlavičku location v odpovědi:

   - Klikněte na **+ přidat podmínku** a potom klikněte na část s **Pokud** pokynů a rozbalte ho.![ Přidat název pravidla](media/rewrite-http-headers-portal/add-condition.png)

   - Vyberte **hlavičky protokolu HTTP** z **typ proměnné ke kontrole** rozevíracího seznamu. 

   - Vyberte **typ záhlaví** jako **odpovědi**.

   - Protože v tomto příkladu jsme hlavičku location, která je běžné záhlaví, vyberte hodnocení **společné hlavičky** přepínač jako **název hlavičky**.

   - Vyberte **umístění** z **společné hlavičky** rozevíracího seznamu.

   - Vyberte **ne** jako **malá a velká písmena** nastavení.

   - Vyberte **rovná se (=)** z **operátor** rozevíracího seznamu.

   - Zadejte vzorek regulárního výrazu. V tomto příkladu použijeme vzor `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Klikněte na **OK**.

     ![Upravit hlavičku umístění](media/rewrite-http-headers-portal/condition.png)

7. Přidejte akci, kterou chcete přepsat hlavičku umístění:

   - Vyberte **nastavit** jako **typ akce**.

   - Vyberte **odpovědi** jako **typ záhlaví**.

   - Vyberte **společné hlavičky** jako **název hlavičky**.

   - Vyberte **umístění** z **společné hlavičky** rozevíracího seznamu.

   - Zadejte hodnotu hlavičky. V tomto příkladu budeme používat `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví. Tato operace nahradí *azurewebsites.net* s *contoso.com* hlavičky location.

   - Klikněte na **OK**.

     ![Upravit hlavičku umístění](media/rewrite-http-headers-portal/action.png)

8. Klikněte na **vytvořit** k vytvoření přepsání nastavení.

   ![Upravit hlavičku umístění](media/rewrite-http-headers-portal/create.png)

9. Budete přesměrováni na zobrazení přepsání sady. Ověřte, zda je k dispozici v seznamu sad revize revize sadu, do které jste vytvořili výše.

   ![Upravit hlavičku umístění](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci potřebných k provedení některé nejběžnější případy použití, naleznete v tématu [společné hlavičky revize scénáře](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
