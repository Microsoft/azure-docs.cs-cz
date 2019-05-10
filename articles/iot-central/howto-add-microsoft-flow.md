---
title: Vytváření pracovních postupů s Azure IoT Central konektorem v Microsoft Flow | Dokumentace Microsoftu
description: Použití konektoru pro IoT Central v Microsoft Flow spouštět pracovní postupy a vytvořit, získat, aktualizovat, odstraňovat zařízení a spusťte příkazy v pracovních postupech.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: c0a03b70c6e5e4742e03d4892b2b5f97c908ab9c
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467988"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Vytváření pracovních postupů s IoT Central konektorem v Microsoft Flow

*Toto téma platí pro tvůrce a správce.*

Pomocí Microsoft Flow můžete automatizovat pracovní postupy mezi mnoha aplikacemi a služeb, které využívají firemní uživatelé. Pracovní postupy můžete aktivovat pomocí IoT Central konektoru v Microsoft Flow, když se pravidlo aktivuje v IoT Central. V pracovním postupu aktivovaného IoT Central nebo jakékoli jiné aplikace můžete použít akce v spojnici IoT Central:
- Vytvořit zařízení
- Získejte informace o zařízení
- Aktualizovat vlastnosti a nastavení zařízení
- Spuštění příkazu na zařízení
- Odstranění zařízení

Podívejte se na [tyto šablony Microsoft Flow](https://aka.ms/iotcentralflowtemplates) IoT Central, které se připojovat k jiným službám, jako jsou mobilní oznámení a Microsoft Teams.

## <a name="prerequisites"></a>Požadavky

- Aplikace s průběžnými platbami
- Microsoft osobní nebo pracovní nebo školní účet pro přihlášení do Flow ([Další informace o plánech Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Trigger pracovního postupu

V této části se dozvíte, jak aktivovat mobilní oznámení v mobilní aplikaci Flow při pravidlo aktivuje v IoT Central. V rámci aplikace IoT Central a používání vloženého návrháře Microsoft Flow můžete vytvořit tato celý pracovní postup.

1. Začněte tím, že [vytváření pravidla v IoT Central](howto-create-telemetry-rules.md). Po uložení podmínky pravidla, vyberte **akce Microsoft Flow** jako novou akci. Otevře se dialogové okno pro konfiguraci pracovního postupu. IoT Central uživatelský účet, který jste přihlášeni, se použije pro přihlášení do Microsoft Flow.

    ![Vytvořit novou akci Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Zobrazí se seznam pracovních postupů tha mají přístup k a jsou připojené k tomuto pravidlu IoT Central. Klikněte na tlačítko **zkoumání šablon** nebo **nový > vytvořit ze šablony** a můžete si vybrat z kterékoli z dostupných šablon. 

    ![Dostupné šablony Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Jste vyzváni k přihlášení ke konektorům v šabloně, kterou jste zvolili. Po přihlášení konektorů budete přesměrováni do návrháře k vytvoření pracovního postupu. Pracovní postup obsahuje aktivační událost IoT Central, která má svoji aplikaci a pravidlo už vytvořilo.

1. Pracovní postup můžete přizpůsobit úpravou informace předány akcí a přidání nové akce. V tomto příkladu "action" je **oznámení – odeslat mobilní oznámení**. Můžete zahrnout *dynamický obsah* z vašeho IoT Central pravidla předávání podél důležité informace, jako je název zařízení a časové razítko oznámení.

    > [!NOTE]
    > Vyberte **zobrazit další** textu v okně dynamického obsahu se získat měření a hodnoty vlastností, které aktivuje pravidlo.

    ![Tok akce s otevřeným podoknem dynamické úpravy](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Po dokončení úprav akci, vyberte **Uložit**. Budete přesměrováni na stránku přehled vašeho pracovního postupu. Zde můžete zobrazit historii spuštění a sdílet ho s dalšími kolegy.

    > [!NOTE]
    > Pokud chcete ostatním uživatelům ve vaší aplikaci IoT Central, chcete-li upravit toto pravidlo, musí ho sdílet s nimi v Microsoft Flow. Přidáte své účty Microsoft Flow jako vlastníky ve svém pracovním postupu.

1. Pokud vrátíte se do vaší aplikace IoT Central, uvidíte, že toto pravidlo obsahuje akci Microsoft Flow v oblasti akce.

Můžete také vytvářet pracovní postupy pomocí konektoru rozhraní IoT Central přímo z Microsoft Flow. Pak můžete které IoT Central aplikace pro připojení k.

## <a name="create-a-device-in-a-workflow"></a>Vytvoření zařízení v pracovním postupu

V této části se dozvíte, jak vytvořit nové zařízení v IoT Central při stisknutí tlačítka na mobilním zařízení pomocí mobilní aplikace Microsoft Flow. Tato akce ve službě Flow můžete integrovat systémy ERP, jako je Dynamics IoT Central tak, že vytvoříte nové zařízení, když se zařízení přidá jinde.

1. Začněte vytvořením prázdné pracovního postupu v Microsoft Flow.

1. Vyhledejte **tlačítko tok pro mobilní zařízení** jako trigger.

1. V této aktivační události, přidat textový vstup volá **název zařízení**. Změnit popis text, který má být **zadejte název zařízení novým zařízením**.

1. Přidejte novou akci. Hledat **vytvoření zařízení Azure IoT Central -** akce.

1. Vyberte aplikaci a zvolte zařízení šablonu pro vytvoření zařízení z v rozevíracích seznamech. Zobrazí se vám akce po rozbalení zobrazují všechny vlastnosti a nastavení zařízení.

1. Vyberte pole název zařízení. V podokně dynamického obsahu vyberte **název zařízení**. Tato hodnota se předá ze vstupu uživatele zadá přes mobilní aplikaci a je název nové zařízení na IoT Central. Jediné povinné pole v tomto příkladu je název zařízení označená červenou hvězdičkou. Jiné šablony zařízení může mít více povinná pole, které je potřeba vyplnit pro vytvoření nového zařízení.

    ![Tok vytvořit dynamické podokna akcí zařízení](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Volitelné) Vyplňte další pole, která je vhodná pro vytváření nových zařízení.

1. Nakonec uložte pracovního postupu.

1. Zkuste pracovního postupu v mobilní aplikaci Microsoft Flow. Přejděte **tlačítka** kartě v aplikaci. Měli byste vidět tlačítko -> vytvořit nový pracovní postup zařízení. Zadejte název nové zařízení a sledujte, zobrazí se i IoT Central!

    ![Snímek obrazovky mobilní zařízení vytvořit tok](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Aktualizace zařízení v pracovním postupu

V této části se dozvíte, jak aktualizovat nastavení zařízení a vlastností v IoT Central při stisknutí tlačítka na mobilním zařízení pomocí mobilní aplikace Microsoft Flow.

1. Začněte vytvořením prázdné pracovního postupu v Microsoft Flow.

1. Vyhledejte **tlačítko tok pro mobilní zařízení** jako trigger.

1. V tento trigger, přidejte jako vstup **umístění** textové zadání, která odpovídá nastavení nebo vlastnosti, kterou chcete změnit. Změňte popis.

1. Přidejte novou akci. Hledat **aktualizovat zařízení Azure IoT Central -** akce.

1. Vyberte aplikace z rozevíracího seznamu. Teď budete potřebovat ID existující zařízení, které chcete aktualizovat. 

    > [!NOTE] 
    > **Je nutné použít ID nalezeno v adrese URL** na stránce s podrobnostmi zařízení zařízení, které chcete aktualizovat. V Průzkumníku zařízení seznam zařízení se nenašlo ID zařízení není ten správný pro použití v Microsoft Flow.

    ![IoT Central ID z adresy URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Je-li aktualizovat název zařízení. Pokud chcete aktualizovat všechny vlastnosti a nastavení zařízení, je třeba vybrat šablonu zařízení zařízení, které chcete aktualizovat v **šablona zařízení** rozevíracího seznamu. Akce dlaždic se rozbalí a zobrazí všechny vlastnosti a nastaveních, pomocí kterých můžete aktualizovat.

    ![Tok pracovní postup aktualizace zařízení](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Vyberte jednotlivé vlastnosti a nastavení, které chcete aktualizovat. V podokně dynamického obsahu zvolte odpovídající vstup z triggeru. V tomto příkladu hodnotu umístění rozšíří se dolů do umožňuje aktualizovat vlastnost umístění zařízení.

1. Nakonec uložte pracovního postupu.

1. Zkuste pracovního postupu v mobilní aplikaci Microsoft Flow. Přejděte **tlačítka** kartě v aplikaci. Měli byste vidět tlačítko -> aktualizace zařízení pracovního postupu. Zadejte vstupy a zařízení v IoT Central aktualizovat!

## <a name="get-device-information-in-a-workflow"></a>Získejte informace o zařízení v pracovním postupu

Můžete získat informace o zařízení s použitím jeho ID **získat zařízení Azure IoT Central -** akce. 
> [!NOTE] 
> **Je nutné použít ID nalezeno v adrese URL** na stránce s podrobnostmi zařízení zařízení, které chcete aktualizovat. V Průzkumníku zařízení seznam zařízení se nenašlo ID zařízení není ten správný pro použití v Microsoft Flow.

Můžete získat informace, jako je název zařízení, název šablony zařízení, hodnoty vlastnosti a hodnoty nastavení k předání do pozdějších akcích v pracovním postupu. Tady je příklad pracovního postupu, který se předá jméno zákazníka hodnotu vlastnosti ze zařízení do Microsoft Teams.

   ![Tok pracovní postup get zařízení](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Spuštění příkazu na zařízení v pracovním postupu
Spustíte příkaz na zařízení, zadat jeho pomocí ID **Azure IoT Central – spusťte příkaz** akce. 

> [!NOTE] 
> **Je nutné použít ID nalezeno v adrese URL** na stránce s podrobnostmi zařízení zařízení, které chcete aktualizovat. V Průzkumníku zařízení seznam zařízení se nenašlo ID zařízení není ten správný pro použití v Microsoft Flow.
    
Můžete si vybrat příkaz pro spuštění a předání v parametrech příkaz v rámci této akce. Tady je příklad pracovního postupu, který spouští příkaz restartování zařízení pomocí tlačítka v mobilní aplikaci Microsoft Flow.

   ![Tok pracovní postup get zařízení](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Odstranit zařízení v pracovním postupu

Můžete odstranit zařízení s použitím jeho ID **odstranit zařízení Azure IoT Central -** akce. 
> [!NOTE] 
> **Je nutné použít ID nalezeno v adrese URL** na stránce s podrobnostmi zařízení zařízení, které chcete aktualizovat. V Průzkumníku zařízení seznam zařízení se nenašlo ID zařízení není ten správný pro použití v Microsoft Flow.

Tady je příklad pracovního postupu, který odstraní zařízení při stisknutí tlačítka v mobilní aplikaci Microsoft Flow.

   ![Pracovní postup zařízení odstranění toku](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže při vytváření připojení ke konektoru Azure IoT Central, tady je několik užitečných tipů k vám.

1. Osobní účty Microsoft (například @hotmail.com, @live.com, @outlook.com domén) nejsou v tuto chvíli nepodporuje. Musíte použít Azure Active Directory (AD) pracovní nebo školní účet.

2. K používání konektoru IoT Central v Microsoft Flow, musíte přihlášení aplikace IoT Central alespoň jednou. V opačném případě aplikace nezobrazí v rozevíracích seznamech aplikací.

3. Pokud při použití účtu Azure AD jsou dochází k chybě, zkuste otevřít prostředí Windows PowerShell a spusťte následující rutiny jako správce.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak používat Microsoft Flow k vytváření pracovních postupů, navrhované dalším krokem je [spravovat zařízení](howto-manage-devices.md).

