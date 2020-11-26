---
title: IT Service Management Connector v Log Analytics
description: Tento článek poskytuje přehled konektoru pro správu služby IT (ITSMC) a informace o jeho používání pro monitorování a správu pracovních položek ITSM v Log Analytics a rychlé řešení problémů.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b4e35296a999070a6f536f4a52cfb7c3a1d4f42a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186486"
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
> Od 1. října 2020 Cherwell a ITSM integrace s s výstrahou Azure už nebudou pro nové zákazníky povolené. Nová připojení ITSM se nepodporují. Existující připojení ITSM budou podporována.

Pomocí ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě výstrah Azure (výstrahy metrik, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete svůj incident a data žádostí o změnu z nástroje ITSM na pracovní prostor služby Azure Log Analytics synchronizovat.

Informace o právních výrazech a zásadách ochrany osobních údajů najdete v tématu [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Můžete začít používat ITSMC provedením následujících kroků:

1.  [Přidat ITSMC.](#add-it-service-management-connector)
2.  [Vytvořte připojení ITSM.](#create-an-itsm-connection)
3.  [Použijte připojení.](#use-itsmc)


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

   ![Snímek obrazovky, který zobrazuje okno lístku ITSM.](media/itsmc-overview/itsm-action-configuration.png)
   
   * V případě, že vyberete položku "událost" v rozevíracím seznamu pracovní položky: Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku protokolu** na výběr přepínačů, bude každá výstraha vytvářet novou pracovní položku. Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** v výběru přepínačů, bude mít každá položka konfigurace svou vlastní pracovní položku.
   ![Snímek obrazovky, který zobrazuje okno lístku ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Vyberte **OK**.

Při vytváření nebo úpravách pravidla upozornění Azure použijte skupinu akcí, která má akci ITSM. Když se výstraha aktivuje, v nástroji ITSM se vytvoří nebo aktualizuje pracovní položka.

> [!NOTE]
>
>- Informace o cenách akce ITSM najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.
>
>
>- Pole s krátkým popisem v definici pravidla výstrahy je při odeslání pomocí akce ITSM omezené na 40 znaků.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizujte a analyzujte data o incidentech a žádostech o změnu.

V závislosti na konfiguraci při nastavování připojení se ITSMC může synchronizovat až 120 dní a data žádosti o změnu. Schéma záznamu protokolu pro tato data je k dispozici v [Další části](#additional-information) tohoto článku.

Pomocí řídicího panelu ITSMC můžete vizualizovat incident a data žádosti o změnu.

![Snímek obrazovky zobrazující řídicí panel ITSMC](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také poskytuje informace o stavu konektoru, který můžete použít jako výchozí bod k analýze problémů s připojením.

Můžete také vizualizovat incidenty synchronizované s ovlivněnými počítači v Service Map.

Service Map automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Umožňuje vám zobrazit vaše servery podle toho, jak jste si je rozmysleli: jako propojené systémy, které poskytují důležité služby. Service Map zobrazuje připojení mezi servery, procesy a porty v rámci libovolné architektury připojené k protokolu TCP. Kromě instalace agenta není nutná žádná konfigurace. Další informace najdete v tématu [použití Service map](../insights/service-map.md).

Pokud používáte Service Map, můžete zobrazit položky oddělení služeb vytvořené v řešeních ITSM, jak je znázorněno zde:

![Snímek obrazovky zobrazující Log Analytics obrazovku](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


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
- Pokud se připojení z uživatelského rozhraní připojeného zdroje nepovede a zobrazí se **Chyba při ukládání** zprávy o připojení, proveďte následující kroky:
   - Pro připojení ServiceNow, Cherwell a prov:  
     - Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé připojení.  
     - Ujistěte se, že máte v odpovídajícím ITSM produktu dostatečná oprávnění pro připojení.  
   - Pro Service Manager připojení:  
     - Ujistěte se, že je webová aplikace úspěšně nasazená a že je vytvořené hybridní připojení. Pokud chcete ověřit, jestli se úspěšně navázalo připojení k místnímu Service Managermu počítači, přečtěte si adresu URL webové aplikace, jak je popsáno v dokumentaci pro vytvoření [hybridního připojení](./itsmc-connections.md#configure-the-hybrid-connection).  

- Pokud se data z ServiceNow nesynchronizují Log Analytics, ujistěte se, že instance ServiceNow není v režimu spánku. Instance ServiceNow dev někdy přecházejí do režimu spánku, pokud jsou nečinné po dlouhou dobu. Pokud to neudělá, nahlaste problém.
- Pokud se Log Analytics výstrahy aktivují, ale pracovní položky se nevytvoří v produktu ITSM, pokud se položky konfigurace nevytvoří/propojí s pracovními položkami nebo další informace, podívejte se na tyto zdroje:
   -  ITSMC: řešení zobrazuje souhrn připojení, pracovních položek, počítačů a dalších. Vyberte dlaždici, která má popisek **stavu konektoru** . Provedete to tak, že přejdete do **protokolu hledání** pomocí příslušného dotazu. Další informace najdete v záznamech protokolu s `LogType_S` `ERROR` .
   - Stránka **hledání v protokolu** : zobrazí chyby a související informace přímo pomocí dotazu `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Řešení potíží s nasazením Service Manager Web App
-   Pokud máte problémy s nasazením webové aplikace, ujistěte se, že máte oprávnění k vytváření a nasazování prostředků v rámci předplatného.
-   Pokud získáte **odkaz na objekt není nastaven na instanci objektu** při spuštění [skriptu](itsmc-service-manager-script.md), ujistěte se, že jste zadali platné hodnoty v části **Konfigurace uživatele** .
-   Pokud se vám nepodaří vytvořit obor názvů služby Service Bus Relay, ujistěte se, že je v předplatném registrovaný požadovaný poskytovatel prostředků. Pokud není zaregistrované, ručně vytvořte obor názvů služby Service Bus Relay z Azure Portal. Můžete ho také vytvořit při [vytváření hybridního připojení](./itsmc-connections.md#configure-the-hybrid-connection) v Azure Portal.


## <a name="contact-us"></a>Kontaktujte nás

Pokud máte dotazy nebo připomínky ke konektoru správy IT služeb, kontaktujte nás na adrese [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Další kroky
[Přidat ITSM produkty/služby do konektoru správy služeb IT](./itsmc-connections.md)