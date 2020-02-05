---
title: Vytváření pracovních postupů pomocí konektoru IoT Central v Azure Logic Apps | Microsoft Docs
description: Pomocí konektoru IoT Central v Azure Logic Apps můžete aktivovat pracovní postupy a vytvářet, aktualizovat a odstraňovat zařízení v pracovních postupech.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3625d7374090bca73e2f054f7da4b58e951e7719
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990262"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Vytváření pracovních postupů pomocí konektoru IoT Central v Azure Logic Apps

*Toto téma se týká tvůrců a správců.*

Pomocí Azure Logic Apps můžete vytvářet automatizované škálovatelné pracovní postupy, které integrují aplikace a data napříč službami Cloud Services a místními systémy. Azure Logic Apps má mnoho konektorů napříč mnoha službami Azure, službami Microsoftu a dalšími produkty typu software jako služba (SaaS). Pomocí konektoru IoT Central v Azure Logic Apps můžete aktivovat pracovní postupy, když se pravidlo aktivuje v IoT Central. Pomocí akcí v konektoru IoT Central můžete také vytvořit zařízení, aktualizovat vlastnosti a nastavení zařízení nebo odstranit zařízení.

V Microsoft Flow můžete použít konektor IoT Central. Azure Logic Apps i Microsoft Flow jsou první integrační služby pro návrháře, ale cílí na mírně různé cílové skupiny. Flow umožňuje každému pracovnímu procesu Office vytvářet pracovní postupy, které potřebují. Logic Apps pomáhá IT specialistům vytvářet integrace, které potřebují k připojení dat. Porovnejte Flow a Logic Apps [sem](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Přečtěte si, jak vytvářet pracovní postupy pomocí konektoru IoT Central [v Microsoft Flow.](howto-add-microsoft-flow.md)

## <a name="prerequisites"></a>Požadavky

- Aplikace vytvořená pomocí standardního cenového plánu
- Účet a předplatné Azure pro vytváření a správu Logic Apps

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Aktivovat pracovní postup, když se aktivuje pravidlo

V této části se dozvíte, jak odeslat zprávu do Microsoft teams, když se aktivuje pravidlo. Pracovní postup můžete nakonfigurovat tak, aby používal jiné konektory k provádění akcí, jako je například odeslání události do centra událostí, vytvoření nové pracovní položky Azure DevOps nebo vložení nového řádku do systému SQL Server.

1. Začněte [vytvořením pravidla v IoT Central](howto-create-telemetry-rules.md). Po uložení podmínek pravidla vyberte dlaždici **Azure Logic Apps** jako novou akci. Vyberte **vytvořit v Azure Portal**. Přejdete na Azure Portal pro vytvoření nové aplikace logiky. Možná se budete muset přihlásit k účtu Azure.

1. Zadejte požadované informace pro vytvoření nové aplikace logiky. Můžete zvolit předplatné Azure, do kterého se má nová aplikace logiky zřídit. Nemusí se jednat o stejné předplatné, na které se IoT Central aplikace vytvořila. Vyberte **Vytvořit**.

    ![Vytvoření aplikace logiky v Azure Portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Po úspěšném vytvoření aplikace logiky se automaticky přejdou na návrháře Logic Apps. Vyberte **prázdnou aplikaci logiky**. 

    ![Vytvoření prázdné aplikace logiky](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. V Návrháři vyhledejte "IoT Central" a vyberte, **kdy se** má aktivovat pravidlo, které se aktivuje. Přihlaste se ke konektoru pomocí účtu, pomocí kterého se přihlašujete do aplikace IoT Central.

    ![Přihlášení ke konektoru IoT Central](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Po úspěšném přihlášení by se měla zobrazit pole. V rozevíracích seznamech vyberte **aplikaci** a **pravidlo** .

    ![Vybrat aplikaci a pravidlo](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Přidejte novou akci. Vyhledejte **týmy pro odesílání zpráv** a v konektoru Microsoft Teams Connector vyberte **Odeslat zprávu** . Přihlaste se ke konektoru pomocí účtu, který používáte v Microsoft Teams.

1. V akci vyberte **tým** a **kanál**. Do pole **zpráva** zadejte, co chcete v každé zprávě vyslovit. Můžete zahrnout *dynamický obsah* z pravidla IoT Central, který do vašeho oznámení předává důležité informace, jako je název zařízení a časové razítko.
    > [!NOTE]
    > Pokud chcete získat hodnoty měření a vlastností, které pravidlo aktivovaly, vyberte **Zobrazit další** text v okně dynamického obsahu.

    ![Akce úpravy aplikace logiky s otevřeným dynamickým podoknem](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Po dokončení úprav akce vyberte **Uložit**.

1. Pokud se vrátíte zpět k vaší IoT Central aplikaci, zobrazí se toto pravidlo s Azure Logic Apps akcí v oblasti akce.

Pracovní postup můžete kdykoli začít vytvářet pomocí konektoru IoT Central v Logic Apps v Azure Portal. Pak můžete zvolit, která IoT Central aplikace a k jakému pravidlu se chcete připojit, a pořád funguje stejným způsobem. Nemusíte pokaždé spouštět na stránce IoT Central Rules.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Vytvoření, aktualizace a odstranění zařízení v pracovním postupu

Při vytváření pracovního postupu ve vaší aplikaci logiky můžete přidat akci pomocí konektoru IoT Central. Dostupné akce jsou **Vytvoření zařízení**, **aktualizace zařízení**a **odstranění zařízení**.

> [!NOTE]
> Pokud chcete **aktualizovat zařízení** a **Odstranit zařízení**, budete potřebovat ID existujícího zařízení, které chcete aktualizovat nebo odstranit. ID zařízení IoT Central můžete získat v **Device Explorer**

![ID zařízení v Průzkumníkovi zařízení IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat Microsoft Flow k sestavování pracovních postupů, je navržený další krok ke [správě zařízení](howto-manage-devices.md).
