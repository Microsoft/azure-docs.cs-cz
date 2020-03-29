---
title: Rychlejší vytváření pracovních postupů aplikace logiky pomocí předem sestavených šablon
description: Rychlé vytváření pracovních postupů aplikace logiky pomocí předem vytvořených šablon poskytovaných aplikacemi Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: aac0060527af7b7d880f971e7608be3fa44a2d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905110"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Vytváření pracovních postupů aplikací logiky z předem připravených šablon

Chcete-li začít vytvářet pracovní postupy rychleji, Logic Apps poskytuje šablony, které jsou předem vytvořené aplikace logiky, které se řídí běžně používanými vzory. Použijte tyto šablony, jak je k dispozici, nebo je upravte tak, aby vyhovovaly vašemu scénáři.

Zde jsou některé kategorie šablon:

| Typ šablony | Popis | 
| ------------- | ----------- | 
| Šablony podnikového cloudu | Pro integraci azure blob, Dynamics CRM, Salesforce, Box a obsahuje další konektory pro potřeby podnikového cloudu. Tyto šablony můžete například použít k uspořádání obchodních zájemců nebo k zálohování podnikových dat souborů. | 
| Šablony osobní produktivity | Zvyšte osobní produktivitu nastavením denních připomenutí, přeměnou důležitých pracovních položek na seznamy úkolů a automatizací zdlouhavých úkolů až do jednoho kroku schválení uživatelem. | 
| Šablony spotřebitelského cloudu | Pro integraci služeb sociálních médií, jako je Twitter, Slack a e-mail. Užitečné pro posílení marketingových iniciativ sociálních médií. Tyto šablony také zahrnují úkoly, jako je kopírování v cloudu, což zvyšuje produktivitu tím, že šetří čas na tradičně opakujících se úlohách. | 
| Šablony podnikových integračních balíčků | Pro konfiguraci kanálů VETER (ověření, extrakce, transformace, obohacení, směrování), přijetí dokumentu X12 EDI přes AS2 a transformace na XML a zpracování zpráv X12, EDIFACT a AS2. | 
| Šablony vzorů protokolu | Pro implementaci vzorů protokolu, jako je například odpověď na požadavek přes protokol HTTP a integrace přes FTP a SFTP. Použijte tyto šablony, jak je k dispozici, nebo na nich sestavte pro složité vzory protokolů. | 
||| 

Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. Další informace o vytváření aplikace logiky najdete v [tématu vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Vytvoření aplikací logiky ze šablon

1. Pokud jste to ještě neudělali, přihlaste se na [portál Azure](https://portal.azure.com "portál Azure").

2. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Enterprise Integration** > **Logic App**.

   ![Azure Portal, Nový, Podniková integrace, Aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Vytvořte aplikaci logiky s použitím nastavení uvedeného v tabulce pod tímto obrázkem:

   ![Zadání podrobností o aplikaci logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | *název_vaší_aplikace_logiky* | Zadejte jedinečný název aplikace logiky. | 
   | **Předplatné** | *název_vašeho_předplatného_Azure* | Vyberte předplatné Azure, které chcete použít. | 
   | **Skupina prostředků** | *název_vaší_skupiny_prostředků_Azure* | Vytvořte nebo vyberte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pro tuto aplikaci logiky a uspořádat všechny prostředky přidružené k této aplikaci. | 
   | **Umístění** | *oblast_vašeho_datového_centra_Azure* | Vyberte oblast datového centra pro nasazení aplikace logiky, například USA – západ. | 
   | **Log Analytics** | **Vypnuto** (výchozí) nebo **Zapnuto** | Nastavte [protokolování diagnostiky](../logic-apps/monitor-logic-apps-log-analytics.md) pro aplikaci logiky pomocí [protokolů Azure Monitor](../log-analytics/log-analytics-overview.md). Vyžaduje, abyste již měli pracovní prostor Analýzy protokolů. | 
   |||| 

4. Až budete připraveni, vyberte **Připnout na řídicí panel**. Aplikace logiky se tak automaticky zobrazí na řídicím panelu Azure a po nasazení se automaticky otevře. Zvolte **Vytvořit**.

   > [!NOTE]
   > Pokud nechcete svou aplikaci logiky připnout, po nasazení ji musíte ručně vyhledat a otevřít, abyste mohli pokračovat.

   Jakmile Azure nasadí vaši aplikaci logiky, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem. 
   Pod videem najdete šablony pro běžné vzory aplikací logiky. 

5. Přejděte za úvodní video a běžné aktivační události na **šablony**. Zvolte předem sestavenou šablonu. Například:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Chcete-li vytvořit aplikaci logiky od začátku, zvolte **Blank Logic App**.

   Když vyberete předem sestavenou šablonu, můžete zobrazit další informace o této šabloně. 
   Například:

   ![Výběr předem sestavené šablony](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Chcete-li pokračovat ve vybrané šabloně, zvolte **Použít tuto šablonu**. 

7. Na základě konektorů v šabloně budete vyzváni k provedení některého z těchto kroků:

   * Přihlaste se pomocí přihlašovacích údajů k systémům nebo službám, na které šablona odkazuje.

   * Vytvořte připojení pro všechny služby nebo systémy, na které šablona odkazuje. Chcete-li vytvořit připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste tato připojení již nastavili, zvolte **Pokračovat**.

   Například:

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Až budete hotovi, aplikace logiky se otevře a zobrazí se v Návrháři logických aplikací.

   > [!TIP]
   > Chcete-li se vrátit do prohlížeče šablon, zvolte **Šablony** na panelu nástrojů návrháře. Tato akce zahodí všechny neuložené změny, takže se zobrazí varovná zpráva potvrzující váš požadavek.

8. Pokračujte v vytváření aplikace logiky.

   > [!NOTE] 
   > Mnoho šablon obsahuje konektory, které již mohou mít předem vyplněné požadované vlastnosti. Některé šablony však může stále vyžadovat, abyste zadali hodnoty před správně nasadit aplikaci logiky. Pokud se pokusíte nasadit bez dokončení chybějící pole vlastností, zobrazí se chybová zpráva. 

## <a name="update-logic-apps-with-templates"></a>Aktualizace aplikací logiky pomocí šablon

1. Na [webu Azure Portal](https://portal.azure.com "portál Azure")najděte a otevřete aplikaci logiky v návrháři aplikace logiky.

2. Na panelu nástrojů návrháře zvolte **Šablony**. Tato akce zahodí všechny neuložené změny, takže se zobrazí varovná zpráva, abyste mohli potvrdit, že chcete pokračovat. Chcete-li potvrdit, zvolte **OK**. Například:

   ![Zvolte "Šablony"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Přejděte za úvodní video a běžné aktivační události na **šablony**. Zvolte předem sestavenou šablonu. Například:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Když vyberete předem sestavenou šablonu, můžete zobrazit další informace o této šabloně. 
   Například:

   ![Výběr předem sestavené šablony](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Chcete-li pokračovat ve vybrané šabloně, zvolte **Použít tuto šablonu**. 

5. Na základě konektorů v šabloně budete vyzváni k provedení některého z těchto kroků:

   * Přihlaste se pomocí přihlašovacích údajů k systémům nebo službám, na které šablona odkazuje.

   * Vytvořte připojení pro všechny služby nebo systémy, na které šablona odkazuje. Chcete-li vytvořit připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste tato připojení již nastavili, zvolte **Pokračovat**.

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Vaše aplikace logiky se teď otevře a zobrazí se v Návrháři aplikací logiky.

8. Pokračujte v vytváření aplikace logiky. 

   > [!TIP]
   > Pokud jste změny neuložili, můžete svou práci zahodit a vrátit se k předchozí aplikaci logiky. Na panelu nástrojů návrháře zvolte **Zahodit**.

> [!NOTE] 
> Mnoho šablon obsahuje konektory, které již mohou mít předem vyplněné požadované vlastnosti. Některé šablony však může stále vyžadovat, abyste zadali hodnoty před správně nasadit aplikaci logiky. Pokud se pokusíte nasadit bez dokončení chybějící pole vlastností, zobrazí se chybová zpráva.

## <a name="deploy-logic-apps-built-from-templates"></a>Nasazení aplikací logiky vytvořených ze šablon

Po provedeném provádění změn v šabloně můžete změny uložit. Tato akce také automaticky publikuje aplikaci logiky.

Na panelu nástrojů návrháře zvolte **Uložit**.

![Uložení a publikování aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

Další informace o vytváření aplikací logiky prostřednictvím příkladů, scénářů, příběhů zákazníků a návodů.

> [!div class="nextstepaction"]
> [Kontrola příkladů aplikací logiky, scénářů a návodů](../logic-apps/logic-apps-examples-and-scenarios.md)