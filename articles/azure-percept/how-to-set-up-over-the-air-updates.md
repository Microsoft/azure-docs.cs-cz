---
title: Nastavení IoT Hub Azure pro nasazení přes Air aktualizace
description: Přečtěte si, jak nakonfigurovat Azure IoT Hub pro nasazení aktualizací přes Air do Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064116"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Jak nastavit Azure IoT Hub pro nasazení přes aktualizace Air do Azure Percept DK

Zajistěte, aby se Azure Percept DK v bezpečí a v aktuálním stavu používaly při použití aktualizací přes Air. V několika jednoduchých krocích budete moct nastavit prostředí Azure pomocí aktualizace zařízení pro IoT Hub a nasadit nejnovější aktualizace do Azure Percept DK.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste sadu SDK pro vývoj k síti Wi-Fi, vytvořili IoT Hub a připojili jste sadu pro vývoj do IoT Hub

## <a name="create-a-device-update-account"></a>Vytvoření účtu aktualizace zařízení

1. Přejít na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu Azure, který používáte se službou Azure Percept.

1. Na panelu hledání v horní části stránky zadejte **aktualizace zařízení pro IoT Hub**.

1. Vyberte možnost **aktualizace zařízení pro IoT Hub** , když se objeví na panelu hledání.

1. Klikněte na tlačítko **+ Přidat** v levé horní části stránky.

1. Vyberte **předplatné Azure** a **skupinu prostředků** přidruženou k vašemu zařízení azure Percept a jeho IoT Hub.

1. Zadejte **název** a **umístění** pro účet aktualizace zařízení.

1. Přečtěte si podrobnosti a vyberte **zkontrolovat + vytvořit**.

1. Po dokončení nasazení klikněte na **Přejít k prostředku**.

## <a name="create-a-device-update-instance"></a>Vytvoření instance aktualizace zařízení

1. V části aktualizace zařízení pro prostředek IoT Hub klikněte na **instance** v části **Správa instancí**.

1. Klikněte na **+ vytvořit**, zadejte název instance a vyberte IoT Hub přidružené k vašemu zařízení Azure Percept. Dokončení tohoto může trvat několik minut.

1. Klikněte na **Vytvořit**.

## <a name="configure-iot-hub"></a>Konfigurace IoT Hub

1. Na stránce **instance** správy instancí počkejte, než se instance aktualizace zařízení přesune do stavu **úspěšné** . Kliknutím na ikonu **aktualizovat** aktualizujte stav.

1. Vyberte instanci, která je pro vás vytvořená, a klikněte na **konfigurovat IoT Hub**. V levém podokně vyberte Souhlasím, **abyste provedli tyto změny** , a klikněte na **aktualizovat**.

1. Počkejte, až se proces úspěšně dokončí.

## <a name="configure-access-control-roles"></a>Konfigurace rolí řízení přístupu

Poslední krok vám umožní udělit uživatelům oprávnění k publikování a nasazování aktualizací.

1. V aktualizaci zařízení pro prostředek IoT Hub klikněte na **řízení přístupu (IAM)**.

1. Klikněte na **+ Přidat** a pak vyberte **Přidat přiřazení role**.

1. Jako **role** vyberte **správce aktualizace zařízení**. Pro **přiřazení přístupu k** vybranému **uživateli, skupině nebo principu služby**. V části **Vybrat** vyberte svůj účet nebo účet osoby, která bude nasazovat aktualizace. Klikněte na **Uložit**.

> [!TIP]
> Pokud chcete dát více lidem v přístupu ke svojí organizaci, můžete tento krok zopakovat a dát každému z těchto uživatelů **správce aktualizace zařízení**.

## <a name="next-steps"></a>Další kroky

Teď jste připraveni aktualizovat službu [Azure Percept dev Kit přes Air](./how-to-update-over-the-air.md) pomocí aktualizace zařízení IoT Hub.