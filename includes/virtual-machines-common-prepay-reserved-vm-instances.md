---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/14/2019
ms.openlocfilehash: c7ff7ab0800449c2a3aa9d58bd036981caccaa1c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156262"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Předplatit si virtuální počítače se službou Azure Reserved VM Instances (RI)

Pro virtuální počítače předem a ušetřete peníze s instancemi Azure Reserved Virtual Machine (VM). Další informace najdete v tématu [nabídky Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Můžete si koupit rezervované Instance virtuálního počítače [webu Azure portal](https://portal.azure.com). Chcete-li zakoupit instanci:

- Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise **přidat Reserved Instances** musí být povolená v [portál EA](https://ea.azure.com). Nebo, pokud je toto nastavení zakázané, musíte být správce EA na předplatné.
- Do programu Cloud Solution Provider (CSP) pouze správce agentů nebo obchodní zástupci koupit rezervace.

Sleva za rezervaci se automaticky použije na počet spuštěných virtuálních počítačů, které odpovídají obor rezervace a atributy. Můžete aktualizovat rozsah rezervace prostřednictvím [webu Azure portal](https://portal.azure.com), prostředí PowerShell, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Určení správné velikosti virtuálního počítače před zakoupením

Předtím, než můžete koupit rezervaci, byste měli určit velikost virtuálního počítače, které potřebujete. V následujících částech vám pomůže určit správné velikosti virtuálního počítače.

### <a name="use-reservation-recommendations"></a>Doporučení pro použití v rezervace

Rezervace doporučení můžete použít k určení rezervace, které byste si zakoupit.

- Doporučení k nákupu a doporučené množství jsou zobrazit, když si koupíte rezervovanou instanci virtuálního počítače na webu Azure Portal.
- Azure Advisor poskytuje doporučení nákupu pro jednotlivá předplatná.  
- Rozhraní API můžete využít k získání nákupní doporučení pro sdílený obor a rozsahem jednoho předplatného. Další informace najdete v tématu [rezervované instance purchase doporučení rozhraní API pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pro zákazníky EA, nákupní doporučení pro sdílené a obory jednoho předplatného jsou dostupné [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="classic-vms-and-cloud-services"></a>Klasické virtuální počítače a cloudové služby

Rezervované instance virtuálních počítačů automaticky platí pro obě klasické virtuální počítače a cloudové služby, když je povolená velikost flexibilita instancí. Nejsou k dispozici žádné speciální SKU pro klasické virtuální počítače nebo cloudové služby. Stejné skladové položky virtuálních počítačů na ně vztahují.

Například může převést klasické virtuální počítače nebo cloudové služby na virtuální počítače založené na Azure Resource Manageru. V tomto příkladu platí sleva za rezervaci automaticky na odpovídající virtuální počítače. Není nutné k *exchange* existující rezervovaná instance – automaticky použije.

### <a name="analyze-your-usage-information"></a>Analyzovat informace o použití
Je vhodné analyzovat informace o použití sloužící k určení rezervace, které byste si zakoupit.

Data o využití je k dispozici v souboru využití a rozhraní API. Je používáte společně k určení, které rezervace zakoupit. Měli byste zkontrolovat pro instance virtuálních počítačů, které mají vysoké využití na každý den k určení množství rezervací k nákupu.

Vyhněte `Meter` podkategorie a `Product` využití datových polí. Nemusíte se rozlišovat mezi velikosti virtuálních počítačů, které používají službu premium storage. Pokud použijete tato pole k určení velikosti virtuálního počítače pro nákup rezervace, může si koupit nesprávnou velikost. Pak nebude získat slevu na rezervaci, které očekáváte. Místo toho odkazovat `AdditionalInfo` v souboru využití nebo rozhraní API využití k určení správné velikosti virtuálních počítačů.

### <a name="purchase-restriction-considerations"></a>Důležité informace o nákupu omezení

Rezervované instance virtuálních počítačů jsou dostupné pro většinu velikostí virtuálních počítačů s několika výjimkami. Pro následující virtuální počítače se nevztahují slevy na rezervaci:

- **Virtuální počítač řady** -A-series, Av2-series a G-series.

- **Virtuální počítače ve verzi preview** – všechny řady virtuálních počítačů nebo velikost, která je ve verzi preview.

- **Cloudy** -rezervace nejsou dostupné ke koupi v oblastí Německo a Čína.

- **Nedostatečná kvóta** -rezervace, který je v oboru pro jedno předplatné musí mít kvóty virtuálních procesorů dostupných v předplatném pro nové rezervovaných Instancí. Například pokud cílové předplatné má kvótu virtuálních procesorů 10 pro řady D-Series, pak nelze koupíte rezervace pro 11 Standard_D1 instancí. Kontrola kvóty pro rezervace zahrnují virtuální počítače už nasazená v předplatném. Například pokud předplatné má kvótu virtuálních procesorů 10 pro řady D-Series a má dvě instance standard_D1 nasazený, pak můžete koupit rezervaci pro 10 standard_D1 instancí v tomto předplatném. Je možné [vytvoření nabídky žádosti o zvýšení](../articles/azure-supportability/resource-manager-core-quotas-request.md) k vyřešení tohoto problému.

- **Omezení kapacity** – ve výjimečných případech, omezení pro nákup rezervace nový dílčí virtuálního počítače o velikosti, z důvodu nízká kapacita v oblasti Azure.

## <a name="buy-a-reserved-vm-instance"></a>Nákup instancí rezervovaných virtuálních počítačů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte **přidat** koupit novou rezervaci.
4. Vyplňte požadovaná pole. Instance spuštěného virtuálního počítače, které odpovídají atributy, které jste vybrali kvalifikovat pro získání sleva za rezervaci. Skutečný počet instancí virtuálních počítačů, které získávají slevy závisí na rozsah a množství vybranou.

    | Pole      | Popis|
    |------------|--------------|
    |Název        |Název této rezervace.|
    |Předplatné|Předplatné použité k úhradě rezervace. Způsob platby v rámci předplatného účtuje pořizovací náklady pro rezervaci. Tento typ předplatného musí být smlouvu enterprise agreement (nabízejí čísla: MS-AZR-0017P nebo MS-AZR - 0148 P) nebo s průběžnými platbami (nabízejí čísla: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|    
    |Scope       |Obor pro rezervaci může vztahovat na jedno předplatné jeden nebo více odběrů (sdíleném oboru). Pokud vyberete: <ul><li>Jedno předplatné – sleva za rezervaci se používá pro virtuální počítače v tomto předplatném. </li><li>Na úrovni Shared – sleva za rezervaci se použijí pro virtuální počítače spuštěné v rámci všech předplatných v rámci kontextu vaší fakturace. Pro podnikové zákazníky sdílený obor je registrace a obsahuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Oblast    |Oblast Azure, která je předmětem rezervace.|    
    |Velikost virtuálního počítače     |Velikost instancí virtuálních počítačů.|
    |Optimalizovat pro     |Flexibilita velikost instancí virtuálních počítačů platí sleva za rezervaci pro ostatní virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Priorita kapacity upřednostňuje kapacitu datacentra pro vaše nasazení. To nabízí máte ještě větší jistotu budete moct spustit instance virtuálních počítačů, když je potřebujete. Priorita kapacity je k dispozici, pouze pokud obor rezervace je jedno předplatné. |
    |Termín        |Jeden nebo tři roky.|
    |Množství    |Počet instancí se zakoupených v rámci rezervace. Počet spuštěných instancí virtuálních počítačů, které můžete získat slevu fakturace je množství. Například pokud spustíte 10 Standard_D2 virtuálních počítačů v oblasti východní USA, potom zadáte množství jako 10 a maximalizovat pro všechny počítače spuštěný. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>Změňte rezervaci po nákupu

Rezervace po nákupu můžete provádět následující typy změn:

- Aktualizovat obor rezervace
- Flexibilita instancí velikost (Pokud je k dispozici)
- Vlastnictví

Také můžete rozdělit do menších bloků dat a sloučení už rozdělit rezervace rezervaci. Žádná ze změn způsobí nové obchodní transakce nebo změnit koncové datum rezervaci.

Následující typy změn nelze změnit po zakoupení přímo:

- Stávající rezervaci oblasti
- Skladová jednotka
- Množství
- Trvání

Můžete však *exchange* rezervace, pokud chcete provádět změny.

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Pokud potřebujete zrušit svou rezervaci, může se vám účtovat poplatek za předčasné ukončení ve výši 12 %. Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší. Výše vrácených peněz je omezená na 50 000 USD za rok. Výše vrácených peněz, které obdržíte, je zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Pokud chcete požádat o zrušení, přejděte na rezervaci v Azure portal a vyberte **náhrada** vytvořit žádost o podporu.

Pokud potřebujete u své rezervace rezervovaných instancí virtuálních počítačů změnit oblast, skupinu velikostí virtuálních počítačů nebo období, můžete ji vyměnit za jinou rezervaci stejné nebo vyšší hodnoty. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. 1 nebo 3 roky termín začíná od když vytvoříte novou rezervaci. Žádost o exchange, přejděte na rezervaci na webu Azure Portal a vyberte **Exchange** vytvořit žádost o podporu.

Další informace o tom, jak exchange nebo náhrady rezervace, naleznete v tématu [výměny rezervaci a vrácení peněz](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](../articles/billing/billing-manage-reserved-vm-instance.md).
- Další informace o rezervacích Azure, najdete v následujících článcích:
    - [Co jsou Azure rezervace?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Vysvětlení, jak se sleva za rezervaci použije](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software Windows, které nejsou součástí rezervace](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
