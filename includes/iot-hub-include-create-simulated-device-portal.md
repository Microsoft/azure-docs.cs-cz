---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175629"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

V dalším kroku vytvořte identitu zařízení a uložte její klíč pro pozdější použití. Tuto identitu zařízení používá simulační aplikace k odesílání zpráv do centra IoT. Tato funkce není k dispozici v PowerShellu ani při použití šablony Azure Resource Manager. V následujících krocích se dozvíte, jak vytvořit simulované zařízení pomocí [Azure Portal](https://portal.azure.com).

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se ke svému účtu Azure.

2. Vyberte **skupiny prostředků** a pak zvolte skupinu prostředků. Tento kurz používá **ContosoResources**.

3. V seznamu prostředků vyberte Centrum IoT. Tento kurz používá **ContosoTestHub**. Vyberte v podokně Hub volbu **Zařízení IoT**.

4. Vyberte **+ Přidat**. V podokně Přidat zařízení zadejte ID zařízení. Tento kurz používá **Contoso-Test-Device**. Ponechte klíče prázdné a zaškrtněte **Automaticky vygenerovat klíče**. Zkontrolujte, že je povoleno **Připojit zařízení k centru IoT**. Vyberte **Uložit**.

   ![Obrazovka pro přidání zařízení](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Teď, když je vytvořený, vyberte zařízení, aby se zobrazily vygenerované klíče. Vyberte ikonu kopírování na primárním klíči a uložte ji někam do poznámkového bloku pro testovací fázi tohoto kurzu.

   ![Podrobnosti o zařízení, včetně klíčů](./media/iot-hub-include-create-simulated-device-portal/device-details.png)