---
title: Nasazení šablon pomocí příkazového řádku v zásobníku Azure | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) a platformy pro nasazení šablon do Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316191"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Nasazení šablon v zásobníku Azure pomocí příkazového řádku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Pomocí příkazového řádku pro nasazení šablon Azure Resource Manageru do Azure zásobníku Development Kit. Šablony Azure Resource Manageru nasazení a zřizování všechny prostředky pro svoji aplikaci v rámci jediné koordinované operace.

## <a name="before-you-begin"></a>Než začnete
 - [Nainstalujte a připojte](azure-stack-version-profiles-azurecli2.md) do protokolů Azure pomocí rozhraní příkazového řádku Azure
 - Stažení souborů *azuredeploy.json* a *azuredeploy.parameters.json* z [vytvoření šablony příklad účet úložiště](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Nasazení šablony
Přejděte do složky, kde byly tyto soubory stáhnout a spustit následující příkaz pro nasazení šablony:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Tento příkaz nasadí šablony do skupiny prostředků **cliRG** v zásobníku POC Azure výchozí umístění.

## <a name="validate-template-deployment"></a>Ověření nasazení šablony
Pokud chcete zobrazit tento prostředek skupiny a účet úložiště, použijte následující příkazy:

    azure group list

    azure storage account list




