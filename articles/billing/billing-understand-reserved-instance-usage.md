---
title: Pochopení využití vyhrazenou instanci Azure pro vaše předplatné s průběžnými platbami | Microsoft Docs
description: Zjistěte, jak číst vaše využití Rady pro pochopení aplikaci vyhrazená instance pro předplatné s průběžnými platbami
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
ms.openlocfilehash: 142e172b3f4ab4b88bb3733f70d5e0fb252854ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami

Pochopení využití vyhrazená instance pomocí ReservationId z [rezervace stránky](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) a soubor využití z [portál účtů Azure.](https://account.azure.com)


>[!NOTE]
>Tento článek se nevztahuje na EA zákazníků. Pokud jste zákazník EA, přečtěte si téma [pochopit vyhrazenou instanci využití podnikového zápisu.](billing-understand-reserved-instance-usage-ea.md) Tento článek také předpokládá, že rezervace se použije pro v rámci jednoho předplatného. Pokud rezervace se použije k více než jedno předplatné, může rezervace benefit span více souborů csv. 

V následující části předpokládají, že používáte virtuální počítač s Windows Standard_DS1_v2 v oblasti USA – východ a vaše rezervace informace vypadá podobně jako v následující tabulce:

| Pole | Hodnota |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Množství |1|
|Skladová jednotka (SKU) | Standard_DS1_v2|
|Oblast | eastus |

## <a name="reservation-application"></a>Rezervace aplikace

Část hardwaru virtuálního počítače je zahrnout, protože nasazených virtuálních počítačů odpovídající atributy rezervace. Chcete-li zjistit, jaký software Windows není předmětem vyhrazená Instance, přejděte na [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Příkaz části sdíleného svazku clusteru
Tato část vaší sdíleného svazku clusteru zobrazuje celkové využití vaší rezervace. Použijte filtr na dílčí kategorie měření pole, které obsahuje "Rezervace-" a vaše data vypadá jako na následujícím snímku obrazovky: ![přímé rezervace – příkaz](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Řádek rezervace základní virtuální počítač obsahuje celkový počet hodin, které jsou předmětem rezervace. Tento řádek je 0,00 Kč, protože vyhrazenou instanci pokrývá ji. Rezervace Windows Svr (1 jádro) řádek obsahuje náklady na Windows software.

### <a name="daily-usage-section-of-csv"></a>Denní využití části sdíleného svazku clusteru
Na další informace o filtrování a zadejte ID rezervace. Následující snímek obrazovky ukazuje pole související s rezervace. 

![Denní poplatky za používání](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. V poli Další informace o ReservationId je rezervace, která se použije k uplatnění výhody k virtuálnímu počítači.
2. ConsumptionMeter je měření Id pro virtuální počítač.
3. Základní rezervace virtuálních počítačů měření dílčí kategorie řádek představuje řádku nákladů $0 v části prohlášení. Náklady na provozování tento virtuální počítač je již uhrazeno rezervace.
4. Jedná se o Id měření pro rezervace. Náklady na toto monitorování je $0. Žádné virtuální počítače, která kvalifikují pro vyhrazenou instanci má tento MeterId ve sdíleném svazku clusteru, aby se zohlednily náklady. 
5. Standard_DS1_v2 je jeden virtuální procesor bez výhody Azure hybridní nasazení virtuálních počítačů a virtuálních počítačů. Toto měření proto popisuje dalších poplatků softwaru systému Windows. V tématu [náklady na software Windows instancí virtuálních počítačů rezervy Azure.](billing-reserved-instance-windows-software-costs.md) Najít měření odpovídající řady D 1 jádro virtuálních počítačů. Pokud se používá Azure hybridní výhody, není použita tato dalších poplatků. 

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa instancí vyhrazený virtuální počítač](billing-manage-reserved-vm-instance.md)
- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače](billing-save-compute-costs-reservations.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.