---
title: Registrovat nové zařízení Azure IoT okraj (VS Code) | Microsoft Docs
description: Visual Studio Code použít k vytvoření nového zařízení IoT Edge ve službě Azure IoT hub
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7902461f58df1b4fe0c3ed3b577f668fe8be4cc2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035825"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registrovat nové zařízení Azure IoT Edge z Visual Studio Code

Zařízení IoT můžete používat s Azure IoT Edge, budete muset registraci je službou IoT hub. Po registraci zařízení se zobrazí připojovací řetězec, který umožňuje nastavit zařízení pro úlohy okraj. 

Tento článek ukazuje, jak registrovat nové zařízení IoT Edge pomocí kódu v jazyce Visual Studio (VS Code). Provádějí většinu operací v produktu VS Code několika způsoby. Tento článek používá v Exploreru, ale můžete také použít příkaz palety spouštět většinu kroků. 

## <a name="prerequisites"></a>Požadavky

* [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se k službě IoT hub.

Azure IoT rozšíření pro Visual Studio Code můžete použít k provádění operací službou IoT hub. Pro tyto operace pro práci potřebujete přihlásit k účtu Azure a vyberte službu IoT hub, kterou právě pracujete.

1. Otevřete v sadě Visual Studio Code **Explorer** zobrazení.

2. V dolní části v Exploreru, rozbalte **zařízení Azure IoT Hub** části. 

   ![Rozbalte zařízení Azure IoT Hub](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Klikněte na **...**  v **zařízení Azure IoT Hub** záhlaví části. Pokud nevidíte se třemi tečkami, pozastavte ukazatel myši nad záhlaví. 

4. Zvolte **vyberte IoT Hub**.

5. Pokud nejste přihlášení k účtu Azure, postupujte podle pokynů k tomu. 

6. Vyberte své předplatné Azure. 

7. Vyberte služby IoT hub. 

## <a name="create-a-device"></a>Vytvoření zařízení

1. V Průzkumníku kód VS, rozbalte **zařízení Azure IoT Hub** části. 

2. Klikněte na **...**  v **zařízení Azure IoT Hub** záhlaví části. Pokud nevidíte se třemi tečkami, pozastavte ukazatel myši nad záhlaví. 

3. Vyberte **vytvoření zařízení IoT Edge**. 

4. V textovém poli, které se otevře poskytněte zařízení ID. 

V dialogovém okně výstupu uvidíte výsledky příkazu. Vytisknout informace o zařízení, což zahrnuje **deviceId** kterou jste zadali a **connectionString** používané k připojení fyzického zařízení do služby IoT hub. 

## <a name="view-all-devices"></a>Zobrazit všechna zařízení

Všechna zařízení, které se připojují ke službě IoT hub jsou uvedeny v **zařízení Azure IoT Hub** části Průzkumník kódu služby Visual Studio. Zařízení IoT hraniční jsou odlišit od jiných hraniční zařízení s jinou ikonu a fakt, že můžete rozbalit a zobrazit jeho moduly nasazené na každém zařízení. 

   ![Zobrazení zařízení v produktu VS Code](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Načtení připojovacího řetězce

Pokud jste připravení nastavit vaše zařízení, je potřeba připojovací řetězec, který odkazuje fyzického zařízení s svou identitu ve službě IoT hub.

1. Klikněte pravým tlačítkem na ID zařízení v **zařízení Azure IoT Hub** části. 
2. Vyberte **zkopírujte připojovací řetězec zařízení**.

   Připojovací řetězec je zkopírovat do schránky. 

Můžete také vybrat **získat informace o zařízení** v místní nabídce zobrazíte všechny informace o zařízení, včetně připojovací řetězec, v okně výstupu. 


## <a name="next-steps"></a>Další postup

Zjistěte, jak [moduly nasadit do zařízení s Visual Studio Code](how-to-deploy-modules-vscode.md).
