---
title: Porozumět používání Azure vyhrazenou instanci pro podnik | Microsoft Docs
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
ms.openlocfilehash: d6e8b2544f919abeb7fde0e37fc12bd29f0171ef
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063828"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Pochopení využití vyhrazená instance Azure u prováděcí smlouvy Enterprise
Pochopení využití vyhrazená instance pomocí **ReservationId** z [rezervace stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a využití souboru [EA portál](https://ea.azure.com). Můžete také zjistit využití vyhrazenou instanci v souhrnu oddílu využití [EA portál](https://ea.azure.com).

>[!NOTE]
>Pokud jste si zakoupili vyhrazenou instanci v průběžnými platbami fakturace kontextu, najdete v části [Rady pro pochopení vyhrazená instance využití pro vaše předplatné s průběžnými platbami.](billing-understand-reserved-instance-usage.md)

V následující části předpokládají, že používáte virtuální počítač Standard_D1_v2 s Windows v oblasti USA – východ a vaše vyhrazenou instanci informace vypadá podobně jako v následující tabulce:

| Pole | Hodnota |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Množství |1|
|Skladová jednotka (SKU) | Standard_D1|
|Oblast | eastus |

## <a name="reserved-instance-application"></a>Vyhrazená instance aplikace

Část hardwaru virtuálního počítače je zahrnout, protože nasazených virtuálních počítačů odpovídající atributy vyhrazenou instanci. Pokud chcete zobrazit, jaký software Windows není předmětem vyhrazená instance, přejděte na náklady na software vyhrazenou instancí virtuálních počítačů Azure, přejděte na [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Vyhrazená instance využití ve sdíleném svazku clusteru
Csv využití EA si můžete stáhnout z portálu EA. V souboru staženém csv, filtrovat na další informace a zadejte vaše **ReservationID**. Následující snímek obrazovky ukazuje pole související s vyhrazenou instancí:

![Vyhrazená instance csv Enterprise Agreement (EA) pro Azure.](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** v další informace o pole představuje vyhrazenou instanci, která se použije k uplatnění výhody k virtuálnímu počítači.
2. ConsumptionMeter je MeterId pro virtuální počítač.
3. To je měření rezervace s $0 náklady, protože náklady na provozování virtuálního počítače je již placené vyhrazená instance. 
4. Standard_D1 je jeden virtuální procesor bez výhody Azure hybridní nasazení virtuálních počítačů a virtuálních počítačů. Toto měření proto popisuje dalších poplatků softwaru systému Windows. V tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md) Najít měření odpovídající řady D 1 jádro virtuálních počítačů. Pokud se používá Azure hybridní výhody, nebudou použita tento dalších poplatků.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Vyhrazená instance využití v využití na souhrnné stránce EA portálu

Vyhrazená instance využití taky se zobrazí v části Souhrn využití EA portálu: ![souhrn využití Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Vám není účtován pro součást hardwaru virtuálního počítače, jako je předmětem vyhrazenou instanci. 
2. Budou účtovat softwaru Windows jako Benefit hybridní Azure se nepoužívá. 

## <a name="next-steps"></a>Další postup
Další informace o Azure vyhrazenou instancí, naleznete v následujících článcích:

- [Jaké jsou vyhrazená instance virtuálních počítačů Azure?](billing-save-compute-costs-reservations.md)
- [Předem pro virtuální počítače s instancemi Azure vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Spravovat vyhrazená instance v Azure](billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.