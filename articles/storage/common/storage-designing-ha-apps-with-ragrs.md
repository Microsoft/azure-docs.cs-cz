---
title: Navrhujte vysoce dostupné aplikace pomocí geograficky redundantního úložiště
titleSuffix: Azure Storage
description: Naučte se používat geograficky redundantní úložiště pro čtení k architektovi vysoce dostupné aplikace, která je dostatečně flexibilní pro zpracování výpadků.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157088"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Navrhování vysoce dostupných aplikací pomocí geograficky redundantního úložiště pro přístup ke čtení

Běžnou funkcí cloudových infrastruktur, jako je Azure Storage, je to, že poskytují vysoce dostupnou platformu pro hostování aplikací. Vývojáři cloudových aplikací musí pečlivě zvážit, jak tuto platformu využít k poskytování vysoce dostupných aplikací svým uživatelům. Tento článek se zaměřuje na to, jak můžou vývojáři použít jednu z geograficky redundantních možností replikace Azure, aby zajistili, že jejich aplikace Azure Storage jsou vysoce dostupné.

Účty úložiště nakonfigurované pro geograficky redundantní replikaci jsou synchronně replikovány v primární oblasti a pak asynchronně replikovány do sekundární oblasti, která je stovky mil daleko. Azure Storage nabízí dva typy geograficky redundantní replikace:

* [Geograficky redundantní úložiště (GZRS) (preview)](storage-redundancy.md) poskytuje replikaci pro scénáře, které vyžadují vysokou dostupnost a maximální odolnost. Data se synchronně replikují ve třech zónách dostupnosti Azure v primární oblasti pomocí zónově redundantního úložiště (ZRS) a pak se asynchronně replikuje do sekundární oblasti. Pro přístup ke čtení dat v sekundární oblasti povolte geograficky redundantní úložiště pro čtení geograficky zónově redundantní (RA-GZRS).
* [Geograficky redundantní úložiště (GRS)](storage-redundancy.md) poskytuje meziregionální replikaci k ochraně před regionálními výpadky. Data jsou replikována synchronně třikrát v primární oblasti pomocí místně redundantního úložiště (LRS) a pak replikována asynchronně do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte geograficky redundantní úložiště pro čtení (RA-GRS).

Tento článek ukazuje, jak navrhnout aplikaci pro zpracování výpadku v primární oblasti. Pokud primární oblast přestane být k dispozici, aplikace se může přizpůsobit k provádění operací čtení proti sekundární oblasti místo. Než začnete, ujistěte se, že je váš účet úložiště nakonfigurovaný pro RA-GRS nebo RA-GZRS.

Informace o tom, které primární oblasti jsou spárovány s sekundární mise, najdete [v tématu kontinuita podnikání a zotavení po havárii (BCDR): Azure spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

V tomto článku jsou zahrnuty fragmenty kódu a odkaz na úplnou ukázku na konci, kterou můžete stáhnout a spustit.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Aspekty návrhu aplikace při čtení ze sekundárního

Účelem tohoto článku je ukázat, jak navrhnout aplikaci, která bude i nadále fungovat (i když v omezené kapacitě) i v případě závažné havárie v primárním datovém centru. Aplikaci můžete navrhnout tak, aby zpracovávala přechodné nebo dlouhotrvající problémy čtením ze sekundární oblasti, když dojde k problému, který narušuje čtení z primární oblasti. Pokud primární oblast je opět k dispozici, aplikace se může vrátit ke čtení z primární oblasti.

Mějte na paměti tyto klíčové body při navrhování aplikace pro RA-GRS nebo RA-GZRS:

* Azure Storage udržuje kopii dat, která ukládáte jen pro čtení ve vaší primární oblasti v sekundární oblasti. Jak je uvedeno výše, služba úložiště určuje umístění sekundární oblasti.

* Kopie jen pro čtení je [nakonec konzistentní](https://en.wikipedia.org/wiki/Eventual_consistency) s daty v primární oblasti.

* U objektů BLOB, tabulek a front můžete zadat dotaz na sekundární oblast pro hodnotu *Poslední čas synchronizace,* která informuje o tom, kdy došlo k poslední replikaci z primární do sekundární oblasti. (To není podporováno pro soubory Azure, které v současné době nemají redundanci RA-GRS.)

* Klientská knihovna úložiště můžete použít ke čtení a zápisu dat v primární nebo sekundární oblasti. Požadavky na čtení můžete také automaticky přesměrovat do sekundární oblasti, pokud časový mat ný to pro čtení primární oblasti.

* Pokud primární oblast přestane být k dispozici, můžete zahájit převzetí služeb při selhání účtu. Při převzetí služeb při selhání do sekundární oblasti se položky DNS ukazující na primární oblast změní tak, aby přecšly na sekundární oblast. Po dokončení převzetí služeb při selhání je obnoven přístup pro zápis pro účty GRS a RA-GRS. Další informace najdete v [tématu Převzetí služeb při selhání účtu zotavení po havárii a úložiště (preview) ve službě Azure Storage](storage-disaster-recovery-guidance.md).

> [!NOTE]
> Převzetí služeb při selhání účtu spravovaného zákazníkem (preview) ještě není k dispozici v oblastech podporujících GZRS/RA-GZRS, takže zákazníci nemohou aktuálně spravovat události převzetí služeb při selhání účtu s účty GZRS a RA-GZRS. Během předběžné verze bude společnost Microsoft spravovat všechny události převzetí služeb při selhání ovlivňující účty GZRS/RA-GZRS.

### <a name="using-eventually-consistent-data"></a>Použití nakonec konzistentních dat

Navrhované řešení předpokládá, že je přijatelné vrátit potenciálně zastaralá data volající aplikace. Vzhledem k tomu, že data v sekundární oblasti je nakonec konzistentní, je možné, že primární oblast může být nepřístupná před dokončením replikace aktualizace sekundární oblasti.

Předpokládejme například, že zákazník odešle aktualizaci úspěšně, ale primární oblast selže před rozšířením aktualizace do sekundární oblasti. Když zákazník požádá o zpětné čtení dat, obdrží zastaralá data ze sekundární oblasti namísto aktualizovaných dat. Při navrhování aplikace se musíte rozhodnout, zda je to přijatelné, a pokud ano, jak budete zákazníkovi shlášením. 

Dále v tomto článku ukážeme, jak zkontrolovat čas poslední synchronizace pro sekundární data a zkontrolovat, zda je sekundární aktuální.

### <a name="handling-services-separately-or-all-together"></a>Manipulace se službami samostatně nebo společně

I když je nepravděpodobné, je možné, že jedna služba přestane být nedostupná, zatímco ostatní služby jsou stále plně funkční. Můžete zpracovat opakování a režim jen pro čtení pro každou službu samostatně (objekty BLOB, fronty, tabulky) nebo můžete zpracovávat opakované pokusy obecně pro všechny služby úložiště dohromady.

Například pokud používáte fronty a objekty BLOB v aplikaci, můžete se rozhodnout, že do samostatného kódu pro zpracování opakovatelných chyb pro každou z nich. Pokud pak získáte opakování ze služby objektů blob, ale služba fronty stále funguje, bude ovlivněna pouze část aplikace, která zpracovává objekty BLOB. Pokud se rozhodnete zpracovat všechny pokusy o službu úložiště opakovaně a volání služby objektů blob vrátí opakovatelnou chybu, pak požadavky na službu objektů blob a služby fronty budou ovlivněny.

Nakonec to závisí na složitosti aplikace. Můžete se rozhodnout nezpracovávat chyby podle služby, ale místo toho přesměrovat požadavky na čtení pro všechny služby úložiště do sekundární oblasti a spustit aplikaci v režimu jen pro čtení, když zjistíte problém s jakoukoli službou úložiště v primární oblasti.

### <a name="other-considerations"></a>Další aspekty

Jedná se o další aspekty, které budeme diskutovat ve zbytku tohoto článku.

* Zpracování opakovaných pokusů o čtení pomocí vzoru jističe

* Nakonec konzistentní data a čas poslední synchronizace

* Testování

## <a name="running-your-application-in-read-only-mode"></a>Spuštění aplikace v režimu jen pro čtení

Chcete-li efektivně připravit na výpadek v primární oblasti, musíte být schopni zpracovat jak neúspěšné požadavky na čtení, tak neúspěšné požadavky na aktualizaci (s aktualizací v tomto případě, což znamená vložení, aktualizace a odstranění). Pokud primární oblast selže, požadavky na čtení mohou být přesměrovány do sekundární oblasti. Požadavky na aktualizaci však nelze přesměrovat na sekundární, protože sekundární je jen pro čtení. Z tohoto důvodu je třeba navrhnout aplikaci pro spuštění v režimu jen pro čtení.

Můžete například nastavit příznak, který je zaškrtnutý před odesláním všech požadavků na aktualizaci do služby Azure Storage. Když jeden z požadavků na aktualizaci projde, můžete přeskočit a vrátit odpovídající odpověď zákazníkovi. Můžete dokonce chtít zakázat některé funkce úplně, dokud nebude problém vyřešen a upozornit uživatele, že tyto funkce jsou dočasně nedostupné.

Pokud se rozhodnete zpracovávat chyby pro každou službu samostatně, budete také muset zpracovat schopnost spouštět aplikaci v režimu jen pro čtení podle služby. Můžete mít například příznaky jen pro čtení pro každou službu, která může být povolena a zakázána. Potom můžete zpracovat příznak na příslušných místech v kódu.

Možnost spouštět aplikaci v režimu jen pro čtení má další vedlejší výhodu – poskytuje možnost zajistit omezenou funkčnost během hlavního upgradu aplikace. Můžete spustit aplikaci pro spuštění v režimu jen pro čtení a přejděte na sekundární datové centrum, což zajistí, že při provádění upgradů nikdo nepřistupuje k datům v primární oblasti.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Zpracování aktualizací při spuštění v režimu jen pro čtení

Existuje mnoho způsobů, jak zpracovat požadavky na aktualizaci při spuštění v režimu jen pro čtení. Nebudeme pokrývat to komplexně, ale obecně existuje několik vzorů, které považujete.

1. Můžete odpovědět na uživatele a sdělit mu, že v současné době nepřijímáte aktualizace. Systém správy kontaktů může například umožnit zákazníkům přístup ke kontaktním informacím, ale neprovádět aktualizace.

2. Aktualizace můžete zařadit do fronty v jiné oblasti. V takovém případě byste zapisovali čekající požadavky na aktualizaci do fronty v jiné oblasti a poté byste měli způsob, jak tyto požadavky zpracovat po znovu přepnut primární datové centrum do režimu online. V tomto scénáři byste měli dát zákazníkovi vědět, že požadovaná aktualizace je zařazena do fronty pro pozdější zpracování.

3. Aktualizace můžete zapsat do účtu úložiště v jiné oblasti. Když se primární datové centrum vrátí do režimu online, můžete mít způsob, jak tyto aktualizace sloučit do primárních dat, v závislosti na struktuře dat. Pokud například vytváříte samostatné soubory s datem a časovým razítkem v názvu, můžete tyto soubory zkopírovat zpět do primární oblasti. To funguje pro některé úlohy, jako je například protokolování a iOT data.

## <a name="handling-retries"></a>Zpracování opakování

Klientská knihovna Azure Storage vám pomůže určit, které chyby se můžou opakovat. Například chyba 404 (prostředek nebyl nalezen) nebude opakována, protože opakování není pravděpodobné, že povede k úspěchu. Na druhou stranu 500 chyba může být zopakována, protože se jedná o chybu serveru a problém může být jednoduše přechodný problém. Další podrobnosti najdete v [otevřeném zdrojovém kódu třídy ExponencialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) v klientské knihovně úložiště .NET. (Vyhledejte metodu ShouldRetry.)

### <a name="read-requests"></a>Číst požadavky

Požadavky na čtení lze přesměrovat do sekundárního úložiště, pokud dojde k potížím s primárním úložištěm. Jak je uvedeno výše v [použití nakonec konzistentní data](#using-eventually-consistent-data), musí být přijatelné pro vaši aplikaci potenciálně číst zastaralá data. Pokud používáte knihovnu klienta úložiště pro přístup k datům ze sekundární, můžete určit chování opakování požadavku na čtení nastavením hodnoty vlastnosti **LocationMode** na jednu z následujících možností:

* **PrimaryOnly** (výchozí nastavení)

* **PrimárníThenSekundární**

* **Pouze sekundární**

* **SekundárníThenPrimární**

Když nastavíte **LocationMode** na **PrimaryThenSecondary**, pokud počáteční požadavek na čtení primární koncový bod selže s chybou, která může být opakována, klient automaticky provede další požadavek na čtení do sekundárního koncového bodu. Pokud je chyba časový limit serveru, pak klient bude muset počkat na vypršení časového limitu, než obdrží opakovatelnou chybu ze služby.

Existují v podstatě dva scénáře, které je třeba zvážit při rozhodování o tom, jak reagovat na opakovatelnou chybu:

* Jedná se o izolovaný problém a následné požadavky na primární koncový bod nevrátí opakovatelné chyby. Příkladem, kde k tomu může dojít, je v případě, že dojde k přechodné chybě sítě.

    V tomto scénáři neexistuje žádná významná snížení výkonu v tom, že **LocationMode** nastavena na **PrimaryThenSecondary,** protože k tomu dochází pouze zřídka.

* Jedná se o problém s alespoň jednu ze služeb úložiště v primární oblasti a všechny následné požadavky na tuto službu v primární oblasti pravděpodobně vrátí opakovatelné chyby po určitou dobu. Příkladem je, pokud primární oblast je zcela nepřístupné.

    V tomto scénáři je snížení výkonu, protože všechny vaše požadavky na čtení se pokusí primární koncový bod jako první, počkejte na vypršení časového limitu a pak přepnout na sekundární koncový bod.

Pro tyto scénáře byste měli určit, že je pokračující problém s primární koncový bod a odeslat všechny požadavky na čtení přímo do sekundárního koncového bodu nastavením **LocationMode** vlastnost **SecondaryOnly**. V tomto okamžiku byste měli také změnit aplikaci tak, aby byla spuštěna v režimu jen pro čtení. Tento přístup se nazývá [vzor jističe](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Aktualizovat požadavky

Vzor jističe lze také použít pro požadavky na aktualizaci. Požadavky na aktualizaci však nelze přesměrovat do sekundárního úložiště, které je jen pro čtení. Pro tyto požadavky byste měli ponechat Vlastnost **LocationMode** nastavenou na **PrimaryOnly** (výchozí). Chcete-li zpracovat tyto chyby, můžete použít metriku pro tyto požadavky – například 10 chyb v řadě – a když je splněna prahová hodnota, přepněte aplikaci do režimu jen pro čtení. Můžete použít stejné metody pro návrat do režimu aktualizace jako ty, které jsou popsány níže v další části o jistič vzor.

## <a name="circuit-breaker-pattern"></a>Model Jistič (Circuit Breaker)

Pomocí jistič vzor ve vaší aplikaci můžete zabránit opakování operace, která je pravděpodobně nezdaří opakovaně. Umožňuje aplikaci pokračovat v běhu, nikoli zabírají čas, zatímco operace je opakována exponenciálně. Také zjistí, kdy byla závada opravena, kdy aplikace může operaci zkusit znovu.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Jak implementovat schéma jističe

Chcete-li zjistit, že je pokračující problém s primární koncový bod, můžete sledovat, jak často klient narazí opakovatelné chyby. Vzhledem k tomu, že každý případ je jiný, musíte se rozhodnout pro prahovou hodnotu, kterou chcete použít pro rozhodnutí přepnout na sekundární koncový bod a spustit aplikaci v režimu jen pro čtení. Můžete se například rozhodnout provést přepínač, pokud existuje 10 selhání v řadě bez úspěchu. Dalším příkladem je přepnout, pokud 90 % požadavků v období 2 minuty nezdaří.

Pro první scénář můžete jednoduše zachovat počet selhání a pokud je úspěch před dosažením maxima, nastavte počet zpět na nulu. Pro druhý scénář jedním ze způsobů, jak ji implementovat, je použití objektu MemoryCache (v rozhraní .NET). Pro každý požadavek přidejte cacheItem do mezipaměti, nastavte hodnotu na úspěch (1) nebo selhání (0) a nastavte dobu vypršení platnosti na 2 minuty od této chvíle (nebo bez ohledu na časové omezení). Po dosažení doby vypršení platnosti položky je položka automaticky odebrána. To vám dá válcování 2-minutové okno. Pokaždé, když provedete požadavek na službu úložiště, nejprve použijete dotaz Linq přes objekt MemoryCache k výpočtu procenta úspěchu sečtením hodnot a dělením počtem. Když procento úspěch klesne pod určitou prahovou hodnotu (například 10%), nastavte **LocationMode** vlastnost pro čtení požadavků **secondaryOnly** a přepnout aplikaci do režimu jen pro čtení před pokračováním.

Prahová hodnota chyb, která se používá k určení, kdy provést přepínač se může lišit od služby ke službě v aplikaci, takže byste měli zvážit jejich konfigurovatelné parametry. Toto je také místo, kde se rozhodnete zpracovat opakovatelné chyby z každé služby samostatně nebo jako jeden, jak bylo popsáno dříve.

Dalším aspektem je, jak zpracovat více instancí aplikace a co dělat, když zjistíte opakovatelné chyby v každé instanci. Například můžete mít 20 virtuálních aplikací spuštěných se stejnou aplikací načtenou. Zpracováváte každou instanci zvlášť? Pokud jedna instance začne mít problémy, chcete omezit odpověď pouze na jednu instanci, nebo chcete zkusit, aby všechny instance reagovaly stejným způsobem, když má jedna instance problém? Zpracování instancí samostatně je mnohem jednodušší než pokus o koordinaci odpovědi napříč nimi, ale jak to provést závisí na architektuře vaší aplikace.

### <a name="options-for-monitoring-the-error-frequency"></a>Možnosti sledování četnosti chyb

Máte tři hlavní možnosti pro sledování frekvence opakování v primární oblasti, abyste zjistili, kdy přejít do sekundární oblasti a změnit aplikaci tak, aby byla spuštěna v režimu jen pro čtení.

* Přidejte obslužnou [**rutinu**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) pro opakování události na [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) objektu, který předáte vaše požadavky na úložiště – to je metoda zobrazená v tomto článku a použita v doprovodné ukázce. Tyto události požáru vždy, když klient opakuje požadavek, což umožňuje sledovat, jak často klient narazí opakovatelné chyby na primární koncový bod.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* V [**vyhodnotit**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) metoda ve vlastní zásady opakování můžete spustit vlastní kód vždy, když dojde k opakování. Kromě nahrávání při opakování, to také dává možnost změnit chování opakování.

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

* Třetím přístupem je implementace vlastní součásti monitorování ve vaší aplikaci, která průběžně ping váš koncový bod primárního úložiště s fiktivní požadavky na čtení (například čtení malý objekt blob) k určení jeho stavu. To by zabralo některé zdroje, ale ne významnou částku. Pokud je zjištěn problém, který dosáhne prahové hodnoty, provedete přepínač do **režimu SecondaryOnly** a jen pro čtení.

V určitém okamžiku budete chtít přepnout zpět na použití primárního koncového bodu a povolení aktualizací. Pokud používáte jednu z prvních dvou výše uvedených metod, můžete jednoduše přepnout zpět na primární koncový bod a povolit režim aktualizace po libovolně vybrané množství času nebo počet operací byla provedena. Potom jej můžete nechat znovu projít logikou opakování. Pokud byl problém opraven, bude nadále používat primární koncový bod a povolit aktualizace. Pokud stále existuje problém, přepne se po selhání nastavených kritérií zpět do sekundárního koncového bodu a režimu jen pro čtení.

Pro třetí scénář při ping koncového bodu primárního úložiště se stane úspěšný znovu, můžete aktivovat přepínač zpět na **PrimaryOnly** a pokračovat v povolení aktualizací.

## <a name="handling-eventually-consistent-data"></a>Zpracování konzistentních dat

Geograficky redundantní úložiště funguje replikací transakcí z primární do sekundární oblasti. Tento proces replikace zaručuje, že data v sekundární oblasti je *nakonec konzistentní*. To znamená, že všechny transakce v primární oblasti se nakonec objeví v sekundární oblasti, ale že může dojít k prodlevě před jejich zobrazením a že neexistuje žádná záruka, že transakce dorazí do sekundární oblasti ve stejném pořadí, v jakém jsou byly původně použity v primárním regionu. Pokud vaše transakce dorazí v sekundární oblasti mimo pořadí, *můžete* zvážit data v sekundární oblasti, které mají být v nekonzistentním stavu, dokud služba dožene.

V následující tabulce je uveden příklad toho, co se může stát, když aktualizujete podrobnosti o zaměstnanci tak, aby byl členem role *správců.* V tomto příkladu to vyžaduje aktualizaci entity **zaměstnance** a aktualizaci entity **role správce** s celkovým počtem správců. Všimněte si, jak jsou aktualizace použity mimo pořadí v sekundární oblasti.

| **Čas** | **Transakce**                                            | **Replikace**                       | **Čas poslední synchronizace** | **Výsledek** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakce A: <br> Vložit zaměstnance <br> účetní jednotka v primárním |                                   |                    | Transakce A vložená do primárního,<br> dosud nereplikovány. |
| T1       |                                                            | Transakce A <br> replikovány do<br> Sekundární | T1 | Transakce A replikována na sekundární. <br>Čas poslední synchronizace byl aktualizován.    |
| T2       | Transakce B:<br>Aktualizace<br> zaměstnanecký subjekt<br> v primárním  |                                | T1                 | Transakce B zapsaná na primární,<br> dosud nereplikovány.  |
| T3       | Transakce C:<br> Aktualizace <br>správce<br>role entity v<br>Primární |                    | T1                 | Transakce C zapsaná na primární,<br> dosud nereplikovány.  |
| *T4*     |                                                       | Transakce C <br>replikovány do<br> Sekundární | T1         | Transakce C replikována na sekundární.<br>LastSyncTime není aktualizován, protože <br>transakce B ještě nebyla replikována.|
| *T5*     | Číst entity <br>od sekundárních                           |                                  | T1                 | Získáte zatuchlou hodnotu pro zaměstnance <br> účetní jednotku, protože transakce B <br> ještě replikovány. Získáte novou hodnotu pro<br> entita role správce, protože C má<br> Replikované. Čas poslední synchronizace stále ještě nebyl<br> byla aktualizována, protože transakce B<br> nereplikoval. Můžete říct,<br>Entita role správce je nekonzistentní. <br>protože datum a čas účetní jednotky je po <br>poslední čas synchronizace. |
| *T6*     |                                                      | Transakce B<br> replikovány do<br> Sekundární | T6                 | *T6* – Všechny transakce prostřednictvím C mají <br>byla replikována, čas poslední synchronizace<br> je aktualizován. |

V tomto příkladu předpokládejme, že klient přepne na čtení ze sekundární oblasti na T5. V tuto chvíli může úspěšně číst entitu **role správce,** ale entita obsahuje hodnotu pro počet správců, která není v souladu s počtem entit **zaměstnanců,** které jsou v současné době označeny jako správci v sekundární oblasti. Váš klient by mohl jednoduše zobrazit tuto hodnotu s rizikem, že se jedná o nekonzistentní informace. Alternativně by se klient mohl pokusit určit, že **role správce** je v potenciálně nekonzistentním stavu, protože aktualizace proběhly mimo pořadí, a poté informovat uživatele o této skutečnosti.

Chcete-li rozpoznat, že má potenciálně nekonzistentní data, klient můžete použít hodnotu *poslední ho času synchronizace,* který můžete získat kdykoli dotazem na službu úložiště. To vám řekne čas, kdy data v sekundární oblasti byla naposledy konzistentní a kdy služba použila všechny transakce před tímto bodem v čase. Ve výše uvedeném příkladu je po vložení **entity zaměstnance** do sekundární oblasti čas poslední synchronizace nastaven na *T1*. Zůstane na *T1,* dokud služba neaktualizuje entitu **zaměstnance** v sekundární oblasti, když je nastavena na *T6*. Pokud klient načte čas poslední synchronizace při čtení entity na *T5*, může porovnat s časovým razítkem na entitě. Pokud je časové razítko entity pozdější než poslední čas synchronizace, pak entita je v potenciálně nekonzistentním stavu a můžete provést jakoukoli příslušnou akci pro vaši aplikaci. Použití tohoto pole vyžaduje, abyste věděli, kdy byla dokončena poslední aktualizace primární.

Informace o kontrole času poslední synchronizace najdete [v tématu Kontrola vlastnosti Poslední čas synchronizace pro účet úložiště](last-sync-time-get.md).

## <a name="testing"></a>Testování

Je důležité otestovat, že vaše aplikace se chová podle očekávání, když narazí na opakované chyby. Například je třeba otestovat, že aplikace přepne do sekundárního a do režimu jen pro čtení, když zjistí problém a přepne zpět, když primární oblast bude k dispozici znovu. Chcete-li to provést, potřebujete způsob, jak simulovat opakovatelné chyby a řídit, jak často k nim dochází.

[Fiddler](https://www.telerik.com/fiddler) můžete použít k zachycení a úpravě odpovědí HTTP ve skriptu. Tento skript může identifikovat odpovědi, které pocházejí z primárního koncového bodu, a změnit stavový kód HTTP na ten, který klientská knihovna úložiště rozpozná jako opakovatelnou chybu. Tento fragment kódu ukazuje jednoduchý příklad fiddler skriptu, který zachycuje odpovědi na čtení požadavků proti tabulka **employeedata** vrátit stav 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Tento příklad můžete rozšířit zachytit širší rozsah požadavků a pouze změnit **responseCode** na některé z nich lépe simulovat scénář reálného světa. Další informace o přizpůsobení fiddlerskriptů naleznete [v tématu Úprava požadavku nebo odpovědi](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) v dokumentaci Fiddler.

Pokud jste provedli prahové hodnoty pro přepnutí aplikace do režimu jen pro čtení konfigurovatelné, bude snazší otestovat chování s objemy transakcí mimo produkční prostředí.

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak číst ze sekundární oblasti, včetně dalšího příkladu nastavení vlastnosti Poslední doba synchronizace, naleznete v [tématu Možnosti redundance úložiště Azure a Geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Úplný ukázku ukazuje, jak provést přepínač tam a zpět mezi primární a sekundární koncové body, najdete [v tématu Ukázky Azure – použití jistič vzor s RA-GRS úložiště](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
