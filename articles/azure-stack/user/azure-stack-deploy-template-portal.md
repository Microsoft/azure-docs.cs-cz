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
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: b9adac3f2f56093c3559570aab4e905eb047ccd2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247081"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Nasazení šablon pomocí portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Na portálu můžete použít k nasazení šablony Azure Resource Manageru ke službě Azure Stack.

## <a name="to-deploy-a-template"></a>K nasazení šablony

1. Přihlaste se k portálu vyberte **+ vytvořit prostředek**a pak vyberte **vlastní**.
2. Vyberte **Template deployment**.
3. Vyberte **úpravy šablony**a vložte kód JSON šablony do okna kódu. Vyberte **Uložit**.
4. Vyberte **upravit parametry**, zadejte hodnoty pro parametry, které jsou zobrazeny a pak vyberte **OK**.
5. Vyberte **předplatné**. Zvolte předplatné, které chcete použít a potom vyberte **OK**.
6. Vyberte **skupiny prostředků**. Zvolte existující skupinu prostředků nebo vytvořte novou a potom vyberte **OK**.
7. Vyberte **Vytvořit**. Nová dlaždice na řídicím panelu sleduje průběh nasazování šablony.

## <a name="next-steps"></a>Další postup

Další informace o nasazení šablony, najdete v následujícím článku:

- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
