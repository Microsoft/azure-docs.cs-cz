---
title: Sledování zpráv B2B pomocí Log Analytics – Azure Logic Apps | Dokumentace Microsoftu
description: Sledování B2B komunikace pro účty pro integraci a Azure Logic Apps s využitím Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: aacdaf4ec55b1223ce993a7246ed9f0405a5054e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957177"
---
# <a name="track-b2b-messages-with-azure-log-analytics"></a>Sledování zpráv B2B s Azure Log Analytics

Po nastavení komunikace B2B mezi obchodními partnery v účtu integrace těchto partnerů si mohou vyměňovat zprávy s protokoly, například AS2, X 12 a EDIFACT. Pokud chcete zkontrolovat, že tyto zprávy jsou zpracovány správně, můžete sledovat tyto zprávy s [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Například můžete použít tyto možnosti sledování založeného na webu pro sledování zpráv:

* Počet zpráv a stav
* Stav potvrzení
* Je možné korelovat s potvrzení zprávy
* Podrobné informace o chybě popisy chyb
* Možnosti vyhledávání

> [!NOTE]
> Tato stránka výše popsaný postup, jak k provádění těchto úkolů se Microsoft Operations Management Suite (OMS), což je [vyřazení z provozu v lednu 2019](../log-analytics/log-analytics-oms-portal-transition.md), nahradí tyto kroky místo toho pomocí služby Azure Log Analytics. 

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Přečtěte si [jak vytvořit aplikaci logiky](quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Účet integrace, který je nastaven díky monitorování a protokolování. Přečtěte si [tom, jak vytvořit integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [jak nastavit monitorování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste tak dosud neučinili, [publikovat diagnostická data do Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Po předchozí požadavky splňujete, budete potřebovat pracovní prostor Log Analytics, který používáte pro sledování B2B komunikace prostřednictvím Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Nainstalujte řešení Logic Apps B2B

Předtím, než máte Log Analytics sledování zpráv B2B pro vaši aplikaci logiky, přidejte **Logic Apps B2B** řešení do služby Log Analytics. Další informace o [přidání řešení do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole vyhledejte "log analytics" a vyberte **Log Analytics**.

   ![Vyberte Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. V části **Log Analytics**vyhledejte a vyberte pracovní prostor Log Analytics. 

   ![Vyberte pracovní prostor Log Analytics](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. V části **Začínáme se službou Log Analytics** > **konfigurovat řešení monitorování**, zvolte **zobrazení řešení**.

   ![Zvolte "Zobrazit řešení"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na stránce s přehledem, zvolte **přidat**, které se otevře **řešení pro správu** seznamu. V tomto seznamu, vyberte **Logic Apps B2B**. 

   ![Vyberte řešení Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Pokud nemůžete najít řešení, v dolní části seznamu, zvolte **načíst další** až se zobrazí řešení.

1. Zvolte **vytvořit**, zkontrolujte pracovní prostor Log Analytics, ve které chcete řešení nainstalovat a klikněte na tlačítko **vytvořit** znovu.   

   ![Zvolte možnost "Vytvořit" pro Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Pokud už nechcete používat existujícího pracovního prostoru, můžete také vytvořit nový pracovní prostor v tuto chvíli.

1. Až budete hotovi, vraťte se do pracovního prostoru **přehled** stránky. 

   Řešení Logic Apps B2B se teď zobrazí na stránce Přehled. 
   Při zpracování zpráv B2B, počet zpráv na této stránce se aktualizuje.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Zobrazit informace o zpráv B2B

Po zpracování zpráv B2B, můžete zobrazit stav a podrobnosti o těchto zpráv na **Logic Apps B2B** dlaždici.

1. Přejděte do pracovního prostoru analýzy logiky a otevřete stránku přehledu. Zvolte **Logic Apps B2B**.

   ![Počet aktualizovaných zpráv](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Ve výchozím nastavení **Logic Apps B2B** dlaždice zobrazuje data na základě jednoho dne. Chcete-li změnit rozsah dat na jiný interval, zvolte ovládací prvek oboru v horní části stránky:
   > 
   > ![Interval změny](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Po zprávu stav se zobrazí řídicí panel, můžete zobrazit další podrobnosti pro konkrétní zprávu typ, který zobrazuje data na základě jednoho dne. Zvolte dlaždici **AS2**, **X12**, nebo **EDIFACT**.

   ![Zobrazení stavu zprávy](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Seznam zpráv, které se zobrazí pro vybrané dlaždice. 
   Další informace o vlastnostech pro každý typ zprávy, najdete v těchto popisů vlastnosti zprávy:

   * [Vlastnosti zprávy AS2](#as2-message-properties)
   * [Vlastnosti zprávy X12](#x12-message-properties)
   * [Vlastnosti zprávy EDIFACT](#EDIFACT-message-properties)

   Tady je například o seznam zpráv AS2 může vypadat:

   ![Zobrazení zpráv AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. K zobrazení nebo exportovat vstupů a výstupů pro konkrétní zprávy, vyberte tyto zprávy a zvolte **Stáhnout**. Po zobrazení výzvy, uložte soubor .zip na místní počítač a potom rozbalte tento soubor. 

   Extrahovanou složku obsahuje složku pro každou vybranou zprávu. 
   Pokud jste nastavili potvrzení zprávy složka také obsahuje soubory s podrobnostmi potvrzení. 
   Všechny složky zpráva má minimálně tyto soubory: 
   
   * Čitelný soubory s datová část vstupního a výstupní datovou část Podrobnosti
   * Kódované soubory se vstupy a výstupy

   U každého typu zprávy můžete najít složku a název formáty souborů:

   * [Formáty názvů složku a soubor AS2](#as2-folder-file-names)
   * [X12 složku a název souboru formáty](#x12-folder-file-names)
   * [Formáty název složky a souboru EDIFACT](#edifact-folder-file-names)

   ![Stáhnout soubory zpráv.](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Chcete-li zobrazit všechny akce, které mají stejné ID spuštění, dále **prohledávání protokolů** zvolte zprávu ze seznamu zpráv.

   Tyto akce podle sloupce, nebo vyhledejte konkrétní výsledky můžete řadit.

   ![Akce se stejným ID spuštění](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Chcete-li hledat výsledky s využitím předem připravených dotazy, zvolte **Oblíbené**.

   * Přečtěte si [sestavování dotazů přidáním filtrů](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Nebo si přečtěte Další informace o [vyhledání dat pomocí prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-searches.md).

   * Chcete-li změnit dotaz do vyhledávacího pole, aktualizujte dotaz sloupců a hodnot, které chcete použít jako filtry.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Popisy vlastností a formáty názvů pro AS2, X 12 a EDIFACT zprávy

Pro každý typ zprávy tady jsou popisy vlastností a formáty názvů souborů stažené zpráv.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Popisy vlastností zprávy AS2

Tady jsou popisy vlastností pro každou zprávu AS2.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partner s identitou hosta podle **přijímat nastavení**, nebo se musí zadat partner s identitou hostitele v **odeslat nastavení** pro smlouvy AS2 |
| Příjemce | Partner hostitelů zadaná v **přijímat nastavení**, nebo se musí zadat partner s identitou hosta v **odeslat nastavení** pro smlouvy AS2 |
| Aplikace logiky | Aplikace logiky, ve kterém jsou nastavené akce AS2 |
| Status | Stav zprávy AS2 <br>Úspěch = přijatá nebo odeslaná platná zpráva AS2. Je nastavený žádný zprávy MDN. <br>Úspěch = přijatá nebo odeslaná platná zpráva AS2. Nastavení a obdrží zprávu MDN nebo se odesílají zprávy MDN. <br>Nepovedlo = přijatá neplatná zpráva AS2. Je nastavený žádný zprávy MDN. <br>Čekající = přijatá nebo odeslaná platná zpráva AS2. Je nastavený zprávy MDN a měl by zprávy MDN. |
| Potvrzení | Stav zprávy MDN <br>Přijata = přijatých nebo odeslaných kladné zprávy MDN. <br>Čekající = přijímat nebo odesílat zprávy MDN. čekání. <br>Zamítnuto = přijatých nebo odeslaných negativní zprávy MDN. <br>Není nutné = MDN není nastavený ve smlouvě. |
| Směr | Směr zprávy AS2 |
| ID korelace | ID, které souvisí všechny aktivační události a akce v aplikaci logiky |
| ID zprávy | ID zprávy AS2 ze záhlaví zpráv AS2 |
| Timestamp | Čas, kdy akce AS2 zpracována zprávy |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formáty názvů soubory stažené zpráv AS2

Tady jsou formáty názvů pro každý stažený složce zpráv AS2 a soubory.

| Soubor nebo složku | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Vstup, výstup a pokud nastavení potvrzení souborů | **Datová část vstupního**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_input_payload.txt </p>**Výstupní datovou část**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_výstup\_payload.txt </p></p>**Vstupy**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_inputs.txt </p></p>**Výstupy**: [odesílatele]\_[příjemce]\_AS2\_[ID korelace]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Popisy vlastností zprávy X12

Tady jsou popisy vlastností pro každou X12 zprávy.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partner s identitou hosta podle **přijímat nastavení**, nebo se musí zadat partner s identitou hostitele v **odeslat nastavení** pro x X12 smlouvy |
| Příjemce | Partner hostitelů zadaná v **přijímat nastavení**, nebo se musí zadat partner s identitou hosta v **odeslat nastavení** pro x X12 smlouvy |
| Aplikace logiky | Aplikace logiky kde X12 akce jsou nastavené. |
| Status | X12 zpráv stavu <br>Úspěch = přijatých nebo odeslaných platný X12 zprávy. Je nastavený žádný funkční potvrzení. <br>Úspěch = přijatých nebo odeslaných platný X12 zprávy. Nastavení a obdrží funkční potvrzení nebo funkční potvrzení posílá. <br>Nepovedlo = přijatých nebo odeslaných x neplatný X12 zprávy. <br>Čekající = přijatých nebo odeslaných platný X12 zprávy. Funkční potvrzení je nastavena a funkční potvrzení se očekává. |
| Potvrzení | Funkční stav Ack (997) <br>Přijata = přijatých nebo odeslaných kladné funkční potvrzení <br>Zamítnuto = přijatých nebo odeslaných negativní funkční potvrzení <br>Čekající = očekává funkční potvrzení, ale nebyl přijat. <br>Čekající = generované funkční potvrzení, ale nelze odeslat na partnera. <br>Není nutné = funkční potvrzení není nastavený. |
| Směr | X12 zprávy směr |
| ID korelace | ID, které souvisí všechny aktivační události a akce v aplikaci logiky |
| Typ zprávy | Typ zprávy 12 EDI X |
| ICN | Kontrolní číslo výměny. pro X12 zprávy |
| TSCN | Transakce nastavit kontrolní číslo pro X12 zprávy |
| Timestamp | Čas při X12 akci zpracovat zprávu |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 pojmenujte formáty pro soubory stažené zpráv.

Tady jsou formáty názvů pro každý stáhli X12 složky a soubory zpráv.

| Soubor nebo složku | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [odesílatele] \_[příjemce]\_X12\_[výměny kontrolní číslo]\_[global-ovládacího prvku number]\_[-číslo sady transakcí – ovládací prvek-]\_[timestamp] |
| Vstup, výstup a pokud nastavení potvrzení souborů | **Datová část vstupního**: [odesílatele]\_[příjemce]\_X12\_[výměny kontrolní číslo]\_input_payload.txt </p>**Výstupní datovou část**: [odesílatele]\_[příjemce]\_X12\_[výměny kontrolní číslo]\_výstup\_payload.txt </p></p>**Vstupy**: [odesílatele]\_[příjemce]\_X12\_[výměny kontrolní číslo]\_inputs.txt </p></p>**Výstupy**: [odesílatele]\_[příjemce]\_X12\_[výměny kontrolní číslo]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Popisy vlastností zprávy EDIFACT

Tady jsou popisy vlastností pro každou zprávu EDIFACT.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partner s identitou hosta podle **přijímat nastavení**, nebo se musí zadat partner s identitou hostitele v **odeslat nastavení** pro smlouvy EDIFACT |
| Příjemce | Partner hostitelů zadaná v **přijímat nastavení**, nebo se musí zadat partner s identitou hosta v **odeslat nastavení** pro smlouvu EDIFACT |
| Aplikace logiky | Aplikace logiky, ve kterém jsou nastavené akce EDIFACT |
| Status | Stav zprávy EDIFACT <br>Úspěch = přijatých nebo odeslaných platnou zprávu EDIFACT. Je nastavený žádný funkční potvrzení. <br>Úspěch = přijatých nebo odeslaných platnou zprávu EDIFACT. Nastavení a obdrží funkční potvrzení nebo funkční potvrzení posílá. <br>Nepovedlo = přijatých nebo odeslaných neplatná zpráva EDIFACT <br>Čekající = přijatých nebo odeslaných platnou zprávu EDIFACT. Funkční potvrzení je nastavena a funkční potvrzení se očekává. |
| Potvrzení | Funkční stav Ack (997) <br>Přijata = přijatých nebo odeslaných kladné funkční potvrzení <br>Zamítnuto = přijatých nebo odeslaných negativní funkční potvrzení <br>Čekající = očekává funkční potvrzení, ale nebyl přijat. <br>Čekající = generované funkční potvrzení, ale nelze odeslat na partnera. <br>Není nutné = není nastavený funkční potvrzení. |
| Směr | Směr zprávy EDIFACT |
| ID korelace | ID, které souvisí všechny aktivační události a akce v aplikaci logiky |
| Typ zprávy | Typ zprávy EDIFACT |
| ICN | Kontrolní číslo výměny. pro zprávy EDIFACT |
| TSCN | Transakce nastavit kontrolní číslo pro zprávy EDIFACT |
| Timestamp | Čas, kdy akce EDIFACT zpracována zprávy |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formáty názvů EDIFACT pro soubory stažené zpráv.

Tady jsou formáty názvů pro každý stažený složce zpráv EDIFACT a soubory.

| Soubor nebo složku | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [odesílatele] \_[příjemce]\_EDIFACT\_[výměny kontrolní číslo]\_[global-ovládacího prvku number]\_[-číslo sady transakcí – ovládací prvek-]\_[timestamp] |
| Vstup, výstup a pokud nastavení potvrzení souborů | **Datová část vstupního**: [odesílatele]\_[příjemce]\_EDIFACT\_[výměny kontrolní číslo]\_input_payload.txt </p>**Výstupní datovou část**: [odesílatele]\_[příjemce]\_EDIFACT\_[výměny kontrolní číslo]\_výstup\_payload.txt </p></p>**Vstupy**: [odesílatele]\_[příjemce]\_EDIFACT\_[výměny kontrolní číslo]\_inputs.txt </p></p>**Výstupy**: [odesílatele]\_[příjemce]\_EDIFACT\_[výměny kontrolní číslo]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Další postup

* [Dotaz na zprávy B2B ve službě Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní sledování schémata](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
