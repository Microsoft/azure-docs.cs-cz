---
ms.topic: include
ms.openlocfilehash: c30dbe25252a18e1edaed5ec81d86cc1dd976250
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513878"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Předplatit si virtuální počítače se službou Azure Reserved VM Instances

Pro virtuální počítače předem a ušetřete peníze s instancemi Azure Reserved Virtual Machine (VM). Další informace najdete v tématu [nabídky Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Můžete si koupit rezervované instance Azure [webu Azure portal](https://portal.azure.com). K nákupu rezervovaných instancí:
-   Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
-   Předplatné Enterprise, musí být povolené nákupy rezervovaných instancí ve [portál EA](https://ea.azure.com).
-   Programu Cloud Solution Provider (CSP) můžou nakupovat jenom správce agentů nebo obchodní zástupci rezervovaných instancí.

## <a name="determine-the-right-vm-size-before-purchase"></a>Určení správné velikosti virtuálního počítače před nákupem
Pole podkategorie měřiče a produktu v datech využití nerozlišuje velikosti virtuálních počítačů, které používají službu premium storage z velikostí virtuálních počítačů, které nechcete používat premium storage, pomocí těchto pole k určení virtuální počítač velikost pro nákup rezervace může způsobit nesprávné rezervace zakoupit a nevztahuje Smlouva slevy na rezervaci. Použijte jednu z následujících metod k určení správné velikosti virtuálního počítače pro nákup rezervace.
- Odkazovat AdditionalInfo > ServiceType pole v souboru využití nebo dat o využití rozhraní API. Toto pole bude poskytovat správnou velikost virtuálních počítačů při nasazování virtuálních počítačů, které můžete používat premium storage.
- Můžete také získat přesné informace o velikosti virtuálních počítačů pomocí Powershellu, Azure Resource Manageru nebo z virtuálního počítače podrobnosti na webu Azure Portal.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Nákup instancí rezervovaných virtuálních počítačů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** koupit novou rezervovanou instanci.
4. Vyplňte požadovaná pole. Instance spuštěného virtuálního počítače, které odpovídají atributy, které jste vybrali kvalifikovat získat slevu za rezervovanou instanci. Skutečný počet instancí virtuálních počítačů, které získávají slevy závisí na rozsah a množství vybranou.

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název této vyhrazené instanci.| 
    |Předplatné|Předplatné použité k úhradě rezervované Instance. Pomocí způsobu platby v rámci předplatného se předem vyfakturují náklady na rezervovanou instanci. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P) nebo předplatné s průběžnými platbami (číslo nabídky: MS-AZR-0003P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|    
    |Rozsah       |Rozsah rezervované Instance můžete vztahovat na jedno předplatné jeden nebo více odběrů (sdíleném oboru). Pokud vyberete: <ul><li>Jedno předplatné – slevy rezervované Instance se použijí pro virtuální počítače v tomto předplatném. </li><li>Na úrovni Shared – slevu za rezervovanou instanci platí pro virtuální počítače spuštěné v rámci všech předplatných v rámci kontextu vaší fakturace. Pro podnikové zákazníky sdílený obor je registraci a zahrnuje všechna předplatná (s výjimkou předplatná pro vývoj/testování) během registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Umístění    |Oblast Azure, která je předmětem rezervovaných instancí.|    
    |Velikost virtuálního počítače     |Velikost instancí virtuálních počítačů.|
    |Optimalizovat pro     |Flexibilita velikost instancí virtuálních počítačů platí sleva za rezervaci pro ostatní virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Priorita kapacity rezervuje kapacitu datacentra pro vaše nasazení. To nabízí máte ještě větší jistotu budete moct spustit instance virtuálních počítačů, když je potřebujete. Priorita kapacity je k dispozici, pouze pokud obor rezervace je jedno předplatné. |
    |Označení        |Jeden nebo tři roky.|
    |Množství    |Počet instancí se zakoupených v rámci rezervovaných instancí. Počet spuštěných instancí virtuálních počítačů, které můžete získat slevu fakturace je množství. Například pokud spustíte 10 Standard_D2 virtuálních počítačů v oblasti východní USA, potom zadáte množství jako 10 a maximalizovat pro všechny počítače spuštěný. |
5. Náklady na rezervované Instance můžete zobrazit, když vyberete **vypočítat náklady**.

    ![Snímek obrazovky před odesláním nákup rezervované Instance](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Vyberte **Koupit**.
7. Vyberte **zobrazit tuto rezervaci** chcete podívat na stav vašeho nákupu.

    ![Snímek obrazovky po odeslání nákup rezervované Instance](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Další postup 
Slevu za rezervovanou instanci se automaticky použije na počet spuštěných virtuálních počítačů, které odpovídají rozsah rezervované Instance a atributy. Můžete aktualizovat obor rezervované Instance prostřednictvím [webu Azure portal](https://portal.azure.com), prostředí PowerShell, rozhraní příkazového řádku nebo prostřednictvím rozhraní API. 

Další informace o správě rezervovanou instanci, najdete v článku [rezervované instance v Azure spravovat](../articles/billing/billing-manage-reserved-vm-instance.md).

Další informace o službě Azure reserved instances, naleznete v následujících článcích:

- [Co jsou Azure Reserved VM Instances?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Správa rezervovaných instancí v Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Vysvětlení způsobu uplatňování slev se službou Reserved Instances](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervovaných instancí u předplatného s průběžnými platbami](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervovaných instancí u smlouvy Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Rezervované instance nezahrnují náklady na software Windows](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Rezervované instance v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
