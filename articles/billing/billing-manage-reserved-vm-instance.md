---
title: Správa Azure rezervace | Dokumentace Microsoftu
description: Zjistěte, jak můžete změnit obor předplatného a spravovat přístup pro Azure rezervace.
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
ms.date: 08/08/2018
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628568"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Správa rezervací na prostředky v Azure

Po nákupu Azure rezervace, můžete použít jiné předplatné než ten, který jste zadali během nákupu rezervace. Případně pokud odpovídající virtuální počítače, databáze SQL nebo jiných prostředků se systémem ve více předplatných, můžete změnit obor rezervace tak, aby sdílené. Pro maximalizaci sleva za rezervaci, ujistěte se, že počet instancí, které jste zakoupili odpovídá atributy a počet prostředků, které máte systémem. Další informace najdete v tématu [Azure rezervace](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Změnit obor pro rezervaci.

 Vaše sleva za rezervaci se vztahuje na virtuální počítače, databáze SQL nebo další prostředky, které odpovídají vaší rezervace a spustit v rámci oboru rezervace. Rozsah rezervace může být jedno předplatné nebo všechna předplatná v fakturační kontextu. Je-li nastavit obor na jedno předplatné, rezervace porovnáváno s prostředky ve vybraném předplatném. Je-li nastavit obor na sdílené Azure odpovídá rezervace k prostředkům, které běží ve všech předplatných v rámci kontextu fakturace. Kontext fakturace je závislá na předplatné použité k koupit rezervaci.

Chcete-li aktualizovat obor rezervace, rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervace.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změna oboru. Pokud změníte z sdílí jeden obor, můžete vybrat pouze pokud jste vlastníkem předplatného. Jenom předplatná v rámci stejného kontextu fakturace jako rezervace, je možné vybrat. Určuje kontext fakturace předplatného, které jste vybrali při nákupu rezervace. Obor platí jenom pro předplatná s průběžnými platbami MS-AZR - 0003P a předplatných MS-AZR - 0017P nabídky Enterprise. Smlouvy enterprise předplatná pro vývoj/testování nejsou nárok na získání sleva za rezervaci.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Přidání nebo změna uživatelů, kteří můžou spravovat rezervace

Přidáním uživatelů do role na rezervaci můžete delegovat správu rezervaci. Ve výchozím nastavení osoby, které zakoupíte rezervaci a správce účtu mít roli vlastníka na rezervaci.

Z předplatných, které získáte slevy na rezervaci můžete spravovat přístup k rezervace nezávisle na sobě. Pokud někomu poskytnete oprávnění ke správě rezervace, který je nedává práva ke správě předplatného. A pokud někomu poskytnete oprávnění ke správě předplatných v rámci oboru rezervaci, která nedává jejich práva ke správě rezervace.

Delegovat správu přístupu k rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace** do seznamu rezervace, které máte přístup.
3. Vyberte rezervace, které chcete delegovat přístup ostatním uživatelům.
4. Vyberte **řízení přístupu (IAM)** v nabídce.
5. Vyberte **přidat** > **Role** > **vlastníka** (nebo jinou roli, pokud chcete udělit omezený přístup).
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka. 
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Optimalizace rezervovaných instancí virtuálních počítačů pro flexibilitu nebo kapacity priority velikost virtuálního počítače

 Flexibilita instancí virtuálních počítačů platí sleva za rezervaci pro ostatní virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Ve výchozím nastavení Pokud obor rezervace sdílí, velikost flexibilita instancí zapnutý a kapacitu datového centra není nastaveno jako prioritní pro nasazení virtuálních počítačů. Pro rezervace, kde je jeden obor můžete optimalizovat rezervace pro Priorita kapacity místo velikost flexibilita instancí virtuálních počítačů. Priorita kapacity rezervuje kapacitu datacentra pro vaše nasazení, nabízí máte ještě větší jistotu budete moct spustit potřebné instance virtuálního počítače, když je potřebujete.

Chcete-li aktualizovat obor rezervace, rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervace.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Optimalizovat pro nastavení změňte.

## <a name="split-a-single-reservation-into-two-reservations"></a>Rozdělit jeden rezervace do dvou rezervace

 Po nákupu více než jednu instanci, můžete přiřadit instancí v rámci rezervace do různých předplatných. Ve výchozím nastavení všechny instance (zadaný během nákupu množství) mají jeden rozsah – buď jedno předplatné nebo sdílet. Například zakoupili 10 virtuální počítače Standard D2 a zadaný obor na odběr A. Teď můžete změnit obor pro sedm rezervace na předplatné A a zbývajících 3 k předplatnému služby serveru B. rozdělení rezervace umožňuje distribuci instancí detailní oboru správy. Přidělení pro předplatná můžete zjednodušit zvolením sdíleném oboru. Ale pro účely náklady na správu nebo plánování rozpočtu, můžete přidělit množství na konkrétní předplatné.

 Můžete rozdělit rezervace do dvou rezervace ale Powershellu, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API.

### <a name="split-a-reservation-by-using-powershell"></a>Rozdělení rezervaci pomocí Powershellu

1. Spuštěním následujícího příkazu Získejte ID objednávky rezervace:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Získejte podrobnosti o rezervaci:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rozdělit do dvou rezervace a distribuovat instancí:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Spuštěním následujícího příkazu můžete aktualizovat obor:
    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>Další postup

Další informace o Azure rezervace, naleznete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplatit si virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předem za výpočetní prostředky, SQL Database s Azure SQL Database vyhrazené kapacity](../sql-database/sql-database-reserved-capacity.md)
- [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
