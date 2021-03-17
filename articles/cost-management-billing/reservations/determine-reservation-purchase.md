---
title: Určení, kterou rezervaci Azure byste měli koupit
description: Tento článek vám pomůže určit, kterou rezervaci byste měli koupit.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 08/04/2020
ms.author: banders
ms.openlocfilehash: d89f890d3e2bb8238a00b4a529b8804a8c38fa6f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684741"
---
# <a name="determine-what-reservation-to-purchase"></a>Určení rezervace k zakoupení

Všechny rezervace s výjimkou Azure Databricks se využívají na hodinovém základu. Rezervace byste měli koupit na základě konzistentního základního využití. Existuje několik způsobů určení, co koupit, a tento článek vám pomůže určit, kterou rezervaci byste měli koupit.

Nákup větší kapacity, než odpovídá vašemu historickému využití, vede k nedostatečně využité rezervaci. Pokud je to možné, měli byste se nedostatečnému využití vyhýbat. Nevyužitá rezervovaná kapacita se nepřenáší z jedné hodiny do druhé. Využití přesahující rezervované množství se účtuje s využitím dražších tarifů průběžných plateb.

## <a name="analyze-usage-data"></a>Analýz dat o využití

Následující části vám pomohou analyzovat údaje o denním využití a určit vaše základní využití a také to, která rezervace se má koupit.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analýza využití pro nákup rezervované instance virtuálního počítače

Zjistěte si vhodnou velikost virtuálního počítače, kterou je potřeba koupit. Například rezervace zakoupená pro virtuální počítače řady ES se nevztahuje na virtuální počítače řady E a naopak.

Virtuální počítače propagačních řad nezískávají slevu za rezervaci, proto je z vaší analýzy odeberte.

Pokud chcete upřesnit odpovídající využití virtuálních počítačů, na data o využití použijte následující filtry:

- Nastavte **MeterCategory** na **Virtual Machines**.
- Získejte informace **ServiceType** z pole **AdditionalInfo**. Tyto informace navrhují vhodnou velikost virtuálního počítače. Například Standard E32.
- K určení datacentra využití použijte pole **ResourceLocation**.

Ignorujte prostředky, které mají méně než 24 hodin využití za den.

Pokud chcete provést analýzu na úrovni skupiny velikostí instance, můžete získat hodnoty flexibilní velikosti instance tady: [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Zkombinujte tyto hodnoty s vašimi daty a proveďte analýzu. Další informace o flexibilitě velikosti instance najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Analýza využití pro nákup rezervované instance služby Azure Synapse Analytics

Rezervovaná kapacita se vztahuje na ceny DWU služby Azure Synapse Analytics. Nevztahuje se na náklady na licence Azure Synapse Analytics ani na jiné náklady než výpočetní funkce.

Pokud chcete upřesnit odpovídající využití, na údaje o využití použijte následující filtry:


- Jako **MeterCategory** nastavte **SQL Database**.
- Jako **MeterName** nastavte **vCore**.
- Pro všechny záznamy o využití, které mají v názvu _Compute_, nastavte filtr **MeterSubCategory**.

V části **AdditionalInfo** zjistěte hodnotu **vCores**. Udává, kolik virtuálních jader se použilo. Množství zjistíte vynásobením hodnoty **vCores** počtem hodin, kdy se databáze používala.

Tato data informují o konzistentním využití pro:

- Kombinace typů databáze. Například spravovaná instance nebo elastický fond pro jednu databázi.
- Úroveň služeb. Například pro obecné účely nebo pro důležité obchodní informace.
- Generace. Například Gen 5.
- Umístění prostředku

### <a name="analysis-for-azure-synapse-analytics"></a>Analýza pro Azure Synapse Analytics

Rezervovaná kapacita se vztahuje na využití DWU služby Azure Synapse Analytics a dá se koupit v přírůstcích po 100 DWU. Pokud chcete upřesnit odpovídající využití, na údaje o využití použijte následující filtry:

- Jako **MeterName** nastavte **100 DWU**.
- Jako **podkategorii měřiče** nastavte **Optimalizováno pro výpočty Gen2**.

K určení využití pro Azure Synapse Analytics v oblasti použijte pole **Umístění prostředku**.

Využití služby Azure Synapse Analytics se může během dne vertikálně navyšovat a snižovat. Promluvte si s týmem, který instanci Azure Synapse Analytics spravuje, abyste se dozvěděli víc o základním využití.

Přejděte k rezervacím na webu Azure a kupte si rezervovanou kapacitu služby Azure Synapse Analytics (v násobcích po 100 DWU).

## <a name="reservation-purchase-recommendations"></a>Doporučení k nákupu rezervací

Doporučení k nákupu rezervací se vypočítávají analýzou hodinových dat využití za posledních 7, 30 a 60 dnů. Azure vypočte, jaké náklady byste měli, pokud byste měli rezervaci, a porovná je s vašimi skutečnými náklady na průběžné platby, které se za příslušnou dobu naúčtovaly. Výpočet se provádí pro každé množství, které jste během daného časového rámce použili. Doporučí se množství, které zajišťuje maximální úspory.

Představte si například, že ve většině případů využíváte 500 virtuálních počítačů, ale v některých případech využití naroste až na 700 virtuálních počítačů. V tomto příkladu Azure vypočítá úspory pro 500 i 700 virtuálních počítačů. Vzhledem k tomu, že využití 700 virtuálních počítačů je jenom občasné, při výpočtu doporučení se určí, že se úspory maximalizují při nákupu rezervace 500 virtuálních počítačů, a poskytne se doporučení pro množství rovné 500.

Je třeba počítat s následujícím:

- Doporučení rezervací se počítají podle sazeb využití na vyžádání, které se na vás vztahují.
- Doporučení se počítají pro jednotlivé velikosti, nikoli pro skupinu velikostí instancí.
- Doporučené množství pro rozsah se snižuje ve stejný den, kdy pro tento rozsah zakoupíte rezervace.
    - Aktualizace doporučení množství rezervací napříč různými rozsahy ale může trvat až 25 dnů. Pokud například nakupujete na základě doporučení pro sdílený rozsah, může úprava doporučení pro rozsah jednotlivých předplatných trvat až 25 dní.

## <a name="recommendations-in-the-azure-portal"></a>Doporučení na webu Azure Portal

Nákupy rezervací vypočítané modulem pro doporučení se zobrazují na kartě **Doporučené** na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Tady je ukázkový obrázek.

![Obrázek ukazující doporučení](./media/determine-reservation-purchase/select-product-ri.png)

Další informace o [doporučeních](reserved-instance-purchase-recommendations.md#recommendations-in-the-azure-portal)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Doporučení v aplikaci Power BI Cost Management

Zákazníci, kteří mají smlouvu Enterprise nebo Smlouvu se zákazníkem Microsoftu, mohou pro doporučení pro nákup a virtuální počítače využívat sestavy pokrytí virtuálních počítačů pomocí RI. V sestavách pokrytí se uvádí celkové využití a využití, která je pokryté rezervovanými instancemi.

1. Získejte [aplikaci Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Přejděte k sestavě pokrytí virtuálních počítačů pomocí RI pro rozsah na jedno předplatné nebo sdílený rozsah, v závislosti na rozsahu, pro který chcete nakupovat.
3. Vyberte oblast a skupinu velikostí instancí. Pro zvolený filtr se zobrazí využití, pokrytí pomocí RI a doporučení pro nákup.

## <a name="recommendations-in-azure-advisor"></a>Doporučení od služby Azure Advisor

Doporučení pro nákup rezervací pro virtuální počítače jsou k dispozici v [Azure Advisoru](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- Advisor poskytuje doporučení jenom v rámci jednotlivých předplatných.
- Doporučení Advisoru se počítají shrnutí za 30 dnů zpětně. Předpokládané úspory jsou pro období rezervace na 3 roky.
- Pokud si koupíte rezervaci se sdíleným rozsahem, doporučení pro nákup rezervací Advisoru může trvat až 30 dnů, než zmizí.

## <a name="recommendations-using-apis"></a>Doporučení s využitím rozhraní API

Rozhraní REST API pro [doporučení pro rezervace](/rest/api/consumption/reservationrecommendations/list) můžete využít k zobrazení doporučení prostřednictvím kódu programu.

## <a name="next-steps"></a>Další kroky

- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)