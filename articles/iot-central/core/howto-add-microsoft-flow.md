---
title: Použití konektoru Azure IoT Central v Microsoft Flow | Microsoft Docs
description: Pomocí konektoru IoT Central v Microsoft Flow můžete aktivovat pracovní postupy a vytvářet, získávat, aktualizovat, odstraňovat zařízení a spouštět příkazy v pracovních postupech.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 42e79a03f5e3ba0e3e39cd37d9911b3a150cf175
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985454"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Vytváření pracovních postupů pomocí konektoru IoT Central v Microsoft Flow

*Toto téma se týká tvůrců a správců.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Použijte Microsoft Flow k automatizaci pracovních postupů napříč mnoha aplikacemi a službami, na kterých využívají firemní uživatelé. Pomocí konektoru IoT Central v Microsoft Flow můžete aktivovat pracovní postupy, když se pravidlo aktivuje v IoT Central. V pracovním postupu aktivovaném IoT Central nebo jakékoli jiné aplikaci můžete použít akce v konektoru IoT Central k těmto akcím:
- Vytvoření zařízení
- Získat informace o zařízení
- Aktualizace vlastností a nastavení zařízení
- Spuštění příkazu na zařízení
- Odstranění zařízení

Podívejte se na [tyto Microsoft Flow šablony](https://aka.ms/iotcentralflowtemplates) , které se připojují IoT Central k jiným službám, jako jsou například mobilní oznámení a Microsoft Teams.

## <a name="prerequisites"></a>Požadavky

- Aplikace vytvořená pomocí standardního cenového plánu
- Osobní nebo pracovní nebo školní účet Microsoftu pro použití Microsoft Flow (další[informace o Microsoft Flowch plánech](https://aka.ms/microsoftflowplans))
- Pracovní nebo školní účet pro použití konektoru služby Azure IoT Central

## <a name="trigger-a-workflow"></a>Aktivace pracovního postupu

V této části se dozvíte, jak aktivovat mobilní oznámení v mobilní aplikaci Flow při triggeru pravidla v IoT Central. Celý pracovní postup můžete v aplikaci IoT Central sestavit pomocí integrovaného návrháře Microsoft Flow.

1. Začněte [vytvořením pravidla v IoT Central](howto-create-telemetry-rules.md). Po uložení podmínek pravidla vyberte **akci Microsoft Flow** jako novou akci. Otevře se dialogové okno, ve kterém můžete nakonfigurovat pracovní postup. IoT Central uživatelský účet, ke kterému jste se přihlásili, se použije k přihlášení k Microsoft Flow.

    ![Vytvořit novou Microsoft Flow akci](media/howto-add-microsoft-flow/createflowaction.png)

1. Zobrazí se seznam pracovních postupů, ke kterým máte přístup, a jsou k tomuto pravidlu IoT Central připojeni. Klikněte na **prozkoumat šablony** nebo **Nový > vytvořit ze šablony** a můžete si vybrat z libovolné dostupné šablony. 

    ![Dostupné šablony Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Zobrazí se výzva, abyste se přihlásili ke konektorům v šabloně, kterou jste zvolili. 

    > [!NOTE]
    > Pokud chcete použít konektor Azure IoT Central, musíte se přihlásit pomocí účtu Azure Active Directory (pracovní nebo školní účet). Konektor Azure IoT Central nepodporuje osobní účet, například abc@outlook.com nebo abc@live.com.

    Až se přihlásíte ke konektorům, budete mít v Návrháři k sestavení pracovního postupu. Pracovní postup obsahuje aktivační proceduru IoT Central, která obsahuje již vyplněnou aplikaci a pravidlo.

1. Pracovní postup můžete přizpůsobit tak, že upravíte informace předané akci a přidáte nové akce. V tomto příkladu je akce **oznámení – poslat mi mobilní oznámení**. Můžete zahrnout *dynamický obsah* z pravidla IoT Central, který do vašeho oznámení předává důležité informace, jako je název zařízení a časové razítko.

    > [!NOTE]
    > Pokud chcete získat hodnoty měření a vlastností, které pravidlo aktivovaly, vyberte **Zobrazit další** text v okně dynamického obsahu.

    ![Akce úpravy toku s otevřeným dynamickým podoknem](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Po dokončení úprav akce vyberte **Uložit**. Budete přesměrováni na stránku Přehled pracovního postupu. Tady můžete zobrazit historii spuštění a sdílet ji s dalšími kolegy.

    > [!NOTE]
    > Pokud chcete, aby toto pravidlo upravili jiní uživatelé v aplikaci IoT Central, musíte je s nimi nasdílet v Microsoft Flow. Přidejte své Microsoft Flow účty jako vlastníky do pracovního postupu.

1. Pokud se vrátíte zpět k aplikaci IoT Central, uvidíte toto pravidlo v oblasti akcí Microsoft Flow akci.

Pracovní postupy můžete vytvářet také pomocí konektoru IoT Central přímo v Microsoft Flow. Pak můžete zvolit, ke které aplikaci IoT Central se chcete připojit.

## <a name="create-a-device-in-a-workflow"></a>Vytvoření zařízení v pracovním postupu

V této části se dozvíte, jak vytvořit nové zařízení v IoT Central při stisknutí tlačítka na mobilním zařízení pomocí Microsoft Flow mobilní aplikace. Pomocí této akce v programu Flow můžete integrovat systémy ERP, jako je Dynamics, pomocí IoT Central vytvořením nového zařízení, když se zařízení přidá jinam.

1. Začněte vytvořením prázdného pracovního postupu v Microsoft Flow.

1. Hledat **tlačítko toku pro mobilní zařízení** jako Trigger

1. V této aktivační události přidejte textový vstup nazvaný **název zařízení**. Změňte text popisu tak, aby bylo možné **zadat název zařízení nového zařízení**.

1. Přidejte novou akci. Vyhledejte **IoT Central Azure – vytvořit akci zařízení** .

1. Vyberte aplikaci a v rozevíracích seznamech vyberte šablonu zařízení, ze které chcete vytvořit zařízení. Uvidíte akci rozšířit a zobrazí se všechny vlastnosti a nastavení zařízení.

1. Vyberte pole název zařízení. V podokně dynamického obsahu vyberte **název zařízení**. Tato hodnota se předává ze vstupu, který uživatel zadá přes mobilní aplikaci, a je název nového zařízení v IoT Central. V tomto příkladu je jediným vyžadovaným polem název zařízení, který je označen červenou hvězdičkou. Jiná šablona zařízení může mít více povinných polí, která musí být vyplněna, aby bylo možné vytvořit nové zařízení.

    ![Dynamické podokno pro vytvoření toku akce zařízení](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Volitelné Vyplňte další pole podle potřeby pro vytváření nových zařízení.

1. Nakonec uložte pracovní postup.

1. Vyzkoušejte svůj pracovní postup v mobilní aplikaci Microsoft Flow. V aplikaci přejdete na kartu **tlačítka** . Mělo by se zobrazit vaše tlačítko > Vytvoření nového pracovního postupu zařízení. Zadejte název nového zařízení a sledujte jeho zobrazení v IoT Central!

    ![Mobilní snímek obrazovky pro vytvoření zařízení Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Aktualizace zařízení v pracovním postupu

V této části se dozvíte, jak aktualizovat nastavení a vlastnosti zařízení v IoT Central po stisknutí tlačítka na mobilním zařízení pomocí Microsoft Flow mobilní aplikace.

1. Začněte vytvořením prázdného pracovního postupu v Microsoft Flow.

1. Hledat **tlačítko toku pro mobilní zařízení** jako Trigger

1. V této aktivační události přidejte vstup **jako textový vstup** , který odpovídá nastavení nebo vlastnosti, kterou chcete změnit. Změňte text popisu.

1. Přidejte novou akci. Vyhledejte akci **IoT Central Azure – aktualizace zařízení** .

1. Z rozevíracího seznamu vyberte svou aplikaci. Teď budete potřebovat ID existujícího zařízení, které chcete aktualizovat. 

    > [!NOTE] 
    > Na stránce Podrobnosti o zařízení, které chcete aktualizovat, je **nutné použít ID, které najdete na adrese URL** . ID zařízení, které se nachází v seznamu zařízení v Průzkumníkovi zařízení, není ten, který se má použít v Microsoft Flow.

    ![ID IoT Central z adresy URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Název zařízení můžete aktualizovat. Pokud chcete aktualizovat některé vlastnosti a nastavení zařízení, musíte v rozevíracím seznamu **Šablona zařízení** vybrat šablonu zařízení, kterou chcete aktualizovat. Dlaždice akce se rozbalí, aby se zobrazily všechny vlastnosti a nastavení, které můžete aktualizovat.

    ![Pracovní postup aktualizace toku pro zařízení](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Vyberte všechny vlastnosti a nastavení, které chcete aktualizovat. V podokně dynamického obsahu vyberte odpovídající vstup z aktivační události. V tomto příkladu se hodnota umístění rozšíří, aby se aktualizovala vlastnost umístění zařízení.

1. Nakonec uložte pracovní postup.

1. Vyzkoušejte svůj pracovní postup v mobilní aplikaci Microsoft Flow. V aplikaci přejdete na kartu **tlačítka** . Mělo by se zobrazit vaše tlačítko > aktualizace pracovního postupu zařízení. Zadejte vstupy a podívejte se, jak se zařízení aktualizuje v IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Získání informací o zařízení v pracovním postupu

Podle jeho ID můžete získat informace o zařízení pomocí **IoT Central Azure – získat akci zařízení** . 
> [!NOTE] 
> Na stránce Podrobnosti o zařízení, které chcete aktualizovat, je **nutné použít ID, které najdete na adrese URL** . ID zařízení, které se nachází v seznamu zařízení v Průzkumníkovi zařízení, není ten, který se má použít v Microsoft Flow.

Můžete získat informace, jako je název zařízení, název šablony zařízení, hodnoty vlastností a hodnoty nastavení, které chcete předat pozdějším akcím v pracovním postupu. Tady je příklad pracovního postupu, který předává hodnotu vlastnosti název zákazníka ze zařízení do Microsoft Teams.

   ![Tok získat pracovní postup zařízení](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Spuštění příkazu na zařízení v pracovním postupu
Můžete spustit příkaz na zařízení určeném pomocí jeho ID pomocí akce **IoT Central Azure – spuštění příkazu** . 

> [!NOTE] 
> Na stránce Podrobnosti o zařízení, které chcete aktualizovat, je **nutné použít ID, které najdete na adrese URL** . ID zařízení, které se nachází v seznamu zařízení v Průzkumníkovi zařízení, není ten, který se má použít v Microsoft Flow.
    
Můžete vybrat příkaz pro spuštění a předání parametrů příkazu prostřednictvím této akce. Tady je příklad pracovního postupu, který spouští příkaz k restartování zařízení z tlačítka v mobilní aplikaci Microsoft Flow.

   ![Tok získat pracovní postup zařízení](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Odstranění zařízení v pracovním postupu

Podle jeho ID můžete zařízení odstranit pomocí akce **Azure IoT Central-odstranit zařízení** . 
> [!NOTE] 
> Na stránce Podrobnosti o zařízení, které chcete aktualizovat, je **nutné použít ID, které najdete na adrese URL** . ID zařízení, které se nachází v seznamu zařízení v Průzkumníkovi zařízení, není ten, který se má použít v Microsoft Flow.

Tady je příklad pracovního postupu, který odstraní zařízení na vyžádání tlačítka v mobilní aplikaci Microsoft Flow.

   ![Pracovní postup odstranění zařízení v toku](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže při vytváření připojení ke konektoru služby Azure IoT Central, najdete tady několik tipů, které vám pomůžou.

1. Osobní účty Microsoft (například @hotmail.com, @live.com, @outlook.com domény) se v tuto chvíli nepodporují. Musíte použít pracovní nebo školní účet Azure Active Directory (AD).

2. Pokud chcete použít konektor IoT Central v Microsoft Flow, musíte se k IoT Central aplikaci přihlásit aspoň jednou. V opačném případě se aplikace nebude zobrazovat v rozevíracích seznamech aplikace.

3. Pokud se vám při používání účtu Azure AD zobrazuje chyba, zkuste otevřít Windows PowerShell a spustit následující rutin jako správce.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat Microsoft Flow k sestavování pracovních postupů, je navržený další krok ke [správě zařízení](howto-manage-devices.md).

