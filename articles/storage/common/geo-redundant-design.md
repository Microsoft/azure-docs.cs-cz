---
title: Použití geografické redundance k návrhu vysoce dostupných aplikací
titleSuffix: Azure Storage
description: Naučte se používat geograficky redundantní úložiště k navržení vysoce dostupné aplikace, která je dostatečně flexibilní, aby mohla zvládnout výpadky.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c16f8233a2800025a8c6f601e236b86d2fd044fd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480679"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>Použití geografické redundance k návrhu vysoce dostupných aplikací

Běžnou funkcí cloudových infrastruktur, jako je Azure Storage, je, že poskytují vysoce dostupnou a odolnou platformu pro hostování dat a aplikací. Vývojáři cloudových aplikací musí pečlivě zvážit, jak tuto platformu využít k maximalizaci výhod jejich uživatelů. Azure Storage nabízí geograficky redundantní úložiště, které zajistí vysokou dostupnost i v případě regionu výpadku. Účty úložiště nakonfigurované pro geograficky redundantní replikaci se synchronně replikují v primární oblasti a pak se asynchronně replikují do sekundární oblasti, která je od sebe stovky kilometrů.

Azure Storage nabízí dvě možnosti pro geograficky redundantní replikaci. Jediným rozdílem mezi těmito dvěma možnostmi je způsob, jakým se data replikují v primární oblasti:

* [Geograficky redundantní úložiště (GZRS)](storage-redundancy.md): data se replikují synchronně v rámci tří zón dostupnosti Azure v primární oblasti s využitím *redundantního úložiště (ZRS) pro zónu*, potom se asynchronně replikují do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte přístup pro čtení Geo-Zone-redundantní úložiště (RA-GZRS).

    Microsoft doporučuje používat GZRS/RA-GZRS pro scénáře, které vyžadují maximální dostupnost a odolnost.

* [Geograficky redundantní úložiště (GRS)](storage-redundancy.md): data se replikují synchronně třikrát v primární oblasti pomocí *místně redundantního úložiště (LRS)* a pak se asynchronně replikují do sekundární oblasti. Pro přístup pro čtení dat v sekundární oblasti povolte geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).

V tomto článku se dozvíte, jak navrhnout aplikaci pro zpracování výpadku v primární oblasti. Pokud primární oblast nebude k dispozici, může být aplikace přizpůsobena provádění operací čtení v sekundární oblasti. Než začnete, ujistěte se, že je váš účet úložiště nakonfigurovaný pro RA-GRS nebo RA-GZRS.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Požadavky na návrh aplikace při čtení ze sekundárního

Účelem tohoto článku je Ukázat, jak navrhnout aplikaci, která bude i nadále fungovat (i když v omezené kapacitě) i v případě závažné havárie v primárním datovém centru. Aplikaci můžete navrhnout tak, aby zpracovávala přechodné nebo dlouhodobé problémy čtením ze sekundární oblasti, když dojde k problému, který je v konfliktu se čtením z primární oblasti. Když je primární oblast opět k dispozici, vaše aplikace se může vrátit ke čtení z primární oblasti.

Při návrhu aplikace pro RA-GRS nebo RA-GZRS mějte na paměti tyto klíčové body:

* Azure Storage uchovává kopii dat, která ukládáte do primární oblasti v sekundární oblasti, jen pro čtení. Jak je uvedeno výše, služba úložiště Určuje umístění sekundární oblasti.

* Kopie, která je jen pro čtení, je [nakonec konzistentní](https://en.wikipedia.org/wiki/Eventual_consistency) s daty v primární oblasti.

* U objektů blob, tabulek a front můžete zadat dotaz na sekundární oblast pro hodnotu *čas poslední synchronizace* , která oznamuje, že došlo k poslední replikaci z primární do sekundární oblasti. (To není podporováno u souborů Azure, které v tuto chvíli nemají redundanci RA-GRS.)

* Pomocí klientské knihovny pro úložiště můžete číst a zapisovat data buď v primární nebo sekundární oblasti. Pokud vyprší časový limit žádosti o čtení do primární oblasti, můžete také automaticky přesměrovat požadavky na čtení do sekundární oblasti.

* Pokud primární oblast nebude k dispozici, můžete iniciovat převzetí služeb při selhání účtu. Při převzetí služeb při selhání do sekundární oblasti se položky DNS odkazující na primární oblast změní tak, aby odkazovaly na sekundární oblast. Po dokončení převzetí služeb při selhání bude pro účty GRS a RA-GRS obnovený přístup pro zápis. Další informace najdete v tématu [převzetí služeb při selhání při zotavení po havárii a účtu úložiště](storage-disaster-recovery-guidance.md).

### <a name="using-eventually-consistent-data"></a>Použití nakonec konzistentních dat

Navrhované řešení předpokládá, že je přijatelné vracet potenciálně zastaralá data do volající aplikace. Vzhledem k tomu, že data v sekundární oblasti jsou nakonec konzistentní, je možné, že primární oblast nebude přístupná, než se dokončí aktualizace sekundární oblasti.

Předpokládejme například, že zákazník úspěšně odeslal aktualizaci, ale primární region se nezdaří před tím, než se aktualizace rozšíří do sekundární oblasti. Když zákazník požádá o čtení dat zpět, obdrží ze sekundární oblasti místo aktualizovaných dat zastaralá data. Při návrhu aplikace se musíte rozhodnout, jestli je to přijatelné, a pokud ano, jak se bude zákazník považovat za zprávu. 

Později v tomto článku ukážeme, jak kontrolovat čas poslední synchronizace pro sekundární data a ověřit, jestli je sekundární je aktuální.

### <a name="handling-services-separately-or-all-together"></a>Zpracování služeb samostatně nebo dohromady

V nepravděpodobném případě je možné, že jedna služba nebude k dispozici, zatímco ostatní služby jsou pořád plně funkční. Můžete zpracovávat opakované pokusy a režim jen pro čtení pro každou službu samostatně (objekty blob, fronty, tabulky) nebo můžete pokusy pokaždé pro všechny služby úložiště společně zpracovávat současně.

Pokud například používáte ve své aplikaci fronty a objekty blob, můžete se rozhodnout do samostatného kódu pro zpracování opakovaných chyb pro každé z nich. Pokud se pak znovu pokusíte ze služby BLOB Service, ale i nadále funguje, bude to mít vliv jenom na součást aplikace, která zpracovává objekty blob. Pokud se rozhodnete všechny opakované pokusy o službu úložiště zpracovat obecně a volání služby BLOB Service vrátí chybu s opakováním, bude to mít vliv na požadavky na službu BLOB Service i na službu front.

Nakonec závisí na složitosti vaší aplikace. Můžete se rozhodnout Nezpracovávat selhání podle služby, ale místo toho přesměrovat požadavky na čtení pro všechny služby úložiště do sekundární oblasti a spustit aplikaci v režimu jen pro čtení při detekci problému se službou úložiště v primární oblasti.

### <a name="other-considerations"></a>Další důležité informace

Toto jsou další okolnosti, které budeme projednávat ve zbývající části tohoto článku.

* Zpracování opakovaných pokusů žádostí o čtení pomocí vzoru pro přerušení okruhu

* Nakonec konzistentní data a čas poslední synchronizace

* Testování

## <a name="running-your-application-in-read-only-mode"></a>Spuštění aplikace v režimu jen pro čtení

Aby bylo možné efektivně připravovat výpadek v primární oblasti, je nutné, abyste mohli zpracovávat žádosti o neúspěšné čtení i neúspěšné žádosti o aktualizaci (s aktualizací v tomto případě to znamená vložení, aktualizace a odstranění). Pokud dojde k chybě primární oblasti, žádosti o čtení je možné přesměrovat do sekundární oblasti. Žádosti o aktualizaci ale nelze přesměrovat na sekundární, protože sekundární je jen pro čtení. Z tohoto důvodu je nutné navrhnout aplikaci tak, aby běžela v režimu jen pro čtení.

Můžete například nastavit příznak, který je zkontrolován před odesláním žádostí o aktualizaci do Azure Storage. Pokud jeden z požadavků na aktualizaci přichází prostřednictvím, můžete ho přeskočit a vrátit se k příslušné reakci na zákazníka. Některé funkce můžete dokonce chtít zcela vypnout, dokud se problém nevyřeší a upozorní uživatele, že tyto funkce jsou dočasně nedostupné.

Pokud se rozhodnete zpracovávat chyby každé služby samostatně, budete také muset zpracovat možnost spuštění aplikace v režimu jen pro čtení podle služby. Například můžete mít příznaky jen pro čtení pro každou službu, kterou lze povolit a zakázat. Pak můžete příznak zpracovat na příslušných místech v kódu.

Možnost spuštění vaší aplikace v režimu jen pro čtení má další výhodu na straně druhé. díky tomu máte možnost zajistit během upgradu hlavní aplikace omezené funkce. Svou aplikaci můžete aktivovat tak, aby běžela v režimu jen pro čtení, odkazovala na sekundární datové centrum, takže nikdo nepřistupuje k datům v primární oblasti, když provádíte upgrade.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Zpracování aktualizací při spuštění v režimu jen pro čtení

Existuje mnoho způsobů, jak zpracovávat žádosti o aktualizaci při spuštění v režimu jen pro čtení. Nebudeme se k tomu zabývat komplexně, ale obecně existuje několik vzorů, které považujete za vhodné.

1. Můžete reagovat na uživatele a sdělit jim, že v současné době nepřijímáte aktualizace. Systém pro správu kontaktů by například mohl zákazníkům umožnit přístup k kontaktním údajům, ale nemůže dělat aktualizace.

2. Aktualizace můžete zařadit do fronty v jiné oblasti. V takovém případě zapíšete čekající žádosti o aktualizaci do fronty v jiné oblasti a potom budete mít možnost tyto požadavky zpracovat po opětovném přepnutí primárního datového centra do online režimu. V tomto scénáři byste měli dát zákazníkovi jistotu, že požadovaná aktualizace je zařazená do fronty pro pozdější zpracování.

3. Aktualizace můžete zapsat do účtu úložiště v jiné oblasti. Až se primární datové centrum vrátí do režimu online, můžete mít možnost tyto aktualizace sloučit do primárních dat v závislosti na struktuře dat. Pokud například vytváříte samostatné soubory s razítkem data a času v názvu, můžete tyto soubory zkopírovat zpátky do primární oblasti. To funguje pro některé úlohy, jako je protokolování a data iOT.

## <a name="handling-retries"></a>Zpracování opakovaných pokusů

Klientská knihovna Azure Storage vám pomůže určit, které chyby se můžou opakovat. Například Chyba 404 (prostředek se nepovedlo najít) se nebude opakovat, protože opakování nebude pravděpodobně mít za následek úspěch. Na druhé straně se může opakovat Chyba 500, protože se jedná o chybu serveru a problém může být jednoduše přechodným problémem. Další podrobnosti najdete v [kódu open source pro třídu ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) v klientské knihovně úložiště .NET. (Vyhledejte metodu ShouldRetry.)

### <a name="read-requests"></a>Žádosti o čtení

Pokud dojde k potížím s primárním úložištěm, můžou se požadavky na čtení přesměrovat do sekundárního úložiště. Jak bylo uvedeno výše v [používání trvalých dat](#using-eventually-consistent-data), musí být přijatelné, aby vaše aplikace mohla číst zastaralá data. Pokud používáte klientskou knihovnu pro úložiště pro přístup k datům ze sekundární verze, můžete určit chování opakování žádosti o čtení nastavením hodnoty vlastnosti **LocationMode** na jednu z následujících možností:

* **PrimaryOnly** (výchozí)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

Pokud nastavíte **LocationMode** na **PrimaryThenSecondary**, pokud počáteční žádost o čtení na primární koncový bod dojde k chybě s chybou, která se dá opakovat, klient automaticky provede další požadavek na čtení sekundárního koncového bodu. Pokud je chyba časovým limitem serveru, bude muset klient počkat na vypršení časového limitu, než obdrží chybu, která bude možné znovu spustit ze služby.

Při rozhodování, jak reagovat na opakovanou chybu, je třeba zvážit v podstatě dva scénáře:

* Jedná se o izolovaný problém a následné požadavky na primární koncový bod nevrátí opakovanou chybu. K tomu může dojít například v případě přechodné chyby sítě.

    V tomto scénáři nedochází k výraznému snížení výkonu ve **LocationMode** nastaveném na **PrimaryThenSecondary** , protože k tomu dochází jenom zřídka.

* Jedná se o problém s aspoň jednou ze služeb úložiště v primární oblasti a všechny následné požadavky na tuto službu v primární oblasti budou nejspíš vracet opakované chyby po určitou dobu. Příkladem je, že primární oblast je zcela nepřístupná.

    V tomto scénáři dochází ke snížení výkonu, protože všechny žádosti o čtení nejprve vyzkouší primární koncový bod, počkejte na vypršení časového limitu a pak přepněte do sekundárního koncového bodu.

V těchto scénářích byste měli zjistit, jestli existuje průběžný problém s primárním koncovým bodem a poslat všechny požadavky na čtení přímo do sekundárního koncového bodu nastavením vlastnosti **LocationMode** na **SecondaryOnly**. V tuto chvíli byste měli aplikaci změnit také tak, aby běžela pouze v režimu jen pro čtení. Tento přístup je známý jako [vzorek přerušení okruhu](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Žádosti o aktualizaci

Pro požadavky na aktualizaci lze také použít vzor pro přerušení okruhu. Žádosti o aktualizaci se ale nedají přesměrovat do sekundárního úložiště, které je jen pro čtení. Pro tyto požadavky byste měli ponechat vlastnost **LocationMode** nastavenou na **PrimaryOnly** (výchozí). Chcete-li tyto chyby zpracovat, můžete použít metriku na tyto požadavky – například 10 chyb na řádku – a když je prahová hodnota splněna, přepněte aplikaci do režimu jen pro čtení. Stejné metody můžete použít pro návrat do režimu aktualizace, jak je popsáno níže v další části o vzoru pro přerušení okruhu.

## <a name="circuit-breaker-pattern"></a>Model Jistič (Circuit Breaker)

Použití vzoru pro přerušení okruhů ve vaší aplikaci může zabránit tomu, aby se znovu pokusilo o operaci, která pravděpodobně selže opakovaně. Umožňuje, aby aplikace pokračovala v běhu, a ne čas, kdy se operace opakuje exponenciálně. Také zjistí, kdy byla chyba opravena, v jakém čase aplikace může operaci opakovat.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Postup implementace vzoru pro přerušení okruhu

Chcete-li zjistit, zda se jedná o průběžný problém s primárním koncovým bodem, můžete sledovat, jak často v klientovi dojde k opakovaným chybám. Vzhledem k tomu, že se každý případ liší, musíte se rozhodnout na prahové hodnotě, kterou chcete použít pro rozhodnutí přepnout na sekundární koncový bod a spustit aplikaci v režimu jen pro čtení. Například se můžete rozhodnout provést přepínač, pokud dojde k 10 selháním v řádku bez úspěchů. Dalším příkladem je přepnutí, pokud selže 90% žádostí v období 2 minut.

V prvním scénáři můžete jednoduše zachovat počet selhání a v případě úspěchu až do maximální doby nastavit počet zpět na nulu. V druhém scénáři je jedním ze způsobů, jak implementovat, je použít objekt MemoryCache (v rozhraní .NET). Pro každý požadavek přidejte do mezipaměti CacheItem., nastavte hodnotu na Success (1) nebo neúspěšná (0) a nastavte čas vypršení platnosti na 2 minuty od tohoto okamžiku (nebo podle toho, co vaše časové omezení je). Po dosažení doby vypršení platnosti položky je položka automaticky odebrána. Tím získáte souhrnné okno se dvěma minutami. Pokaždé, když vytvoříte požadavek na službu úložiště, nejprve použijete dotaz LINQ v rámci objektu MemoryCache k výpočtu procenta úspěšnosti sečtením hodnot a vydělením podle počtu. Pokud procento úspěšnosti klesne pod určitou prahovou hodnotu (například 10%), nastavte vlastnost **LocationMode** pro požadavky na čtení na **SecondaryOnly** a před pokračováním přepněte aplikaci do režimu jen pro čtení.

Prahová hodnota chyb, která se používá k určení, kdy se má přepínač lišit od služby ke službě ve vaší aplikaci, proto byste měli zvážit, že tyto parametry jsou konfigurovatelné. V takovém případě se také rozhodnete, že bude možné zpracovávat opakované chyby z každé služby samostatně nebo jako jednu, jak je popsáno výše.

Dalším aspektem je postup, jak zpracovat více instancí aplikace a co dělat při detekci opakovaných chyb v každé instanci. Můžete mít například 20 virtuálních počítačů spuštěných se stejnou aplikací. Každou instanci zpracujete samostatně? Pokud se u jedné instance spustí problémy, přejete si omezit reakci jenom na jednu instanci, nebo chcete zkusit, aby všechny instance odpovídaly stejným způsobem, když má jedna instance nějaký problém? Zpracovávání instancí samostatně je mnohem jednodušší než pokus o koordinaci odezvy napříč nimi, ale jak to uděláte, záleží na architektuře vaší aplikace.

### <a name="options-for-monitoring-the-error-frequency"></a>Možnosti monitorování četnosti chyb

Máte tři hlavní možnosti monitorování četnosti opakování v primární oblasti, aby bylo možné určit, kdy přepnout do sekundární oblasti a změnit aplikaci tak, aby běžela v režimu jen pro čtení.

* Přidejte obslužnou rutinu pro událost [**opakování**](/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) události na objekt [**OperationContext**](/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) , který předáte vašim požadavkům na úložiště – toto je metoda zobrazená v tomto článku a používaná v doprovodné ukázce. Tyto události se aktivují pokaždé, když klient opakuje požadavek, což vám umožní sledovat, jak často má klient v primárním koncovém bodě narazit opakované chyby.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* V metodě [**Evaluate**](/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) ve vlastních zásadách opakování můžete spustit vlastní kód vždy, když dojde k opakování. Kromě nahrávání, když dojde k opakování, vám to také umožní změnit chování při opakování.

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

* Třetím přístupem je implementace vlastní součásti monitorování ve vaší aplikaci, která nepřetržitě otestuje pomocí testu koncového bodu primárního úložiště (například čtení malého objektu BLOB) a určí jeho stav. Tím se zabere několik prostředků, ale ne značná částka. Po zjištění problému, který dosáhne vaší prahové hodnoty, byste měli přepnout do režimu **SecondaryOnly** a jen pro čtení.

V určitém okamžiku budete chtít přejít zpátky k použití primárního koncového bodu a povolení aktualizací. Pokud používáte jednu z prvních dvou metod uvedených výše, můžete jednoduše přejít zpátky k primárnímu koncovému bodu a povolit režim aktualizace po libovolné době zvolené doby nebo počtu operací. Pak můžete znovu projít logiku opakování. Pokud byl problém vyřešen, bude nadále používat primární koncový bod a povoluje aktualizace. Pokud dojde k problému, bude po neúspěšném nastavování nastavených kritérií přepnut do sekundárního koncového bodu a režimu jen pro čtení.

V případě třetího scénáře se při opakovaném pokusu o ověření platnosti koncového bodu primárního úložiště v případě, že dojde k úspěšnému dokončení aktualizace, můžete aktivovat přepínač zpět na **PrimaryOnly** a pokračovat v povolování aktualizací

## <a name="handling-eventually-consistent-data"></a>Zpracování nakonec konzistentních dat

Geograficky redundantní úložiště funguje replikací transakcí z primární do sekundární oblasti. Tento proces replikace zaručuje, že data v sekundární oblasti jsou *nakonec konzistentní*. To znamená, že se všechny transakce v primární oblasti budou nakonec zobrazovat v sekundární oblasti, ale může dojít k prodlevě, než se objeví, a nezaručujeme, že transakce přicházejí do sekundární oblasti ve stejném pořadí, ve kterém byly původně použité v primární oblasti. Pokud vaše transakce dorazí do sekundární oblasti mimo pořadí, *můžete zvážit, že vaše* data v sekundární oblasti budou v nekonzistentním stavu, dokud se služba nedostane.

Následující tabulka ukazuje příklad toho, co se může stát, když aktualizujete podrobnosti o zaměstnanci, abyste je mohli udělat jako členové role *správců* . V tomto příkladu je potřeba aktualizovat entitu **Zaměstnanec** a aktualizovat entitu **role správce** s počtem celkového počtu správců. Všimněte si, jak se aktualizace aplikují v sekundární oblasti mimo pořadí.

| **Čas** | **Transakce**                                            | **Replikace**                       | **Čas poslední synchronizace** | **Výsledek** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transakce A: <br> Vložit zaměstnance <br> entita v primárním objektu |                                   |                    | Transakce A vložená na primární,<br> ještě není replikované. |
| T1       |                                                            | Transakce A <br> replikováno do<br> sekundární | T1 | Transakce byla replikována do sekundárního. <br>Čas poslední synchronizace se aktualizoval.    |
| T2       | Transakce B:<br>Aktualizace<br> entita zaměstnance<br> v primárním  |                                | T1                 | Transakce B se zapsala do primárního,<br> ještě není replikované.  |
| T3       | Transakce C:<br> Aktualizace <br>správce<br>entita role v<br>primární |                    | T1                 | Transakce C byla zapsána do primárního,<br> ještě není replikované.  |
| *T4*     |                                                       | Transakce C <br>replikováno do<br> sekundární | T1         | Transakce C byla replikována do sekundárního.<br>LastSyncTime se neaktualizovala, protože <br>transakce B se ještě nereplikoval.|
| *Výtisk*     | Čtení entit <br>ze sekundární                           |                                  | T1                 | Získáte zastaralou hodnotu pro zaměstnance. <br> entita, protože transakce B nebyla <br> replika ještě proběhla. Získáte novou hodnotu pro<br> entita role správce, protože C má<br> replikovateln. Čas poslední synchronizace ještě není.<br> Aktualizováno, protože transakce B<br> nereplikuje se. Můžete říct, že<br>entita role správce je nekonzistentní. <br>protože je datum/čas entity po <br>Čas poslední synchronizace |
| *T6*     |                                                      | Transakce B<br> replikováno do<br> sekundární | T6                 | *T6* – všechny transakce prostřednictvím C <br>replikováno, čas poslední synchronizace<br> je aktualizovaný. |

V tomto příkladu Předpokládejme, že klient přepne na čtení ze sekundární oblasti v T5. V tuto chvíli může úspěšně číst entitu **role správce** , ale entita obsahuje hodnotu pro počet správců, kteří nejsou konzistentní s počtem entit **zaměstnanců** , kteří jsou v této době označeni jako správci v sekundární oblasti. Váš klient může jednoduše zobrazit tuto hodnotu s rizikem, že se jedná o nekonzistentní informace. Klient se případně může pokusit určit, že **role správce** je v potenciálně nekonzistentním stavu, protože aktualizace nastaly mimo pořadí, a pak uživatele informovat o této skutečnosti.

Aby bylo možné rozpoznat, že má potenciálně nekonzistentní data, může klient použít hodnotu *času poslední synchronizace* , kterou můžete kdykoli získat pomocí dotazu na službu úložiště. Tím se dozvíte čas, kdy byla data v sekundární oblasti naposledy konzistentní a kdy služba použila všechny transakce před tímto bodem v čase. V příkladu uvedeném výše služba vloží entitu **Zaměstnanec** do sekundární oblasti, čas poslední synchronizace se nastaví na *T1*. Zůstane v *T1* , dokud služba neaktualizuje entitu **Zaměstnanec** v sekundární oblasti, když je nastavená na *T6*. Pokud klient načte čas poslední synchronizace při čtení entity v *T5*, může ji porovnat s časovým razítkem v entitě. Pokud je časové razítko v entitě pozdější než čas poslední synchronizace, pak je entita v potenciálně nekonzistentním stavu a můžete to provést, pokud je pro vaši aplikaci vhodná akce. Použití tohoto pole vyžaduje, abyste věděli, kdy byla poslední aktualizace k primárnímu dokončení dokončena.

Informace o tom, jak ověřit čas poslední synchronizace, najdete v tématu [Zkontrolujte vlastnost čas poslední synchronizace pro účet úložiště](last-sync-time-get.md).

## <a name="testing"></a>Testování

Je důležité, abyste otestovali, že se vaše aplikace chová podle očekávání, když nalezne chyby s opakováním. Například je třeba otestovat, že se aplikace přepne do sekundárního a do režimu jen pro čtení, když zjistí problém, a přepne zpět, jakmile bude primární region opět k dispozici. K tomu potřebujete způsob, jak simulovat opakované chyby a určit, jak často k nim dochází.

Pomocí [Fiddler](https://www.telerik.com/fiddler) můžete zachytit a upravit odpovědi HTTP ve skriptu. Tento skript může identifikovat odpovědi, které pocházejí z primárního koncového bodu, a změnit stavový kód HTTP na ten, který Klientská knihovna pro úložiště rozpozná jako opakovanou chybu. Tento fragment kódu ukazuje jednoduchý příklad skriptu Fiddler, který zachycuje odpovědi na požadavky na čtení v tabulce **EmployeeData** , aby vrátil stav 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Tento příklad můžete rozšířit tak, aby zachytil širší rozsah požadavků a **ResponseCode** jenom na některých z nich, aby lépe simuloval scénář reálného světa. Další informace o přizpůsobení skriptů Fiddler najdete v tématu [Úprava žádosti nebo odpovědi](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) v dokumentaci k Fiddler.

Pokud jste provedli prahové hodnoty pro přepínání aplikace na režim jen pro čtení, bude snazší otestovat chování pomocí svazků neprodukčních transakcí.

## <a name="next-steps"></a>Další kroky

Kompletní vzorek, který ukazuje, jak přepnout zpátky mezi primárním a sekundárním koncovým body, najdete v tématu [ukázky Azure – použití vzoru pro přerušení okruhu s úložištěm RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
