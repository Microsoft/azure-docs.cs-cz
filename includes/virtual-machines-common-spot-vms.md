---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7cfa6e9810057493cc3007eec7fd1668a70c727e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179068"
---
Použití přímých virtuálních počítačů vám umožní využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Množství dostupné kapacity se může lišit v závislosti na velikosti, oblasti, denní době a dalších. Při nasazování virtuálních počítačů na místě Azure přidělí virtuální počítače, pokud je k dispozici dostatek kapacity, ale pro tyto virtuální počítače neexistuje žádná smlouva SLA. Virtuální počítač s přímým dostupností nenabízí žádné záruky vysoké dostupnosti. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure na místě virtuální počítače s oznámením po dobu 30 sekund. 

> [!IMPORTANT]
> Instance přímých instancí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="eviction-policy"></a>Zásada vyřazení

Virtuální počítače je možné vyřadit na základě kapacity nebo maximální ceny, kterou jste nastavili. U virtuálních počítačů je zásada vyřazení nastavená tak, aby se přesunuly vaše vyřazené virtuální počítače do stavu Zastaveno (přidělení zrušeno) *, což vám* umožní znovu nasadit vyřazené virtuální počítače později. Změna přidělení virtuálních počítačů na místě bude ale závislá na dostupnosti dostupné kapacity. Navrácené virtuální počítače se budou počítat s vaší kvótou vCPU a bude vám účtováno za vaše základní disky. 

Uživatelé se můžou přihlásit k přijímání oznámení v rámci virtuálního počítače prostřednictvím [Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). To vám upozorní na to, jestli se virtuální počítače vyloučí a že budete mít 30 sekund na dokončení všech úloh a před vyřazením provést úlohy vypnutí. 


| Možnost | Výsledek |
|--------|---------|
| Maximální cena je nastavená na > = aktuální cena. | Virtuální počítač se nasadí, pokud je dostupná kapacita a kvóta. |
| Maximální cena je nastavená na < aktuální cena. | Virtuální počítač není nasazený. Zobrazí se chybová zpráva, že maximální cena musí být > = aktuální cena. |
| Restartováním virtuálního počítače zastavte zastavit nebo zrušit přidělení, pokud je maximální cena > = aktuální cena. | Pokud máte kapacitu a kvótu, virtuální počítač se nasadí. |
| Restartováním virtuálního počítače zastavte zastavit nebo zrušit přidělení, pokud je maximální cena < aktuální cena. | Zobrazí se chybová zpráva, že maximální cena musí být > = aktuální cena. | 
| Cena za virtuální počítač se zálohuje a teď je > maximální cenou. | Virtuální počítač se vyřadí. Dostanete oznámení 30 s před skutečnou vyřazením. | 
| Po vyřazení se cena za virtuální počítač vrátí zpět, aby se < maximální cena. | Virtuální počítač se znovu automaticky nespustí. Virtuální počítač můžete restartovat sami a bude se vám účtovat aktuální cena. |
| Pokud je maximální cena nastavená na `-1` | Virtuální počítač nebude z důvodu cen vyvyřazen. Maximální cena bude aktuální cena až do ceny za standardní virtuální počítače. Nebudete se vám nikdy účtovat za standardní cenu.| 
| Změna maximální ceny | Pokud chcete změnit maximální cenu, musíte zrušit přidělení virtuálního počítače. Zrušte přidělení virtuálního počítače, nastavte t novou maximální cenu a pak aktualizujte virtuální počítač. |

## <a name="limitations"></a>Omezení

Následující velikosti virtuálních počítačů nejsou podporované pro virtuální počítače s přímým odkazem:
 - Řady B-Series
 - Propagační verze libovolné velikosti (například Dv2, NV, NC, H – propagační velikosti)

Virtuální počítače na místě nemůžou používat dočasné disky s operačním systémem.

Virtuální počítače s přímým použitím se dají nasadit do jakékoli oblasti, kromě Microsoft Azure Čína 21Vianet.

## <a name="pricing"></a>Ceny

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


S proměnnými cenami máte možnost nastavit maximální cenu v USD (USD), která používá až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu, která se má `-1`, virtuální počítač se nevyřadí na základě ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.


##  <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Po vytvoření je virtuální počítač na místě stejný jako běžný standardní virtuální počítač?

**A:** Ano, s výjimkou smlouvy SLA pro virtuální počítače na místě a jejich vyřazení z provozu kdykoli se dá provést.


**Otázka:** Co dělat při vyřazení, ale stále potřebují kapacitu?

**A:** Pokud potřebujete kapacitu hned, doporučujeme použít virtuální počítače místo přímých virtuálních počítačů.


**Otázka:** Jak se Správa kvót spravuje pro virtuální počítače na místě?

**A:** Virtuální počítače s přímým chodem budou mít samostatný fond kvót. Kvóta na místě se bude sdílet mezi virtuálními počítači a instancemi sady škálování. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Otázka:** Můžu požádat o další kvótu na místě?

**A:** Ano, žádost budete moci odeslat, abyste zvýšili kvótu pro virtuální počítače pomocí [procesu žádosti o standardní kvótu](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**Otázka:** Jaké kanály podporují přímé virtuální počítače?

**A:** V následující tabulce najdete informace o dostupnosti virtuálních počítačů.

<a name="channel"></a>

| Kanály Azure               | Dostupnost virtuálních počítačů Azure       |
|------------------------------|-----------------------------------|
| Smlouva Enterprise         | Ano                               |
| Průběžné platby                | Ano                               |
| Poskytovatel cloudových služeb (CSP) | [Obraťte se na svého partnera.](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Výhody                     | Není k dispozici.                     |
| Financovan                    | Není k dispozici.                     |
| Bezplatná zkušební verze                   | Není k dispozici.                     |


**Otázka:** Kde můžu publikovat otázky?

**A:** Svůj dotaz můžete vystavit a označit pomocí `azure-spot` na [Q & A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



