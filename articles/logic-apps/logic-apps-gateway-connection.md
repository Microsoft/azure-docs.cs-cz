---
title: Přístup ke zdrojům dat místně z Azure Logic Apps | Microsoft Docs
description: Připojení k místním zdrojům dat z Logic Apps vytvořením místní brány dat
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 029dc8daaf456c155d46eefa699772882bdabee5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982872"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Připojení k místním zdrojům dat z Azure Logic Apps

Pokud chcete získat přístup ke zdrojům dat z aplikací logiky, vytvořte na Azure Portal prostředek místní brány dat. Vaše aplikace logiky pak můžou používat [](../logic-apps/logic-apps-gateway-install.md#supported-connections)místní konektory. Tento článek ukazuje, jak vytvořit prostředek brány Azure *po* [Stažení a instalaci brány do místního počítače](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Pokud se chcete připojit k virtuálním sítím Azure, zvažte místo toho vytvoření [*prostředí integrační služby*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . 

Informace o tom, jak používat bránu s dalšími službami, najdete v těchto článcích:

* [Místní brána dat Power BI Microsoftu](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow místní bránu dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps místní bránu dat](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services místní bránu dat](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Požadavky

* Bránu dat jste už [stáhli a nainstalovali na místním počítači](../logic-apps/logic-apps-gateway-install.md).

* Vaše instalace brány už není přidružená k prostředku brány v Azure. Instalaci brány můžete propojit jenom s jedním prostředkem brány, který se stane při vytvoření prostředku brány a vybrání instalace brány. Díky tomuto propojení není instalace brány dostupná pro jiné prostředky.

* Když se přihlásíte k Azure Portal a vytvoříte prostředek brány, ujistěte se, že jste používali stejný přihlašovací účet, který jste předtím použili k [instalaci místní brány dat](../logic-apps/logic-apps-gateway-install.md#requirements) , spolu se stejným předplatným [Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) , které jste použili k instalaci brány. Pokud ještě nemáte předplatné Azure, zaregistrujte <a href="https://azure.microsoft.com/free/" target="_blank">si bezplatný účet Azure</a>.

* K vytvoření a údržbě prostředku brány v Azure Portal potřebuje [účet služby Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) aspoň oprávnění **Přispěvatel** . Místní brána dat se spouští jako služba systému Windows a je nastavená tak, aby se `NT SERVICE\PBIEgwService` používala pro přihlašovací údaje služby systému Windows. 

  > [!NOTE]
  > Účet služby systému Windows se liší od účtu používaného pro připojení k místním zdrojům dat a z pracovního nebo školního účtu Azure, který se používá pro přihlášení ke cloudovým službám.

## <a name="download-and-install-gateway"></a>Stáhnout a nainstalovat bránu

Než budete pokračovat postupem v tomto článku, ujistěte se, že je brána již nainstalována v místním počítači.
Pokud jste to ještě neudělali, postupujte podle kroků ke [Stažení a instalaci místní brány dat](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Vytvoření prostředku Azure pro bránu

Po instalaci brány na místní počítač můžete vytvořit prostředek Azure pro vaši bránu. Tento krok taky přidruží prostředek brány k vašemu předplatnému Azure.

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Ujistěte se, že používáte stejnou pracovní nebo školní e-mailovou adresu, která se používá k instalaci brány.

2. V hlavní nabídce Azure vyberte **vytvořit** > 
**integraci** > prostředků**místní bránu dat**.

   ![Najít místní bránu dat](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na stránce **vytvořit bránu připojení** zadejte pro prostředek brány tyto informace:

   | Vlastnost | Popis | 
   |----------|-------------|
   | **Název prostředku** | Název prostředku brány, který může obsahovat jenom písmena`-`, číslice, spojovníky (), podtržítka (`_`), kulaté závorky (`(`, `)`) a tečky (`.`). | 
   | **Předplatné** | Název vašeho předplatného Azure, který by měl být stejný jako předplatné vaší aplikace logiky. Výchozí předplatné vychází z účtu Azure, který jste použili k přihlášení. | 
   | **Skupina prostředků** | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) pro uspořádání souvisejících prostředků | 
   | **Location** | Azure toto umístění omezuje na stejnou oblast, která byla vybrána pro cloudovou službu brány během [Instalace brány](../logic-apps/logic-apps-gateway-install.md). <p>**Poznámka:** Zajistěte, aby toto umístění prostředku brány odpovídalo umístění cloudové služby brány. V opačném případě se instalace brány nemusí zobrazit v seznamu nainstalovaných bran, abyste je mohli vybrat v dalším kroku. Pro prostředek brány a pro vaši aplikaci logiky můžete použít jiné oblasti. | 
   | **Název instalace** | Pokud vaše instalace brány ještě není vybraná, vyberte bránu, kterou jste předtím nainstalovali. | 
   | | | 

   Zde naleznete příklad:

   ![Zadání podrobností pro vytvoření místní brány dat](./media/logic-apps-gateway-connection/createblade.png)

4. Pokud chcete přidat prostředek brány do řídicího panelu Azure, vyberte **Připnout na řídicí panel**. Jakmile budete hotoví, vyberte **Vytvořit**.

   Pokud chcete bránu kdykoli najít nebo zobrazit, v hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "místní brány dat" a pak vyberte místní **brány dat**.

   ![Hledání "místních bran dat"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Připojení k místním datům

Po vytvoření prostředku brány a přidružení předplatného Azure k tomuto prostředku můžete nově vytvořit propojení mezi aplikací logiky a místním zdrojem dat pomocí brány.

1. V Azure Portal vytvořte nebo otevřete aplikaci logiky v návrháři aplikace logiky.

2. Přidejte konektor, který podporuje místní připojení, například **SQL Server**.

3. Teď nastavte připojení:

   1. Vyberte **připojit přes místní bránu dat**. 

   2. U **bran**vyberte prostředek brány, který jste předtím vytvořili. 

      I když umístění připojení brány musí existovat ve stejné oblasti jako aplikace logiky, můžete vybrat bránu v jiné oblasti.

   3. Zadejte jedinečný název připojení a další požadované informace. 

      Jedinečný název připojení vám pomůže později snadno identifikovat toto připojení, zejména při vytváření více připojení. Pokud je to možné, zahrňte taky kvalifikovanou doménu pro vaše uživatelské jméno.
   
      Zde naleznete příklad:

      ![Vytvoření propojení mezi aplikací logiky a bránou dat](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Jakmile budete hotoví, vyberte **Vytvořit**. 

Vaše připojení k bráně je teď připravené na použití vaší aplikace logiky.

## <a name="edit-connection"></a>Upravit připojení

Po vytvoření připojení brány pro vaši aplikaci logiky možná budete chtít později aktualizovat nastavení tohoto konkrétního připojení.

1. Najděte připojení brány:

   * Pokud chcete najít všechna připojení rozhraní API jenom pro vaši aplikaci logiky, v nabídce aplikace logiky v části **vývojové nástroje**vyberte **připojení rozhraní API**. 
   
     ![Přejít do aplikace logiky, vyberte připojení rozhraní API.](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Pokud chcete najít všechna připojení rozhraní API přidružená k vašemu předplatnému Azure: 

     * V hlavní nabídce Azure přejděte na **všechny služby** > **Webová** > **připojení rozhraní API**. 
     * Nebo z hlavní nabídky Azure přejděte na **všechny prostředky**.

2. Vyberte připojení brány, které chcete, a pak zvolte **Upravit připojení rozhraní API**.

   > [!TIP]
   > Pokud se vaše aktualizace neprojeví, zkuste [zastavit a restartovat službu brány systému Windows](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Odstranit prostředek brány

Pokud chcete vytvořit jiný prostředek brány, přidružit bránu k jinému prostředku nebo odebrat prostředek brány, můžete odstranit prostředek brány, aniž by to ovlivnilo instalaci brány. 

1. V hlavní nabídce Azure přejděte na **všechny prostředky**. 

2. Vyhledejte a vyberte prostředek brány.

3. Pokud jste to ještě neučinili, vyberte v nabídce prostředku brány místní **bránu dat**. 

4. Na panelu nástrojů prostředek vyberte možnost **Odstranit**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Zabezpečení aplikací logiky](./logic-apps-securing-a-logic-app.md)
* [Běžné příklady a scénáře pro Logic Apps](./logic-apps-examples-and-scenarios.md)
