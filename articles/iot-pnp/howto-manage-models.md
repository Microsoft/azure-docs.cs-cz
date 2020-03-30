---
title: Správa modelů náhledu IoT Plug and Play v úložišti| Dokumenty společnosti Microsoft
description: Jak spravovat modely funkcí zařízení v úložišti pomocí portálu Azure Certified for IoT Portal, rozhraní příkazového příkazového příkazu Azure a kódu Visual Studia.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159145"
---
# <a name="manage-models-in-the-repository"></a>Správa modelů v úložišti

Úložiště modelu Náhled technologie IoT Plug and Play ukládá modely a rozhraní schopností zařízení. Díky úložišti jsou modely a rozhraní zjistitelné a spotřební vývojáři řešení.

Ke správě úložiště můžete použít tři nástroje:

- Portál Azure Certified for IoT
- Rozhraní příkazového řádku Azure
- Visual Studio Code

## <a name="model-repositories"></a>Modelové repozitáře

Existují dva typy úložiště modelu pro ukládání modelů a rozhraní schopností zařízení:

- Existuje jediné _veřejné úložiště,_ které ukládá modely schopností zařízení a rozhraní pro zařízení v [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat). Toto úložiště také ukládá [běžná rozhraní](./concepts-common-interfaces.md) a [moduly DCM a rozhraní publikovaná společností Microsoft Partners](./howto-onboard-portal.md). Informace o certifikaci zařízení a přidání modelu schopností zařízení do veřejného úložiště naleznete v kurzu [Certifikace zařízení IoT Plug and Play](./tutorial-certification-test.md).
- Existuje více _repozitářů společnosti_. Úložiště společnosti se automaticky vytvoří pro vaši organizaci, když [se nalodinete na portál Azure Certified for IoT](./howto-onboard-portal.md). Úložiště vaší společnosti můžete použít k ukládání modelů a rozhraní schopností zařízení během vývoje a testování.

## <a name="azure-certified-for-iot-portal"></a>Azure certifikovaný pro portál IoT

Na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com)můžete dokončit následující úkoly:

- [Dokončete certifikační proces pro vaše zařízení IoT](./tutorial-certification-test.md).
- Najděte modely funkcí zařízení IoT Plug and Play. Pomocí těchto modelů můžete [rychle vytvářet zařízení připravená pro IoT a integrovat je s řešeními](./quickstart-connect-pnp-device-solution-node.md).

## <a name="azure-cli"></a>Azure CLI

Rozhraní příkazového řádku Azure poskytuje příkazy pro správu modelů a rozhraní schopností zařízení v úložištích veřejných a firemních modelů IoT Plug and Play. Další informace najdete v [tématu Instalace a použití rozšíření Azure IoT pro Průvodce příkazovými příkazy KOnVu Azure.](./howto-install-pnp-cli.md)

## <a name="visual-studio-code"></a>Visual Studio Code

Chcete-li otevřít zobrazení **úložiště modelu** v kódu sady Visual Studio.

1. Otevřete kód Visual Studia, použijte **Ctrl+Shift+P**, zadejte a vyberte **IoT Plug and Play: Open Model Repository**.

1. Můžete otevřít úložiště **veřejných modelů** nebo **otevřít úložiště organizačního modelu**. Pro úložiště modelu společnosti je třeba zadat připojovací řetězec úložiště modelu. Tento připojovací řetězec najdete na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) na kartě **Připojovací řetězce** pro **úložiště společnosti**.

1. Nová karta otevře zobrazení **Úložiště modelů.**

    Toto zobrazení slouží k přidávání, stahování a odstraňování modelů a rozhraní schopností zařízení. Pomocí filtru můžete najít určité položky v seznamu.

1. Chcete-li přepínat mezi úložištěm firemních modelů a úložištěm veřejných modelů, použijte **kombinaci kláves Ctrl+Shift+P**, zadejte a vyberte **IoT Plug and Play: Odhlásit úložiště modelů**. Pak použijte příkaz **IoT Plug and Play: Open Model Repository.**

> [!NOTE]
> V Kódu VS je úložiště veřejného modelu jen pro čtení. Partneři Microsoftu můžou aktualizovat veřejné úložiště na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Další kroky

Dalším navrhovaným krokem je naučit se [odesílat zařízení IoT Plug and Play pro certifikaci](tutorial-certification-test.md).
