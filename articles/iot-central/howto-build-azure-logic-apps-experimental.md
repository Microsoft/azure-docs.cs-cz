---
title: Vytváření pracovních postupů s konektorem IoT Central ve službě Azure Logic Apps | Dokumentace Microsoftu
description: Použití konektoru pro IoT Central v Azure Logic Apps spouštět pracovní postupy a vytvořit, aktualizovat a odstraňovat devices v pracovních postupech.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2aced85828e5563b838af327151b2b94bcafb931
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773202"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Vytváření pracovních postupů s konektorem IoT Central ve službě Azure Logic Apps

*Toto téma platí pro tvůrce a správce.*

Použijte Azure Logic Apps k vytváření automatizovaných škálovatelných pracovních postupů, které integrují aplikace a data napříč cloudovými službami a místními systémy. Služba Azure Logic Apps se spoustou konektorů napříč řadou služeb Azure, služby a produkty Software-As-A-Service (SaaS). Pracovní postupy můžete aktivovat pomocí konektoru rozhraní IoT Central ve službě Azure Logic Apps, když se pravidlo aktivuje v IoT Central. Akce, které můžete použít také v konektoru IoT Central vytvoření zařízení, aktualizovat vlastnosti a nastavení zařízení nebo zařízení odstranit. 

V Microsoft Flow můžete použít konektor IoT Central. Azure Logic Apps a Microsoft Flow jsou založené na návrháři integrační služby, ale mírně odlišné cílové skupiny. Flow umožňuje libovolné pracovníkovi vytvářet obchodní pracovní postupy, které potřebují. Logic Apps umožňuje odborníkům na IT na integrace, které potřebují k připojení dat sestavení. Porovnání služeb Flow a Logic Apps [tady](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Další informace o tom, jak vytvářet pracovní postupy s IoT Central konektorem v Microsoft Flow [tady](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). 

## <a name="prerequisites"></a>Požadavky

- Aplikace s průběžnými platbami
- Účet Azure a přístup k vytvoření a Správa aplikací logiky

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Aktivovat pracovní postup, když se aktivuje pravidlo

Tato část ukazuje, jak publikovat zprávu do Microsoft Teams, když se aktivuje pravidlo. Můžete nakonfigurovat svůj pracovní postup pro jiné konektory slouží k provádění akcí, například odeslání události do vašeho centra událostí vytvořit novou pracovní položku Azure DevOps a vložit nový řádek v SQL serveru.

1. Začněte tím, že [vytváření pravidla v IoT Central](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Po uložení podmínky pravidla, klikněte na tlačítko **Azure Logic Apps** dlaždici jako novou akci. Klikněte na tlačítko **vytvořit na webu Azure portal**. Budete přesměrováni na web Azure Portal k vytvoření nové aplikace logiky. Budete se muset přihlásit ke svému účtu Azure.

1. Zadejte požadované informace a vytvořte novou aplikaci logiky. Můžete zřídit nové aplikace logiky do předplatného Azure. Nemusí být stejné jako předplatné, které vaše aplikace IoT Central byl vytvořen v. Klikněte na možnost **Vytvořit**.

    ![Vytvoření aplikace logiky na webu Azure portal](./media/howto-build-azure-logic-apps-experimental/createinazureportal.png)

1. Po úspěšném vytvoření aplikace logiky budete automaticky přesměrováni do návrháře pro Logic Apps. Klikněte na tlačítko **prázdná aplikace logiky**. 

    ![Vytvoření prázdné aplikace logiky](./media/howto-build-azure-logic-apps-experimental/blanklogicapp.png)

1. V návrháři, vyhledejte "iot central" a zvolte **když se aktivuje pravidlo** aktivační události. Přihlaste se pomocí účtu, kterým jste přihlášení do aplikace IoT Central pomocí konektoru. 

    ![Přihlaste se do konektoru IoT Central](./media/howto-build-azure-logic-apps-experimental/addtrigger.png)

1. Po úspěšném přihlášení měli byste vidět pole se zobrazí. Vyberte **aplikace** a **pravidlo** z rozevíracích seznamech.

    ![Vyberte aplikaci a pravidla](./media/howto-build-azure-logic-apps-experimental/pickappandrule.png)

1. Přidejte novou akci. Vyhledejte **publikovat na teams zprávu** a zvolte **odeslání zprávy** z konektoru Microsoft Teams. Přihlaste se pomocí účtu, který používáte v Microsoft Teams konektoru. 

1. V akci, zvolte **týmu** a **kanálu**. Vyplňte **zpráva** pole co má každá zpráva říct. Můžete zahrnout *dynamický obsah* z vašeho IoT Central pravidla předávání podél důležité informace, jako je název zařízení a časové razítko oznámení.
    > [!NOTE]
    > Klikněte na tlačítko "Další informace" textu v okně dynamického obsahu můžete získat měření a hodnoty vlastností, které aktivuje pravidlo.

    ![Otevřete úpravy akce aplikace logiky s podoknem dynamické](./media/howto-build-azure-logic-apps-experimental/buildworkflow.png)

1. Po dokončení úprav akci, klikněte na tlačítko **Uložit**.

1. Pokud vrátíte se do vaší aplikace IoT Central, uvidíte, že toto pravidlo obsahuje akci Azure Logic Apps v rámci oblasti akce.

Můžete vždy začněte vytvářet pracovního postupu pomocí IoT Central konektor v Logic Apps na webu Azure Portal. Pak můžete vybrat která aplikace IoT Central a pravidlo, které se připojit k a budou i nadále fungovat stejným způsobem. Není nutné spustit pokaždé, když ze stránky pravidla IoT Central.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Vytvářet, aktualizovat a odstraňovat zařízení v pracovním postupu

Při vytváření pracovního postupu ve vaší aplikaci logiky, můžete přidat akci s použitím konektoru IoT Central. Vás bude **vytvoření zařízení**, **aktualizovat zařízení**, a **odstranit zařízení**.

> [!NOTE]
> Pro **aktualizovat zařízení** a **odstranit zařízení**, budete potřebovat ID existující zařízení, které chcete aktualizovat nebo odstranit. Můžete získat ID zařízení IoT Central **Device Explorer**

![ID zařízení Průzkumník zařízení IoT Central](./media/howto-build-azure-logic-apps-experimental/iotcdeviceid.png)


## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak pomocí Microsoft Flow můžete vytvářet pracovní postupy, doporučené dalším krokem je [spravovat zařízení](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
