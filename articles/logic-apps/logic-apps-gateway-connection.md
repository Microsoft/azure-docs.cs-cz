---
title: Přístup ke zdrojům dat v místním prostředí
description: Připojení k místním zdrojům dat z Azure Logic Apps vytvořením prostředků místní datové brány Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 29c1aaf18ea45d869d32a8817aeb03faa3b67c32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456572"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Připojení k místním zdrojům dat z Azure Logic Apps

Než budete mít přístup ke zdrojům dat v místním prostředí z vašich aplikací logiky, musíte vytvořit prostředek Azure po [instalaci místní brány *dat* do místního počítače](../logic-apps/logic-apps-gateway-install.md). Vaše aplikace logiky pak použít tento prostředek brány Azure v aktivačních událostí a akcí [poskytovaných místní konektory,](../connectors/apis-list.md#on-premises-connectors) které jsou k dispozici pro Azure Logic Apps.

Tento článek ukazuje, jak vytvořit prostředek brány Azure pro dříve [nainstalovanou bránu v místním počítači](../logic-apps/logic-apps-gateway-install.md). Další informace o bráně najdete v [tématu Jak brána funguje](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Chcete-li se připojit k virtuálním sítím Azure, zvažte místo toho vytvoření [*prostředí integrační služby.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 

Informace o tom, jak používat bránu s jinými službami, naleznete v těchto článcích:

* [Místní datová brána Microsoft Power Automate](/power-automate/gateway-reference)
* [Místní datová brána Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Místní datová brána Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Místní datová brána Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

V Aplikacích Azure Logic Apps místní brána dat podporuje [místní konektory](../connectors/apis-list.md#on-premises-connectors) pro tyto zdroje dat:

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

Azure Logic Apps podporuje operace čtení a zápisu prostřednictvím brány dat. Tyto operace však mají [omezení velikosti datové části](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). I když samotná brána nevzniká další náklady, [model oceňování Logic Apps](../logic-apps/logic-apps-pricing.md) se vztahuje na tyto konektory a další operace v Azure Logic Apps.

## <a name="prerequisites"></a>Požadavky

* Již [jste nainstalovali místní bránu dat v místním počítači](../logic-apps/logic-apps-gateway-install.md).

* Používáte stejný [účet Azure a předplatné,](../logic-apps/logic-apps-gateway-install.md#requirements) které se používalo při instalaci této brány dat. Tento účet Azure musí patřit do jednoho [klienta nebo adresáře Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* Instalace brány ještě není registrovaná a nárokovaná jiným prostředkem brány Azure.

  Když na webu Azure Portal vytvoříte prostředek brány, vyberete instalaci brány, která odkazuje na prostředek brány a jenom tento prostředek brány. V Aplikacích Logika Azure místní aktivační události a akce pak použít prostředek brány pro připojení k místním zdrojům dat. V těchto aktivačních událostí a akcí, můžete vybrat předplatné Azure a přidružené brány prostředek, který chcete použít. Každý prostředek brány odkazuje pouze na jednu instalaci brány, která odkazuje pouze na jeden účet Azure.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Vytvoření prostředku brány Azure

Po instalaci brány do místního počítače vytvořte prostředek Azure pro bránu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí stejného účtu Azure, který se používal k instalaci brány.

1. Do vyhledávacího pole portálu Azure zadejte "místní bránu dat" a vyberte **Místní brány dat**.

   ![Najít "Místní brána dat"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. V **části Místní brány dat**vyberte **Přidat**.

   ![Přidání nového prostředku Azure pro bránu dat](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. V části **Vytvořit bránu připojení**zadejte tyto informace pro prostředek brány. Až to budete mít, vyberte **Vytvořit**.

   | Vlastnost | Popis |
   |----------|-------------|
   | **Název prostředku** | Zadejte název prostředku brány, který obsahuje pouze písmena, čísla, pomlčky`-`( ), podtržítka (`_`), závorky`(`( , `)`) nebo tečky (`.`). |
   | **Předplatné** | Vyberte předplatné Azure pro účet Azure, který se používal pro instalaci brány. Výchozí předplatné je založené na účtu Azure, který jste použili k přihlášení. |
   | **Skupina prostředků** | [Skupina prostředků Azure,](../azure-resource-manager/management/overview.md) kterou chcete použít |
   | **Umístění** | Stejná oblast nebo umístění, která byla vybrána pro cloudovou službu brány během [instalace brány](../logic-apps/logic-apps-gateway-install.md). V opačném případě se instalace brány v seznamu **Název instalace** nezobrazí. Umístění aplikace logiky se může lišit od umístění prostředku brány. |
   | **Název instalace** | Vyberte instalaci brány, která se v seznamu zobrazí pouze v případě, že jsou splněny tyto podmínky: <p><p>- Instalace brány používá stejnou oblast jako prostředek brány, který chcete vytvořit. <br>- Instalace brány není propojena s jiným prostředkem brány Azure. <br>- Instalace brány je propojena se stejným účtem Azure, který používáte k vytvoření prostředku brány. <br>- Váš účet Azure patří do jednoho [klienta nebo adresáře Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) a je stejný účet, který byl použit pro instalaci brány. <p><p>Další informace naleznete v části [Nejčastější dotazy.](#faq) |
   |||

   Tady je příklad, který ukazuje instalaci brány, která je ve stejné oblasti jako prostředek brány a je propojená se stejným účtem Azure:

   ![Poskytnutí podrobností pro vytvoření prostředku brány dat](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Připojení k místním datům

Po vytvoření prostředku brány a přidružení předplatného Azure k tomuto prostředku teď můžete vytvořit připojení mezi aplikací logiky a místním zdrojem dat pomocí brány.

1. Na webu Azure Portal vytvořte nebo otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Přidejte konektor, který podporuje místní připojení, například **SQL Server**.

1. Vyberte **Připojit přes místní bránu dat**.

1. V části **Gateways**vyberte ze seznamu **Předplatná** předplatné Azure, které má požadovaný prostředek brány.

1. Ze seznamu **Brána připojení,** který zobrazuje dostupné prostředky brány ve vybraném předplatném, vyberte požadovaný prostředek brány. Každý prostředek brány je propojen s instalací jedné brány.

   > [!NOTE]
   > Seznam bran obsahuje prostředky brány v jiných oblastech, protože umístění vaší aplikace logiky se může lišit od umístění prostředku brány. 

1. Zadejte jedinečný název připojení a další požadované informace, které závisí na připojení, které chcete vytvořit.

   Jedinečný název připojení vám pomůže toto připojení snadno najít později, zejména pokud vytvoříte více připojení. Pokud je to možné, uveďte také kvalifikovanou doménu pro vaše uživatelské jméno.

   Zde naleznete příklad:

   ![Vytvoření připojení mezi aplikací logiky a bránou dat](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Až to budete mít, vyberte **Vytvořit**.

Připojení brány je teď připravené pro aplikaci logiky.

## <a name="edit-connection"></a>Upravit připojení

Chcete-li aktualizovat nastavení připojení brány, můžete připojení upravit.

1. Chcete-li najít všechna připojení rozhraní API pouze pro aplikaci logiky, v nabídce aplikace logiky v části **Nástroje pro vývoj**vyberte připojení rozhraní **API**.

   ![V nabídce aplikace logiky vyberte "Připojení rozhraní API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Vyberte požadované připojení brány a pak vyberte **Upravit připojení rozhraní API**.

   > [!TIP]
   > Pokud se aktualizace neprojeví, zkuste [zastavit a restartovat účet služby windows brány](../logic-apps/logic-apps-gateway-install.md#restart-gateway) pro instalaci brány.

Pokud chcete najít všechna připojení rozhraní API přidružená k vašemu předplatnému Azure:

* V nabídce Portál Azure vyberte **Všechna připojení** > **webového** > rozhraní**API**služeb .
* Nebo z nabídky portálu Azure vyberte **Všechny prostředky**. Nastavte filtr **Typ** na **připojení rozhraní API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Odstranit prostředek brány

Chcete-li vytvořit jiný prostředek brány, propojit instalaci brány s jiným prostředkem brány nebo odebrat prostředek brány, můžete odstranit prostředek brány bez ovlivnění instalace brány.

1. V nabídce Portál Azure vyberte **Všechny prostředky**nebo vyhledejte a vyberte **všechny prostředky** z libovolné stránky. Vyhledání a vyvýběru prostředku brány.

1. Pokud ještě nejste vybráni, vyberte v nabídce prostředků brány **možnost Místní brána dat**. Na panelu nástrojů prostředků brány vyberte **Odstranit**.

   Například:

   ![Odstranění prostředků brány v Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Proč se instalace brány nezobrazí, když vytvořím prostředek brány v Azure? <br/>
**A**: K tomuto problému může dojít z těchto důvodů:

* Váš účet Azure musí být stejný účet, který je propojený s instalací brány v místním počítači. Zkontrolujte, že jste přihlášení k portálu Azure se stejnou identitou, která je propojená s instalací brány. Také se ujistěte, že váš účet Azure patří do jednoho [klienta Azure AD nebo adresáře](../active-directory/fundamentals/active-directory-whatis.md#terminology) a je nastavena na stejné hospodařilo do klienta Azure AD nebo adresáře, který se používal při instalaci brány.

* Vaše prostředek brány a instalace brány musí používat stejnou oblast. Umístění aplikace logiky se však může lišit od umístění prostředku brány.

* Instalace brány je již zaregistrována a nárokována jiným prostředkem brány. Tyto instalace se v seznamu **Název instalace** nezobrazí. Pokud chcete zkontrolovat registrace bran na webu Azure Portal, najděte všechny prostředky Azure, které mají **místní datové brány,** ve *všech* vašich předplatných Azure. Informace o odpojení instalace brány od jiného prostředku brány naleznete v tématu [Odstranění prostředku brány](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Další kroky

* [Zabezpečení aplikací logiky](./logic-apps-securing-a-logic-app.md)
* [Běžné příklady a scénáře pro aplikace logiky](./logic-apps-examples-and-scenarios.md)
