---
title: Kontrola chyb fondu a uzlu služby Batch
description: Zkontrolujte chyby a jak se vyhnout při vytváření fondů a uzlů
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435208"
---
# <a name="checking-for-pool-and-node-errors"></a>Kontrola chyb fondu a uzlu

Při vytváření a Správa fondů služby Batch, operací, které okamžitě a jsou asynchronní operace, které nejsou okamžitě, běžet na pozadí a může trvat několik minut.

Detekce selhání pro operace, které se projeví okamžitě je jednoduché, všechny chyby se okamžitě vrácené rozhraní API, rozhraní příkazového řádku nebo uživatelského rozhraní.

Tento článek se týká operace na pozadí, které lze provést pro fondy a uzly fondu – Určuje, jak lze zjistit chyby, a jak se lze vyvarovat chyby.

## <a name="pool-errors"></a>Fond chyby

### <a name="resize-timeout-or-failure"></a>Časový limit pro změnu velikosti nebo selhání

Pokud je zadána vytvořením nového fondu nebo změnu velikosti existujícího fondu, cílový počet uzlů.  Operace se ihned dokončí, ale skutečný přidělení nové uzly nebo odebrání existujících uzlů probíhá na pozadí přes co může být několik minut.  Časový limit změny velikosti je zadán v [vytvořit](https://docs.microsoft.com/rest/api/batchservice/pool/add) nebo [změnit velikost](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API – Pokud nelze získat cílový počet uzlů v časový limit pro změnu velikosti, pak operace se zastaví s fondem, že přejdete do stabilního stavu a k chybám změny velikosti.

Časový limit změny velikosti je ohlášena [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) vlastnost posledního vyhodnocení, která obsahuje jednu nebo více chyb, ke kterým došlo.

Vypršení časového limitu pro změnu velikosti mezi běžné příčiny patří:
- Změna velikosti vypršení časového limitu je příliš krátké.
  - Výchozí časový limit je 15 minut, což je obvykle dostatek času pro uzly fondu přidělené nebo se musí odebrat.
  - Při přidělování velký počet uzlů (více než 1000 z Marketplace image) nebo víc než 300 uzlů z vlastní image, při vytváření fondu nebo změny velikosti, se doporučuje časový limit 30 minut.
- Kvóta pro jádra nedostatečné
  - Účet batch obsahuje kvóty pro počet jader, která se rozmístěných v mnoha všechny fondy.  Batch nebude přidělovat uzly, jakmile bylo dosaženo této kvóty.  Kvóta pro jádra [je možné zvýšit](https://docs.microsoft.com/azure/batch/batch-quota-limit) povolit další uzly, které mají být přiděleny.
- Nedostatečná podsítě IP adres při [fond je ve virtuální síti](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsíť virtuální sítě musí mít dostatek nepřiřazených IP adres pro přidělení na všech uzlech fondu požadovány, v opačném případě uzly nelze vytvořit.
- Nedostatek prostředků při [fond je ve virtuální síti](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Prostředky, jako jsou – nástroje pro vyrovnávání zatížení, veřejné IP adresy a skupin zabezpečení sítě se vytvoří v předplatném sloužící k vytvoření účtu Batch.  Kvóty předplatných pro tyto prostředky musí být dostatečné.
- Použití vlastní image virtuálního počítače pro velká fondy
  - Velké fondy pomocí vlastních imagí můžete déle přidělit a změňte jeho velikost vypršení časového limitu může dojít.  Jsou součástí omezení a doporučení pro konfigurace [konkrétní článek](https://docs.microsoft.com/azure/batch/batch-custom-images). 

### <a name="auto-scale-failures"></a>Selhání automatické škálování

Místo ve fondu vytvořit nebo změnit jeho velikost explicitním nastavením cílového počtu uzlů pro fond, je možné automaticky škálovat počet uzlů ve fondu.  [Pro fond lze vytvořit vzorec automatického škálování](https://docs.microsoft.com/azure/batch/batch-automatic-scaling), který se vyhodnotí v pravidelných intervalech konfigurovatelné nastavení cílového počtu uzlů pro fond.  Následující typy chyb může dojít a zjistili:

- Vyhodnocení automatické škálování může selhat.
- Výsledný operace změny velikosti může selhat a vypršení časového limitu.
- Může se jednat problém s vzorec automatického škálování, což vede k nesprávné uzel cílové hodnoty, s změny velikosti práci nebo vypršení časového limitu.

Získat informace o posledního vyhodnocení automatické škálování pomocí [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) vlastnost, která informuje o době hodnocení, hodnoty a výsledek hodnocení a všechny chyby provádění vyhodnocení.

Informace o všechna hodnocení automaticky zachycena [fondu změnit velikost události dokončení](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Odstranění

Za předpokladu, že je uzlů ve fondu, fond odstranit výsledky operace v uzlech se neodstraní, za nímž následuje samotného objektu fondu.  Může trvat několik minut, než uzly fondu, která se má odstranit.

[Fondu stavu](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) se nastaví na odstranění během procesu odstranění.  Volající aplikace může zjistit, pokud se odstranění fondu trvá moc dlouho pomocí vlastnosti "stavu" a "stateTransitionTime".

## <a name="pool-compute-node-errors"></a>Fond výpočetních uzlů chyby

Uzly mohou být přiděleny úspěšně ve fondu, ale různými problémy, které můžou vést k uzlům právě není v pořádku a není možné použít.  Po přidělení uzlů ve fondu se budou účtovat poplatky za a je proto důležité ke zjišťování problémů, abyste neplatili za uzly, které nelze použít.

### <a name="start-task-failure"></a>Selhání spuštění úlohy

Volitelně [spouštěcí úkol](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) se dá nastavit pro fond.  Stejně jako u jakékoli jiné úlohy, je možné zadat příkazový řádek a zdrojové soubory ke stažení ze služby storage.  Spouštěcí úkol je zadána pro fond, ale spustit na každém uzlu - po zahájení každý uzel a poté spusťte na nich spouštěcí úkol.  Další vlastnosti [spouštěcí úkol](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask), "waitForSuccess", určí, zda služby Batch by měl čekat pro spouštěcí úkol se nepodaří před naplánováním jakýchkoli úkolů na uzlu.

Pokud selže spouštěcí úkol a zadat konfigurace zahájení úlohy počkat na úspěšné dokončení, uzel nepoužitelné a bude pořád nabíhat poplatky.

Selhání spuštění úkolů můžete zjistit pomocí [výsledek](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) a [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) vlastnosti na nejvyšší úrovni [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) vlastnost uzlu.

Neúspěšné spouštěcí úkol také vede k uzlu [stavu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) nastavena na "starttaskfailed", ale pouze pokud byl nastaven 'waitForSuccess' na 'true'.

Stejně jako u každého úkolu můžete mít řadu příčin neúspěšného úkolu spuštění.  Řešení potíží s, by měly být porovnány standardního výstupu skriptu, stderr a všechny soubory další protokolu specifické pro úlohy.

### <a name="application-package-download-failure"></a>Chyba při stahování balíčku aplikace

Jeden nebo více balíčků aplikací můžete volitelně zadaný pro fond, zadaný balíček soubory se stáhnou do každého uzlu a nekomprimovaný po spuštění uzlu, ale před úkoly jsou naplánovány.  Je běžné použití příkazový řádek úkolu spuštění, společně s balíčky aplikací, které pokud chcete kopírovat soubory do jiného umístění nebo spusťte instalační program, třeba.

Nepovedlo se stáhnout a dekomprimovat balíček aplikace se ohlásí uzel [chyby](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) vlastnost.  Stav uzlu se nastaví "nepoužitelné".

### <a name="node-in-unusable-state"></a>Uzel v nepoužitelném stavu

[Stav uzlu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) lze nastavit na nepoužitelné z mnoha důvodů.  Když nepoužitelný, úlohy nelze naplánovat na uzlu, ale uzlu bude pořád nabíhat poplatky.

Batch se vždy pokusí obnovit nepoužitelné uzly, ale obnovení může nebo nemusí být možné, v závislosti na příčinu.

Pokud nelze určit příčinu, bude nahlášena uzel [chyby](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) vlastnost.

Příklady příčiny 'nepoužitelné' uzly:

- Neplatný vlastní image; není správně připravily, např.
- Chyby infrastruktury nebo nízké úrovně upgradu, což vede k základní virtuální počítač se přesunula. Batch se obnoví uzlu.

### <a name="node-agent-log-files"></a>Soubory protokolu agenta uzlu

Pokud je nutné se obrátit na podporu týkající se problému uzlu fondu, může získat soubory protokolů z procesu agenta služby Batch, na kterém běží na všech uzlech fondu.  Soubory protokolu pro uzly se dají nahrát, prostřednictvím portálu Azure portal, Průzkumníka služby Batch, nebo [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs).  Odesílání a ukládání souborů protokolu může být velmi užitečné jako uzel nebo fond můžete odstranit tak náklady spuštěné uzly.

## <a name="next-steps"></a>Další postup

Ujistěte se, že se že aplikace nasadila, komplexní kontroly chyb, zejména pro asynchronní operace, tak, aby problémy můžete rychle zjistili a diagnostikovat.
