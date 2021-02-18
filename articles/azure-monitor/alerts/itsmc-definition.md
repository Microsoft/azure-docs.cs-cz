---
title: IT Service Management Connector v Log Analytics
description: Tento článek poskytuje přehled konektoru pro správu služby IT (ITSMC) a informace o jeho používání pro monitorování a správu pracovních položek ITSM v Log Analytics a rychlé řešení problémů.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ba32cfa4bc5cd0b41a210cf88fb598afc3064495
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610658"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Připojení Azure k nástrojům ITSM pomocí služby IT Service Management Connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Tento článek poskytuje informace o tom, jak nakonfigurovat konektor ITSMC (IT Service Management Connector) v Log Analytics centrálně spravovat pracovní položky správy služeb IT (ITSM).

## <a name="add-it-service-management-connector"></a>Přidat konektor pro správu služeb IT

Než budete moct vytvořit připojení, musíte nainstalovat ITSMC.

1. V Azure Portal vyberte **vytvořit prostředek**:

   ![Snímek obrazovky, který zobrazuje položku nabídky pro vytvoření prostředku.](media/itsmc-overview/azure-add-new-resource.png)

2. Vyhledejte **konektor pro správu služeb IT** v Azure Marketplace. Pak vyberte **vytvořit**:

   ![Snímek obrazovky, který zobrazuje tlačítko vytvořit v Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. V části **pracovní prostor La** vyberte pracovní prostor Log Analytics, do kterého chcete nainstalovat ITSMC.
   > [!NOTE]
   > ITSMC můžete nainstalovat do pracovních prostorů Log Analytics jenom v následujících oblastech: Východní USA, Západní USA 2, Střed USA – jih, Středozápadní USA, US Gov – Arizona, US Gov – Virginie, Kanada – střed, Západní Evropa, Jižní Británie, jihovýchodní Asie, Japonsko – východ, Střed Indie a Austrálie – jihovýchod.

4. V části **pracovní prostor Log Analytics** vyberte skupinu prostředků, ve které chcete vytvořit prostředek ITSMC:

   ![Snímek obrazovky zobrazující část Log Analytics pracovní prostor](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se nyní pracovní prostory OMS označují jako *Log Analytics pracovní prostory*.

5. Vyberte **OK**.

Po nasazení prostředku ITSMC se v pravém horním rohu okna zobrazí oznámení.

## <a name="create-an-itsm-connection"></a>Vytvoření připojení ITSM

Po instalaci ITSMC je potřeba, abyste ITSM nástroj a povolili připojení z ITSMC. V závislosti na produktu ITSM, ke kterému se připojujete, vyberte jeden z následujících odkazů pro pokyny:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Po připravenou nástroje ITSM dokončete tyto kroky a vytvořte připojení:

1. Ve **všech prostředcích** vyhledejte **partnera (*název vašeho pracovního prostoru*)**:

   ![Snímek obrazovky, který zobrazuje poslední prostředky v Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

1. V části **zdroje dat pracovního prostoru** v levém podokně vyberte **připojení ITSM**:

   ![Snímek obrazovky zobrazující položku nabídky připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)

1. Vyberte **Přidat připojení**.

1. Zadejte nastavení připojení podle ITSM produktu, který používáte:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Ve výchozím nastavení ITSMC aktualizuje konfigurační data připojení každých 24 hodin. Pokud chcete data připojení okamžitě aktualizovat tak, aby odrážela jakékoli úpravy nebo aktualizace šablon, které jste provedli, vyberte v podokně připojení tlačítko **synchronizovat** :
   >
   > ![Snímek obrazovky, který zobrazuje tlačítko synchronizovat v podokně připojení](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytváření pracovních položek ITSM z výstrah Azure

Po vytvoření připojení ITSM můžete pomocí ITMC vytvořit pracovní položky v nástroji ITSM založené na výstrahách Azure. Chcete-li vytvořit pracovní položky, použijte akci ITSM ve skupinách akcí.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob, jak aktivovat akce pro vaše výstrahy Azure. Můžete použít skupiny akcí s výstrahami metriky, výstrahami protokolu aktivit a Log Analytics výstrahami v Azure Portal.

> [!NOTE]
> Po vytvoření připojení ITSM je třeba počkat 30 minut, než se proces synchronizace dokončí.

## <a name="define-a-template"></a>Definice šablony

Některé typy pracovních položek mohou používat šablony, které definujete v nástroji ITSM. Pomocí šablon můžete definovat pole, která se automaticky vyplní podle pevných hodnot pro skupinu akcí. Šablonu, kterou chcete použít, můžete definovat jako součást definice skupiny akcí.

Vytvoření skupiny akcí:

1. V Azure Portal vyberte  **výstrahy**.
2. V nabídce v horní části obrazovky vyberte **Spravovat akce**:

    ![Snímek obrazovky zobrazující položku nabídky spravovat akce](media/itsmc-overview/action-groups-selection-big.png)

   Zobrazí se okno **vytvořit skupinu akcí** .

3. Vyberte **předplatné** a **skupinu prostředků** , ve kterých chcete vytvořit skupinu akcí. Zadejte hodnoty v **názvu skupiny akcí** a **zobrazované jméno** pro vaši skupinu akcí. Pak vyberte **Další: oznámení**.

    ![Snímek obrazovky, který ukazuje okno vytvořit skupinu akcí.](media/itsmc-overview/action-groups-details.png)

4. Na kartě **oznámení** vyberte **Další: akce**.
5. Na kartě **Akce** vyberte v seznamu **typ akce** možnost **ITSM** . Do pole **název** zadejte název akce. Pak vyberte tlačítko pera, které představuje **Úpravy podrobností**.

    ![Snímek obrazovky zobrazující výběry pro vytvoření skupiny akcí](media/itsmc-definition/action-group-pen.png)

6. V seznamu **předplatné** vyberte předplatné, které obsahuje váš pracovní prostor Log Analytics. V seznamu **připojení** vyberte název konektoru ITSM. Za ním bude následovat název vašeho pracovního prostoru. Příkladem je *MyITSMConnector (MyWorkspace)*.

7. Vyberte typ **pracovní položky** .

8. Pokud chcete vyplnit předem vydaná pole s pevnými hodnotami, vyberte **použít vlastní šablonu**. V opačném případě v seznamu **šablon** vyberte existující [šablonu](#define-a-template) a v polích šablony zadejte pevné hodnoty.

9. V poslední části rozhraní pro vytvoření skupiny akcí ITSM můžete definovat, kolik pracovních položek bude vytvořeno pro každou výstrahu.

   > [!NOTE]
   > Tato část je relevantní jenom pro výstrahy prohledávání protokolu. U všech ostatních typů výstrah vytvoříte jednu pracovní položku na výstrahu.

   * Pokud jste vybrali možnost **incident** nebo **Alert** v rozevíracím seznamu **pracovní položky** , máte možnost vytvořit jednotlivé pracovní položky pro každou položku konfigurace.
    
     ![Snímek obrazovky zobrazující oblast lístku T S M s incidentem vybraným jako pracovní položka](media/itsmc-overview/itsm-action-configuration.png)
    
     * Pokud zaškrtnete políčko **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** , bude každá položka konfigurace v každé výstraze vytvářet novou pracovní položku. Vzhledem k tomu, že dojde k několika výstrahám pro stejné položky konfigurace, bude pro každou položku konfigurace k dispozici více než jedna pracovní položka.

       Například výstraha se třemi položkami konfigurace vytvoří tři pracovní položky. Výstraha s jednou položkou konfigurace vytvoří jednu pracovní položku.
        
     * Zrušíte-li zaškrtnutí políčka **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** , bude ITSMC vytvořit jednu pracovní položku pro každé pravidlo výstrahy a připojit k ní všechny ovlivněné položky konfigurace. Bude vytvořena nová pracovní položka, pokud je předchozí zavřena.

       >[!NOTE]
       > V takovém případě některé Výstrahy aktivovány negenerují nové pracovní položky v nástroji ITSM.

       Například výstraha se třemi položkami konfigurace vytvoří jednu pracovní položku. Pokud má výstraha pro stejné pravidlo výstrahy jako předchozí příklad jednu položku konfigurace, bude tato položka konfigurace připojena k seznamu ovlivněných položek konfigurace v vytvořené pracovní položce. Výstraha pro jiné pravidlo výstrahy s jednou položkou konfigurace vytvoří jednu pracovní položku.

   * Pokud jste vybrali **událost** v rozevíracím seznamu **pracovní položky** , můžete zvolit, že chcete vytvořit jednotlivé pracovní položky pro každou položku protokolu nebo pro každou položku konfigurace.
    
     ![Snímek obrazovky, který zobrazuje lístek I T S M s událostí vybranou jako pracovní položka.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku protokolu (pole položky konfigurace není vyplněno. Může vést k velkému počtu pracovních položek.)**, bude pro každý řádek ve výsledcích hledání dotazu výstrahy hledání protokolu vytvořena pracovní položka. Vlastnost Description v datové části pracovní položky bude obsahovat řádek z výsledků hledání.
      
     * Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku konfigurace**, každá položka konfigurace v každé výstraze vytvoří novou pracovní položku. Každá položka konfigurace může obsahovat více než jednu pracovní položku v systému ITSM. Tato možnost je shodná s tím, jak je zaškrtnuto políčko, které se zobrazí po výběru možnosti **incident** jako typ pracovní položky.

10. Vyberte **OK**.

Při vytváření nebo úpravách pravidla upozornění Azure použijte skupinu akcí, která má akci ITSM. Když se výstraha aktivuje, v nástroji ITSM se vytvoří nebo aktualizuje pracovní položka.

> [!NOTE]
> Informace o cenách akce ITSM najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.
>
> Pole s krátkým popisem v definici pravidla výstrahy je při odeslání pomocí akce ITSM omezené na 40 znaků.

## <a name="next-steps"></a>Další kroky

* [Řešení problémů v ITSMC](./itsmc-resync-servicenow.md)
