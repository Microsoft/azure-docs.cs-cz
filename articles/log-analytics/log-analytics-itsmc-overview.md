---
title: IT služby konektoru Management v Azure Log Analytics | Microsoft Docs
description: Tento článek obsahuje přehled IT služby správy konektoru (ITSMC) a informace o tom, jak používat toto řešení centrálně monitorovat a spravovat ITSM pracovních položek v Azure Log Analytics a rychle vyřešit všechny problémy.
services: log-analytics
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: a51b96641c80a6575c64f58fa09e0bf6078e4185
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Připojení Azure k nástrojům ITSM pomocí konektoru služby správy IT

![Symbol konektoru služby správy IT](./media/log-analytics-itsmc/itsmc-symbol.png)

Konektor pro správu služby IT (ITSMC) umožňuje připojit Azure a podporovaných produktu nebo služby IT služby správy (ITSM).

Azure services jako analýzy protokolů a monitorování Azure poskytuje nástroje ke zjišťování, analýze a řešení problémů s Azure a prostředky mimo Azure. Pracovní položky související s problém obvykle však nacházet v produktu nebo službě ITSM. Konektor ITSM poskytuje obousměrné připojení mezi Azure a ITSM nástroje k řešení problémů rychlejší.

ITSMC podporuje připojení s následující ITSM nástroje:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

S ITSMC můžete

-  Vytváření pracovních položek v nástroji ITSM, na základě vaší Azure výstrah (metriky výstrahy, protokol aktivit výstrahy a upozornění analýzy protokolů).
-  Volitelně můžete synchronizovat vašeho incidentu a změnit data žádosti z vaší ITSM nástroj k pracovnímu prostoru analýzy protokolů Azure.


Můžete začít používat konektor ITSM pomocí následujících kroků:

1.  [Přidat řešení ITSM konektoru](#adding-the-it-service-management-connector-solution)
2.  [Vytvoření připojení k ITSM](#creating-an-itsm-connection)
3.  [Používat připojení](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Přidání IT řešení pro správu konektoru služby

Před vytvořením připojení, je nutné přidat řešení ITSM konektor.

1.  Na portálu Azure, klikněte na tlačítko **+ nový** ikonu.

    ![Azure nový prostředek](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Vyhledejte **IT Service Management Connector** Marketplace a klikněte na **vytvořit**.

    ![Přidat ITSMC řešení](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  V **pracovním prostorem OMS** vyberte pracovní prostor analýzy protokolů Azure, kam chcete nainstalovat řešení.
4.  V **nastavení pracovního prostoru OMS** vyberte ResourceGroup, kde chcete vytvořit prostředek řešení.

    ![Pracovní prostor ITSMC](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  Klikněte na možnost **Vytvořit**.

Po nasazení řešení prostředků oznámení se zobrazí v horní pravé části okna.


## <a name="creating-an-itsm--connection"></a>Vytvoření připojení k ITSM

Po instalaci řešení, můžete vytvořit připojení.

Pro vytvoření připojení, musíte připravená data vaše ITSM nástroje umožňující připojení z řešení ITSM konektor.  

V závislosti na produkt ITSM, ke kterému se připojujete použijte následující postup:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Jakmile máte prepped vaše ITSM nástrojů, použijte následující postup k vytvoření připojení:

1.  Přejděte na **všechny prostředky**, vyhledejte **ServiceDesk(YourWorkspaceName)**.
2.  V části **zdroje dat pracovního prostoru** v levém podokně klikněte na **ITSM připojení**.
    ![ITSM připojení](./media/log-analytics-itsmc/itsm-connections.png)

    Tato stránka zobrazuje seznam připojení.
3.  Klikněte na tlačítko **přidat připojení**.

    ![Přidat připojení ITSM](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Zadejte nastavení připojení, jak je popsáno v [konfiguraci připojení ITSMC s váš článek produktů nebo služeb ITSM](log-analytics-itsmc-connections.md).

    > [!NOTE]

    > Ve výchozím nastavení aktualizuje ITSMC připojení konfigurační data jednou za každých 24 hodin. Aktualizovat připojení k data okamžitě pro úpravy nebo aktualizace šablony, které provedete, klikněte na tlačítko "Aktualizace" zobrazí vedle připojení.

    ![Aktualizace připojení](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Použití řešení
   Pomocí konektoru ITSM řešení můžete vytvořit pracovní položky z Azure výstrahy, analýzy protokolů výstrahy a analýzy protokolů záznamy protokolu.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytváření pracovních položek ITSM z Azure výstrah

Až budete mít ITSM připojení vytvořit, můžete vytvořit pracovní položky (položek) v nástroji vaší ITSM podle Azure výstrahy, pomocí **ITSM akce** v **skupiny akcí**.

Akce skupiny poskytují modulární a opakovaně použitelné způsob spouštění akcí pro upozornění Azure. Můžete vytvořit skupiny akcí s metriky výstrahy, protokol aktivit výstrahy a upozornění Azure Log Analytics na portálu Azure.

Použijte následující postup:

1. Na portálu Azure, klikněte na tlačítko **monitorování**.
2. V levém podokně klikněte na **skupiny akcí**. **Přidat skupinu akce** se zobrazí v okně.

    ![Skupiny akcí](media/log-analytics-itsmc/action-groups.png)

3. Zadejte **název** a **ShortName** pro skupinu pro akce. Vyberte **skupiny prostředků** a **předplatné** kde chcete vytvořit skupině Akce.

    ![Podrobnosti skupiny akce](media/log-analytics-itsmc/action-groups-details.png)

4. Vyberte v seznamu akcí **ITSM** z rozevírací nabídky pro **typ akce**. Zadejte **název** pro akci a klikněte na tlačítko **upravit podrobnosti**.
5. Vyberte **předplatné** kde je umístěn pracovní prostor analýzy protokolů. Vyberte **připojení** name (název vašeho konektoru ITSM), za nímž následuje název pracovního prostoru. Například "MyITSMMConnector(MyWorkspace)".

    ![Podrobnosti o ITSM akce](./media/log-analytics-itsmc/itsm-action-details.png)

6. Vyberte **pracovní položka** typu z rozevírací nabídky.
   Vyberte existující šablony nebo vyplnit pole, která vyžaduje vaše ITSM produktu.
7. Klikněte na **OK**.

Při vytváření nebo úpravách Azure pravidla výstrahy, použijte skupinu akce, který má ITSM akce. Když se aktivuje výstraha, pracovní položky je vytvořit nebo aktualizovat v nástroj ITSM.

>[!NOTE]

> Informace o cenách ITSM akce najdete v tématu [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro akce skupiny.


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>Vytváření pracovních položek ITSM z výstrah analýzy protokolů

Pravidla výstrah můžete nakonfigurovat na portálu Azure Log Analytics k vytváření pracovních položek v nástroji ITSM, pomocí následujícího postupu.

1. Z **hledání protokolů** okně Spustit protokolu vyhledávací dotaz. Chcete-li zobrazit data. Výsledky dotazu jsou zdroj pro pracovní položky.
2. V **hledání protokolů**, klikněte na tlačítko **výstrahy** otevřete **přidat pravidlo výstrahy** stránky.

    ![Obrazovka analýzy protokolů](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. Na **přidat pravidlo výstrahy** okno, zadejte požadované podrobnosti pro **název**, **závažnost**, **vyhledávací dotaz**, a **výstrah kritéria** (časová okna/metrika měří období).
4. Vyberte **Ano** pro **ITSM akce**.
5. Vyberte ITSM připojení z **vyberte připojení** seznamu.
6. Zadejte podrobnosti podle potřeby.
7. Vytvořit samostatný pracovní položku pro každé položky protokolu této výstrahy, vyberte **vytvořit jednotlivé pracovní položky pro každý záznam protokolu** zaškrtávací políčko.

    Nebo

    nechte toto políčko Zrušit vytvořit pouze jeden pracovní položku pro libovolný počet záznamů protokolu v rámci této výstrahy.

7. Klikněte na **Uložit**.

Můžete zobrazit výstrahy analýzy protokolů, který jste vytvořili v části **Nastavení > výstrahy**. Pracovní položky odpovídající ITSM připojení vytvářejí, když je splněna podmínka zadaný výstrahy.


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>Vytváření pracovních položek ITSM ze záznamů protokolu analýzy protokolů

Můžete také vytvořit pracovní položky v připojených zdrojů ITSM přímo z záznam protokolu. To slouží k otestování, pokud připojení funguje správně.


1. Z **hledání protokolů**hledání požadovaných dat, vyberte podrobností a klikněte na tlačítko **vytvořit pracovní položka**.

    **Vytvoření pracovní položky ITSM** zobrazí se okno:

    ![Obrazovka analýzy protokolů](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   Přidejte následující podrobnosti:

  - **Název pracovní položky**: název pro pracovní položku.
  - **Pracovní položky Popis**: popis novou pracovní položku.
  - **Vliv na počítače**: název počítače, kde tato data protokolu byla nalezena.
  - **Vyberte připojení**: ITSM připojení, ve kterém chcete vytvořit tuto pracovní položku.
  - **Pracovní položka**: typ pracovní položky.

3. Chcete-li použít existující šablonu pracovní položky pro incidentu, klikněte na tlačítko **Ano** pod **generovat pracovní položka založený na šabloně** a potom klikněte na **vytvořit**.

    Nebo:

    Klikněte na tlačítko **ne** Pokud chcete zadat vlastní hodnoty.

4. Zadejte odpovídající hodnoty v **typu Kontakt**, **dopad**, **naléhavost**, **kategorie**, a **dílčí kategorie** textová pole a pak klikněte na tlačítko **vytvořit**.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizace a analyzovat incidentu a změnit data požadavku

V závislosti na vaší konfiguraci při nastavování připojení ITSM konektor synchronizovat až 120 dní incidentů a změn dat požadavku. Je součástí schéma záznam protokolu pro tato data [další části](#additional-information).

Pomocí řídicího panelu ITSM konektor v řešení můžete vizualizovat data požadavku incidentů a změn.

![Obrazovka analýzy protokolů](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Řídicí panel obsahuje také informace o stav konektoru, který můžete použít jako východisko analyzovat problémy s připojení.

Také můžete vizualizovat incidenty synchronizovat proti ovlivněné počítače, v rámci řešení mapy služeb.

Mapa služeb automaticky vyhledá součásti aplikace v systémech Windows a Linux a mapuje komunikace mezi službami. Umožňuje zobrazit vaše servery co možná z nich – jako vzájemně propojena systémy, které doručují důležité služby. Mapy služeb zobrazí připojení mezi servery, procesy, a vyžaduje porty mezi žádné připojení TCP architektura žádnou konfiguraci, jiné než instalace agenta. [Další informace](../operations-management-suite/operations-management-suite-service-map.md).

Pokud používáte řešení mapy služeb, můžete zobrazit položky služby podpory, které jsou vytvořené v ITSM řešení, jak je znázorněno v následujícím příkladu:

![Obrazovka analýzy protokolů](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

Další informace: [mapy služeb](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>Další informace

### <a name="data-synced-from-itsm-product"></a>Synchronizované z produktu ITSM dat
Incidenty a žádosti o změnu jsou synchronizované z vašeho produktu ITSM do pracovního prostoru analýzy protokolů na základě konfigurace připojení.

Tyto informace jsou uvedeny příklady podle dat shromažďovaných funkcí ITSMC:

> [!NOTE]

> V závislosti na typu pracovní položky naimportovat do analýzy protokolů **ServiceDesk_CL** obsahuje následující pole:

**Pracovní položka:** **incidenty**  
ServiceDeskWorkItemType_s="Incident"

**Pole**

- ServiceDeskConnectionName
- ID služby podpory
- Stav
- Naléhavosti
- Dopad
- Priorita
- Eskalace
- Vytvořil(a)
- Vyřešil
- Uzavřené
- Zdroj
- Přiřazené k
- Kategorie
- Nadpis
- Popis
- Datum vytvoření
- Datum uzavření
- Datum vyřešení
- Datum poslední změny
- Počítač


**Pracovní položka:** **žádosti o změnu**

ServiceDeskWorkItemType_s="ChangeRequest"

**Pole**
- ServiceDeskConnectionName
- ID služby podpory
- Vytvořil(a)
- Uzavřené
- Zdroj
- Přiřazené k
- Nadpis
- Typ
- Kategorie
- Stav
- Eskalace
- Konflikt stavu
- Naléhavosti
- Priorita
- Riziko
- Dopad
- Přiřazené k
- Datum vytvoření
- Datum uzavření
- Datum poslední změny
- Požadovaná data
- Plánované počáteční datum
- Naplánované koncové datum
- Pracovní počáteční datum
- Pracovní koncové datum
- Popis
- Počítač

## <a name="output-data-for-a-servicenow-incident"></a>Výstupní data pro ServiceNow incident

| Pole analýzy protokolů | ServiceNow pole |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| IncidentState_s | Stav |
| Urgency_s |Naléhavosti |
| Impact_s |Dopad|
| Priority_s | Priorita |
| CreatedBy_s | Otevřít |
| ResolvedBy_s | Vyřešil(a)|
| ClosedBy_s  | Uzavřené |
| Source_s| Obraťte se na typ |
| AssignedTo_s | Přiřazeno  |
| Category_s | Kategorie |
| Title_s|  Krátký popis |
| Description_s|  Poznámky |
| CreatedDate_t|  Opened |
| ClosedDate_t| zavřené|
| ResolvedDate_t|Vyřešené|
| Počítač  | položky konfigurace |

## <a name="output-data-for-a-servicenow-change-request"></a>Výstupní data pro ServiceNow žádost o změnu

| Log Analytics | ServieNow pole |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| CreatedBy_s | Žadatel |
| ClosedBy_s | Uzavřené |
| AssignedTo_s | Přiřazeno  |
| Title_s|  Krátký popis |
| Type_s|  Typ |
| Category_s|  Kategorie |
| CRState_s|  Stav|
| Urgency_s|  Naléhavosti |
| Priority_s| Priorita|
| Risk_s| Riziko|
| Impact_s| Dopad|
| RequestedDate_t  | Požadovaná data |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  |     Plánované počáteční datum |
| PlannedEndDate_t  |   Plánované koncové datum |
| WorkStartDate_t  | Skutečné počáteční datum |
| WorkEndDate_t | Skutečné koncové datum|
| Description_s | Popis |
| Počítač  | Položky konfigurace |


## <a name="troubleshoot-itsm-connections"></a>Řešení potíží s ITSM připojení
1.  Pokud připojení selže z uživatelského rozhraní připojené zdroje s **Chyba při ukládání připojení** zpráva, proveďte následující kroky:
- Pro připojení ServiceNow, Cherwell a Provance  
           - Zkontrolujte správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro jednotlivá připojení.  
           - Zkontrolujte, pokud máte dostatečná oprávnění v rámci odpovídající ITSM produktu pro připojení.  
- U připojení k portálu Service Manager  
           - Zajistěte, aby webová aplikace je úspěšně nasazen a hybridní připojení se vytvoří. Ověřte připojení se úspěšně naváže na místní počítač portálu Service Manager, najdete na adresu URL webové aplikace podle popisu v dokumentaci k provádění [hybridní připojení](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Pokud není získávání synchronizovat data z ServiceNow k analýze protokolů, zajistěte, aby ServiceNow instance není pozastaveno. Instance ServiceNow Dev někdy přejděte do režimu spánku při nečinnosti, po dlouhou dobu. Jinak ohlaste daný problém.
3.  Pokud OMS výstrahy fire, ale pracovní položky nejsou vytvořeny v produktu ITSM nebo položek konfigurace nejsou vytvořen nebo propojené pracovní položky nebo další obecné informace, podívejte se na těchto místech:
 -  ITSMC: Řešení zobrazuje souhrnné údaje o připojení nebo pracovní položky nebo počítače atd. Klikněte na dlaždici zobrazující **stav konektoru**, což trvá, abyste **hledání protokolů** s relevantní dotazu. Podívejte se na záznamy protokolu s LogType_S jako chyba. Další informace.
 - **Protokolu vyhledávání** stránky: zobrazení chyb/souvisejících informací o přímo pomocí dotazu *typ = ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Řešení potíží s nasazení aplikace webového portálu Service Manager
1.  V případě jakékoliv problémy s nasazení webové aplikace zkontrolujte, zda že máte dostatečná oprávnění v rámci předplatného uvedených vytvořit nebo nasadit prostředky.
2.  Pokud dojde **"Objektu odkaz není nastaven na instanci objektu"** Chyba při spuštění [skriptu](log-analytics-itsmc-service-manager-script.md), zkontrolujte, zda jste zadali platné hodnoty v části **konfigurace uživatele** části .
3.  Pokud se vám nepodaří vytvoření oboru názvů service bus relay, ujistěte se, že zprostředkovatel požadovaný prostředek je zaregistrován v rámci předplatného. Pokud není zaregistrovaný, ručně vytvořte obor názvů předávání sběrnice z portálu Azure. Můžete také vytvořit ji při [vytvoření hybridního připojení](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) z portálu Azure.


## <a name="contact-us"></a>Kontaktujte nás

Pro dotazy nebo zpětnou vazbu na správu konektoru služby IT, kontaktujte nás na adrese [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Další postup
[Přidání ITSM produktů nebo služeb do konektoru služby správy IT](log-analytics-itsmc-connections.md).
