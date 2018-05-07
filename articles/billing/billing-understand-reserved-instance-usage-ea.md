---
title: Pochopení využití vyhrazenou instanci Azure pro podnik | Microsoft Docs
description: Zjistěte, jak číst vaše využití pochopit aplikace vyhrazená instance pro podnikového zápisu.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: cf79926e6497c50156f2a0191997ca06bc605c16
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Pochopení vyhrazenou instanci využití pro podnikového zápisu
Pochopení využití vyhrazená instance pomocí ReservationId z [rezervace stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) a soubor využití z [EA portálu.](https://ea.azure.com) Můžete také zjistit rezervace využití v souhrnu oddílu využití [EA portálu.](https://ea.azure.com)

>[!NOTE]
>Pokud jste si zakoupili rezervace v průběžnými platbami fakturace kontextu, najdete v části [pochopit vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami.](billing-understand-reserved-instance-usage.md)

V následující části předpokládají, že používáte virtuální počítač Standard_D1_v2 s Windows v oblasti USA – východ a vaše rezervace informace vypadá podobně jako v následující tabulce:

| Pole | Hodnota |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Množství |1|
|Skladová jednotka (SKU) | Standard_D1|
|Oblast | eastus |

## <a name="reservation-application"></a>Rezervace aplikace

Část hardwaru virtuálního počítače je zahrnout, protože nasazených virtuálních počítačů odpovídající atributy rezervace. Pokud chcete zobrazit, jaký software Windows není předmětem vyhrazená Instance, přejděte na náklady na software vyhrazenou instancí virtuálních počítačů Azure, přejděte na [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reservation-usage-in-csv"></a>Rezervace využití ve sdíleném svazku clusteru
Csv využití EA si můžete stáhnout z portálu EA. V souboru staženém csv, filtrovat na další informace a zadejte ID rezervace. Následující snímek obrazovky ukazuje související s rezervace pole:

![Csv EA pro vyhrazenou instanci](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. V poli Další informace o ReservationId představuje rezervace, která se použije k uplatnění výhody k virtuálnímu počítači.
2. ConsumptionMeter je MeterId pro virtuální počítač.
3. To je ReservationMeter s $0 náklady, protože náklady na provozování virtuálního počítače je již uhrazeno rezervace. 
4. Standard_D1 je jeden virtuální procesor bez výhody Azure hybridní nasazení virtuálních počítačů a virtuálních počítačů. Toto měření proto popisuje dalších poplatků softwaru systému Windows. V tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md) Najít měření odpovídající řady D 1 jádro virtuálních počítačů. Pokud se používá Azure hybridní výhody, nebudou použita tento dalších poplatků.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Rezervace využití v využití na souhrnné stránce EA portálu

Vyhrazená Instance využití taky se zobrazí v části Souhrn využití EA portálu: ![EA souhrnu využití](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Vám není účtován pro součást hardwaru virtuálního počítače, jako je předmětem vyhrazenou instanci. 
2. Budou účtovat softwaru Windows jako Benefit hybridní Azure se nepoužívá. 

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa instancí vyhrazený virtuální počítač](billing-manage-reserved-vm-instance.md)
- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače](billing-save-compute-costs-reservations.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.