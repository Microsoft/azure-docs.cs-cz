---
title: Přístup ke zdrojům dat v místním prostředí pro Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření a nastavení na místní bránu dat, můžete přístup ke zdrojům dat místně z aplikací logiky
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: e8e8d85d2c95c1dda7271de72491594562b7d3c1
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413702"
---
# <a name="connect-to-data-sources-on-premises-from-azure-logic-apps-with-on-premises-data-gateway"></a>Připojení ke zdrojům dat v místním prostředí v Azure Logic Apps s místní bránou dat

Přístup z aplikace logiky ke zdrojům dat v místním prostředí, můžete vytvořit prostředek brány dat v Azure, takže aplikace logiky můžete použít [místní konektory](../logic-apps/logic-apps-gateway-install.md#supported-connections). Tento článek popisuje, jak vytvořit váš prostředek Azure gateway *po* vám [stáhnout a nainstalovat bránu na místním počítači](../logic-apps/logic-apps-gateway-install.md). 

Informace o tom, jak použít bránu s ostatními službami, najdete v těchto článcích:

* [Microsoft Power BI – místní brána dat](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow na místní bránu dat](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps na místní bránu dat](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Služba Azure Analysis Services na místní brány dat](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Požadavky

* Když jste již [stáhnout a nainstalovat bránu dat na místním počítači](../logic-apps/logic-apps-gateway-install.md).

* Instalace brány není již přidružený prostředek brány v Azure. Instalace brány můžete propojit pouze jeden prostředek brány, který se stane, když vytvoříte prostředek brány a vyberete instalaci brány. Toto propojení díky instalaci brány není k dispozici pro jiné prostředky.

* Přihlaste se k webu Azure portal a vytvoříte prostředek brány, ujistěte se, že používáte stejný přihlašovací účet, který byl dříve použitých k [instalace místní brány dat](../logic-apps/logic-apps-gateway-install.md#requirements) spolu s stejné [předplatného Azure ](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) , která byla použita k instalaci brány. Pokud nemáte ještě předplatné Azure <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

* K vytváření a údržbě prostředku brány na webu Azure Portal, vaše [účet služby Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) potřebuje aspoň **Přispěvatel** oprávnění. Místní brána dat běží jako služba Windows a nastaven na použití `NT SERVICE\PBIEgwService` pro Windows service přihlašovací údaje. 

  > [!NOTE]
  > Windows, které účet služby se liší od účtu použité pro připojování k místním datům zdrojů a z Azure pracovní nebo školní účet použitý k přihlášení do cloudových služeb.

## <a name="download-and-install-gateway"></a>Stažení a instalace brány

Než budete pokračovat s kroky v tomto článku, ujistěte se, že vaše brána je již nainstalována na místním počítači.
Pokud jste tak dosud neučinili, postupujte podle kroků pro [stažení a instalace místní brány dat](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Vytvoření prostředku Azure pro bránu

Po instalaci brány na místním počítači, pak vytvoříte prostředek Azure pro vaši bránu. Tento krok také přiřadí prostředku brány ve vašem předplatném Azure.

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Ujistěte se, že používáte stejné Azure pracovní nebo školní e-mailovou adresu použít k instalaci brány.

2. V hlavní nabídce Azure zvolte **vytvořit prostředek** > 
**integrace** > **On-premises data gateway**.

   ![Vyhledejte "On-premises data gateway"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na **vytvořit připojení brány** zadejte tyto informace pro váš prostředek brány:

   | Vlastnost | Popis | 
   |----------|-------------|
   | **Název** | Název pro prostředek brány | 
   | **Předplatné** | Název předplatného Azure, které by měly být stejné předplatné jako aplikace logiky. Výchozí odběr je založený na účtu Azure, který jste použili k přihlášení. | 
   | **Skupina prostředků** | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) pro uspořádání souvisejících prostředků | 
   | **Umístění** | Azure omezuje toto umístění do stejné oblasti, která byla vybrána pro cloudovou službu Brána během [instalace brány](../logic-apps/logic-apps-gateway-install.md). <p>**Poznámka:**: Ujistěte se, že toto umístění prostředku brány odpovídá umístění bránu cloudové služby. V opačném případě instalaci brány se nemusí zobrazit v seznamu nainstalovaných bran pro výběr v dalším kroku. Pro prostředek brány a pro vaši aplikaci logiky, můžete použít různé oblasti. | 
   | **Název instalace** | Pokud vaše instalace brány není vybrána, vyberte bránu, kterou jste dříve nainstalovali. | 
   | | | 

   Zde naleznete příklad:

   ![Zadejte podrobnosti a vytvořte na místní bránu dat](./media/logic-apps-gateway-connection/createblade.png)

4. Chcete-li přidat prostředek brány na řídicí panel Azure, **připnout na řídicí panel**. Jakmile budete hotoví, vyberte **Vytvořit**.

   Chcete-li najít nebo zobrazit vaše brána v každém okamžiku v hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "on-premises data Gateway" a pak vyberte **On-premises Data Gateway**.

   ![Vyhledejte "On-premises Data Gateway"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Připojení k místním datům

Po vytvoření prostředku brány a přidružit tento prostředek vašeho předplatného Azure, můžete teď vytvořit připojení mezi vaší aplikace logiky a místní zdroje dat pomocí brány.

1. Na webu Azure Portal vytvořte nebo otevřete aplikaci logiky v návrháři aplikace logiky.

2. Přidejte konektor, který podporuje místní připojení, například **systému SQL Server**.

3. Nyní nastavte připojení:

   1. Vyberte **připojit přes místní bránu dat**. 

   2. Pro **brány**, vyberte prostředek brány, kterou jste vytvořili dřív. 

      I když vaše brána připojení umístění musí existovat ve stejné oblasti jako aplikace logiky, můžete vybrat bránu v jiné oblasti.

   3. Zadejte jedinečný název a další požadované informace. 

      Název jedinečný připojení pomáhá snadno identifikovat toto připojení později, zejména v případě, že vytvoříte několik připojení. Pokud je k dispozici, také obsahovat kvalifikované domény pro vaše uživatelské jméno.
   
      Zde naleznete příklad:

      ![Vytvořte připojení mezi logiku aplikace a data brány](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Jakmile budete hotoví, vyberte **Vytvořit**. 

Připojení k bráně je nyní připraven pro aplikaci logiky použít.

## <a name="edit-connection"></a>Upravit připojení

Po vytvoření připojení brány pro vaši aplikaci logiky můžete později aktualizovat nastavení pro dané připojení.

1. Vyhledání připojení k bráně:

   * K vyhledání všech připojení rozhraní API pro pouze aplikaci logiky, v nabídce aplikace logiky, v části **nástroje pro vývoj**vyberte **připojení rozhraní API**. 
   
     ![Přejděte do aplikace logiky, vyberte "Připojení rozhraní API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Vyhledání všech připojení rozhraní API, které jsou přidružené k vašemu předplatnému Azure: 

     * V hlavní nabídce Azure přejděte na **všechny služby** > **webové** > **připojení rozhraní API**. 
     * Nebo v hlavní nabídce Azure přejděte na **všechny prostředky**.

2. Vyberte připojení brány a klikněte na tlačítko **připojení API. upravte**.

   > [!TIP]
   > Pokud vaše aktualizace se projeví, zkuste [zastavení a restartování služba Windows brány](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Odstranit prostředek brány

K vytváření prostředku různé brány, bránu přidružit k jinému prostředku nebo odebrání prostředku brány, můžete odstranit prostředek brány aniž by to ovlivnilo instalaci brány. 

1. V hlavní nabídce Azure přejděte na **všechny prostředky**. 

2. Vyhledejte a vyberte váš prostředek brány.

3. Pokud ještě není vybraná, v nabídce prostředků brány, vyberte **On-premises Data Gateway**. 

4. Na panelu nástrojů themeroller prostředků **odstranit**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Zabezpečení aplikací logiky](./logic-apps-securing-a-logic-app.md)
* [Běžných příkladů a scénářů pro logic apps](./logic-apps-examples-and-scenarios.md)
