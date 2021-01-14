---
title: Použití virtuálních počítačů Azure na místě
description: Naučte se používat virtuální počítače Azure na místě k úsporám za cenu.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 1e3934a8ff91d764a5148b3d490b44f30983a284
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202126"
---
# <a name="use-spot-vms-in-azure"></a>Použití bodových virtuálních počítačů v Azure

Použití přímých virtuálních počítačů vám umožní využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Množství dostupné kapacity se může lišit v závislosti na velikosti, oblasti, denní době a dalších. Při nasazování virtuálních počítačů na místě Azure přidělí virtuální počítače, pokud je k dispozici dostatek kapacity, ale pro tyto virtuální počítače neexistuje žádná smlouva SLA. Virtuální počítač s přímým dostupností nenabízí žádné záruky vysoké dostupnosti. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure na místě virtuální počítače s oznámením po dobu 30 sekund. 


## <a name="eviction-policy"></a>Zásady vyřazení

Virtuální počítače je možné vyřadit na základě kapacity nebo maximální ceny, kterou jste nastavili. Při vytváření virtuálního počítače s přímým použitím můžete nastavit zásady vyřazení, aby se nastavilo zrušení *přidělení* (výchozí) nebo *odstranění*. 

Zásada zrušení *přidělení* přesune váš virtuální počítač do stavu Zastaveno (přidělení zrušeno), což vám umožní později ho znovu nasadit. Neexistuje však záruka, že přidělení bude úspěšné. Navrácené virtuální počítače se budou počítat s vaší kvótou a za základní disky se vám budou účtovat náklady na úložiště. 

Pokud chcete, aby se virtuální počítač po vyřazení odstranil, můžete nastavit zásadu vyřazení, která se má *Odstranit*. Vyřazení virtuálních počítačů se odstraní společně s jejich podkladovým diskům, takže se za úložiště nebudete nadále účtovat. 

Můžete se přihlásit k odběru oznámení na virtuálním počítači prostřednictvím [Azure Scheduled Events](./linux/scheduled-events.md). To vám upozorní na to, jestli se virtuální počítače vyloučí a že budete mít 30 sekund na dokončení všech úloh a před vyřazením provést úlohy vypnutí. 


| Možnost | Výsledek |
|--------|---------|
| Maximální cena je nastavená na >= aktuální cena. | Virtuální počítač se nasadí, pokud je dostupná kapacita a kvóta. |
| Maximální cena je nastavená na < aktuální cena. | Virtuální počítač není nasazený. Zobrazí se chybová zpráva, že maximální cena musí být >= aktuální cena. |
| Restartováním virtuálního počítače zastavte zastavit nebo zrušit přidělení, pokud je maximální cena >= aktuální cena. | Pokud máte kapacitu a kvótu, virtuální počítač se nasadí. |
| Restartováním virtuálního počítače zastavte zastavit nebo zrušit přidělení, pokud je maximální cena < aktuální cena. | Zobrazí se chybová zpráva, že maximální cena musí být >= aktuální cena. | 
| Cena za virtuální počítač se zálohuje a teď je > maximální cenou. | Virtuální počítač se vyřadí. Dostanete oznámení 30 s před skutečnou vyřazením. | 
| Po vyřazení se cena za virtuální počítač vrátí zpět, aby se < maximální cena. | Virtuální počítač se znovu automaticky nespustí. Virtuální počítač můžete restartovat sami a bude se vám účtovat aktuální cena. |
| Pokud je maximální cena nastavená na `-1` | Virtuální počítač nebude z důvodu cen vyvyřazen. Maximální cena bude aktuální cena až do ceny za standardní virtuální počítače. Nebudete se vám nikdy účtovat za standardní cenu.| 
| Změna maximální ceny | Pokud chcete změnit maximální cenu, musíte zrušit přidělení virtuálního počítače. Zrušte přidělení virtuálního počítače, nastavte novou maximální cenu a pak aktualizujte virtuální počítač. |


## <a name="limitations"></a>Omezení

Následující velikosti virtuálních počítačů nejsou podporované pro virtuální počítače s přímým odkazem:
 - Řady B-Series
 - Propagační verze libovolné velikosti (například Dv2, NV, NC, H – propagační velikosti)

Virtuální počítače s přímým použitím se dají nasadit do jakékoli oblasti, kromě Microsoft Azure Čína 21Vianet.

<a name="channel"></a>

V současné době jsou podporovány následující [typy nabídek](https://azure.microsoft.com/support/legal/offer-details/) :

-   Smlouva Enterprise
-   Průběžné platby
-   Financovan
- Pro poskytovatele cloudových služeb (CSP) se obraťte na svého partnera.


## <a name="pricing"></a>Ceny

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Můžete také zadat dotaz na informace o cenách pomocí [rozhraní API maloobchodních cen Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) a dotazovat se na informace o cenách s cenami. `meterName`A `skuName` bude obsahovat oba `Spot` .

S proměnnými cenami máte možnost nastavit maximální cenu v USD (USD), která používá až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.

## <a name="pricing-and-eviction-history"></a>Historie cen a vyřazení

V oblasti portálu můžete zobrazit historické ceny a sazby vyřazení podle velikosti. Vyberte **Zobrazit historii cen a porovnat ceny v přilehlých oblastech** , abyste viděli tabulku nebo graf cen pro konkrétní velikost.  Ceny a sazby vyřazení na následujících obrázcích jsou pouze příklady. 

**Graf**:

:::image type="content" source="./media/spot-chart.png" alt-text="Snímek obrazovky s možnostmi oblastí s rozdílem v cenách a tarify vyřazení jako graf":::

**Tabulka**:

:::image type="content" source="./media/spot-table.png" alt-text="Snímek obrazovky s možnostmi oblastí s rozdílem v cenách a tarify vyřazení jako tabulka":::



##  <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Po vytvoření je virtuální počítač na místě stejný jako běžný standardní virtuální počítač?

**A:** Ano, s výjimkou smlouvy SLA pro virtuální počítače na místě a jejich vyřazení z provozu kdykoli se dá provést.


**Otázka:** Co dělat při vyřazení, ale stále potřebují kapacitu?

**A:** Pokud potřebujete kapacitu hned, doporučujeme použít virtuální počítače místo přímých virtuálních počítačů.


**Otázka:** Jak se Správa kvót spravuje pro virtuální počítače na místě?

**A:** Virtuální počítače s přímým chodem budou mít samostatný fond kvót. Kvóta na místě se bude sdílet mezi virtuálními počítači a instancemi sady škálování. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).


**Otázka:** Můžu požádat o další kvótu na místě?

**A:** Ano, žádost budete moci odeslat, abyste zvýšili kvótu pro virtuální počítače pomocí [procesu žádosti o standardní kvótu](../azure-portal/supportability/per-vm-quota-requests.md).


**Otázka:** Kde můžu publikovat otázky?

**A:** Svůj dotaz můžete odeslat a označit `azure-spot` na adrese [Q&A](/answers/topics/azure-spot.html). 


**Otázka:** Jak můžu změnit maximální cenu pro virtuální počítač s přímým použitím?

**A:** Než budete moct změnit maximální cenu, musíte virtuální počítač navrátit. Pak můžete na portálu změnit maximální cenu z **konfiguračního** oddílu pro daný virtuální počítač. 

## <a name="next-steps"></a>Další kroky
K nasazení virtuálních počítačů na místě použijte rozhraní příkazového [řádku](./linux/spot-cli.md), [portálu](spot-portal.md), [ARM](./linux/spot-template.md)nebo [PowerShell](./windows/spot-powershell.md) .

Můžete také nasadit [sadu škálování s instancemi virtuálních počítačů na místě](../virtual-machine-scale-sets/use-spot.md).

Pokud dojde k chybě, přečtěte si [kódy chyb](./error-codes-spot.md).
