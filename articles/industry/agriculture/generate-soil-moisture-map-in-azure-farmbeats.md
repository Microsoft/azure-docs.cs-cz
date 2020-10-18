---
title: Generovat heatmapu vlhkosti půdy
description: Popisuje, jak ve službě Azure FarmBeats generovat heatmapu vlhkosti v půdě.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 2887e7c5fd875942c3d19391267ddbb9b73ccfec
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165549"
---
# <a name="generate-soil-moisture-heatmap"></a>Generovat heatmapu vlhkosti půdy

Vlhkost v půdě je voda, která je držena v prostorech mezi částicemi v půdě.Heatmapu vlhkosti v půdě vám pomůže pochopit údaje o vlhkosti v libovolné hloubce a ve velkém rozlišení v rámci vašich farem. K vygenerování přesné a použitelné heatmapu vlhkosti v půdě je nutné použít jednotné nasazení snímačů od stejného poskytovatele. Různí poskytovatelé budou mít rozdíly ve způsobu měření vlhkosti půdy spolu s rozdíly v kalibraci. Heatmapu se generuje pro konkrétní hloubku pomocí senzorů nasazených v této hloubce.

Tento článek popisuje proces generování heatmapu vlhkosti v půdě pro vaši farmu pomocí akcelerátoru služby Azure FarmBeats. V tomto článku se dozvíte, jak:

- [Vytváření farem](#create-a-farm)
- [Přiřazení senzorů ke farmám](#get-soil-moisture-sensor-data-from-partner)
- [Generovat heatmapu vlhkosti půdy](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Než začnete

Zajistěte, aby:  

- Předplatné Azure.
- Spuštěná instance Azure FarmBeats.
- Pro farmu jsou k dispozici minimálně tři senzory vlhkosti v půdě.

## <a name="create-a-farm"></a>Vytvořit farmu

Farma je geografickou oblastí zájmu, pro kterou chcete vytvořit heatmapu vlhkosti půdy. Můžete vytvořit farmu pomocí [rozhraní API farmy](https://aka.ms/FarmBeatsDatahubSwagger) nebo v [uživatelském rozhraní akcelerátoru FarmsBeats](manage-farms-in-azure-farmbeats.md#create-farms) .

## <a name="deploy-sensors"></a>Nasadit senzory

Měli byste fyzicky nasadit senzory vlhkosti v půdě na farmu. Můžete zakoupit senzory vlhkosti v půdě od kteréhokoli z našich schválených partnerů – [Přidavisové nástroje](https://www.davisinstruments.com/product/enviromonitor-gateway/) a [Teralytic](https://teralytic.com/). Měli byste se zamluvit se svým poskytovatelem senzorů a provést na farmě fyzické nastavení.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Získat data ze senzorů vlhkosti půdy od partnera

Vzhledem k tomu, že senzory spouštějí streamování, data na řídicím panelu dat umožňují data do Azure FarmBeats. To se dá udělat z partnerské aplikace.

Pokud jste například zakoupili Davisové senzory, přihlásíte se ke svému účtu počasí s odkazem a poskytnete požadovaná pověření pro povolení streamování dat do Azure FarmBeats. Pokud chcete získat požadované přihlašovací údaje, postupujte podle pokynů v článku [získání dat ze senzorů](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Po zadání přihlašovacích údajů a výběru **Odeslat** na partnerských aplikacích můžete nasměrovat data do služby Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Přiřazení senzorů vlhkosti v půdě pro farmu

Po propojení účtu snímače se službou Azure FarmBeats je potřeba přiřadit senzory vlhkosti v půdě do farmy zájmu.

1.  Na stránce domů v nabídce vyberte možnost **farmy** a zobrazí se stránka seznam **farmy** .
2.  Vyberte **MyFarm**  >  **Přidat zařízení**.
3.  Zobrazí se okno **Přidat zařízení** . Vyberte jakékoli zařízení, které je propojené s senzory vlhkosti v půdě pro vaši farmu.

    ![Snímek obrazovky, na kterém se zobrazuje obrazovka Přidat zařízení](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Vyberte **Přidat zařízení**.     

## <a name="generate-soil-moisture-heatmap"></a>Generovat heatmapu vlhkosti půdy

V tomto kroku se vytvoří úloha nebo dlouhodobá operace, která bude generovat heatmapu vlhkosti v půdě pro vaši farmu.

1.  Na domovské stránce přejděte do části **farmy** v levém navigačním panelu a zobrazte stránku farmy.
2.  Vyberte **MyFarm**.
3.  Na stránce **Podrobnosti o farmě** vyberte možnost **Generovat mapu přesnosti**.
4.  V rozevírací nabídce vyberte **vlhkost půdy**.
5.  V okně **vlhkosti půdy** vyberte **Tento týden**.
6.  V části **Vybrat míru vlhkosti půdního** **senzoru**zadejte míru, kterou chcete pro mapu použít.
    Pokud chcete zjistit míru snímače, vyberte v **senzorech**jakýkoliv senzor vlhkosti půdy. Ve **vlastnostech senzoru**použijte hodnotu **Název míry** .

    ![Snímek obrazovky, který ukazuje obrazovku vlhkosti půdy.](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Vyberte **Generovat mapy**.
    Zobrazí se potvrzovací zpráva s podrobnostmi o úloze. Další informace najdete v tématu stav úlohy v úlohách.

    >[!NOTE]
    > Dokončení úlohy trvá přibližně tři až čtyři hodiny.

### <a name="download-the-soil-moisture-heatmap"></a>Stáhnout heatmapu vlhkosti půdy

Použijte k tomu následující postup:

1. Na stránce **úlohy** ověřte **stav úlohy** pro úlohu, kterou jste vytvořili v posledním postupu.
2. Po **úspěšném**zobrazení stavu úlohy vyberte v nabídce možnost **mapy** .
3. Vyhledejte mapu podle dne vytvoření ve formátu <půdní-moisture_MyFarm_YYYY-MM-DD>.
4. Vyberte mapu ve sloupci **název** , zobrazí se automaticky otevírané okno s náhledem vybrané mapy.
5. Vyberte **Stáhnout**. Mapa se stáhne a uloží do místní složky vašeho počítače.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně vygenerovali heatmapu vlhkosti půdy, se naučíte, jak [vygenerovat senzory umístění](generate-maps-in-azure-farmbeats.md#sensor-placement-map) a ingestovat [historická data telemetrie](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
