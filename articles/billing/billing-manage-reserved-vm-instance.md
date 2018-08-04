---
title: Správa Azure Reserved VM Instances | Dokumentace Microsoftu
description: Zjistěte, jak můžete změnit obor předplatného a správa přístupu pro Azure Reserved VM Instances.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: yashesvi
ms.openlocfilehash: 589afc736faaa210fdcd5cf6c79d10af4af3c0e9
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502521"
---
# <a name="manage-reserved-instances-in-azure"></a>Správa rezervovaných instancí v Azure

Po nákupu rezervované Instance virtuálního počítače Azure, můžete použít jiné předplatné než verze zadaná při nákupu rezervovaných instancí. Případně pokud odpovídající virtuální počítače se systémem ve více předplatných, můžete změnit rozsah rezervované instance tak, aby sdílené. Pro maximalizaci slevu za rezervovanou instanci, ujistěte se, že počet instancí, které jste zakoupili odpovídá atributy a počet virtuálních počítačů, které máte systémem. Další informace o službě Azure reserved instances, naleznete v tématu [Šetřete peníze optimalizací platíte předem za virtuální počítače Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Změnit obor rezervované instance
 Slevy rezervované instance se vztahuje na virtuální počítače, které odpovídá rezervované instance a spuštění v rámci oboru rezervovaných instancí. Obor rezervované instance může být jedno předplatné nebo všechna předplatná v fakturační kontextu. Je-li nastavit obor na jedno předplatné, rezervované instance je nalezena shoda s běžícím virtuálním počítačům ve vybraném předplatném. Pokud nastavíte rozsah na sdílené, Azure odpovídá rezervované instance pro virtuální počítače, na kterých běží v rámci všech předplatných v rámci kontextu fakturace. Kontext fakturace je závislá na předplatné použité k nákupu rezervovaných instancí. Další informace najdete v tématu [platba předem pro virtuální počítače s rezervovanými instancemi](https://go.microsoft.com/fwlink/?linkid=861721).

Chcete-li aktualizovat obor rezervované instance: 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervovaných instancí.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změna oboru. Pokud změníte z sdílí jeden obor, můžete vybrat pouze pokud jste vlastníkem předplatného. Je možné vybrat jenom předplatná v rámci stejného kontextu fakturace jako rezervované instance. Určuje kontext fakturace předplatného, které jste vybrali při nákupu rezervované instance. Obor platí jenom pro předplatná s průběžnými platbami MS-AZR - 0003P a předplatných MS-AZR - 0017P nabídky Enterprise. Smlouvy enterprise předplatná pro vývoj/testování nejsou nárok na získání slevu za rezervovanou instanci.

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Přidání nebo změna uživatelů, kteří můžou spravovat rezervovaných instancí
Přidáním uživatelů do role na rezervované instance můžete delegovat správu rezervovaných instancí. Ve výchozím nastavení osoby, která zakoupili rezervované instance a správce účtu mít roli vlastníka na rezervovanou instanci. 

Přístup reserved instances můžete spravovat nezávisle z předplatných, které získat slevu za rezervovanou instanci. Pokud někomu poskytnete oprávnění ke správě rezervované instance, která je nedává práva ke správě předplatného. A pokud někomu poskytnete oprávnění ke správě předplatných v rámci oboru rezervované instance, který nedává jejich práva ke správě rezervovaných instancí.
 
Delegovat správu přístupu pro rezervovanou instanci: 
1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  Vyberte **všechny služby** > **rezervace** na rezervované instance, ke kterým máte přístup k seznamu.
3.  Vyberte rezervované instance, které chcete delegovat přístup ostatním uživatelům.
4.  Vyberte **řízení přístupu (IAM)** v nabídce.
5.  Vyberte **přidat** > **Role** > **vlastníka** (nebo jinou roli, pokud chcete udělit omezený přístup). 
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka. 
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Optimalizace rezervovaných instancí virtuálních počítačů pro flexibilitu nebo kapacity priority velikost virtuálního počítače
 Flexibilita instancí virtuálních počítačů platí sleva za rezervaci pro ostatní virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Ve výchozím nastavení Pokud obor rezervace sdílí, velikost flexibilita instancí zapnutý a kapacitu datového centra není nastaveno jako prioritní pro nasazení virtuálních počítačů. Pro rezervace, kde je jeden obor můžete optimalizovat rezervace pro Priorita kapacity místo velikost flexibilita instancí virtuálních počítačů. Priorita kapacity rezervuje kapacitu datacentra pro vaše nasazení, nabízí máte ještě větší jistotu budete moct spustit potřebné instance virtuálního počítače, když je potřebujete.

Chcete-li aktualizovat obor rezervované instance: 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervovaných instancí.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Optimalizovat pro nastavení změňte.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Rozdělit jeden rezervovaných instancí do dvou rezervovaných instancí
 Po nákupu více než jednu instanci, můžete přiřadit instancí v rámci rezervované instance do různých předplatných. Ve výchozím nastavení všechny instance (zadaný během nákupu množství) mají jeden rozsah – buď jedno předplatné nebo sdílet. Například zakoupili 10 virtuální počítače Standard D2 a zadaný obor na odběr A. Teď můžete změnit obor sedm reserved instances na předplatné A a zbývajících 3 k předplatnému služby serveru B. rozdělení rezervované instance můžete distribuovat instance detailní oboru správy. Přidělení pro předplatná můžete zjednodušit zvolením sdíleném oboru. Ale pro účely náklady na správu nebo plánování rozpočtu, můžete přidělit množství na konkrétní předplatné.

 Můžete rozdělit rezervovaných instancí do dvou rezervované instance však Powershellu, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Rozdělení rezervované instance pomocí Powershellu
1. Získejte ID objednávky rezervované instance spuštěním následujícího příkazu:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Získejte podrobnosti o rezervovaných instancí:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rozdělit rezervovaných instancí do dvou a distribuovat instancí:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```

1. Spuštěním následujícího příkazu můžete aktualizovat obor:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>Další postup
Další informace o službě Azure reserved instances, naleznete v následujících článcích:

- [Co jsou Azure Reserved VM Instances?](billing-save-compute-costs-reservations.md)
- [Předplatit si virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Vysvětlení způsobu uplatňování slev se službou Reserved Instances](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervovaných instancí u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervovaných instancí u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Rezervované instance nezahrnují náklady na software Windows](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
