---
title: Zotavení po havárii pro účty pro integraci
description: Nastavte si účty pro integraci a artefakty B2B s zotavením po havárii mezi oblastmi v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 1e006dd690e9c008afada8a490da6c3238bc0791
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565407"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Nastavení zotavení po havárii mezi oblastmi pro účty pro integraci v Azure Logic Apps

Úlohy B2B zahrnují peněžní transakce, jako jsou objednávky a faktury. Během události po havárii je velmi důležité, aby firma rychle obnovila požadavky na SLA na úrovni firmy dohodnuté se svými partnery. Tento článek ukazuje, jak vytvořit plán provozní kontinuity pro úlohy B2B. 

* Připravenost na zotavení po havárii 
* Převzetí služeb při selhání sekundární oblastí během události havárie 
* Přejít zpět k primární oblasti po havárii události

## <a name="disaster-recovery-readiness"></a>Připravenost na zotavení po havárii  

1. Identifikujte sekundární oblast a vytvořte [účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) v sekundární oblasti.

2. Přidejte partnery, schémata a smlouvy pro požadované toky zpráv, kde je potřeba replikovat stav spuštění do účtu integrace sekundární oblasti.

   > [!TIP]
   > Ujistěte se, že existuje konzistence v konvenci pojmenování artefaktů integračního účtu napříč oblastmi. 

3. Pokud chcete stáhnout stav spuštění z primární oblasti, vytvořte v sekundární oblasti aplikaci logiky. 

   Tato aplikace logiky by měla mít *Trigger* a *akci*. 
   Aktivační událost by se měla připojit k účtu pro integraci primární oblasti a akce by se měla připojit k účtu pro integraci sekundární oblasti. 
   V závislosti na časovém intervalu se aktivační událost dotazuje tabulky stavu spuštění primární oblasti a vyžádá si nové záznamy (pokud nějaké jsou). Tato akce aktualizuje účet pro integraci sekundární oblasti. 
   To pomáhá získat přírůstkový běhový stav z primární oblasti do sekundární oblasti.

4. Provozní kontinuita v Logic Apps účet integrace je navržená tak, aby podporovala v závislosti na protokolech B2B – X12, AS2 a EDIFACT. Chcete-li najít podrobné kroky, vyberte příslušné odkazy.

5. Doporučení je také k nasazení všech prostředků primární oblasti v sekundární oblasti. 

   Mezi prostředky primární oblasti patří Azure SQL Database nebo Azure Cosmos DB, Azure Service Bus a Event Hubs Azure, které se používají pro zasílání zpráv, Azure API Management a funkci Azure Logic Apps v Azure App Service.   

6. Navažte spojení z primární oblasti do sekundární oblasti. Pokud chcete stáhnout stav spuštění z primární oblasti, vytvořte v sekundární oblasti aplikaci logiky. 

   Aplikace logiky by měla mít aktivační událost a akci. 
   Aktivační událost by se měla připojit k účtu pro integraci primární oblasti. 
   Akce by se měla připojit k účtu pro integraci sekundární oblasti. 
   V závislosti na časovém intervalu se aktivační událost dotazuje tabulky stavu spuštění primární oblasti a vyžádá si nové záznamy (pokud nějaké jsou). 
   Akce je aktualizuje na účet pro integraci sekundární oblasti. 
   Tento proces pomáhá získat přírůstkový běhový stav z primární oblasti do sekundární oblasti.

Provozní kontinuita v účtu Logic Apps Integration účet poskytuje podporu založenou na protokolech B2B X12, AS2 a EDIFACT. Podrobné pokyny týkající se použití X12 a AS2 najdete v části [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) a [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) v tomto článku.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Převzetí služeb při selhání v sekundární oblasti během události havárie

Když v případě havárie není dostupná primární oblast pro provozní kontinuitu, přímý provoz do sekundární oblasti. Sekundární oblast pomáhá podnikům rychle obnovit funkce, aby splnily požadavky na RPO a RTO, na základě kterých jejich partneři souhlasí. Také minimalizuje úsilí při převzetí služeb při selhání z jedné oblasti do jiné oblasti. 

Při kopírování řídicích čísel z primární oblasti do sekundární oblasti je očekávaná latence. Abyste se vyhnuli odesílání duplicitních řídicích čísel partnerům během události havárie, doporučujeme zvýšit počet kontrolních čísel v rámci smluv o sekundárních oblastech pomocí [rutin PowerShellu](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Přejít zpět na událost po havárii primární oblasti

Pokud se chcete vrátit k primární oblasti, když je k dispozici, postupujte podle následujících kroků:

1. Přestanou přijímat zprávy od partnerů v sekundární oblasti.  

2. Zvyšte čísla vygenerovaných ovládacích prvků pro všechny smlouvy primární oblasti pomocí [rutin PowerShellu](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Přímý provoz ze sekundární oblasti do primární oblasti.

4. Ověřte, že je povolená aplikace logiky vytvořené v sekundární oblasti pro přijímání stavu spuštění z primární oblasti.

## <a name="x12"></a>X12 

Provozní kontinuita pro dokumenty EDI X12 je založena na řídicích číslech:

> [!TIP]
> K vytváření aplikací logiky můžete použít také [šablonu rychlý Start X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) . Vytváření primárních a sekundárních integračních účtů je předpokladem pro použití šablony. Šablona pomáhá vytvořit dvě aplikace logiky, jednu pro přijatá řídicí čísla a druhou pro vygenerovaná řídicí čísla. Příslušné triggery a akce se vytvoří v Logic Apps, připojí Trigger k primárnímu účtu pro integraci a akci sekundárního účtu pro integraci.

**Požadavky**

Pokud chcete pro příchozí zprávy povolit zotavení po havárii, vyberte nastavení pro příjem duplicitních dat v X12 smlouvě.

![Vyberte duplicitní nastavení kontroly.](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Vytvoření [Aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Vyhledejte **X12**a vyberte **X12 – když se změní číslo ovládacího prvku**.   

   ![Hledat X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu pro integraci. 
   Aktivační událost by měla být připojená k účtu pro integraci primární oblasti.

3. Zadejte název připojení, v seznamu vyberte svůj *účet pro integraci primární oblasti* a zvolte **vytvořit**.   

   ![Snímek obrazovky, který ukazuje, kde zadat název připojení a vybrat účet pro integraci primární oblasti ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Nastavení **synchronizace hodnoty DateTime pro začátek** je volitelné. **Frekvence** se dá nastavit na **den**, **hodinu**, **minutu**nebo **sekundu** v intervalu.   

   ![Snímek obrazovky, který zobrazuje nastavení synchronizace hodnoty DateTime ke spuštění.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Vyberte **Nový krok** > **Přidat akci**.

   ![Snímek obrazovky zobrazující tlačítko pro nový krok a možnost přidat akci](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Vyhledejte **X12**a vyberte **X12 – přidat nebo aktualizovat řídicí čísla**.   

   ![Přidat nebo aktualizovat řídicí čísla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Pokud chcete připojit akci k účtu pro integraci sekundární oblasti, vyberte **změnit připojení**  >  **Přidat nové připojení** a seznam dostupných účtů pro integraci. Zadejte název připojení, v seznamu vyberte svůj *účet pro integraci sekundární oblasti* a zvolte **vytvořit**. 

   ![Snímek obrazovky, který ukazuje, kde přidat název účtu pro integraci sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Kliknutím na ikonu v pravém horním rohu přepněte na nezpracované vstupy.

   ![Snímek obrazovky, který zobrazuje ikonu pro výběr přechodu na nezpracované.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Z dialogového okna pro výběr dynamického obsahu vyberte tělo a uložte aplikaci logiky.

   ![Pole dynamického obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   V závislosti na časovém intervalu se aktivační událost dotazuje tabulky kontrolního čísla přijaté primární oblastí a vyžádá si nové záznamy. 
   Akce aktualizuje záznamy v účtu pro integraci sekundární oblasti. 
   Pokud nejsou k dispozici žádné aktualizace, zobrazí se stav triggeru jako **přeskočeno**.   

   ![Tabulka čísel ovládacích prvků](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

V závislosti na časovém intervalu je přírůstkový běhový stav replikován z primární oblasti do sekundární oblasti. V případě události po havárii není primární oblast dostupná, přímý provoz do sekundární oblasti pro provozní kontinuitu. 

## <a name="edifact"></a>EDIFACT 

Provozní kontinuita pro EDIFACT dokumenty EDI je založena na řídicích číslech.

**Požadavky**

Pokud chcete pro příchozí zprávy povolit zotavení po havárii, vyberte nastavení pro příjem duplicitních dat v EDIFACT smlouvě.

![Snímek obrazovky, který zobrazuje duplicitní nastavení kontroly nastavení pro příjem EDIFACT smlouvy.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Vytvoření [Aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Vyhledejte **EDIFACT**a vyberte **EDIFACT – když se změní číslo ovládacího prvku**.

   ![Hledat EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu pro integraci. 
   Aktivační událost by měla být připojená k účtu pro integraci primární oblasti. 

3. Zadejte název připojení, v seznamu vyberte svůj *účet pro integraci primární oblasti* a zvolte **vytvořit**.    

   ![Název účtu pro integraci primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Nastavení **synchronizace hodnoty DateTime pro začátek** je volitelné. **Frekvence** se dá nastavit na **den**, **hodinu**, **minutu**nebo **sekundu** v intervalu.    

   ![Snímek obrazovky zobrazující nastavení hodnoty DateTime a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Vyberte **Nový krok** > **Přidat akci**.    

   ![Snímek obrazovky, který ukazuje, kde vybrat přidat akci](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Vyhledejte **EDIFACT**a vyberte **EDIFACT – přidat nebo aktualizovat řídicí čísla**.   

   ![Snímek obrazovky, který ukazuje, kde přidat nebo aktualizovat řídicí čísla](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Pokud chcete připojit akci k účtu pro integraci sekundární oblasti, vyberte **změnit připojení**  >  **Přidat nové připojení** a seznam dostupných účtů pro integraci. Zadejte název připojení, v seznamu vyberte svůj *účet pro integraci sekundární oblasti* a zvolte **vytvořit**.

   ![Vytvořte název účtu pro integraci sekundární oblasti.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Kliknutím na ikonu v pravém horním rohu přepněte na nezpracované vstupy.

   ![Snímek obrazovky, který zvýrazní ikonu, která se má vybrat, když chcete přepnout na nezpracované vstupy.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Z dialogového okna pro výběr dynamického obsahu vyberte tělo a uložte aplikaci logiky.   

   ![Snímek obrazovky zobrazující dynamický výběr obsahu, kde můžete vybrat text z.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   V závislosti na časovém intervalu se aktivační událost dotazuje tabulky kontrolního čísla přijaté primární oblastí a vyžádá si nové záznamy.
   Akce aktualizuje záznamy na účet pro integraci sekundární oblasti. 
   Pokud nejsou k dispozici žádné aktualizace, zobrazí se stav triggeru jako **přeskočeno**.

   ![Tabulka čísel ovládacích prvků](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

V závislosti na časovém intervalu je přírůstkový běhový stav replikován z primární oblasti do sekundární oblasti. V případě události po havárii není primární oblast dostupná, přímý provoz do sekundární oblasti pro provozní kontinuitu. 

## <a name="as2"></a>AS2 

Provozní kontinuita pro dokumenty, které používají protokol AS2, je založena na ID zprávy a hodnotě MIKROFONu.

> [!TIP]
> K vytváření aplikací logiky můžete použít také [šablonu rychlý Start AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) . Vytváření primárních a sekundárních integračních účtů je předpokladem pro použití šablony. Šablona pomáhá vytvořit aplikaci logiky, která má aktivační událost a akci. Aplikace logiky vytvoří připojení z triggeru k primárnímu účtu pro integraci a akci sekundárního účtu pro integraci.

1. Vytvořte [aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.  

2. Vyhledejte **AS2**a vyberte **AS2 – když se vytvoří hodnota mikrofonu**.   

   ![Hledat AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu pro integraci. 
   Aktivační událost by měla být připojená k účtu pro integraci primární oblasti. 
   
3. Zadejte název připojení, v seznamu vyberte svůj *účet pro integraci primární oblasti* a zvolte **vytvořit**.

   ![Snímek obrazovky, který ukazuje, kde zadat název připojení, když je vytvořena hodnota MIKROFONu.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Nastavení **hodnoty data a času pro spuštění synchronizace hodnot typu MIC** je volitelné. **Frekvence** se dá nastavit na **den**, **hodinu**, **minutu**nebo **sekundu** v intervalu.   

   ![DateTime a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Vyberte **Nový krok** > **Přidat akci**.  

   ![Nový krok, přidání akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Vyhledejte **AS2**a vyberte **AS2-přidat nebo aktualizovat obsah MIC**.  

   ![Přidání nebo aktualizace MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Pokud chcete připojit akci k sekundárnímu účtu pro integraci, vyberte **změnit připojení**  >  **Přidat nové připojení** a seznam dostupných účtů pro integraci. Zadejte název připojení, v seznamu vyberte svůj *účet pro integraci sekundární oblasti* a zvolte **vytvořit**.

   ![Název účtu pro integraci sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Kliknutím na ikonu v pravém horním rohu přepněte na nezpracované vstupy.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Z dialogového okna pro výběr dynamického obsahu vyberte tělo a uložte aplikaci logiky.   

   ![Dynamický obsah](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   V závislosti na časovém intervalu se aktivační událost dotazuje tabulky primární oblasti a vyžádá si nové záznamy. Akce je aktualizuje na účet pro integraci sekundární oblasti. 
   Pokud nejsou k dispozici žádné aktualizace, zobrazí se stav triggeru jako **přeskočeno**.  

   ![Tabulka primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

V závislosti na časovém intervalu je přírůstkový běhový stav replikován z primární oblasti do sekundární oblasti. V případě události po havárii není primární oblast dostupná, přímý provoz do sekundární oblasti pro provozní kontinuitu. 

## <a name="next-steps"></a>Další kroky

[Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)
