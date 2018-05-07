---
title: Správa instancí Azure vyhrazený virtuální počítač | Microsoft Docs
description: Zjistěte, jak můžete změnit obor předplatného a správa přístupu pro vyhrazené instance virtuálních počítačů Azure.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: 003abb3ddf632261dc372549209ba664cb8f8398
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="manage-reserved-virtual-machine-instances"></a>Správa instancí vyhrazený virtuální počítač

Po nákupu vyhrazená Instance virtuálního počítače Azure, můžete použít rezervace do jiného předplatného než verze zadaná při nákupu. Případně pokud odpovídající virtuální počítače běží v rámci více předplatných, můžete změnit rozsah rezervace tak, aby sdílené. Pokud chcete maximalizovat slevu rezervace, ujistěte se, že se počet instancí, které jste koupili odpovídající atributy a počet virtuálních počítačů, které máte systémem. Další informace o vyhrazenou instancí virtuálního počítače najdete v tématu [ušetřit peníze předem platebním pro virtuální počítače Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Změnit rozsah pro rezervace
 Vaše slevu rezervace se vztahuje na virtuální počítače, které odpovídají vaše rezervace a spustit v rámci oboru rezervace. Oboru rezervace může být jednoho předplatného nebo všechny odběry ve vašem fakturace kontextu. Je-li nastavit obor k jednomu předplatnému, rezervace je odpovídat běžící virtuální počítače ve vybraném předplatném. Pokud nastavíte oboru sdílené, Azure odpovídá rezervace virtuální počítače spouštěné ve všech předplatných v rámci fakturace kontextu. Kontext fakturace je závislá na předplatné použité koupit rezervace. Další informace najdete v tématu [před platím u virtuálních počítačů s vyhrazenou instancí virtuálních počítačů](https://go.microsoft.com/fwlink/?linkid=861721).

Aktualizace oboru rezervace: 
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervace.
4. Vyberte **nastavení** > **konfigurace**.
5. Změna oboru. Pokud změníte z sdílená jeden obor, můžete zvolit pouze odběry, kde jste vlastníkem. Pouze předplatných v rámci stejného kontextu fakturace jako rezervace, lze vybrat. Kontext fakturace je určen podle předplatné, které jste vybrali při nákupu rezervace. Obor se vztahuje pouze na nabídku průběžných plateb MS-AZR - 0003P odběry a odběry nabídka MS-AZR - 0017P Enterprise. Pro smlouvy enterprise nejsou způsobilé získat slevu rezervace vývoje/testování odběry.

## <a name="split-a-single-reservation-into-two-reservations"></a>Rozdělit jeden rezervace do dvou rezervací
 Po nákupu více než jednu instanci, můžete přiřadit instancí v rámci rezervace různých předplatných. Ve výchozím nastavení všechny instance (určené během zakoupení množství) máte jeden obor – buď jednoho předplatného nebo sdílet. Například zakoupených 10 standardní D2 virtuálních počítačů a zadat oboru, který má být předplatné A. Nyní můžete změnit rozsah pro 7 instance vyhrazené virtuálních počítačů do předplatného A a zbývající 3 k předplatnému B. rozdělení rezervace umožňuje distribuci instancí granulární oboru správy. Přidělení předplatným můžete zjednodušit výběrem sdílené oboru. Ale pro účely náklady na správu a sestavování rozpočtu, kterou můžete přidělit počty konkrétní předplatným.

 Můžete rozdělit do dvou rezervace rezervace, když prostředí PowerShell, rozhraní příkazového řádku, nebo přes rozhraní API.

### <a name="split-a-reservation-by-using-powershell"></a>Rozdělení rezervace pomocí prostředí PowerShell
1. Získání ID rezervace pořadí tak, že spustíte následující příkaz:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Získání podrobností o rezervace:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Rozdělit do dvou rezervace a distribuovat instancí:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Rozsah můžete aktualizovat tak, že spustíte následující příkaz:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Přidat nebo změnit uživatele, kteří můžou spravovat rezervace
Můžete delegovat správu rezervace podle přidání uživatelů do rolí na rezervace. Ve výchozím nastavení osoba, která koupili rezervace a správce účtu mít roli vlastníka na rezervace. 

Přístup k rezervace můžete spravovat nezávisle z odběry, které získají slevu rezervace. Když je někdo udělit oprávnění ke správě rezervace, který není jim poskytnout oprávnění ke správě předplatného. A pokud někdo udělíte oprávnění ke správě předplatného v rámci oboru rezervace, který není jim poskytnout oprávnění ke správě rezervace.
 
Delegovat správu přístupu k rezervace: 
1.  Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2.  Vyberte **všechny služby** > **rezervace** do seznamu rezervace, které máte přístup.
3.  Vyberte rezervace, který chcete delegovat přístup ostatním uživatelům.
4.  Vyberte **řízení přístupu (IAM)** v nabídce.
5.  Vyberte **přidat** > **Role** > **vlastníka** (nebo na jinou roli, pokud chcete udělit omezený přístup). 
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka. 
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače](billing-save-compute-costs-reservations.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
