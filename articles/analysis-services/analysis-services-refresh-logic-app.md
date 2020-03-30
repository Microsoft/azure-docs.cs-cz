---
title: Aktualizace pomocí modelů logických aplikací pro Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak kód ovat asynchronní aktualizaci pro Služby Azure Analysis Services pomocí Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127023"
---
# <a name="refresh-with-logic-apps"></a>Aktualizace pomocí Logic Apps

Pomocí logic apps a volání REST můžete provádět automatizované operace aktualizace dat na tabulkových modelech Azure Analysis, včetně synchronizace replik jen pro čtení pro horizontální navýšení kapacity dotazu.

Další informace o používání rozhraní REST API se službou Azure Analysis Services najdete v [tématu Asynchronní aktualizace pomocí rozhraní REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Ověřování

Všechna volání musí být ověřena pomocí platného tokenu služby Azure Active Directory (OAuth 2).  Příklady v tomto článku budou používat instanční objekt (SPN) k ověření služby Azure Analysis Services. Další informace najdete [v tématu Vytvoření instančního objektu pomocí portálu Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Návrh aplikace logiky

> [!IMPORTANT]
> Následující příklady předpokládají, že brána firewall služby Azure Analysis Services je zakázána. Pokud je brána firewall povolena, musí být veřejná IP adresa iniciátoru požadavku uvedena na seznamu povolených v bráně firewall služby Azure Analysis Services. Další informace o rozsahech IP adres aplikací Azure Logic Apps v rámci oblasti najdete v [tématu Limity a informace o konfiguraci pro Aplikace Logika Azure](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Požadavky

#### <a name="create-a-service-principal-spn"></a>Vytvoření instančního objektu (SPN)

Další informace o vytvoření instančního objektu najdete [v tématu Vytvoření instančního objektu pomocí portálu Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurace oprávnění ve službě Azure Analysis Services
 
Instanční objekt, který vytvoříte, musí mít na serveru oprávnění správce serveru. Další informace naleznete [v tématu Přidání instančního objektu do role správce serveru](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurace aplikace logiky

V tomto příkladu je aplikace logiky navržena tak, aby se aktivovala při přijetí požadavku HTTP. To umožní použití nástroje orchestrace, jako je například Azure Data Factory, k aktivaci aktualizace modelu Služby Azure Analysis Services.

Po vytvoření aplikace logiky:

1. V návrháři aplikace logiky zvolte první akci jako **Při přijetí požadavku HTTP**.

   ![Přidat aktivitu přijatého protokolu HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Tento krok se naplní url HTTP POST po uložení aplikace logiky.

2. Přidejte nový krok a vyhledejte **protokol HTTP**.  

   ![Přidání aktivity PROTOKOLU HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Přidání aktivity PROTOKOLU HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Chcete-li přidat tuto akci, přidejte tuto akci vyberte **protokol HTTP.**

   ![Přidání aktivity PROTOKOLU HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Aktivitu protokolu HTTP nakonfigurujte následujícím způsobem:

|Vlastnost  |Hodnota  |
|---------|---------|
|**Metoda**     |POST         |
|**Identifikátor URI**     | https://*oblast serveru*/servery/ název serveru */modely/**název databáze*/aktualizuje <br /> <br /> Například: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Hlavičky**     |   Typ obsahu, aplikace/json <br /> <br />  ![Hlavičky](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Text**     |   Další informace o vytvoření těla požadavku naleznete v [tématu Asynchronní aktualizace pomocí rozhraní REST API - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Ověřování**     |Active Directory OAuth         |
|**Tenant**     |Vyplnění služby Azure Active Directory TenantId         |
|**Cílová skupina**     |https://*.asazure.windows.net         |
|**ID klienta**     |Zadejte název klienta hlavního_serveru služeb.         |
|**Typ pověření**     |Tajný kód         |
|**Tajný kód**     |Zadejte tajný klíč od hlavního názvu služby.         |

Příklad:

![Dokončená aktivita HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Nyní otestujte aplikaci logiky.  V návrháři aplikace logiky klikněte na **spustit**.

![Otestování aplikace logiky](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Využití aplikace logiky pomocí Azure Data Factory

Po uložení aplikace logiky zkontrolujte **Při přijetí požadavku HTTP** aktivity a zkopírujte adresu URL HTTP **POST,** která je nyní generována.  Toto je adresa URL, kterou může služba Azure Data Factory použít k vytvoření asynchronního volání pro aktivaci aplikace logiky.

Tady je příklad webové aktivity Azure Data Factory, která provádí tuto akci.

![Webová aktivita datové továrny](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Použití samostatné aplikace logiky

Pokud neplánujete používat nástroj Orchestrace, jako je například Data Factory k aktivaci aktualizace modelu, můžete nastavit aplikaci logiky pro aktivaci aktualizace na základě plánu.

Pomocí výše uvedeného příkladu odstraňte první aktivitu a nahraďte ji aktivitou **Plán.**

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/12.png)

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/13.png)

Tento příklad bude používat **opakování**.

Po přidání aktivity nakonfigurujte interval a frekvenci, přidejte nový parametr a **zvolte V těchto hodinách**.

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/16.png)

Vyberte hledané hodiny.

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/15.png)

Uložte aplikaci logiky.

## <a name="next-steps"></a>Další kroky

[ukázky](analysis-services-samples.md)  
[ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/analysisservices/servers)
