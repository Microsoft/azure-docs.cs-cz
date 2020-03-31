---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371645"
---
Když se zavážete k instanci rezervovaného virtuálního počítače Azure, můžete ušetřit peníze. Sleva rezervace se automaticky použije na počet spuštěných virtuálních počítačů, které odpovídají oboru rezervace a atributům. Nemusíte přiřazovat rezervaci k virtuálnímu počítači, abyste získali slevy. Nákup rezervované instance zahrnuje jenom výpočetní část využití virtuálního počítače. Pro virtuální počítače se systémem Windows je měřič využití rozdělen do dvou samostatných měřičů. K dispozici je výpočetní měřič, který je stejný jako měřič Linuxu a měřič IP systému Windows. Poplatky, které se zobrazí při nákupu, jsou pouze pro výpočetní náklady. Poplatky nezahrnují náklady na software systému Windows. Další informace o nákladech na software najdete v [tématu Náklady na software, které nejsou součástí rezervovaných instancí virtuálních počítače Azure](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Určení správné velikosti virtuálního počítače před nákupem

Než si koupíte rezervaci, měli byste určit velikost virtuálního počítače, který potřebujete. Následující části vám pomůžou určit správnou velikost virtuálního počítače.

### <a name="use-reservation-recommendations"></a>Použít doporučení pro rezervaci

Můžete použít doporučení rezervace k určení rezervace, které byste měli zakoupit.

- Doporučení pro nákup a doporučené množství se zobrazí, když si na webu Azure Portal koupíte rezervovanou instanci virtuálního počítače.
- Azure Advisor poskytuje doporučení pro nákup y pro jednotlivá předplatná.  
- Rozhraní API můžete použít k získání doporučení pro nákup pro sdílený obor i obor jednoho předplatného. Další informace naleznete v [tématu Rezervovaná pole doporučení pro nákup instance pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Pro zákazníky smlouvy Enterprise (EA) a Microsoft Customer Agreement (MCA) jsou doporučení k nákupu sdílených oborů a oborů jednoho předplatného dostupná s [balíčkem obsahu Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Služby, které získávají slevy za rezervaci virtuálních počítačů

Rezervace virtuálních počítačů se můžou vztahovat na využití virtuálních počítačů z několika služeb, nejen na vaše nasazení virtuálních počítačů. To, které prostředky získávají slevy za rezervované instance, se liší v závislosti na nastavení flexibility velikosti instance.

#### <a name="instance-size-flexibility-setting"></a>Nastavení flexibility velikosti instance

Nastavení flexibility velikosti instance určuje, které služby mají získat slevy za rezervované instance.

Když má položka *ConsumedService* hodnotu `Microsoft.Compute`, slevy za rezervace se automaticky uplatňují na veškeré využití vyhovujících virtuálních počítačů bez ohledu na to, jestli je nastavení zapnuté, nebo vypnuté. Proto si u hodnoty *ConsumedService* kontrolujte údaje o využití. Možné příklady:

- Virtual Machines
- Škálovací sady virtuálních počítačů
- Služba kontejneru
- Nasazení služby Azure Batch (v režimu předplatných uživatelů)
- Azure Kubernetes Service (AKS)
- Service Fabric

Když je nastavení zapnuté a položka *ConsumedService* má některou z následujících hodnot, na veškeré využití vyhovujících virtuálních počítačů se automaticky uplatňují slevy za rezervace:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

V datech využití si zkontrolujte hodnotu *ConsumedService*, abyste zjistili, jestli má dané využití nárok na slevy za rezervace.

Další informace o flexibilitě velikosti instance najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analýza informací o využití

Analyzujte informace o využití a zjistěte, které rezervace byste měli zakoupit. Data o využití jsou k dispozici v souboru využití a rozhraní API. Použijte je společně k určení, kterou rezervaci koupit. Zkontrolujte instance virtuálních počítače, které mají vysoké využití na denní bázi k určení množství rezervací k nákupu. Vyhněte se `Meter` `Product` podkategorii a polím v datech o využití. Nerozlišují mezi velikostmi virtuálních zařízení, které používají prémiové úložiště. Pokud tato pole použijete k určení velikosti virtuálního počítače pro nákup rezervace, můžete si koupit nesprávnou velikost. Pak nebudete mít slevu na rezervaci, kterou očekáváte. Místo toho naleznete `AdditionalInfo` v poli v souboru využití nebo rozhraní API pro využití k určení správné velikosti virtuálního počítače.

Soubor využití zobrazuje vaše poplatky podle fakturačního období a denního využití. Informace o stahování souboru využití najdete v [tématu Zobrazení a stažení využití azure a poplatků](../articles/cost-management-billing/understand/download-azure-daily-usage.md). Potom pomocí informací o souboru použití můžete [určit, jakou rezervaci koupit](../articles/cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Aspekty omezení nákupu

Rezervované instance virtuálních počítače jsou k dispozici pro většinu velikostí virtuálních počítače s některými výjimkami. Slevy na rezervace se nevztahují na následující virtuální hospova:

- **Řada vm** - řada A, řada Av2 nebo Řada G.

- **Náhled nebo promo virtuální počítače** – všechny série nebo velikost virtuálních počítače, které jsou ve verzi Preview nebo používá propagační měřič.

- **Cloudy** – Rezervace nelze zakoupit v oblastech Německa nebo Číny.

- **Nedostatečná kvóta** – Rezervace, která je vymezena na jedno předplatné, musí mít kvótu virtuálního procesoru k dispozici v předplatném pro novou RI. Pokud má například cílové předplatné limit kvóty 10 virtuálních procesorů pro řadu D, nemůžete si zakoupit rezervaci pro 11 Standard_D1 instancí. Kontrola kvótpro rezervace zahrnuje virtuální chody, které jsou už nasazené v předplatném. Například pokud má předplatné kvótu 10 virtuálních procesorů pro D-Series a má nasazené dvě standard_D1 instance, můžete si zakoupit rezervaci pro 10 standard_D1 instancí v tomto předplatném. K vyřešení tohoto problému můžete [vytvořit žádost o zvýšení nabídky.](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)

- **Omezení kapacity** – ve výjimečných případech Azure omezuje nákup nových rezervací pro podmnožinu velikostí virtuálních počítačů z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reserved-vm-instance"></a>Nákup rezervované instance virtuálního počítače

Rezervovaný virtuální počítač si můžete koupit na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).
Tyto požadavky platí pro nákup instance rezervovaného virtuálního soudu:

- Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s průběžnou sazbou.
- U předplatných EA musí být na [portálu EA](https://ea.azure.com/)povolena možnost **Přidat rezervované instance** . Pokud je toto nastavení zakázané, musíte být správcem smlouvy Enterprise pro příslušné předplatné.
- V programu Zprostředkovatel cloudových řešení (CSP) mohou rezervace zakoupit pouze správci nebo prodejci.

Zakoupení instance:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **všechny služby** > **Rezervace**.
1. Chcete-li zakoupit novou **rezervaci,** vyberte Přidat a klepněte na položku **Virtuální počítač**.
1. Vyplňte požadovaná pole. Na získání slevy za rezervace mají nárok spuštěné instance virtuálních počítačů, které odpovídají vybraným atributům. Skutečný počet instancí virtuálních počítačů, které slevu získají, závisí na vybraném rozsahu a množství.

Pokud máte smlouvu eA, můžete pomocí **možnosti Přidat další** rychle přidat další instance. Tato možnost není k dispozici pro jiné typy předplatného.


| Pole      | Popis|
|------------|--------------|
|Předplatné|Předplatné slouží k zaplacení rezervace. Za způsob platby v rámci předplatného se účtují náklady na rezervaci. Typ předplatného musí být smlouva enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo Smlouva se zákazníkem společnosti Microsoft nebo individuální předplatné s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Poplatky se odečtou ze zůstatku peněžního závazku, pokud jsou k dispozici, nebo jsou účtovány jako nadbytečné. U předplatného s průběžnými platbami se poplatky účtují na základě platební karty nebo způsobu platby faktury v rámci předplatného.|    
|Rozsah       |Obor rezervace může zahrnovat jedno předplatné nebo více předplatných (sdílený obor). Pokud vyberete: <ul><li>**Rozsah na jednu skupinu prostředků** – Sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.</li><li>Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.</li><li>Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. Pro zákazníky EA je fakturační kontext registrace. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul>|
|Region (Oblast)    |Oblast Azure, která je pokryta rezervace.|    
|Velikost virtuálního počítače     |Velikost instancí virtuálního počítače.|
|Optimalizovat pro     |Flexibilita velikosti instance virtuálního počítače je vybrána ve výchozím nastavení. Kliknutím na **Upřesnit nastavení** změňte hodnotu flexibility velikosti instance a použijte slevu na rezervaci u ostatních virtuálních počítače ve stejné [skupině velikostí virtuálních počítače](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Při použití priority kapacity se pro vaše nasazení upřednostňuje kapacita datového centra. Nabízí další důvěru ve vaši schopnost spouštět instance virtuálních počítače, když je potřebujete. Priorita kapacity je k dispozici pouze v případě, že obor rezervace je jedno předplatné. |
|Označení        |Jeden rok nebo tři roky.|
|Množství    |Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných instancí virtuálních účtů, které můžou získat fakturační slevu. Pokud například používáte 10 Standard_D2 virtuálních virtuálních měsících v USA – východ, zadejte množství jako 10, abyste maximalizovali přínos pro všechny spuštěné virtuální hody. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Údaje o využití a využití rezervací

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. Můžete zjistit, která instance virtuálního virtuálního soudu obdržela slevu na rezervaci pro každou rezervaci.

Další informace o tom, jak se slevy na rezervace zobrazují v datech o využití, najdete v [tématu Principy využití rezervací Azure pro registraci enterprise,](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) pokud jste zákazníkem EA. Pokud máte individuální předplatné, [přečtěte si informace o využití rezervací Azure pro vaše předplatné s průběžným platbou](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po zakoupení

V zakoupené rezervaci můžete provádět následující typy změn:

- Aktualizace rozsahu rezervace
- Flexibilita velikosti instance (pokud je k dispozici)
- Vlastnictví

Můžete také rozdělit rezervaci na menší bloky a sloučit již rozdělené rezervace. Žádná ze změn nezpůsobí novou obchodní transakci ani nezmění koncové datum rezervace.

Po zakoupení nelze provést následující typy změn:

- Oblast existující rezervace
- Skladová jednotka (SKU)
- Množství
- Doba trvání

Pokud však chcete provést změny, můžete si rezervaci *vyměnit.*

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervace, najdete v tématu [Správa rezervací Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Co jsou rezervace Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Vysvětlení způsobu uplatnění slevy za rezervaci](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervací u smlouvy Enterprise](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Náklady na software systému Windows nejsou zahrnuty v rezervacích](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
