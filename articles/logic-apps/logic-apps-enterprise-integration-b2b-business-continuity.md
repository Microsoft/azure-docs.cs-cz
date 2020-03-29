---
title: Zotavení po havárii pro integrační účty
description: Nastavení integračních účtů a artefaktů B2B s obnovením zotavení po havárii napříč oblastmi v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905136"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Nastavení zotavení po havárii napříč oblastmi pro účty integrace v Aplikacích Azure Logic Apps

Úlohy B2B zahrnují peněžní transakce, jako jsou objednávky a faktury. Během události způsobené katastrofou je důležité, aby se firma rychle zotavila a splnila tak dohody s obchodními sítěmi, na kterých se dohodli se svými partnery. Tento článek ukazuje, jak vytvořit plán kontinuity podnikání pro úlohy B2B. 

* Připravenost zotavení po havárii 
* Převzetí služeb při selhání do sekundární oblasti během události katastrofy 
* Návrat do primární oblasti po katastrofě

## <a name="disaster-recovery-readiness"></a>Připravenost zotavení po havárii  

1. Identifikujte sekundární oblast a [vytvořte účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) v sekundární oblasti.

2. Přidejte partnery, schémata a dohody pro toky požadovaných zpráv, kde stav spuštění musí být replikován do účtu integrace sekundární oblasti.

   > [!TIP]
   > Ujistěte se, že je konzistence v konvenci pojmenování artefaktu účtu integrace napříč oblastmi. 

3. Chcete-li vyžádat stav spuštění z primární oblasti, vytvořte aplikaci logiky v sekundární oblasti. 

   Tato aplikace logiky by měla mít *aktivační událost* a *akci*. 
   Aktivační událost by se měla připojit k účtu integrace primární oblasti a akce by se měla připojit k účtu integrace sekundární oblasti. 
   Na základě časového intervalu se aktivační událost aktivuje v primární tabulce stavu spuštění oblasti a vytáhne nové záznamy, pokud existuje. Akce je aktualizuje na účet integrace sekundární oblasti. 
   To pomáhá získat přírůstkový stav runtime z primární oblasti do sekundární oblasti.

4. Kontinuita podnikání v účtu integrace Logic Apps je navržena tak, aby podporovala na základě protokolů B2B - X12, AS2 a EDIFACT. Chcete-li najít podrobné kroky, vyberte příslušné odkazy.

5. Doporučujeme také nasadit všechny prostředky primární oblasti v sekundární oblasti. 

   Mezi prostředky primární oblasti patří Azure SQL Database nebo Azure Cosmos DB, Azure Service Bus a Azure Event Hubs používané pro zasílání zpráv, Azure API Management a funkce Azure Logic Apps ve službě Azure App Service.   

6. Navázat připojení z primární oblasti do sekundární oblasti. Chcete-li vyžádat stav spuštění z primární oblasti, vytvořte aplikaci logiky v sekundární oblasti. 

   Aplikace logiky by měla mít aktivační událost a akci. 
   Aktivační událost by se měla připojit k účtu integrace primární oblasti. 
   Akce by se měla připojit k účtu integrace sekundární oblasti. 
   Na základě časového intervalu se aktivační událost aktivuje v primární tabulce stavu spuštění oblasti a vytáhne nové záznamy, pokud existuje. 
   Akce je aktualizuje na účet integrace sekundární oblasti. 
   Tento proces pomáhá získat přírůstkový stav runtime z primární oblasti do sekundární oblasti.

Kontinuita podnikání v účtu integrace Logic Apps poskytuje podporu založenou na protokolech B2B X12, AS2 a EDIFACT. Podrobné kroky k použití X12 a AS2 naleznete v tématu [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) a [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) v tomto článku.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Převzetí služeb při selhání do sekundární oblasti během události havárie

Během události po havárii, pokud primární oblast není k dispozici pro kontinuitu provozu, přímý provoz do sekundární oblasti. Sekundární oblast pomáhá podniku rychle obnovit funkce, aby splnila rpo/rto, na které se dohodli jejich partneři. Také minimalizuje úsilí o převzetí služeb při selhání z jedné oblasti do jiné oblasti. 

Existuje očekávaná latence při kopírování řídicích čísel z primární oblasti do sekundární oblasti. Chcete-li se vyhnout odesílání duplicitních generovaných řídicích čísel partnerům během události [katastrofy,](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)doporučujeme zvýšení počtu ovládacích míst v dohodách sekundární oblasti pomocí rutin prostředí PowerShell .

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Návrat k primární oblasti po katastrofě

Chcete-li se vrátit do primární oblasti, pokud je k dispozici, postupujte takto:

1. Zastavte přijímání zpráv od partnerů v sekundární oblasti.  

2. Zvýšení generovaných řídicích čísel pro všechny primární oblasti smlouvy pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Přímý provoz ze sekundární oblasti do primární oblasti.

4. Zkontrolujte, zda je povolena aplikace logiky vytvořená v sekundární oblasti pro vytahování stavu spuštění z primární oblasti.

## <a name="x12"></a>X12 

Kontinuita provozu dokumentů EDI X12 je založena na kontrolních číslech:

> [!TIP]
> K vytváření aplikací logiky můžete také použít [šablonu rychlého startu X12.](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) Vytvoření účtů primární a sekundární integrace jsou předpokladem pro použití šablony. Šablona pomáhá vytvořit dvě aplikace logiky, jednu pro přijatá čísla ovládacích prvku a druhou pro vygenerovaná čísla ovládacích prvku. Příslušné aktivační události a akce jsou vytvořeny v aplikacích logiky, připojení aktivační události k primární účet integrace a akce k účtu sekundární integrace.

**Požadavky**

Chcete-li povolit zotavení po havárii pro příchozí zprávy, vyberte nastavení duplicitní kontroly v nastavení příjmu smlouvy X12.

![Výběr duplicitních nastavení kontroly](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Vytvořte [aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Hledat na **X12**a vyberte **X12 - Při změně čísla ovládacího prvku**.   

   ![Hledat X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu integrace. 
   Aktivační událost by měla být připojena k účtu integrace primární oblasti.

3. Zadejte název připojení, ze seznamu vyberte *účet integrace primární oblasti* a zvolte **Vytvořit**.   

   ![Název účtu integrace primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Nastavení **Synchronizace čísel ovládacího prvku DateTime** pro spuštění ovládacího prvku je volitelné. **Frekvenci** lze nastavit na **den**, **hodinu**, **minutu**nebo **sekundu** s intervalem.   

   ![DateTime a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Vyberte **Nový krok:** > **Přidejte akci**.

   ![Nový krok, Přidání akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Hledat na **X12**a vyberte **X12 - Přidat nebo aktualizovat řídicí čísla**.   

   ![Přidání nebo aktualizace řídicích čísel](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Chcete-li připojit akci k účtu integrace sekundární oblasti, vyberte **změnit připojení** > **Přidat nové připojení** pro seznam dostupných účtů integrace. Zadejte název připojení, ze seznamu vyberte *účet integrace sekundární oblasti* a zvolte **Vytvořit**. 

   ![Název účtu integrace sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Přepněte na nezpracované vstupy kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Z výběru dynamického obsahu vyberte Text a uložte aplikaci logiky.

   ![Pole dynamického obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Na základě časového intervalu se aktivační událost aktivuje, že primární oblast obdržela tabulku čísel ovládacího prvku a vytáhne nové záznamy. 
   Akce aktualizuje záznamy v účtu integrace sekundární oblasti. 
   Pokud nejsou k dispozici žádné aktualizace, zobrazí se stav aktivační události jako **Přeskočeno**.   

   ![Tabulka kontrolních čísel](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Na základě časového intervalu se stav přírůstkového běhu replikuje z primární oblasti do sekundární oblasti. Během události po havárii, pokud primární oblast není k dispozici, přímý provoz do sekundární oblasti pro kontinuitu provozu. 

## <a name="edifact"></a>EDIFACT 

Kontinuita provozu dokumentů EDI EDIFACT je založena na kontrolních číslech.

**Požadavky**

Chcete-li povolit zotavení po havárii pro příchozí zprávy, vyberte nastavení duplicitní kontroly v nastavení příjmu smlouvy EDIFACT.

![Výběr duplicitních nastavení kontroly](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Vytvořte [aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Hledat na **EDIFACT**, a vyberte **EDIFACT - Při změně čísla ovládacího prvku**.

   ![Hledat EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu integrace. 
   Aktivační událost by měla být připojena k účtu integrace primární oblasti. 

3. Zadejte název připojení, ze seznamu vyberte *účet integrace primární oblasti* a zvolte **Vytvořit**.    

   ![Název účtu integrace primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Nastavení **Synchronizace čísel ovládacího prvku DateTime** pro spuštění ovládacího prvku je volitelné. **Frekvenci** lze nastavit na **den**, **hodinu**, **minutu**nebo **sekundu** s intervalem.    

   ![DateTime a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Vyberte **Nový krok:** > **Přidejte akci**.    

   ![Nový krok, Přidání akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Hledat na **EDIFACT**a vyberte **EDIFACT - Přidat nebo aktualizovat řídicí čísla**.   

   ![Přidání nebo aktualizace řídicích čísel](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Chcete-li připojit akci k účtu integrace sekundární oblasti, vyberte **změnit připojení** > **Přidat nové připojení** pro seznam dostupných účtů integrace. Zadejte název připojení, ze seznamu vyberte *účet integrace sekundární oblasti* a zvolte **Vytvořit**.

   ![Název účtu integrace sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Přepněte na nezpracované vstupy kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Z výběru dynamického obsahu vyberte Text a uložte aplikaci logiky.   

   ![Pole dynamického obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Na základě časového intervalu se aktivační událost aktivuje, že primární oblast obdržela tabulku čísel ovládacího prvku a vytáhne nové záznamy.
   Akce aktualizuje záznamy na účet integrace sekundární oblasti. 
   Pokud nejsou k dispozici žádné aktualizace, zobrazí se stav aktivační události jako **Přeskočeno**.

   ![Tabulka kontrolních čísel](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Na základě časového intervalu se stav přírůstkového běhu replikuje z primární oblasti do sekundární oblasti. Během události po havárii, pokud primární oblast není k dispozici, přímý provoz do sekundární oblasti pro kontinuitu provozu. 

## <a name="as2"></a>AS2 

Kontinuita provozu pro dokumenty, které používají protokol AS2, je založena na ID zprávy a hodnotě MIC.

> [!TIP]
> Můžete také použít [šablonu rychlého startu AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) k vytvoření aplikací logiky. Vytvoření účtů primární a sekundární integrace jsou předpokladem pro použití šablony. Šablona pomáhá vytvořit aplikaci logiky, která má aktivační událost a akci. Aplikace logiky vytvoří připojení z aktivační události na účet primární integrace a akce k účtu sekundární integrace.

1. Vytvořte [aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.  

2. Hledat na **AS2**a vyberte **AS2 - Při vytvoření hodnoty MIC**.   

   ![Hledat AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu integrace. 
   Aktivační událost by měla být připojena k účtu integrace primární oblasti. 
   
3. Zadejte název připojení, ze seznamu vyberte *účet integrace primární oblasti* a zvolte **Vytvořit**.

   ![Název účtu integrace primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Nastavení **synchronizace hodnot MIC DateTime pro spuštění je** volitelné. **Frekvenci** lze nastavit na **den**, **hodinu**, **minutu**nebo **sekundu** s intervalem.   

   ![DateTime a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Vyberte **Nový krok:** > **Přidejte akci**.  

   ![Nový krok, Přidání akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Hledat na **AS2**a vyberte **AS2 - Přidat nebo aktualizovat mic obsah**.  

   ![Přidání nebo aktualizace MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Chcete-li připojit akci k účtu sekundární integrace, vyberte **změnit připojení** > **Přidat nové připojení** pro seznam dostupných účtů integrace. Zadejte název připojení, ze seznamu vyberte *účet integrace sekundární oblasti* a zvolte **Vytvořit**.

   ![Název účtu integrace sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Přepněte na nezpracované vstupy kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Z výběru dynamického obsahu vyberte Text a uložte aplikaci logiky.   

   ![Dynamický obsah](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Na základě časového intervalu se aktivační událost aktivuje v tabulce primární oblasti a vytáhne nové záznamy. Akce je aktualizuje na účet integrace sekundární oblasti. 
   Pokud nejsou k dispozici žádné aktualizace, zobrazí se stav aktivační události jako **Přeskočeno**.  

   ![Tabulka primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Na základě časového intervalu se stav přírůstkového běhu replikuje z primární oblasti do sekundární oblasti. Během události po havárii, pokud primární oblast není k dispozici, přímý provoz do sekundární oblasti pro kontinuitu provozu. 

## <a name="next-steps"></a>Další kroky

[Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)

