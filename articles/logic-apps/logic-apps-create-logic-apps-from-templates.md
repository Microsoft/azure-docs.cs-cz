---
title: Vytváření pracovních postupů ze šablony – Azure Logic Apps | Dokumentace Microsoftu
description: Vytváření pracovních postupů rychleji pomocí šablony aplikace logiky v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 554171e02a369905bcf9cd4aa7bfa2e59ab46336
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229753"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Vytvoření pracovních postupů aplikace logiky z předem připravených šablon

Abyste mohli začít vytváření pracovních postupů rychleji, Logic Apps poskytuje šablony, které jsou předem připravených logic apps, které následují běžně používané vzory. Pomocí těchto šablon, jak je uvedeno nebo upravit je, aby vám vyhovoval.

Tady jsou některé kategorie šablon:

| Typ šablony | Popis | 
| ------------- | ----------- | 
| Enterprise cloud šablony | Pro integraci objektů Blob v Azure, Dynamics CRM, Salesforce, Box a obsahuje jiné konektory pro cloud vaše organizace potřebuje. Například můžete použít tyto šablony k uspořádání potenciální nebo zálohovat data podnikových souborů. | 
| Osobní produktivitu šablony | Zvýšit osobní produktivitu tím, že nastavíte denní připomenutí, zapnutí důležité pracovní položky do seznamů úkolů a automatizace zdlouhavým úlohám dolů krok schválení jednoho uživatele. | 
| Cloud – šablony příjemce | Pro integraci služby sociálních médií, jako je Twitter, Slack a e-mailu. Užitečné pro posílení marketing iniciativy sociálních médií. Tyto šablony také obsahovat úlohy, jako je kopírování, cloudu, což zvyšuje úroveň produktivity úspora času tradičně opakujícími se úkoly. | 
| Enterprise integration pack šablony | Pro konfiguraci VETER (ověření, extrakce, transformace, rozšiřovat, směrování) kanály, které přijímají x X12 dokumentu EDI přes AS2 a transformovat XML a zpracování X12, EDIFACT a zprávy AS2. | 
| Protokol vzor šablony | Pro implementaci protokolu vzory, třeba typu žádost odpověď prostřednictvím protokolu HTTP a integrace v rámci FTP a SFTP. Pomocí těchto šablon, jak je uvedeno nebo sestavení na nich vzory komplexní protokolu. | 
||| 

Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. Další informace o vytváření aplikace logiky najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Vytváření aplikací logiky ze šablon

1. Pokud jste to ještě neudělali, přihlaste se k [webu Azure portal](https://portal.azure.com "webu Azure portal").

2. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

   ![Azure Portal, Nový, Podniková integrace, Aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Vytvořte aplikaci logiky s použitím nastavení uvedeného v tabulce pod tímto obrázkem:

   ![Zadání podrobností o aplikaci logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | *název_vaší_aplikace_logiky* | Zadejte jedinečný název aplikace logiky. | 
   | **Předplatné** | *název_vašeho_předplatného_Azure* | Vyberte předplatné Azure, které chcete použít. | 
   | **Skupina prostředků** | *název_vaší_skupiny_prostředků_Azure* | Vytvořte nebo vyberte [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) pro tuto aplikaci logiky a uspořádat všechny prostředky přidružené k této aplikaci. | 
   | **Umístění** | *oblast_vašeho_datového_centra_Azure* | Vyberte oblast datového centra pro nasazení aplikace logiky, například USA – západ. | 
   | **Log Analytics** | **Vypnout** (výchozí) nebo **na** | Zapnout [protokolování diagnostiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) pro vaši aplikaci logiky přes [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Vyžaduje se, že už máte pracovní prostor Log Analytics. | 
   |||| 

4. Až budete připraveni, vyberte **Připnout na řídicí panel**. Aplikace logiky se tak automaticky zobrazí na řídicím panelu Azure a po nasazení se automaticky otevře. Zvolte **Vytvořit**.

   > [!NOTE]
   > Pokud nechcete svou aplikaci logiky připnout, po nasazení ji musíte ručně vyhledat a otevřít, abyste mohli pokračovat.

   Jakmile Azure nasadí vaši aplikaci logiky, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem. 
   Pod videem najdete šablony pro běžné vzory aplikací logiky. 

5. Posunout po zavedení video a běžné triggery na **šablony**. Výběr předem připravené šablony. Příklad:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Chcete-li vytvořit aplikaci logiky úplně od začátku, zvolte **prázdná aplikace logiky**.

   Když vyberete předem připravených šablon, můžete zobrazit další informace o této šabloně. 
   Příklad:

   ![Zvolte předem připravených šablon](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Chcete-li pokračovat pomocí šablony vybrané, zvolte **pomocí této šablony**. 

7. Podle konektorů v šabloně, zobrazí se výzva k provedení těchto kroků:

   * Přihlaste se pomocí přihlašovacích údajů k systémům nebo službám, které odkazují šablony.

   * Vytvořte připojení pro žádné služby ani systémy odkazována pomocí šablony. Vytvoření připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste už nastavili tato připojení, zvolit **pokračovat**.

   Příklad:

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Až budete hotovi, vaše aplikace logiky otevře a zobrazí se v návrháři pro Logic Apps.

   > [!TIP]
   > Vraťte se do prohlížeče šablony, zvolte **šablony** na panelu nástrojů návrháře. Tato akce zahodí všechny neuložené změny, takže k potvrzení vaší žádosti se zobrazí zpráva s upozorněním.

8. Pokračujte v sestavování aplikace logiky.

   > [!NOTE] 
   > Mnoho šablon zahrnují konektory, které může mít už předem požadované vlastnosti. Ale některé šablony může být stále vyžadují, abyste zadali hodnoty před správně nasazením aplikace logiky. Pokud při pokusu nasadit bez dokončení chybí pole vlastností, obdržíte chybovou zprávu. 

## <a name="update-logic-apps-with-templates"></a>Aktualizace aplikací logiky s využitím šablon

1. V [webu Azure portal](https://portal.azure.com "webu Azure portal"), vyhledání a otevření aplikace logiky v th návrhář aplikace logiky.

2. Na panelu nástrojů návrháře zvolte **šablony**. Tato akce zahodí všechny neuložené změny, takže si můžete ověřit, že chcete pokračovat, zobrazí se zpráva upozornění. Pokud chcete ověřit, tlačítko **OK**. Příklad:

   ![Zvolte možnost "Šablony"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Posunout po zavedení video a běžné triggery na **šablony**. Výběr předem připravené šablony. Příklad:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Když vyberete předem připravených šablon, můžete zobrazit další informace o této šabloně. 
   Příklad:

   ![Zvolte předem připravených šablon](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Chcete-li pokračovat pomocí šablony vybrané, zvolte **pomocí této šablony**. 

5. Podle konektorů v šabloně, zobrazí se výzva k provedení těchto kroků:

   * Přihlaste se pomocí přihlašovacích údajů k systémům nebo službám, které odkazují šablony.

   * Vytvořte připojení pro žádné služby ani systémy odkazována pomocí šablony. Vytvoření připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste už nastavili tato připojení, zvolit **pokračovat**.

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Aplikace logiky teď otevře a zobrazí se v návrháři pro Logic Apps.

8. Pokračujte v sestavování aplikace logiky. 

   > [!TIP]
   > Neuložili jste změny, můžete zrušit svou práci a vrátit k předchozí aplikace logiky. Na panelu nástrojů návrháře zvolte **zahodit**.

> [!NOTE] 
> Mnoho šablon zahrnují konektory, které může mít už předem vyplní požadované vlastnosti. Ale některé šablony může být stále vyžadují, abyste zadali hodnoty před správně nasazením aplikace logiky. Pokud při pokusu nasadit bez dokončení chybí pole vlastností, obdržíte chybovou zprávu.

## <a name="deploy-logic-apps-built-from-templates"></a>Nasazení aplikace logiky vytvořené ze šablony

Když provedete změny šablony, můžete svoje změny uložíte. Tato akce také automaticky publikuje vaši aplikaci logiky.

Na panelu nástrojů návrháře zvolte **Uložit**.

![Uložení a publikování aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

Další informace o vytváření aplikací logiky pomocí příklady, scénáře, příběhy zákazníků a názorné postupy.

> [!div class="nextstepaction"]
> [Projděte si příklady aplikaci logiky, scénáře a názorné postupy](../logic-apps/logic-apps-examples-and-scenarios.md)