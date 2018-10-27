---
title: Vytváření pracovních postupů s IoT Central konektorem v Microsoft Flow | Dokumentace Microsoftu
description: Použití konektoru pro IoT Central v Microsoft Flow spouštět pracovní postupy a vytvořit, aktualizovat a odstraňovat devices v pracovních postupech.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: cc54a3a375fa2780e289a20b699df5d65aaf08cc
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155571"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Vytváření pracovních postupů s IoT Central konektorem v Microsoft Flow

*Toto téma platí pro tvůrce a správce.*

Pomocí Microsoft Flow můžete automatizovat pracovní postupy mezi mnoha aplikacemi a služeb, které využívají firemní uživatelé. Pracovní postupy můžete aktivovat pomocí IoT Central konektoru v Microsoft Flow, když se pravidlo aktivuje v IoT Central. V pracovním postupu aktivovaného IoT Central nebo jakékoli jiné aplikace můžete použít akce v konektoru IoT Central vytvoření zařízení, aktualizovat vlastnosti a nastavení zařízení nebo zařízení odstranit. Podívejte se na [tyto šablony Microsoft Flow](https://aka.ms/iotcentralflowtemplates) IoT Central, které se připojovat k jiným službám, jako jsou mobilní oznámení a Microsoft Teams.

> [!NOTE] 
> Budete potřebovat k přihlášení do Microsoft Flow s Microsoftem osobní nebo pracovní nebo školní účet. Další informace o plánech Microsoft Flow [tady](https://aka.ms/microsoftflowplans).

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Aktivovat pracovní postup, když se aktivuje pravidlo

V této části se dozvíte, jak aktivovat mobilní oznámení v mobilní aplikaci Flow, když se pravidlo aktivuje v IoT Central.

1. Začněte tím, že [vytváření pravidla v IoT Central](howto-create-telemetry-rules.md). Po uložení podmínky pravidla, klikněte na tlačítko **akce Microsoft Flow** jako novou akci. Nové kartě nebo v okně by měla otevřít v prohlížeči, přechod do Microsoft Flow.

    ![Vytvořit novou akci Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.PNG)

1. Přihlaste se k Microsoft Flow. To nemusí být stejný účet jako ten, který použijete v IoT Central. Budete přesměrováni na stránku přehled zobrazující konektoru služby IoT Central připojování k vlastní akci.

1. Přihlaste se do konektoru IoT Central a klikněte na tlačítko **pokračovat**. Budete přesměrováni na Návrhář Microsoft Flow k sestavení pracovního postupu. Pracovní postup obsahuje aktivační událost IoT Central, která má svoji aplikaci a pravidlo už vytvořilo.

1. Zvolte **+ nový krok** a **přidat akci**. V tomto okamžiku můžete přidat jakékoli akce, které chcete do svého pracovního postupu. Jako příklad můžeme odeslat mobilní oznámení. Vyhledejte **oznámení**a zvolte **oznámení – odeslat mobilní oznámení**.

1. V akci zadejte textové pole s Chcete oznámení říct. Můžete zahrnout *dynamický obsah* z vašeho IoT Central pravidla předávání podél důležité informace, jako je název zařízení a časové razítko oznámení.

    > [!NOTE]
    > Klikněte na tlačítko "Další informace" textu v okně dynamického obsahu můžete získat měření a hodnoty vlastností, které aktivuje pravidlo.

    ![Tok akce s otevřeným podoknem dynamické úpravy](./media/howto-add-microsoft-flow/flowdynamicpane.PNG)

1. Po dokončení úprav akci, klikněte na tlačítko **Uložit**. Budete přesměrováni na stránku přehled vašeho pracovního postupu. Zde můžete zobrazit historii spuštění a sdílet ho s dalšími kolegy.

    > [!NOTE]
    > Pokud chcete ostatním uživatelům ve vaší aplikaci IoT Central, chcete-li upravit toto pravidlo, musí ho sdílet s nimi v Microsoft Flow. Přidáte své účty Microsoft Flow jako vlastníky ve svém pracovním postupu.

1. Pokud vrátíte se do vaší aplikace IoT Central, uvidíte, že toto pravidlo obsahuje akci Microsoft Flow oblasti akce.

Můžete vždy začněte vytvářet pracovního postupu pomocí IoT Central konektoru v Microsoft Flow. Pak můžete vybrat, která aplikace IoT Central a pravidlo, které se připojit k.

## <a name="create-a-device-in-a-workflow"></a>Vytvoření zařízení v pracovním postupu

V této části se dozvíte, jak vytvořit nové zařízení v IoT Central při stisknutí tlačítka na mobilním zařízení pomocí mobilní aplikace Microsoft Flow. Tato akce ve službě Flow můžete integrovat systémy ERP, jako je Dynamics IoT Central tak, že vytvoříte nové zařízení, když se zařízení přidá jinde.

1. Začněte vytvořením prázdné pracovního postupu v Microsoft Flow.

1. Vyhledejte **tlačítko tok pro mobilní zařízení** jako trigger.

1. V této aktivační události, přidat textový vstup volá **název zařízení**. Změnit popis text, který má být **zadejte název zařízení novým zařízením**.

1. Přidejte novou akci. Hledat **vytvoření zařízení Azure IoT Central -** akce.

1. Vyberte aplikaci a zvolte zařízení šablonu pro vytvoření zařízení z v rozevíracích seznamech. Zobrazí se vám akce po rozbalení zobrazují všechny vlastnosti a nastavení zařízení.

1. Vyberte pole název zařízení. V podokně dynamického obsahu vyberte **název zařízení**. Tato hodnota se předá ze vstupu, který uživatel zadá přes mobilní aplikaci a bude název nové zařízení na IoT Central. Jediné povinné pole v tomto příkladu je název zařízení označená červenou hvězdičkou. Jiné šablony zařízení může mít více povinná pole, které je potřeba vyplnit pro vytvoření nového zařízení.

    ![Tok vytvořit dynamické podokna akcí zařízení](./media/howto-add-microsoft-flow/flowcreatedevice.PNG)
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

1. Vyberte aplikace z rozevíracího seznamu. Teď budete potřebovat ID existující zařízení, které chcete aktualizovat. V prohlížeči adresu URL můžete získat ID zařízení IoT Central.

    ![ID zařízení Průzkumník zařízení IoT Central](./media/howto-add-microsoft-flow/iotcdeviceid.PNG)

1. Je-li aktualizovat název zařízení. Pokud chcete aktualizovat všechny vlastnosti a nastavení zařízení, je třeba vybrat šablonu zařízení zařízení, které chcete aktualizovat v **šablona zařízení** rozevíracího seznamu. Akce dlaždic se rozbalí a zobrazí všechny vlastnosti a nastaveních, pomocí kterých můžete aktualizovat.

    ![Tok pracovní postup aktualizace zařízení](./media/howto-add-microsoft-flow/flowupdatedevice.PNG)

1. Vyberte jednotlivé vlastnosti a nastavení, které chcete aktualizovat. V podokně dynamického obsahu zvolte odpovídající vstup z triggeru. V tomto příkladu hodnotu umístění rozšíří se dolů do umožňuje aktualizovat vlastnost umístění zařízení.

1. Nakonec uložte pracovního postupu.

1. Zkuste pracovního postupu v mobilní aplikaci Microsoft Flow. Přejděte **tlačítka** kartě v aplikaci. Měli byste vidět tlačítko -> aktualizace zařízení pracovního postupu. Zadejte vstupy a zařízení v IoT Central aktualizovat!

## <a name="delete-a-device-in-a-workflow"></a>Odstranit zařízení v pracovním postupu

Můžete odstranit zařízení s jeho pomocí ID zařízení **odstranit zařízení Azure IoT Central -** akce. Tady je příklad pracovního postupu, který odstraní zařízení při stisknutí tlačítka v mobilní aplikaci Microsoft Flow.

   ![Pracovní postup zařízení odstranění toku](./media/howto-add-microsoft-flow/flowdeletedevice.PNG)
    
## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže při vytváření připojení ke konektoru Azure IoT Central, tady je několik užitečných tipů k vám.

1. Osobní účty Microsoft (například @hotmail.com, @live.com, @outlook.com domén) nejsou v tuto chvíli nepodporuje. Musíte použít AAD pracovní nebo školní účet.

2. K používání konektoru IoT Central v Microsoft Flow, musíte přihlášení aplikace IoT Central alespoň jednou. V opačném případě aplikace nezobrazí v rozevíracích seznamech aplikací.

3. Pokud se vám zobrazuje chybu při používání účet AAD, otevřete prostředí Windows PowerShell a spusťte následující rutiny jako správce.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak pomocí Microsoft Flow můžete vytvářet pracovní postupy, doporučené dalším krokem je [spravovat zařízení](howto-manage-devices.md).

