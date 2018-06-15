---
title: Nasazení řešení vzdáleného monitorování - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak zřídit vzdálené monitorování akcelerátoru řešení z azureiotsuite.com.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: eb78ce91755c43f1c6fedf62a70238df911b940f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33773628"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Nasazení vzdálené monitorování řešení akcelerátoru

V tomto kurzu se dozvíte, jak zřídit vzdálené monitorování akcelerátoru řešení. Nasazení řešení od azureiotsuite.com. Můžete taky nasadit řešení pomocí rozhraní příkazového řádku, další informace o této možnosti najdete [nasadit řešení akcelerátoru z příkazového řádku](iot-suite-remote-monitoring-deploy-cli.md).

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

    ![Zvolte vzdálené monitorování](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. Na **vytvořit vzdálené monitorování řešení** stránky, zadejte **název řešení** pro vaše vzdálené monitorování akcelerátoru řešení.

1. Vyberte **základní** nebo **standardní** nasazení. Pokud nasazujete řešení se dozvíte, jak to funguje nebo chcete spustit ukázku, zvolte **základní** možnost minimalizovat náklady.

1. Vyberte buď **Java** nebo **.NET** jako jazyk. Všechny mikroslužeb jsou k dispozici jako implementace Java nebo .NET.

1. Zkontrolujte **podrobnosti řešení** panel pro další informace o možnostech konfigurace.

1. Vyberte **Předplatné** a **Oblast**, které chcete při zřizování řešení použít.

1. Kliknutím na tlačítko **Vytvořit řešení** zahájíte proces zřizování. Tento proces obvykle trvá několik minut:

    ![Podrobnosti řešení vzdáleného monitorování](media/iot-suite-remote-monitoring-deploy/createform.png)

Informace o odstraňování potíží, najdete v části [co dělat, když se nasazení nezdaří](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) v úložišti GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Přihlaste se k řešení akcelerátoru

Po dokončení procesu zřizování přihlášením k vaší vzdálené monitorování akcelerátoru řešení.

1. Na **zřízení řešení** vyberte vaší nové řešení vzdáleného monitorování:

    ![Zvolte nové řešení](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. Zobrazení informací o řešení vzdáleného monitorování v panelu, který se zobrazí. Zvolte **řídicí panel řešení** pro připojení k řešení vzdáleného monitorování.

    > [!NOTE]
    > Když skončíte s ním můžete odstranit řešení vzdáleného monitorování z tohoto panelu.

    ![Panel řešení](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. Řídicím panelu řešení vzdáleného monitorování se zobrazí v prohlížeči.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace řešení akcelerátoru
> * Nasazení řešení akcelerátoru
> * Přihlaste se k řešení akcelerátoru

Teď, když jste nasadili řešení vzdáleného monitorování, dalším krokem je [prozkoumejte možnosti řídicí panel řešení](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->