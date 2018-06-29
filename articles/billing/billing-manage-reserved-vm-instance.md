---
title: Správa virtuálních počítačů Azure vyhrazenou instancí | Microsoft Docs
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
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063827"
---
# <a name="manage-reserved-instances-in-azure"></a>Spravovat vyhrazená instance v Azure

Po nákupu vyhrazená Instance virtuálního počítače Azure, můžete použít vyhrazenou instanci do jiného předplatného než verze zadaná při nákupu. Případně pokud odpovídající virtuální počítače běží v rámci více předplatných, můžete změnit rozsah vyhrazenou instanci tak, aby sdílené. Pokud chcete maximalizovat slevu vyhrazenou instanci, ujistěte se, že se počet instancí, které jste koupili odpovídající atributy a počet virtuálních počítačů, které máte systémem. Další informace o Azure vyhrazenou instancí, najdete v části [ušetřit peníze předem platebním pro virtuální počítače Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Změnit rozsah pro vyhrazenou instanci
 Vaše slevu vyhrazenou instanci se vztahuje na virtuální počítače, které odpovídají vyhrazenou instanci a spustit v rámci oboru vyhrazenou instanci. Rozsah je vyhrazená instance může být jednoho předplatného nebo všechny odběry ve vašem fakturace kontextu. Je-li nastavit obor k jednomu předplatnému, vyhrazenou instanci odpovídá běžící virtuální počítače ve vybraném předplatném. Pokud nastavíte oboru sdílené, Azure odpovídá vyhrazenou instanci systému na virtuální počítače spouštěné ve všech předplatných v rámci fakturace kontextu. Kontext fakturace je závislá na předplatné použité koupit vyhrazenou instanci. Další informace najdete v tématu [před platím u virtuálních počítačů s vyhrazenou instancí](https://go.microsoft.com/fwlink/?linkid=861721).

Chcete-li aktualizovat rozsah vyhrazenou instanci: 
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte vyhrazenou instanci.
4. Vyberte **nastavení** > **konfigurace**.
5. Změna oboru. Pokud změníte z sdílená jeden obor, můžete zvolit pouze odběry, kde jste vlastníkem. Lze vybrat pouze předplatných v rámci stejného kontextu fakturace jako vyhrazené instance. Kontext fakturace je určen podle předplatné, které jste vybrali při nákupu vyhrazenou instanci. Obor se vztahuje pouze na nabídku průběžných plateb MS-AZR - 0003P odběry a odběry nabídka MS-AZR - 0017P Enterprise. Pro smlouvy enterprise nejsou způsobilé získat vyhrazenou instanci slevu vývoje/testování odběry.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Rozdělit jednu vyhrazenou instanci do dvě vyhrazená instance
 Po nákupu více než jednu instanci, můžete přiřadit instancí v vyhrazenou instanci různých předplatných. Ve výchozím nastavení všechny instance (určené během zakoupení množství) máte jeden obor – buď jednoho předplatného nebo sdílet. Například zakoupených 10 standardní D2 virtuálních počítačů a zadat oboru, který má být předplatné A. Nyní můžete změnit rozsah pro sedm vyhrazenou instancí do předplatného A a zbývající 3 k předplatnému B. rozdělení vyhrazenou instanci umožňuje distribuci instancí granulární oboru správy. Přidělení předplatným můžete zjednodušit výběrem sdílené oboru. Ale pro účely náklady na správu a sestavování rozpočtu, kterou můžete přidělit počty konkrétní předplatným.

 Můžete rozdělit vyhrazenou instanci do dvě vyhrazená instance, když prostředí PowerShell, rozhraní příkazového řádku, nebo přes rozhraní API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Rozdělení vyhrazenou instanci pomocí prostředí PowerShell
1. Získání ID pořadí vyhrazenou instanci spuštěním následujícího příkazu:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Získání podrobností o vyhrazenou instanci:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Rozdělit do dvou vyhrazenou instanci a distribuovat instancí:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Rozsah můžete aktualizovat tak, že spustíte následující příkaz:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Přidat nebo změnit uživatele, kteří mohou spravovat vyhrazenou instanci
Můžete delegovat správu vyhrazená instance přidáním osoby do role na vyhrazené instanci. Ve výchozím nastavení osoba, který koupili vyhrazenou instanci a správce účtu mít roli vlastníka na vyhrazené instanci. 

Přístup k vyhrazenou instancí můžete spravovat nezávisle z odběry, které získají slevu vyhrazenou instanci. Když je někdo udělit oprávnění ke správě vyhrazenou instanci, která není jim poskytnout oprávnění ke správě předplatného. A pokud někdo udělíte oprávnění ke správě předplatného v rámci oboru vyhrazenou instanci, která není jim poskytnout oprávnění ke správě vyhrazenou instanci.
 
Delegovat správu přístupu pro vyhrazené instanci: 
1.  Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2.  Vyberte **všechny služby** > **rezervace** seznam vyhrazené instance, které máte přístup.
3.  Vyberte vyhrazenou instanci, kterou chcete delegovat přístup ostatním uživatelům.
4.  Vyberte **řízení přístupu (IAM)** v nabídce.
5.  Vyberte **přidat** > **Role** > **vlastníka** (nebo na jinou roli, pokud chcete udělit omezený přístup). 
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka. 
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
Další informace o Azure vyhrazenou instancí, naleznete v následujících článcích:

- [Jaké jsou vyhrazená instance virtuálních počítačů Azure?](billing-save-compute-costs-reservations.md)
- [Předem pro virtuální počítače s instancemi Azure vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
