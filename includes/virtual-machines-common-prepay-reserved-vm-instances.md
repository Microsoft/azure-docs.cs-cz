---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/30/2018
ms.openlocfilehash: 1da2278eee6fcea5c013e9c2f5f4ad3e3013b590
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906301"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Předplatit si virtuální počítače se službou Azure Reserved VM Instances

Pro virtuální počítače předem a ušetřete peníze s instancemi Azure Reserved Virtual Machine (VM). Další informace najdete v tématu [nabídky Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Můžete si koupit rezervované Instance virtuálního počítače [webu Azure portal](https://portal.azure.com). Chcete-li zakoupit instanci:

- Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise, musí být v povoleno nákup rezervace [portál EA](https://ea.azure.com).
- Do programu Cloud Solution Provider (CSP) pouze správce agentů nebo obchodní zástupci koupit rezervace.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Určení správné velikosti virtuálního počítače před zakoupením

Pole podkategorie měřiče a produktu v datech využití není rozlišovat mezi velikosti virtuálních počítačů, které používají službu premium storage z virtuálních počítačů, které ji nemají. Používáte-li tato pole k určení velikosti virtuálního počítače pro rezervaci, mohou koupit nesprávnou velikost a nebude získat slevu na rezervaci, které očekáváte. K určení správné velikosti virtuálního počítače při nákupu rezervace použijte jednu z následujících metod:

- Odkazujte na pole AdditionalInfo v souboru využití nebo rozhraní API využití k určení správné velikosti virtuálních počítačů. Nepoužívejte hodnoty z polí podkategorie měřiče nebo produktu. Tato pole není rozdíl mezi verzí S a Non-S virtuálního počítače.
- Získat přesné informace o velikosti virtuálních počítačů pomocí Powershellu, Azure Resource Manageru, nebo z virtuálního počítače podrobnosti na webu Azure Portal.

Rezervované instance virtuálních počítačů jsou dostupné pro většinu velikostí virtuálních počítačů s několika výjimkami:

- Sleva za rezervaci neplatí pro následující virtuální počítače:
  - Klasické virtuální počítače a cloudové služby
  - Řady virtuálních počítačů: Řady A-series, Av2-series a G-series
  - Virtuální počítače ve verzi Preview: Všechny řady virtuálních počítačů nebo velikost, která je ve verzi preview
- Cloudů: Rezervace nejsou dostupné ke koupi v oblastí Německo a Čína.
- Nedostatečná kvóta: Rezervace, který je v oboru pro jedno předplatné musí mít kvóty virtuálních procesorů dostupných v předplatném pro nové rezervovaných Instancí. Například pokud cílové předplatné má kvótu virtuálních procesorů 10 pro řady D-Series, pak nelze koupíte rezervace pro 11 Standard_D1 instancí. Kontrola kvóty pro rezervace zahrnují virtuální počítače už nasazená v předplatném. Například pokud předplatné má kvótu virtuálních procesorů 10 pro řady D-Series a má dvě instance standard_D1 nasazený, pak můžete koupit rezervaci pro 10 standard_D1 instancí v tomto předplatném. 
- Kapacitní omezení: Ve výjimečných případech omezeních nákup rezervace nový dílčí velikostí virtuálních počítačů z důvodu nízká kapacita v oblasti Azure.

## <a name="buy-a-reserved-vm-instance"></a>Nákup instancí rezervovaných virtuálních počítačů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** koupit novou rezervaci.
4. Vyplňte požadovaná pole. Instance spuštěného virtuálního počítače, které odpovídají atributy, které jste vybrali kvalifikovat pro získání sleva za rezervaci. Skutečný počet instancí virtuálních počítačů, které získávají slevy závisí na rozsah a množství vybranou.

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název této rezervace.| 
    |Předplatné|Předplatné použité k úhradě rezervace. Způsob platby v rámci předplatného účtuje pořizovací náklady pro rezervaci. Tento typ předplatného musí být smlouvu enterprise agreement (číslo nabídky: MS-AZR-0017P) i pro průběžné platby (číslo nabídky: MS-AZR-0003P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|    
    |Rozsah       |Obor pro rezervaci může vztahovat na jedno předplatné jeden nebo více odběrů (sdíleném oboru). Pokud vyberete: <ul><li>Jedno předplatné – sleva za rezervaci se používá pro virtuální počítače v tomto předplatném. </li><li>Na úrovni Shared – sleva za rezervaci se použijí pro virtuální počítače spuštěné v rámci všech předplatných v rámci kontextu vaší fakturace. Pro podnikové zákazníky sdílený obor je registraci a zahrnuje všechna předplatná (s výjimkou předplatná pro vývoj/testování) během registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Oblast    |Oblast Azure, která je předmětem rezervace.|    
    |Velikost virtuálního počítače     |Velikost instancí virtuálních počítačů.|
    |Optimalizovat pro     |Flexibilita velikost instancí virtuálních počítačů platí sleva za rezervaci pro ostatní virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Priorita kapacity upřednostňuje kapacitu datacentra pro vaše nasazení. To nabízí máte ještě větší jistotu budete moct spustit instance virtuálních počítačů, když je potřebujete. Priorita kapacity je k dispozici, pouze pokud obor rezervace je jedno předplatné. |
    |Označení        |Jeden nebo tři roky.|
    |Množství    |Počet instancí se zakoupených v rámci rezervace. Počet spuštěných instancí virtuálních počítačů, které můžete získat slevu fakturace je množství. Například pokud spustíte 10 Standard_D2 virtuálních počítačů v oblasti východní USA, potom zadáte množství jako 10 a maximalizovat pro všechny počítače spuštěný. |
5. Můžete zobrazit náklady na rezervaci, když vyberete **vypočítat náklady**.

    ![Snímek obrazovky před odesláním nákup rezervace](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Vyberte **Koupit**.
7. Vyberte **zobrazit tuto rezervaci** chcete podívat na stav vašeho nákupu.

    ![Snímek obrazovky po odeslání nákup rezervace](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Pokud potřebujete zrušit svou rezervaci, může se vám účtovat poplatek za předčasné ukončení ve výši 12 %. Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší. Výše vrácených peněz je omezená na 50 000 USD za rok. Výše vrácených peněz, které obdržíte, je zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Pokud chcete požádat o zrušení, přejděte na rezervaci v Azure portal a vyberte **náhrada** vytvořit žádost o podporu.

Pokud potřebujete u své rezervace rezervovaných instancí virtuálních počítačů změnit oblast, skupinu velikostí virtuálních počítačů nebo období, můžete ji vyměnit za jinou rezervaci stejné nebo vyšší hodnoty. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. 1 nebo 3leté období začne okamžikem vytvoření nové rezervace. Žádost o exchange, přejděte na rezervaci na webu Azure Portal a vyberte **Exchange** vytvořit žádost o podporu.

## <a name="next-steps"></a>Další postup

Sleva za rezervaci se automaticky použije na počet spuštěných virtuálních počítačů, které odpovídají obor rezervace a atributy. Můžete aktualizovat rozsah rezervace prostřednictvím [webu Azure portal](https://portal.azure.com), prostředí PowerShell, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API.

Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](../articles/billing/billing-manage-reserved-vm-instance.md).

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Správa rezervací v Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Vysvětlení, jak se sleva za rezervaci použije](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
