---
title: Nasazení šablon pomocí sady Visual Studio v zásobníku Azure | Microsoft Docs
description: Informace o nasazení šablony pomocí sady Visual Studio v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 6cd722fedc0483e37ce6ee491d74a7c985111353
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605119"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Nasazení šablon v zásobníku Azure pomocí sady Visual Studio

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Visual Studio můžete použít k nasazení šablony Azure Resource Manageru do Azure zásobníku.

## <a name="to-deploy-a-template"></a>Pro nasazení šablony

1. [Nainstalujte a připojte](azure-stack-install-visual-studio.md) do protokolů Azure pomocí sady Visual Studio.
2. Otevřete sadu Visual Studio.
3. Vyberte **soubor**a potom vyberte **nový**. V **nový projekt**, vyberte **skupiny prostředků Azure**.
4. Zadejte **název** pro nový projekt a potom vyberte **OK**.
5. V **vybrat šablonu Azure**, vyberte **Azure Quickstart zásobníku** z rozevíracího seznamu.
6. Vyberte **101-create-storage-account**a potom vyberte **OK**.
7. Do nového projektu, rozbalte **šablony** uzlu v **Průzkumníku řešení** zobrazíte dostupné šablony.
8. V **Průzkumníku řešení**, vyberte název projektu a pak vyberte **nasadit**. Vyberte **nové nasazení**.
9. V **nasadit do skupiny prostředků**, použijte **předplatné** rozevíracího seznamu vyberte předplatné Microsoft Azure zásobníku.
10. Z **skupiny prostředků** seznamu, vyberte existující skupinu prostředků nebo vytvořte novou.
11. Z **umístění skupiny prostředků** seznam, vyberte umístění a pak vyberte **nasadit**.
12. V **upravit parametry**, zadejte hodnoty pro parametry (které se liší podle šablony) a potom vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí příkazového řádku](azure-stack-deploy-template-command-line.md)
* [Vývoj šablon pro Azure zásobníku](azure-stack-develop-templates.md)
