---
title: Nasazení pomocí portálu v Azure zásobníku šablon | Microsoft Docs
description: Další informace o použití portálu Azure zásobníku pro nasazení šablon.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604229"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Nasazení šablony pomocí portálu Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Na portálu můžete použít k nasazení šablony Azure Resource Manageru do Azure zásobníku.

## <a name="to-deploy-a-template"></a>Pro nasazení šablony

1. Přihlaste se k portálu, vyberte **nový**a potom vyberte **vlastní**.
2. Vyberte **nasazení šablony**.
3. Vyberte **úpravy šablony**a vložte kód JSON šablony do okna kódu. Vyberte **Uložit**.
4. Vyberte **upravit parametry**, zadejte hodnoty pro parametry, které se zobrazují a potom vyberte **OK**.
5. Vyberte **předplatné**. Vyberte předplatné, které chcete použít a potom vyberte **OK**.
6. Vyberte **skupiny prostředků**. Vyberte existující skupinu prostředků nebo vytvořte novou a potom vyberte **OK**.
7. Vyberte **Vytvořit**. Nová dlaždice na řídicím panelu sleduje průběh nasazení šablony.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
