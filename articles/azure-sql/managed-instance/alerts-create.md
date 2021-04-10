---
title: Nastavení výstrah a oznámení pro spravovanou instanci (Azure Portal)
description: Pomocí Azure Portal můžete vytvořit výstrahy spravované instance SQL, které můžou aktivovat oznámení nebo automatizaci při splnění podmínek, které zadáte.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592712"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Vytváření upozornění pro službu Azure SQL Managed Instance pomocí webu Azure Portal
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto článku se dozvíte, jak nastavit výstrahy pro databáze v databázi spravované instance Azure SQL pomocí Azure Portal. Upozornění můžou poslat e-mail, zavolat webhook, spustit funkci Azure Functions nebo runbook, zavolat externí systém lístků kompatibilní s ITSM, zavolat na telefon nebo poslat textovou zprávu, když určitá metrika, například metrika velikosti úložiště instance nebo využití procesoru, dosáhne předdefinované prahové hodnoty. Tento článek také poskytuje osvědčené postupy pro nastavení dob upozornění.


## <a name="overview"></a>Přehled

Můžete obdržet upozornění na základě metrik monitorování pro události nebo služby Azure.

* **Hodnoty metrik** – výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte v obou směrech. To znamená, že se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní.

Můžete nakonfigurovat výstrahu, která při triggeru provede následující akce:

* Posílání e-mailových oznámení správcům služeb a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání telefonního čísla pomocí hlasového výzvy
* Poslat textovou zprávu na telefonní číslo
* Volání webhooku
* Volání funkce Azure Functions
* Volání Runbooku Azure
* Volání externího systému kompatibilního s ITSM pro lístky

Pomocí [Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure](../../azure-monitor/alerts/alerts-classic-portal.md) nebo [Azure monitor REST API](/rest/api/monitor/alertrules)můžete nakonfigurovat a získat informace o pravidlech výstrah. 

## <a name="alerting-metrics-available-for-managed-instance"></a>Metriky výstrahy dostupné pro spravovanou instanci

> [!IMPORTANT]
> Metriky výstrah jsou k dispozici pouze pro spravovanou instanci. Metriky výstrah pro jednotlivé databáze ve spravované instanci nejsou k dispozici. Telemetrie diagnostiky databáze je na druhé straně k dispozici ve formě [diagnostických protokolů](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Výstrahy na diagnostické protokoly je možné nastavit z produktu [SQL Analytics](../../azure-monitor/insights/azure-sql.md) pomocí [skriptů s výstrahami protokolu](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) pro spravovanou instanci.

Pro konfiguraci výstrah jsou k dispozici následující metriky spravované instance:

| Metric | Popis | Měrná jednotka \ možné hodnoty |
| :--------- | --------------------- | ----------- |
| Průměrné procento procesoru | Průměrné procento využití procesoru ve vybraném časovém období | 0-100 (procenta) |
| Přečtené vstupně-výstupní bajty | Ve vybraném časovém období je přečtených vstupně-výstupních bajtů. | Bajty |
| Zapsané vstupně-výstupní bajty | Vstupně-výstupní bajty zapsané ve vybraném časovém období | Bajty |
| Počet požadavků v/v | Počet vstupně-výstupních požadavků ve vybraném časovém období | Číselné |
| Rezervované místo v úložišti | Aktuální maximální místo v úložišti rezervované pro spravovanou instanci. Změny s operací škálování prostředků. | MB (MB) |
| Využité místo úložiště | Prostor úložiště použitý ve vybraném období Změny s využitím úložiště databázemi a instancemi. | MB (MB) |
| Počet virtuálních jader | Virtuální jádra zřízené pro spravovanou instanci. Změny s operací škálování prostředků. | 4-80 (virtuální jádra) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě s Azure Portal

1. Na webu Azure [Portal](https://portal.azure.com/)vyhledejte spravovanou instanci, kterou zajímáte o monitorování, a vyberte ji.

2. V části monitorování vyberte položku nabídky **metriky** .

   ![Monitorování](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. V rozevírací nabídce vyberte jednu z metrik, pro kterou chcete nastavit upozornění (v tomto příkladu se zobrazuje využité místo pro úložiště).

4. Vyberte agregační období – průměr, minimum nebo maximum dosažené v daném časovém období (průměr, minimum nebo maximum). 

5. Vybrat **nové pravidlo výstrahy**

6. V podokně vytvořit pravidlo výstrahy klikněte na **název podmínky** (využité místo úložiště se zobrazí v příkladu).

   ![Definovat podmínku](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. V podokně Konfigurace logiky signálů definujte operátor, typ agregace a prahovou hodnotu.

   * Možnosti typu operátoru jsou větší než, rovné a menší než (prahová hodnota).
   * Možnosti typu agregace jsou minimální, maximální nebo průměr (v období členitosti agregace).
   * Prahová hodnota je hodnota výstrahy, která bude vyhodnocena na základě kritérií operátora a agregace.
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   V příkladu zobrazeném na snímku obrazovky se používá hodnota 1840876 MB představující prahovou hodnotu 1,8 TB. V případě, že je operátor v příkladu nastaven na hodnotu větší než, bude výstraha vytvořena v případě, že spotřeba prostoru úložiště na spravované instanci projde více než 1,8 TB. Všimněte si, že prahová hodnota pro metriky prostorů úložiště musí být vyjádřená v MB.

8. Nastavte interval hodnocení – členitost agregace v minutách a četnost vyhodnocování. Frekvence hodnocení Poznamenejte čas, po který bude systém výstrah pravidelně kontrolovat, zda byla podmínka prahová hodnota splněna.

9. Vyberte skupina akcí. Zobrazí se podokno skupina akcí, ve kterém budete moci vybrat existující nebo vytvořit novou akci. Tato akce definuje, k čemu dojde při aktivaci výstrahy (například odeslání e-mailu, volání na telefonu, spuštění Webhooku, funkce Azure nebo sady Runbook).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Pokud chcete vytvořit novou skupinu akcí, vyberte **+ vytvořit skupinu akcí** .

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Určete, jak chcete být upozorňováni: zadejte název skupiny akcí, krátký název, název akce a vyberte typ akce. Typ akce definuje, jestli budete upozorňováni e-mailem, textovou zprávou, hlasovým hovorem nebo pokud bude možné Webhook, funkci Azure Functions, Runbook se spustí, nebo se vytvoří lístek ITSM ve vašem kompatibilním systému.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Vyplňte podrobnosti pravidla výstrahy pro vaše záznamy, vyberte typ závažnosti.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Vytvoření pravidla výstrahy dokončíte kliknutím na tlačítko **vytvořit pravidlo upozornění** .

Nové pravidlo upozornění bude aktivní během několika minut a bude aktivováno na základě vašich nastavení.

## <a name="verifying-alerts"></a>Ověřování výstrah

> [!NOTE]
> Pokud chcete potlačit výstrahy na vysokou úroveň, přečtěte si téma [potlačení výstrahy pomocí pravidel akcí](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts).

Po nastavení pravidla výstrahy ověřte, že jste spokojeni s triggerem výstrah a jeho frekvencí. Pro příklad, který se zobrazuje na této stránce pro nastavení výstrahy na využité místo, se může zobrazit e-mailová zpráva, jako je ta, kterou vidíte níže.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

E-mail zobrazuje název výstrahy, podrobnosti prahové hodnoty a důvody, proč byla výstraha aktivována, což vám pomůže ověřit a vyřešit potíže s výstrahou. Pomocí tlačítka **Zobrazit v Azure Portal** můžete zobrazit upozornění přijatá prostřednictvím e-mailu v Azure Portal. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Zobrazit, pozastavit, aktivovat, upravit a odstranit existující pravidla upozornění

> [!NOTE]
> Existující výstrahy je potřeba spravovat z nabídky výstrahy z Azure Portalovém řídicím panelu. Existující výstrahy nejde změnit z okna prostředku spravované instance.

Zobrazení, pozastavení, aktivace, úpravy a odstranění existujících výstrah:

1. Vyhledejte výstrahy pomocí Azure Portalho vyhledávání. Klikněte na výstrahy.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Případně můžete také kliknout na výstrahy na navigačním panelu Azure, pokud jste ji nakonfigurovali.

2. V podokně výstrahy vyberte Spravovat pravidla výstrah.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Zobrazí se seznam existujících výstrah. Vyberte jednotlivá pravidla výstrahy, která chcete spravovat. Existující aktivní pravidla je možné upravit a vyladit podle svých preferencí. Aktivní pravidla je také možné pozastavit bez odstranění. 

## <a name="next-steps"></a>Další kroky

* Informace o Azure Monitor systému výstrah najdete v tématu [Přehled výstrah v Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
* Další informace o výstrahách metrik najdete [v tématu vysvětlení, jak fungují výstrahy metrik v Azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md)
* Další informace o konfiguraci Webhooku v upozorněních najdete v tématu [volání Webhooku s klasickými výstrahami metriky](../../azure-monitor/alerts/alerts-webhooks.md) .
* Informace o konfiguraci a správě výstrah pomocí PowerShellu najdete v tématu [pravidla akcí](/powershell/module/az.monitor/add-azmetricalertrulev2) .
* Informace o konfiguraci a správě výstrah pomocí rozhraní API najdete v tématu [Azure Monitor REST API Reference](/rest/api/monitor/) .