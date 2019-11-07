---
title: Přístup ke zdrojům dat místně – Azure Logic Apps
description: Připojení k místním zdrojům dat z Azure Logic Apps vytvořením prostředku místní brány dat Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/18/2019
ms.openlocfilehash: 49ed6eedf55a8a3c0faa2fa1ff60b9894af78285
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580858"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Připojení k místním zdrojům dat z Azure Logic Apps

Než budete mít přístup ke zdrojům dat místně z aplikace logiky, musíte po [instalaci místní *brány dat* do místního počítače](../logic-apps/logic-apps-gateway-install.md)vytvořit prostředek Azure. Vaše aplikace logiky potom použijí tento prostředek brány Azure v aktivačních událostech a akcích poskytovaných [místními konektory](../connectors/apis-list.md#on-premises-connectors) , které jsou k dispozici pro Azure Logic Apps.

Tento článek ukazuje, jak vytvořit prostředek brány Azure pro dříve [nainstalovanou bránu na místním počítači](../logic-apps/logic-apps-gateway-install.md). Další informace o bráně najdete v tématu [jak brána funguje](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Pokud se chcete připojit k virtuálním sítím Azure, zvažte místo toho vytvoření [*prostředí integrační služby*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Informace o tom, jak používat bránu s dalšími službami, najdete v těchto článcích:

* [Místní brána dat Power BI Microsoftu](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Automatizace místní brány dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Místní brána dat Microsoft Power Apps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Místní brána dat v Azure Logic Apps podporuje místní [konektory](../connectors/apis-list.md#on-premises-connectors) pro tyto zdroje dat:

* BizTalk Server 2016
* Systém souborů
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure Logic Apps podporuje operace čtení a zápisu přes bránu dat. Nicméně tyto operace mají [omezení velikosti datové části](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). I když samotné brány neúčtují další náklady, [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md) se vztahuje na tyto konektory a další operace v Azure Logic Apps.

## <a name="prerequisites"></a>Požadavky

* [Místní brána dat je už v místním počítači nainstalovaná](../logic-apps/logic-apps-gateway-install.md).

* Máte [stejný účet a předplatné Azure](../logic-apps/logic-apps-gateway-install.md#requirements) , které se použily při instalaci této brány dat.

* Vaše instalace brány ještě není zaregistrovaná a deklarovaná jiným prostředkem Azure Gateway.

  Při vytváření prostředku brány v Azure Portal vyberete instalaci brány, která odkazuje na prostředek brány a jenom na tento prostředek brány. V Azure Logic Apps místní triggery a akce pak pomocí prostředku brány připojte k místním zdrojům dat. V těchto aktivačních událostech a akcích vyberete předplatné Azure a přidružený prostředek brány, který chcete použít. Každý prostředek brány odkazuje jenom na jednu instalaci brány, která odkazuje jenom na jeden účet Azure.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Vytvořit prostředek brány Azure

Po instalaci brány na místní počítač vytvořte prostředek Azure pro vaši bránu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) se stejným účtem Azure, který jste použili k instalaci brány.

1. Do vyhledávacího pole Azure Portal zadejte "místní brána dat" a vyberte místní **brány dat**.

   ![Najít místní bránu dat](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. V části **místní brány dat**vyberte **Přidat**.

   ![Přidat nový prostředek Azure pro bránu dat](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. V části **vytvořit bránu připojení**zadejte tyto informace pro prostředek brány. Až to budete mít, vyberte **Vytvořit**.

   | Vlastnost | Popis |
   |----------|-------------|
   | **Název prostředku** | Zadejte název prostředku brány, který obsahuje jenom písmena, číslice, spojovníky (`-`), podtržítka (`_`), kulaté závorky (`(`, `)`) nebo tečky (`.`). |
   | **Předplatné** | Vyberte předplatné Azure pro účet Azure, který se použil pro instalaci brány. Výchozí předplatné vychází z účtu Azure, který jste použili k přihlášení. |
   | **Skupina prostředků** | [Skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) , kterou chcete použít |
   | **Umístění** | Stejná oblast nebo umístění, které bylo vybráno pro cloudovou službu brány během [Instalace brány](../logic-apps/logic-apps-gateway-install.md). V opačném případě se instalace brány nezobrazí v seznamu **název instalace** . Vaše umístění vaší aplikace logiky se může lišit od umístění prostředku brány. |
   | **Název instalace** | Vyberte instalaci brány, která se zobrazí v seznamu jenom v případě, že jsou splněné tyto podmínky: <p><p>– Existuje ve stejné oblasti jako prostředek brány, který chcete vytvořit. <br>– Odpojování k jinému prostředku služby Azure Gateway <br>– Propojeno se stejným účtem Azure, který používáte k vytvoření prostředku brány <p><p>Další informace najdete v části [Nejčastější dotazy](#faq) . |
   |||

   Tady je příklad, který ukazuje instalaci brány, která je ve stejné oblasti jako prostředek brány a je propojená se stejným účtem Azure:

   ![Zadání podrobností pro vytvoření prostředku brány dat](./media/logic-apps-gateway-connection/add-azure-data-gateway-information.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Připojení k místním datům

Po vytvoření prostředku brány a přidružení předplatného Azure k tomuto prostředku můžete nově vytvořit propojení mezi aplikací logiky a místním zdrojem dat pomocí brány.

1. V Azure Portal vytvořte nebo otevřete aplikaci logiky v návrháři aplikace logiky.

1. Přidejte konektor, který podporuje místní připojení, například **SQL Server**.

1. Vyberte **připojit přes místní bránu dat**.

1. V části **brány**v seznamu **předplatná** vyberte předplatné Azure, které má prostředek brány, který chcete.

1. V seznamu **Brána připojení** , která zobrazuje dostupné prostředky brány ve vybraném předplatném, vyberte prostředek brány, který chcete. Každý prostředek brány je propojený s jednou instalací brány.

   > [!NOTE]
   > Seznam bran zahrnuje prostředky brány v jiných oblastech, protože umístění vaší aplikace logiky se může lišit od umístění prostředku brány. 

1. Zadejte jedinečný název připojení a další požadované informace, které závisí na připojení, které chcete vytvořit.

   Jedinečný název připojení vám pomůže později snadno najít toto připojení, zejména pokud vytvoříte více připojení. Pokud je to možné, zahrňte taky kvalifikovanou doménu pro vaše uživatelské jméno.

   Zde naleznete příklad:

   ![Vytvoření propojení mezi aplikací logiky a bránou dat](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Až to budete mít, vyberte **Vytvořit**.

Vaše připojení k bráně je teď připravené na použití vaší aplikace logiky.

## <a name="edit-connection"></a>Upravit připojení

Chcete-li aktualizovat nastavení pro připojení brány, můžete upravit připojení.

1. Pokud chcete najít všechna připojení rozhraní API jenom pro vaši aplikaci logiky, v nabídce aplikace logiky v části **vývojové nástroje**vyberte **připojení rozhraní API**.

   ![V nabídce aplikace logiky vyberte připojení rozhraní API.](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Vyberte připojení brány, které chcete, a pak vyberte **Upravit připojení rozhraní API**.

   > [!TIP]
   > Pokud se vaše aktualizace neprojeví, zkuste [zastavit a restartovat účet služby brány systému Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) pro instalaci brány.

Pokud chcete najít všechna připojení rozhraní API přidružená k vašemu předplatnému Azure:

* V hlavní nabídce Azure přejděte na **všechny služby** > **připojení rozhraní API**pro **Web** > .
* Nebo z hlavní nabídky Azure přejděte na **všechny prostředky**. Nastavte filtr **typu** na **připojení rozhraní API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Odstranit prostředek brány

Pokud chcete vytvořit jiný prostředek brány, propojte instalaci brány s jiným prostředkem brány nebo odeberte prostředek brány, můžete odstranit prostředek brány, aniž by to ovlivnilo instalaci brány.

1. V hlavní nabídce Azure vyberte **všechny prostředky**. Vyhledejte a vyberte prostředek brány.

1. Pokud jste to ještě neučinili, vyberte v nabídce prostředku brány místní **bránu dat**. Na panelu nástrojů prostředek brány vyberte **Odstranit**.

   Příklad:

   ![Odstranění prostředku brány v Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway-resource.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka**: Proč se při vytváření prostředku moje brány v Azure nezobrazí moje instalace brány? <br/>
Odpověď: k tomuto problému může **dojít z těchto**důvodů:

* Váš účet Azure musí být stejný účet, který je propojený s instalací brány na místním počítači. Ověřte, že jste přihlášení k Azure Portal se stejnou identitou, která je propojená s instalací brány.

* Instalace prostředků a brány brány musí používat stejnou oblast. Vaše umístění aplikace logiky se ale může lišit od umístění prostředku brány.

* Instalace brány je už zaregistrovaná a deklarovaná jiným prostředkem brány. Tyto instalace se nebudou zobrazovat v seznamu **název instalace** . Pokud chcete zkontrolovat registrace brány v Azure Portal, najděte všechny prostředky Azure, které mají **místní brány dat** , a to ve *všech* předplatných Azure. Pokud chcete odpojit instalaci brány od jiného prostředku brány, podívejte se na téma [odstranění prostředku brány](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Další kroky

* [Zabezpečení aplikací logiky](./logic-apps-securing-a-logic-app.md)
* [Běžné příklady a scénáře pro Logic Apps](./logic-apps-examples-and-scenarios.md)
