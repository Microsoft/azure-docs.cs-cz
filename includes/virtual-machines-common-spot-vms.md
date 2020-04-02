---
title: zahrnout soubor
description: zahrnout soubor
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d56964b7415e4ca5903950cd46c02b3c27f62d5e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547408"
---
Použití spotových virtuálních měn vám umožní využít naši nevyužitou kapacitu s výraznými úsporami nákladů. Kdykoli v okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává virtuální počítače Spot. Virtuální počítače Spot jsou proto skvělé pro úlohy, které zvládnou přerušení, jako jsou úlohy dávkového zpracování, vývojová a testovací prostředí, velké výpočetní úlohy a další.

Množství dostupné kapacity se může lišit v závislosti na velikosti, oblasti, denní době a dalších. Při nasazování virtuálních počítačů Spot Azure přidělí virtuální počítače, pokud je k dispozici kapacita, ale pro tyto virtuální počítače neexistuje žádná sla. Virtuální virtuální ms spot nenabízí žádné záruky vysoké dostupnosti. Kdykoli v okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává virtuální počítače Spot s oznámením 30 sekund. 


## <a name="eviction-policy"></a>Politika vystěhovaní

Virtuální počítači lze vyřazovat na základě kapacity nebo maximální ceny, kterou nastavíte. Pro virtuální počítače zásady vyřazování je nastavena na *navrátit,* který přesune vaše vyřazené virtuální počítače do zastaveného stavu, což vám umožní znovu nasadit vystěhované virtuální počítače později. Přerozdělování virtuálních počítačů Spot však bude záviset na dostupné kapacitě spotu. Přidělené virtuální počítače se započítávají do kvóty virtuálních procesorů na místě a budou se vám účtovat podkladové disky. 

Uživatelé se můžou přihlásit k odběru oznámení ve virtuálním počítači prostřednictvím [naplánovaných událostí Azure](../articles/virtual-machines/linux/scheduled-events.md). To vás upozorní, pokud vaše virtuální počítače jsou vyřazovány a budete mít 30 sekund k dokončení všech úloh a provedení úloh vypnutí před vyřazení. 


| Možnost | Výsledek |
|--------|---------|
| Maximální cena je nastavena na >= aktuální cena. | Virtuální ms se nasadí, pokud jsou k dispozici kapacita a kvóta. |
| Maximální cena je nastavena na < aktuální ceny. | Virtuální virtuální měna se nenasadí. Zobrazí se chybová zpráva, že maximální cena musí být >= aktuální cena. |
| Restartování virtuálního virtuálního soudu stop/reallocate, pokud je maximální cena >= aktuální cena | Pokud je kapacita a kvóta, pak virtuální ho se nasadí. |
| Restartování virtuálního virtuálního soudu stop/reallocate, pokud je maximální cena < aktuální cenu | Zobrazí se chybová zpráva, že maximální cena musí být >= aktuální cena. | 
| Cena za virtuální ms se zvýšila a nyní > maximální cenu. | Virtuální měkce vystěhovají. Dostanete oznámení 30s před skutečným vystěhováním. | 
| Po vystěhování cena za virtuální počítač se vrátí k < maximální cenu. | Virtuální virtuální ms se automaticky nespustí. Virtuální počítač můžete restartovat sami a bude se účtovat za aktuální cenu. |
| Pokud je maximální cena nastavena na`-1` | Virtuální virtuální hotel nebude vystěhován z důvodů cen. Maximální cena bude aktuální cena až do ceny standardních virtuálních měn. Nikdy vám nebude účtována nad standardní cenu.| 
| Změna maximální ceny | Chcete-li změnit maximální cenu, musíte virtuální ho navrátit. Navrátit virtuální ho, nastavit t novou maximální cenu a pak aktualizovat virtuální ho. |

## <a name="limitations"></a>Omezení

Následující velikosti virtuálních počítače nejsou podporované pro virtuální počítače Spot:
 - Řada B
 - Promo verze libovolné velikosti (jako Dv2, NV, NC, H promo velikosti)

Virtuální počítače spotu momentálně nemůžou používat dočasné disky operačního systému.

Virtuální počítače spot lze nasadit do libovolné oblasti, s výjimkou Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Ceny

Ceny pro spotové virtuální počítače jsou variabilní na základě oblasti a skladové položky. Další informace najdete v tématu Ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


S variabilními cenami máte možnost nastavit maximální cenu v amerických dolarech (USD) s použitím až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0.98765 USD za hodinu. Pokud nastavíte maximální `-1`cenu , virtuální počítač nebude vystěhován na základě ceny. Cena za virtuální ho virtuálního mítna bude aktuální cena za spot nebo cena za standardní virtuální ms, který je vždy menší, pokud je k dispozici kapacita a kvóta.


##  <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Once created, is a Spot VM the same as regular standard VM?

**A:** Ano, kromě toho, že neexistuje žádná smlouva SLA pro virtuální virtuální chod spotů a mohou být kdykoli vystěhovány.


**Otázka:** Co dělat, když se vystěhují, ale stále potřebujete kapacitu?

**A:** Doporučujeme používat standardní virtuální chod místo virtuálních virtuálních měn spot, pokud potřebujete kapacitu hned.


**Otázka:** Jak se spravují kvóty pro virtuální chody na místě?

**A:** Virtuální virtuální chod spotů budou mít samostatný fond kvót. Kvóta na místě se bude sdílet mezi virtuálními stránkami a instancemi škálovací sady. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Otázka:** Mohu požádat o další kvótu pro Spot?

**A:** Ano, budete moci odeslat žádost o zvýšení kvóty pro virtuální počítače Spot prostřednictvím [procesu standardní žádosti o kvótu](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**Otázka:** Jaké kanály podporují spotové virtuální aplikace?

**A:** Dostupnost virtuálního virtuálního bodu najdete v tabulce níže.

<a name="channel"></a>

| Kanály Azure               | Dostupnost virtuálních počítače Azure Spot       |
|------------------------------|-----------------------------------|
| Smlouva Enterprise         | Ano                               |
| Pay As You Go                | Ano                               |
| Poskytovatel cloudových služeb (CSP) | [Kontaktujte svého partnera](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Výhody                     | Není k dispozici.                     |
| Sponzorované                    | Není k dispozici.                     |
| Bezplatná zkušební verze                   | Není k dispozici.                     |


**Otázka:** Kde mohu psát otázky?

**A:** Svůj dotaz můžete zveřejnit `azure-spot` a označit na [q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



