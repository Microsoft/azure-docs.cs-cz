---
title: Přístup ke zdrojům dat místně z Azure Logic Apps
description: Připojení k místním zdrojům dat z Logic Apps vytvořením místní brány dat
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 65c1d427939dc39aebece24b923bc4ebfbf136bb
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861000"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Připojení k místním zdrojům dat z Azure Logic Apps

Pokud chcete získat přístup ke zdrojům dat z aplikací logiky, vytvořte na Azure Portal prostředek místní brány dat. Vaše aplikace logiky pak můžou používat [](../logic-apps/logic-apps-gateway-install.md#supported-connections)místní konektory. Tento článek ukazuje, jak vytvořit prostředek brány Azure *po* [Stažení a instalaci brány do místního počítače](../logic-apps/logic-apps-gateway-install.md). Další informace o tom, jak brána funguje, najdete v tématu [jak brána funguje](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Pokud se chcete připojit k virtuálním sítím Azure, zvažte místo toho vytvoření [*prostředí integrační služby*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Informace o tom, jak používat bránu s dalšími službami, najdete v těchto článcích:

* [Místní brána dat Power BI Microsoftu](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow místní bránu dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps místní bránu dat](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Požadavky

* [Místní bránu dat jste už nainstalovali na místním počítači](../logic-apps/logic-apps-gateway-install.md).

* Máte [stejný účet Azure a předplatné Azure](../logic-apps/logic-apps-gateway-install.md#requirements) , které jste použili při instalaci místní brány dat.

* Předtím jste nepřipojili instalaci brány k jinému prostředku brány v Azure.

  Při vytváření prostředku brány vyberete instalaci brány, kterou chcete přidružit k vašemu prostředku brány. Instalace připojené brány není k dispozici, abyste ji mohli vybrat při vytváření prostředků brány.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Vytvořit prostředek brány Azure

Po instalaci brány na místní počítač vytvořte prostředek Azure pro vaši bránu. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) se stejným účtem Azure, který jste použili k instalaci brány.

1. Do vyhledávacího pole Azure Portal zadejte "místní brána dat" a vyberte místní **brány dat**.

   ![Najít místní bránu dat](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. V části **místní brány dat**vyberte **Přidat**.

   ![Přidat bránu dat](./media/logic-apps-gateway-connection/add-gateway.png)

1. V části **vytvořit bránu připojení**zadejte tyto informace pro prostředek brány. Až to budete mít, vyberte **Vytvořit**.

   | Vlastnost | Popis |
   |----------|-------------|
   | **Název prostředku** | Název prostředku brány, který může obsahovat jenom písmena`-`, číslice, spojovníky (), podtržítka (`_`), kulaté závorky (`(`, `)`) a tečky (`.`). |
   | **Předplatné** | Vaše předplatné Azure, které musí být stejné jako instalace brány a aplikace logiky. Výchozí předplatné vychází z účtu Azure, který jste použili k přihlášení. |
   | **Skupina prostředků** | [Skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) , kterou chcete použít |
   | **Location** | Stejná oblast jako umístění, které bylo vybráno pro cloudovou službu brány během [Instalace brány](../logic-apps/logic-apps-gateway-install.md). V opačném případě se instalace brány nezobrazí v seznamu **název instalace** , abyste mohli vybrat. Vaše umístění vaší aplikace logiky se může lišit od umístění prostředku brány. |
   | **Název instalace** | Pokud vaše instalace brány ještě není vybraná, vyberte bránu, kterou jste předtím nainstalovali. Dříve připojené instalace brány se v tomto seznamu nezobrazí, abyste je mohli vybrat. |
   |||

   Zde naleznete příklad:

   ![Zadání podrobností pro vytvoření místní brány dat](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Připojení k místním datům

Po vytvoření prostředku brány a přidružení předplatného Azure k tomuto prostředku můžete nově vytvořit propojení mezi aplikací logiky a místním zdrojem dat pomocí brány.

1. V Azure Portal vytvořte nebo otevřete aplikaci logiky v návrháři aplikace logiky.

1. Přidejte konektor, který podporuje místní připojení, například **SQL Server**.

1. Vyberte **připojit přes místní bránu dat**. 

1. U **bran**vyberte prostředek brány, který jste vytvořili.

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

* V hlavní nabídce Azure přejděte na **všechny služby** > **Webová** > **připojení rozhraní API**.
* Nebo z hlavní nabídky Azure přejděte na **všechny prostředky**. Nastavte filtr **typu** na **připojení rozhraní API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Odstranit prostředek brány

Pokud chcete vytvořit jiný prostředek brány, propojte instalaci brány s jiným prostředkem brány nebo odeberte prostředek brány, můžete odstranit prostředek brány, aniž by to ovlivnilo instalaci brány. 

1. V hlavní nabídce Azure vyberte **všechny prostředky**. Vyhledejte a vyberte prostředek brány.

1. Pokud jste to ještě neučinili, vyberte v nabídce prostředku brány místní **bránu dat**. Na panelu nástrojů prostředek brány vyberte **Odstranit**.

   Příklad:

   ![Odstranit bránu](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Další kroky

* [Zabezpečení aplikací logiky](./logic-apps-securing-a-logic-app.md)
* [Běžné příklady a scénáře pro Logic Apps](./logic-apps-examples-and-scenarios.md)
