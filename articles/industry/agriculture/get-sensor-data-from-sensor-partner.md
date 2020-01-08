---
title: Získat data senzorů od partnerů
description: Tento článek popisuje, jak získat data ze senzorů od partnerů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 455652795a13fe9755c1ed57681bedaf7a70a5d5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435172"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Získat data ze senzorů od partnerů snímačů

Azure FarmBeats pomáhá přenést streamovaná data ze zařízení IoT a senzorů do DataHub. V současné době jsou podporovány následující partneři zařízení snímačů.

  ![Partneři FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Integrace dat zařízení s Azure FarmBeats vám pomůže získat základní data ze senzorů IoT nasazených ve vaší farmě do DataHub. Data, jakmile jsou k dispozici, je možné vizuálně vymezit pomocí akcelerátoru FarmBeats. Data se dají použít pro vytváření modelů datových fúzí a machine learningu a umělé Intelligence (ML/AI) pomocí FarmBeats.

Pokud chcete spustit streamování dat senzorů, zajistěte následující:

-  Nainstalovali jste FarmBeats do Azure Marketplace.
-  Rozhodli jste se senzory a zařízení, která chcete nainstalovat na farmu.
-  Pokud plánujete používat senzory vlhkosti v půdě, použijte mapu umístění snímače vlhkosti FarmBeats, abyste získali doporučení k počtu senzorů a tam, kde je přesně chcete umístit. Další informace najdete v tématu věnovaném [generování map](generate-maps-in-azure-farmbeats.md).
- Zakoupíte a nasadíte zařízení nebo senzory od partnera zařízení ve vaší farmě. Ujistěte se, že máte přístup k datům ze senzorů prostřednictvím řešení partnerů pro zařízení.

## <a name="enable-device-integration-with-farmbeats"></a>Povolit integraci zařízení s FarmBeats

Po spuštění streamování dat ze senzorů můžete zahájit proces získávání dat do systému FarmBeats. Poskytněte poskytovateli zařízení následující informace, které umožní integraci do FarmBeats:

 - Koncový bod rozhraní API
 - ID tenanta
 - ID klienta
 - Tajný klíč klienta
 - Připojovací řetězec EventHub

Předchozí informace vám poskytne váš systémový integrátor. Všechny problémy, se kterými se můžete setkat, když povolíte integraci zařízení, kontaktujte svůj systémový integrátor.

Případně můžete přihlašovací údaje vygenerovat spuštěním tohoto skriptu z Azure Cloud Shell. Postupujte následovně.

1. Stáhněte [soubor zip](https://aka.ms/farmbeatspartnerscript)a extrahujte ho na místní disk. V souboru ZIP jsou dva soubory.
2. Přihlaste se k https://portal.azure.com/ a otevřete Cloud Shell. Tato možnost je k dispozici na panelu nástrojů v pravém horním rohu Azure Portal.

    ![Panel nástrojů Azure Portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ujistěte se, že je prostředí nastavené na **PowerShell**. Ve výchozím nastavení je nastavená na bash.

    ![Nastavení panelu nástrojů PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Nahrajte dva soubory z kroku 1 v instanci Cloud Shell.

    ![Tlačítko nahrát na panelu nástrojů](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Přejít do adresáře, kam se nahrály soubory. Ve výchozím nastavení se odešlou do domovského adresáře pod uživatelským jménem.
6. Spusťte tento skript:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. Postupujte podle pokynů na obrazovce a zaznamenejte hodnoty pro **koncový bod rozhraní API**, **ID tenanta**, **ID klienta**, **tajný klíč klienta**a **připojovací řetězec EventHub**. Připojovací řetězec EventHub je k dispozici jako součást odpovědi rozhraní API v Swagger.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrace dat zařízení pomocí generovaných přihlašovacích údajů

Pokud chcete propojit FarmBeats pomocí sady přihlašovacích údajů, které jste vygenerovali v předchozí části, navštivte Portál pro partnery zařízení:

 - Koncový bod rozhraní API
 - Připojovací řetězec EventHub
 - ID klienta
 - Tajný klíč klienta
 - ID tenanta

 Poskytovatel zařízení potvrdí úspěšnou integraci. Po potvrzení můžete zobrazit všechna zařízení a senzory v Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Zobrazit zařízení a snímače

V následující části můžete zobrazit zařízení a senzory pro vaši farmu.

### <a name="view-devices"></a>Zobrazení zařízení

V současné době FarmBeats podporuje následující zařízení:

- **Uzel**: zařízení, ke kterému jsou připojené některé snímače.
- **Brána**: zařízení, ke kterému jsou připojené aspoň jeden uzel.

Postupujte následovně.

1. Na domovské stránce v nabídce vyberte **zařízení** .
  Na stránce **zařízení** se zobrazí typ zařízení, model, stav, farma, na kterou je umístěna, a datum poslední aktualizace pro metadata. Ve výchozím nastavení je sloupec farma nastaven na *hodnotu null*. Můžete se rozhodnout přiřadit zařízení k farmě. Další informace najdete v tématu [přiřazení zařízení](#assign-devices).
2. Vyberte zařízení, ve kterém se zobrazí vlastnosti zařízení, telemetrie a podřízená zařízení připojená k zařízení.

    ![Stránka zařízení](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Zobrazit senzory

Postupujte následovně.

1. Na domovské stránce vyberte z nabídky **snímače** .
  Na stránce **senzory** se zobrazí podrobnosti o typu senzoru, farmě, k níž je připojen, k nadřazenému zařízení, názvu portu, typu portu a posledním aktualizovanému stavu.
2. Vyberte senzor pro zobrazení vlastností senzorů, aktivních výstrah a telemetrie ze senzoru.

    ![Stránka senzorů](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Přiřazení zařízení  

Po přetečení dat senzoru je můžete přiřadit do farmy, do které jste nasadili senzory.

1. Na stránce domů v nabídce vyberte možnost **farmy** . Zobrazí se stránka seznam **farmy** .
2. Vyberte farmu, ke které chcete zařízení přiřadit, a vyberte **Přidat zařízení**.
3. Zobrazí se okno **Přidat zařízení** . Vyberte zařízení, které chcete pro farmu přiřadit.

    ![Okno Přidat zařízení](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Vyberte **Přidat zařízení**. Případně přejděte do nabídky **zařízení** , vyberte zařízení, která chcete přiřadit k farmě, a vyberte **přidružit zařízení**.
5. V okně **přidružit zařízení** vyberte farmu z rozevíracího seznamu a výběrem **použít u všech** přidružte farmu ke všem vybraným zařízením.

    ![Okno přidružit zařízení](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Pokud chcete každé zařízení přidružit k jiné farmě, vyberte šipku rozevíracího seznamu ve sloupci **přiřadit k farmě** a vyberte farmu pro každý řádek zařízení.
7. Vyberte **přiřadit** a dokončete přiřazení zařízení.

### <a name="visualize-sensor-data"></a>Vizualizace dat ze senzorů

Postupujte následovně.

1. Na stránce domů v nabídce vyberte možnost **farmy** a zobrazte stránku **farmy** .
2. Vyberte **farmu** , pro kterou chcete zobrazit data ze senzorů.
3. Na řídicím panelu **farmy** můžete zobrazit data telemetrie. Můžete zobrazit živou telemetrii nebo použít **vlastní rozsah** pro zobrazení určitého rozsahu kalendářních dat.

    ![Řídicí panel farmy](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Odstranění snímače

Postupujte následovně.

1. Na domovské stránce výběrem **senzorů** z nabídky zobrazíte stránku **senzory** .
2. Vyberte zařízení, které chcete odstranit, a v potvrzovacím okně vyberte **Odstranit** .

    ![Tlačítko Odstranit](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Potvrzovací zpráva ukazuje, že senzor byl úspěšně odstraněn.

## <a name="delete-devices"></a>Odstranit zařízení

Postupujte následovně.

1. Na stránce domů v nabídce vyberte **zařízení** a zobrazte stránku **zařízení** .
2. Vyberte zařízení, které chcete odstranit, a v potvrzovacím okně vyberte **Odstranit** .

    ![Tlačítko Odstranit](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Další kroky

Teď máte k dispozici data ze senzorů do instance služby Azure FarmBeats. Nyní se dozvíte, jak [vygenerovat mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
