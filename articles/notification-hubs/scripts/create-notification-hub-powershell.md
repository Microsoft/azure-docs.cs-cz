---
title: 'Skript prostředí PowerShell: Vytvoření centra oznámení Azure | Dokumentace Microsoftu'
description: Tento skript Powershellu vytvoří centra oznámení Azure.
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d1d9628c073b298b6e01f044abfde8b1d40ece2d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245673"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Použití Powershellu k vytvoření centra oznámení Azure

Tento ukázkový skript Powershellu vytvoří centra oznámení Azure vzorku. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete k odebrání skupiny prostředků a všechny prostředky, které s ním spojená následující příkaz:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-Aznotificationhubsnamespace) | Vytvoří obor názvů pro Centrum oznámení. |
| [New-AzNotificationHub](/powershell/module//azurerm.notificationhubs/new-Aznotificationhubsnamespace) | Vytvoří centrum oznámení. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).
