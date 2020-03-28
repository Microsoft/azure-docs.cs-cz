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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175629"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

V dalším kroku vytvořte identitu zařízení a uložte její klíč pro pozdější použití. Tuto identitu zařízení používá simulační aplikace k odesílání zpráv do centra IoT. Tato funkce není dostupná v PowerShellu nebo při použití šablony Azure Resource Manager. Následující kroky vám řeknou, jak vytvořit simulované zařízení pomocí [portálu Azure](https://portal.azure.com).

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se ke svému účtu Azure.

2. Vyberte **skupiny prostředků** a pak vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

3. V seznamu prostředků vyberte centrum IoT hub. Tento kurz používá **ContosoTestHub**. Vyberte v podokně Hub volbu **Zařízení IoT**.

4. Vyberte **+ Přidat**. V podokně Přidat zařízení zadejte ID zařízení. Tento kurz používá **Contoso-Test-Device**. Ponechte klíče prázdné a zaškrtněte **Automaticky vygenerovat klíče**. Zkontrolujte, že je povoleno **Připojit zařízení k centru IoT**. Vyberte **Uložit**.

   ![Obrazovka přídavných zařízení](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Teď, když byla vytvořena, vyberte zařízení, které zobrazí generované klíče. Vyberte ikonu Kopírovat na primárním klíči a uložte ji někam, například poznámkový blok pro testovací fázi tohoto kurzu.

   ![Podrobnosti o zařízení, včetně tlačítek](./media/iot-hub-include-create-simulated-device-portal/device-details.png)