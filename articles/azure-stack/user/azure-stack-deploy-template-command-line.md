---
title: Nasazení šablon pomocí příkazového řádku ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití rozhraní příkazového řádku (CLI) napříč platformami pro nasazení šablony do služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054840"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Nasazení šablon ve službě Azure Stack pomocí příkazového řádku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Použijte příkazový řádek pro nasazení šablony Azure Resource Manageru do Azure Stack Development Kit. Šablony Azure Resource Manageru nasadit a zřiďte všechny prostředky pro vaši aplikaci v rámci jediné koordinované operace.

## <a name="before-you-begin"></a>Než začnete
 - [Nainstalujte a připojte](azure-stack-version-profiles-azurecli2.md) do služby Azure Stack pomocí Azure CLI
 - Stažení souborů *azuredeploy.json* a *azuredeploy.parameters.json* z [vytvořit příklad šablony úložiště účtu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Nasazení šablony
Přejděte do složky, kam byly tyto soubory stáhnout a spustit následující příkaz k nasazení šablony:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Tento příkaz nasadí šablony do skupiny prostředků **cliRG** ve výchozím umístění Azure Stack POC.

## <a name="validate-template-deployment"></a>Ověření šablony nasazení
Pokud chcete zobrazit tento prostředek skupiny a účet úložiště, použijte následující příkazy:

    azure group list

    azure storage account list




