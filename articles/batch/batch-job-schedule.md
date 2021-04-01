---
title: Plánování úloh
description: Pomocí plánování úloh můžete spravovat úkoly.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 7da3c78e00f5d7e41a5396603cf4885a50cb6e5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89146347"
---
# <a name="schedule-jobs-for-efficiency"></a>Plánování úloh pro zajištění efektivity

Plánování dávkových úloh umožňuje určit prioritu úloh, které chcete spustit jako první, a vzít v úvahu úlohy, které mají závislosti na jiných úkolech. Když naplánujete úlohy, můžete zajistit, abyste používali aspoň množství prostředků. Uzly mohou být vyřazeny z provozu, pokud není potřeba, úkoly, které jsou závislé na jiných úkolech, jsou v průběhu optimalizace pracovních postupů k disviset přesně v čase. V jednom okamžiku běží pouze jedna úloha. Nový se nespustí, dokud se nedokončí předchozí. Můžete nastavit úlohu na automatické dokončování. 

## <a name="benefit-of-job-scheduling"></a>Výhody plánování úloh

Výhodou plánování úloh je, že můžete zadat plán pro vytvoření úlohy. Úkoly, které naplánujete pomocí úlohy Správce úloh, jsou přidružené k úloze. Úkol správce úloh vytvoří úlohy pro úlohu. K tomu je potřeba úkol správce úloh ověřit s účtem Batch. Použijte přístupový token AZ_BATCH_AUTHENTICATION_TOKEN. Token umožní přístup ke zbytku úlohy. 

## <a name="use-the-portal-to-schedule-a-job"></a>Naplánování úlohy pomocí portálu

   1. Přihlaste se k [Azure Portal](https://portal.azure.com/).

   2. Vyberte účet Batch, ve kterém chcete úlohy naplánovat.

   3. Vyberte **Přidat** a vytvořte nový plán úlohy a vyplňte **základní formulář**.



![Naplánování úlohy][1]

**ID plánu úlohy**: jedinečný identifikátor pro tento plán úlohy.

**Zobrazovaný název**: zobrazovaný název úlohy nemusí být jedinečný, ale může mít maximální délku 1024 znaků.

**Nespouštět do**: Určuje čas, kdy se úloha spustí. Pokud toto nastavení nenastavíte, plán se hned spustí, aby se spouštěly úlohy.

**Nespouštět po**: po dobu, kterou tady nastavíte, se nespouštějí žádné úlohy. Pokud nezadáte čas, vytvoří se plán opakované úlohy, který zůstane aktivní, dokud ho neukončíte explicitně.

**Interval opakování**: můžete určit dobu mezi úlohami. V jednom okamžiku může být naplánována pouze jedna úloha, takže pokud je čas vytvořit novou úlohu v rámci plánu úlohy, ale předchozí úloha stále běží, služba Batch novou úlohu nevytvoří až do dokončení předchozí úlohy.  

**Spouštěcí okno**: tady zadáte časový interval od času, kdy plán indikuje, že se má úloha vytvořit, až do chvíle, kdy by se měla dokončit. Pokud aktuální úloha není během svého okna dokončena, další úloha se nespustí.

V dolní části základního formuláře určíte fond, na kterém chcete úlohu spustit. Pokud chcete zjistit informace o ID fondu, vyberte **aktualizovat**. 

![Zadat fond][2]


**ID fondu**: Identifikujte fond, na kterém budete úlohu spouštět.

**Úloha konfigurace úlohy**: vyberte **aktualizovat** , pokud chcete pojmenovat úlohu správce úloh i úkoly přípravy a vydání úlohy, pokud je používáte.

**Priorita**: Udělte úloze prioritu.

**Maximální doba na zdi**: Nastavte maximální dobu, po kterou může úloha běžet. Pokud se nedokončí v časovém rámci, dávka úlohu ukončí. Pokud jste to nestavili, nebudete mít pro úlohu žádný časový limit.

**Maximální počet opakovaných pokusů o úlohu**: Určete počet opakovaných pokusů o úlohu, která může být provedena maximálně čtyřikrát. To není stejné jako počet opakovaných pokusů o volání rozhraní API.

**Po dokončení všech úloh**: výchozí hodnota není žádná akce.

V **případě neúspěchu úlohy**: výchozí hodnota není žádná akce. Úloha se nezdařila, pokud je počet opakování vyčerpán nebo při spuštění úlohy došlo k chybě. 

Pokud v levém navigačním panelu kliknete **na možnost** **Uložit**, můžete sledovat provádění úlohy, a to tak, že vyberete **informace o spuštění**.


## <a name="for-more-information"></a>Další informace

Pokud chcete spravovat úlohu pomocí Azure CLI, přečtěte si téma [AZ batch job-Schedule](/cli/azure/batch/job-schedule).

## <a name="next-steps"></a>Další kroky

[Vytvořte závislosti úkolů pro spouštění úloh, které jsou závislé na jiných úkolech](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


