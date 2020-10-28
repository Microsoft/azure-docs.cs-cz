---
title: IT Service Management Connector v Azure Log Analytics | Microsoft Docs
description: Tento článek poskytuje přehled konektoru pro správu služeb IT (ITSMC) a informace o tom, jak toto řešení použít k centrálnímu monitorování a správě pracovních položek ITSM v Azure Log Analytics a k řešení případných problémů rychle.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 5c18a904f0ec0f100312ee3fafb53038bd2ccf19
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745672"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Propojení Azure s nástroji ITSM s využitím ITSM konektoru

![Symbol IT Service Management Connectoru](media/itsmc-overview/itsmc-symbol.png)

ITSM konektor (ITSMC) umožňuje propojit Azure s podporovaným produktem nebo službou správy IT služeb (ITSM).

Služby Azure, jako jsou Log Analytics a Azure Monitor poskytují nástroje pro detekci, analýzu a řešení potíží s prostředky Azure a mimo Azure. Pracovní položky týkající se problému se ale obvykle nacházejí v ITSMm produktu nebo službě. Konektor ITSM poskytuje obousměrné propojení mezi nástroji Azure a ITSM, které vám pomůžou rychleji řešit problémy.

ITSMC podporuje připojení s následujícími ITSM nástroji:

-   ServiceNow
-   System Center Service Manager
-   Prov
-   Cherwell

Pomocí ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě vašich výstrah Azure (výstrahy metrik, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete svůj incident a data žádostí o změnu z nástroje ITSM na pracovní prostor služby Azure Log Analytics synchronizovat.

Přečtěte si další informace o [právních podmínek a zásadách ochrany osobních údajů](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Můžete začít používat konektor ITSM pomocí následujících kroků:

1.  [Přidat řešení konektoru ITSM](#adding-the-it-service-management-connector-solution)
2.  Vytvoření připojení ITSM
3.  [Použít připojení](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Přidání řešení IT Service Management Connector

Než budete moct vytvořit připojení, musíte přidat řešení konektoru ITSM.

1. V Azure Portal klikněte na **+ Nová** ikona.

   ![Nový prostředek Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Vyhledejte na webu Marketplace **konektor pro správu služeb IT** a klikněte na **vytvořit** .

   ![Přidat řešení ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. V části **pracovní prostor OMS** vyberte pracovní prostor Azure Log Analytics, do kterého chcete řešení nainstalovat.
   >[!NOTE]
   > * V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se teď pracovní prostory OMS označují jako Log Analytics pracovní prostory.
   > * Konektor ITSM se dá nainstalovat jenom do Log Analytics pracovních prostorů v následujících oblastech: Východní USA, západ USA 2, Střed USA – jih, Středozápadní USA, Fairfax, Central Canada, Západní Evropa, Jižní Británie, jihovýchodní Asie, Východní Japonsko, Střed Indie, jihovýchodní Austrálie.

4. V části **Nastavení pracovního prostoru OMS** vyberte zdrojovou položku, kde chcete vytvořit prostředek řešení.

   ![Pracovní prostor ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se teď pracovní prostory OMS označují jako Log Analytics pracovní prostory.

5. Klikněte na **OK** .

Po nasazení prostředku řešení se zobrazí oznámení v pravém horním rohu okna.


## <a name="creating-an-itsm--connection"></a>Vytvoření připojení ITSM

Po instalaci řešení můžete vytvořit připojení.

Pro vytvoření připojení budete muset nástroj ITSM použít k tomu, aby bylo možné připojení z řešení konektoru ITSM.  

V závislosti na ITSM produktu, ke kterému se připojujete, použijte následující postup:

- [System Center Service Manager (SCSM)](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Prov](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Jakmile budete mít připravenou nástroje ITSM, vytvořte připojení pomocí následujících kroků:

1. Přejít na **všechny prostředky** , vyhledejte **partnera (YourWorkspaceName)** .
2. V části **zdroje dat pracovního prostoru** v levém podokně klikněte na **připojení ITSM** .
   ![Připojení ITSM](media/itsmc-overview/itsm-connections.png)

   Tato stránka zobrazuje seznam připojení.
3. Klikněte na **Přidat připojení** .

   ![Přidat připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Zadejte nastavení připojení, jak je popsáno v [tématu Konfigurace připojení ITSMC k článku ITSM Products/Services](./itsmc-connections.md).

   > [!NOTE]
   >
   > Ve výchozím nastavení ITSMC aktualizuje konfigurační data připojení jednou za každých 24 hodin. Pokud chcete data připojení okamžitě aktualizovat pro jakékoli úpravy nebo aktualizace šablon, které uděláte, klikněte na tlačítko **synchronizovat** v okně připojení.

   ![Aktualizace připojení](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Použití řešení
   Pomocí řešení konektoru ITSM můžete vytvářet pracovní položky z výstrah Azure, Log Analytics upozornění a záznamů protokolu Log Analytics.

## <a name="template-definitions"></a>Definice šablon
   Existují typy pracovních položek, které mohou používat šablony, které jsou definovány nástrojem ITSM.
Použití šablon umožňuje zákazníkovi definovat pole, která se automaticky vyplní podle pevných hodnot, které jsou definovány jako součást skupiny akcí. Definice šablon se provádí v nástroji ITSM.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytváření pracovních položek ITSM z výstrah Azure

Po vytvoření připojení ITSM můžete vytvořit pracovní položku v nástroji ITSM na základě výstrah Azure pomocí **Akce ITSM** ve **skupinách akcí** .

Skupiny akcí poskytují modulární a opakovaně použitelný způsob aktivace akcí pro vaše výstrahy Azure. Skupiny akcí s upozorněními na metriky, upozornění protokolu aktivit a výstrahy služby Azure Log Analytics můžete používat v Azure Portal.

> [!NOTE]
> Po vytvoření připojení ITSM je potřeba po dokončení konektoru, který se vytvoří pro proces synchronizace, počkat na 30 min.
> 

Použijte následující postup:

1. V Azure Portal klikněte na  **výstrahy** .
2. V horním podokně klikněte na možnost  **Spravovat akce** . Zobrazí se okno **Přidat skupinu akcí** .

    [![Skupiny akcí](media/itsmc-overview/action-groups-selection.png)](media/itsmc-overview/action-groups-selection-big.png)

3. Vyberte **předplatné** a **skupinu prostředků** , ve kterých chcete vytvořit skupinu akcí. Zadejte **název skupiny akcí** a **zobrazované jméno** pro vaši skupinu akcí. Klikněte na **Další: oznámení** .

    ![Podrobnosti o skupinách akcí](media/itsmc-overview/action-groups-details.png)

4. V seznamu oznámení klikněte na **vnoření: akce** .
5. V seznamu akce vyberte možnost **ITSM** z rozevírací nabídky pro **typ akce** . Zadejte **název** akce a klikněte na pero, které představuje **Podrobnosti o úpravách** .
6. Vyberte **předplatné** , ve kterém se nachází váš Log Analytics pracovní prostor. Vyberte název **připojení** (název konektoru ITSM) následovaný názvem vašeho pracovního prostoru. Například "MyITSMMConnector (MyWorkspace)."

    ![Podrobnosti o akci ITSM](media/itsmc-overview/itsm-action-configuration.png)

7. Z rozevírací nabídky vyberte typ **pracovní položky** .

8. Chcete-li vyplnit pole s pevnými hodnotami, byste měli zaškrtnout políčko použít vlastní šablonu, jinak zvolit použití existující [šablony](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#template-definitions) z rozevíracího seznamu a vyplnit pole šablony pevnými hodnotami.

9. Zaškrtnutím políčka **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** každá položka konfigurace bude mít svou vlastní pracovní položku. To znamená, že bude jedna pracovní položka na jednu položku konfigurace a bude aktualizována podle výstrah, které budou vytvořeny.
Pokud zrušíte zaškrtnutí políčka **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** , každá výstraha vytvoří novou pracovní položku, což znamená, že může být více než jedna výstraha pro jednu položku konfigurace.

10. Klikněte na **OK** .

Při vytváření nebo úpravách pravidla upozornění Azure použijte skupinu akcí, která má akci ITSM. Když se aktivuje výstraha, v nástroji ITSM se vytvoří nebo aktualizuje pracovní položka.

> [!NOTE]
>
> Informace o cenách ITSM akce najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.

> [!NOTE]
>
> Pole krátký popis v definici pravidla výstrahy je při odeslání pomocí akce ITSM omezeno na 40 znaků.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizujte a analyzujte data o incidentech a žádostech o změnu.

V závislosti na vaší konfiguraci při nastavování připojení může konektor ITSM synchronizovat až 120 dní a data žádosti o změnu. Schéma záznamu protokolu pro tato data je k dispozici v [následující části](#additional-information).

Data incidentu a žádosti o změnu lze vizuálně vylepšovat pomocí řídicího panelu konektoru ITSM v řešení.

![Snímek obrazovky zobrazující řídicí panel konektoru ITSM](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také poskytuje informace o stavu konektoru, který lze použít jako výchozí bod k analýze případných problémů s připojením.

Můžete také vizualizovat incidenty synchronizované s ovlivněnými počítači v rámci řešení Service Map.

Service Map automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Umožňuje zobrazit servery přirozeným způsobem – jako propojené systémy, které doručují důležité služby. Service Map ukazuje propojení mezi servery, procesy a porty v jakékoli architektuře propojené pomocí protokolu TCP a nevyžaduje přitom žádnou konfiguraci kromě instalace agenta. [Přečtěte si další informace](../insights/service-map.md).

Pokud používáte řešení Service Map, můžete zobrazit položky oddělení služeb vytvořené v řešeních ITSM, jak je znázorněno v následujícím příkladu:

![Log Analytics obrazovce](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Další informace: [Service map](../insights/service-map.md)


## <a name="additional-information"></a>Další informace

### <a name="data-synced-from-itsm-product"></a>Data synchronizovaná z produktu ITSM
Incidenty a žádosti o změnu se synchronizují z vašeho ITSM produktu do vašeho pracovního prostoru Log Analytics na základě konfigurace připojení.

Následující informace ukazují příklady dat shromažďovaných nástrojem ITSMC:

> [!NOTE]
>
> V závislosti na typu pracovní položky importovaném do Log Analytics **ServiceDesk_CL** obsahuje následující pole:

**Pracovní položka:** **incidenty**  
ServiceDeskWorkItemType_s = incident

**Pole**

- ServiceDeskConnectionName
- ID oddělení služeb
- Stav
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
- Stav
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
| IncidentState_s | Stav |
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
| CRState_s|  Stav|
| Urgency_s|  Naléhavost |
| Priority_s| Priorita|
| Risk_s| Riziko|
| Impact_s| Dopad|
| RequestedDate_t  | Požadováno podle data |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  |     Plánované počáteční datum |
| PlannedEndDate_t  |   Plánované koncové datum |
| WorkStartDate_t  | Skutečné datum zahájení |
| WorkEndDate_t | Skutečné datum ukončení|
| Description_s | Popis |
| Počítač  | Položka konfigurace |


## <a name="troubleshoot-itsm-connections"></a>Řešení potíží s připojením ITSM
1. Pokud se připojení nezdařilo z uživatelského rozhraní připojeného zdroje s **chybou při ukládání** zprávy o připojení, proveďte následující kroky:
   - Pro připojení ServiceNow, Cherwell a prov,  
   - Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé připojení.  
   - Zkontrolujte, jestli máte v odpovídajícím ITSM produktu dostatečná oprávnění pro vytvoření připojení.  
   - Pro Service Manager připojení,  
   - Zajistěte, aby byla webová aplikace úspěšně nasazena a vytvořilo se hybridní připojení. Pokud chcete ověřit, že se připojení úspěšně navázalo na místním Service Manager počítači, přejděte na adresu URL webové aplikace, jak je popsáno v dokumentaci pro vytvoření [hybridního připojení](./itsmc-connections.md#configure-the-hybrid-connection).  

2. Pokud se data z ServiceNow nesynchronizují Log Analytics, ujistěte se, že instance ServiceNow není v režimu spánku. Instance pro vývoj v ServiceNow se někdy po dlouhou dobu nečinným přechodem do režimu spánku. Jinak nahlaste problém.
3. Pokud se Log Analytics výstrahy aktivují, ale pracovní položky se nevytvoří v produktu ITSM nebo položky konfigurace nejsou vytvořené/propojené s pracovními položkami nebo pro jiné obecné informace, podívejte se do následujících umístění:
   -  ITSMC: řešení zobrazuje souhrn připojení/pracovních položek/počítačů atd. Klikněte na dlaždici se **stavem konektoru** , který vám umožní **Protokolovat hledání**  pomocí příslušného dotazu. Další informace najdete v záznamech protokolu s LogType_S chybě.
   - Stránka **prohledávání protokolu** : zobrazení chyb/souvisejících informací přímo pomocí `*` ServiceDeskLog_CL dotazů `*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Řešení potíží s nasazením Service Manager Web App
1.  V případě jakýchkoli problémů s nasazením webové aplikace se ujistěte, že máte dostatečná oprávnění v předplatném, zmíněné pro vytváření a nasazování prostředků.
2.  Pokud se při spuštění [skriptu](itsmc-service-manager-script.md)zobrazí chyba **"odkaz na objekt není nastaven na instanci objektu"** , ujistěte se, že jste zadali platné hodnoty v části **Konfigurace uživatele** .
3.  Pokud se vám nepodaří vytvořit obor názvů služby Service Bus Relay, zajistěte, aby byl v předplatném zaregistrován požadovaný poskytovatel prostředků. Pokud není zaregistrován, ručně vytvořte obor názvů služby Service Bus Relay z Azure Portal. Můžete ho také vytvořit při [vytváření hybridního připojení](./itsmc-connections.md#configure-the-hybrid-connection) z Azure Portal.


## <a name="contact-us"></a>Kontaktujte nás

Pokud máte jakékoli dotazy nebo připomínky ke konektoru pro IT služby IT, kontaktujte nás na adrese [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Další kroky
[Přidejte ITSM produkty/služby do konektoru správy služeb IT](./itsmc-connections.md).

