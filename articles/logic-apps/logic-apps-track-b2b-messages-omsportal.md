---
title: Sledování zpráv B2B pomocí protokolů Azure Monitor – Azure Logic Apps | Microsoft Docs
description: Sledování komunikace B2B pro integrační účty a Azure Logic Apps s využitím Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 33c4efb2b783b5071513f069beac9cdf73c373a8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997841"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Sledování zpráv B2B s využitím protokolů Azure Monitoru

Po nastavení komunikace B2B mezi obchodními partnery v účtu integrace mohou tito partneři vyměňovat zprávy s protokoly, jako jsou AS2, X12 a EDIFACT. Chcete-li ověřit, zda jsou tyto zprávy zpracovány správně, můžete tyto zprávy sledovat pomocí [protokolů Azure monitor](../log-analytics/log-analytics-overview.md). Můžete například využít tyto možnosti sledování webu pro sledování zpráv:

* Počet zpráv a stav
* Stav potvrzení
* Korelace zpráv s potvrzováním
* Podrobné popisy chyb pro selhání
* Možnosti hledání

> [!NOTE]
> Na této stránce jsou dřív popsané kroky, jak provádět tyto úlohy s Microsoft Operations Management Suite (OMS), která se [vyřazuje z ledna 2019](../azure-monitor/platform/oms-portal-transition.md), místo toho nahrazuje tyto kroky pomocí Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, která je nastavená pomocí diagnostického protokolování. Naučte [se, jak vytvořit aplikaci logiky](quickstart-create-first-logic-app-workflow.md) a [jak nastavit protokolování pro tuto aplikaci logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Účet pro integraci, který je nastavený s monitorováním a protokolováním. Přečtěte si, [jak vytvořit účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a [jak nastavit monitorování a protokolování pro tento účet](../logic-apps/logic-apps-monitor-b2b-message.md).

* Pokud jste to ještě neudělali, [publikujte diagnostická data do protokolů Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Po splnění předchozích požadavků budete také potřebovat Log Analytics pracovní prostor, který používáte ke sledování komunikace B2B prostřednictvím Log Analytics. Pokud nemáte pracovní prostor Log Analytics, přečtěte si, [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Instalace řešení Logic Apps B2B

Než budete moct Azure Monitor protokoly sledovat zprávy B2B pro vaši aplikaci logiky, přidejte **Logic Apps B2B** řešení do Azure monitor protokolů. Přečtěte si další informace o [přidávání řešení do protokolů Azure monitor](../azure-monitor/learn/quick-create-workspace.md).

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby**. Do vyhledávacího pole Najděte Log Analytics a vyberte **Log Analytics**.

   ![Vyberte Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. V části **Log Analytics**Najděte pracovní prostor Log Analytics a vyberte ho. 

   ![Vybrat Log Analytics pracovní prostor](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. V části Začínáme **se službou Log Analytics** > **nakonfigurovat řešení monitorování**vyberte **Zobrazit řešení**.

   ![Výběr možnosti Zobrazit řešení](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Na stránce Přehled klikněte na tlačítko **Přidat**, které otevře seznam **řešení pro správu** . V tomto seznamu vyberte možnost **Logic Apps B2B**. 

   ![Vybrat Logic Apps B2B řešení](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Pokud řešení nemůžete najít, v dolní části seznamu vyberte možnost **načíst další** , dokud se řešení nezobrazí.

1. Zvolte **vytvořit**, potvrďte Log Analytics pracovní prostor, do kterého chcete řešení nainstalovat, a pak zvolte **vytvořit** znovu.   

   ![Pro Logic Apps B2B vyberte vytvořit.](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Pokud nechcete používat stávající pracovní prostor, můžete v tuto chvíli vytvořit nový pracovní prostor.

1. Až to budete mít, vraťte se zpátky na stránku **Přehled** svého pracovního prostoru. 

   Řešení Logic Apps B2B se teď zobrazí na stránce Přehled. 
   Při zpracování zpráv B2B se aktualizuje počet zpráv na této stránce.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Zobrazit informace o zprávě B2B

Po zpracování zpráv B2B můžete zobrazit stav a podrobnosti pro tyto zprávy na dlaždici **Logic Apps B2B** .

1. Přejdete do pracovního prostoru Logic Analytics a otevřete stránku Přehled. Vyberte **Logic Apps B2B**.

   ![Počet aktualizovaných zpráv](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Ve výchozím nastavení se na dlaždici **Logic Apps B2B** zobrazuje data na základě jednoho dne. Chcete-li změnit rozsah dat na jiný interval, vyberte v horní části stránky ovládací prvek rozsah:
   > 
   > ![Interval změny](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Po zobrazení řídicího panelu stavu zprávy můžete zobrazit další podrobnosti pro určitý typ zprávy, který zobrazuje data na základě jednoho dne. Vyberte dlaždici pro **AS2**, **X12**nebo **EDIFACT**.

   ![Zobrazit stav zprávy](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Zobrazí se seznam zpráv pro vaši zvolenou dlaždici. 
   Další informace o vlastnostech každého typu zprávy najdete v popisech těchto vlastností zprávy:

   * [Vlastnosti zprávy AS2](#as2-message-properties)
   * [Vlastnosti zprávy X12](#x12-message-properties)
   * [Vlastnosti zprávy EDIFACT](#EDIFACT-message-properties)

   Tady je příklad, jak může seznam zpráv AS2 vypadat takto:

   ![Zobrazit AS2 zprávy](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Chcete-li zobrazit nebo exportovat vstupy a výstupy pro konkrétní zprávy, vyberte tyto zprávy a zvolte možnost **Stáhnout**. Po zobrazení výzvy uložte soubor. zip do místního počítače a potom tento soubor rozbalte. 

   Extrahovaná složka obsahuje složku pro každou vybranou zprávu. 
   Pokud nastavíte potvrzení, složka zprávy obsahuje také soubory s podrobnostmi o potvrzení. 
   Každá složka zpráv má alespoň tyto soubory: 
   
   * Uživatelsky čitelné soubory s podrobnostmi vstupní datové části a výstupní datové části
   * Zakódované soubory se vstupy a výstupy

   Pro každý typ zprávy můžete najít složku a formáty názvů souborů zde:

   * [AS2 složka a formáty názvů souborů](#as2-folder-file-names)
   * [X12 složka a formáty názvů souborů](#x12-folder-file-names)
   * [EDIFACT složka a formáty názvů souborů](#edifact-folder-file-names)

   ![Stažení souborů zpráv](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Chcete-li zobrazit všechny akce, které mají stejné ID spuštění, na stránce **prohledávání protokolu** vyberte zprávu ze seznamu zpráv.

   Tyto akce můžete seřadit podle sloupce nebo vyhledat konkrétní výsledky.

   ![Akce se stejným ID spuštění](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Pokud chcete hledat ve výsledcích s předem vytvořenými dotazy, vyberte **Oblíbené**.

   * Naučte [se vytvářet dotazy přidáním filtrů](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Nebo se dozvíte víc o [tom, jak najít data pomocí prohledávání protokolů v Azure Monitorch protokolech](../log-analytics/log-analytics-log-searches.md).

   * Chcete-li změnit dotaz ve vyhledávacím poli, aktualizujte dotaz sloupci a hodnotami, které chcete použít jako filtry.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Popisy vlastností a formáty názvů pro zprávy AS2, X12 a EDIFACT

V případě každého typu zprávy zde jsou popisy vlastností a formáty názvů pro stažené soubory zpráv.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Popis vlastnosti zprávy AS2

Tady jsou popisy vlastností každé zprávy AS2.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partner hosta zadaný v **Nastavení příjmu**nebo v hostitelském partnerovi zadaném v **nastavení Odeslat** pro smlouvu AS2 |
| Přijímač | Hostitelský partner zadaný v **Nastavení příjmu**nebo partner hosta zadaný v **nastavení Odeslat** pro smlouvu AS2 |
| Aplikace logiky | Aplikace logiky, ve které se nastavují akce AS2 |
| Stav | Stav zprávy AS2 <br>Úspěch = přijata nebo odeslána platná zpráva AS2. Není nastavený žádný MDN. <br>Úspěch = přijata nebo odeslána platná zpráva AS2. MDN se nastavuje a přijímá nebo se posílá MDN. <br>Neúspěšné = byla přijata neplatná zpráva AS2. Není nastavený žádný MDN. <br>Pending = přijato nebo odeslána platná zpráva AS2. MDN je nastavené a očekává se MDN. |
| Ztracen | Stav zprávy MDN <br>Přijato = přijato nebo odesláno kladné MDN. <br>Čeká na vyřízení = čekání na přijetí nebo odeslání MDN. <br>Odmítnuto = přijato nebo odesláno záporné MDN. <br>Nepožadováno = MDN se v této smlouvě nenastavuje. |
| Direction | Směr zprávy AS2 |
| ID korelace | ID, které koreluje všechny triggery a akce v aplikaci logiky |
| ID zprávy | ID zprávy AS2 ze záhlaví zpráv AS2 |
| Timestamp | Čas, kdy akce AS2 zpracovala zprávu |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formáty názvů AS2 pro stažené soubory zpráv

Tady jsou formáty názvů pro každou staženou složku zpráv AS2 a soubory.

| Složka nebo soubor | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Vstup, výstup a v případě nastavení, potvrzení souborů | **Vstupní datová část**: [Sender\_] [přijímač\_]\_AS2 [Correlation-ID\_] input_payload. txt </p>**Výstupní datová část**: [Sender\_] [přijímač\_]\_AS2 [Correlation-ID\_]\_Output data. txt </p></p>**Vstupy**: [Sender]\_[přijímač]\_AS2\_[Correlation-ID]\_Inputs. txt </p></p>**Výstupy**: [Sender\_] [přijímač\_]\_AS2 [Correlation-ID\_] výstups. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Popis vlastnosti zprávy X12

Tady jsou popisy vlastností každé zprávy X12.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partner hosta zadaný v **Nastavení příjmu**nebo v hostitelském partnerovi zadaném v **nastavení Odeslat** pro smlouvu X12 |
| Přijímač | Hostitelský partner zadaný v **Nastavení příjmu**nebo partner hosta zadaný v **nastavení Odeslat** pro smlouvu X12 |
| Aplikace logiky | Aplikace logiky, ve které se nastavují akce X12 |
| Stav | Stav zprávy X12 <br>Úspěch = přijata nebo odeslána platná zpráva X12. Není nastavené žádné funkční potvrzení. <br>Úspěch = přijata nebo odeslána platná zpráva X12. Je nastaveno a přijato funkční potvrzení, nebo je odesláno funkční potvrzení. <br>Nepodařilo se = přijmout nebo odeslat neplatnou zprávu X12. <br>Pending = přijato nebo odeslána platná zpráva X12. Je nastavené funkční potvrzení a očekává se funkční potvrzení. |
| Ztracen | Stav funkce ACK (997) <br>Přijato = přijato nebo odesláno kladné funkční potvrzení. <br>Odmítnuto = přijato nebo odesláno záporné funkční potvrzení. <br>Pending = očekává se funkční potvrzení, ale nepřijalo se. <br>Pending = vygenerovalo funkční potvrzení, ale nemůže být odesláno partnerovi. <br>Nepožadováno = funkční potvrzení není nastavené. |
| Direction | Směr zprávy X12 |
| ID korelace | ID, které koreluje všechny triggery a akce v aplikaci logiky |
| Typ zprávy | Typ zprávy X12 EDI |
| ICN | Řídicí číslo výměny pro zprávu X12 |
| TSCN | Kontrolní číslo sady transakcí pro zprávu X12 |
| Timestamp | Čas, kdy akce X12 zpracovala zprávu |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formáty názvů X12 pro stažené soubory zpráv

Tady jsou formáty názvů pro každou staženou složku zpráv X12 a soubory.

| Složka nebo soubor | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Vstup, výstup a v případě nastavení, potvrzení souborů | **Vstupní datová část**: [Sender\_] [přijímač\_]\_X12 [Interchange-Control-number\_] input_payload. txt </p>**Výstupní datová část**: [Sender\_] [přijímač\_]\_X12 [Interchange-Control-number\_]\_Output data. txt </p></p>**Vstupy**: [Sender]\_[přijímač]\_X12\_[Interchange-Control-Number]\_Inputs. txt </p></p>**Výstupy**: [Sender\_] [přijímač\_]\_X12 [Interchange-Control-number\_] Outputs. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Popis vlastnosti zprávy EDIFACT

Tady jsou popisy vlastností každé zprávy EDIFACT.

| Vlastnost | Popis |
| --- | --- |
| Odesílatel | Partner hosta zadaný v **Nastavení příjmu**nebo v hostitelském partnerovi zadaném v **nastavení Odeslat** pro smlouvu EDIFACT |
| Přijímač | Hostitelský partner zadaný v **Nastavení příjmu**nebo partner hosta zadaný v **nastavení Odeslat** pro smlouvu EDIFACT |
| Aplikace logiky | Aplikace logiky, ve které se nastavují akce EDIFACT |
| Stav | Stav zprávy EDIFACT <br>Úspěch = přijata nebo odeslána platná zpráva EDIFACT. Není nastavené žádné funkční potvrzení. <br>Úspěch = přijata nebo odeslána platná zpráva EDIFACT. Je nastaveno a přijato funkční potvrzení, nebo je odesláno funkční potvrzení. <br>Nepodařilo se = přijmout nebo odeslat neplatnou zprávu EDIFACT <br>Pending = přijato nebo odeslána platná zpráva EDIFACT. Je nastavené funkční potvrzení a očekává se funkční potvrzení. |
| Ztracen | Stav CONTRL (funkční ACK) <br>Přijato = přijato nebo odesláno kladné funkční potvrzení. <br>Odmítnuto = přijato nebo odesláno záporné funkční potvrzení. <br>Pending = očekává se funkční potvrzení, ale nepřijalo se. <br>Pending = vygenerovalo funkční potvrzení, ale nemůže být odesláno partnerovi. <br>Nepožadováno = funkční potvrzení není nastavené. |
| Direction | Směr zprávy EDIFACT |
| ID korelace | ID, které koreluje všechny triggery a akce v aplikaci logiky |
| Typ zprávy | Typ zprávy EDIFACT |
| ICN | Řídicí číslo výměny pro zprávu EDIFACT |
| TSCN | Kontrolní číslo sady transakcí pro zprávu EDIFACT |
| Timestamp | Čas, kdy akce EDIFACT zpracovala zprávu |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formáty názvů EDIFACT pro stažené soubory zpráv

Tady jsou formáty názvů pro každou staženou složku zpráv EDIFACT a soubory.

| Složka nebo soubor | Formát názvu |
| :------------- | :---------- |
| Složka zpráv | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Vstup, výstup a v případě nastavení, potvrzení souborů | **Vstupní datová část**: [Sender\_] [přijímač\_]\_EDIFACT [Interchange-Control-number\_] input_payload. txt </p>**Výstupní datová část**: [Sender\_] [přijímač\_]\_EDIFACT [Interchange-Control-number\_]\_Output data. txt </p></p>**Vstupy**: [Sender]\_[přijímač]\_EDIFACT\_[Interchange-Control-Number]\_Inputs. txt </p></p>**Výstupy**: [Sender\_] [přijímač\_]\_EDIFACT [Interchange-Control-number\_] Outputs. txt |
|          |             |

## <a name="next-steps"></a>Další kroky

* [Dotaz na zprávy B2B v protokolu Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schémata sledování AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémata sledování X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Vlastní schémata sledování](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
