---
title: IT Service Management Connector v Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje přehled IT Service Management Connector (ITSMC) a informace o tom, jak toto řešení používat ke centrální monitorování a správu nástrojem ITSM pracovní položky ve službě Azure Log Analytics a vyřešte všechny problémy rychle.
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.component: ''
ms.openlocfilehash: 7227fd68f696e827a076005bcd116e222f23e9b0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882516"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Připojení Azure k nástrojům ITSM pomocí IT Service Management Connector

![IT Service Management Connector symbol](media/itsmc-overview/itsmc-symbol.png)

Na IT Service Management Connector (ITSMC) umožňuje připojit Azure a podporovaných produkt nebo službu správy služeb IT (ITSM).

Služby Azure Log Analytics a Azure Monitor poskytuje nástroje pro rozpoznávání, analýza a řešení potíží s Azure a prostředků mimo Azure. Pracovní položky, obvykle problém souvisí však být umístěn v produktu ITSM nebo služby. Konektor ITSM poskytuje obousměrné připojení mezi Azure a ITSM nástroje, které vám pomohou vyřešit problémy rychleji.

ITSMC podporuje připojení k nástroji ITSM následující:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

ITSMC vám umožní

-  Vytvoření pracovní položky v nástroji ITSM, založené na Azure upozornění (upozornění na metriky, výstrahy protokolu aktivit a upozornění Log Analytics).
-  Volitelně můžete synchronizovat incident a změnit data žádosti z vašeho nástroje ITSM do pracovního prostoru Azure Log Analytics.


Můžete začít používat konektor ITSM pomocí následujících kroků:

1.  [Přidat řešení ITSM konektor](#adding-the-it-service-management-connector-solution)
2.  [Vytvořte připojení ITSM](#creating-an-itsm-connection)
3.  [Použití připojení](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Přidání IT řešení pro správu konektoru služby

Než vytvoříte připojení, budete muset přidat řešení ITSM konektor.

1.  Na webu Azure portal, klikněte na tlačítko **+ nová** ikonu.

    ![Nový prostředek Azure](media/itsmc-overview/azure-add-new-resource.png)

2.  Vyhledejte **IT Service Management Connector** na webu Marketplace a klikněte na **vytvořit**.

    ![Přidat ITSMC řešení](media/itsmc-overview/add-itsmc-solution.png)

3.  V **pracovní prostor OMS** vyberte pracovní prostor Azure Log Analytics, ve kterém chcete nainstalovat řešení.
   >[!NOTE]
   >Jako součást probíhající přechod z Microsoft Operations Management Suite (OMS) do Azure monitoru pracovními prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics.
4.  V **nastavení pracovního prostoru OMS** vyberte skupiny prostředků, ve kterém chcete vytvořit řešení prostředků.

    ![Pracovní prostor ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
    >[!NOTE]
    >Jako součást probíhající přechod z Microsoft Operations Management Suite (OMS) do Azure monitoru pracovními prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics.

5.  Klikněte na možnost **Vytvořit**.

Po nasazení řešení prostředků oznámení se zobrazí v horní části napravo od okna.


## <a name="creating-an-itsm--connection"></a>Vytváří se připojení ITSM

Po instalaci řešení, můžete vytvořit připojení.

Pro vytvoření připojení, musíte povolit připojení z řešení ITSM konektor ITSM nástroj pro přípravu.  

V závislosti na produktu ITSM, ke kterému se připojujete použijte následující postup:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Jakmile budete mít připravenou na to nástrojům ITSM, postupujte podle pokynů můžete vytvořit připojení:

1.  Přejděte na **všechny prostředky**, vyhledejte **ServiceDesk(YourWorkspaceName)**.
2.  V části **zdroje dat pracovního prostoru** v levém podokně klikněte na tlačítko **připojení ITSM**.
    ![Připojení ITSM](media/itsmc-overview/itsm-connections.png)

    Tato stránka zobrazuje seznam připojení.
3.  Klikněte na tlačítko **přidat připojení**.

    ![Přidat připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4.  Zadejte nastavení připojení, jak je popsáno v [konfiguraci připojení ITSMC vašich produktů nebo služeb Team Foundation článek ITSM](../../azure-monitor/platform/itsmc-connections.md).

    > [!NOTE]

    > Ve výchozím nastavení aktualizuje ITSMC připojení konfigurační data jednou za každých 24 hodin. Pokud chcete aktualizovat data vašeho připojení okamžitě pro úpravy nebo šablony aktualizace, které provedete, klikněte na tlačítko **synchronizace** tlačítka v okně připojení k.

    ![Aktualizace připojení](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Použití řešení
   Díky využití řešení ITSM konektor, můžete vytvořit pracovní položky z upozornění Log Analytics a Log Analytics protokolovat záznamy upozornění v Azure.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytvoření pracovní položky ITSM z výstrah Azure

Jakmile se připojení ITSM vytvořené, můžete vytvořit pracovní položky v nástroji ITSM založené na upozornění Azure pomocí **akce ITSM** v **skupiny akcí**.

Skupiny akcí poskytují modulární a opakovaně použitelné způsob aktivace akce pro Azure Alerts. Použití skupin akcí s upozornění metrik, upozornění protokolu aktivit a upozornění Azure Log Analytics na webu Azure portal.

Použijte následující postup:

1. Na webu Azure portal, klikněte na tlačítko **monitorování**.
2. V levém podokně klikněte na tlačítko **skupiny akcí**. **Přidat skupinu akcí** zobrazí se okno.

    ![Skupiny akcí](media/itsmc-overview/action-groups.png)

3. Zadejte **název** a **ShortName** pro vaši skupinu pro akce. Vyberte **skupiny prostředků** a **předplatné** ve které chcete vytvořit vaši skupinu akcí.

    ![Podrobnosti skupiny akcí](media/itsmc-overview/action-groups-details.png)

4. V seznamu akcí vyberte **ITSM** z rozevírací nabídky pro **typ akce**. Zadejte **název** akce a klikněte na **upravit podrobnosti**.
5. Vyberte **předplatné** kde se nachází váš pracovní prostor Log Analytics. Vyberte **připojení** name (název vašeho konektoru ITSM), za nímž následuje název pracovního prostoru. Například "MyITSMMConnector(MyWorkspace)."

    ![Podrobnosti akce ITSM](media/itsmc-overview/itsm-action-details.png)

6. Vyberte **pracovní položku** typu z rozevírací nabídky.
   Rozhodnete použít existující šablonu nebo vyplnit pole vyžadují produktu ITSM.
7. Klikněte na **OK**.

Při vytváření a úpravy pravidlo upozornění Azure, použijte skupinu akcí, která má akce ITSM. Když se aktivuje upozornění, pracovní položka je v nástroji ITSM vytvořené nebo aktualizované.

>[!NOTE]

> Informace o cenách akce ITSM, najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizovat a analyzovat incident a žádost o data změny

Na základě vaší konfigurace při nastavování připojení ITSM konektor synchronizovat až 120 dnů incidentů a změn dat požadavku. Schéma záznam protokolu pro tato data je součástí [další části](#additional-information).

Data žádosti incidentů a změn lze vizualizovat na řídicím panelu ITSM konektor v řešení.

![Log Analytics obrazovky](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také obsahuje informace o stav konektoru, který může sloužit jako výchozí bod k analýze problémů s připojeními.

Můžete také vizualizovat incidenty synchronizované proti ovlivněné počítače v rámci řešení Service Map.

Řešení Service Map automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi službami. Umožňuje zobrazit servery přirozeným způsobem – jako propojené systémy, které doručují důležité služby. Service Map ukazuje propojení mezi servery, procesy a porty v jakékoli architektuře propojené TCP bez konfigurace vyžaduje jiné než instalace agenta. [Další informace](../../azure-monitor/insights/service-map.md).

Pokud používáte řešení Service Map, můžete zobrazit položek oddělení služeb vytvořené v řešení ITSM, jak je znázorněno v následujícím příkladu:

![Log Analytics obrazovky](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Další informace: [Service Map](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Další informace

### <a name="data-synced-from-itsm-product"></a>Data, které jsou synchronizované z produktu ITSM.
Incidenty a žádosti o změnu jsou synchronizované z vašeho produktu ITSM. do pracovního prostoru Log Analytics na základě konfigurace tohoto připojení.

Tyto informace jsou uvedeny příklady dat shromažďovaných funkcí ITSMC:

> [!NOTE]

> V závislosti na typu pracovní položky importovat do Log Analytics, **ServiceDesk_CL** obsahuje následující pole:

**Pracovní položka:** **incidenty**  
ServiceDeskWorkItemType_s="Incident"

**Pole**

- ServiceDeskConnectionName
- ID oddělení služeb
- Stav
- Naléhavost
- Dopad
- Priorita
- Eskalace
- Vytvořil
- Vyřešil
- Uzavřel
- Zdroj
- Přiřazené k
- Kategorie
- Titul
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
- ID oddělení služeb
- Vytvořil
- Uzavřel
- Zdroj
- Přiřazené k
- Titul
- Typ
- Kategorie
- Stav
- Eskalace
- Konflikt stavu
- Naléhavost
- Priorita
- Riziko
- Dopad
- Přiřazené k
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

## <a name="output-data-for-a-servicenow-incident"></a>Výstupní data pro ServiceNow incident

| Log Analytics pole | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| IncidentState_s | Stav |
| Urgency_s |Naléhavost |
| Impact_s |Dopad|
| Priority_s | Priorita |
| CreatedBy_s | Otevřít v |
| ResolvedBy_s | Vyřešil(a)|
| ClosedBy_s  | Uzavřel |
| Source_s| Typ kontaktu |
| AssignedTo_s | Přiřazeno  |
| Category_s | Kategorie |
| Title_s|  Krátký popis |
| Description_s|  Poznámky |
| CreatedDate_t|  Otevřeno |
| ClosedDate_t| Uzavřeno|
| ResolvedDate_t|Vyřešené|
| Počítač  | Položky konfigurace |

## <a name="output-data-for-a-servicenow-change-request"></a>Žádost o změnu výstupní data pro ServiceNow

| Log Analytics | ServieNow pole |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| CreatedBy_s | Žadatel |
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
| RequestedDate_t  | Požadoval datum |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  |     Plánované počáteční datum |
| PlannedEndDate_t  |   Plánované koncové datum |
| WorkStartDate_t  | Skutečné počáteční datum |
| WorkEndDate_t | Skutečné koncové datum|
| Description_s | Popis |
| Počítač  | Položky konfigurace |


## <a name="troubleshoot-itsm-connections"></a>Řešení potíží s připojení ITSM
1.  Pokud se nepovede připojit z uživatelského rozhraní připojený zdroj pomocí **Chyba při ukládání připojení** zpráva, proveďte následující kroky:
 - Připojení ServiceNow a Provance a Cherwell  
    - Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný kód klienta pro každé připojení.  
    - zaškrtněte, pokud máte dostatečná oprávnění v odpovídající produktu ITSM. k vytvoření připojení.  
 - U připojení k portálu Service Manager  
    - Zajistěte, aby byla úspěšně nasazena aplikace, webové a hybridní připojení je vytvořené. K ověření, připojení se úspěšně naváže s počítačem v místním prostředí portálu Service Manager, navštivte adresu URL webové aplikace podle popisu v dokumentaci k provádění [hybridní připojení](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2.  Pokud se data z ServiceNow získávání nesynchronizuje do Log Analytics, ujistěte se, že ServiceNow instance není pozastaveno. Instance ServiceNow Dev někdy přechází do režimu spánku při nečinnosti dlouhou dobu. Jinak ohlaste ho.
3.  Pokud se aktivuje upozornění Log Analytics ale fungovat položky nejsou vytvářena v produktu ITSM. nebo položky konfigurace nejsou vytvořeny nebo propojených pracovních položek nebo další obecné informace, podívejte se na následujících místech:
 -  ITSMC: Řešení zobrazuje souhrn připojení/pracovní položky/počítače atd. Klikněte na dlaždici zobrazující **stav konektoru**, což vás přesměruje na **prohledávání protokolů** s odpovídající dotaz. Podívejte se na záznamy protokolu s LogType_S jako chyba. Další informace.
 - **Prohledávání protokolů** stránky: Zobrazit chyby a související informace přímo pomocí dotazu `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Řešení potíží s nasazení portálu Service Manager webových aplikací
1.  V případě jakýchkoli problémů s nasazení webové aplikace Ujistěte se, že máte dostatečná oprávnění v rámci předplatného uvedených k vytvoření a nasazení prostředků.
2.  Pokud dojde **"Objekt odkaz není nastaven na instanci objektu"** Chyba při spuštění [skript](itsmc-service-manager-script.md), ujistěte se, že jste zadali platné hodnoty v rámci **konfigurace uživatele** oddílu .
3.  Pokud chcete vytvořit obor názvů služby Service bus relay, ujistěte se, že požadovaný prostředek poskytovatel je zaregistrovaný v rámci předplatného. Pokud není zaregistrovaný, ručně vytvořte obor názvů služby Service bus relay z webu Azure portal. Můžete také vytvořit, je při [vytváření hybridního připojení](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) z portálu Azure portal.


## <a name="contact-us"></a>Kontaktujte nás

Pro jakékoli dotazy nebo připomínky na IT Service Management Connector kontaktujte nás na adrese [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Další postup
[Přidat ITSM produktů a služeb IT Service Management Connector](../../azure-monitor/platform/itsmc-connections.md).
