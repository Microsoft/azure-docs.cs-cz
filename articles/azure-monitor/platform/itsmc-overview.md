---
title: Konektor správy služeb IT v Azure Log Analytics | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled it service management konektoru (ITSMC) a informace o tom, jak používat toto řešení centrálně sledovat a spravovat pracovní položky ITSM v Azure Log Analytics a rychle vyřešit všechny problémy.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274538"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Připojení nástrojů Azure k ITSM pomocí konektoru pro správu služeb IT

![Symbol konektoru správy služeb IT](media/itsmc-overview/itsmc-symbol.png)

It Service Management Connector (ITSMC) umožňuje připojit Azure a podporovaný produkt/službu ITSM (IT Service).

Služby Azure, jako je Log Analytics a Azure Monitor, poskytují nástroje pro zjišťování, analýzu a řešení problémů s prostředky Azure i jiných zdrojů než Azure. Pracovní položky související s problémem se však obvykle nacházejí v produktu nebo službě ITSM. Konektor ITSM poskytuje obousměrné připojení mezi nástroji Azure a ITSM, které vám pomůže rychleji řešit problémy.

ITSMC podporuje připojení s následujícími nástroji ITSM:

-   ServiceNow
-   Správce služeb systémového centra
-   Provance
-   Cherwell

S ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě vašich výstrah Azure (upozornění na metriky, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete synchronizovat incident a změnit data žádosti z nástroje ITSM do pracovního prostoru Azure Log Analytics.

Přečtěte si více o [právních podmínkách a zásadách ochrany osobních údajů](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Konektor ITSM můžete začít používat pomocí následujících kroků:

1.  [Přidání řešení konektoru ITSM](#adding-the-it-service-management-connector-solution)
2.  Vytvoření připojení ITSM
3.  [Použití připojení](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Přidání řešení konektoru pro správu služeb IT

Před vytvořením připojení je třeba přidat řešení konektoru ITSM.

1. Na webu Azure Portal klikněte na **+ Nová** ikona.

   ![Nový prostředek Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Vyhledejte **konektor správy služeb IT** na webu Marketplace a klepněte na tlačítko **Vytvořit**.

   ![Přidání řešení ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. V části **OMS Workspace** vyberte pracovní prostor Azure Log Analytics, do kterého chcete nainstalovat řešení.
   >[!NOTE]
   > * V rámci probíhajícího přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se pracovní prostory OMS teď označují jako pracovní prostory Log Analytics.
   > * Konektor ITSM lze nainstalovat pouze do pracovních prostorů Log Analytics v následujících oblastech: Východní USA, Západní US2, Jižní Střed USA, Západní střed USA, Střední Kanada, Západní Evropa, Jižní Velká Británie, Jihovýchodní Asie, Východní Japonsko, Střední Indie, Jihovýchodní Austrálie.

4. V části **Nastavení pracovního prostoru OMS** vyberte skupinu prostředků, do které chcete vytvořit prostředek řešení.

   ![Pracovní prostor ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >V rámci probíhajícího přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se pracovní prostory OMS teď označují jako pracovní prostory Log Analytics.

5. Klikněte na **Vytvořit**.

Při nasazení prostředku řešení se zobrazí oznámení v pravém horním rohu okna.


## <a name="creating-an-itsm--connection"></a>Vytvoření připojení ITSM

Po instalaci řešení můžete vytvořit připojení.

Pro vytvoření připojení budete muset připravit nástroj ITSM, abyste povolili připojení z řešení KONEKTOR ITSM.  

V závislosti na produktu ITSM, ke kterému se připojujete, postupujte takto:

- [Správce služeb systémového centra (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Po přípravě nástrojů ITSM vytvořte připojení následujícím postupem:

1. Přejděte na **všechny prostředky**, vyhledejte **službu ServiceDesk (YourWorkspaceName).**
2. V části **Zdroje dat pracovního prostoru** v levém podokně klepněte na **položku Připojení ITSM**.
   ![Připojení ITSM](media/itsmc-overview/itsm-connections.png)

   Na této stránce se zobrazí seznam připojení.
3. Klepněte na tlačítko **Přidat připojení**.

   ![Přidání připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Zadejte nastavení připojení, jak je popsáno v [článku Konfigurace připojení ITSMC k produktům/službám ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Ve výchozím nastavení ITSMC aktualizuje konfigurační data připojení jednou za 24 hodin. Chcete-li data připojení okamžitě aktualizovat pro všechny úpravy nebo aktualizace šablon, které provedete, klikněte na tlačítko **Synchronizovat** v okně připojení.

   ![Aktualizace připojení](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Použití řešení
   Pomocí řešení KONEKTOR ITSM můžete vytvářet pracovní položky z výstrah Azure, výstrahy Log Analytics a protokolanalytics protokolu záznamů protokolu.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytvoření pracovních položek ITSM z výstrah Azure

Jakmile budete mít vytvořené připojení ITSM, můžete vytvořit pracovní položky v nástroji ITSM na základě výstrah Azure pomocí **akce ITSM** ve **skupinách akcí**.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob spouštění akcí pro výstrahy Azure. Skupiny akcí můžete použít s upozorněními na metriky, výstrahami protokolu aktivit a výstrahami Azure Log Analytics na webu Azure Portal.

Použijte následující postup:

1. Na webu Azure Portal klikněte na **Monitor**.
2. V levém podokně klikněte na **skupiny akcí**. Zobrazí se okno **Přidat skupinu akcí.**

    ![Skupiny akcí](media/itsmc-overview/action-groups.png)

3. Zadejte **název** a **zkrácený název** skupiny akcí. Vyberte **skupinu prostředků** a **předplatné,** kde chcete vytvořit skupinu akcí.

    ![Detail skupin akcí](media/itsmc-overview/action-groups-details.png)

4. V seznamu Akce vyberte **ITSM** z rozevírací nabídky **Pro typ akce**. Zadejte **název** akce a klepněte na tlačítko **Upravit podrobnosti**.
5. Vyberte **předplatné,** ve kterém se nachází pracovní prostor Log Analytics. Vyberte název **připojení** (název konektoru ITSM) následovaný názvem pracovního prostoru. Například "MyITSMMConnector(MyWorkspace)."

    ![Podrobnosti akce ITSM](media/itsmc-overview/itsm-action-details.png)

6. V rozevírací nabídce vyberte typ **pracovní položky.**
   Zvolte, zda chcete použít existující šablonu nebo vyplňte pole vyžadovaná produktem ITSM.
7. Klikněte na tlačítko **OK**.

Při vytváření nebo úpravách pravidla výstrahy Azure použijte skupinu akcí, která má akci ITSM. Když se aktivuje výstraha, pracovní položka je vytvořena nebo aktualizována v nástroji ITSM.

> [!NOTE]
>
> Informace o cenách akce ITSM naleznete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizace a analýza dat incidentu a změny požadavků

Na základě vaší konfigurace při nastavování připojení může konektor ITSM synchronizovat až 120 dní dat požadavku na incidenty a změnu. Schéma záznamu protokolu pro tato data je uvedeno v [další části](#additional-information).

Data požadavku na incident y a změny lze vizualizovat pomocí řídicího panelu konektoru ITSM v řešení.

![Obrazovka Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také poskytuje informace o stavu konektoru, který lze použít jako výchozí bod pro analýzu problémů s připojeními.

Můžete také vizualizovat incidenty synchronizované s ovlivněné počítače v rámci řešení mapa služeb.

Mapa služeb automaticky zjišťuje součásti aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Umožňuje zobrazit servery přirozeným způsobem – jako propojené systémy, které doručují důležité služby. Service Map ukazuje propojení mezi servery, procesy a porty v jakékoli architektuře propojené pomocí protokolu TCP a nevyžaduje přitom žádnou konfiguraci kromě instalace agenta. [Další informace](../../azure-monitor/insights/service-map.md).

Pokud používáte řešení Mapa služeb, můžete zobrazit položky servisního oddělení vytvořené v řešeních ITSM, jak je znázorněno v následujícím příkladu:

![Obrazovka Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Více informací: [Mapa služeb](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Další informace

### <a name="data-synced-from-itsm-product"></a>Data synchronizovaná z produktu ITSM
Incidenty a žádosti o změnu se synchronizují z vašeho produktu ITSM do pracovního prostoru Log Analytics na základě konfigurace připojení.

Následující informace ukazují příklady údajů shromážděných ITSMC:

> [!NOTE]
>
> V závislosti na typu pracovní položky importované do analýzy protokolů **obsahuje ServiceDesk_CL** následující pole:

**Pracovní položka:** **Incidenty**  
ServiceDeskWorkItemType_s="Incident"

**Pole**

- Název_připojení služby ServiceDeskConnectionName
- ID servisní přepážky
- Stav
- Naléhavost
- Dopad
- Priorita
- Eskalace
- Autor
- Vyřešeno
- Uzavřeno
- Zdroj
- Přiřazeno
- Kategorie
- Nadpis
- Popis
- Datum vytvoření
- Datum uzavření
- Datum vyřešení
- Datum poslední změny
- Počítač


**Pracovní položka:** **Žádosti o změnu**

ServiceDeskWorkItemType_s="Žádost o změnu"

**Pole**
- Název_připojení služby ServiceDeskConnectionName
- ID servisní přepážky
- Autor
- Uzavřeno
- Zdroj
- Přiřazeno
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
- Přiřazeno
- Datum vytvoření
- Datum uzavření
- Datum poslední změny
- Požadované datum
- Plánované datum zahájení
- Plánované koncové datum
- Počáteční datum práce
- Koncové datum práce
- Popis
- Počítač

## <a name="output-data-for-a-servicenow-incident"></a>Výstupní data pro incident ServiceNow

| Pole Log Analytics | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| IncidentState_s | Stav |
| Urgency_s |Naléhavost |
| Impact_s |Dopad|
| Priority_s | Priorita |
| CreatedBy_s | Otevřel |
| ResolvedBy_s | Vyřešeno|
| ClosedBy_s  | Uzavřeno |
| Source_s| Typ kontaktu |
| AssignedTo_s | Přiřazeno  |
| Category_s | Kategorie |
| Title_s|  Stručný popis |
| Description_s|  Poznámky |
| CreatedDate_t|  Otevřít |
| ClosedDate_t| closed|
| ResolvedDate_t|Vyřešeno|
| Počítač  | Položka konfigurace |

## <a name="output-data-for-a-servicenow-change-request"></a>Výstupní data pro požadavek na změnu ServiceNow

| Log Analytics | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| CreatedBy_s | Žádost o žádost |
| ClosedBy_s | Uzavřeno |
| AssignedTo_s | Přiřazeno  |
| Title_s|  Stručný popis |
| Type_s|  Typ |
| Category_s|  Kategorie |
| CRState_s|  Stav|
| Urgency_s|  Naléhavost |
| Priority_s| Priorita|
| Risk_s| Riziko|
| Impact_s| Dopad|
| RequestedDate_t  | Požadováno podle data |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  |     Plánované datum zahájení |
| PlannedEndDate_t  |   Plánované koncové datum |
| WorkStartDate_t  | Skutečné počáteční datum |
| WorkEndDate_t | Skutečné koncové datum|
| Description_s | Popis |
| Počítač  | Položka konfigurace |


## <a name="troubleshoot-itsm-connections"></a>Poradce při potížích s připojením ITSM
1. Pokud se připojení nezdaří z rozhraní připojeného zdroje s **chybou při ukládání** zprávy o připojení, postupujte takto:
   - Pro servicenow, Cherwell a provance připojení,  
   - ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé z připojení.  
   - zkontrolujte, zda máte v příslušném produktu ITSM dostatečná oprávnění k nastojepřipojení.  
   - U připojení správce služeb  
   - ujistěte se, že webová aplikace je úspěšně nasazena a hybridní připojení je vytvořen. Chcete-li ověřit, zda je připojení úspěšně navázáno s místním počítačem Správce služeb, navštivte adresu URL webové aplikace, jak je podrobně popsáno v dokumentaci k vytvoření [hybridního připojení](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Pokud se data z ServiceNow nesynchronizují s analýzou protokolů, ujistěte se, že instance ServiceNow není v režimu spánku. ServiceNow Dev Instance někdy přejít do režimu spánku při nečinnosti po dlouhou dobu. Jinak to nahlaste.
3. Pokud Log Analytics výstrahy požáru, ale pracovní položky nejsou vytvořeny v produktu ITSM nebo položky konfigurace nejsou vytvořeny nebo propojeny s pracovní položky nebo pro jiné obecné informace, podívejte se na následujících místech:
   -  ITSMC: Řešení zobrazuje souhrn připojení / pracovních položek / počítačů atd. Klikněte na dlaždici se **stavem konektoru**, která vás přenese k **přihlášení k vyhledávání** pomocí příslušného dotazu. Další informace naleznete v záznamech protokolu s LogType_S jako chyba.
   - **Log Search** stránka: zobrazit chyby / `*`související`*`informace přímo pomocí dotazu ServiceDeskLog_CL .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Poradce při potížích s nasazením webovéaplikace Správce služeb
1.  V případě jakýchkoli problémů s nasazením webové aplikace se ujistěte, že máte dostatečná oprávnění v předplatném uvedeném k vytvoření nebo nasazení prostředků.
2.  Pokud se při spuštění [skriptu](itsmc-service-manager-script.md)zobrazí chyba **"Odkaz na objekt není nastaven na instanci objektu "** , ujistěte se, že jste v části **Konfigurace uživatele** zadali platné hodnoty.
3.  Pokud se vám nepodaří vytvořit obor názvů přenosu sběrnice služby, ujistěte se, že je v předplatném zaregistrován požadovaný poskytovatel prostředků. Pokud nejste zaregistrováni, ručně vytvořte obor názvů přenosu služby service bus z portálu Azure. Můžete také vytvořit při [vytváření hybridní připojení](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) z portálu Azure.


## <a name="contact-us"></a>Kontaktujte nás

Pro případné dotazy nebo zpětnou vazbu na [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com)konektor správy služeb IT nás kontaktujte na adrese .

## <a name="next-steps"></a>Další kroky
[Přidání produktů/služeb ITSM do konektoru pro správu služeb IT](../../azure-monitor/platform/itsmc-connections.md).
