---
title: IT Service Management Connector v Log Analytics
description: Tento článek poskytuje přehled konektoru pro správu služby IT (ITSMC) a informace o jeho používání pro monitorování a správu pracovních položek ITSM v Log Analytics a rychlé řešení problémů.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b26643daede9e26f2bf1807ae99a6ced5d1cb08c
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901568"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Připojení Azure k nástrojům ITSM pomocí služby IT Service Management Connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Tento článek poskytuje informace o tom, jak nakonfigurovat konektor IT Service Management (ITSMC) v Log Analytics pro centrální správu pracovních položek.

## <a name="add-it-service-management-connector"></a>Přidat konektor pro správu služeb IT

Než budete moct vytvořit připojení, musíte přidat ITSMC.

1. V Azure Portal vyberte **vytvořit prostředek**:

   ![Snímek obrazovky, který ukazuje položku nabídky vytvořit prostředek.](media/itsmc-overview/azure-add-new-resource.png)

2. Vyhledejte **konektor pro správu služeb IT** v Azure Marketplace. Vyberte **vytvořit**:

   ![Snímek obrazovky, který zobrazuje tlačítko vytvořit v Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. V části **pracovní prostor La** vyberte pracovní prostor Azure Log Analytics, do kterého chcete nainstalovat ITSMC.
   >[!NOTE]
   >
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

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Po připravenou nástrojů ITSM dokončete tyto kroky a vytvořte připojení:

1. Ve **všech prostředcích** vyhledejte **partnera (*název vašeho pracovního prostoru*)**:

   ![Snímek obrazovky, který zobrazuje poslední prostředky v Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. V části **zdroje dat pracovního prostoru** v levém podokně vyberte **připojení ITSM**:

   ![Snímek obrazovky zobrazující položku nabídky připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)
1. Vyberte **Přidat připojení**.

1. Zadejte nastavení připojení podle popisu v tématu ITSM Products/Services:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Ve výchozím nastavení ITSMC aktualizuje konfigurační data připojení každých 24 hodin. Pokud chcete data připojení okamžitě aktualizovat tak, aby odrážela jakékoli úpravy nebo aktualizace šablon, které jste provedli, vyberte v okně připojení tlačítko **synchronizovat** :
   >
   > ![Snímek obrazovky, který zobrazuje tlačítko synchronizovat v okně připojení](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Použití ITSMC

   Pomocí ITSMC můžete vytvořit výstrahy z Azure Monitor výstrahy do nástroje ITSM.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytváření pracovních položek ITSM z výstrah Azure

Po vytvoření připojení ITSM můžete v nástroji ITSM vytvořit pracovní položky založené na výstrahách Azure. Chcete-li vytvořit pracovní položky, použijte akci ITSM ve skupinách akcí.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob, jak aktivovat akce pro vaše výstrahy Azure. V Azure Portal můžete použít skupiny akcí s upozorněními na metriky, výstrahy protokolu aktivit a výstrahy Azure Log Analytics.

> [!NOTE]
> Po vytvoření připojení ITSM musíte počkat 30 minut, než se proces synchronizace dokončí.

### <a name="template-definitions"></a>Definice šablon

   Existují typy pracovních položek, které mohou používat šablony, které jsou definovány nástrojem ITSM.
Pomocí šablon můžete definovat pole, která budou automaticky vyplněna podle pevných hodnot, které jsou definovány jako součást skupiny akcí. Šablony definujete v nástroji ITSM.
Můžete definovat, která šablona se má použít jako součást definice skupiny akcí.

Pro vytvoření skupin akcí použijte následující postup:

1. V Azure Portal vyberte  **výstrahy**.
2. V nabídce v horní části obrazovky vyberte **Spravovat akce**:

    ![Snímek obrazovky zobrazující položku nabídky spravovat akce](media/itsmc-overview/action-groups-selection-big.png)

   Zobrazí se okno **vytvořit skupinu akcí** .

3. Vyberte **předplatné** a **skupinu prostředků** , ve kterých chcete vytvořit skupinu akcí. Zadejte **název skupiny akcí** a **zobrazované jméno** pro vaši skupinu akcí. Vyberte **Další: oznámení**.

    ![Snímek obrazovky, který ukazuje okno vytvořit skupinu akcí.](media/itsmc-overview/action-groups-details.png)

4. V seznamu oznámení vyberte **Další: akce**.
5. V seznamu akce vyberte v seznamu **typ akce** možnost **ITSM** . Zadejte **název** akce. Vyberte tlačítko pera, které představuje **Úpravy podrobností**.

    ![Snímek obrazovky zobrazující definici skupiny akcí](media/itsmc-definition/action-group-pen.png)

6. V seznamu **předplatné** vyberte předplatné, ve kterém se nachází váš pracovní prostor Log Analytics. V seznamu **připojení** vyberte název konektoru ITSM. Za ním bude následovat název vašeho pracovního prostoru. Například MyITSMConnector (MyWorkspace).

7. Vyberte typ **pracovní položky** .

8. Pokud chcete vyplnit předem vydaná pole s pevnými hodnotami, vyberte **použít vlastní šablonu**. V opačném případě v seznamu **šablon** vyberte existující [šablonu](#template-definitions) a v polích šablony zadejte pevné hodnoty.

9. V poslední části definice skupiny Action ITSM můžete definovat, kolik výstrah bude vytvořeno z každé výstrahy. Tato část je relevantní jenom pro výstrahy prohledávání protokolu.

    * V případě, že vyberete v rozevírací nabídce pracovní položky "incident" nebo "Alert":
        * Pokud zaškrtnete zaškrtávací políčko **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** , každá položka konfigurace v každé výstraze vytvoří novou pracovní položku. V systému ITSM může být více než jedna položka konfigurace na jednu položku konfigurace.

            Například:
            1) Výstraha 1 se 3 položkami konfigurace: A, B, C-vytvoří 3 pracovní položky.
            2) Výstraha 2 s 1 položkou konfigurace: D – vytvoří 1 pracovní položku.

                **Na konci tohoto toku budou 4 výstrahy.**
        * Zrušíte-li zaškrtnutí políčka **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** , budou zobrazovány výstrahy, které nebudou vytvářet novou pracovní položku. pracovní položky budou sloučeny podle pravidla výstrahy.

            Například:
            1) Výstraha 1 se 3 položkami konfigurace: A, B, C-vytvoří 1 pracovní položku.
            2) Výstraha 2 pro stejné pravidlo výstrahy jako fáze 1 s 1 položkou konfigurace: D – bude sloučena s pracovní položkou ve fázi 1.
            3) Upozornění 3 pro jiné pravidlo výstrahy s 1 položkou konfigurace: E-vytvoří 1 pracovní položku.

                **Na konci tohoto toku budou 2 výstrahy.**

       ![Snímek obrazovky, který zobrazuje okno incidentu ITSM.](media/itsmc-overview/itsm-action-configuration.png)

    * V případě, že jste vybrali v rozevíracím seznamu "událost" pracovní položky:
        * Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku protokolu** v výběru přepínačů, bude pro každý řádek ve výsledcích hledání dotazu výstrahy hledání protokolu vytvořena výstraha. V datové části výstrahy bude vlastnost Popis obsahovat řádek z výsledků hledání.
        * Pokud vyberete možnost **vytvořit jednotlivé pracovní položky pro každou položku konfigurace** v výběru přepínačů, bude každá položka konfigurace v každé výstraze vytvářet novou pracovní položku. V systému ITSM může být více než jedna položka konfigurace na jednu položku konfigurace. Tato akce bude stejná jako při zaškrtnutí části zaškrtávací políčko v části incident/výstraha.
    ![Snímek obrazovky zobrazující okno události ITSM](media/itsmc-overview/itsm-action-configuration-event.png)

10. Vyberte **OK**.

Při vytváření nebo úpravách pravidla upozornění Azure použijte skupinu akcí, která má akci ITSM. Když se výstraha aktivuje, v nástroji ITSM se vytvoří nebo aktualizuje pracovní položka.

> [!NOTE]
>
>- Informace o cenách akce ITSM najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.
>
>
>- Pole s krátkým popisem v definici pravidla výstrahy je při odeslání pomocí akce ITSM omezené na 40 znaků.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)
