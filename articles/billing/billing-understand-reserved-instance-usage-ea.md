---
title: Pochopení využití vyhrazenou instanci Azure pro podnik – fakturace Azure | Microsoft Docs
description: Zjistěte, jak číst vaše využití, abyste pochopili, jak se použije Instance vyhrazené virtuálního počítače Azure u prováděcí smlouvy Enterprise.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Pochopení využití vyhrazenou instanci Azure u prováděcí smlouvy Enterprise
Pochopení využití vyhrazená instance pomocí **ReservationId** z [rezervace stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a využití souboru [EA portál](https://ea.azure.com). Můžete také zjistit využití vyhrazenou instanci v souhrnu oddílu využití [EA portál](https://ea.azure.com).

>[!NOTE]
>Pokud jste si zakoupili vyhrazenou instanci v průběžnými platbami fakturace kontextu, najdete v části [pochopit vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami.](billing-understand-reserved-instance-usage.md)

V následující části předpokládají, že používáte virtuální počítač standard_d1_v2 s Windows v oblasti USA – východ a vyhrazenou instanci informace vypadá jako v následující tabulce:

| Pole | Hodnota |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Množství |1|
|Skladová jednotka (SKU) | Standard_D1|
|Oblast | eastus |

## <a name="reserved-instance-application"></a>Vyhrazená Instance aplikace

Část hardwaru virtuálního počítače je zahrnout, protože nasazených virtuálních počítačů odpovídající atributy vyhrazenou instanci. Pokud chcete zobrazit, jaký software Windows není předmětem vyhrazená Instance, přejděte na náklady na software vyhrazenou instancí virtuálních počítačů Azure, přejděte na [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Vyhrazená Instance využití ve sdíleném svazku clusteru
Csv využití EA si můžete stáhnout z portálu EA. V souboru staženém csv, filtrovat na další informace a zadejte vaše **ReservationID**. Následující snímek obrazovky ukazuje pole související s vyhrazenou instancí:

![Csv Enterprise Agreement (EA) pro vyhrazenou instanci Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** v další informace o pole představuje vyhrazenou instanci, která se použije k uplatnění výhody k virtuálnímu počítači.
2. ConsumptionMeter je MeterId pro virtuální počítač.
3. To je měření rezervace s $0 náklady, protože náklady na provozování virtuálního počítače je již placené vyhrazená instance. 
4. Standard_D1 je jeden virtuální procesor bez výhody Azure hybridní nasazení virtuálních počítačů a virtuálních počítačů. Toto měření proto popisuje dalších poplatků softwaru systému Windows. V tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md) Najít měření odpovídající řady D 1 jádro virtuálních počítačů. Pokud se používá Azure hybridní výhody, nebudou použita tento dalších poplatků.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Vyhrazená Instance využití v využití na souhrnné stránce EA portálu

Vyhrazená Instance využití taky se zobrazí v části Souhrn využití EA portálu: ![souhrn využití Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Vám není účtován pro součást hardwaru virtuálního počítače, jako je předmětem vyhrazenou instanci. 
2. Budou účtovat softwaru Windows jako Benefit hybridní Azure se nepoužívá. 

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí Azure, najdete v následujících článcích:

- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí Azure](billing-save-compute-costs-reservations.md)
- [Předem pro virtuální počítače s vyhrazenou instancí](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Spravovat vyhrazená instance](billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.