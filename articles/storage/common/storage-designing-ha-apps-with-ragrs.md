---
title: Navrhování vysoce dostupné aplikace pomocí Azure přístup pro čtení geograficky redundantní úložiště (RA-GRS) | Microsoft Docs
description: Jak používat Azure RA-GRS úložiště do architektury vysokou dostupností aplikace dostatečně flexibilní, aby zpracování výpadků.
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2018
ms.author: tamram
ms.openlocfilehash: f7f3f2d99e5582a1bcb672cc176258dfff9c3217
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322926"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Navrhování pomocí RA-GRS vysoce dostupných aplikací.

Běžné funkce cloudové infrastruktury, jako je Azure Storage je, že poskytují vysokou dostupností platformu pro hostování aplikací. Vývojáři cloudové aplikace musíte pečlivě zvažte, jak využít této platformě, aby poskytovat vysoce dostupné aplikace uživatelům. Tento článek se zaměřuje na tom, jak mohou vývojáři geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) ujistěte se, které svých aplikací Azure Storage jsou vysoce dostupné.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Tento článek se zaměřuje na GRS a RA-GRS. S GRS udržovaly tři kopie dat v primární oblast, kterou jste vybrali při nastavování účtu úložiště. Tři další kopie je udržováno asynchronně v sekundární oblasti definované v Azure. RA-GRS je totéž jako GRS, s tím rozdílem, že máte přístup pro čtení k sekundární kopie. Další informace o různých možnostech redundance úložiště Azure najdete v tématu [replikace Azure Storage](https://docs.microsoft.com/azure/storage/storage-redundancy). Replikace článek také ukazuje dvojice primární a sekundární oblasti.

Existují fragmenty kódu, které jsou zahrnuté v tomto článku a odkaz na ucelenou ukázku na konci, kterou můžete stáhnout a spustit.

> [!NOTE]
> Úložiště Azure nyní podporuje zónově redundantní úložiště (ZRS) pro vytváření vysoce dostupných aplikací. ZRS nabízí jednoduchým řešením pro potřeby redundance mnoho aplikací. ZRS poskytuje ochranu proti selhání hardwaru nebo závažné havárie, které mají vliv na jeden datového centra. Další informace najdete v tématu [Zónově redundantní úložiště (ZRS): aplikace s vysokou dostupností Azure Storage](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Klíčové funkce RA-GRS

Při navrhování aplikace pro RA-GRS mějte na paměti tyto klíčové body:

* Úložiště Azure udržuje kopii dat, které ukládáte ve vaší primární oblasti v sekundární oblasti jen pro čtení. Jak jsme uvedli výše, službu úložiště určuje umístění sekundární oblast.

* Kopie jen pro čtení je [nakonec byl konzistentní](https://en.wikipedia.org/wiki/Eventual_consistency) s daty v primární oblasti.

* Pro objekty BLOB, tabulek a front, můžete dát dotaz na sekundární oblast pro *čas poslední synchronizace* hodnotu, která oznamuje, kdy došlo k chybě poslední replikace z primárního na sekundární oblast. (To není podporováno pro soubory Azure, která nemá RA-GRS redundance v tuto chvíli).

* Klientská knihovna pro úložiště můžete použít k interakci s daty v primární nebo sekundární oblast. Taky můžete přesměrovat číst požadavky automaticky sekundární oblast, když požadavek čtení primární oblasti časový limit.

* Pokud je hlavní problém ovlivňující usnadnění dat v primární oblasti, tým Azure mohou aktivovat geo-převzetí služeb při selhání, v tomto okamžiku se změní položky DNS odkazující na primární oblasti tak, aby odkazoval na sekundární oblast.

* Pokud dojde k selhání geograficky, Azure bude vyberte nové sekundární umístění a replikovat data do tohoto umístění a potom přejděte sekundární položky DNS. Sekundární koncový bod nebude k dispozici, dokud nedokončí účet úložiště, replikaci. Další informace najdete v tématu [co dělat, když dojde k výpadku Azure Storage](https://docs.microsoft.com/azure/storage/storage-disaster-recovery-guidance).

## <a name="application-design-considerations-when-using-ra-grs"></a>Aspekty návrhu aplikace při používání RA-GRS

Účelem tohoto článku je ukazují, jak můžete navrhnout aplikaci, která budou nadále fungovat (i když v omezené kapacitou) i v případě významnější havárie v primárním datovém centru. Můžete vytvářet aplikace pro zpracování přechodný nebo dlouhotrvající problémy čtení ze sekundární oblasti když dojde k problému, který brání systému čtení z primární oblasti. Když primární oblasti opět k dispozici, vaše aplikace může vrátit čtení z primární oblasti.

### <a name="using-eventually-consistent-data"></a>Pomocí nakonec byl konzistentní dat

Navrhované řešení předpokládá, že je přijatelné vrácení potenciálně zastaralých dat do volající aplikace. Vzhledem k tomu, že jsou data v sekundární oblasti nakonec byl konzistentní, je možné, že může být primární oblasti nedostupný, aktualizaci sekundární oblast ještě před ukončením replikace.

Předpokládejme například, zákazník odešle aktualizaci úspěšně, ale před aktualizace rozšířena do sekundární oblasti selže primární oblasti. Když zákazník zeptá, číst data zpět, dostane zastaralá data z oblasti sekundární místo aktualizovaná data. Při navrhování vaší aplikace, musíte se rozhodnout, jestli je to přijatelné a pokud ano, jak budou zprávy zákazníka. 

Později v tomto článku ukážeme, jak zkontrolovat poslední čas synchronizace sekundární dat. Zkontrolujte, zda je aktuální sekundární.

### <a name="handling-services-separately-or-all-together"></a>Zpracování služby samostatně nebo společně všechny

Při nepravděpodobné, je možné jednu služba k dispozici, zatímco ostatní služby jsou stále plně funkční. Dokáže zpracovat opakování a režimu jen pro čtení pro jednotlivé služby samostatně (objekty BLOB, fronty, tabulky), nebo může zpracovat opakování obecně pro všechny služby úložiště společně.

Pokud používáte fronty a objekty BLOB ve vaší aplikaci, můžete rozhodnout umístit do samostatného kódu pro zpracování opakovatelné chyby pro každou z nich. Pak pokud získáte od služby objektů blob zkuste to znovu, ale stále funguje služba fronty, bude ovlivněn jenom část aplikace, který zpracovává objekty BLOB. Pokud se rozhodnete obecně zpracovat všechny opakování služby úložiště a vrátí Opakovatelná chyba volání služby objektů blob, bude mít vliv žádosti o služby objektů blob a fronty služby.

Nakonec to závisí na složitosti vaší aplikace. Můžete rozhodnout není pro zpracování chyby službou, ale místo pro přesměrování požadavků pro všechny úložiště služby sekundární oblast na čtení a spuštění aplikace v režimu jen pro čtení, když jste k problému s jakoukoli službu, úložiště v primární oblasti.

### <a name="other-considerations"></a>Další důležité informace

Jedná se o dalších hledisek, které se budeme zabývat ve zbývající části tohoto článku.

*   Zpracování opakování požadavků na čtení pomocí vzoru jistič

*   Nakonec konzistentní data a čas poslední synchronizace

*   Testování

## <a name="running-your-application-in-read-only-mode"></a>Spuštění aplikace v režimu jen pro čtení

Používání úložiště RA-GRS, musí být schopna zpracovávat i neúspěšné požadavky na čtení a neúspěšné požadavky aktualizace (s aktualizací v tomto případě znamená vložení, aktualizace a odstranění). Pokud primární datového centra softwaru selže, přečtěte si požadavky můžete přesměrovat do sekundárního datového centra. Žádosti o aktualizaci nemůže však přesměrováni na sekundární, protože sekundární je jen pro čtení. Z tohoto důvodu musíte návrhu aplikace na spouštění v režimu jen pro čtení.

Například můžete nastavit příznak, který je zaškrtnuta možnost před všechny žádosti o aktualizaci se odešlou do služby Azure Storage. Pokud jeden z žádosti o aktualizaci se zobrazí výzva, můžete vynechat a vrátí odpověď odpovídající zákazníka. Můžete chtít i zakázat některé funkce úplně, dokud nebude problém vyřešen a upozorněte uživatele, že tyto funkce jsou dočasně nedostupné.

Pokud se rozhodnete samostatně zpracování chyb pro každou službu, musíte se také pro zpracování možnost aplikaci spustit v režimu jen pro čtení službou. Například můžete mít pro každou službu, kterou lze povolit a zakázat příznaky jen pro čtení. Potom může zpracovávat příznak na příslušná místa v kódu.

Bude možné aplikaci spustit v režimu jen pro čtení má další výhody straně – získáte možnost zajistit omezenou funkčnost během upgradu hlavní aplikace. Můžete aktivovat aplikaci spustit v režimu jen pro čtení a přejděte na sekundární datové středisko, o zajištění, že když děláte upgrady, je nikdo přístup k datům v primární oblasti.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Zpracování aktualizací při spuštění v režimu jen pro čtení

Existuje mnoho způsobů pro zpracování žádosti o aktualizaci při spuštění v režimu jen pro čtení. To jsme nebude komplexně pokrývat, ale obecně platí, existuje několik vzorů, které považujete za.

1.  Můžete reagovat na uživatele a sdělte jim, že nejsou aktuálně přijímat aktualizace. Například může systém kontaktní správy umožňují zákazníkům získat přístup k informacím, ale nikoli aktualizace.

2.  Můžete zařadit vaše aktualizace v jiné oblasti. V takovém případě by vaše čekání na aktualizaci požadavků na zápis do fronty v jiné oblasti a pak mít možnost zpracování těchto žádostí po primární datové centrum bude znovu online. V tomto scénáři by měl necháte zákazník vědět, že požadovaná aktualizace ve frontě pro pozdější zpracování.

3.  Vaše aktualizace může zapisovat do účtu úložiště v jiné oblasti. Pak když primární datové centrum přejde do režimu online, můžete mít způsob, jak do primární dat, v závislosti na strukturu dat sloučit tyto aktualizace. Například pokud vytvoříte samostatné soubory pomocí razítka data a času v názvu, můžete zkopírovat tyto soubory zpět na primární oblasti. Tento postup funguje pro některé úlohy, jako jsou data protokolování a iOT.

## <a name="handling-retries"></a>Zpracování opakování

Jak budete vědět, které chyby se opakovatelná? To je dáno Klientská knihovna pro úložiště. Například chybu 404 (prostředek nebyl nalezen) není opakovatelná, protože ho opakování není mohlo vést úspěch. Na druhé straně je opakovatelné 500 Chyba, protože je k serverové chybě a může být přechodný problém. Další podrobnosti, projděte si [otevřete zdrojového kódu pro třídu ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) v klientské knihovny .NET úložiště. (Podívejte se na metodu ShouldRetry.)

### <a name="read-requests"></a>Požadavky na čtení

Čtení požadavky můžete přesměrovat do sekundárního úložiště, pokud dojde k problému s primární úložiště. Uvedené výše v [pomocí nakonec konzistentních dat](#using-eventually-consistent-data), musí být přijatelné pro vaši aplikaci potenciálně číst zastaralá data. Pokud používáte Klientská knihovna pro úložiště pro přístup k datům RA-GRS, můžete zadat chování opakovat požadavek čtení nastavením hodnoty pro **LocationMode** vlastnost na jednu z následujících akcí:

*   **PrimaryOnly** (výchozí)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Když nastavíte **LocationMode** k **PrimaryThenSecondary**, pokud požadavek na počáteční čtení k selže primární koncový bod s opakovatelnou chybou, klient automaticky provede jiný požadavků na čtení sekundární koncový bod. Pokud je chyba vypršení časového limitu serveru, bude mít klient čekání na časový limit vyprší před obdrží Opakovatelná chyba ze služby.

V podstatě existují dva scénáře vzít v úvahu při rozhodování jak reagovat na Opakovatelná chyba:

*   Toto je problém s izolované a další požadavky na primární koncový bod nebude vracet Opakovatelná chyba. Příklad, kdy k tomu může dojít je, když dojde k chybě přechodný síťový.

    V tomto scénáři není žádné snížení výkonu výrazné tomu, aby **LocationMode** nastavena na **PrimaryThenSecondary** jako tato situace nastane pouze zřídka.

*   Jedná o problém s alespoň jedním služby úložiště v primární oblasti a všechny následné žádosti do této služby v primární oblasti pravděpodobně se chcete vrátit opakovatelné chyby v časovém intervalu. Příkladem je, pokud je primární oblasti úplně nedostupná.

    V tomto scénáři je snížení výkonu vzhledem k tomu, že všechny vaše požadavky na čtení bude pokuste se nejdřív primární koncový bod, počkejte časový limit vyprší a potom přepnout na sekundární koncový bod.

U těchto scénářů byste měli určit, existuje se probíhající problém s primární koncový bod a odeslání všech žádostí o přímo na sekundární koncový bod čtení nastavením **LocationMode** vlastnost **SecondaryOnly**. V tomto okamžiku změníte také aplikace spuštěna v režimu jen pro čtení. Tento postup se označuje jako [jistič vzor](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Žádosti o aktualizaci

Vzor jistič lze použít také k aktualizaci požadavky. Žádosti o aktualizaci však nelze přesměrovat do sekundárního úložiště, která je jen pro čtení. Pro tyto požadavky, ponechte **LocationMode** vlastnost nastavena na hodnotu **PrimaryOnly** (výchozí). Ke zpracování těchto chyb, můžete použít metriky na tyto požadavky – například 10 selhání v řádku – a vaše prahová hodnota při splnění, přepnout aplikace do režimu jen pro čtení. Stejné metody pro vrácení slouží k aktualizaci režimu tak, jak je popsáno níže v další části o vzoru jistič.

## <a name="circuit-breaker-pattern"></a>Model Jistič (Circuit Breaker)

Použití vzoru jistič v aplikaci, můžete zabránit v opakování operace, která je pravděpodobně selžou opakovaně. Umožňuje aplikaci nadále spouštět spíše než zabírají čas při operaci je exponenciálnímu opakovat. Navíc rozpozná, když byl opraven chyby, po kterém aplikace to zkuste znovu.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Implementace vzoru jistič

Pokud chcete zjistit, zda je problém s probíhající primární koncový bod, můžete sledovat, jak často klient zaznamená opakovatelné chyby. Protože každý případ se liší, je nutné rozhodnout, prahovou hodnotu, kterou chcete použít pro rozhodnutí přepnout sekundární koncový bod a spusťte aplikaci v režimu jen pro čtení. Může například rozhodnout, že k provedení přepínač, pokud jsou v řádek s žádné úspěchů 10 selhání. Dalším příkladem je přepínač pokud selže 90 % požadavků v období 2 minut.

Pro prvního scénáře můžete jednoduše zachovat počet selhání a pokud je úspěšné dříve, než dorazila maximum, nastavit počet zpět na nulu. Jedním ze způsobů k implementaci pro druhý scénář, je použít objekt MemoryCache (v rozhraní .NET). Pro každý požadavek přidání CacheItem do mezipaměti, nastavte hodnotu na úspěšné provedení (1) nebo selhání (0) a nastavte čas vypršení platnosti 2 minut od nyní (nebo vše, co je vaším omezením čas). Když je dosaženo času vypršení platnosti položky, položka je automaticky odstraněna. Tím získáte okno postupného 2 minut. Pokaždé, když musíte provést žádost službě úložiště, nejprve použijete dotaz Linq napříč objekt MemoryCache pro výpočet procenta úspěšnosti souhrnné zpracování hodnoty a vydělí počet. Když procenta úspěšnosti klesne pod některé prahové hodnoty (například 10 %), nastavte **LocationMode** vlastnost pro čtení žádosti **SecondaryOnly** a přepínačů aplikace do režimu jen pro čtení, než budete pokračovat.

Prahovou hodnotu chyby, které umožňuje určit, kdy vytvořit přepínač se může lišit od služby ve vaší aplikaci, proto byste měli zvážit přitom konfigurovatelné parametry. Toto je také rozhodnete, kde se budou zpracovávat opakovatelné chyby z každé služby samostatně nebo jako jeden, jak je popsáno dříve.

Je potřeba zamyslet se určuje způsob zpracování více instancí aplikace a co dělat, když zjistíte opakovatelné chyby v každé instanci. Například můžete mít 20 virtuálních počítačů s stejnou aplikaci načíst. Samostatně každá instance zpracovávat? Pokud jedna instance spustí, dochází k problémům, chcete omezit odpovědi jenom jednu instanci, nebo chcete opakujte tak, aby měl reagovat všechny instance stejným způsobem, když jedna instance má problém? Zpracování instance samostatně je mnohem jednodušší než pokusu o koordinaci odpovědi mezi nimi, ale tento postup závisí na architektuře vaší aplikace.

### <a name="options-for-monitoring-the-error-frequency"></a>Možnosti sledování četnost chyb

Máte tři hlavní možnosti sledování frekvenci opakování v primární oblasti, aby bylo možné určit, kdy přepnout na sekundární oblast a změňte aplikace spuštěna v režimu jen pro čtení.

*   Přidejte obslužnou rutinu pro [ **bude opakován** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) událostí na [ **informacím OperationContext** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) objekt můžete předat do úložiště požadavky – Toto je metoda zobrazí v tomto článku a používá se v doprovodné ukázce. Tyto události fire vždy, když klient pokus obnovuje žádost, které umožňují sledovat, jak často klient zaznamená opakovatelné chyby na primární koncový bod.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   V [ **Evaluate** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) metoda v zásadách vlastní opakování, můžete spustit vlastní kód vždy, když probíhá opakování. Kromě zápisu při opakovaném pokusu se stane, to také vám dává možnost změny chování vaší opakování.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

*   Je třetí přístup k implementaci vlastních monitorovací komponentu v aplikaci, které průběžně odešle příkaz ping váš koncový bod primárního úložiště s fiktivní číst požadavků (jako je například čtení malých objektů blob) k určení jeho stav. Bude to trvat až některé prostředky, ale není značné množství. Pokud k problému je zjistit, která dosáhne vaší prahové hodnoty, by pak proveďte přepínač tak, aby **SecondaryOnly** a režimu jen pro čtení.

V určitém okamžiku můžete přepnout zpět na používá primární koncový bod a umožní aktualizace. Pokud pomocí jedné z výše uvedených první dvě metody, může jednoduše přepnete zpět do primární koncový bod a povolit režim aktualizace po provedení nahodile vybrané množství času nebo počet operací. Pak můžete nechat ji znovu projít logika opakovaných pokusů. Pokud byl opraven problém, se bude nadále používat primární koncový bod a povolit aktualizace. Pokud stále existuje problém, ji budou jednou přepněte zpátky na sekundární koncový bod a režimu jen pro čtení po selhání kritéria, které jste nastavili.

Třetí scénář, jakmile otestováním koncový bod primárního úložiště opět úspěšné, můžete aktivovat přepínač zpět na **PrimaryOnly** a pokračovat v povolení aktualizace.

## <a name="handling-eventually-consistent-data"></a>Zpracování nakonec byl konzistentní dat

Geograficky redundantní účet úložiště jen pro čtení funguje tak, že replikuje transakce z primární oblasti do sekundární. Tento proces replikace zaručuje, že se data v sekundární oblasti *nakonec byl konzistentní*. To znamená, že všechny transakce v primární oblasti se nakonec objeví v sekundární oblasti, ale že mohou být prodleva předtím, než se objeví, a že neexistuje žádná záruka transakce přicházející do sekundární oblasti ve stejném pořadí jako, ve kterém byly původně uplatňují v primární oblasti. Pokud vaše transakce přicházejí v sekundární oblasti mimo pořadí, můžete *může* vezměte v úvahu vaše data v sekundární oblasti, kterou chcete být v nekonzistentním stavu, dokud služba zachytí.

Následující tabulka znázorňuje příklad co může dojít v případě, že aktualizujete podrobnosti zaměstnanec, aby se mu členem *správci* role. Z důvodu v tomto příkladu to vyžaduje, můžete aktualizovat **zaměstnanec** entitu a aktualizace **role správce** entity s počtem celkový počet správců. Všimněte si, jak aktualizace jsou použity mimo pořadí v sekundární oblasti.

| **Čas** | **Transakce**                                            | **Replikace**                       | **Čas poslední synchronizace** | **výsledek** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakce A: <br> Vložit zaměstnance <br> entity v primární |                                   |                    | Transakce A vložit do primární,<br> není ještě nereplikovaly. |
| T1       |                                                            | Transakce A <br> replikovat do<br> sekundární | T1 | Replikovat do sekundárního A transakce. <br>Čas poslední synchronizace aktualizovat.    |
| T2       | Transakce B:<br>Aktualizace<br> Zaměstnanec entity<br> v primární  |                                | T1                 | Transakce B zapsána do primární,<br> není ještě nereplikovaly.  |
| T3       | Transakce C:<br> Aktualizace <br>Správce<br>entitu role v<br>primární |                    | T1                 | Transakce zapsána do primární, C<br> není ještě nereplikovaly.  |
| *T4*     |                                                       | Transakce C <br>replikovat do<br> sekundární | T1         | Transakce C replikovat na sekundární.<br>Nelze aktualizovat, protože LastSyncTime <br>ještě nebyla replikována transakce B.|
| *T5*     | Ke čtení entit <br>ze sekundární                           |                                  | T1                 | Získat zastaralé hodnotu pro zaměstnance <br> entity vzhledem k tomu, že nebyla transakce B <br> ještě nereplikovaly. Získat novou hodnotu<br> entitu role správce vzhledem k tomu, že má C<br> replikovat. Čas poslední synchronizace stále ještě<br> byla aktualizovat, protože transakce B<br> nebyla replikována. Můžete zjistit<br>Entita role správce je nekonzistentní <br>protože je datum a čas entity za <br>Čas poslední synchronizace. |
| *T6*     |                                                      | Transakce B<br> replikovat do<br> sekundární | T6                 | *T6* – mít všechny transakce prostřednictvím C <br>byla replikovat, čas poslední synchronizace<br> je aktualizována. |

V tomto příkladu se předpokládá, že klient přepíná na čtení ze sekundární oblasti v T5. Můžou číst **role správce** entity v tuto chvíli, ale entita obsahuje hodnotu pro počet správců, který není konzistentní s počtem **zaměstnanec** entit, které jsou označeny jako správci v sekundární oblasti v tuto chvíli. Váš klient jednoduše zobrazit tuto hodnotu s riziko, že je nekonzistentní informace. Alternativně může klient pokusí zjistit, který **role správce** je ve stavu potenciálně nekonzistentní, protože aktualizace dojít mimo pořadí a potom informovat uživatele o této skutečnosti.

Rozpoznat, zda má potenciálně nekonzistentní data, může klient použít hodnotu *čas poslední synchronizace* , můžete se kdykoli dotazováním služby úložiště. Znamená to, čas, kdy byl naposledy data v sekundární oblasti konzistentní a když službu měl u všech transakcí před tento bod v čase. V příkladu výše, po vloží službu **zaměstnanec** entity v sekundární oblasti, čas poslední synchronizace je nastaven na *T1*. Zůstane v *T1* až do aktualizace služby **zaměstnanec** entity v sekundární oblasti, pokud je nastavena na *T6*. Pokud klient načte čas poslední synchronizace při přečte entity na *T5*, ho můžete porovnat s časovým razítkem u entity. Pokud se časové razítko u entity později než čas poslední synchronizace, pak tato entita je ve stavu potenciálně nekonzistentní a může trvat vše, co je pro vaši aplikaci příslušnou akci. Použít toto pole je nutné vědět, kdy byla dokončena poslední aktualizace na primární.

## <a name="testing"></a>Testování

Je důležité k testování, že chování vaší aplikace podle očekávání, pokud se setká s opakovatelnou chyby. Například je potřeba provést testování, aby aplikace přepínače na sekundární a do režimu jen pro čtení, když zjistí problém a přepínače zpět Jakmile primární oblasti opět k dispozici. K tomu potřebujete způsob, jak simulovat opakovatelné chyb a řízení, jak často k nim dojde.

Můžete použít [Fiddler](http://www.telerik.com/fiddler) zachytí a upravit odpovědi HTTP ve skriptu. Tento skript můžete identifikovat odpovědi, které pocházejí z váš primární koncový bod a změnit stavový kód protokolu HTTP na takový, který Klientská knihovna pro úložiště rozpozná jako Opakovatelná chyba. Tento fragment kódu ukazuje jednoduchý příklad Fiddler skriptu, který zabrání odpovědi ke čtení požadavky DHCP proti **employeedata** tabulky vrátit 502 stav:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Tento příklad zachytí většímu počtu požadavků a změnit jedině tak může rozšířit **responseCode** u některých z nich lépe simulace scénářem z reálného prostředí. Další informace o přizpůsobení Fiddler skriptů najdete v tématu [úprava požadavek nebo odpověď](http://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) v dokumentaci k aplikaci Fiddler.

Pokud jste provedli prahové hodnoty pro přepínání vaší aplikace do režimu jen pro čtení konfigurovat, bude snazší testování chování se svazky mimo produkční transakce.

## <a name="next-steps"></a>Další kroky

* Další informace o přístup pro čtení geografická redundance, včetně další příklad nastavení LastSyncTime, najdete v tématu [možnosti redundance úložiště Windows Azure a geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Kompletní příklad znázorňující aby přepínač mezi primární a sekundární koncové body, najdete v tématu [Azure Samples – vzor jistič pomocí úložiště RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
