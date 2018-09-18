---
title: Monitorování připojení zařízení pomocí Průzkumníka Azure IoT Central
description: Monitorování zprávy typu zařízení a sledovat změny dvojče zařízení pomocí IoT Central Průzkumník rozhraní příkazového řádku.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 962f394607d20869bf00db624533996b0060eaf2
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986305"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorování připojení zařízení pomocí Průzkumníka Azure IoT Central

*Toto téma platí pro tvůrce a správce.*

Pomocí IoT Central Průzkumník rozhraní příkazového řádku můžete zobrazit zprávy zařízení odesílají do IoT Central a sledovat změny v dvojčeti služby IoT Hub. Získávejte hlubší vhled do stavu připojení zařízení a diagnostikovat problémy zprávy typu zařízení nedosáhne cloudu nebo přestane reagovat na změny dvojčete zařízení můžete použít tento nástroj open source.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Navštivte iotc-explorer úložišti v Githubu](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Požadavky
+ Verze Node.js 8.x nebo vyšší - https://nodejs.org
+ Budete muset požádat správce vaší aplikace k vygenerování přístupového tokenu pro použití v iotc-explorer

## <a name="installing-iotc-explorer"></a>Instalace iotc-explorer

Spusťte následující příkaz z příkazového řádku pro instalaci:

```
npm install -g iotc-explorer
```

> [!NOTE]
> Obvykle bude třeba spustit příkaz install s `sudo` v prostředí na bázi systému Unix.

## <a name="running-iotc-explorer"></a>Spuštění iotc-explorer

Níže jsou uvedeny některé příkazy a běžné možnosti, které můžete spustit, když pomocí `iotc-explorer`. Chcete-li zobrazit úplnou sadu příkazů a možností, můžete předat `--help` k `iotc-explorer` nebo některý z jeho dílčích příkazů.

### <a name="login"></a>Přihlásit

Předtím, než můžete začít, musíte mít správce vaší aplikace IoT Central pro získání přístupového tokenu pro použití. Správce má následující kroky:
1. Přejděte na **správy/přístupové tokeny**. 
1. Klikněte na tlačítko **generovat**.
![Snímek obrazovky stránky tokenu přístupu](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Zadejte název tokenu, klikněte na tlačítko **Další**, a **zkopírujte hodnotu Token**.
    > [!NOTE]
    > Hodnota tokenu pouze zobrazí jednou, tak, aby ho musíte zkopírovat před jeho zavřením dialogu. Po zavření dialogového okna, to se nikdy nezobrazí znovu.

    ![Zkopírovat snímek dialogové okno tokenu přístupu](media/howto-use-iotc-explorer/copyaccesstoken.png)

Potom můžete tento token pro přihlášení k rozhraní příkazového řádku spuštěním:

```sh
iotc-explorer login "<Token value>"
```

Pokud by místo toho máte token uchovávaných v historii vašeho prostředí, můžete nechat token out a místo toho ho po zobrazení výzvy zadat:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorování zprávy typu zařízení

Můžete se podívat zprávy přicházející z určitého zařízení nebo všechna zařízení ve vaší aplikaci pomocí `monitor-messages` příkazu. Tím se spustí, který bude nepřetržitě výstup nové zprávy, jak ve sledování.

Sledovat všechna zařízení ve vaší aplikaci, spusťte následující příkaz:

```
iotc-explorer monitor-messages
```
Výstup: ![výstup příkazu monitorování zprávy](media/howto-use-iotc-explorer/monitormessages.PNG)

Ke sledování konkrétních zařízení, stačí přidat na konec příkazu ID zařízení:

```
iotc-explorer monitor-messages <your-device-id>
```

Je také možné výstupu formátu více vhodných pro počítač přidáním příkazu `--raw` možnost příkazu:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Získáte, dvojče zařízení

Můžete použít `get-twin` příkaz má být získán obsah dvojčete zařízení IoT Central. Chcete-li tak učinit, spusťte následující příkaz:

```
iotc-explorer get-twin <your-device-id>
```

Výstup: ![výstup příkazu get-twin](media/howto-use-iotc-explorer/getdevicetwin.PNG)

Stejně jako u `monitor-messages`, můžete získat další počítač popisný výstup předáním `--raw` možnost:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak pomocí Průzkumníka IoT Central, navrhované dalším krokem je prozkoumat [správy zařízení IoT Central](howto-manage-devices.md).
