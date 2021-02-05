---
title: Úspora za Azure App Service s využitím rezervované kapacity
description: Přečtěte si, jak můžete ušetřit náklady na Azure App Service rezervované instance Premium v3 a za izolované poplatky za razítko.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 89e0c62b580c0c354fc7277e61b452005a86e3d9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577785"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Úspora nákladů pomocí Azure App Service rezervovaných instancí

Tento článek vysvětluje, jak můžete ukládat s Azure App Service rezervovaných instancí pro instance Premium v3 a poplatky za izolované razítko.

## <a name="save-with-premium-v3-reserved-instances"></a>Uložit s rezervovanými instancemi Premium V3

Když potvrdíte rezervovanou instanci Azure App Service Premium v3, můžete ušetřit peníze. Sleva rezervace se automaticky použije na počet spuštěných instancí, které odpovídají oboru a atributům rezervace. K získání slevy není nutné přiřadit k instanci rezervaci.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Určení správné rezervované velikosti instance před nákupem

Před nákupem rezervace byste měli určit velikost rezervované instance Premium v3, kterou potřebujete. Následující části vám pomůžou určit správnou velikost rezervované instance úrovně Premium v3.

### <a name="use-reservation-recommendations"></a>Použití doporučení rezervací

K určení rezervací, které byste měli koupit, můžete použít doporučení rezervací.

- Doporučení nákupu a doporučené množství se zobrazí při nákupu rezervované instance Premium V3 v Azure Portal.
- Azure Advisor poskytuje doporučení pro nákup pro jednotlivá předplatná.
- Rozhraní API můžete použít k získání doporučení pro nákup jak pro sdílený obor, tak pro obor jednoho předplatného. Další informace najdete v tématu [rozhraní API pro doporučení pro nákup rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pro zákazníky smlouva Enterprise (EA) a Microsoft Customer Agreement (MCA) jsou k dispozici doporučení pro sdílení a jednotné rozsahy, které jsou k dispozici v [balíčku obsahu Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

#### <a name="instance-size-flexibility-setting"></a>Nastavení flexibility velikosti instance

Nastavení flexibility velikosti instance určuje, které služby mají získat slevy za rezervované instance.

Bez ohledu na to, jestli je nastavení zapnuté nebo vypnuté, se slevy na rezervované instance automaticky vztahují na všechny vyhovující použití rezervované instance Premium v3.

### <a name="analyze-your-usage-information"></a>Analýza informací o použití

Analýza informací o použití vám pomůže určit, které rezervace byste měli koupit. Data o využití jsou k dispozici v souboru využití a v rozhraních API. Použijte je společně k určení rezervace k nákupu. Pro určení množství rezervací, které mají být zakoupeny, zkontrolujte, zda jsou pro instance Premium v3 s vysokým využitím každodenní.

Váš soubor využití zobrazuje vaše poplatky podle fakturačního období a denního využití. Informace o stažení souboru využití najdete v tématu [zobrazení a stažení využití Azure a poplatků](../understand/download-azure-daily-usage.md). Pak můžete pomocí informací o souboru využití [určit, jakou rezervaci chcete koupit](determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Požadavky na omezení nákupu

Slevy za rezervované instance se nevztahují na následující instance Premium V3:

- **Náhled nebo propagační instance** – jakákoli rezervovaná instance řady Premium V3 nebo velikost, která je ve verzi Preview, nebo používá propagační měření.
- **Cloudy** – rezervace nejsou k dispozici pro nákup v oblastech Německo a Čína.

## <a name="buy-a-premium-v3-reserved-instance"></a>Koupit rezervovanou instanci Premium V3

Rezervovanou rezervovanou instanci Premium V3 můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](prepare-buy-reservation.md). Tyto požadavky se vztahují na nákup rezervované instance Premium V3:

- Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s tarifem průběžných plateb.
- Pro předplatná EA musí být na [portálu EA](https://ea.azure.com/)povolená možnost **Přidat rezervované instance** . Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro příslušné předplatné.
- Pro program poskytovatele Cloud Solution Provider (CSP) mohou koupit rezervace pouze agenti správce nebo prodejní agenti.

Postup zakoupení instance:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Všechny služby** > **Rezervace**.
3. Vyberte **Přidat** a Zakupte novou rezervaci a pak klikněte na **instance**.
4. Vyplňte požadovaná pole. Se spuštěnými rezervovanými instancemi Premium v3, které odpovídají vybraným atributům nárok na slevu rezervace. Skutečný počet rezervovaných instancí Premium v3, které získají slevu, závisí na zvoleném rozsahu a množství.

Pokud máte smlouvu EA, můžete k rychlému přidání dalších instancí použít **možnost přidat další** . Možnost není k dispozici pro jiné typy předplatného.

| **Pole** | **Popis** |
|---|---|
| Předplatné | Předplatné použité pro platbu za rezervaci. Náklady na kapacitu se účtují pomocí způsobu platby zvoleného pro toto předplatné. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P), zákaznická smlouva Microsoftu nebo jednotlivá předplatná s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se strhávají z peněžního zůstatku, pokud je k dispozici, nebo se účtují jako nadlimitní využití. U předplatného s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v rámci předplatného. |
| Obor | Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: <ul><li>Rozsah **Jedna skupina prostředků** – sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků. </li><li>Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.</li><li>Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul> |
| Oblast | Oblast Azure, která je pokrytá rezervací. |
| Velikost rezervované instance úrovně Premium V3 | Velikost rezervovaných instancí Premium v3. |
| Optimalizovat pro | Ve výchozím nastavení je vybraná flexibilita velikosti vyhrazené instance Premium v3. Klikněte na **Upřesnit nastavení** a změňte hodnotu flexibilita velikosti instance tak, aby se tato sleva mohla použít u ostatních rezervovaných instancí Premium V3 ve stejné [skupině velikostí rezervované instance Premium V3](../../virtual-machines/reserved-vm-instance-size-flexibility.md). Při použití priority kapacity se pro vaše nasazení upřednostňuje kapacita datového centra. Nabízí další důvěru ve vaší schopnosti spouštět rezervované instance Premium v3, když je potřebujete. Priorita kapacity je dostupná jenom v případě, že obor rezervací je jedno předplatné. |
| Označení | Jeden rok nebo tři roky. K dispozici je také 5 let termínu pro rezervované instance HBv2 Premium v3. |
| Množství | Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných rezervovaných instancí Premium v3, které mohou získat fakturační slevu. Pokud například v Východní USA spustíte 10 standardních \_ instancí D2 Premium v3, pak zadáte množství 10. tím se maximalizuje výhod všech spuštěných rezervovaných instancí Premium v3. |

## <a name="save-with-isolated-stamp-fees"></a>Uložit s oddělenými poplatky za razítko

Když potvrdíte rezervaci pro využití kolku na dobu tří let, můžete ušetřit na poplatcích za kolek služby Azure App Service v izolovaném prostředí. Pokud si chcete koupit rezervovanou kapacitu s poplatky za kolek služby Azure App Service v izolovaném prostředí, musíte zvolit oblast Azure, ve které se kolek nasadí, a počet kolků k zakoupení.

Když zakoupíte rezervaci, využití poplatků za kolek v izolovaném prostředí odpovídající atributům rezervace se už nebude účtovat podle sazeb pro průběžné platby. Rezervace se automaticky použije na počet izolovaných kolků, který odpovídá rozsahu a oblasti rezervované kapacity. Rezervaci nemusíte přiřazovat k izolovanému kolku. Rezervace se nevztahuje na pracovní procesy, takže všechny další prostředky přidružené ke kolku se účtují samostatně.

Když rezervovaná kapacita vyprší, izolované kolky poběží dál, ale budou se fakturovat podle sazeb pro průběžné platby. Rezervace se neprodlužují automaticky.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Určení správné rezervace izolovaného razítka k nákupu

Když si koupíte rezervaci, potvrzujete, že další tři roky budete používat rezervovaná množství. Kontrolou dat o využití zjistíte, kolik izolovaných kolků služby App Service konzistentně používáte a můžete v budoucnu používat.

Také byste měli rozumět tomu, jak izolovaný kolek vytváří měřič pro Linux nebo Windows.

- Prázdný izolovaný kolek standardně vytváří měřič kolku pro Windows. Například bez nasazení pracovních procesů. Pokud jsou na kolku nasazené pracovní procesy pro Windows, vytváří kolek dál tento měřič.
- Pokud nasadíte pracovní proces pro Linux, změní se měřič na měřič kolku pro Linux.
- V případech, kdy jsou nasazené pracovní procesy pro Linux i Windows, vytváří kolek měřič pro Windows.

Měřič kolku se tak během své existence může změnit z měřiče pro Windows na měřič pro Linux.

Rezervace s kolkem pro Windows si kupte, pokud máte na kolku jeden nebo více pracovních procesů pro Windows. Rezervaci s kolkem pro Linux si kupte jenom v případě, že se chystáte používat na kolku _výhradně_ pracovní procesy pro Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Zakoupení rezervované kapacity izolovaného kolku

Rezervovanou kapacitu izolovaného kolku si můžete koupit na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](./prepare-buy-reservation.md). Pokud si chcete koupit rezervovanou kapacitu, musíte mít roli vlastníka alespoň u jednoho předplatného se smlouvou Enterprise nebo u individuálního předplatného se sazbami pro průběžné platby.

- U předplatných se smlouvou Enterprise musí být na portálu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Pokud je toto nastavení zakázané, musíte být správcem EA.
- U programu CSP (Cloud Solution Provider) můžou rezervovanou kapacitu služby Azure Synapse Analytics zakoupit jenom agenti správy nebo agenti prodeje.

**Postup při nákupu:**

1. Přejděte na web [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Vyberte předplatné. K výběru předplatného, které se použije k placení rezervované kapacity, použijte seznam **Předplatné**. Náklady na rezervovanou kapacitu se účtují pomocí způsobu platby zvoleného pro předplatné. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo předplatné s průběžnými platbami (číslo nabídky: MS-AZR-0003P nebo MS-AZR-0023P) nebo předplatné CSP.
    - V případě předplatného se smlouvou Enterprise se poplatky strhávají ze zůstatku Azure Prepaymentu (dřív označovaný jako peněžní závazek) v rámci dané registrace nebo se účtují jako nadlimitní využití.
    - V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.
1. Vyberte **Rozsah** a zvolte rozsah předplatného.
    - Rozsah **Jedna skupina prostředků** – sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
    - Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
    - Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace prováděcí smlouva. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.
1. Vyberte **Oblast** a zvolte oblast Azure, která je pokrytá rezervovanou kapacitou. Pak přidejte rezervaci do košíku.
1. Vyberte typ Isolated Plan a pak klikněte na **Vybrat**.  
    ![Příklad ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Zadejte množství izolovaných kolků služby App Service, které chcete rezervovat. Například množství 3 vám poskytne tři rezervované kolky na oblast. Klikněte na **Další: Zkontrolovat + koupit**.
1. Zkontrolujte a klikněte na **Koupit**.

Po nákupu můžete přejít na stránku [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade), na které můžete kdykoli zobrazit a monitorovat stav nákupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Jak se uplatnění slevy zobrazuje v datech o využití

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. V datech o využití se zobrazuje sleva za rezervaci pro každou instanci kolku v každé rezervaci.

Pokud jste zákazníkem se smlouvou Enterprise, najdete další informace o tom, jak se v datech o využití zobrazuje sleva za rezervaci, v tématu [jak zákazníci se smlouvou Enterprise získají informace o nákladech na rezervace a data o využití](understand-reserved-instance-usage-ea.md). Jinak se podívejte na téma [s vysvětlením využití rezervací Azure u individuálního předplatného se sazbami pro průběžné platby](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
  - [Vysvětlení způsobu uplatňování slev za rezervaci na izolované kolky služby Azure App Service](reservation-discount-app-service.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)