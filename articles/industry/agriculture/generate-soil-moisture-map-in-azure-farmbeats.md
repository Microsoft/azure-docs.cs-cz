---
title: Generovat půdní vlhkost Heatmap
description: Popisuje, jak generovat půdní vlhkost Heatmap v Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482566"
---
# <a name="generate-soil-moisture-heatmap"></a>Generovat půdní vlhkost Heatmap

Půdní vlhkost je voda, která se drží v prostoru mezi částicemi půdy.Půdní vlhkost Heatmap vám pomůže pochopit data vlhkosti v jakékoli hloubce a ve vysokém rozlišení ve vašich farmách. Pro vytvoření přesné a použitelné tepelné mapy vlhkosti půdy je vyžadováno jednotné nasazení senzorů od stejného poskytovatele. Různí poskytovatelé budou mít rozdíly ve způsobu měření vlhkosti půdy spolu s rozdíly v kalibraci. Heatmap je generován pro určitou hloubku pomocí senzorů nasazených v této hloubce.

Tento článek popisuje proces generování heatmap půdní vlhkosti pro vaši farmu pomocí akcelerátoru Azure FarmBeats. V tomto článku se dozvíte, jak:

- [Vytvořit farmy](#create-a-farm)
- [Přiřazení senzorů farmám](#get-soil-moisture-sensor-data-from-partner)
- [Generovat půdní vlhkost Heatmap](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Než začnete

Zajistěte, aby:  

- Předplatné Azure.
- Spuštěná instance Azure FarmBeats.
- Pro farmu jsou k dispozici minimálně tři senzory vlhkosti půdy.

## <a name="create-a-farm"></a>Vytvoření farmy

Farma je geografická oblast zájmu, pro kterou chcete vytvořit tepelnou mapu vlhkosti půdy. Farmu můžete vytvořit pomocí [rozhraní FARMAPI](https://aka.ms/FarmBeatsDatahubSwagger) nebo v [uzu akcelerátoru FarmsBeats](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Nasazení senzorů

Měli byste fyzicky nasadit senzory vlhkosti půdy na farmě. Senzory vlhkosti půdy si můžete zakoupit od některého z našich schválených partnerů - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) a [Teralytic](https://teralytic.com/). Měli byste koordinovat se svým poskytovatelem senzoru dělat fyzické nastavení na farmě.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Získejte data senzorů vlhkosti půdy od partnera

Když senzory začnou streamovat data do řídicího panelu dat partnera, umožňují data do Azure FarmBeats. To lze provést z partnerské aplikace.

Pokud jste například zakoupili daviské senzory, přihlásíte se ke svému účtu s odkazem na počasí a poskytnete požadovaná pověření, která umožní streamování dat do Azure FarmBeats. Chcete-li získat požadovaná pověření, postupujte podle pokynů [z získat data ze senzoru](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Jakmile zadáte přihlašovací údaje a v partnerské aplikaci vyberete **Odeslat,** můžete mít data tok do Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Přiřazení snímačů vlhkosti půdy do farmy

Jakmile propojíte svůj účet senzorů do Azure FarmBeats, musíte přiřadit senzory vlhkosti půdy k farmě, která vás zajímá.

1.  Na domovské stránce vyberte **farmy** z nabídky, zobrazí se stránka se seznamem **Farmy.**
2.  Vyberte možnost Přidat**zařízení** **MyFarm** > .
3.  Zobrazí se okno **Přidat zařízení.** Vyberte libovolné zařízení, které je spojeno se senzory vlhkosti půdy pro vaši farmu.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Vyberte **Přidat zařízení**.     

## <a name="generate-soil-moisture-heatmap"></a>Generovat půdní vlhkost Heatmap

Tento krok je vytvořit práci nebo dlouhou provozní operaci, která bude generovat půdní vlhkost Heatmap pro vaši farmu.

1.  Na domovské stránce přejděte na **Položku Farmy** z levé navigační nabídky a zobrazte stránku farmy.
2.  Vyberte **možnost MyFarm**.
3.  Na stránce **Podrobnosti farmy** vyberte **Generovat přesnost mapy**.
4.  V rozbalovací nabídce vyberte **položku Půdní vlhkost**.
5.  V okně **Půdní vlhkost** vyberte **tento týden**.
6.  Do části Vybrat **měření čidla půdní** **vlhkosti** zadejte míru, kterou chcete pro mapu použít.
    Chcete-li najít měření senzoru, **vyberte**v části Senzory libovolný snímač vlhkosti půdy. Ve **vlastnostech senzoru**použijte hodnotu **Změřit název.**

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Vyberte **Generovat mapy**.
    Zobrazí se potvrzovací zpráva s podrobnostmi o úloze. Další informace naleznete v tématu Stav úlohy v úlohách.

    >[!NOTE]
    > Práce trvá přibližně tři až čtyři hodiny.

### <a name="download-the-soil-moisture-heatmap"></a>Stáhněte si heatmapu vlhkosti půdy

Použijte k tomu následující postup:

1. Na stránce **Úlohy** zkontrolujte **stav úlohy,** kterou jste vytvořili v posledním postupu.
2. Když se zobrazí stav úlohy **Proběhlúspěšně**, vyberte v nabídce **možnost Mapy.**
3. Vyhledejte mapu v den, kdy byla vytvořena ve formátu <půda-moisture_MyFarm_YYYY-MM-DD>.
4. Vyberte mapu ve sloupci **Název,** zobrazí se vyskakovací okno s náhledem vybrané mapy.
5. Vyberte **Download** (Stáhnout). Mapa je stažena a uložena do místní složky počítače.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Další kroky

Nyní, když jste úspěšně vygenerovali heatmapu půdní vlhkosti, zjistěte, jak [generovat umístění senzorů](generate-maps-in-azure-farmbeats.md#sensor-placement-map) a [ingestovat historická telemetrická data](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
