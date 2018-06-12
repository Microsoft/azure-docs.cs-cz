---
title: Zotavení po havárii pro účet Integrace B2B – Azure Logic Apps | Microsoft Docs
description: Zotavení po havárii B2B aplikace logiky
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 65c7262916219a74dcd6bdab487306b5bd5f709f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299092"
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Zotavení po havárii mezi oblastmi B2B aplikace logiky

Úlohy B2B zahrnovat peníze transakce jako objednávky a faktury. Během události po havárii, je velmi důležitá pro podnik k rychle obnovit do splnění smluv SLA firemní úrovni dohodnutých s jejich partnery. Tento článek ukazuje, jak sestavit plán kontinuity obchodních pro úlohy B2B. 

* Připravenost obnovení po havárii 
* Převzetí služeb při selhání pro sekundární oblast během události po havárii 
* Vrátit zpět na primární oblasti po události po havárii

## <a name="disaster-recovery-readiness"></a>Připravenost obnovení po havárii  

1. Určete sekundární oblasti a vytvořte [integrace účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) v sekundární oblasti.

2. Přidání partnery, schémat a smluv pro požadované zpráva toky, kde stav spuštění musí replikovat na sekundární oblasti integrace účet.

   > [!TIP]
   > Ujistěte se, že konzistence v účtu artefaktů integrace zásady vytváření názvů v oblastech. 

3. Stav spuštění načítat z primární oblasti, vytvoření aplikace logiky v sekundární oblasti. 

   Musí mít tuto aplikaci logiky *aktivační událost* a *akce*. 
   Aktivační události by se měly připojit primární oblasti integrace účtu a akci by se měly připojit sekundární oblasti integrace účtu. 
   Podle toho, časový interval, aktivační událost dotazování primární oblasti spustit stav tabulce a vrátí nové záznamy, pokud existuje. Akce aktualizace je sekundární oblasti integrace účtu. 
   To pomůže získat přírůstkové běhový stav od primární oblasti sekundární oblasti.

4. Kontinuita podnikových procesů v účtu integrace Logic Apps je navržen pro podporu založené na protokolech B2B - X12 AS2, EDIFACT a. Podrobné kroky, vyberete na příslušné odkazy.

5. Doporučuje se pro všechny prostředky primární oblasti v sekundární oblasti příliš nasazení. 

   Primární oblasti prostředky zahrnují Azure SQL Database nebo Azure Cosmos DB, Azure Service Bus a Azure Event Hubs použít pro zasílání zpráv Azure API Management a funkce Azure Logic Apps v Azure App Service.   

6. Navázání spojení mezi primární oblasti v sekundární oblasti. Stav spuštění načítat z primární oblasti, vytvoření aplikace logiky v sekundární oblasti. 

   Aplikace logiky měli aktivační události a akce. 
   Aktivační události by měl připojení k účtu integrace primární oblasti. 
   Akce by měl připojení k účtu integrace sekundární oblast. 
   Podle toho, časový interval, aktivační událost dotazování primární oblasti spustit stav tabulce a vrátí nové záznamy, pokud existuje. 
   Akce aktualizace je účet integrace sekundární oblast. 
   Tento proces pomáhá získání přírůstkové běhového stavu od primární oblasti sekundární oblast.

Kontinuita podnikových procesů v účtu integrace Logic Apps poskytuje podporu na základě protokolů B2B X12, AS2, EDIFACT a. Podrobné pokyny týkající se použití X12 a AS2 najdete v tématu [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) a [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) v tomto článku.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Převzetí služeb při selhání pro sekundární oblast během události po havárii

Během události po havárii, když primární oblasti není k dispozici pro kontinuitu podnikových procesů, přímé přenosy sekundární oblast. Pomáhá sekundární oblasti a obchodní obnovení funkce rychle tak, aby splňoval RPO/RTO dohodnutých jejich partnery. Minimalizuje se také ve snaze o převzetí služeb při selhání z jedné oblasti jiné oblasti. 

Při kopírování řízení čísla od primární oblasti v sekundární oblasti očekávané latence neexistuje. Nechcete-li odesílání duplicitní generovaného řízení čísla partnerům během události po havárii, doporučuje se má zvýšit číslo ovládacího prvku v sekundární oblasti smlouvy pomocí [rutiny prostředí PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Vrátit zpět na primární oblasti událost po havárii

K návratu do primární oblasti případě, že je k dispozici, postupujte takto:

1. Zastavte příjem zpráv od partnerů v sekundární oblasti.  

2. Zvýšit číslo generované ovládací prvek pro všechny smlouvy primární oblasti s použitím [rutiny prostředí PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Přímé přenosy z sekundární oblast na primární oblasti.

4. Zkontrolujte, zda je povolena v sekundární oblasti pro stahování, spusťte z primární oblasti stav vytvořit aplikaci logiky.

## <a name="x12"></a>X12 

Kontinuita podnikových procesů pro EDI X 12 dokumentů je založena na ovládací prvek čísla:

> [!TIP]
> Můžete také [X12 rychlý start šablony](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) k vytvoření aplikace logiky. Vytváření primární a sekundární integrace účty jsou nezbytné požadavky pro použití šablony. Šablona pomůže vytvořit dvě logiku aplikace, jednu pro čísla přijaté řízení a druhou pro generovaný řízení čísla. Příslušné triggery a akce se vytvoří v aplikace logiky, připojení k účtu primární integrace a akce účet sekundární integrace aktivační událost.

**Požadavky**

Chcete-li povolit zotavení po havárii pro příchozí zprávy, vyberte duplicitní zkontrolujte nastavení v X12 nastavení přijmout smlouvy.

![Vyberte nastavení duplicitní kontroly](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Vytvoření [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Hledání **X12**a vyberte **X12-při změně číslo řízení**.   

   ![Vyhledejte X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Aktivační událost zobrazí výzvu k navázání připojení k účtu integrace. 
   Aktivační události by měly být připojené k primární oblasti integrace účtu.

3. Zadejte název připojení, vyberte vaše *primární oblasti integrace účet* ze seznamu a vyberte **vytvořit**.   

   ![Název účtu primární oblasti integrace](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **Datum a čas k řízení číslo synchronizaci spustit** nastavení je volitelné. **Frekvence** může být nastaven na **den**, **hodinu**, **minutu**, nebo **druhý** se v intervalu.   

   ![Data a času a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Vyberte **nový krok** > **přidat akci**.

   ![Nový krok, přidat na akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Hledání **X12**a vyberte **X12-přidáte nebo aktualizujete řízení čísla**.   

   ![Přidat nebo aktualizovat kontrolní čísla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Chcete-li akce připojení k účtu integrace sekundární oblasti, vyberte **změnit připojení** > **přidat nové připojení** seznam účtů k dispozici integrace. Zadejte název připojení, vyberte vaše *sekundární oblasti integrace účet* ze seznamu a vyberte **vytvořit**. 

   ![Název účtu služby sekundární oblast integrace](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Přepněte na nezpracovaná vstupy kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracovaná vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Vybrat text z dynamického obsahu výběr a uložte aplikaci logiky.

   ![Dynamická pole obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Založený na časový interval, aktivační události dotazuje primární oblasti přijatých řízení číslo v tabulce a vrátí nové záznamy. 
   Akce aktualizace záznamů v sekundární oblasti integrace účtu. 
   Pokud nejsou dostupné žádné aktualizace, stav aktivační události zobrazuje jako **vynecháno**.   

   ![Číslo tabulky ovládací prvek](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Podle toho, časový interval, přírůstkové běhový stav replikuje z primární oblasti v sekundární oblasti. Během události po havárii, když primární oblasti není k dispozici, přímé přenosy sekundární oblast pro kontinuitu podnikových procesů. 

## <a name="edifact"></a>EDIFACT 

Kontinuita podnikových procesů pro dokumenty EDI EDIFACT je založena na ovládací prvek čísla.

**Požadavky**

Pokud chcete povolit obnovení po havárii pro příchozí zprávy, vyberte v nastavení přijímat smlouvy EDIFACT duplicitní zkontrolujte nastavení.

![Vyberte nastavení duplicitní kontroly](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Vytvoření [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.    

2. Hledání **EDIFACT**a vyberte **EDIFACT - při změně číslo řízení**.

   ![Vyhledejte EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Aktivační událost zobrazí výzvu k navázání připojení k účtu integrace. 
   Aktivační události by měly být připojené k primární oblasti integrace účtu. 

3. Zadejte název připojení, vyberte vaše *primární oblasti integrace účet* ze seznamu a vyberte **vytvořit**.    

   ![Název účtu primární oblasti integrace](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **Datum a čas k řízení číslo synchronizaci spustit** nastavení je volitelné. **Frekvence** může být nastaven na **den**, **hodinu**, **minutu**, nebo **druhý** se v intervalu.    

   ![Data a času a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Vyberte **nový krok** > **přidat akci**.    

   ![Nový krok, přidat na akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Hledání **EDIFACT**a vyberte **EDIFACT - přidáte nebo aktualizujete řízení čísla**.   

   ![Přidat nebo aktualizovat kontrolní čísla](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Chcete-li akce připojení k účtu integrace sekundární oblasti, vyberte **změnit připojení** > **přidat nové připojení** seznam účtů k dispozici integrace. Zadejte název připojení, vyberte vaše *sekundární oblasti integrace účet* ze seznamu a vyberte **vytvořit**.

   ![Název účtu služby sekundární oblast integrace](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Přepněte na nezpracovaná vstupy kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracovaná vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Vybrat text z dynamického obsahu výběr a uložte aplikaci logiky.   

   ![Dynamická pole obsahu](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Založený na časový interval, aktivační události dotazuje primární oblasti přijatých řízení číslo v tabulce a vrátí nové záznamy.
   Akce aktualizace záznamy k účtu integrace sekundární oblast. 
   Pokud nejsou dostupné žádné aktualizace, stav aktivační události zobrazuje jako **vynecháno**.

   ![Číslo tabulky ovládací prvek](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Podle toho, časový interval, přírůstkové běhový stav replikuje z primární oblasti v sekundární oblasti. Během události po havárii, když primární oblasti není k dispozici, přímé přenosy sekundární oblast pro kontinuitu podnikových procesů. 

## <a name="as2"></a>AS2 

Kontinuita podnikových procesů pro dokumenty, které používají protokol AS2 podle ID zprávy a hodnota povinná kontrola úrovně Důvěryhodnosti.

> [!TIP]
> Můžete také [šablony rychlý start AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) k vytvoření aplikace logiky. Vytváření primární a sekundární integrace účty jsou nezbytné požadavky pro použití šablony. Šablona pomůže vytvořit aplikaci logiky, který má aktivační události a akce. Aplikace logiky vytvoří připojení z aktivační událost primární integrace účtu a akci, která má účet sekundární integrace.

1. Vytvoření [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) v sekundární oblasti.  

2. Hledání **AS2**a vyberte **AS2 - hodnota při povinná kontrola úrovně Důvěryhodnosti je vytvořena**.   

   ![Vyhledejte AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Aktivační událost zobrazí výzvu k navázání připojení k účtu integrace. 
   Aktivační události by měly být připojené k primární oblasti integrace účtu. 
   
3. Zadejte název připojení, vyberte vaše *primární oblasti integrace účet* ze seznamu a vyberte **vytvořit**.

   ![Název účtu primární oblasti integrace](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **Datum a čas k spuštění synchronizace hodnota povinná kontrola úrovně Důvěryhodnosti** nastavení je volitelné. **Frekvence** může být nastaven na **den**, **hodinu**, **minutu**, nebo **druhý** se v intervalu.   

   ![Data a času a frekvence](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Vyberte **nový krok** > **přidat akci**.  

   ![Nový krok, přidat na akce](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Hledání **AS2**a vyberte **AS2 - přidáte nebo aktualizujete obsah povinná kontrola úrovně Důvěryhodnosti**.  

   ![Povinná kontrola úrovně Důvěryhodnosti přidání nebo aktualizace](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Pokud chcete připojit k sekundární integrace účet akce, vyberte **změnit připojení** > **přidat nové připojení** seznam účtů k dispozici integrace. Zadejte název připojení, vyberte vaše *sekundární oblasti integrace účet* ze seznamu a vyberte **vytvořit**.

   ![Název účtu služby sekundární oblast integrace](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Přepněte na nezpracovaná vstupy kliknutím na ikonu v pravém horním rohu.

   ![Přepnout na nezpracovaná vstupy](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Vybrat text z dynamického obsahu výběr a uložte aplikaci logiky.   

   ![Dynamický obsah](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Založený na časový interval, aktivační události dotazuje primární oblasti tabulku a vrátí nové záznamy. Akce aktualizace je účet integrace sekundární oblast. 
   Pokud nejsou dostupné žádné aktualizace, stav aktivační události zobrazuje jako **vynecháno**.  

   ![Primární oblasti tabulku](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Podle toho, časový interval, přírůstkové běhový stav replikuje z primární oblasti sekundární oblast. Během události po havárii, když primární oblasti není k dispozici, přímé přenosy sekundární oblast pro kontinuitu podnikových procesů. 

## <a name="next-steps"></a>Další postup

[Monitorování zpráv B2B](logic-apps-monitor-b2b-message.md)

