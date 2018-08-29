---
title: Zotavení po havárii pro účty pro integraci B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Připravte se na zotavení po havárii mezi oblastmi v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: 3d465123f814887282bf2b29a5b6e0836601c243
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123900"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Zotavení po havárii mezi oblastmi pro účty pro integraci B2B v Azure Logic Apps

Pracovní postupy B2B zahrnují peněžní transakce jako objednávky a faktury. Během události po havárii, je velmi důležité pro firmu k rychlé obnovení dodržet podmínky smluv SLA úrovni organizační dohodnutých s jejich partneři. Tento článek ukazuje, jak sestavit plán obchodní kontinuity podnikových procesů pro úlohy s B2B. 

* Připravenost pro zotavení po havárii 
* Převzetí služeb při selhání do sekundární oblasti během události po havárii 
* Vrátit zpět do primární oblasti po havárii

## <a name="disaster-recovery-readiness"></a>Připravenost pro zotavení po havárii  

1. Identifikujte sekundární oblasti a vytvoření [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) v sekundární oblasti.

2. Přidání partnerů, schémat a smlouvy pro požadovaná zpráva toky, kdy stav spuštění musí replikovat do sekundární oblasti účtu integrace.

   > [!TIP]
   > Ujistěte se, že je konzistence napříč oblastmi v zásady vytváření názvů artefaktu účtu integrace. 

3. Stav spuštění načítat z primární oblasti, vytvoření aplikace logiky v sekundární oblasti. 

   Tato aplikace logiky by měl mít *aktivační událost* a *akce*. 
   Aktivační událost se má připojit k účtu pro integraci primární oblasti a akce se má připojit k účtu pro integraci sekundární oblasti. 
   Podle časového intervalu, trigger dotazuje spustit tabulka stav primární oblasti a získává nové záznamy, pokud existuje. Tato akce aktualizuje je sekundární oblasti účtu pro integraci. 
   To pomáhá získat stav přírůstkové runtime z primární oblasti do sekundární oblasti.

4. Kontinuita podnikových procesů v účtu integrace Logic Apps je navržena pro podporu založené na protokolech B2B - X12, AS2 a EDIFACT. Podrobný postup najdete vyberte příslušné odkazy.

5. Doporučení je příliš nasadit všechny prostředky primární oblasti do sekundární oblasti. 

   Primární oblast prostředky zahrnují Azure SQL Database nebo Azure Cosmos DB, Azure Service Bus a Azure Event Hubs použít pro zasílání zpráv Azure API Management a funkce Azure Logic Apps ve službě Azure App Service.   

6. Připojení z primární oblasti do sekundární oblasti. Stav spuštění načítat z primární oblasti, vytvoření aplikace logiky v sekundární oblasti. 

   Aplikace logiky by měla mít aktivační události a akce. 
   Aktivační událost se má připojit k účtu pro integraci primární oblasti. 
   Akce se má připojit k účtu pro integraci sekundární oblasti. 
   Podle časového intervalu, trigger dotazuje spustit tabulka stav primární oblasti a získává nové záznamy, pokud existuje. 
   Tato akce aktualizuje je do sekundární oblasti účtu integrace. 
   Tento proces pomáhá získat stav přírůstkové runtime z primární oblasti do sekundární oblasti.

Kontinuita podnikových procesů v účtu integrace Logic Apps poskytuje podporu na základě protokolů B2B X12, AS2 a EDIFACT. Podrobné pokyny k používání X12 a AS2 najdete v tématu [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) a [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) v tomto článku.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Převzetí služeb při selhání do sekundární oblasti během události po havárii

Během události po havárii, pokud primární oblast není k dispozici zajišťuje nepřetržitý chod podniků, směrovat přenos dat do sekundární oblasti. Pomáhá sekundární oblasti a obchodní k obnovení funkce rychle dosáhnout RPO/RTO dohodnutých jejich partnery. Také minimalizuje úsilí při převzetí služeb při selhání z jedné oblasti do jiné oblasti. 

Neexistuje očekávaná latence při kopírování kontrolních čísel od primární oblasti do sekundární oblasti. Vyhněte se odesílání duplicitní vygenerovanému ovládacímu prvku čísla partnerům během události po havárii, doporučujeme zvýšit kontrolních čísel v sekundární oblasti smluv s použitím [rutin prostředí PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Vrátit zpět na primární oblast událost po havárii

Chcete-li vrátit do primární oblasti až bude k dispozici, postupujte takto:

1. Zastavte příjem zpráv od partnerů v sekundární oblasti.  

2. Zvýšit číslo vygenerovaný ovládací prvek pro všechny primární oblasti smlouvy s použitím [rutin prostředí PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Směrovat přenos dat ze sekundární oblasti do primární oblasti.

4. Zkontrolujte, zda je povoleno vytvořit v sekundární oblasti pro přijímání změn z primární oblasti stav spuštění aplikace logiky.

## <a name="x12"></a>X12 

Kontinuita podnikových procesů pro EDI X 12 dokumentů jsou založené na kontrolních čísel:

> [!TIP]
> Můžete také použít [X12 rychlý start šablony](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) k vytváření aplikací logiky. Vytváření účtů integrace primární a sekundární jsou požadavky na použití šablony. Šablona pomůže vytvořit dvě logic apps, jeden pro přijaté kontrolních čísel a druhý k vygenerovanému ovládacímu prvku čísla. Příslušných triggerů a akcí se vytvoří ve službě logic apps, aktivační událost při připojování k primární integrační účet a akce, která má sekundární integrační účet.

**Požadavky**

Pokud chcete povolit zotavení po havárii pro příchozí zprávy, vyberte v X12 duplicitní zkontrolujte nastavení přijímat nastavení smlouvy.

![Vyberte nastavení duplicitní kontroly](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Vytvoření [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Hledat na **X12**a vyberte **X12 – když se upraví nějaké kontrolní číslo**.   

   ![Hledat X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu pro integraci. 
   Aktivační událost musí být připojené k účtu pro integraci primární oblasti.

3. Zadejte název připojení, vyberte vaše *účtu pro integraci primární oblasti* ze seznamu a zvolte **vytvořit**.   

   ![Název integračního účtu primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **Datum a čas spuštění synchronizace kontrolního čísla** nastavení je volitelné. **Frekvence** může být nastaven na **den**, **hodinu**, **minutu**, nebo **druhý** s intervalem.   

   ![Datum a čas a četnost](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Vyberte **nový krok** > **přidat akci**.

   ![Nový krok, přidat akci](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Hledat na **X12**a vyberte **X12-přidat nebo aktualizovat kontrolní čísla**.   

   ![Přidat nebo aktualizovat kontrolní čísla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Pro připojení k účtu pro integraci sekundární oblasti akci, vyberte **změnit připojení** > **přidat nové připojení** seznam dostupných integračních účtů. Zadejte název připojení, vyberte vaše *účtu pro integraci sekundární oblasti* ze seznamu a zvolte **vytvořit**. 

   ![Název integračního účtu sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Nezpracované vstupy přepnout kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Výběr textu z dynamického obsahu pro výběr a uložte aplikaci logiky.

   ![Pole dynamického obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Aktivační událost podle časového intervalu, dotazuje primární oblast přijatá ovládací prvek čísla v tabulce a získává nové záznamy. 
   Tato akce aktualizuje záznamy v sekundární oblasti účtu integrace. 
   Pokud nejsou žádné aktualizace, stav aktivační události se zobrazí jako **vynecháno**.   

   ![Ovládací prvek tabulka čísel](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Podle časového intervalu, stav přírůstkové runtime replikuje z primární oblasti do sekundární oblasti. Během události po havárii, pokud primární oblast není k dispozici, s přímým přístupem provoz do sekundární oblasti, která zajišťuje nepřetržitý chod podniků. 

## <a name="edifact"></a>EDIFACT 

Kontinuita podnikových dokumentů EDI EDIFACT je založená na kontrolních čísel.

**Požadavky**

Pokud chcete povolit zotavení po havárii pro příchozí zprávy, vyberte v nastavení přijímat smlouvy EDIFACT duplicitní zkontrolujte nastavení.

![Vyberte nastavení duplicitní kontroly](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Vytvoření [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Hledat na **EDIFACT**a vyberte **EDIFACT – když se upraví nějaké kontrolní číslo**.

   ![Hledat EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu pro integraci. 
   Aktivační událost musí být připojené k účtu pro integraci primární oblasti. 

3. Zadejte název připojení, vyberte vaše *účtu pro integraci primární oblasti* ze seznamu a zvolte **vytvořit**.    

   ![Název integračního účtu primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **Datum a čas spuštění synchronizace kontrolního čísla** nastavení je volitelné. **Frekvence** může být nastaven na **den**, **hodinu**, **minutu**, nebo **druhý** s intervalem.    

   ![Datum a čas a četnost](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Vyberte **nový krok** > **přidat akci**.    

   ![Nový krok, přidat akci](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Hledat na **EDIFACT**a vyberte **EDIFACT - přidat nebo aktualizovat kontrolní čísla**.   

   ![Přidat nebo aktualizovat kontrolní čísla](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Pro připojení k účtu pro integraci sekundární oblasti akci, vyberte **změnit připojení** > **přidat nové připojení** seznam dostupných integračních účtů. Zadejte název připojení, vyberte vaše *účtu pro integraci sekundární oblasti* ze seznamu a zvolte **vytvořit**.

   ![Název integračního účtu sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Nezpracované vstupy přepnout kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Výběr textu z dynamického obsahu pro výběr a uložte aplikaci logiky.   

   ![Pole dynamického obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Aktivační událost podle časového intervalu, dotazuje primární oblast přijatá ovládací prvek čísla v tabulce a získává nové záznamy.
   Tato akce aktualizuje záznamy do sekundární oblasti účtu pro integraci. 
   Pokud nejsou žádné aktualizace, stav aktivační události se zobrazí jako **vynecháno**.

   ![Ovládací prvek tabulka čísel](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Podle časového intervalu, stav přírůstkové runtime replikuje z primární oblasti do sekundární oblasti. Během události po havárii, pokud primární oblast není k dispozici, s přímým přístupem provoz do sekundární oblasti, která zajišťuje nepřetržitý chod podniků. 

## <a name="as2"></a>AS2 

Kontinuita podnikových procesů pro dokumenty, které používají protokol AS2 podle ID zprávy a hodnoty MIC.

> [!TIP]
> Můžete také použít [šablony rychlý start AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) k vytváření aplikací logiky. Vytváření účtů integrace primární a sekundární jsou požadavky na použití šablony. Šablony vám pomůže vytvořit aplikaci logiky, který má aktivační událost a akce. Aplikace logiky vytvoří připojení z aktivační události na primární integrační účet a akce pro sekundární integrační účet.

1. Vytvoření [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.  

2. Hledat na **AS2**a vyberte **AS2 - hodnoty MIC když se vytvoří**.   

   ![Hledat AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Aktivační událost vás vyzve k navázání připojení k účtu pro integraci. 
   Aktivační událost musí být připojené k účtu pro integraci primární oblasti. 
   
3. Zadejte název připojení, vyberte vaše *účtu pro integraci primární oblasti* ze seznamu a zvolte **vytvořit**.

   ![Název integračního účtu primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **Datum a čas spuštění synchronizace hodnoty MIC** nastavení je volitelné. **Frekvence** může být nastaven na **den**, **hodinu**, **minutu**, nebo **druhý** s intervalem.   

   ![Datum a čas a četnost](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Vyberte **nový krok** > **přidat akci**.  

   ![Nový krok, přidat akci](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Hledat na **AS2**a vyberte **AS2 - přidat nebo aktualizovat obsah MIC**.  

   ![Povinná kontrola úrovně Důvěryhodnosti přidání nebo aktualizace](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Chcete-li připojit k sekundární integrační účet akce, vyberte **změnit připojení** > **přidat nové připojení** seznam dostupných integračních účtů. Zadejte název připojení, vyberte vaše *účtu pro integraci sekundární oblasti* ze seznamu a zvolte **vytvořit**.

   ![Název integračního účtu sekundární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Nezpracované vstupy přepnout kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracované vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Výběr textu z dynamického obsahu pro výběr a uložte aplikaci logiky.   

   ![Dynamický obsah](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Aktivační událost podle časového intervalu, dotazuje tabulku primární oblasti a získává nové záznamy. Tato akce aktualizuje je do sekundární oblasti účtu pro integraci. 
   Pokud nejsou žádné aktualizace, stav aktivační události se zobrazí jako **vynecháno**.  

   ![Tabulka primární oblasti](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Podle časového intervalu, stav přírůstkové runtime replikuje z primární oblasti do sekundární oblasti. Během události po havárii, pokud primární oblast není k dispozici, s přímým přístupem provoz do sekundární oblasti, která zajišťuje nepřetržitý chod podniků. 

## <a name="next-steps"></a>Další postup

[Monitorování zpráv B2B](logic-apps-monitor-b2b-message.md)

