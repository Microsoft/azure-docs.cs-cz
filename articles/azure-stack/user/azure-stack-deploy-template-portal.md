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
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4013b7136fd365b1d37348cfc1f0b46cce787ce2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363119"
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

Další informace o nasazení šablony najdete v tématu:

[Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
