---
title: IT Service Management Connector v Log Analytics
description: Tento článek poskytuje přehled konektoru pro správu služby IT (ITSMC) a informace o jeho používání pro monitorování a správu pracovních položek ITSM v Log Analytics a rychlé řešení problémů.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 3d4e5ad0b24b7163072d7e3110a523dad9608923
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507367"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Připojení Azure k nástrojům ITSM pomocí služby IT Service Management Connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (ITSMC) umožňuje připojit Azure k podporovanému produktu nebo službě IT Service Management (ITSM).

Služby Azure, jako je Azure Log Analytics a Azure Monitor poskytují nástroje pro detekci, analýzu a řešení problémů s prostředky Azure a mimo Azure. Pracovní položky, které se k problému vztahují, se obvykle nacházejí v ITSMm produktu nebo ve službě. ITSMC poskytuje obousměrné propojení mezi nástroji Azure a ITSM, které vám pomůžou rychleji řešit problémy.

ITSMC podporuje připojení s následujícími ITSM nástroji:

-   ServiceNow
-   System Center Service Manager
-   Prov
-   Cherwell

   >[!NOTE]
> Naši zákazníci Cherwell a prov navrhují, aby používali [akci Webhooku](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

Pomocí ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě výstrah Azure (výstrahy metrik, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete svůj incident a data žádostí o změnu z nástroje ITSM na pracovní prostor služby Azure Log Analytics synchronizovat.

Informace o právních výrazech a zásadách ochrany osobních údajů najdete v tématu [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Můžete začít používat ITSMC provedením následujících kroků:

1.  [Přidat ITSMC.](#add-it-service-management-connector)
2. [Propojení produktů nebo služeb ITSM s využitím ITSM konektoru](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
3.  [Vytvořte připojení ITSM.](#create-an-itsm-connection)
4.  [Použijte připojení.](#use-itsmc)
   
   >[!NOTE]
> Je nutné postupovat podle tohoto pořadí, jinak se zobrazí chyba.

##  <a name="add-it-service-management-connector"></a>Přidat konektor pro správu služeb IT

Než budete moct vytvořit připojení, musíte přidat ITSMC.

1. V Azure Portal vyberte **vytvořit prostředek**:

   ![Snímek obrazovky, který ukazuje položku nabídky vytvořit prostředek.](media/itsmc-overview/azure-add-new-resource.png)

2. Vyhledejte **konektor pro správu služeb IT** v Azure Marketplace. Vyberte **vytvořit**:

   ![Snímek obrazovky, který zobrazuje tlačítko vytvořit v Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. V části **pracovní prostor OMS** vyberte pracovní prostor Azure Log Analytics, do kterého chcete nainstalovat ITSMC.
   >[!NOTE]
   > * V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se teď pracovní prostory OMS označují jako *Log Analytics pracovní prostory*.
   > * ITSMC se dá nainstalovat jenom do Log Analytics pracovních prostorů v následujících oblastech: Východní USA, Západní USA 2, Střed USA – jih, Středozápadní USA, US Gov – Arizona, US Gov – Virginie, Kanada – střed, Západní Evropa, Jižní Velká Británie, jihovýchodní Asie, Japonsko – východ, Střed Indie a Austrálie – jihovýchod.


4. V části **pracovní prostor Log Analytics** vyberte skupinu prostředků, ve které chcete vytvořit prostředek ITSMC:

   ![Snímek obrazovky zobrazující část Log Analytics pracovní prostor](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se teď pracovní prostory OMS označují jako *Log Analytics pracovní prostory*.

5. Vyberte **OK**.

Po nasazení prostředku ITSMC se v pravém horním rohu okna zobrazí oznámení.


## <a name="create-an-itsm-connection"></a>Vytvoření připojení ITSM

Po instalaci ITSMC můžete vytvořit připojení.

Pokud chcete vytvořit připojení, budete muset nástroj ITSM použít k tomu, aby bylo možné připojení z ITSMC.  

Podle ITSM produktu, ke kterému se připojujete, vyberte jeden z následujících odkazů pro pokyny:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Prov](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Po připravenou nástrojů ITSM dokončete tyto kroky a vytvořte připojení:

1. Ve **všech prostředcích** vyhledejte **partnera (*název vašeho pracovního prostoru*)**:

   ![Snímek obrazovky, který zobrazuje poslední prostředky v Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. V části **zdroje dat pracovního prostoru** v levém podokně vyberte **připojení ITSM**:

   ![Snímek obrazovky zobrazující položku nabídky připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)
   Tato stránka zobrazuje seznam připojení.
1. Vyberte **Přidat připojení**.

4. Zadejte nastavení připojení, jak je popsáno v tématu [Konfigurace připojení ITSMC k vašim ITSMm produktům a službám](./itsmc-connections.md).

   > [!NOTE]
   >
   > Ve výchozím nastavení ITSMC aktualizuje konfigurační data připojení každých 24 hodin. Pokud chcete data připojení okamžitě aktualizovat tak, aby odrážela jakékoli úpravy nebo aktualizace šablon, které jste provedli, vyberte v okně připojení tlačítko **synchronizovat** :
   >
   > ![Snímek obrazovky, který zobrazuje tlačítko synchronizovat v okně připojení](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>Použití ITSMC
   Pomocí ITSMC můžete vytvářet pracovní položky z výstrah Azure, výstrahy Log Analytics a Log Analytics záznamů protokolu.

## <a name="template-definitions"></a>Definice šablon
   Existují typy pracovních položek, které mohou používat šablony, které jsou definovány nástrojem ITSM.
Pomocí šablon můžete definovat pole, která budou automaticky vyplněna podle pevných hodnot, které jsou definovány jako součást skupiny akcí. Šablony definujete v nástroji ITSM. V rámci definice skupiny akcí můžete definovat, ve které šabloně se chcete použít.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytváření pracovních položek ITSM z výstrah Azure

Po vytvoření připojení ITSM můžete v nástroji ITSM vytvořit pracovní položky založené na výstrahách Azure. Chcete-li vytvořit pracovní položky, použijte akci ITSM ve skupinách akcí.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob, jak aktivovat akce pro vaše výstrahy Azure. V Azure Portal můžete použít skupiny akcí s upozorněními na metriky, výstrahy protokolu aktivit a výstrahy Azure Log Analytics.

> [!NOTE]
> Po vytvoření připojení ITSM musíte počkat 30 minut, než se proces synchronizace dokončí.
> 

Chcete-li vytvořit pracovní položky, použijte následující postup:

1. V Azure Portal vyberte  **výstrahy**.
2. V nabídce v horní části obrazovky vyberte **Spravovat akce**:

    ![Snímek obrazovky zobrazující položku nabídky spravovat akce](media/itsmc-overview/action-groups-selection-big.png)

   Zobrazí se okno **vytvořit skupinu akcí** .

3. Vyberte **předplatné** a **skupinu prostředků** , ve kterých chcete vytvořit skupinu akcí. Zadejte **název skupiny akcí** a **zobrazované jméno** pro vaši skupinu akcí. Vyberte **Další: oznámení**.

    ![Snímek obrazovky, který ukazuje okno vytvořit skupinu akcí.](media/itsmc-overview/action-groups-details.png)

4. V seznamu oznámení vyberte **Další: akce**.
5. V seznamu akce vyberte v seznamu **typ akce** možnost **ITSM** . Zadejte **název** akce. Vyberte tlačítko pera, které představuje **Úpravy podrobností**.
6. V seznamu **předplatné** vyberte předplatné, ve kterém se nachází váš pracovní prostor Log Analytics. V seznamu **připojení** vyberte název konektoru ITSM. Za ním bude následovat název vašeho pracovního prostoru. Například MyITSMConnector (MyWorkspace).

7. Vyberte typ **pracovní položky** .

8. Pokud chcete vyplnit předem vydaná pole s pevnými hodnotami, vyberte **použít vlastní šablonu**. V opačném případě v seznamu **šablon** vyberte existující [šablonu](#template-definitions) a v polích šablony zadejte pevné hodnoty.

9. Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku konfigurace**, bude každá položka konfigurace mít svou vlastní pracovní položku. Pro každou položku konfigurace bude k dispozici jedna pracovní položka. Bude aktualizován podle výstrah, které budou vytvořeny.

    * V případě, že vyberete v rozevíracím seznamu pracovní položky "incident" nebo "Alert": Pokud zrušíte zaškrtnutí políčka **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** , bude každá výstraha vytvářet novou pracovní položku. Může existovat více než jedna výstraha na jednu položku konfigurace.

       ![Snímek obrazovky, který zobrazuje okno incidentu ITSM.](media/itsmc-overview/itsm-action-configuration.png)

    * V případě, že vyberete položku "událost" v rozevíracím seznamu pracovní položky: Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku protokolu** na výběr přepínačů, bude každá výstraha vytvářet novou pracovní položku. Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** v výběru přepínačů, bude mít každá položka konfigurace svou vlastní pracovní položku.
   ![Snímek obrazovky zobrazující okno události ITSM](media/itsmc-overview/itsm-action-configuration-event.png)

10. Vyberte **OK**.

Při vytváření nebo úpravách pravidla upozornění Azure použijte skupinu akcí, která má akci ITSM. Když se výstraha aktivuje, v nástroji ITSM se vytvoří nebo aktualizuje pracovní položka.

> [!NOTE]
>
>- Informace o cenách akce ITSM najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.
>
>
>- Pole s krátkým popisem v definici pravidla výstrahy je při odeslání pomocí akce ITSM omezené na 40 znaků.

## <a name="additional-information"></a>Další informace

### <a name="data-synced-from-your-itsm-product"></a>Data synchronizovaná z produktu ITSM
Incidenty a žádosti o změnu se synchronizují z vašeho ITSM produktu do vašeho pracovního prostoru Log Analytics v závislosti na konfiguraci připojení.

V této části jsou uvedeny některé příklady dat shromažďovaných nástrojem ITSMC.

Pole v **ServiceDesk_CL** se liší v závislosti na typu pracovní položky, který importujete do Log Analytics. Zde je seznam polí pro dva typy pracovních položek:

**Pracovní položka:** **incidenty**  
ServiceDeskWorkItemType_s = incident

**Pole**

- ServiceDeskConnectionName
- ID oddělení služeb
- State
- Naléhavost
- Dopad
- Priorita
- Eskalace
- Created By
- Vyřešil
- Uzavřel
- Zdroj
- Přiřazený pro
- Kategorie
- Nadpis
- Popis
- Datum vytvoření
- Datum uzavření
- Datum vyřešení
- Datum poslední změny
- Počítač


**Pracovní položka:** **žádosti o změnu**

ServiceDeskWorkItemType_s = "žádost o změnu"

**Pole**
- ServiceDeskConnectionName
- ID oddělení služeb
- Created By
- Uzavřel
- Zdroj
- Přiřazený pro
- Nadpis
- Typ
- Kategorie
- State
- Eskalace
- Stav konfliktu
- Naléhavost
- Priorita
- Riziko
- Dopad
- Přiřazený pro
- Datum vytvoření
- Datum uzavření
- Datum poslední změny
- Požadované datum
- Plánované počáteční datum
- Plánované koncové datum
- Datum zahájení práce
- Datum ukončení práce
- Popis
- Počítač

## <a name="output-data-for-a-servicenow-incident"></a>Výstupní data pro incident ServiceNow

| Log Analytics pole | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| IncidentState_s | State |
| Urgency_s |Naléhavost |
| Impact_s |Dopad|
| Priority_s | Priorita |
| CreatedBy_s | Otevřel |
| ResolvedBy_s | Vyřešil|
| ClosedBy_s  | Uzavřel |
| Source_s| Typ kontaktu |
| AssignedTo_s | Přiřazeno  |
| Category_s | Kategorie |
| Title_s|  Krátký popis |
| Description_s|  Poznámky |
| CreatedDate_t|  Otevřít |
| ClosedDate_t| closed|
| ResolvedDate_t|Vyřešeno|
| Počítač  | Položka konfigurace |

## <a name="output-data-for-a-servicenow-change-request"></a>Výstupní data pro žádost o změnu ServiceNow

| Log Analytics | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| CreatedBy_s | Požadoval (a) |
| ClosedBy_s | Uzavřel |
| AssignedTo_s | Přiřazeno  |
| Title_s|  Krátký popis |
| Type_s|  Typ |
| Category_s|  Kategorie |
| CRState_s|  State|
| Urgency_s|  Naléhavost |
| Priority_s| Priorita|
| Risk_s| Riziko|
| Impact_s| Dopad|
| RequestedDate_t  | Požadováno podle data |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  | Plánované počáteční datum |
| PlannedEndDate_t  | Plánované koncové datum |
| WorkStartDate_t  | Skutečné datum zahájení |
| WorkEndDate_t | Skutečné datum ukončení|
| Description_s | Popis |
| Počítač  | Položka konfigurace |

## <a name="contact-us"></a>Kontaktujte nás

Pokud máte dotazy nebo připomínky ke konektoru správy IT služeb, kontaktujte nás na adrese [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Další kroky
[Přidat ITSM produkty/služby do konektoru správy služeb IT](./itsmc-connections.md)
