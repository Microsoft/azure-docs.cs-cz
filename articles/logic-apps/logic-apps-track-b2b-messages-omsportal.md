---
title: Sledování zpráv B2B s Azure Log Analytics - Azure Logic Apps | Microsoft Docs
description: Sledování komunikace B2B pro integraci účet a logiku aplikace s Azure Log Analytics
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 48523e1f1bc8d5b810cc7c9d1a7308f1aaadf8bb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="track-b2b-communication-with-azure-log-analytics"></a>Sledování komunikace B2B s Azure Log Analytics

Po nastavení komunikace B2B mezi dvěma systémem obchodních procesů nebo aplikací prostřednictvím účtu integrace tyto entity můžou vyměňovat zprávy mezi sebou. Zkontrolujte, zda tyto zprávy jsou zpracovány správně, můžete sledovat AS2, X12, a EDIFACT zprávy s [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Například můžete použít tyto možnosti sledování založené na webu pro sledování zpráv:

* Počet zpráv a stav
* Potvrzení stavu
* Korelovat zprávy s potvrzování
* Podrobné informace o chybě popisy chyb
* Možnosti hledání

## <a name="requirements"></a>Požadavky

* Aplikace logiky, který je nastavený s protokolování diagnostiky. Další informace [postup vytvoření aplikace logiky](quickstart-create-first-logic-app-workflow.md) a [postup nastavení protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Integrace účet, který je nastavený s sledování a protokolování. Další informace [postup vytvoření účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [postup nastavení sledování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste to ještě neudělali, [publikování diagnostických dat k analýze protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Po splnění předchozí požadavky, měli byste mít pracovního prostoru v analýzy protokolů. Měli byste použít ve stejném pracovním prostoru pro sledování vaší komunikace B2B ve analýzy protokolů. 
>  
> Pokud nemáte pracovní prostor analýzy protokolů, přečtěte si [jak vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="add-the-logic-apps-b2b-solution-to-log-analytics"></a>Přidejte logiku aplikace B2B řešení k analýze protokolů

Pokud chcete, aby analýzy protokolů sledování zpráv B2B pro svou aplikaci logiky, je nutné přidat **B2B aplikace logiky** řešení na portálu OMS. Další informace o [přidání řešení k analýze protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

1. V [portál Azure](https://portal.azure.com), zvolte **všechny služby**. Vyhledejte "analýzy protokolů" a potom vyberte **analýzy protokolů** jak je vidět tady:

   ![Najít analýzy protokolů](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. V části **analýzy protokolů**, najděte a vyberte pracovní prostor analýzy protokolů. 

   ![Vyberte pracovní prostor analýzy protokolů](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. V části **správy**, zvolte **portálu OMS**.

   ![Zvolte portálu OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Po otevření domovské stránce vyberte **řešení Galerie**.    

   ![Zvolte řešení Galerie](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. V části **všechna řešení**, najít a zvolte **B2B aplikace logiky**.     

   ![Zvolte logiku aplikace B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. V části **B2B aplikace logiky**, zvolte **přidat**.

   ![Vyberte Přidat](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   Na domovské stránce na dlaždici **zpráv B2B aplikace logiky** se teď zobrazí. 
   Tuto dlaždici aktualizuje počet zpráv při zpracování zpráv B2B.

   ![Domovská stránka, dlaždice zpráv B2B aplikace logiky](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-log-analytics"></a>Sledovat stav zprávu a podrobnosti v analýzy protokolů

1. Po zpracování zpráv B2B, můžete zobrazit stav a podrobnosti o těchto zpráv. Na domovské stránce vyberte **zpráv B2B aplikace logiky** dlaždici.

   ![Počet aktualizované zpráv](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > Ve výchozím nastavení **zpráv B2B aplikace logiky** dlaždice zobrazuje data podle jednoho dne. Chcete-li změnit rozsah dat na jiný interval, vyberte ovládacího prvku oboru v horní části stránky:
   > 
   > ![Změnit rozsah dat](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. Po zpráva zobrazení řídicího panelu stavu můžete zobrazit další podrobnosti o konkrétní zpráva typu, který zobrazuje data podle jednoho dne. Vyberte dlaždice pro **AS2**, **X12**, nebo **EDIFACT**.

   ![Stav zobrazení zpráv](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Pro vybrané dlaždice se zobrazí seznam zpráv. 
   Další informace o vlastnostech u každého typu zprávy naleznete v tématu tyto popisy vlastností zpráv:

   * [Vlastnosti zprávy AS2](#as2-message-properties)
   * [X12 zprávy vlastnosti](#x12-message-properties)
   * [Vlastnosti zprávy EDIFACT](#EDIFACT-message-properties)

   Zde je například jak může vypadat seznam zpráv AS2:

   ![Zobrazení zpráv AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Zobrazení nebo exportovat vstupy a výstupy specifické pro zprávy, vyberte tyto zprávy a zvolte **Stáhnout**. Když se zobrazí výzva, uložte soubor .zip do místního počítače a potom rozbalte tento soubor. 

   Extrahovanou složku zahrnuje složku pro každou vybranou zprávu. 
   Pokud jste nastavili potvrzení zprávy složka také obsahuje soubory s podrobnostmi o potvrzení. 
   Každou zprávu složku má minimálně tyto soubory: 
   
   * Čitelný soubory se datová část vstupního a výstupní datové části Podrobnosti
   * Kódované soubory s vstupy a výstupy

   U každého typu zprávy můžete najít složku a název formáty souborů:

   * [AS2 složku a název souboru formáty](#as2-folder-file-names)
   * [X12 složku a název souboru formáty](#x12-folder-file-names)
   * [EDIFACT složku a název souboru formáty](#edifact-folder-file-names)

   ![Stažení souborů zpráv](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Chcete-li zobrazit všechny akce, které mají stejnou ID spuštění, na **hledání protokolů** vyberte zprávu ze seznamu zpráv.

   Můžete řadit podle sloupce, nebo vyhledejte konkrétní výsledky tyto akce.

   ![Akce se stejným ID spuštění](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Hledat výsledky s předem dotazy, zvolte **Oblíbené**.

   * Další informace [jak vytvářet dotazy přidáním filtry](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Další informace o [vyhledávání dat pomocí protokolu hledání v analýzy protokolů](../log-analytics/log-analytics-log-searches.md).

   * Chcete-li změnit dotaz do vyhledávacího pole, aktualizujte dotaz sloupců a hodnot, které chcete použít jako filtry.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Popisy vlastností a formáty názvu pro AS2, X 12 a EDIFACT zprávy

U každého typu zprávy tady jsou popisy vlastností a formáty názvu pro zprávu stažené soubory.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Popisy vlastností zpráv AS2

Tady jsou popisy vlastností pro každou zprávu AS2.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partnerovi hosta zadaný v **přijímat nastavení**, nebo hostitele partnera zadaný v **odeslat nastavení** pro smlouvy AS2 |
| Příjemce | Hostitele partnera, zadaný v **přijímat nastavení**, nebo partnera hosta zadaný v **odeslat nastavení** pro smlouvy AS2 |
| Aplikace logiky | Aplikace logiky, kde se nastaví AS2 akce |
| Status | Stav zpráv AS2 <br>Úspěch = přijatých nebo odeslaných zprávu platný AS2. Žádné MDN je nastavený. <br>Úspěch = přijatých nebo odeslaných zprávu platný AS2. Nastavení a obdrží MDN nebo odesílání MDN. <br>Se nezdařilo = přijatá neplatná zpráva AS2. Žádné MDN je nastavený. <br>Čekající = přijatých nebo odeslaných zprávu platný AS2. MDN nastavení a MDN se očekává. |
| Potvrzení | Stav zpráv MDN <br>Přijatá = přijatých nebo odeslaných kladné MDN. <br>Čekající na vyřízení = čekání na příjem nebo odeslání MDN. <br>Odmítl = přijatých nebo odeslaných záporné MDN. <br>Není vyžadována = MDN není nastavený smlouvy. |
| Směr | Směr zprávy AS2 |
| ID korelace | ID, které koreluje všechny aktivační události a akce v aplikaci logiky |
| ID zprávy | ID zprávy AS2 ze záhlaví zprávy AS2 |
| Časové razítko | Čas, kdy akce AS2 zpracována zprávy |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formáty názvu AS2 pro soubory stažené zpráv

Tady jsou formáty názvu pro každou složku zpráva stažené AS2 a soubory.

| Složka nebo soubor | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Vstupní, výstupní a v případě nastavení, potvrzení soubory | **Datová část vstupního**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_input_payload.txt </p>**Výstupní datové**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_výstup\_payload.txt </p></p>**Vstupy**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_inputs.txt </p></p>**Výstupy**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 zprávy popisy vlastností

Zde je uveden popis vlastnost pro každý X12 zprávy.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partnerovi hosta zadaný v **přijímat nastavení**, nebo hostitele partnera zadaný v **odeslat nastavení** pro X12 smlouvy |
| Příjemce | Hostitele partnera, zadaný v **přijímat nastavení**, nebo partnera hosta zadaný v **odeslat nastavení** pro X12 smlouvy |
| Aplikace logiky | Aplikace logiky kde X12 jsou nastavení akce |
| Status | X12 zprávy stav <br>Úspěch = přijatých nebo odeslaných platný X12 zprávy. Je nastavený žádný funkční potvrzení. <br>Úspěch = přijatých nebo odeslaných platný X12 zprávy. Funkční potvrzení se nastavení služby a přijímají nebo odesílání funkční potvrzení. <br>Se nezdařilo = přijatých nebo odeslaných neplatný X12 zprávy. <br>Čekající = přijatých nebo odeslaných platný X12 zprávy. Funkční potvrzení nastavení a funkční potvrzení se očekává. |
| Potvrzení | Funkční stav Ack (997) <br>Přijatá = přijatých nebo odeslaných kladné funkční ack. <br>Odmítl = přijatých nebo odeslaných záporné funkční ack. <br>Čekající na vyřízení = očekává funkční potvrzení, ale nebyl přijat. <br>Čekající na vyřízení = generované funkční potvrzení, ale nelze odeslat na partnera. <br>Není vyžadována = funkčnosti ack není nastaven. |
| Směr | X12 zprávy směr |
| ID korelace | ID, které koreluje všechny aktivační události a akce v aplikaci logiky |
| Msg typu | Typ EDI X 12 zprávy |
| ICN | Číslo řízení Interchange X12 zpráv |
| TSCN | Transakce nastavit číslo ovládací prvek pro X12 zpráv |
| Časové razítko | Čas při X12 akci zpracovat zprávu |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 název formáty souborů stažené zpráv

Tady jsou formáty názvu pro každý stáhnout X12 složky a soubory zpráv.

| Složka nebo soubor | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [odesílatele] \_[příjemce]\_X12\_[číslo datového přenosu řízení]\_[globální – ovládací prvek číslo]\_[transakce set řízení number]\_[časové razítko] |
| Vstupní, výstupní a v případě nastavení, potvrzení soubory | **Datová část vstupního**: [odesílatele]\_[příjemce]\_X12\_[číslo datového přenosu řízení]\_input_payload.txt </p>**Výstupní datové**: [odesílatele]\_[příjemce]\_X12\_[číslo datového přenosu řízení]\_výstup\_payload.txt </p></p>**Vstupy**: [odesílatele]\_[příjemce]\_X12\_[číslo datového přenosu řízení]\_inputs.txt </p></p>**Výstupy**: [odesílatele]\_[příjemce]\_X12\_[číslo datového přenosu řízení]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Popisy vlastností EDIFACT zpráv

Tady jsou popisy vlastností pro každou zprávu EDIFACT.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partnerovi hosta zadaný v **přijímat nastavení**, nebo hostitele partnera zadaný v **odeslat nastavení** pro smlouvy EDIFACT |
| Příjemce | Hostitele partnera, zadaný v **přijímat nastavení**, nebo partnera hosta zadaný v **odeslat nastavení** pro smlouvy EDIFACT |
| Aplikace logiky | Aplikace logiky, kde se nastaví EDIFACT akce |
| Status | Stav zpráv EDIFACT <br>Úspěch = přijatých nebo odeslaných zprávu platný EDIFACT. Je nastavený žádný funkční potvrzení. <br>Úspěch = přijatých nebo odeslaných zprávu platný EDIFACT. Funkční potvrzení se nastavení služby a přijímají nebo odesílání funkční potvrzení. <br>Se nezdařilo = přijatých nebo odeslaných neplatná zpráva EDIFACT <br>Čekající = přijatých nebo odeslaných zprávu platný EDIFACT. Funkční potvrzení nastavení a funkční potvrzení se očekává. |
| Potvrzení | Funkční stav Ack (997) <br>Přijatá = přijatých nebo odeslaných kladné funkční ack. <br>Odmítl = přijatých nebo odeslaných záporné funkční ack. <br>Čekající na vyřízení = očekává funkční potvrzení, ale nebyl přijat. <br>Čekající na vyřízení = generované funkční potvrzení, ale nelze odeslat na partnera. <br>Není vyžadována = funkční Ack není nastaven. |
| Směr | Směr EDIFACT zprávy |
| ID korelace | ID, které koreluje všechny aktivační události a akce v aplikaci logiky |
| Msg typu | Typ zprávy EDIFACT |
| ICN | Ovládací prvek číslo Interchange EDIFACT zprávy |
| TSCN | Nastavit řízení číslo transakce pro zprávu EDIFACT |
| Časové razítko | Čas, kdy akce EDIFACT zpracována zprávy |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formáty názvu EDIFACT pro soubory stažené zpráv

Tady jsou formáty názvu pro každou složku zpráva stažené EDIFACT a soubory.

| Složka nebo soubor | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [odesílatele] \_[příjemce]\_EDIFACT\_[číslo datového přenosu řízení]\_[globální – ovládací prvek číslo]\_[transakce set řízení number]\_[časové razítko] |
| Vstupní, výstupní a v případě nastavení, potvrzení soubory | **Datová část vstupního**: [odesílatele]\_[příjemce]\_EDIFACT\_[číslo datového přenosu řízení]\_input_payload.txt </p>**Výstupní datové**: [odesílatele]\_[příjemce]\_EDIFACT\_[číslo datového přenosu řízení]\_výstup\_payload.txt </p></p>**Vstupy**: [odesílatele]\_[příjemce]\_EDIFACT\_[číslo datového přenosu řízení]\_inputs.txt </p></p>**Výstupy**: [odesílatele]\_[příjemce]\_EDIFACT\_[číslo datového přenosu řízení]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Další postup

* [Dotaz pro B2B zprávy v analýzy protokolů](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní sledování schémata](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)