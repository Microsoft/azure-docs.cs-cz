---
title: Co jsou rezervace Azure?
description: Přečtěte si o Azure rezervace a ceny a Ušetřete na virtuálních počítačů, databází SQL, Azure Cosmos DB a další náklady na prostředky.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cd0a70aa0fb5096c5b0157ae078c961da03109bc
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565312"
---
# <a name="what-are-azure-reservations"></a>Co jsou rezervace Azure?

Azure rezervací můžete ušetřit platíte předem za jeden rok nebo tři roky virtuálních počítačů, SQL Database výpočetní kapacitu, propustnost služby Azure Cosmos DB nebo další prostředky Azure. Platíte předem, můžete získat slevu na prostředky, které používáte. Rezervace může výrazně snížit virtuálního počítače SQL database výpočetní služby Azure Cosmos DB, nebo jiný prostředek stojí až 72 % oproti průběžným platbám. Rezervace poskytovat fakturační slevy a neovlivní jejich běhový stav vašich prostředků.

Můžete si koupit rezervaci [webu Azure portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Proč si koupit rezervaci?

Pokud máte virtuální počítače, služby Azure Cosmos DB nebo databází SQL, které běží dlouhou dobu, nákup rezervace nabízí cenově nejvýhodnější možnost. Například při spuštění průběžně čtyři instance služby bez rezervace, můžete se účtovat tarify průběžných plateb. Pokud zakoupíte rezervaci pro tyto prostředky, získáte okamžitě sleva za rezervaci. Prostředky jsou již účtovat podle tarifů průběžných plateb.

## <a name="charges-covered-by-reservation"></a>Poplatky za předmětem rezervace

Plány služby:

- **Rezervované Instance virtuálního počítače** -rezervaci pokrývá jenom náklady na výpočetní výkon virtuálního počítače. Nezahrnuje další poplatky za software, sítě nebo úložiště.
- **Azure Cosmos DB rezervované kapacity** -rezervaci pokrývá zajištěné propustnosti pro vaše prostředky. Nezahrnuje úložiště a sítě poplatky.
- **Databáze SQL vyhrazený vCore** – pouze náklady na výpočetní výkon jsou součástí rezervaci. Licence se fakturuje samostatně.

Pro virtuální počítače Windows a SQL Database, může zahrnovat náklady na licencování s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kdo má nárok na nákup rezervace?

Koupit plán, musíte mít roli vlastníka předplatného v podniku (MS-AZR - 0017P nebo MS-AZR - 0148P) nebo předplatné s průběžnými platbami (MS-AZR - 003P nebo MS-AZR - 0023P). Poskytovatele cloud solution Provider, můžete pomocí webu Azure portal nebo [partnerského centra](/partner-center/azure-reservations) nákupu rezervace Azure.

Zákazníkům se smlouvou EA může omezit nákupy správcům EA zakázáním **přidat rezervované instance** možnost přes portál EA. Správce EA musí být vlastníkem předplatného pro alespoň jedno předplatné EA si koupit rezervaci. Možnost je užitečná pro podniky, které mají centralizovaný tým k nákupu rezervace pro jiné nákladových středisek. Po nákupu můžete přidat centralizované týmy cost center vlastníky rezervace. Vlastníci můžete pak určit obor rezervace svá předplatná. Centrální tým nemusí mít přístup vlastníka předplatného se nákup rezervace.

Sleva za rezervaci se vztahuje pouze na prostředky spojené s předplatným zakoupeným prostřednictvím Enterprise, CSP a jednotlivé plány průběžných plateb.

## <a name="scope-reservations"></a>Rozsah rezervace

Můžete omezit rozsah rezervace skupinám předplatné nebo prostředek. Nastavuje se obor pro rezervaci vybere kde úspory rezervace platí. Pokud jste-li nastavit obor rezervace do skupiny prostředků, slevy na rezervaci platí pouze pro skupiny prostředků – ne celé předplatné.

### <a name="reservation-scoping-options"></a>Možnosti oboru rezervace

S prostředkem skupina oborů, budete mít tři možnosti, jak určit obor rezervace, v závislosti na potřebách:

- **Jednotného oboru skupiny prostředků** – platí pro odpovídající prostředky ve vybrané skupině prostředků pouze sleva za rezervaci.
- **Jednotného oboru předplatného** – sleva za rezervaci se vztahuje na odpovídající prostředkům ve vybraném předplatném.
- **Sdílený obor** – sleva za rezervaci se vztahuje na odpovídající zdroje v oprávněné předplatné, které jsou v kontextu fakturace. Pro zákazníky se smlouvou Enterprise je kontext fakturace registraci. Jednotlivých předplatných s průběžnými sazbami fakturační rozsah je všechny oprávněné předplatné vytvořili správce účtu.

Při použití slevy na rezervaci na využití, zpracovává Azure rezervace v následujícím pořadí:

1. Rezervace, které jsou vymezeny na skupinu prostředků
2. Jeden obor rezervace
3. Sdílený obor rezervace

Jedna skupina prostředků můžete získat slevy na rezervaci více rezervace, v závislosti na tom, jak rozsah vaší rezervace.

### <a name="scope-a-reservation-to-a-resource-group"></a>Rozsah rezervace do skupiny prostředků

Při nákupu rezervace nebo nastavit obor po nákupu, můžete omezit rozsah rezervace do skupiny prostředků. Musíte být vlastníkem předplatného, pokud chcete určit obor rezervace do skupiny prostředků.

Chcete-li nastavit obor, přejděte [nákupu rezervace](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) stránky na webu Azure Portal. Vyberte typ rezervace, které chcete koupit. Na **vyberte produkt, který chcete zakoupit** výběru formuláře, změna **oboru** hodnota, která se **jedna skupina prostředků** a vyberte skupinu prostředků.

![Příklad zobrazující výběr nákup rezervace virtuálních počítačů](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Zobrazí nákupní doporučení pro skupinu prostředků v rezervaci virtuálních počítačů. Doporučení se počítají analýzou vašeho využití v posledních 30 dnech. Nákupní doporučení se provádí v případě, že náklady na prostředky s rezervované instance jsou levnější než náklady na provozování prostředků s průběžných plateb. Další informace o doporučeních nákup rezervace, najdete v článku [získat rezervované Instance purchase doporučení založená na vzor používání](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) blogový příspěvek.

Můžete kdykoli aktualizovat rozsah po nákupu rezervace. Uděláte to tak, přejděte na rezervaci, klikněte na tlačítko **konfigurace** a rescope rezervace. Změna oboru mzdy rezervace není obchodní transakce. Období rezervace se nezmění. Další informace o aktualizaci rozsahu najdete v tématu [aktualizovat rozsah po nákupu rezervace](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Příklad zobrazující změnit obor rezervace](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Sledovat a optimalizovat využití rezervace

Můžete monitorovat využití rezervace několika různými způsoby – prostřednictvím webu Azure portal, prostřednictvím rozhraní API nebo data o využití. Chcete-li zobrazit všechny rezervace, ke kterým máte přístup, přejděte na **rezervace** na webu Azure Portal. Mřížka rezervace ukazuje poslední procento zaznamenané využití pro rezervaci. Klikněte na rezervaci zobrazíte dlouhodobého využívání rezervace.

Můžete také získat pomocí využití rezervace [rozhraní API](billing-reservation-apis.md#see-reservation-usage) a z vašich [data o využití](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) Pokud jste zákazníkem smlouvy enterprise.

Pokud si všimnete, že využití vaší skupiny prostředků obor rezervace je nízký, pak může aktualizovat obor rezervace pro jedno předplatné, nebo ho sdílet napříč kontext fakturace. Můžete také rozdělit rezervace a použít výsledný rezervace do různých skupin prostředků.

### <a name="other-considerations"></a>Další důležité informace

Pokud nemáte k dispozici odpovídající prostředky ve skupině prostředků, bude historického rezervace. Rezervaci neplatí automaticky pro jiné skupiny prostředků nebo předplatného níž se nachází nízké využití.

Obor rezervace neaktualizuje automaticky, pokud přesunete skupinu prostředků z jednoho předplatného do druhého. Budete muset rescope rezervace. V opačném případě bude nevyužité rezervace.

## <a name="discounted-subscription-and-offer-types"></a>Zlevněné typy předplatného a nabídky

Slevy rezervace platí pro následující oprávněné předplatné a nabízí typy.

- Smlouvy Enterprise (nabízejí čísla: MS-AZR-0017P nebo MS-AZR - 0148 P)
- Jednotlivé plány průběžných plateb (nabízejí čísla: MS-AZR-0003P nebo MS-AZR - 0023 P)
- Předplatná CSP

Prostředky, které běží v rámci předplatného s jinými typy nabídky nepřijímají sleva za rezervaci.

## <a name="how-is-a-reservation-billed"></a>Jak se účtuje rezervace?

Rezervace jsou zpoplatněné nad rámec platby vázané na předplatné. Pokud máte předplatné Enterprise, náklady na rezervaci se odečte od váš zůstatek peněžního závazku. Pokud váš zůstatek peněžního závazku využívání služeb nezahrnuje náklady na rezervaci, bude se vám účtovat Nadlimitní využití. Pokud máte předplatné z individuálních plánů s průběžnými sazbami platební karty, kterou máte v účtu je účtována okamžitě. Bude se vám účtovat na faktuře uvidíte poplatky na další faktuře.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva ve výši rezervace

Sleva za rezervaci se vztahuje na využití prostředků odpovídající atributy, které jste vybrali při nákupu rezervace. Atributy zahrnout rozsahu, kde spustit odpovídající virtuální počítače, databáze SQL, Azure Cosmos DB nebo jiné prostředky. Například pokud chcete sleva za rezervaci čtyři virtuální počítače Standard D2 v oblasti západní USA, vyberte předplatné, ve kterém běží virtuální počítače.

Sleva za rezervaci se "*použití – it nebo ztratit – it*". Pokud nemáte k dispozici odpovídající prostředky pro hodinu, potom můžete přijít o množství rezervací pro určitou hodinu. Nelze provést vpřed nevyužité vyhrazené hodin.

Při vypínání prostředek sleva za rezervaci automaticky použije další odpovídající prostředek v zadaném oboru. Pokud systém nenašel žádné odpovídající prostředky v zadaném oboru, pak jsou vyhrazené hodin *ztráty*.

Například může být později vytvořte prostředek a mít odpovídající rezervaci, která je historického. V tomto příkladu sleva za rezervaci automaticky použije nový odpovídající prostředek.

Pokud jsou virtuální počítače spuštěné v různých předplatných v rámci vaší registrace účtu, vyberte obor jako sdílené. Sdílený obor umožňuje sleva za rezervaci použít napříč předplatnými. Rozsah můžete změnit po nákupu rezervace. Další informace najdete v tématu [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).

Sleva za rezervaci platí jenom pro prostředky přidružené organizace, CSP, nebo předplatné s platbami jako můžete přejít sazby. Prostředky, které běží v rámci předplatného s jinými typy nabídky nepřijímají sleva za rezervaci.

## <a name="when-the-reservation-term-expires"></a>Vypršení platnosti období rezervace

Na konci období rezervace fakturace slevy vyprší a virtuální počítač, SQL database, Azure Cosmos DB nebo jiný prostředek se účtuje ve výši platby jako můžete přejít cena. Azure rezervace není automatického obnovení. Pokračovat v uplatňování slevy fakturace, musíte koupit novou rezervaci pro opravňující služby a softwaru.

## <a name="discount-applies-to-different-sizes"></a>Sleva za vztahuje na různé velikosti

Při nákupu rezervace sleva provést u ostatních instancí s atributy, které jsou ve stejné skupině velikostí. Tato funkce se označuje jako velikost flexibilita instancí. Flexibilita pokrytí slevy závisí na typu rezervace a atributy, které můžete vybrat při nákupu rezervace.

Plány služby:

- Rezervované instance virtuálních počítačů: Když koupit rezervaci a vyberete **optimalizovaná pro**: **instance velikost flexibilitu**, pokrytí slevy závisí na velikosti virtuálního počítače, vyberete. Rezervaci můžete použít velikosti virtuálních počítačů (VM) ve stejné skupině velikost series. Další informace najdete v tématu [flexibilitu velikost virtuálního počítače s rezervovanými instancemi virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SQL Database rezervované kapacity: Pokrytí slevy závisí na úrovni výkonu, kterou vyberete. Další informace najdete v tématu [pochopit, jak se použije sleva za rezervaci Azure](billing-understand-reservation-charges.md).
- Azure Cosmos DB rezervované kapacity: Pokrytí slevy závisí na zřízenou propustnost. Další informace najdete v tématu [vysvětlení, používání Azure Cosmos DB sleva za rezervaci](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
    - [Vysvětlení využití rezervace pro vaše předplatné s průběžnými sazbami](billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Další informace o rezervacích pro plány služby:
    - [Virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Rezervované kapacity prostředků Azure Cosmos DB pomocí služby Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Rezervované kapacity výpočetních prostředků SQL Database s využitím Azure SQL Database](../sql-database/sql-database-reserved-capacity.md) Další informace o rezervacích pro softwarové plány:
    - [Plány softwaru Red Hat z Azure rezervací](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Plány softwaru SUSE z Azure rezervací](../virtual-machines/linux/prepay-suse-software-charges.md)
