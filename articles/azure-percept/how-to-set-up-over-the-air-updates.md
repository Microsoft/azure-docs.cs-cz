---
title: Nastavení IoT Hub Azure pro nasazení v rámci aktualizací Air
description: Přečtěte si, jak nakonfigurovat Azure IoT Hub pro nasazení aktualizací přes Air do Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662321"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Jak nastavit Azure IoT Hub pro nasazení přes aktualizace Air do Azure Percept DK
Zajistěte, aby se Azure Percept DK v bezpečí a v aktuálním stavu používaly při použití aktualizací přes Air. V několika jednoduchých krocích budete moct nastavit prostředí Azure pomocí aktualizace zařízení pro IoT Hub a nasadit nejnovější aktualizace do Azure Percept DK.

## <a name="create-a-device-update-account"></a>Vytvoření účtu aktualizace zařízení

1. Přejít na [Azure Portal](https://portal.azure.com) a přihlásit se pomocí účtu Azure, který používáte se službou Azure Percept 

1. V okně hledání v horní části stránky začněte zadávat text aktualizace zařízení pro IoT Hub.

1. Vyberte možnost **aktualizace zařízení pro centra IoT** , jak se zobrazí v okně hledání.

1. Klikněte na tlačítko **+ Přidat** v levé horní části stránky.

1. Vyberte předplatné Azure a skupinu prostředků, která je přidružená k vašemu zařízení Azure Percept (tady je IoT Hub umístění pro registraci).

1. Zadejte název a umístění pro účet aktualizace zařízení.

1. Zkontrolujte podrobnosti a pak vyberte **zkontrolovat + vytvořit**.
 
1. Po dokončení nasazení klikněte na **Přejít k prostředku**.
 
## <a name="create-a-device-update-instance"></a>Vytvoření instance aktualizace zařízení
Teď vytvořte instanci v rámci aktualizace zařízení pro IoT Hub účet.

1. V části aktualizace zařízení pro prostředek IoT Hub klikněte na **instance** v části **Správa instancí**.
 
1. Klikněte na **+ vytvořit**, zadejte název instance a vyberte IoT Hub přidružené k vašemu zařízení Azure Percept (to znamená, že se vytvořilo během připojování). Dokončení tohoto může trvat několik minut.
 
1. Klikněte na **Vytvořit**.

## <a name="configure-iot-hub"></a>Konfigurace IoT Hub

1. Na stránce **instance** správy instancí počkejte, než se instance aktualizace zařízení přesune do stavu **úspěšné** . Kliknutím na ikonu **aktualizace** vedle pole **Odstranit** aktualizujte stav.
 
1. Vyberte instanci, která je pro vás vytvořená, a pak klikněte na **konfigurovat IoT Hub**. V levém podokně vyberte Souhlasím, **abyste provedli tyto změny** , a klikněte na **aktualizovat**.
 
1. Počkejte, až se proces úspěšně dokončí.
 
## <a name="configure-access-control-roles"></a>Konfigurace rolí řízení přístupu
Poslední krok vám umožní udělit uživatelům oprávnění k publikování a nasazování aktualizací.

1. V aktualizaci zařízení pro prostředek IoT Hub klikněte na **řízení přístupu (IAM)** .
 
2. Klikněte na **+ Přidat** a pak vyberte **Přidat přiřazení role** .
 
3. Jako **role** vyberte **správce aktualizace zařízení**. Pro **přiřazení přístupu k** vybranému **uživateli, skupině nebo principu služby**. V části **Vyberte** vyberte svůj účet nebo účet osoby, která bude nasazovat aktualizace. Pak klikněte na **Uložit**. 

    > [!TIP]
    > Pokud chcete dát více lidem v přístupu ke svojí organizaci, můžete tento krok zopakovat a dát každému z těchto uživatelů **správce aktualizace zařízení**.

## <a name="next-steps"></a>Další kroky

Nyní jste nastavili a mohli [aktualizovat službu Azure Percept dev Kit přes Air](./how-to-update-over-the-air.md) pomocí aktualizace zařízení IoT Hub. Přejděte do IoT Hub Azure, kterou používáte pro vaše zařízení Azure Percept.