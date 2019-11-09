---
title: Získat data senzorů od partnerů
description: Popisuje, jak získat data ze senzorů od partnerů.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 424df8ac7145129dd70a9a5791648730189b528b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889661"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Získat data ze senzorů od partnerů snímačů

Azure FarmBeats pomáhá přenést streamovaná data ze zařízení IoT a senzorů do datového centra. V současné době jsou podporovány následující partneři zařízení snímače:

  ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Integrace dat zařízení s Azure FarmBeats vám pomůže získat základní data ze senzorů IoT nasazených ve vaší farmě do datového centra. Data, jakmile jsou k dispozici, je možné vizuálně vymezit pomocí akcelerátoru FarmBeats a dají se použít k vytváření modelů pro datové fúze a AI a ML pomocí FarmBeats.

Pokud chcete spustit streamování dat senzorů, zajistěte následující:

-  Nainstalovali jste FarmBeats z Azure Marketplace.
-  Rozhodli jste se snímače a zařízení, která chcete nainstalovat na farmu.
-  Pokud plánujete používat senzory vlhkosti v půdě, můžete k získání doporučení na základě počtu senzorů a tam, kde je přesně umístit senzory, použít mapu umístění snímače vlhkosti FarmBeats. Další informace najdete v tématu věnovaném [generování map](generate-maps.md).

- Zakupte a nasaďte zařízení/senzory od partnera zařízení ve farmě. Ujistěte se, že máte přístup k datům ze senzorů prostřednictvím řešení partnerů pro zařízení.

### <a name="enable-device-integration-with-farmbeats"></a>Povolit integraci zařízení s FarmBeats   

Jakmile začnete streamovat data senzorů, můžete zahájit proces získávání dat do systému FarmBeats. Aby bylo možné integraci FarmBeats, musíte poskytovateli zařízení zadat následující informace:  

 - Koncový bod rozhraní API  
 - ID tenanta  
 - ID klienta  
 - Tajný kód klienta  
 - Připojovací řetězec EventHub

Výše uvedené informace vám poskytne váš systémový integrátor. U všech problémů při povolování integrace zařízení se obraťte na integrátor systému.

Případně můžete přihlašovací údaje vygenerovat spuštěním tohoto skriptu z Azure Cloud Shell. Postupujte podle následujících kroků:

1. Stáhněte [soubor zip](https://aka.ms/farmbeatspartnerscript) a extrahujte ho na místní disk. V souboru ZIP se nacházejí dva soubory.
2. Přihlaste se k https://portal.azure.com/ a otevřete Cloud Shell (Tato možnost je k dispozici na pravém horním panelu portálu).  

    ![Beats farmy projektu](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ujistěte se, že je prostředí nastavené na **PowerShell** – ve výchozím nastavení je nastavené na bash.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Nahrajte dva soubory (z kroku 1 výše) do Cloud Shell.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Přejít do adresáře, kam se nahrály soubory (ve výchozím nastavení se nahrají do domovského adresáře > uživatelské jméno).
6. Spusťte tento skript:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. Pokud chcete zachytit hodnoty, postupujte podle pokynů na obrazovce. (Koncový bod rozhraní API, ID tenanta, ID klienta, tajný klíč klienta a připojovací řetězec EventHub).

**Integrujte data zařízení pomocí generovaných přihlašovacích údajů.**

Navštivte portál partnera zařízení a propojte FarmBeats pomocí sady přihlašovacích údajů, které jste vygenerovali v předchozí části.

 - Koncový bod rozhraní API  
 - Připojovací řetězec EventHub  
 - ID klienta  
 - Tajný kód klienta  
 - ID tenanta  

 Poskytovatel zařízení potvrdí úspěšnou integraci. Po potvrzení můžete zobrazit všechna zařízení a senzory v Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Zobrazit zařízení a snímače

V následující části můžete zobrazit zařízení a senzory ve vaší farmě.

### <a name="view-devices"></a>Zobrazení zařízení

V současné době FarmBeats podporuje následující zařízení:

- **Uzel**: zařízení, ke kterému jsou připojené některé snímače.
- **Brána**: zařízení, ke kterému jsou připojené některé snímače.

Použijte k tomu následující postup:

1. Na domovské stránce v nabídce vyberte **zařízení** .
  Na stránce zařízení se zobrazí typ zařízení, model, stav, farma, na kterou je umístěna, a datum poslední aktualizace pro metadata. Ve výchozím nastavení je sloupec farma nastaven na hodnotu NULL. Můžete se rozhodnout přiřadit zařízení k farmě. Další informace najdete v tématu [přiřazení zařízení](#assign-devices).
2. Vyberte zařízení, ve kterém se zobrazí vlastnosti zařízení, telemetrie a podřízená zařízení připojená k zařízení.  

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Zobrazit senzory

Použijte k tomu následující postup:

1. Na domovské stránce vyberte z nabídky **snímače** .
  Na stránce senzory se zobrazí podrobnosti o typu senzoru, farmě, ke které je připojen, k nadřazenému zařízení, názvu portu, typu portu a posledním aktualizovanému stavu.
2. Vyberte senzor pro zobrazení vlastností senzorů, aktivních výstrah a telemetrie ze senzoru.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Přiřazení zařízení  

Jakmile budete mít data senzorů v nástroji, můžete je přiřadit do farmy, do které jste nasadili senzory.

1. Na stránce domů v nabídce vyberte možnost **farmy** a zobrazí se stránka seznam **farmy** .  
2. Vyberte farmu, ke které chcete zařízení přiřadit, a vyberte **Přidat zařízení**.  
3. Zobrazí se okno **Přidat zařízení** . Vyberte zařízení, které chcete pro farmu přiřadit.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Vyberte **Přidat zařízení**. Případně přejděte do nabídky **zařízení** , vyberte zařízení, která chcete přiřadit k farmě, a vyberte **přidružit zařízení**.  
5. V okně **přidružit zařízení** vyberte farma z rozevíracího seznamu a vyberte **použít u všech** a přidružte farmu ke všem vybraným zařízením.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Pokud chcete každé zařízení přidružit k jiné farmě, vyberte rozevírací seznam ve sloupci **přiřadit k farmě** a vyberte farmu pro každý řádek zařízení.  
7. Vyberte **přiřadit** a dokončete přiřazení zařízení.

### <a name="visualize-sensor-data"></a>Vizualizace dat ze senzorů

Použijte k tomu následující postup:

1. Na stránce domů v nabídce vyberte možnost **farmy** a zobrazte stránku **farmy** .  
2. Vyberte **farmu** , pro kterou chcete zobrazit data ze senzorů.  
3. Na řídicím panelu **farmy** můžete zobrazit data telemetrie. Můžete se rozhodnout zobrazit živou telemetrii nebo použít **vlastní rozsah** k zobrazení v určitém časovém rozsahu.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Odstranit senzor

Postupujte následovně:

1. Na domovské stránce výběrem **senzorů** z nabídky zobrazíte stránku **senzory** .  
2. Vyberte zařízení, které chcete odstranit, a vyberte **Odstranit** z potvrzovacího okna.

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Potvrzovací zpráva ukazuje, že senzor byl úspěšně odstraněn.  

## <a name="delete-devices"></a>Odstranit zařízení

Postupujte následovně:

1. Na stránce domů v nabídce vyberte **zařízení** a zobrazte stránku zařízení.  
2. Vyberte zařízení, které chcete odstranit, a v potvrzovacím okně vyberte **Odstranit** .

    ![Beats farmy projektu](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Další kroky

Teď máte k dispozici data ze senzorů do instance služby Azure FarmBeats. Nyní se dozvíte, jak [vygenerovat mapy](generate-maps.md#generate-maps) pro vaše farmy.
