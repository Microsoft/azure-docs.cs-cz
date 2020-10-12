---
title: Přístup ke zdrojům dat místně
description: Připojení k místním zdrojům dat z Azure Logic Apps vytvořením prostředku brány dat v Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, divswa, logicappspm
ms.topic: article
ms.date: 08/18/2020
ms.openlocfilehash: 2dd086ccc45458299cf6b8a7ad83d023055c96ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661193"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Připojení k místním zdrojům dat z Azure Logic Apps

Po [instalaci místní *brány dat* na místním počítači](../logic-apps/logic-apps-gateway-install.md) a před tím, než budete moci získat přístup ke zdrojům dat místně z aplikace logiky, je potřeba vytvořit prostředek brány v Azure pro instalaci brány. Pak můžete vybrat tento prostředek brány v aktivačních událostech a akcích, které chcete použít pro [místní konektory](../connectors/apis-list.md#on-premises-connectors) dostupné v Azure Logic Apps. Azure Logic Apps podporuje operace čtení a zápisu přes bránu dat. Nicméně tyto operace mají [omezení velikosti datové části](/data-integration/gateway/service-gateway-onprem#considerations).

Tento článek ukazuje, jak vytvořit prostředek brány Azure pro dříve [nainstalovanou bránu na místním počítači](../logic-apps/logic-apps-gateway-install.md). Další informace o bráně najdete v tématu [jak brána funguje](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> K přímému přístupu k místním prostředkům v Azure Virtual Networks bez nutnosti používat bránu zvažte místo toho vytvoření [*prostředí integrační služby*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Informace o tom, jak používat bránu s dalšími službami, najdete v těchto článcích:

* [Microsoft Power Automatizujte místní bránu dat](/power-automate/gateway-reference)
* [Místní brána dat Power BI Microsoftu](/power-bi/service-gateway-onprem)
* [Místní brána dat Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Místní brána dat v Azure Logic Apps podporuje místní [konektory](../connectors/apis-list.md#on-premises-connectors) pro tyto zdroje dat:

* BizTalk Server 2016
* Systém souborů
* IBM DB2  
* IBM Informix
* IBM MQ V8.0.0.1
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Designer
* SQL Server
* Teradata

Můžete také vytvořit [vlastní konektory](../logic-apps/custom-connector-overview.md) , které se připojují ke zdrojům dat přes HTTP nebo HTTPS pomocí REST nebo protokolu SOAP. I když samotné brány neúčtují další náklady, [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md) se vztahuje na tyto konektory a další operace v Azure Logic Apps.

## <a name="prerequisites"></a>Požadavky

* [Místní brána dat je už v místním počítači nainstalovaná](../logic-apps/logic-apps-gateway-install.md). Aby bylo možné vytvořit prostředek brány, který odkazuje na tuto instalaci, musí tato instalace brány existovat.

* Máte [stejný účet a předplatné Azure](../logic-apps/logic-apps-gateway-install.md#requirements) , které jste použili pro instalaci brány. Tento účet Azure musí patřit jenom do jednoho [tenanta Azure Active Directory (Azure AD) nebo adresáře](../active-directory/fundamentals/active-directory-whatis.md#terminology). Pokud chcete vytvořit prostředek brány v Azure, musíte použít stejný účet Azure a předplatné, protože prostředek brány v Azure může vytvořit jenom Správce brány. Instanční objekty se aktuálně nepodporují.

  * Při vytváření prostředku brány v Azure vyberete instalaci brány, která se připojí k vašemu prostředku brány, a jenom tento prostředek brány. Každý prostředek brány se může propojit jenom s jednou instalací brány. Nemůžete vybrat instalaci brány, která už je přidružená k jinému prostředku brány.
  
  * Vaše aplikace logiky a prostředek brány nemusí existovat ve stejném předplatném Azure. Pokud máte přístup k předplatnému v aktivačních událostech a akcích, které mají přístup k místním zdrojům dat, můžete vybrat další předplatná Azure, která mají prostředky brány.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Vytvořit prostředek brány Azure

Po instalaci brány na místní počítač vytvořte prostředek Azure pro vaši bránu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) se stejným účtem Azure, který jste použili k instalaci brány.

1. Do vyhledávacího pole Azure Portal zadejte "místní brána dat" a vyberte místní **brány dat**.

   ![Najít místní bránu dat](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. V části **místní brány dat**vyberte **Přidat**.

   ![Přidat nový prostředek Azure pro bránu dat](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. V části **vytvořit bránu připojení**zadejte tyto informace pro prostředek brány. Po dokončení vyberte **Vytvořit**.

   | Vlastnost | Popis |
   |----------|-------------|
   | **Název prostředku** | Zadejte název prostředku brány, který obsahuje jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) nebo tečky ( `.` ). |
   | **Předplatné** | Vyberte předplatné Azure pro účet Azure, který se použil pro instalaci brány. Výchozí předplatné vychází z účtu Azure, který jste použili k přihlášení. |
   | **Skupina prostředků** | [Skupina prostředků Azure](../azure-resource-manager/management/overview.md) , kterou chcete použít |
   | **Umístění** | Stejná oblast nebo umístění, které bylo vybráno pro cloudovou službu brány během [Instalace brány](../logic-apps/logic-apps-gateway-install.md). V opačném případě se instalace brány nezobrazí v seznamu **název instalace** . Vaše umístění vaší aplikace logiky se může lišit od umístění prostředku brány. |
   | **Název instalace** | Vyberte instalaci brány, která se zobrazí v seznamu jenom v případě, že jsou splněné tyto podmínky: <p><p>– Instalace brány používá stejnou oblast jako prostředek brány, který chcete vytvořit. <br>– Instalace brány není propojená s jiným prostředkem služby Azure Gateway. <br>– Instalace brány je propojená se stejným účtem Azure, který používáte k vytvoření prostředku brány. <br>– Váš účet Azure patří do tenanta jednoho [Azure Active Directory (Azure AD) nebo adresáře](../active-directory/fundamentals/active-directory-whatis.md#terminology) a je to stejný účet, který jste použili pro instalaci brány. <p><p>Další informace najdete v části [Nejčastější dotazy](#faq) . |
   |||

   Tady je příklad, který ukazuje instalaci brány, která je ve stejné oblasti jako prostředek brány a je propojená se stejným účtem Azure:

   ![Zadání podrobností pro vytvoření prostředku brány dat](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Připojení k místním datům

Po vytvoření prostředku brány a přidružení předplatného Azure k tomuto prostředku můžete nově vytvořit propojení mezi aplikací logiky a místním zdrojem dat pomocí brány.

1. V Azure Portal vytvořte nebo otevřete aplikaci logiky v návrháři aplikace logiky.

1. Přidejte konektor, který podporuje místní připojení, například **SQL Server**.

1. Vyberte **připojit přes místní bránu dat**.

1. V části **brány**v seznamu **předplatná** vyberte předplatné Azure, které má prostředek brány, který chcete.

   Pokud máte přístup k předplatnému, můžete si vybrat z různých předplatných Azure, která jsou přidružená k jinému prostředku brány. Vaše aplikace logiky a prostředek brány nemusí existovat ve stejném předplatném Azure.

1. V seznamu **Brána připojení** , která zobrazuje dostupné prostředky brány ve vybraném předplatném, vyberte prostředek brány, který chcete. Každý prostředek brány je propojený s jednou instalací brány.

   > [!NOTE]
   > Seznam bran zahrnuje prostředky brány v jiných oblastech, protože umístění vaší aplikace logiky se může lišit od umístění prostředku brány. 

1. Zadejte jedinečný název připojení a další požadované informace, které závisí na připojení, které chcete vytvořit.

   Jedinečný název připojení vám pomůže později snadno najít toto připojení, zejména pokud vytvoříte více připojení. Pokud je to možné, zahrňte taky kvalifikovanou doménu pro vaše uživatelské jméno.

   Tady je příklad:

   ![Vytvoření propojení mezi aplikací logiky a bránou dat](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Po dokončení vyberte **Vytvořit**.

Vaše připojení k bráně je teď připravené na použití vaší aplikace logiky.

## <a name="edit-connection"></a>Upravit připojení

Chcete-li aktualizovat nastavení pro připojení brány, můžete upravit připojení.

1. Pokud chcete najít všechna připojení rozhraní API jenom pro vaši aplikaci logiky, v nabídce aplikace logiky v části **vývojové nástroje**vyberte **připojení rozhraní API**.

   ![V nabídce aplikace logiky vyberte připojení rozhraní API.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Vyberte připojení brány, které chcete, a pak vyberte **Upravit připojení rozhraní API**.

   > [!TIP]
   > Pokud se vaše aktualizace neprojeví, zkuste [zastavit a restartovat účet služby brány systému Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) pro instalaci brány.

Pokud chcete najít všechna připojení rozhraní API přidružená k vašemu předplatnému Azure:

* V nabídce Azure Portal vyberte **všechny služby**  >  **Web**  >  **rozhraní Web API připojení**.
* Nebo z nabídky Azure Portal vyberte **všechny prostředky**. Nastavte filtr **typu** na **připojení rozhraní API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Odstranit prostředek brány

Pokud chcete vytvořit jiný prostředek brány, propojte instalaci brány s jiným prostředkem brány nebo odeberte prostředek brány, můžete odstranit prostředek brány, aniž by to ovlivnilo instalaci brány.

1. V nabídce Azure Portal vyberte **všechny prostředky**, nebo vyhledejte a vyberte **všechny prostředky** z libovolné stránky. Vyhledejte a vyberte prostředek brány.

1. Pokud jste to ještě neučinili, vyberte v nabídce prostředku brány místní **bránu dat**. Na panelu nástrojů prostředek brány vyberte **Odstranit**.

   Například:

   ![Odstranění prostředku brány v Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka**: Proč se při vytváření prostředku moje brány v Azure nezobrazí moje instalace brány? <br/>
Odpověď: k tomuto problému může **dojít z těchto**důvodů:

* Váš účet Azure není stejný účet, který jste použili pro instalaci brány na místním počítači. Ověřte, že jste se přihlásili k Azure Portal se stejnou identitou, jakou jste použili pro instalaci brány. Prostředek brány v Azure může vytvořit jenom Správce brány. Instanční objekty se aktuálně nepodporují.

* Váš účet Azure nepatří do jednoho [tenanta nebo adresáře Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology). Ověřte, že používáte stejný tenant nebo adresář služby Azure AD, který jste použili při instalaci brány.

* Vaše prostředek brány a instalace brány neexistují ve stejné oblasti. Umístění vaší aplikace logiky se ale může lišit od umístění prostředku brány.

* Instalace brány je už přidružená k jinému prostředku brány. Každý prostředek brány se může propojit jenom s jednou instalací brány, která se může propojit jenom s jedním účtem Azure a předplatným. Proto nemůžete vybrat instalaci brány, která je už přidružená k jinému prostředku brány. Tyto instalace se nebudou zobrazovat v seznamu **název instalace** .

  Pokud chcete zkontrolovat registrace brány v Azure Portal, najděte všechny prostředky Azure, které mají typ prostředku **místní brány dat** napříč *všemi* vašimi předplatnými Azure. Pokud chcete odpojit instalaci brány od jiného prostředku brány, podívejte se na téma [odstranění prostředku brány](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Další kroky

* [Zabezpečení aplikací logiky](./logic-apps-securing-a-logic-app.md)
* [Běžné příklady a scénáře pro Logic Apps](./logic-apps-examples-and-scenarios.md)
