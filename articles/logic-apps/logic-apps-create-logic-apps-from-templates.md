---
title: Vytváření pracovních postupů z šablon - Azure Logic Apps | Microsoft Docs
description: Vytváření pracovních postupů rychlejší pomocí šablony aplikace logiky
author: kevinlam1
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72aca1420d76a34f8f43f05b25d146eb743b3233
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298684"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Vytvořit pracovní postupy aplikace logiky z přednastavených šablony

Abyste mohli začít vytváření pracovních postupů rychleji, Logic Apps poskytuje šablony, které jsou předem logic apps, které následují běžně používané vzory. Použít tyto šablony, jak jsou, nebo upravit, aby vyhovovaly vašemu scénáři.

Zde jsou některé šablony kategorií:

| Typ šablony | Popis | 
| ------------- | ----------- | 
| Šablony organizace cloudu | K integraci Azure Blob, Dynamics CRM Salesforce, pole a obsahuje další konektory pro cloudu vaše organizace potřebuje. Například můžete použít tyto šablony k uspořádání potenciální zákazníci nebo zálohovat data podnikových souborů. | 
| Osobních kancelářských šablony | Zvýšení produktivity osobní nastavením denní připomenutí, vypnutí důležité pracovní položky do seznamu úkolů a automatizace zdlouhavé úlohy dolů krok schválení jednoho uživatele. | 
| Šablony příjemce cloudu | Pro integraci služby sociálních médií, jako je například Twitter, zvětralého a e-mailu. Tato možnost je užitečná pro posílení sociálních médií marketingové iniciativy. Tyto šablony také obsahovat úlohy, jako je kopírování, cloudu, což zvyšuje produktivitu uložením časové tradičně opakovaných úloh. | 
| Šablony organizace integration pack | Pro konfiguraci VETER (ověřit, extrakce, transformace, zlepšit komunikaci oddělení, směrovat) kanály, přijetí X12 dokumentu EDI přes AS2 a převod do formátu XML a zpracování X12, EDIFACT a AS2 zprávy. | 
| Protokol vzor šablony | Pro implementaci protokolu vzory, třeba požadavků a odpovědí prostřednictvím protokolu HTTP a integrace v rámci FTP a SFTP. Použít tyto šablony, jak jsou, nebo na jejich sestavení pro komplexní protokol vzory. | 
||| 

Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. Další informace o vytváření aplikace logiky najdete v tématu [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Vytvoření aplikace logiky ze šablon

1. Pokud jste to ještě neudělali, přihlaste se k [portál Azure](https://portal.azure.com "portál Azure").

2. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

   ![Azure Portal, Nový, Podniková integrace, Aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Vytvořte aplikaci logiky s použitím nastavení uvedeného v tabulce pod tímto obrázkem:

   ![Zadání podrobností o aplikaci logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | *název_vaší_aplikace_logiky* | Zadejte jedinečný název aplikace logiky. | 
   | **Předplatné** | *název_vašeho_předplatného_Azure* | Vyberte předplatné Azure, které chcete použít. | 
   | **Skupina prostředků** | *název_vaší_skupiny_prostředků_Azure* | Vytvořte nebo vyberte [skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) pro tuto aplikaci logiky a uspořádat všechny prostředky přidružené k této aplikaci. | 
   | **Umístění** | *oblast_vašeho_datového_centra_Azure* | Vyberte oblast datového centra pro nasazení aplikace logiky, například USA – západ. | 
   | **Log Analytics** | **Vypnout** (výchozí) nebo **na** | Zapnout [protokolování diagnostiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) pro svou aplikaci logiky prostřednictvím [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Vyžaduje se, že už máte pracovní prostor analýzy protokolů. | 
   |||| 

4. Až budete připraveni, vyberte **Připnout na řídicí panel**. Aplikace logiky se tak automaticky zobrazí na řídicím panelu Azure a po nasazení se automaticky otevře. Zvolte **Vytvořit**.

   > [!NOTE]
   > Pokud nechcete svou aplikaci logiky připnout, po nasazení ji musíte ručně vyhledat a otevřít, abyste mohli pokračovat.

   Jakmile Azure nasadí vaši aplikaci logiky, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem. 
   Pod videem najdete šablony pro běžné vzory aplikací logiky. 

5. Posuv po Úvod video a běžné aktivačních událostí na **šablony**. Vyberte šablonu předem. Příklad:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Chcete-li vytvořit aplikaci logiky od začátku, zvolte **prázdné aplikace logiky**.

   Když vyberete šablonu předem, můžete zobrazit další informace o této šablony. 
   Příklad:

   ![Vyberte šablonu předem](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Chcete-li pokračovat s vybrané šablony, vyberte **tuto šablonu použít**. 

7. Podle toho, konektory v šabloně, zobrazí se výzva k provádět tyto kroky:

   * Přihlaste se pomocí přihlašovacích údajů k systému nebo služby, které odkazují šablony.

   * Vytvořte připojení pro žádné služby ani systémy, které odkazuje šablona. Vytvoření připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste již nastavili tato připojení, zvolte **pokračovat**.

   Příklad:

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Když jste hotovi, aplikace logiky otevře a zobrazí se v Návrháři logiku aplikace.

   > [!TIP]
   > Chcete-li vrátit do prohlížeče šablony, zvolte **šablony** na panelu nástrojů návrháře. Tato akce zruší veškeré neuložené změny, tak pro vaši žádost o potvrzení, zobrazí se upozornění.

8. Pokračujte ve vytváření aplikace logiky.

   > [!NOTE] 
   > Mnoho šablony zahrnují konektory, které může mít již naplněnými požadované vlastnosti. Ale některé šablony může být stále vyžadují, abyste zadali hodnoty než budete moci správně nasadit aplikaci logiky. Pokud se pokusíte nasadit bez dokončení chybí pole vlastností, zobrazí chybovou zprávu. 

## <a name="update-logic-apps-with-templates"></a>Aktualizace aplikací logiky s šablonami

1. V [portál Azure](https://portal.azure.com "portál Azure"), najít a otevřít aplikaci logiky v tý návrhář aplikace logiky.

2. Na panelu nástrojů návrháře zvolte **šablony**. Tato akce zruší veškeré neuložené změny, takže můžete potvrdit, že chcete pokračovat, zobrazí se upozornění. Pokud chcete potvrdit, vyberte **OK**. Příklad:

   ![Zvolte "Šablony"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Posuv po Úvod video a běžné aktivačních událostí na **šablony**. Vyberte šablonu předem. Příklad:

   ![Výběr šablony aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Když vyberete šablonu předem, můžete zobrazit další informace o této šablony. 
   Příklad:

   ![Vyberte šablonu předem](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Chcete-li pokračovat s vybrané šablony, vyberte **tuto šablonu použít**. 

5. Podle toho, konektory v šabloně, zobrazí se výzva k provádět tyto kroky:

   * Přihlaste se pomocí přihlašovacích údajů k systému nebo služby, které odkazují šablony.

   * Vytvořte připojení pro žádné služby ani systémy, které odkazuje šablona. Vytvoření připojení, zadejte název připojení a v případě potřeby vyberte prostředek, který chcete použít. 

   * Pokud jste již nastavili tato připojení, zvolte **pokračovat**.

   ![Vytvoření připojení](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Aplikace logiky nyní otevře a zobrazí se v Návrháři logiku aplikace.

8. Pokračujte ve vytváření aplikace logiky. 

   > [!TIP]
   > Pokud vaše změny nebyly uloženy, můžete zrušit práci a vrátit k předchozí aplikaci logiky. Na panelu nástrojů návrháře zvolte **zahodit**.

> [!NOTE] 
> Mnoho šablony zahrnují konektory, které může mít již předem vyplnit požadované vlastnosti. Ale některé šablony může být stále vyžadují, abyste zadali hodnoty než budete moci správně nasadit aplikaci logiky. Pokud se pokusíte nasadit bez dokončení chybí pole vlastností, zobrazí chybovou zprávu.

## <a name="deploy-logic-apps-built-from-templates"></a>Nasazení aplikace logiky vytvořené z šablon

Když provedete změny šablony, můžete uložit provedené změny. Tato akce také automaticky publikuje aplikace logiky.

Na panelu nástrojů návrháře zvolte **Uložit**.

![Uložte a publikovat aplikace logiky](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

Informace o vytváření aplikací logiky příklady, scénáře, zákazník scénářů a návody.

> [!div class="nextstepaction"]
> [Zkontrolujte příklady aplikace logiky, scénáře a návody](../logic-apps/logic-apps-examples-and-scenarios.md)