---
title: Co jsou rezervace Azure?
description: Přečtěte si o Azure Reservations a cenách, které můžete ukládat na své virtuální počítače, databáze SQL, Azure Cosmos DB a další náklady na prostředky.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: bb90a9dec161746356b8c13df448718c53626684
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806339"
---
# <a name="what-are-azure-reservations"></a>Co jsou rezervace Azure?

Azure Reservations vám pomůžou ušetřit peníze tím, že se potvrzováním na jednoletý nebo tři roky virtuálních počítačů, SQL Database výpočetní kapacity, Azure Cosmos DB propustnosti nebo jiných prostředků Azure. Potvrzení vám umožní získat slevu na prostředky, které využijete. Rezervace můžou významně snížit svůj virtuální počítač, výpočetní výkon SQL Database, Azure Cosmos DB nebo jiné náklady na prostředky až do 72% při průběžných platbách. Rezervace poskytují fakturační slevu a neovlivňují běhový stav vašich prostředků.

Můžete zaplatit za rezervaci předem nebo měsíčně. Celkové náklady na front-front-a měsíční rezervace jsou stejné a při výběru platby měsíčně neplatíte žádné další poplatky. K dispozici je měsíční platba pro rezervace Azure, ne pro produkty třetích stran.

Rezervaci můžete koupit v [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Proč koupit rezervaci?

Pokud máte virtuální počítače, Azure Cosmos DB nebo databáze SQL, které se spouštějí po dlouhou dobu, můžete si koupit rezervaci a získat tak cenově nejúčinnější možnost. Pokud například průběžně spustíte čtyři instance služby bez rezervace, budou se vám účtovat tarify průběžných plateb. Když si koupíte rezervaci těchto prostředků, okamžitě získáte slevu za rezervaci. Prostředky se už neúčtují podle tarifů průběžných plateb.

## <a name="charges-covered-by-reservation"></a>Poplatky kryté rezervací

Plány služeb:

- **Rezervovaná instance virtuálního počítače** – rezervace pokrývá jenom výpočetní náklady virtuálních počítačů. Nepokrývá další poplatky za software, sítě nebo úložiště.
- **Azure Cosmos DB Rezervovaná kapacita** – rezervace pokrývá propustnost zřízenou pro vaše prostředky. Nepokrývá vám poplatky za úložiště a sítě.
- **SQL Database rezervované Vcore** – s rezervací jsou zahrnuté jenom výpočetní náklady. Licence se účtuje samostatně.
- **SQL Data Warehouse** – rezervace pokrývá cDWU využití. Nevztahují se na úložiště nebo síťové poplatky spojené s využitím SQL Data Warehouse.
- **App Service poplatky za razítko** – rezervace pokrývá používání razítek. Nevztahují se na pracovní procesy, takže jakékoli další prostředky přidružené k razítku se účtují samostatně.

Pro virtuální počítače s Windows a SQL Database můžete pokrýt náklady na licencování pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kdo má nárok na zakoupení rezervace?

Pokud si chcete koupit plán, musíte mít roli vlastníka předplatného v podniku (MS-AZR-0017P nebo MS-AZR-0148P) nebo předplatném s průběžnými platbami (MS-AZR-0003P nebo MS-AZR-0023P). Poskytovatelé Cloud Solution Provider můžou k nákupu Azure reservations použít Azure Portal nebo [Partnerské centrum](/partner-center/azure-reservations) .

Zákazníci smlouva Enterprise (EA) můžou omezit nákupy na správce EA tím, že na portálu EA zakáže možnost **Přidat rezervované instance** . Správce EA musí být vlastníkem předplatného alespoň jednoho předplatného, aby si mohl koupit rezervaci. Možnost je užitečná pro podniky, které mají centralizovaný tým k nákupu rezervací pro různá nákladová centra. Po nákupu můžou centralizované týmy do rezervací přidat vlastníky nákladového centra. Vlastníci potom můžou rezervaci omezit na své odběry. Centrální tým nemusí mít přístup vlastníka předplatného, kde se rezervace zakoupí.

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k předplatným zakoupeným prostřednictvím organizace, Cloud Solution Provider (CSP) a jednotlivé plány s tarify průběžných plateb.

## <a name="scope-reservations"></a>Rezervace oborů

Můžete určit rozsah rezervace u předplatného nebo skupin prostředků. Nastavením oboru pro rezervaci se vybere, kde se použijí úspory rezervace. Po určení oboru rezervace pro skupinu prostředků se slevy za rezervace vztahují jenom na skupinu prostředků – ne na celé předplatné.

### <a name="reservation-scoping-options"></a>Možnosti oboru rezervací

V případě oboru skupiny prostředků máte k dispozici tři možnosti pro vymezení rezervace v závislosti na vašich potřebách:

- **Obor jedné skupiny prostředků** – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.
- **Rozsah jednoho předplatného** – aplikuje slevu na rezervované prostředky ve vybraném předplatném.
- **Sdílený rozsah** – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky smlouva Enterprise je fakturačním kontextem registrace. U jednotlivých předplatných s tarify průběžných plateb jsou oborem fakturace všechna oprávněná předplatná vytvořená správcem účtu.

Při použití slev rezervací na základě vašeho využití Azure zpracuje rezervaci v tomto pořadí:

1. Rezervace s vymezenou skupinou prostředků
2. Rezervace s jedním oborem
3. Rezervace sdíleného oboru

Jedna skupina prostředků může získat slevy rezervace z více rezervací v závislosti na tom, jak zadáte rozsah rezervací.

### <a name="scope-a-reservation-to-a-resource-group"></a>Určení rozsahu rezervace pro skupinu prostředků

Rezervaci můžete omezit na skupinu prostředků při nákupu rezervace nebo nastavíte obor po nákupu. Pro vymezení rezervace na skupinu prostředků musíte být vlastníkem předplatného.

Chcete-li nastavit obor, v Azure Portal klikněte na stránku [rezervace nákupu](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) . Vyberte typ rezervace, který chcete koupit. V nabídce **Vybrat produkt, ve kterém chcete koupit** výběr, změňte hodnotu oboru na jednu skupinu prostředků. Pak vyberte skupinu prostředků.

![Příklad znázorňující výběr nákupu rezervace virtuálních počítačů](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Zobrazí se doporučení nákupu pro skupinu prostředků v rezervaci virtuálního počítače. Doporučení se vypočtou analýzou využití během posledních 30 dnů. Doporučení k nákupu se provádí v případě, že náklady na spouštění prostředků s rezervovanými instancemi jsou levnější než náklady na spouštění prostředků s tarify průběžných plateb. Další informace o doporučeních nákupu rezervací najdete v tématu [získání doporučení pro nákup rezervovaných instancí na základě vzorce používání](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Po zakoupení rezervace můžete obor vždy aktualizovat. Provedete to tak, že přejdete na rezervace, kliknete na **Konfigurace**a znovu zadáte obor rezervace. Přeoborování rezervace není komerční transakce. Váš výraz rezervace se nezměnil. Další informace o aktualizaci oboru najdete v tématu [aktualizace oboru po zakoupení rezervace](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Příklad znázorňující změnu oboru rezervace](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorování a optimalizace využití rezervací

Využití rezervace můžete monitorovat různými způsoby – Azure Portal prostřednictvím rozhraní API nebo prostřednictvím dat o využití. Pokud chcete zobrazit všechny rezervace, ke kterým máte přístup, přejděte na **rezervace** v Azure Portal. Mřížka rezervací zobrazuje poslední zaznamenané procento využití pro rezervaci. Kliknutím na rezervaci zobrazíte dlouhodobé využití rezervace.

Můžete také získat využití rezervace pomocí [rozhraní API](billing-reservation-apis.md#see-reservation-usage) a dat o [využití](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) , pokud jste zákazníkem se smlouvou Enterprise.

Pokud si všimnete, že využití rezervovaného rozsahu skupiny prostředků je nízké, můžete obor rezervací aktualizovat na jedno předplatné nebo ho sdílet v rámci fakturačního kontextu. Rezervaci můžete také rozdělit a použít výsledné rezervace na jiné skupiny prostředků.

### <a name="other-considerations"></a>Další důležité informace

Pokud ve skupině prostředků nemáte stejné prostředky, rezervace se nevyužívá. Rezervace se automaticky nepoužije pro jinou skupinu prostředků nebo předplatné, u kterých existuje nízké využití.

Obor rezervace se automaticky neaktualizuje, Pokud přesouváte skupinu prostředků z jednoho předplatného do jiného. Pokud odstraníte skupinu prostředků, obor se neaktualizuje. Bude nutné přeřadit [obor rezervace](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). V opačném případě bude rezervace nevyužitá.

## <a name="discounted-subscription-and-offer-types"></a>Zlevněné typy předplatného a nabídek

Slevy za rezervace platí pro následující oprávněné typy předplatných a nabídek.

- Smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P)
- Individuální plány s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P)
- Předplatná CSP

Prostředky, které se spouští v rámci předplatného s jinými typy nabídek, neobdrží slevu za rezervace.

## <a name="how-is-a-reservation-billed"></a>Jak se fakturuje rezervace?

K rezervaci se účtuje způsob platby spojený s předplatným. Pokud máte předplatné Enterprise, náklady na rezervaci se odečtou z bilance vašeho peněžního závazku. Když zůstatek peněžních závazků nepokrývá náklady na rezervaci, účtuje se za nadlimitní využití. Pokud máte předplatné z jednotlivého plánu s tarify průběžných plateb, platební karta, kterou máte v účtu, se účtuje hned za nákupy předem. Na faktuře se zobrazí měsíční platby a kreditní karta se účtuje měsíčně. Pokud se fakturuje podle faktury, uvidíte poplatky na vaší další faktuře.

## <a name="how-reservation-discount-is-applied"></a>Jak se používá sleva rezervace

Sleva rezervace se vztahuje na využití prostředků, které odpovídá atributům, které vyberete při nákupu rezervace. Mezi atributy patří obor, ve kterém se spouští vyhovující virtuální počítače, databáze SQL, Azure Cosmos DB nebo jiné prostředky. Pokud například chcete slevu za rezervované slevy pro čtyři standardní virtuální počítače D2 v oblasti Západní USA, vyberte předplatné, ve kterém jsou virtuální počítače spuštěné.

Slevou rezervace je "*použití-IT-nebo-ztráta-IT*". Pokud nemáte odpovídající prostředky v průběhu hodiny, ztratíte množství rezervace za tuto hodinu. Nevyužité rezervované hodiny nemůžete přenést.

Po vypnutí prostředku se sleva rezervace automaticky použije na jiný shodný prostředek v zadaném oboru. Pokud v zadaném oboru nebyly nalezeny žádné vyhovující prostředky, budou rezervované hodiny *ztraceny*.

Můžete například později vytvořit prostředek a mít odpovídající rezervaci, která je nevyužitá. Sleva rezervace se automaticky vztahuje na nový vyhovující prostředek.

Pokud virtuální počítače běží v různých předplatných v rámci registrace nebo účtu, vyberte obor jako sdílený. Sdílený rozsah umožňuje použití slevy rezervace v rámci předplatných. Po zakoupení rezervace můžete obor změnit. Další informace najdete v tématu [správa Azure reservations](billing-manage-reserved-vm-instance.md).

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k podnikovým, CSP nebo předplatným podle tarifů průběžných plateb. Prostředky, které se spouští v rámci předplatného s jinými typy nabídek, neobdrží slevu za rezervace.

## <a name="when-the-reservation-term-expires"></a>Po vypršení platnosti podmínky rezervace

Na konci rezervovaného období vyprší platnost fakturační slevy. Virtuální počítač, databáze SQL, Azure Cosmos DB nebo jiný prostředek se fakturují podle ceny za průběžné platby. Azure Reservations se automaticky neobnovuje. Pokud chcete i nadále získávat fakturační slevu, musíte si koupit novou rezervaci pro oprávněné služby a software.

## <a name="discount-applies-to-different-sizes"></a>Sleva se vztahuje na různé velikosti.

Když si koupíte rezervaci, sleva se může vztahovat na jiné instance s atributy, které se nacházejí ve stejné skupině velikostí. Tato funkce se označuje jako flexibilita velikosti instance. Flexibilita pokrytí slev závisí na typu rezervace a atributy, které vyberete při nákupu rezervace.

Plány služeb:

- Rezervované instance virtuálních počítačů: Když zakoupíte rezervaci a vyberete **optimalizovanou pro flexibilitu velikosti instance**, bude pokrytí slevy záviset na velikosti virtuálního počítače, kterou vyberete. Tato rezervace se může vztahovat na velikosti virtuálních počítačů ve stejné skupině řady. Další informace najdete v tématu [flexibilita velikosti virtuálních počítačů pomocí rezervovaných instancí virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SQL Database Rezervovaná kapacita: Pokrytí slevy závisí na úrovni výkonu, kterou zvolíte. Další informace najdete v tématu [Vysvětlení způsobu použití slevy za rezervaci Azure](billing-understand-reservation-charges.md).
- Azure Cosmos DB Rezervovaná kapacita: Pokrytí slevy závisí na zřízené propustnosti. Další informace najdete v tématu [Vysvětlení způsobu, jakým se používá Azure Cosmos DB sleva při rezervaci](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Oznámení o rezervacích

V závislosti na tom, jak platíte za předplatné Azure, e-mailem rezervace pro následující uživatele ve vaší organizaci pošleme oznámení. Oznámení se odesílají pro různé události, včetně těchto:

- Koupit
- Nadcházející doba rezervace
- Expiry
- Obnovit
- Výmaz
- Změna oboru

Pro zákazníky s předplatnými EA:
- Prodejnímu uživateli se pošle oznámení o nákupu a kontaktním zprávám EA.
- Další oznámení o životním cyklu rezervace se odesílají jenom do kontaktních zpráv EA.
- Uživatelé přidaní do rezervace s použitím oprávnění RBAC (IAM) neobdrží žádná e-mailová oznámení.

Pro zákazníky s jednotlivými předplatnými:
- Nákupčí obdrží oznámení o nákupu.
- V době nákupu obdrží vlastník fakturačního účtu předplatného oznámení o nákupu.
- Vlastník účtu obdrží všechna ostatní oznámení.


## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- Další informace o Azure Reservations najdete v následujících článcích:
    - [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
    - [Vysvětlení využití rezervací pro vaše předplatné s tarify průběžných plateb](billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervací u smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software systému Windows, které nejsou součástí rezervací](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations v programu partner Center pro Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Další informace o rezervacích pro plány služeb:
    - [Virtual Machines s Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Prostředky Azure Cosmos DB s využitím Azure Cosmos DB rezervované kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL Database výpočetní prostředky s využitím Azure SQL Database rezervované kapacity](../sql-database/sql-database-reserved-capacity.md) Další informace o rezervacích pro softwarové plány:
    - [Softwarové plány Red Hat z Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE softwarové plány z Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)
