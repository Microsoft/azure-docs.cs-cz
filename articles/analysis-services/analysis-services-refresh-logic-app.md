---
title: Aktualizovat pomocí funkce Logic Apps pro modely služby Azure Analysis Services | Dokumentace Microsoftu
description: Zjistěte, jak kód asynchronní aktualizace s využitím Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6e1ac5dfd1972e406a1bd8dcd26e6aef2c4ea6d1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919866"
---
# <a name="refresh-with-logic-apps"></a>Aktualizovat pomocí funkce Logic Apps

Pomocí Logic Apps a volání REST, můžete provádět operace aktualizace automatizované data v tabulkové modely vaší analýzy Azure, včetně synchronizace repliky jen pro čtení pro horizontální navýšení kapacity dotazu.

Další informace o použití rozhraní REST API služby Azure Analysis Services najdete v tématu [asynchronní aktualizace pomocí REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Všechna volání musí být ověřené na platný token Azure Active Directory (OAuth 2).  V příkladech v tomto článku budete používat k ověřování do služby Azure Analysis Services hlavní služby (SPN). Další informace najdete v tématu [vytvoření instančního objektu pomocí webu Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Návrh aplikace logiky

> [!IMPORTANT]
> Následující příklady předpokládají, že brána firewall služby Azure Analysis Services je zakázaná.  Pokud je povolená brána firewall, musí být veřejnou IP adresu iniciátoru žádost o seznam povolených adres v bráně firewall služby Azure Analysis Services. Další informace o IP aplikace logiky rozsahy v jedné oblasti, naleznete v tématu [omezení a konfigurační informace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Požadavky

#### <a name="create-a-service-principal-spn"></a>Vytvoření hlavního názvu služby (SPN)

Další informace o vytvoření instančního objektu najdete v tématu [vytvoření instančního objektu pomocí webu Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurace oprávnění ve službě Azure Analysis Services
 
Instanční objekt služby, které vytvoříte, musí mít oprávnění správce serveru na serveru. Další informace najdete v tématu [přidání hlavního názvu služby k roli správce serveru](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurace aplikace logiky

V tomto příkladu aplikace logiky slouží k aktivaci při přijetí požadavku HTTP. To vám umožní použít nástroj pro orchestraci, jako je například Azure Data Factory pro aktivaci modelu aktualizace služby Azure Analysis Services.

Po vytvoření aplikace logiky:

1. V návrháři aplikace logiky zvolte první akci jako **přijetí požadavku HTTP když**.

   ![Přidat aktivitu protokolu HTTP přijatých](./media/analysis-services-async-refresh-logic-app/1.png)

Po uložení aplikace logiky se tento krok naplní s adresou URL protokolu HTTP POST.

2. Přidání nového kroku a vyhledejte **HTTP**.  

   ![Přidat aktivitu protokolu HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Přidat aktivitu protokolu HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Vyberte **HTTP** přidáte tuto akci.

   ![Přidat aktivitu protokolu HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Nakonfigurujte aktivitu protokolu HTTP následujícím způsobem:

|Vlastnost  |Hodnota  |
|---------|---------|
|**Metoda**     |POST         |
|**URI**     | https://*vaší oblasti server*/servers/*jako název serveru*/models/*název databáze*/ <br /> <br /> Například: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Hlavičky**     |   Typ obsahu, application/json <br /> <br />  ![Hlavičky](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Text**     |   Další informace o tvořící tělo požadavku najdete v tématu [asynchronní aktualizace pomocí REST API – příspěvek /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Ověřování**     |Active Directory OAuth         |
|**tenanta**     |Zadejte vaše ID Tenanta služby Azure Active Directory         |
|**Cílová skupina**     |https://*.asazure.windows.net         |
|**ID klienta**     |Zadejte vaše ClientID název instančního objektu         |
|**Typ přihlašovacích údajů**     |Tajný kód         |
|**Tajný kód**     |Zadejte název tajný klíč instančního objektu         |

Příklad:

![Dokončené aktivity protokolu HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Teď otestujte aplikaci logiky.  V návrháři aplikace logiky, klikněte na tlačítko **spustit**.

![Otestujte aplikaci logiky](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Využití aplikace logiky pomocí služby Azure Data Factory

Po uložení aplikace logiky, přečtěte si **přijetí požadavku HTTP když** aktivit a pak nakopírujte **adresa URL operace HTTP POST** , který je nyní generována.  Toto je adresa URL, která je možné pomocí služby Azure Data Factory pro asynchronní volání triggeru aplikace logiky.

Tady je příklad Azure Data Factory webová aktivita, která provádí tuto akci.

![Aktivita webu objekt pro vytváření dat](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Použití samostatné aplikace logiky

Když se nechystáte v pomocí nástroje Orchestration například Data Factory pro aktivaci modelu obnovení, můžete nastavit aplikaci logiky pro aktivaci obnovení na základě plánu.

Použití výše uvedeného příkladu odstraňte první aktivitu a nahraďte ho hodnotou **plán** aktivity.

![Naplánovat aktivitu](./media/analysis-services-async-refresh-logic-app/12.png)

![Naplánovat aktivitu](./media/analysis-services-async-refresh-logic-app/13.png)

Tento příklad použije **opakování**.

Po přidání aktivity nakonfigurovat Interval a frekvenci, pak přidá nový parametr a zvolte **v těchto hodinách**.

![Naplánovat aktivitu](./media/analysis-services-async-refresh-logic-app/16.png)

Vyberte požadované hodiny.

![Naplánovat aktivitu](./media/analysis-services-async-refresh-logic-app/15.png)

Uložte aplikaci logiky.

## <a name="next-steps"></a>Další postup

[Ukázky](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
