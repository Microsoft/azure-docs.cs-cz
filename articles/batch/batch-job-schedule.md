---
title: Naplánujte si své úlohy
description: Ke správě úkolů použijte plánování úloh.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672430"
---
# <a name="schedule-jobs-for-efficiency"></a>Naplánovat úlohy pro efektivitu

Plánování dávkových úloh umožňuje určit prioritu úloh, které chcete spustit jako první, s přihlédnutím k úkolům, které mají závislosti na jiných úkolech. Plánováním úloh můžete zajistit, abyste použili co nejméně zdrojů. Uzly mohou být vyřazeny z provozu, když nejsou potřeba, úkoly, které jsou závislé na jiných úkolech, se stočily právě včas a optimalizovaly pracovní postupy. Spustí se pouze jedna úloha. Nový se nespustí, dokud se nedokončí předchozí. Úlohu můžete nastavit tak, aby byla automaticky dokončována. 

## <a name="benefit-of-job-scheduling"></a>Výhody plánování úloh

Výhodou plánování úloh je, že můžete zadat plán pro vytváření pracovních míst. Úkoly, které naplánujete pomocí úlohy správce úloh, jsou přidruženy k úloze. Úloha správce úloh vytvoří úkoly pro úlohu. Chcete-li tak učinit, úloha správce úloh musí být ověřena pomocí účtu Batch. Použijte přístupový token AZ_BATCH_AUTHENTICATION_TOKEN. Token umožní přístup ke zbytku úlohy. 

## <a name="use-the-portal-to-schedule-a-job"></a>Naplánování úlohy pomocí portálu

   1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

   2. Vyberte dávkový účet, ve kterém chcete naplánovat úlohy.

   3. Výběrem **možnosti Přidat** vytvořte nový plán úloh a vyplňte **základní formulář**.



![Naplánování úlohy][1]

**ID plánu úloh**: Jedinečný identifikátor tohoto plánu úloh.

**Zobrazovaný název**: Zobrazovaný název úlohy nemusí být jedinečný, ale má maximální délku 1024 znaků.

**Nespouštějte, dokud**: Určuje nejbližší čas spuštění úlohy. Pokud tuto skutečnost nenastavíte, plán bude připraven ke spuštění úloh okamžitě.

**Nespouštějte po**: Po nastavené době nejsou spuštěny žádné úlohy. Pokud nezadáte čas, vytváříte opakovaný plán úloh, který zůstane aktivní, dokud jej explicitně neukončíte.

**Interval opakování**: Můžete určit dobu mezi úlohami. Můžete mít pouze jednu úlohu v čase naplánované, takže pokud je čas vytvořit novou úlohu v rámci plánu úloh, ale předchozí úloha je stále spuštěna, služba Dávka nevytvoří novou úlohu, dokud nebude dokončena předchozí úloha.  

**Počáteční okno**: Zde zadáte časový interval, počínaje časem, kdy plán označuje, že má být úloha vytvořena, až do doby, kdy by měla být dokončena. Pokud se aktuální úloha nedokončí během svého okna, další úloha se nespustí.

V dolní části základního formuláře určíte fond, ve kterém má být úloha spuštěna. Chcete-li najít informace o ID fondu, vyberte **možnost Aktualizovat**. 

![Určení fondu][2]


**ID fondu**: ID fondu : IDentifikujte fond, ve kterých budete úlohu spouštět.

**Úloha konfigurace úlohy**: Vyberte **aktualizovat,** chcete-li pojmenovat úlohu Správce úloh, jakož i úlohy přípravy a uvolnění úlohy, pokud je používáte.

**Priorita**: Dejte práci prioritu.

**Maximální doba nástěnných hodin**: Nastavte maximální dobu, po kterou může být úloha spuštěna. Pokud se nedokončí v časovém rámci, Batch ukončí úlohu. Pokud to toto nenastavíte, neexistuje žádný časový limit pro práci.

**Maximální počet opakování úkolu**: Zadejte, kolikrát lze úkol opakovat maximálně čtyřikrát. To není stejné jako počet opakování volání rozhraní API může mít.

**Po dokončení všech úkolů**: Výchozí hodnota není akce.

**Pokud úloha selže**: Výchozí hodnota není akce. Úloha se nezdaří, pokud je počet opakování vyčerpán nebo došlo k chybě při spuštění úlohy. 

Po výběru **možnosti Uložit**, pokud přejdete na **plány úloh** v levé navigaci, můžete sledovat provádění úlohy výběrem **informace o spuštění**.


## <a name="for-more-information"></a>Další informace

Pokud chcete spravovat úlohu pomocí příkazového příkazu k řešení Azure, přečtěte si seznam [dávkového plánu úloh az](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Další kroky

[Vytvořte závislosti mezi úkoly pro spouštění úloh, které závisí na jiných úkolech](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


