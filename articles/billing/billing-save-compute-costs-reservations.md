---
title: Co jsou rezervace Azure?
description: Informace o rezervacích Azure a úsporách nákladů na virtuální počítače, databáze SQL, službu Azure Cosmos DB a další prostředky
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 02a4ccc1bd7c18c36a7203f7d81cce8923ecf59a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499240"
---
# <a name="what-are-azure-reservations"></a>Co jsou rezervace Azure?

Rezervace Azure umožňují šetřit peníze tím, že potvrzují závazek využívání virtuálních počítačů, výpočetní kapacity služby SQL Database, propustnosti služby Azure Cosmos DB nebo jiných prostředků Azure na jeden nebo tři roky. Díky potvrzení závazku můžete získat slevu na prostředky, které využíváte. Rezervace můžou výrazně snížit náklady na virtuální počítače, výpočetní výkon databází SQL, službu Azure Cosmos DB a další prostředky, a to až o 72 % oproti průběžným platbám. Rezervace poskytují slevu z faktury a neovlivňují běhový stav prostředků.

Za rezervaci můžete zaplatit předem nebo prostřednictvím měsíčních plateb. Celkové náklady na rezervaci placenou předem a rezervaci s měsíčními platbami jsou stejné. Pokud tedy zvolíte měsíční platby, neplatíte žádné další poplatky. Měsíční platba je k dispozici pro rezervace Azure, ne pro produkty třetích stran.

Rezervaci můžete zakoupit na webu [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Proč zakoupit rezervaci?

U virtuálních počítačů, služby Azure Cosmos DB nebo databází SQL, které běží po dlouhé časové úseky, představuje zakoupení rezervace nákladově nejvýhodnější možnost. Pokud například nepřetržitě provozujete čtyři instance služby bez rezervace, účtují se vám sazby pro průběžné platby. Když pro tyto prostředky zakoupíte rezervaci, získáte ihned slevu za rezervaci. Za tyto prostředky se už nebudou účtovat sazby pro průběžné platby.

## <a name="charges-covered-by-reservation"></a>Poplatky, na které se rezervace vztahuje

Plány služby:

- **Rezervovaná instance virtuálního počítače** – Rezervace se vztahuje jenom na náklady na výpočetní funkce virtuálních počítačů. Nevztahuje se na další poplatky za software, sítě nebo úložiště.
- **Rezervovaná kapacita služby Azure Cosmos DB** – Rezervace se vztahuje na propustnost zřízenou pro vaše prostředky. Nevztahuje se na poplatky za úložiště a sítě.
- **Rezervované virtuální jádro služby SQL Database** – Rezervace se vztahuje jenom na náklady na výpočetní funkce. Licence se fakturuje samostatně.
- **SQL Data Warehouse** – Rezervace se vztahuje na využití cDWU. Nevztahuje se na poplatky za úložiště nebo sítě související s využitím služby SQL Data Warehouse.
- **Poplatek za kolek služby App Service** – Rezervace se vztahuje na využití kolku. Nevztahuje se na pracovní procesy, takže všechny další prostředky přidružené ke kolku se účtují samostatně.

U virtuálních počítačů s Windows a služby SQL Database můžete náklady na licencování pokrýt prostřednictvím programu [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kdo má nárok na zakoupení rezervace?

Pokud si chcete koupit plán, musíte mít roli vlastníka předplatného v předplatném se smlouvou Enterprise (MS-AZR-0017P nebo MS-AZR-0148P), předplatném s průběžnými platbami (MS-AZR-0003P nebo MS-AZR-0023P) nebo předplatném se smlouvou se zákazníkem Microsoftu. Poskytovatelé cloudových řešení můžou nakupovat rezervace Azure prostřednictvím webu Azure Portal nebo  [Partnerského centra](/partner-center/azure-reservations) .

Zákazníci se smlouvou Enterprise (EA) můžou omezit nákupy na správce EA tak, že na portálu EA Portal zakážou možnost **Přidat rezervované instance**. Aby správce EA mohl zakoupit rezervaci, musí být vlastníkem alespoň jednoho předplatného EA. Tato možnost je užitečná pro podniky, které chtějí, aby rezervace pro různá nákladová centra pořizoval centralizovaný tým. Po zakoupení můžou centralizované týmy přidat k rezervacím vlastníky nákladových center. Vlastníci potom vymezí rozsah rezervace na svoje předplatná. Centrální tým nemusí umožnit přístup vlastníka předplatného tam, kde se rezervace pořizuje.

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k předplatným zakoupeným prostřednictvím plánů Enterprise, Cloud Solution Provider (CSP), plánů se smlouvou se zákazníkem Microsoftu a individuálních plánů s průběžnými platbami.

## <a name="scope-reservations"></a>Vymezení rozsahů rezervací

Rozsah rezervace můžete vymezit na předplatné nebo na skupiny prostředků. Nastavením rozsahu rezervace se určí, kde se úspory získané rezervací uplatní. Pokud vymezíte rozsah rezervace na skupinu prostředků, uplatní se slevy za rezervaci jenom u této skupiny prostředků – ne u celého předplatného.

### <a name="reservation-scoping-options"></a>Možnosti vymezení rozsahů rezervací

Při vymezování rozsahu rezervace na skupinu prostředků máte v závislosti na vašich potřebách k dispozici tři možnosti:

- **Rozsah na jednu skupinu prostředků** – Sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
- Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
- Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace příslušná registrace. U zákazníků se smlouvou se zákazníkem Microsoftu je rozsahem fakturace příslušný fakturační profil. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.

Při uplatňování slev za používání Azure zpracovává rezervace v tomto pořadí:

1. Rezervace s rozsahem na jednu skupinu prostředků
2. Rezervace s rozsahem na jedno předplatné
3. Rezervace se sdíleným rozsahem

Jedna skupina prostředků může získat slevy za rezervaci z více rezervací v závislosti na tom, jak vymezíte rozsahy vašich rezervací.

### <a name="scope-a-reservation-to-a-resource-group"></a>Vymezení rozsahu rezervace na skupinu prostředků

Rozsah rezervace na skupinu prostředků můžete vymezit při zakoupení rezervace, nebo můžete nastavit rozsah až po pořízení. Abyste mohli vymezit rozsah rezervace na skupinu prostředků, musíte být vlastníkem předplatného.

Pokud chcete vymezit rozsah, přejděte na webu Azure Portal na stránku [Koupit rezervace](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand). Vyberte typ rezervace, který chcete zakoupit. Ve výběrovém formuláři **Vybrat produkt, který chcete koupit** změňte hodnotu Rozsah na Jedna skupina prostředků. Pak vyberte skupinu prostředků.

![Příklad znázorňující výběr při zakoupení rezervace virtuálních počítačů](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Zobrazí se doporučení k nákupu skupiny prostředků v rezervaci virtuálních počítačů. Doporučení se počítají pomocí analýzy využití za posledních 30 dnů. Doporučení k nákupu se provede v případě, že náklady na použití prostředků s rezervovanými instancemi jsou nižší než náklady na použití prostředků se sazbami pro průběžné platby. Další informace o doporučeních k nákupu rezervací najdete v článku o [získání doporučení k nákupu rezervovaných instancí na základě vzorců používání](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Po zakoupení rezervace máte možnost rozsah aktualizovat. Provedete to tak, že přejdete k rezervaci, kliknete na **Konfigurace** a změníte rozsah této rezervace. Změna rozsahu rezervace není komerční transakcí. Období vaší rezervace se nezmění. Další informace o aktualizaci rozsahu najdete v tématu [Aktualizace rozsahu po zakoupení rezervace](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Příklad znázorňující změnu rozsahu rezervace](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorování a optimalizace využití rezervací

Využití rezervace můžete monitorovat různými způsoby – prostřednictvím webu Azure Portal, prostřednictvím rozhraní API nebo prostřednictvím dat o využití. Pokud chcete zobrazit všechny rezervace, ke kterým máte přístup, přejděte na webu Azure Portal na **Rezervace**. Mřížka rezervací zobrazuje poslední zaznamenané procento využití rezervací. Kliknutím na rezervaci zobrazíte dlouhodobé využití příslušné rezervace.

Pokud jste zákazníkem se smlouvou Enterprise nebo smlouvou se zákazníkem Microsoftu, můžete informace o využití rezervace získat také pomocí [rozhraní API](billing-reservation-apis.md#see-reservation-usage) a z [dat o využití](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks).

Pokud zjistíte, že využití rezervace s rozsahem na skupinu prostředků je nízké, můžete rozsah rezervace aktualizovat na jedno předplatné nebo ho sdílet v rámci kontextu fakturace. Rezervaci můžete také rozdělit a výsledné rezervace použít na jiné skupiny prostředků.

### <a name="other-considerations"></a>Další aspekty

Pokud nemáte ve skupině prostředků odpovídající prostředky, rezervace bude nevyužitá. Rezervace s nízkým využitím se automaticky neuplatní pro jinou skupinu prostředků nebo předplatné.

Pokud přesunete skupinu prostředků z jednoho předplatného do jiného, rozsah rezervace se automaticky neaktualizuje. Pokud odstraníte skupinu prostředků, rozsah se neaktualizuje. Budete muset [změnit rozsah rezervace](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). Jinak bude rezervace nevyužitá.

## <a name="discounted-subscription-and-offer-types"></a>Typy zlevněných předplatných a nabídek

Slevy za rezervace platí pro následující oprávněné typy předplatných a nabídek.

- Smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P)
- Předplatná se smlouvou se zákazníkem Microsoftu
- Individuální plány s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P)
- Předplatná CSP

Prostředky, které se spouští v rámci předplatného s jinými typy nabídek, neobdrží slevu za rezervace.

## <a name="how-is-a-reservation-billed"></a>Jak se rezervace fakturuje?

Rezervace se fakturuje za použití způsobu platby spojeného s předplatným. Náklady na rezervaci se odečtou ze zůstatku peněžního závazku, pokud je k dispozici. Pokud zůstatek peněžního závazku nepokryje náklady na rezervaci, bude se vám účtovat nadlimitní využití. Pokud máte předplatné z individuálního plánu s průběžnými platbami, z platební karty přidružené k vašemu účtu se ihned strhne částka za nákup placený předem. Měsíční platby se objeví na vaší faktuře a částka se bude každý měsíc strhávat z vaší platební karty. Při fakturaci na základě faktury uvidíte poplatky na vaší další faktuře.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Sleva za rezervaci se uplatňuje na využití prostředků odpovídající atributům, které vyberete při zakoupení rezervace. Mezi atributy patří rozsah, ve kterém se odpovídající virtuální počítače, databáze SQL, služba Azure Cosmos DB nebo jiné prostředky používají. Pokud například chcete slevu za rezervaci čtyř virtuálních počítačů Standard D2 v oblasti Západní USA, vyberte předplatné, ve kterém se tyto virtuální počítače používají.

Nevyužitá sleva za rezervaci se *nenahrazuje*. Pokud nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny nelze převést na další období.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

Můžete například později vytvořit prostředek a mít odpovídající rezervaci, která je nevyužitá. Sleva za rezervaci se automaticky uplatní na nový vyhovující prostředek.

Pokud se virtuální počítače používají v různých předplatných v rámci prováděcí smlouvy nebo účtu, nastavte rozsah jako sdílený. Sdílený rozsah umožňuje uplatnění slevy za rezervaci napříč předplatnými. Po zakoupení rezervace můžete rozsah změnit. Další informace najdete v tématu [Správa rezervací Azure](billing-manage-reserved-vm-instance.md).

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k předplatným Enterprise, CSP, předplatným se smlouvou se zákazníkem Microsoftu nebo předplatným s průběžnými platbami. Prostředky, které se spouští v rámci předplatného s jinými typy nabídek, neobdrží slevu za rezervace.

## <a name="when-the-reservation-term-expires"></a>Po skončení období rezervace

Na konci rezervačního období vyprší platnost fakturační slevy a prostředky se účtují podle sazeb za průběžné platby. Ve výchozím nastavení není pro rezervace nastavené automatické obnovování. Automatické obnovení rezervace můžete povolit výběrem příslušné možnosti v nastaveních prodloužení. Pokud je nastavené automatické prodloužení, při vypršení platnosti stávající rezervace se koupí náhradní. Náhradní rezervace má ve výchozím nastavení stejné atributy jako rezervace s vypršenou platností. Volitelně můžete v nastaveních prodloužení změnit četnost fakturace, období nebo množství. Prodloužení může nastavit libovolný uživatel, který má přístup k dané rezervaci a k předplatnému použitému pro fakturaci.  

## <a name="discount-applies-to-different-sizes"></a>Sleva se vztahuje na různé velikosti

Když zakoupíte rezervaci, může se sleva uplatnit na jiné instance s atributy, které se nacházejí ve stejné skupině velikostí. Tato funkce se označuje jako flexibilní velikost instance. Flexibilita pokrytí slevou závisí na typu rezervace a atributech vybraných při zakoupení rezervace.

Plány služby:

- Rezervované instance virtuálních počítačů: Pokud zakoupíte rezervaci a vyberete možnost **optimalizace pro flexibilní velikost instance**, bude pokrytí slevou záviset na tom, jakou velikost virtuálního počítače zvolíte. Rezervace se může vztahovat na velikosti virtuálních počítačů ve stejné skupině velikostí. Další informace najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Rezervovaná kapacita služby SQL Database: Pokrytí slevou závisí na úrovni výkonu, kterou zvolíte. Další informace najdete ve [vysvětlení způsobu uplatnění slevy za rezervaci Azure](billing-understand-reservation-charges.md).
- Rezervovaná kapacita služby Azure Cosmos DB: Pokrytí slevou závisí na zřízené propustnosti. Další informace najdete ve [vysvětlení způsobu uplatnění slevy za rezervaci služby Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Oznámení o rezervacích

V závislosti na tom, jak za předplatné Azure platíte, odesíláme oznámení o rezervacích e-mailem následujícím uživatelům ve vaší organizaci. Oznámení se odesílají při různých událostech, včetně těchto:

- Koupit
- Nadcházející vypršení platnosti rezervace
- Vypršení platnosti
- Prodloužení
- Zrušení
- Změna rozsahu rezervace

U zákazníků s předplatnými EA:
- Oznámení o zakoupení se odesílá nákupčímu a kontaktům pro oznámení EA.
- Další oznámení o životním cyklu rezervace se odesílají jenom kontaktům pro oznámení EA.
- Uživatelé přidaní k rezervaci pomocí oprávnění RBAC (IAM) nedostávají žádná e-mailová oznámení.

U zákazníků s individuálními předplatnými:
- Nákupčí obdrží oznámení o zakoupení.
- V okamžiku nákupu obdrží vlastník fakturačního účtu pro dané předplatné oznámení o nákupu.
- Vlastník účtu obdrží všechna další oznámení.


## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
    - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervací u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software pro Windows nezahrnuté v rezervacích](billing-reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Další informace o rezervacích pro plány služby:
    - [Virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Prostředky Azure Cosmos DB s rezervovanou kapacitou služby Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Výpočetní prostředky SQL Database s rezervovanou kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md) Další informace o rezervacích pro plány softwaru:
    - [Plány softwaru Red Hat z rezervací Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Plány softwaru SUSE z rezervací Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
