---
ms.topic: include
ms.openlocfilehash: b41b93db63038645257bc348d5a027fbc4f7fdc9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů

Předem pro virtuální počítače a ušetřit peníze s vyhrazenou instancí virtuálního počítače. Další informace najdete v tématu [vyhrazenou instancí virtuálního počítače nabídky](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Vyhrazená instance virtuálních počítačů můžete zakoupit [portál Azure](https://portal.azure.com). Koupit vyhrazenou instanci virtuálního počítače:
-   Musíte být roli vlastníka pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
-   U předplatného typu Enterprise, musí být povolená rezervace nákupy v [EA portál](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Kupte si Instance vyhrazený virtuální počítač
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** přikoupení Nová rezervace.
4. Vyplňte požadovaná pole. Spuštění virtuálního počítače instancí, které odpovídají vybrané atributy jsou způsobilé pro získání slevu rezervace. Skutečný počet instancím virtuálních počítačů získávající slevy závisí na oboru a množství vybranou.

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název tuto rezervaci.| 
    |Předplatné|Předplatné použít k platbě za rezervace. Způsob platby u předplatného je účtován předem náklady na rezervace. Typ odběru musí být smlouvu enterprise agreement (číslo nabídky: MS-AZR - 0017P) nebo průběžné platby (číslo nabídky: MS-AZR - 0003P). Předplatné enterprise jsou poplatků odečtené od vyrovnávání peněžních závazků zápisu nebo účtován jako Nadlimitní události úrovně. Pro předplatné s průběžnými platbami se účtují poplatky přijímáte platební kartu ani faktury způsobu platby u předplatného.|    
    |Rozsah       |Rezervace pro obor může zahrnovat jeden odběr jeden nebo více odběrů (sdílené rozsah). Pokud zvolíte možnost: <ul><li>Jeden odběr - slevu rezervace se použije u virtuálních počítačů v tomto předplatném. </li><li>Sdílené - slevu rezervace se použije pro virtuální počítače spuštěné ve všech předplatných v rámci vaší fakturace kontextu. Pro podnikové zákazníky oboru sdílené registraci a zahrnuje všechny odběry (s výjimkou vývoje/testování odběry) v rámci registrace. Pro zákazníky průběžné platby sdílené rozsah je všechny odběry průběžné platby vytvořený účet správce.</li></ul>|
    |Umístění    |Oblast Azure, která je předmětem rezervace.|    
    |Velikost virtuálního počítače     |Velikost instance virtuálních počítačů.|
    |Označení        |Jeden rok nebo tři roky.|
    |Množství    |Počet instancí, které se v rámci rezervace zakoupili. Množství je počet spuštěných instancí virtuálních počítačů, které můžete získat fakturační slevy. Například pokud používáte 10 virtuálních počítačů Standard_D2 ve východní USA, pak zadáte množství jako 10 a maximalizovat pro všechny spuštěné počítače. |
5. Když vyberete můžete zobrazit náklady rezervace **vypočítat náklady na**.

    ![Snímek obrazovky před odesláním rezervace nákupu](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Vyberte **nákupu**.
7. Vyberte **zobrazit tuto rezervaci** zobrazíte stav nákupu.

    ![Snímek obrazovky před odesláním rezervace nákupu](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Další postup 
Diskontní rezervace je automaticky použity počet spuštěných virtuálních počítačů, které odpovídají oboru rezervace a atributy. Můžete aktualizovat oboru rezervace prostřednictvím [portál Azure](https://portal.azure.com), prostředí PowerShell, rozhraní příkazového řádku nebo pomocí rozhraní API. 

Další informace o správě rezervace naleznete v tématu [spravovat instancí Azure vyhrazené virtuální počítač](../articles/billing/billing-manage-reserved-vm-instance.md).

Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače](../articles/billing/billing-save-compute-costs-reservations.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows není součástí vyhrazené instance](../articles/billing/billing-reserved-instance-windows-software-costs.md)