---
title: Navrhování aplikací s vysokou dostupností pomocí Azure Storage geograficky redundantní jen pro čtení (RA-GRS) | Dokumentace Microsoftu
description: Jak používat úložiště Azure RA-GRS se navrhovat vysoce dostupné aplikace dostatečně flexibilní, aby zpracování výpadků.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: afcda23faf4e9f0999442fa91d3c016e446c04db
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524538"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Navrhování aplikací s vysokou dostupností pomocí RA-GRS

To běžná funkce cloudové infrastruktury jako služby Azure Storage je, že poskytuje vysoce dostupnou platformu pro hostování aplikací. Vývojáři cloudové aplikace, třeba důkladně zvážit, jak využít této platformy k zajištění vysoce dostupných aplikací na svoje uživatele. Tento článek se týká jak mohou vývojáři pro čtení geograficky redundantní úložiště s přístupem (pro čtení RA-GRS) ujistěte se, které jsou jejich aplikace služby Azure Storage s vysokou dostupností.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Tento článek se zaměřuje na GRS a RA-GRS. S GRS jsou tři kopie dat uchovávány v primární oblasti, kterou jste vybrali při nastavování účtu úložiště. Další tři kopie se zachovají asynchronně v sekundární oblasti definované v Azure. RA-GRS nabízí geograficky redundantní úložiště s přístupem pro čtení na sekundární kopii.

Informace o tom, které jsou primární oblasti souřadnicí které sekundární oblasti naleznete v tématu [obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Nejsou zahrnuty v tomto článku a odkaz na kompletní příklad na konci, kterou můžete stáhnout a spustit fragmenty kódu.

> [!NOTE]
> Azure Storage teď podporuje zónově redundantní úložiště (ZRS) pro vytváření vysoce dostupných aplikací. ZRS poskytuje jednoduché řešení pro spoustu aplikací na potřeby redundance. ZRS poskytuje ochranu proti selhání hardwaru nebo katastrofickými jiného problému ovlivňujícího by to mělo dopad jednom datacentru. Další informace najdete v tématu [zónově redundantní úložiště (ZRS): aplikace služby Azure Storage s vysokou dostupností](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Klíčové funkce služby RA-GRS

Při navrhování aplikace za RA-GRS, mějte na paměti tyto klíčové body:

* Azure Storage uchovává kopii dat, které ukládáte ve vaší primární oblasti do sekundární oblasti jen pro čtení. Jak bylo uvedeno výše, službu úložiště určuje umístění sekundární oblasti.

* Kopírování jen pro čtení je [konzistentní](https://en.wikipedia.org/wiki/Eventual_consistency) s daty v primární oblasti.

* Pro objekty BLOB, tabulky a fronty, můžete dát dotaz na sekundární oblastí pro *čas poslední synchronizace* hodnotu, která určuje, kdy došlo k poslední replikace z primární do sekundární oblasti. (To se nepodporuje pro soubory Azure, které v tuto chvíli nemá redundance RA-GRS).

* Můžete použít klientskou knihovnu pro úložiště pro interakci s daty v primární nebo sekundární oblast. Můžete také vytvořit přesměrování čtení požadavky automaticky do sekundární oblasti, pokud vyprší časový limit čtení žádosti do primární oblasti.

* Pokud neexistuje závažný problém by to mělo dopad dostupnost dat v primární oblasti, tým Azure můžou aktivovat geo-převzetí služeb při selhání, v tomto okamžiku se záznamy DNS, odkazuje na primární oblast změní tak, aby odkazoval na sekundární oblasti.

* Pokud dojde k selhání geo, Azure bude vyberte nový sekundární umístění a replikovat data do tohoto umístění a pak na něj odkažte sekundární položky DNS. Sekundární koncový bod nebude k dispozici, až do dokončení účet úložiště, které se replikují. Další informace najdete v tématu [co dělat, když dojde k výpadku služby Azure Storage](https://docs.microsoft.com/azure/storage/storage-disaster-recovery-guidance).

## <a name="application-design-considerations-when-using-ra-grs"></a>Aspekty návrhu aplikace při používání RA-GRS

Účelem tohoto článku je ukazují, jak navrhovat aplikace, která bude v dále fungovat (byť jen omezená kapacita) i v případě větší havárie v primárním datovém centru. Můžete navrhnout vaši aplikaci obsluhování krátkodobé nebo dlouhodobé problémy, přečtěte si téma ze sekundární oblasti, když dojde k nějakému problému, který brání systému čtení z primární oblasti. Až primární oblast opět k dispozici, vaše aplikace může vrátit na čtení z primární oblasti.

### <a name="using-eventually-consistent-data"></a>Pomocí konečnou konzistenci dat.

Navržené řešení se předpokládá, že je přijatelné vrácení potenciálně zastaralých dat volající aplikace. Vzhledem k tomu, že data v sekundární oblasti jsou nakonec konzistentní, je možné, že může být primární oblasti nedostupný, před dokončením příkazu se aktualizace do sekundární oblasti, které se replikují.

Předpokládejme například, zákazník odešle aktualizaci úspěšně, ale selže primární oblast předtím, než se šíří aktualizace do sekundární oblasti. Když zákazník požádá o načtení dat zpět, dostane zastaralá data ze sekundární oblasti místo aktualizovaná data. Při návrhu aplikace, musíte rozhodnout, jestli je to přijatelné a pokud ano, jak bude zpráva zákazníka. 

Dále v tomto článku vám ukážeme, jak zkontrolovat poslední čas synchronizace pro sekundární data ke kontrole, jestli je aktuální sekundární.

### <a name="handling-services-separately-or-all-together"></a>Zpracování služby samostatně nebo společně

Zatímco je nepravděpodobné, je možné, jedna služba dostupná, zatímco ostatní služby jsou stále plně funkční. Dokáže zpracovat opakovaných pokusů a režimu jen pro čtení pro každou službu zvlášť (objekty BLOB, fronty, tabulky), nebo opakování obecně pro všechny služby storage dokáže zpracovat společně.

Pokud používáte fronty a objekty BLOB ve vaší aplikaci, můžete rozhodnout umístit do samostatného kódu pro zpracování chyb, které vyvolaly pro každou z nich. Pokud získáte ze služby blob service zkuste to znovu, ale stále funguje služba front, pak bude mít vliv pouze část aplikace, která zpracovává objekty BLOB. Pokud se rozhodnete obecně zpracovat všechny opakování služby storage a volání služby blob service vrátí Opakovatelná chyba, bude mít vliv požadavků na službu blob service a službu front.

Nakonec záleží na složitosti aplikace. Můžete rozhodnout zpracovávat selhání služby, ale místo pro přesměrování požadavků pro všechny služby storage do sekundární oblasti na čtení a spuštění aplikace v režimu jen pro čtení, při zjištění problému se všemi službami úložiště v primární oblasti.

### <a name="other-considerations"></a>Další důležité informace

Jedná se o opatření, které se budeme zabývat ve zbývající části tohoto článku.

*   Zpracování opakování požadavků na čtení pomocí vzoru Circuit Breaker

*   Nakonec konzistentní data a čas poslední synchronizace

*   Testování

## <a name="running-your-application-in-read-only-mode"></a>Spuštění aplikace v režimu jen pro čtení

Pokud chcete používat úložiště RA-GRS, musí být schopná zpracovat i neúspěšné požadavky na čtení a nebyl úspěšný, požadavků na aktualizace (aktualizace v tomto případě to znamená vložení, aktualizace a odstranění). Pokud primární datové centrum selže, přečtěte si požadavky můžete přesměrovat do sekundárního datového centra. Žádosti o aktualizaci nelze však přesměrovat na sekundární, protože slouží jen pro čtení. Z tohoto důvodu potřebujete pro návrh aplikace na spouštění v režimu jen pro čtení.

Můžete například nastavit příznak, který je vrácen před všechny žádosti o aktualizaci se odešlou do služby Azure Storage. Když prochází jedné žádosti o aktualizaci můžete přeskočit ho a vrací odpovídající odpověď na zákazníka. Můžete dokonce i zakázat některé funkce zcela, dokud se problém vyřeší a upozornit uživatele, že tyto funkce jsou dočasně nedostupné.

Pokud se rozhodnete pro zpracování chyb pro každou službu zvlášť, je také potřeba zpracovat možnost spouštět vaše aplikace v režimu jen pro čtení službou. Například může mít jen pro čtení příznaky pro každou službu, která mohou být povolené a zakázané. Potom můžete zpracovávat příznak na příslušných místech ve vašem kódu.

Možnost spuštění aplikace v režimu jen pro čtení má další výhodu na straně – to vám dává možnost zajistit omezené funkce během upgradu hlavní aplikace. Aplikace pro spouštění v režimu jen pro čtení a přejděte do sekundárního datového centra, můžete aktivovat zajistit, že nikdo přistupuje k datům v primární oblasti, zatímco provádíte upgrade.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Zpracování aktualizací při spuštění v režimu jen pro čtení

Existuje mnoho způsobů zpracování požadavků na aktualizace při spuštění v režimu jen pro čtení. Jsme nebudeme se zabývat tím komplexně, ale obecně platí, máte několik vzorů, které považujete za.

1.  Můžete reagovat na uživatele a sdělte jim, že nejsou aktuálně přijímat aktualizace. Například systém správy kontaktů umožňuje zákazníkům přístup ke kontaktní údaje, ale ne provádět aktualizace.

2.  Můžete aktualizace v jiné oblasti. V takovém případě by vaše čekající aktualizace požadavků na zápis do fronty v jiné oblasti a pak je mít způsob, jak po primární datové centrum vrátí do režimu online znovu zpracování těchto požadavků. V tomto scénáři byste pak měli nechat zákazníky vědět, že aktualizace požadované zařazeno do fronty pro pozdější zpracování.

3.  Aktualizace lze zapisovat do účtu úložiště v jiné oblasti. Když primární datové centrum vrátí do režimu online, můžete mít způsob, jak sloučit tyto aktualizace na primární data, v závislosti na struktuře dat. Například pokud vytváříte samostatné soubory pomocí razítka data a času v názvu, můžete zkopírovat tyto soubory zpět do primární oblasti. Tento postup funguje pro některé úlohy, jako jsou data protokolování a iOT.

## <a name="handling-retries"></a>Zpracování opakovaných pokusů

Jak budete vědět, které chyby jsou opakovatelná? Toto je určeno klientskou knihovnu pro úložiště. Například chybu 404 (prostředek se nenašel) není opakovatelná, protože ho opakování není nejspíš úspěch. Na druhé straně je opakovatelná 500 chyb, protože se jedná o chybu serveru a může být přechodný problém. Další podrobnosti, projděte si [otevřete zdrojový kód pro třídu ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) v klientské knihovně .NET úložiště. (Hledejte metodu ShouldRetry.)

### <a name="read-requests"></a>Požadavky na čtení

Žádosti o čtení je možné přesměrovat do sekundárního úložiště, pokud dojde k nějakému problému s primární úložiště. Co jsou uvedené výše v [pomocí nakonec konzistentní Data](#using-eventually-consistent-data), musí být přijatelné pro vaši aplikaci ke čtení potenciálně zastaralá data. Pokud používáte klientskou knihovnu pro úložiště pro přístup k datům RA-GRS, můžete určit chování opakování žádosti o čtení tak, že nastavíte hodnotu **LocationMode** vlastnost na jednu z následujících akcí:

*   **PrimaryOnly** (výchozí)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Při nastavení **LocationMode** k **PrimaryThenSecondary**, pokud původní čtení žádost na primární koncový bod selže došlo k opakovatelné chybě, klient automaticky provede další požadavky na čtení sekundární koncový bod. Pokud je chyba vypršení časového limitu serveru, bude mít klient čekání na časový limit vyprší před přijme Opakovatelná chyba služby.

V podstatě existují dva scénáře, které je třeba zvážit, pokud se rozhodujete, jak reagovat na Opakovatelná chyba:

*   Toto je problém s izolované a následné žádosti na primární koncový bod nevrátí Opakovatelná chyba. Příkladem, kdy k tomu může dojít, je po přechodných síťových chyb.

    V tomto scénáři není žádné snížení výkonu tím, že **LocationMode** nastavena na **PrimaryThenSecondary** jako tato situace nastane pouze zřídka.

*   Toto je problém s nejméně jedné služby úložiště v primární oblasti a všechny následné požadavky na tuto službu v primární oblasti se pravděpodobně vrátit opakovatelná chyby pro určitou dobu. Příkladem tohoto je, pokud primární oblast úplně nedostupná.

    V tomto scénáři je snížení výkonu vzhledem k tomu, že všechny vaše požadavky na čtení se nejprve zkuste primární koncový bod, čekat vypršení časového limitu vyprší, potom přepněte do sekundárního koncového bodu.

Pro tyto scénáře, měli byste identifikovat, který existuje probíhající problému s primární koncový bod a odeslat všechny číst požadavků přímo do sekundárního koncového bodu tak, že nastavíte **LocationMode** vlastnost **SecondaryOnly** . V současné době je nutné změnit také aplikace na spouštění v režimu jen pro čtení. Tento postup se označuje jako [vzoru Circuit Breaker](https://msdn.microsoft.com/library/dn589784.aspx).

### <a name="update-requests"></a>Žádosti o aktualizaci

Model jistič lze použít také k aktualizaci žádosti. Žádosti o aktualizaci však nelze přesměrovat do sekundárního úložiště, která je jen pro čtení. Tyto žádosti byste měli nechat **LocationMode** vlastnost nastavena na hodnotu **PrimaryOnly** (výchozí). Zpracování těchto chyb, můžete použít metriky na tyto žádosti – například 10 selhání za sebou – a když se splní vaše mezní hodnota, přepínat aplikace do režimu jen pro čtení. Můžete použít stejné metody pro vrácení aktualizace režimu jako těch popsaných níže v části Další informace o modelu jistič.

## <a name="circuit-breaker-pattern"></a>Model Jistič (Circuit Breaker)

Použití vzoru Circuit Breaker v aplikaci, můžete zabránit opakování operace, která pravděpodobně selže opakovaně. Umožňuje aplikaci, aby kontinuálně běžely, spíše než zabíraly čas při operaci opakovat exponenciálně zvyšuje. Navíc rozpozná, pokud byla opravena chyba, kdy může aplikace zkusit operaci zopakovat.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Implementace vzoru circuit breaker

Pokud chcete zjistit, že se k aktuálnímu problému s primární koncový bod, můžete sledovat, jak často klient zaznamená opakovatelná chyby. Protože každý případ je rozdílný, budete muset při rozhodování o prahovou hodnotu, kterou chcete použít pro rozhodnutí si přepnout na sekundární koncový bod a spusťte aplikaci v režimu jen pro čtení. Například může rozhodnout provádět přepínač v případě, že nedochází k chybám 10 po sobě s žádné úspěchy. Dalším příkladem je přepnout, pokud selžou i 90 % požadavků v období 2 minuty.

Pro prvního scénáře můžete jednoduše udržovat přehled o počtu chyb a pokud není úspěšné před dosažením maximálního, nastaví počet zpět na nulu. Jedním ze způsobů na jeho implementaci pro druhý scénář, je použití objektu MemoryCache (v rozhraní .NET). Pro každý požadavek přidejte CacheItem do mezipaměti, nastavte hodnotu na úspěšné provedení (1) nebo selhání (0) a nastavte čas vypršení platnosti až 2 minut od teď (nebo cokoli, co je vaše omezení času). Když je dosaženo času vypršení platnosti položky, položka se automaticky odebere. Tím získáte postupné okno 2 minuty. Pokaždé, když zadáte požadavek na službu storage, je nejprve použít dotaz Linq napříč objektu MemoryCache pro výpočet Procento úspěšných pokusů sečte hodnoty a vydělením o daný počet. Pokud klesne Procento úspěšných pokusů pod některé prahové hodnoty (například 10 %), nastavit **LocationMode** vlastnost pro čtení žádostí o **SecondaryOnly** a přepínat aplikace do režimu jen pro čtení, než budete pokračovat.

Prahová hodnota chyb používá k určení toho, kdy přechod usnadní může lišit od služby ve vaší aplikaci, proto byste měli zvážit, díky kterým jsou konfigurovatelné parametry. Toto je také pokud jste se rozhodli zpracovávat opakovatelná chyby z každé služby samostatně nebo jako jednu, jak je popsáno výše.

Dalším aspektem je způsob zpracování více instancí aplikace a co dělat, když zjistíte opakovatelná chyby v každé instanci. Například může mít 20 virtuálních počítačů s tu samou aplikaci načíst. Samostatně každá instance zpracování? Pokud jedna instance spuštění dochází k problémům, chcete omezit odpověď jenom jednu instanci nebo chcete akci mít všechny instance nereaguje stejně jako jedna instance má problém? Zpracování instance samostatně je mnohem jednodušší, než se pokoušet o koordinaci mezi ně odpovědi, ale postup závisí na architektuře vaší aplikace.

### <a name="options-for-monitoring-the-error-frequency"></a>Možnosti sledování frekvence chyb

Budete mít tři hlavní možnosti monitorování četnost opakování v primární oblasti, aby bylo možné zjistit, kdy se má přejít do sekundární oblasti a změňte aplikace na spouštění v režimu jen pro čtení.

*   Přidání obslužné rutiny [ **opakování** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) událostí na [ **OperationContext** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) požaduje objekt posílají do úložiště – metoda zobrazí v tomto článku a používá se v průvodní ukázce. Tyto události se aktivuje vždy, když klient pokus obnovuje žádost, vám umožní sledovat, jak často klient zaznamená opakovatelná chyby na primární koncový bod.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   V [ **vyhodnotit** ](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) metoda ve vlastní zásady opakování, můžete spustit vlastní kód pokaždé, když probíhá opakování. Kromě zápisu při opakování proběhne, tím také získáte možnost změnit chování opakování.

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

*   Třetí přístup je pro implementaci vlastní monitorovací komponentu ve vaší aplikaci, nepřetržitě testuje příkazem ping váš primární koncový bod úložiště s dummy číst požadavků (jako je čtení malých objektů blob) k určení jeho stavu. Bude to trvat až několik zdrojů informací, ale ne značné množství. Při zjištění problému, která dosáhne vaše mezní hodnota by pak provést přepínač tak, aby **SecondaryOnly** a režimu jen pro čtení.

V určitém okamžiku se chcete přepnout zpět na primární koncový bod pomocí a umožňuje aktualizace. Pokud používáte jednu z prvních dvou metod uvedených výše, může jednoduše přepnete zpět do primárního koncového bodu a povolit režim aktualizace po provedení libovolně vybrané množství času nebo počet operací. Pak můžete nechat ji znovu projít logika opakovaných pokusů. Pokud problém byl vyřešen, bude pokračovat primárního koncového bodu a povolit aktualizace. Pokud problém přetrvává, ho ještě jednou přepne zpět do sekundárního koncového bodu a režimu jen pro čtení po selhání kritéria, která jste nastavili.

Třetí scénář, při použití příkazu ping primární koncový bod úložiště opět úspěšné, můžete aktivovat přepínač zpět **PrimaryOnly** a pokračovat v povolení aktualizací.

## <a name="handling-eventually-consistent-data"></a>Konzistentní zpracování dat

Geograficky redundantní účet úložiště jen pro čtení funguje tak, že replikuje transakce z primární oblasti do sekundární. Tento proces replikace zaručuje, že data v sekundární oblasti jsou *konzistentní*. To znamená, že všechny transakce v primární oblasti se server zobrazí v sekundární oblasti, ale, že možná s menší prodlevou před zobrazením a, že neexistuje žádná záruka transakce jsou doručeny do sekundární oblasti ve stejném pořadí, ve kterém jsou původně byly použity v primární oblasti. Pokud vaše transakce jsou doručeny do sekundární oblasti mimo pořadí, můžete *může* vezměte v úvahu vaše data v sekundární oblasti, kterou chcete být v nekonzistentním stavu, dokud služba zachytává.

Následující tabulka znázorňuje příklad co může nastat při aktualizaci podrobnosti zaměstnance, aby jí členem *správci* role. Pro účely tohoto příkladu, k tomu je potřeba je aktualizovat **zaměstnance** entity a aktualizovat **role správce** entity s počtem celkový počet správců. Všimněte si, jak aktualizace jsou použity mimo pořadí v sekundární oblasti.

| **čas** | **Transakce**                                            | **Replikace**                       | **Čas poslední synchronizace** | **výsledek** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakce A: <br> Vložit zaměstnance <br> entity ve primárního |                                   |                    | Transakce A vložit do primární,<br> nejsou ještě nereplikovaly. |
| T1       |                                                            | Transakce A <br> replikují do<br> sekundární | T1 | Transakce A replikují do sekundární. <br>Čas poslední synchronizace aktualizovat.    |
| T2       | Transakce B:<br>Aktualizace<br> Zaměstnanec entity<br> v primární  |                                | T1                 | Transakci zapsán do primární, B<br> nejsou ještě nereplikovaly.  |
| T3       | Transakce C:<br> Aktualizace <br>Správce<br>Entita role v<br>primární |                    | T1                 | Transakci zapsán do primární, C<br> nejsou ještě nereplikovaly.  |
| *T4*     |                                                       | Transakce C <br>replikují do<br> sekundární | T1         | Transakce C replikují do sekundární.<br>Nelze aktualizovat, protože LastSyncTime <br>ještě nebyla replikována transakce B.|
| *T5*     | Ke čtení entit <br>ze sekundární                           |                                  | T1                 | Získat hodnotu zastaralé pro zaměstnance <br> entity vzhledem k tomu, že nebyla transakce B <br> ještě nereplikovaly. Získat novou hodnotu<br> Entita role správce vzhledem k tomu, že má C<br> replikovat. Stále ještě čas poslední synchronizace<br> byla aktualizována, protože transakce B<br> nebyl replikován. Poznáte,<br>Entita role správce je nekonzistentní <br>protože entity data a času je po <br>Čas poslední synchronizace. |
| *T6*     |                                                      | Transakce B<br> replikují do<br> sekundární | T6                 | *T6* – mít všechny transakce pomocí jazyka C <br>se replikují, čas poslední synchronizace<br> se aktualizuje. |

V tomto příkladu se předpokládá, že klient přepíná na čtení ze sekundární oblasti v T5. Můžou číst **role správce** entity v tuto chvíli entity však obsahuje hodnotu pro počet správců, která není konzistentní s počtem **zaměstnance** entity, které jsou v tuto chvíli je označena jako správci v sekundární oblasti. Váš klient může jednoduše zobrazit tuto hodnotu, se riziko, že je nekonzistentní informace. Alternativně může pokusit určit, který klient **role správce** je ve stavu potenciálně konzistentní vzhledem k tomu dojít mimo pořadí aktualizací a potom informovat uživatele o této skutečnosti.

Rozpoznat, že má potenciálně nekonzistentní data, může klient použít hodnotu *čas poslední synchronizace* , můžete kdykoli získat dotazováním služby úložiště. Znamená to čas, kdy byla poslední data v sekundární oblasti konzistentní vzhledem k aplikacím a pokud služba použili všechny transakce před tento bod v čase. V příkladu výše, po vloží službu **zaměstnance** entity v sekundární oblasti, čas poslední synchronizace je nastavena na *T1*. Zůstane v *T1* až do aktualizace služby **zaměstnance** entity v sekundární oblasti, pokud je nastavena na *T6*. Pokud klient získá čas poslední synchronizace, když načte entity na *T5*, ho můžete porovnat s časovým razítkem v entitě. Pokud časové razítko u entity je pozdější než čas poslední synchronizace, pak tato entita je v potenciálně nekonzistentním stavu a můžete provést cokoli, co je vhodnou pro vaši aplikaci. Použití tohoto pole vyžaduje vědět, kdy byla dokončena poslední aktualizace na primární.

## <a name="testing"></a>Testování

Je důležité, otestovat, že vaše aplikace chová podle očekávání, pokud se setká s opakovatelnou chyby. Například je potřeba testovat, že aplikace přepínače na sekundární a do režimu jen pro čtení, když zjistí problém a přepne zpět při primární oblast opět k dispozici. K tomuto účelu, budete potřebovat způsob, jak simulovat opakovatelná chyby a řízení, jak často k nim dojde.

Můžete použít [Fiddler](http://www.telerik.com/fiddler) zachytí a upravit odpovědi protokolu HTTP ve skriptu. Tento skript můžete identifikovat odpovědi, které pocházejí z primárního koncového bodu a změnit stavový kód protokolu HTTP, který rozpozná klientskou knihovnu pro úložiště jako Opakovatelná chyba. Tento fragment kódu ukazuje jednoduchý příklad Fiddleru skript, který zachycuje odpovědí na požadavky na čtení **employeedata** tabulky vrátit 502 stav:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Můžete rozšířit a zachytit používání nástroje většímu počtu požadavků a změnit pouze v tomto příkladu **responseCode** na některé z nich pro lepší simulaci reálné scénáře. Další informace o přizpůsobení Fiddleru skriptů, najdete v části [změna požadavku nebo odpovědi](http://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) v dokumentaci k aplikaci Fiddler.

Pokud jste provedli prahové hodnoty pro přepnutí do režimu jen pro čtení konfigurovat aplikace, ji bude snazší testování chování se svazky-li se o neprodukční transakce.

## <a name="next-steps"></a>Další kroky

* Další informace o oprávnění ke čtení geografickou redundanci, včetně další příklad, jak je nastavit LastSyncTime, najdete v tématu [možnosti redundance úložiště Windows Azure a geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Kompletní příklad ukazuje, jak přepínat mezi primárními a sekundárními koncových bodů a ujistěte se, najdete v tématu [ukázky pro Azure – s použitím vzoru Circuit Breaker s využitím úložiště RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
