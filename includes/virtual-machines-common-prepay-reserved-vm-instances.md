---
ms.topic: include
ms.openlocfilehash: 509d2bc3f58c57a3e2e15eed2ea2c70ed17856f3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063693"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Předem pro virtuální počítače s instancemi Azure vyhrazené virtuálních počítačů

Předem pro virtuální počítače a ušetřit peníze s instancemi Azure vyhrazené virtuální počítač (VM). Další informace najdete v tématu [vyhrazenou instancí virtuálních počítačů Azure nabídky](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Můžete si koupit Azure vyhrazenou instancí [portál Azure](https://portal.azure.com). Koupit vyhrazenou instanci:
-   Musíte být roli vlastníka pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
-   U předplatného typu Enterprise, musí být povolená vyhrazenou instanci nákupy v [EA portál](https://ea.azure.com).
-   Cloud Solution Provider (CSP) programu můžete zakoupit pouze správce agentů nebo prodejní agenty vyhrazenou instancí.

[!IMPORTANT]
Musí používat jednu z metod popsaných níže k identifikaci správně velikost virtuálního počítače pro nákup rezervace.

## <a name="determine-the-right-vm-size-before-purchase"></a>Určení správné velikosti virtuálního počítače před nákupu
1. Odkazujte na pole AdditionalInfo v souboru využití nebo využití rozhraní API k určení správné velikosti virtuálního počítače pro nákup rezervace. Nepoužívejte hodnoty z pole měření dílčí kategorie nebo produktu, vzhledem k tomu, že tato pole nerozlišují mezi S a Non-S verzemi virtuálního počítače.
2. Můžete také získat přesné informace o velikosti virtuálních počítačů pomocí prostředí Powershell, Azure Resource Manager nebo z virtuálního počítače podrobnosti na portálu Azure.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Kupte si Instance vyhrazený virtuální počítač
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** přikoupení novou vyhrazenou instanci.
4. Vyplňte požadovaná pole. Spuštění virtuálního počítače instancí, které odpovídají vybrané atributy jsou způsobilé pro získání slevu vyhrazenou instanci. Skutečný počet instancím virtuálních počítačů získávající slevy závisí na oboru a množství vybranou.

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název této vyhrazená instance.| 
    |Předplatné|Předplatné použít k platbě za vyhrazenou instanci. Způsob platby u předplatného je účtovat předem náklady na vyhrazené instanci. Typ odběru musí být smlouvu enterprise agreement (číslo nabídky: MS-AZR - 0017P) nebo průběžné platby (číslo nabídky: MS-AZR - 0003P). Předplatné enterprise jsou poplatků odečtené od vyrovnávání peněžních závazků zápisu nebo účtován jako Nadlimitní události úrovně. Pro předplatné s průběžnými platbami se účtují poplatky přijímáte platební kartu ani faktury způsobu platby u předplatného.|    
    |Rozsah       |Vyhrazená Instance oboru může zahrnovat předplatné na jeden nebo více předplatných (sdílené rozsah). Pokud zvolíte možnost: <ul><li>Jednomu předplatnému - The vyhrazenou instanci slevu se použijí na virtuální počítače v tomto předplatném. </li><li>Sdílené - slevu vyhrazenou instanci se použije pro virtuální počítače spuštěné ve všech předplatných v rámci vaší fakturace kontextu. Pro podnikové zákazníky oboru sdílené registraci a zahrnuje všechny odběry (s výjimkou vývoje/testování odběry) v rámci registrace. Pro zákazníky průběžné platby sdílené rozsah je všechny odběry průběžné platby vytvořený účet správce.</li></ul>|
    |Umístění    |Oblast Azure, která je předmětem vyhrazenou instanci.|    
    |Velikost virtuálního počítače     |Velikost instance virtuálních počítačů.|
    |Označení        |Jeden rok nebo tři roky.|
    |Množství    |Počet instancí se zakoupili v rámci vyhrazenou instanci. Množství je počet spuštěných instancí virtuálních počítačů, které můžete získat fakturační slevy. Například pokud používáte 10 virtuálních počítačů Standard_D2 ve východní USA, pak zadáte množství jako 10 a maximalizovat pro všechny spuštěné počítače. |
5. Když vyberete můžete zobrazit náklady na vyhrazené instanci **vypočítat náklady na**.

    ![Snímek obrazovky před odesláním vyhrazenou instanci nákupu](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Vyberte **nákupu**.
7. Vyberte **zobrazit tuto rezervaci** zobrazíte stav nákupu.

    ![Snímek obrazovky po odeslání vyhrazenou instanci nákupu](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Další postup 
Vyhrazená Instance slevu je automaticky použity počet spuštěných virtuálních počítačů, které odpovídají oboru vyhrazenou instanci a atributy. Můžete aktualizovat rozsah vyhrazenou instanci prostřednictvím [portál Azure](https://portal.azure.com), prostředí PowerShell, rozhraní příkazového řádku nebo pomocí rozhraní API. 

Zjistěte, jak spravovat vyhrazenou instanci, najdete v tématu [spravovat vyhrazená instance v Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Další informace o Azure vyhrazenou instancí, naleznete v následujících článcích:

- [Jaké jsou vyhrazená instance virtuálních počítačů Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Spravovat vyhrazená instance v Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Vyhrazená instance v programu partnera Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.