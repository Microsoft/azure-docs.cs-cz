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
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670978"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

V dalším kroku vytvořte identitu zařízení a uložte její klíč pro pozdější použití. Tuto identitu zařízení používá simulační aplikace k odesílání zpráv do centra IoT. Tato funkce není k dispozici v Powershellu nebo při použití šablony Azure Resource Manageru. Následující kroky se dozvíte, jak vytvořit pomocí simulovaného zařízení [webu Azure portal](https://portal.azure.com).

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se ke svému účtu Azure.

2. Vyberte **skupiny prostředků** a pak zvolte vaši skupinu prostředků. Tento kurz používá **ContosoResources**.

3. V seznamu prostředků vyberte své Centrum IoT. Tento kurz používá **ContosoTestHub**. Vyberte v podokně Hub volbu **Zařízení IoT**.

4. Vyberte **+ Přidat**. V podokně Přidat zařízení zadejte ID zařízení. Tento kurz používá **Contoso-Test-Device**. Ponechte klíče prázdné a zaškrtněte **Automaticky vygenerovat klíče**. Zkontrolujte, že je povoleno **Připojit zařízení k centru IoT**. Vyberte **Uložit**.

   ![Na obrazovce Přidat zařízení](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Teď, když se vytvořil, vyberte zařízení zobrazíte generovaného klíče. Vyberte ikonu kopírování na primární klíč a uložte ho někam třeba v poznámkovém bloku pro testovací fáze tohoto kurzu.

   ![Podrobnosti o zařízení, včetně klíče](./media/iot-hub-include-create-simulated-device-portal/device-details.png)