---
title: Získat data senzorů od partnerů
description: Tento článek popisuje, jak získat data ze senzorů od partnerů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 474b6f8356724546105d902a15427f483f656317
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182770"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Získat data ze senzorů od partnerů snímačů

Azure FarmBeats pomáhá přenést streamovaná data ze zařízení IoT a senzorů do DataHub. V současné době jsou podporovány následující partneři zařízení snímačů.

  ![Partneři FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Integrace dat zařízení s Azure FarmBeats vám pomůže získat základní data ze senzorů IoT nasazených ve vaší farmě do datového centra. Data, jakmile jsou k dispozici, je možné vizuálně vymezit pomocí akcelerátoru FarmBeats. Data se dají použít pro vytváření modelů datových fúzí a machine learningu a umělé Intelligence (ML/AI) pomocí FarmBeats.

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

Pokud chcete vygenerovat výše uvedené informace, postupujte podle následujících kroků:

> [!NOTE]
> Tyto kroky je potřeba provést v Azure pro přístup k předplatnému Azure, kde je nasazený FarmBeats.

1. Přihlaste se k webu https://portal.azure.com/.

2. **Pokud používáte FarmBeats verze 1.2.7 nebo novější, přeskočte kroky a, b a c a přejděte ke kroku 3.** Verzi FarmBeats můžete ověřit tak, že vyberete ikonu **Nastavení** v pravém horním rohu uživatelského rozhraní FarmBeats.

      a.  Přejít na **Azure Active Directory**  >  **Registrace aplikací**

      b. Vyberte **registraci aplikace** vytvořenou jako součást nasazení FarmBeats. Bude mít stejný název jako FarmBeats DataHub.

      c. Vyberte **zveřejnit rozhraní API** > vyberte **Přidat klientskou aplikaci** a zadejte **04b07795-8ddb-461a-bbee-02f9e1bf7b46** a ověřte **obor autorizace**. Tím zajistíte přístup k rozhraní příkazového řádku Azure CLI (Cloud Shell), abyste mohli provést následující kroky:

3. Otevřete Cloud Shell. Tato možnost je k dispozici na panelu nástrojů v pravém horním rohu Azure Portal.

    ![Panel nástrojů Azure Portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Ujistěte se, že je prostředí nastavené na **PowerShell**. Ve výchozím nastavení je nastavená na bash.

    ![Nastavení panelu nástrojů PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Přejít do domovského adresáře.

    ```azurepowershell-interactive
    cd
    ```

6. Spusťte následující příkaz. Tím se k tomu připojí ověřený účet, který se bude používat pro žádosti Azure AD.

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Spusťte následující příkaz. Tím se stáhne skript do domovského adresáře.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Spusťte následující skript. Skript zobrazí výzvu k zadání ID tenanta, které se dá získat ze   >  stránky s **přehledem** Azure Active Directory.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. Název koncového bodu rozhraní DataHub API musí být malými písmeny.
> 2. Pokud kopírujete adresu URL názvu webu farmbeats pro koncový bod rozhraní API DataHub, ujistěte se, že neexistuje žádné koncové lomítko (/).

9. Postupujte podle pokynů na obrazovce a zaznamenejte hodnoty pro **koncový bod rozhraní API**, **ID tenanta**, **ID klienta**, **tajný klíč klienta** a **připojovací řetězec EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrace dat zařízení pomocí generovaných přihlašovacích údajů

Nyní máte následující informace vygenerované z předchozí části.
 - Koncový bod rozhraní API
 - Připojovací řetězec EventHub
 - ID klienta
 - Tajný klíč klienta
 - ID tenanta

Pro připojení FarmBeats ho budete muset poskytnout vašemu partnerovi zařízení. Pro stejný postup použijte portál pro partnery zařízení. Například pokud používáte zařízení z nástrojů Davisové nástroje, Teralytic nebo Pessl Instrumentation (Metos.at), přejít na příslušné stránky, jak je uvedeno níže:

1. [Davisové nástroje](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Nástroje Pessl](https://ng.fieldclimate.com/user-api-services)

Poskytovatel zařízení potvrdí úspěšnou integraci. Po potvrzení můžete zobrazit všechna zařízení a senzory v Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Zobrazit zařízení a snímače

V následující části můžete zobrazit zařízení a senzory pro vaši farmu.

### <a name="view-devices"></a>Zobrazení zařízení

V současné době FarmBeats podporuje následující zařízení:

- **Uzel**: zařízení, ke kterému jsou připojené některé snímače.
- **Brána**: zařízení, ke kterému jsou připojené aspoň jeden uzel.

Postupujte takto:

1. Na domovské stránce v nabídce vyberte **zařízení** .
  Na stránce **zařízení** se zobrazí typ zařízení, model, stav, farma, na kterou je umístěna, a datum poslední aktualizace pro metadata. Ve výchozím nastavení je sloupec farma nastaven na *hodnotu null*. Můžete se rozhodnout přiřadit zařízení k farmě. Další informace najdete v tématu [přiřazení zařízení](#assign-devices).
2. Vyberte zařízení, ve kterém se zobrazí vlastnosti zařízení, telemetrie a podřízená zařízení připojená k zařízení.

    ![Stránka zařízení](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Zobrazit senzory

Postupujte takto:

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

### <a name="visualize-sensor-data"></a>Vizualizace dat ze snímačů

Postupujte takto:

1. Na stránce domů v nabídce vyberte možnost **farmy** a zobrazte stránku **farmy** .
2. Vyberte **farmu** , pro kterou chcete zobrazit data ze senzorů.
3. Na řídicím panelu **farmy** můžete zobrazit data telemetrie. Můžete zobrazit živou telemetrii nebo použít **vlastní rozsah** pro zobrazení určitého rozsahu kalendářních dat.

    ![Řídicí panel farmy](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Odstranění snímače

Postupujte takto:

1. Na domovské stránce výběrem **senzorů** z nabídky zobrazíte stránku **senzory** .
2. Vyberte zařízení, které chcete odstranit, a v potvrzovacím okně vyberte **Odstranit** .

    ![Snímek obrazovky, který zvýrazní stránku odstranění senzorů a tlačítko Odstranit](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Potvrzovací zpráva ukazuje, že senzor byl úspěšně odstraněn.

## <a name="delete-devices"></a>Odstranit zařízení

Postupujte takto:

1. Na stránce domů v nabídce vyberte **zařízení** a zobrazte stránku **zařízení** .
2. Vyberte zařízení, které chcete odstranit, a v potvrzovacím okně vyberte **Odstranit** .

    ![Tlačítko Odstranit](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Další kroky

Teď máte k dispozici data ze senzorů do instance služby Azure FarmBeats. Nyní se dozvíte, jak [vygenerovat mapy](generate-maps-in-azure-farmbeats.md#generate-maps) pro vaše farmy.
