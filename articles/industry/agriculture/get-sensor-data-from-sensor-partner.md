---
title: Získejte data ze senzorů od partnerů
description: Tento článek popisuje, jak získat data ze senzorů od partnerů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384190"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Získejte data ze senzorů od partnerů senzorů

Azure FarmBeats vám pomůže přenést streamovaná data z vašich zařízení ioT a senzorů do Datahubu. V současné době jsou podporovány následující partnery zařízení senzoru.

  ![Partneři FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Integrace dat zařízení s Azure FarmBeats vám pomůže získat pozemní data ze senzorů IoT nasazených ve vaší farmě do datového centra. Data, jakmile jsou k dispozici, mohou být vizualizována pomocí akcelerátoru FarmBeats. Data mohou být použita pro fúzi dat a strojové učení / umělou inteligenci (ML / AI) model budovy pomocí FarmBeats.

Chcete-li spustit datový přenos dat ze senzorů, zajistěte následující:

-  FarmBeats jste nainstalovali na Azure Marketplace.
-  Rozhodli jste se pro senzory a zařízení, které chcete nainstalovat na svou farmu.
-  Pokud máte v plánu používat senzory vlhkosti půdy, použijte FarmBeats Půdní vlhkost senzor umístění mapy získat doporučení o počtu senzorů a kde přesně byste měli umístit. Další informace naleznete v [tématu Generování map](generate-maps-in-azure-farmbeats.md).
- Nakupujete a nasazujete zařízení nebo senzory od partnera zařízení na farmě. Ujistěte se, že máte přístup k datům senzorů prostřednictvím řešení partnerů zařízení.

## <a name="enable-device-integration-with-farmbeats"></a>Povolit integraci zařízení s FarmBeats

Po spuštění streamování dat ze senzorů můžete zahájit proces získávání dat do systému FarmBeats. Poskytněte svému poskytovateli zařízení následující informace, které umožní integraci do služby FarmBeats:

 - Koncový bod rozhraní API
 - ID tenanta
 - ID klienta
 - Tajný klíč klienta
 - Připojovací řetězec EventHub

Výše uvedené informace můžete vygenerovat následujícím postupem: (Vezměte prosím na vědomí, že tyto kroky je nutné provést v Azure, takže budete potřebovat přístup k předplatnému Azure, kde se nasadí FarmBeats)

1. Přihlaste se k webu https://portal.azure.com/.

2. **Pokud jste na FarmBeats verze 1.2.7 nebo novější, přeskočte kroky 2a, 2b a 2c a přejděte ke kroku 3.**. Můžete zkontrolovat Verzi FarmBeats kliknutím na ikonu Nastavení na pravé horní straně ui FarmBeats.

2a. Přejít na registrace aplikací služby Azure Active Directory ->

2b. Klikněte na registraci aplikace, která byla vytvořena jako součást vašeho nasazení FarmBeats. Bude mít stejný název jako vaše datové centrum FarmBeats.

2c. Klikněte na "Vystavit API" -> Klikněte na "Přidat klientskou aplikaci" a zadejte **04b07795-8ddb-461a-bbee-02f9e1bf7b46** a zkontrolujte "Authorize Scope". Tím se umožní přístup k Azure CLI (Cloud Shell) k provedení níže uvedených kroků.

3. Otevřete Cloud Shell. Tato možnost je dostupná na panelu nástrojů v pravém horním rohu portálu Azure.

    ![Panel nástrojů portálu Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. Ujistěte se, že je prostředí nastaveno na **Prostředí PowerShell**. Ve výchozím nastavení je nastavena na Bash.

    ![Nastavení panelu nástrojů prostředí PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. Přejděte do domovského adresáře.

   ```azurepowershell-interactive 

    cd  

    ```

7. Spusťte následující příkaz. Tím se stáhne skript do domovského adresáře.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. Spusťte následující skript. Skript požádá o ID klienta, které lze získat ze stránky Azure Active Directory -> Přehled.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. Podle pokynů na obrazovce zachyťte hodnoty pro **koncový bod rozhraní API**, **ID klienta**, **ID klienta**, **tajný klíč klienta**a **připojovací řetězec EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrace dat zařízení pomocí generovaných přihlašovacích údajů

Nyní máte následující informace generované z předchozí části.
 - Koncový bod rozhraní API
 - Připojovací řetězec EventHub
 - ID klienta
 - Tajný klíč klienta
 - ID tenanta
 
Budete muset poskytnout to to vaše zařízení partnera pro propojení FarmBeats. Přejděte na portál pro partnery zařízení, kde můžete udělat totéž. Například v případě, že používáte zařízení od Davis Instruments, Teralytic nebo Pessl Instruments (Metos.at), přejděte prosím na odpovídající stránky, jak je uvedeno níže:

[Davis nástroje](https://weatherlink.github.io/azure-farmbeats/setup)

[Teralytické](https://app.teralytic.com/)

[Pessl nástroje](https://ng.fieldclimate.com/user-api-services)

 Poskytovatel zařízení potvrzuje úspěšnou integraci. Po potvrzení můžete zobrazit všechna zařízení a senzory na Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Zobrazit zařízení a senzory

Pomocí následující části můžete zobrazit zařízení a senzory pro vaši farmu.

### <a name="view-devices"></a>Zobrazení zařízení

V současné době FarmBeats podporuje následující zařízení:

- **Uzel**: Zařízení, ke kterému je připojen jeden nebo více senzorů.
- **Brána**: Zařízení, ke kterému je připojen jeden nebo více uzlů.

Postupujte následovně.

1. Na domovské stránce vyberte **Zařízení** z nabídky.
  Stránka **Zařízení** zobrazuje typ zařízení, model, stav, farmu, do které je umístěna, a poslední aktualizované datum metadat. Ve výchozím nastavení je sloupec farmy nastaven na *hodnotu NULL*. Zařízení můžete přiřadit ke farmě. Další informace naleznete v tématu [Přiřazení zařízení](#assign-devices).
2. Výběrem zařízení zobrazíte vlastnosti zařízení, telemetrii a podřízená zařízení připojená k zařízení.

    ![Stránka Zařízení](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Zobrazit senzory

Postupujte následovně.

1. Na domovské stránce vyberte **senzory** z nabídky.
  Na stránce **Senzory** se zobrazují podrobnosti o typu senzoru, farmě, ke které je připojen, nadřazeném zařízení, názvu portu, typu portu a posledním aktualizovaném stavu.
2. Vyberte senzor, chcete-li ze senzoru zobrazit vlastnosti senzoru, aktivní výstrahy a telemetrii.

    ![Stránka senzory](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Přiřazení zařízení  

Poté, co data senzorů proudí dovnitř, můžete je přiřadit k farmě, kde jste nasadili senzory.

1. Na domovské stránce vyberte **Farmy** z nabídky. Zobrazí se stránka se **seznamem Farmy.**
2. Vyberte farmu, ke které chcete zařízení přiřadit, a vyberte **Přidat zařízení**.
3. Zobrazí se okno **Přidat zařízení.** Vyberte zařízení, které chcete přiřadit farmě.

    ![Okno Přidat zařízení](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Vyberte **Přidat zařízení**. Případně přejděte do nabídky **Zařízení,** vyberte zařízení, která chcete přiřadit farmě, a vyberte **Přidružit zařízení**.
5. V okně **Přidružit zařízení** vyberte farmu z rozevíracího seznamu a vyberte **Použít u všech,** chcete-li přidružit farmu ke všem vybraným zařízením.

    ![Okno Přidružit zařízení](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Chcete-li přidružit každé zařízení k jiné farmě, vyberte šipku rozevíracího seznamu ve sloupci **Přiřadit farmě** a vyberte farmu pro každý řádek zařízení.
7. Chcete-li dokončit přiřazení zařízení, vyberte **Přiřadit.**

### <a name="visualize-sensor-data"></a>Vizualizace dat ze snímačů

Postupujte následovně.

1. Na domovské stránce vyberte **Farmy** z nabídky a zobrazte stránku **Farmy.**
2. Vyberte **farmu,** pro kterou chcete zobrazit data ze senzorů.
3. Na řídicím panelu **farma** můžete zobrazit telemetrická data. Můžete zobrazit živou telemetrii nebo použít **vlastní rozsah** k zobrazení určitého rozsahu dat.

    ![Řídicí panel farmy](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Odstranění senzoru

Postupujte následovně.

1. Na domovské stránce vyberte **Senzory** z nabídky a zobrazte stránku **Senzory.**
2. Vyberte zařízení, které chcete odstranit, a v **potvrzovacím** okně vyberte Odstranit.

    ![Tlačítko Odstranit](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Potvrzovací zpráva ukazuje, že senzor byl úspěšně odstraněn.

## <a name="delete-devices"></a>Odstranění zařízení

Postupujte následovně.

1. Na domovské stránce vyberte **Zařízení** z nabídky a zobrazte stránku **Zařízení.**
2. Vyberte zařízení, které chcete odstranit, a v **potvrzovacím** okně vyberte Odstranit.

    ![Tlačítko Odstranit](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Další kroky

Nyní máte data senzorů toku do instance Azure FarmBeats. Nyní se naučte vytvářet [mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
