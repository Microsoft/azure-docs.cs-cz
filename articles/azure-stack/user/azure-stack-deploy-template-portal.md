---
title: Nasazení šablon pomocí portálu ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak pomocí portálu Azure Stack pro nasazení šablony.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: b2f4fbca2dcbdd537302746c7b6109e467ea31d7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720221"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Nasazení šablon pomocí portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Na portálu můžete použít k nasazení šablony Azure Resource Manageru ke službě Azure Stack.

## <a name="to-deploy-a-template"></a>K nasazení šablony

1. Přihlaste se k portálu vyberte **+ vytvořit prostředek**a pak vyberte **vlastní**.
2. Vyberte **nasazení šablony**.
3. Vyberte **úpravy šablony**a vložte kód JSON šablony do okna kódu. Vyberte **Uložit**.
4. Vyberte **upravit parametry**, zadejte hodnoty pro parametry, které jsou zobrazeny a pak vyberte **OK**.
5. Vyberte **předplatné**. Zvolte předplatné, které chcete použít a potom vyberte **OK**.
6. Vyberte **skupiny prostředků**. Zvolte existující skupinu prostředků nebo vytvořte novou a potom vyberte **OK**.
7. Vyberte **Vytvořit**. Nová dlaždice na řídicím panelu sleduje průběh nasazování šablony.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
