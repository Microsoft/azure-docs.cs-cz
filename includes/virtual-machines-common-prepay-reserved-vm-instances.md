---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/19/2019
ms.openlocfilehash: 763d424d9d462c4a9531df84f3e5e26bfc1b0a14
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68502274"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Platba za Virtual Machines s využitím Azure Reserved VM Instances (RI)

Můžete zaplatit za virtuální počítače předem a ušetřit peníze pomocí instancí virtuálních počítačů Azure rezervovaných pro Azure. Sleva rezervace se automaticky použije na počet spuštěných virtuálních počítačů, které odpovídají oboru a atributům rezervace. K získání slevy není nutné přiřadit rezervaci k virtuálnímu počítači. Zakoupení rezervované instance pokrývá jenom výpočetní část využití virtuálního počítače. V případě virtuálních počítačů s Windows je měřič využití rozdělen do dvou samostatných měřičů. K dispozici je výpočetní měřič, který je stejný jako měřič pro Linux, a měřič IP adres systému Windows. Poplatky, které se zobrazí při nákupu, budou platit jenom za výpočetní náklady. Poplatky nezahrnují náklady na software systému Windows. Další informace o nákladech na software najdete v části [náklady na software nejsou součástí Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Určení správné velikosti virtuálních počítačů před nákupem

Před nákupem rezervace byste měli určit velikost virtuálního počítače, který potřebujete. Následující části vám pomůžou určit správnou velikost virtuálního počítače.

### <a name="use-reservation-recommendations"></a>Použití doporučení rezervací

K určení rezervací, které byste měli koupit, můžete použít doporučení rezervací.

- Doporučení nákupu a doporučené množství se zobrazí při nákupu rezervované instance virtuálního počítače v Azure Portal.
- Azure Advisor poskytuje doporučení pro nákup pro jednotlivá předplatná.  
- Rozhraní API můžete použít k získání doporučení pro nákup jak pro sdílený obor, tak pro obor jednoho předplatného. Další informace najdete v tématu [rozhraní API pro doporučení pro nákup rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Zákazníkům smlouva Enterprise (EA) jsou k dispozici zákaznická doporučení pro rozsahy sdílených a jednoduchého předplatného v [balíčku obsahu Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Služby, které získávají slevy na rezervaci virtuálních počítačů

Rezervace virtuálních počítačů se můžou vztahovat na využití virtuálních počítačů vysílaná z několika služeb – ne jenom pro nasazení virtuálních počítačů. Prostředky, které získávají slevy na rezervované instance, se mění v závislosti na nastavení flexibility velikosti instance.

#### <a name="instance-size-flexibility-setting"></a>Nastavení flexibility velikosti instance

Nastavení flexibility velikosti instance Určuje, které služby získají slevy za rezervované instance.

Bez ohledu na to, jestli je nastavení zapnuté nebo vypnuté, se slevy za rezervované automaticky  vztahují na `Microsoft.Compute`všechny vyhovující využití virtuálních počítačů, když je ConsumedService. Ověřte proto data o využití pro hodnotu *ConsumedService* . Možné příklady:

- Virtuální počítače
- Škálovací sady virtuálních počítačů
- Služba kontejneru
- Nasazení Azure Batch (v režimu předplatných uživatelů)
- Azure Kubernetes Service (AKS)
- Service Fabric

Pokud je nastavení zapnuté, slevy rezervace se automaticky použijí na vyhovující využití virtuálních počítačů, pokud je *ConsumedService* některou z následujících položek:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Zkontrolujte hodnotu *ConsumedService* v datech o využití a určete, jestli je využití opravňující pro slevy za rezervované slevy.

Další informace o flexibilitě velikosti instancí najdete v tématu [flexibilita velikosti virtuálních počítačů pomocí rezervovaných instancí virtuálních počítačů](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analýza informací o použití
Analýza informací o použití vám pomůže určit, které rezervace byste měli koupit.

Data o využití jsou k dispozici v souboru využití a v rozhraních API. Použijte je společně k určení rezervace k nákupu. Zkontrolujte, jestli jsou instance virtuálních počítačů, které mají každodenní vysoké využití, na denní úrovni a určete množství rezervací, které se mají koupit.

Vyhněte `Meter` se podkategorií `Product` a polím v datech o využití. Nerozlišují se mezi velikosti virtuálních počítačů, které používají službu Premium Storage. Pokud používáte tato pole k určení velikosti virtuálního počítače pro nákup rezervace, můžete si koupit špatnou velikost. Pak nezískáte zlevněnou rezervaci, kterou očekáváte. Místo toho použijte k určení správné velikosti virtuálního počítače polevsouboruvyužitíneborozhraníAPIvyužití.`AdditionalInfo`

### <a name="purchase-restriction-considerations"></a>Požadavky na omezení nákupu

Rezervované instance virtuálních počítačů jsou k dispozici pro většinu velikostí virtuálních počítačů s několika výjimkami. Pro následující virtuální počítače se neplatí žádné slevy za rezervované:

- **Řady virtuálních počítačů** – řady a-Series, Av2-Series nebo G-series.

- **Verze Preview nebo propagační virtuální počítače** – jakákoli řada virtuálních počítačů nebo velikost, která je ve verzi Preview nebo používá propagační měření.

- **Cloudy** – rezervace nejsou k dispozici pro nákup v oblastech Německo a Čína.

- **Nedostatečná kvóta** – rezervace, která je vymezená v rámci jednoho předplatného, musí mít v předplatném k dispozici vCPUou kvótu pro nové rezervované instance. Pokud má například cílové předplatné omezení kvóty 10 vCPU pro řady D-Series, nemůžete koupit rezervaci pro 11 instancí Standard_D1. Kontroly kvóty pro rezervace zahrnují virtuální počítače, které jsou už v předplatném nasazené. Pokud má například předplatné kvótu 10 vCPU pro řady D a má nasazené dvě instance standard_D1, můžete si v tomto předplatném koupit rezervaci pro 10 instancí standard_D1. Chcete-li vyřešit tento problém, můžete [vytvořit žádost o zvýšení poptávky](../articles/azure-supportability/resource-manager-core-quotas-request.md) .

- **Omezení kapacity** – Azure omezuje nákup nových rezervací pro podmnožinu velikostí virtuálních počítačů z důvodu nízké kapacity v oblasti.

## <a name="buy-a-reserved-vm-instance"></a>Zakoupení rezervované instance virtuálního počítače

Rezervovanou instanci virtuálního počítače můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Tyto požadavky se vztahují k zakoupení rezervované instance virtuálního počítače:

- Musíte být v roli vlastníka alespoň pro jedno předplatné EA nebo předplatné s tarifem průběžných plateb.
- Pro předplatná EA musí být na [portálu EA](https://ea.azure.com/)povolená možnost **Přidat rezervované instance** . Nebo, pokud je toto nastavení zakázané, musíte být správce EA pro předplatné.
- Pro program poskytovatele Cloud Solution Provider (CSP) mohou koupit rezervace pouze agenti správce nebo prodejní agenti.

Zakoupení instance:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny** > **rezervace**služeb.
1. Vyberte **Přidat** a Zakupte novou rezervaci a pak klikněte na **virtuální počítač**.
1. Zadejte požadovaná pole. Pokud chcete získat slevu rezervace, spusťte instance virtuálních počítačů, které odpovídají vybraným atributům. Skutečný počet instancí virtuálních počítačů, které obdrží slevu, závisí na zvoleném rozsahu a množství.

| Pole      | Popis|
|------------|--------------|
|Subscription|Předplatné použité pro platbu za rezervaci. Platební metodou předplatného se účtují náklady na front-end pro rezervaci. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo individuální předplatné s tarify průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. U předplatného s tarify průběžných plateb se poplatky účtují na základě platební karty nebo platby na faktuře v rámci předplatného.|    
|Scope       |Rozsah rezervace může zahrnovat jedno nebo víc předplatných (sdílený rozsah). Pokud vyberete: <ul><li>**Obor jedné skupiny prostředků** – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.</li><li>**Rozsah jednoho** předplatného – aplikuje slevu na rezervované prostředky ve vybraném předplatném.</li><li>**Sdílený rozsah** – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky se smlouvou EA je účetním kontextem registrace. U jednotlivých předplatných s tarify průběžných plateb jsou oborem fakturace všechna oprávněná předplatná vytvořená správcem účtu.</li></ul>|
|Oblast    |Oblast Azure, která je pokrytá rezervací.|    
|Velikost virtuálního počítače     |Velikost instancí virtuálních počítačů.|
|Optimalizovat pro     |Ve výchozím nastavení je vybraná flexibilita velikosti instancí virtuálních počítačů. Klikněte na **Upřesnit nastavení** a změňte hodnotu flexibilita velikosti instance tak, aby se na jiné virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)použili sleva rezervace. Priorita kapacity má prioritu kapacity datového centra pro vaše nasazení. Nabízí další důvěru ve vaší schopnosti spouštět instance virtuálních počítačů, když je potřebujete. Priorita kapacity je dostupná jenom v případě, že obor rezervací je jedno předplatné. |
|Termín        |Jeden rok nebo tři roky.|
|Množství    |Počet instancí zakoupených v rámci rezervace. Množství je počet spuštěných instancí virtuálních počítačů, které můžou získat fakturační slevu. Pokud třeba v Východní USA používáte 10 virtuálních počítačů s Standard_D2, pak zadáte množství 10. tím se maximalizuje výhod všech spuštěných virtuálních počítačů. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Využití a data využití rezervací

Data o využití mají platnou cenu za využití, která vrací slevu za rezervaci. Můžete zjistit, která instance virtuálního počítače obdržela slevu za rezervaci pro každou rezervaci.

Další informace o tom, jak se v datech využití zobrazují slevy, najdete v tématu [vysvětlení využívání rezervací Azure pro vaši podnikovou registraci](../articles/billing/billing-understand-reserved-instance-usage-ea.md) , pokud jste zákazníkem EA. Pokud máte individuální předplatné, přečtěte si téma [vysvětlení využití rezervace Azure pro předplatné](../articles/billing/billing-understand-reserved-instance-usage.md)s průběžnými platbami.

## <a name="change-a-reservation-after-purchase"></a>Změna rezervace po nákupu

Po nákupu můžete provést následující typy změn v rezervaci:

- Aktualizovat rozsah rezervací
- Flexibilita velikosti instance (Pokud je k dispozici)
- Jejíž

Rezervaci můžete také rozdělit do menších bloků dat a sloučení už rozdělené rezervace. Žádná z těchto změn nezpůsobí novou komerční transakci ani nemění koncové datum rezervace.

Po nákupu nemůžete provést následující typy změn přímo:

- Existující oblast rezervace
- SKU
- Množství
- Trvání

Můžete ale *vyměnit* rezervaci, pokud chcete provést změny.

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

Pokud potřebujete zrušit svou rezervaci, může se vám účtovat poplatek za předčasné ukončení ve výši 12 %. Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší. Výše vrácených peněz je omezená na 50 000 USD za rok. Výše vrácených peněz, které obdržíte, je zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Pokud ho chcete zrušit, pokračujte na rezervaci v Azure Portal a vyberte **refundace**.

Pokud potřebujete změnit rezervaci rezervovaných instancí virtuálních počítačů na jinou oblast, skupinu velikostí virtuálních počítačů nebo termín, můžete si ji vyměňovat. Výměna musí být pro jinou rezervaci, která má stejnou nebo větší hodnotu. Počáteční datum období nové rezervace se z vyměněné rezervace nepřenáší. Jeden nebo tři roky začíná při vytváření nové rezervace. Chcete-li vyměnit server Exchange, pokračujte na rezervaci v Azure Portal a vyberte možnost **Exchange**.

Další informace o výměně nebo refundaci rezervacích najdete v tématu věnovaném [výměnám a refundacím rezervací](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- Informace o tom, jak spravovat rezervaci, najdete v tématu [správa Azure reservations](../articles/billing/billing-manage-reserved-vm-instance.md).
- Další informace o Azure Reservations najdete v následujících článcích:
    - [Co jsou Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Vysvětlení způsobu použití slevy rezervace](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervací pro předplatné s tarify průběžných plateb](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Vysvětlení použití rezervací pro podnikovou registraci](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software systému Windows, které nejsou součástí rezervací](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations v programu partner Center pro Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
