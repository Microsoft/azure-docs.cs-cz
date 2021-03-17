---
title: Rychlejší vytváření pracovních postupů aplikací logiky pomocí předem připravených šablon
description: Rychlé vytváření pracovních postupů aplikací logiky pomocí předem připravených šablon poskytovaných Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593103"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Vytváření pracovních postupů aplikací logiky z předem připravených šablon

Pokud chcete začít vytvářet pracovní postupy rychleji, Logic Apps poskytuje šablony, které jsou předem připravené Logic Apps, které následují běžně používané vzory. Použijte tyto šablony, které jsou k dispozici, nebo je upravte tak, aby vyhovovaly vašemu scénáři.

Tady je několik kategorií šablon:

| Typ šablony | Description | 
| ------------- | ----------- | 
| Šablony podnikového cloudu | Pro integraci Azure Blob, Dynamics CRM, Salesforce, box a zahrnutí dalších konektorů pro potřeby podnikového cloudu. Tyto šablony můžete například použít k organizování obchodních zájemců nebo zálohování dat podnikových souborů. | 
| Osobní šablony produktivity | Zvýšení osobní produktivity nastavením každodenních připomenutí, zapnutím důležitých pracovních položek do seznamů úkolů a automatizací zdlouhavých úloh do jednoho kroku schválení uživatele. | 
| Šablony cloudu pro spotřebitele | Pro integraci sociálních mediálních služeb, jako jsou Twitter, časová rezerva a e-mail. Užitečné pro posílení marketingových iniciativ na sociálních médiích. Tyto šablony také obsahují úlohy, jako je kopírování do cloudu, což zvyšuje produktivitu tím, že šetří čas u tradičně opakujících se úloh. | 
| Šablony podnikového integračního balíčku | Pro konfiguraci kanálů VETER (ověření, extrakce, transformace, obohacení, směrování), přijímání X12 EDI dokumentu nad AS2 a transformaci do XML a zpracování zpráv X12, EDIFACT a AS2. | 
| Šablony vzorů protokolů | Pro implementaci vzorů protokolů, jako je požadavek-odpověď přes protokol HTTP a integrace přes FTP a SFTP. Použijte tyto šablony, které jsou k dispozici, nebo je Sestavte pro složité vzory protokolu. | 
||| 

Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. Další informace o vytváření aplikace logiky najdete v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Vytvoření aplikací logiky ze šablon

1. Pokud jste to ještě neudělali, přihlaste se k [Azure Portal](https://portal.azure.com "portál Azure").

2. V hlavní nabídce Azure vyberte **vytvořit prostředek**  >  **Podniková integrace**  >  **aplikaci logiky**.

   ![Azure Portal, Nový, Podniková integrace, Aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Vytvořte aplikaci logiky s použitím nastavení uvedeného v tabulce pod tímto obrázkem:

   ![Zadání podrobností o aplikaci logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | *název_vaší_aplikace_logiky* | Zadejte jedinečný název aplikace logiky. | 
   | **Předplatné** | *název_vašeho_předplatného_Azure* | Vyberte předplatné Azure, které chcete použít. | 
   | **Skupina prostředků** | *název_vaší_skupiny_prostředků_Azure* | Vytvořte nebo vyberte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pro tuto aplikaci logiky a uspořádejte všechny prostředky přidružené k této aplikaci. | 
   | **Umístění** | *oblast_vašeho_datového_centra_Azure* | Vyberte oblast datového centra pro nasazení aplikace logiky, například USA – západ. | 
   | **Log Analytics** | **Vypnuto** (výchozí) nebo **zapnuto** | Pomocí [protokolů Azure monitor](../azure-monitor/logs/log-query-overview.md)nastavte [protokolování diagnostiky](../logic-apps/monitor-logic-apps-log-analytics.md) pro vaši aplikaci logiky. Vyžaduje, abyste již Log Analytics pracovní prostor. | 
   |||| 

4. Až budete připraveni, vyberte **Připnout na řídicí panel**. Aplikace logiky se tak automaticky zobrazí na řídicím panelu Azure a po nasazení se automaticky otevře. Vyberte **vytvořit**.

   > [!NOTE]
   > Pokud nechcete svou aplikaci logiky připnout, po nasazení ji musíte ručně vyhledat a otevřít, abyste mohli pokračovat.

   Jakmile Azure nasadí vaši aplikaci logiky, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem. 
   Pod videem najdete šablony pro běžné vzory aplikací logiky. 

5. Posuňte se za úvodní video a běžné aktivační události do **šablon**. Vyberte předem vytvořenou šablonu. Příklad:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Pokud chcete vytvořit aplikaci logiky od začátku, klikněte na **prázdná aplikace logiky**.

   Když vyberete předem vytvořenou šablonu, můžete zobrazit další informace o této šabloně. 
   Příklad:

   ![Zvolit předem vytvořenou šablonu](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Chcete-li pokračovat s vybranou šablonou, vyberte možnost **použít tuto šablonu**. 

7. Na základě konektorů v šabloně se zobrazí výzva, abyste provedli některý z těchto kroků:

   * Přihlaste se pomocí svých přihlašovacích údajů na systémy nebo služby, na které se odkazuje šablona.

   * Vytvořte připojení pro všechny služby nebo systémy, na které odkazuje šablona. Chcete-li vytvořit připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste již nastavili tato připojení, klikněte na tlačítko **pokračovat**.

   Příklad:

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Jakmile budete hotovi, otevře se aplikace logiky a zobrazí se v Návrháři Logic Apps.

   > [!TIP]
   > Chcete-li se vrátit do prohlížeče šablon, klikněte na tlačítko **šablony** na panelu nástrojů návrháře. Tato akce zahodí všechny neuložené změny, takže se zobrazí zpráva upozorňující na potvrzení vaší žádosti.

8. Pokračujte v sestavování aplikace logiky.

   > [!NOTE] 
   > Mnoho šablon obsahuje konektory, které již mohou mít předem vyplněné požadované vlastnosti. Některé šablony však stále vyžadují, abyste zadali hodnoty před tím, než bude možné správně nasadit aplikaci logiky. Pokud se pokusíte nasadit bez vyplnění polí chybějících vlastností, zobrazí se chybová zpráva. 

## <a name="update-logic-apps-with-templates"></a>Aktualizace Logic Apps pomocí šablon

1. V [Azure Portal](https://portal.azure.com "portál Azure")vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky.

2. Na panelu nástrojů návrháře vyberte **šablony**. Tato akce zahodí všechny neuložené změny, takže se zobrazí varovná zpráva, abyste mohli potvrdit, že chcete pokračovat. Pro potvrzení klikněte na **tlačítko OK**. Příklad:

   ![Zvolit šablony](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Posuňte se za úvodní video a běžné aktivační události do **šablon**. Vyberte předem vytvořenou šablonu. Příklad:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Když vyberete předem vytvořenou šablonu, můžete zobrazit další informace o této šabloně. 
   Příklad:

   ![Zvolit předem vytvořenou šablonu](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Chcete-li pokračovat s vybranou šablonou, vyberte možnost **použít tuto šablonu**. 

5. Na základě konektorů v šabloně se zobrazí výzva, abyste provedli některý z těchto kroků:

   * Přihlaste se pomocí svých přihlašovacích údajů na systémy nebo služby, na které se odkazuje šablona.

   * Vytvořte připojení pro všechny služby nebo systémy, na které odkazuje šablona. Chcete-li vytvořit připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste již nastavili tato připojení, klikněte na tlačítko **pokračovat**.

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Vaše aplikace logiky se teď otevře a zobrazí se v Návrháři Logic Apps.

8. Pokračujte v sestavování aplikace logiky. 

   > [!TIP]
   > Pokud jste změny neuložili, můžete svou práci zrušit a vrátit se k předchozí aplikaci logiky. Na panelu nástrojů návrháře klikněte na **Zrušit**.

> [!NOTE] 
> Mnoho šablon obsahuje konektory, které mohou mít již předem vyplněné požadované vlastnosti. Některé šablony však stále vyžadují, abyste zadali hodnoty před tím, než bude možné správně nasadit aplikaci logiky. Pokud se pokusíte nasadit bez vyplnění polí chybějících vlastností, zobrazí se chybová zpráva.

## <a name="deploy-logic-apps-built-from-templates"></a>Nasazení Logic Apps sestavené ze šablon

Po provedení změn v šabloně můžete změny uložit. Tato akce také automaticky publikuje vaši aplikaci logiky.

Na panelu nástrojů návrháře zvolte **Uložit**.

![Uložení a publikování aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Získání podpory

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

Naučte se sestavovat aplikace logiky prostřednictvím příkladů, scénářů, zákaznických scénářů a návodů.

> [!div class="nextstepaction"]
> [Kontrola příkladů, scénářů a názorných postupů aplikace logiky](../logic-apps/logic-apps-examples-and-scenarios.md)
