---
title: Pochopení využití vyhrazenou instanci Azure pro předplatné průběžnými platbami - fakturace Azure | Microsoft Docs
description: Zjistěte, jak číst vaše využití, abyste pochopili, jak se použije Instance vyhrazené virtuálního počítače Azure pro vaše předplatné s průběžnými platbami.
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
ms.openlocfilehash: 7bf4aea86d4d430c15d60a8d73365705ace18b5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami

Pochopení využití vyhrazená instance virtuálních počítačů Azure pomocí ReservationId z [rezervace stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a použití souboru [portál účtů Azure](https://account.azure.com).


>[!NOTE]
>Tento článek se nevztahuje na EA zákazníků. Pokud jste zákazník EA, přečtěte si téma [pochopit vyhrazenou instanci využití podnikového zápisu.](billing-understand-reserved-instance-usage-ea.md) Tento článek také předpokládá, že vyhrazenou instanci se použije pro v rámci jednoho předplatného. Pokud vyhrazená Instance se použije k více než jedno předplatné, může vyhrazenou instanci benefit span více souborů csv. 

V následující části předpokládají, že používáte virtuální počítač s Windows Standard_DS1_v2 v oblasti USA – východ a vyhrazenou instanci informace vypadá jako v následující tabulce:

| Pole | Hodnota |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Množství |1|
|Skladová jednotka (SKU) | Standard_DS1_v2|
|Oblast | eastus |

## <a name="reserved-instance-application"></a>Vyhrazená Instance aplikace

Část hardwaru virtuálního počítače je zahrnout, protože nasazených virtuálních počítačů odpovídající atributy vyhrazenou instanci. Chcete-li zjistit, jaký software Windows není předmětem vyhrazená Instance, přejděte na [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Příkaz části sdíleného svazku clusteru
Tato část vaší sdíleného svazku clusteru zobrazuje celkové využití pro vyhrazenou instanci. Použijte filtr na měření podkategorie pole, které obsahuje "Rezervace-" a vaše data vypadá jako na následujícím snímku obrazovky: ![snímek obrazovky podrobnosti o použití filtrovaných vyhrazenou instanci a poplatky](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Řádek rezervace základní virtuální počítač obsahuje celkový počet hodin, které jsou předmětem vyhrazenou instanci. Tento řádek je 0,00 Kč, protože vyhrazenou instanci pokrývá ji. Rezervace Windows Svr (1 jádro) řádek obsahuje náklady na Windows software.

### <a name="daily-usage-section-of-csv"></a>Denní využití části sdíleného svazku clusteru
Na další informace o filtrování a zadejte vaše **ID rezervace**. Následující snímek obrazovky ukazuje pole související s vyhrazenou instancí. 

![Snímek obrazovky s denní podrobnosti o využití a poplatky](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** dalších údajů, pole je vyhrazená Instance, která se použije k uplatnění výhody k virtuálnímu počítači.
2. ConsumptionMeter je měření Id pro virtuální počítač.
3. Základní rezervace virtuálních počítačů měření podkategorie řádek představuje řádku nákladů $0 v části prohlášení. Náklady na provozování tento virtuální počítač je již uhrazeno vyhrazenou instanci.
4. Jedná se o Id měření pro vyhrazenou instanci. Náklady na toto monitorování je $0. Žádné virtuální počítače, která kvalifikují pro vyhrazenou instanci má tento MeterId ve sdíleném svazku clusteru, aby se zohlednily náklady. 
5. Standard_DS1_v2 je jeden virtuální procesor bez výhody Azure hybridní nasazení virtuálních počítačů a virtuálních počítačů. Toto měření proto popisuje dalších poplatků softwaru systému Windows. V tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md) Najít měření odpovídající řady D 1 jádro virtuálních počítačů. Pokud se používá Azure hybridní výhody, není použita tato dalších poplatků. 

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí, naleznete v následujících článcích:

- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí Azure](billing-save-compute-costs-reservations.md)
- [Předem pro virtuální počítače s vyhrazenou instancí](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Spravovat vyhrazená instance](billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
