---
title: Nasazení řešení vzdáleného monitorování - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak zřídit akcelerátoru řešení vzdáleného monitorování z azureiotsuite.com.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 42f6afcd3cb6880ba6c9cdd2a51e2a3e9ff2c2d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626849"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Nasazení řešení akcelerátoru vzdálené monitorování

V tomto kurzu se dozvíte, jak zřídit akcelerátoru řešení vzdáleného monitorování. Nasazení řešení od azureiotsuite.com. Můžete taky nasadit řešení pomocí rozhraní příkazového řádku, další informace o této možnosti najdete [nasadit řešení akcelerátoru z příkazového řádku](iot-accelerators-remote-monitoring-deploy-cli.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace řešení akcelerátoru
> * Nasazení řešení akcelerátoru
> * Přihlaste se k řešení akcelerátoru

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít aktivní předplatné služby Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Nasazení řešení akcelerátoru

Před nasazením řešení akcelerátoru k předplatnému Azure, musíte zvolit některé možnosti konfigurace:

1. Přihlaste se k [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) pomocí svých přihlašovacích údajů účtu Azure.

1. Klikněte na tlačítko **akci nyní** na **vzdálené monitorování** dlaždici.

    ![Zvolte vzdálené monitorování](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. Na **vytvořit vzdálené monitorování řešení** stránky, zadejte **název řešení** pro vaše akcelerátoru řešení vzdáleného monitorování.

1. Vyberte **základní** nebo **standardní** nasazení. Pokud nasazujete řešení se dozvíte, jak to funguje nebo chcete spustit ukázku, zvolte **základní** možnost minimalizovat náklady.

1. Vyberte buď **Java** nebo **.NET** jako jazyk. Všechny mikroslužeb jsou k dispozici jako implementace Java nebo .NET.

1. Zkontrolujte **podrobnosti řešení** panel pro další informace o možnostech konfigurace.

1. Vyberte **Předplatné** a **Oblast**, které chcete při zřizování řešení použít.

1. Kliknutím na tlačítko **Vytvořit řešení** zahájíte proces zřizování. Tento proces obvykle trvá několik minut:

    ![Podrobnosti řešení vzdáleného monitorování](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Informace o odstraňování potíží, najdete v části [co dělat, když se nasazení nezdaří](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) v úložišti GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Přihlaste se k řešení akcelerátoru

Po dokončení procesu zřizování přihlášením k vaší akcelerátoru řešení vzdáleného monitorování.

1. Na **zřízení řešení** vyberte nové řešení vzdáleného monitorování:

    ![Zvolte nové řešení](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. Zobrazení informací o řešení vzdáleného monitorování v panelu, který se zobrazí. Zvolte **řídicí panel řešení** pro připojení k řešení vzdáleného monitorování.

    > [!NOTE]
    > Když skončíte s ním můžete odstranit řešení vzdáleného monitorování z tohoto panelu.

    ![Panel řešení](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. Řídicí panel řešení vzdáleného monitorování se zobrazí v prohlížeči.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace řešení akcelerátoru
> * Nasazení řešení akcelerátoru
> * Přihlaste se k řešení akcelerátoru

Teď, když jste nasadili řešení vzdáleného monitorování, dalším krokem je [prozkoumejte možnosti řídicí panel řešení](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->