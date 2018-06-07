---
title: 'Skript prostředí PowerShell: vytvoření vlastní image z soubor virtuálního pevného disku v prostředí služby Azure | Microsoft Docs'
description: Tento skript prostředí PowerShell vytvořit vlastní image ze souboru virtuálního pevného disku v prostředí služby Azure.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 19b7c3c6018ec56b056761c336bc56c8b63b47a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636398"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Pomocí prostředí PowerShell vytvořit vlastní image ze souboru virtuálního pevného disku v prostředí služby Azure

Tento ukázkový skript prostředí PowerShell vytvořit vlastní image ze souboru virtuálního pevného disku v prostředí služby Azure

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky
* **Testovacího prostředí**. Skript, musíte mít existující testovacího prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Získá prostředky. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Získá seznam přístupových klíčů pro účet služby Azure Storage. |
| [Nové AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | Nasazení služby Azure se přidá do skupiny prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu prostředí PowerShell služby testovacího prostředí Azure lze nalézt v [Azure PowerShell služby Lab ukázky](../samples-powershell.md).