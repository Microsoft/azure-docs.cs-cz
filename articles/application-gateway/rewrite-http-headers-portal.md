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
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947170"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Přepište hlaviček žádostí a odpovědí HTTP pomocí Azure Application Gateway – Azure portal

Tento článek popisuje, jak pomocí webu Azure portal ke konfiguraci [SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instance přepsání hlavičky protokolu HTTP v požadavky a odpovědi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

Musíte mít instanci Application Gateway SKU v2 k dokončení kroků v tomto článku. Přepsání hlavičky se nepodporuje v v1 SKU. Pokud nemáte k dispozici v2 SKU, vytvořte [SKU v2 Application Gateway](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instance před zahájením.

## <a name="create-required-objects"></a>Vytvoření požadovaných objektů

Pokud chcete nakonfigurovat přepsání hlavičky protokolu HTTP, budete muset dokončit tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlavičky protokolu HTTP:

   - **Přepsat akci**: Slouží k zadání požadavku a pole hlavičky požadavku, které chcete přepsat a novou hodnotu hlavičky. Můžete přidružit jeden nebo více přepsání pomocí akce přepsání.

   - **Přepsání stavu**: Volitelná konfigurace. Přepište podmínky vyhodnotí obsahu žádosti HTTP (S) a odpovědi. Akce revize se vrátí požadavku HTTP (S) nebo odpovědi odpovídá podmínce revize.

     Pokud přiřadíte více než jednu podmínku akci, dojde k akci pouze v případě, že jsou splněné všechny podmínky. Operace je jinými slovy, logické a operace.

   - **Přepsat pravidla**: Obsahuje více akce přepsání / přepisování kombinace podmínku.

   - **Pravidlo pořadí**: Pomáhá určit pořadí, ve kterém spuštění pravidla pro přepis adres. Tato konfigurace je užitečný, pokud máte více pravidla pro přepis adres v sadě revize. Pravidlo pro přepis adres, který má menší hodnotu pořadí pravidlo spustí první. Pokud přiřadíte stejnou hodnotu pořadí pravidlo na dvě pravidla pro přepis, pořadí spouštění je Nedeterministický.

   - **Přepsat nastavení**: Obsahuje více pravidla pro přepis adres, které mají být asociována s pravidlo směrování požadavku.

2. Připojte přepsání nastavení pravidel směrování. Konfigurace přepsání je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky souvisí s naslouchacím procesem zdroj a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. V takovém případě platí jenom pro konkrétní cesty oblasti lokality.

Můžete vytvořit více sad přepsání hlavičky protokolu HTTP a použije každé revize nastavena na víc naslouchacích procesů. Můžete ale použít pouze jeden přepsat nastavenou na konkrétní naslouchací proces.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="configure-header-rewrite"></a>Konfigurace hlaviček revize

V tomto příkladu upravíme adresy URL přesměrování pomocí přepsání hlavičky location v odpovědi HTTP odeslané aplikací back-end.

1. Vyberte **všechny prostředky**a pak vyberte vaši bránu application gateway.

2. Vyberte **přepíše** v levém podokně.

3. Vyberte **přepsání sady**:

   ![Přidat sadu revize](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Zadejte název pro sadu revize a přidružte jej k pravidlo směrování:

   - Zadejte název pro přepsání nastavení v **název** pole.
   - Vyberte jednu nebo více pravidel uvedených v **přidružená pravidla směrování** seznamu. Můžete vybrat jenom pravidla, které nebyly byla přidružena k jiné sady revize. Pravidla, které již byly přidruženy k jiné sady přepsání jsou neaktivní.
   - Vyberte **Další**.
   
     ![Přidejte název a přidružení](media/rewrite-http-headers-portal/name-and-association.png)

5. Vytvořte pravidlo pro přepis adres:

   - Vyberte **přidat pravidlo pro přepis adres**.

     ![Přidat pravidlo pro přepis adres](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Zadejte název pro pravidlo pro přepis adres v **název pravidla revize** pole. Zadejte číslo v **pravidlo pořadí** pole.

     ![Přidat název pravidla revize](media/rewrite-http-headers-portal/rule-name.png)

6. V tomto příkladu jsme budete přepsat hlavičku location pouze v případě, že obsahuje odkaz na azurewebsites.net. Chcete-li to provést, přidejte podmínku, která vyhodnotit, jestli azurewebsites.net obsahuje hlavičku location v odpovědi:

   - Vyberte **přidat podmínku** a pak vyberte pole obsahující **Pokud** pokyny a rozbalte ho.

     ![Přidat podmínku](media/rewrite-http-headers-portal/add-condition.png)

   - V **typ proměnné ke kontrole** seznamu vyberte **hlavičky protokolu HTTP**.

   - V **typ záhlaví** seznamu vyberte **odpovědi**.

   - Protože v tomto příkladu jsme budete vyhodnocovat využívání hlavičku location, což je běžné záhlaví, vyberte **společné hlavičky** pod **název hlavičky**.

   - V **společné hlavičky** seznamu vyberte **umístění**.

   - V části **malá a velká písmena**vyberte **ne**.

   - V **operátor** seznamu vyberte **rovná se (=)**.

   - Zadejte vzorek regulárního výrazu. V tomto příkladu použijeme vzor `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Vyberte **OK**.

     ![Pokud konfigurace podmínky](media/rewrite-http-headers-portal/condition.png)

7. Přidejte akci, kterou chcete přepsat hlavičku umístění:

   - V **typ akce** seznamu vyberte **nastavit**.

   - V **typ záhlaví** seznamu vyberte **odpovědi**.

   - V části **název hlavičky**vyberte **společné hlavičky**.

   - V **společné hlavičky** seznamu vyberte **umístění**.

   - Zadejte hodnotu hlavičky. V tomto příkladu použijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví. Tato hodnota nahradí *azurewebsites.net* s *contoso.com* hlavičky location.

   - Vyberte **OK**.

     ![Přidání akce](media/rewrite-http-headers-portal/action.png)

8. Vyberte **vytvořit** k vytvoření přepsání nastavení:

   ![Výběr možnosti Vytvořit](media/rewrite-http-headers-portal/create.png)

9. Otevře se zobrazení přepsání sady. Ověřte, zda sada revize, které jste vytvořili v seznamu sad revize:

   ![Přepsat nastavení zobrazení](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Další postup

Další informace o tom, jak nastavit některé běžné případy použití, naleznete v tématu [společné hlavičky revize scénáře](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).