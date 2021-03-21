---
title: Relace Remote Renderingu
description: Popisuje, co je relace vzdáleného vykreslování.
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 79f3f93338d15562dcc37857d63bc8b2d7e96b05
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530548"
---
# <a name="remote-rendering-sessions"></a>Relace Remote Renderingu

Ve vzdáleném vykreslování Azure (ARR) je *relace* Klíčovým konceptem. Tento článek vysvětluje, co je přesně relace.

## <a name="overview"></a>Přehled

Vzdálené vykreslování Azure funguje pomocí přesměrování složitých úloh vykreslování do cloudu. Tyto úlohy vykreslování nemůžou být splněné pouhým serverem, protože většina cloudových serverů nemá GPU. Z důvodu množství dat a pevného požadavku na zpracování výsledků v rámci interaktivních snímkových sazeb se na základě toho, který server zpracovává, který požadavek uživatele nedokáže přenášet do jiného počítače průběžně, jako je to možné pro častější webový provoz.

To znamená, že když používáte vzdálené vykreslování Azure, musí být cloudový server s potřebnými hardwarovými funkcemi vyhrazený výhradně pro zpracování vašich požadavků na vykreslování. *Relace* odkazuje na všechno, co se zabývá interakcí s tímto serverem. Začíná počáteční žádostí o rezervaci (*zapůjčení*) počítače pro vaše použití, pokračuje všemi příkazy pro načtení a manipulaci s modely a končí uvolněním zapůjčení na cloudovém serveru.

## <a name="managing-sessions"></a>Správa relací

Existuje několik způsobů, jak spravovat relace a pracovat s nimi. Nezávisle na způsobu vytváření, aktualizace a vypínání relací jsou prostřednictvím [REST API správy relací](../how-tos/session-rest-api.md). V jazyce C# a C++ se tyto operace zveřejňují prostřednictvím tříd `RemoteRenderingClient` a `RenderingSession` . Pro aplikace Unity existují další obslužné funkce poskytované `ARRServiceUnity` komponentou.

Jakmile budete *připojeni* k aktivní relaci, operace jako [načítání modelů](models.md) a interakce s scénou jsou zpřístupněny prostřednictvím `RenderingSession` třídy.

### <a name="managing-multiple-sessions-simultaneously"></a>Správa více relací současně

*Připojení* k několika relacím z jednoho zařízení není možné. V rámci jedné aplikace ale můžete vytvořit, sledovat a vypnout libovolný počet relací. Dokud se aplikace nechce připojit k relaci, nemusíte ji spouštět na zařízení jako HoloLens 2, a to buď. Případ použití pro takovou implementaci může být, pokud chcete řídit relace prostřednictvím centrálního mechanismu. Například jedna může vytvořit webovou aplikaci, do které se může přihlásit víc tabletů a zařízení HoloLens. Pak se aplikace může zobrazit na tabletech, například na to, který model CAD se má zobrazit. Pokud uživatel provede výběr, budou tyto informace sděleny všem zařízením HoloLens, aby bylo možné vytvořit sdílené prostředí.

## <a name="session-phases"></a>Fáze relace

Každá relace projde několik fází.

### <a name="session-startup"></a>Spuštění relace

Když požádáte o [Vytvoření nové relace](../how-tos/session-rest-api.md)pomocí ARR, je první věc, kterou vrátí [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)relace. Tento identifikátor UUID vám umožní zadat dotaz na informace o relaci. Identifikátor UUID a některé základní informace o relaci jsou trvalé po dobu 30 dnů, takže je můžete zadat dotaz na tyto informace i po zastavení relace. V tomto okamžiku bude **stav relace** hlášen jako **spuštění**.

V dalším kroku se vzdálené vykreslování Azure pokusí najít server, který může hostovat vaši relaci. Pro toto hledání existují dva parametry. Nejprve bude vyhradit jenom servery ve vaší [oblasti](../reference/regions.md). Důvodem je, že latence sítě v různých oblastech může být příliš vysoká, aby se zaručilo dáté prostředí. Druhý faktor je požadovaná *Velikost* , kterou jste určili. V každé oblasti je k dispozici omezený počet serverů, které mohou splňovat požadavky na velikost [*Standard*](../reference/vm-sizes.md) nebo [*Premium*](../reference/vm-sizes.md) . V důsledku toho platí, že pokud jsou všechny servery požadované velikosti aktuálně používány ve vaší oblasti, vytvoření relace se nezdaří. Důvodem selhání [může být dotazování](../how-tos/session-rest-api.md).

> [!IMPORTANT]
> Pokud vyžádáte velikost *standardního* serveru a požadavek selže kvůli vysokému vyžádání, neznamená to, že požadavek na *prémiový* server selže i nadále. Takže pokud je to pro vás možnost, můžete se pokusit vrátit se k velikosti *Premium* serveru.

Když služba najde vhodný server, bude muset zkopírovat do něj příslušný virtuální počítač (VM), aby se přepnul na hostitele vzdáleného vykreslování Azure. Tento proces trvá několik minut. Následně se virtuální počítač spustí a přechodem **stavu relace** na **připraveno**.

V tomto okamžiku server aplikace čeká výhradně na zadání. To je také bod, ze kterého se vám bude účtovat služba.

### <a name="connecting-to-a-session"></a>Připojení k relaci

Jakmile bude relace *připravena*, můžete se k ní *připojit* . Při připojení může zařízení odeslat příkazy pro načtení a úpravu modelů. Každý hostitel ARR v jednu chvíli obsluhuje jenom jedno klientské zařízení, takže když se klient připojí k relaci, má výhradní kontrolu nad vykresleným obsahem. To také znamená, že se výkon vykreslování nikdy nebude lišit z důvodů mimo váš ovládací prvek.

> [!IMPORTANT]
> I když se k relaci může *připojit* jenom jeden klient, základní informace o relacích, jako je jejich aktuální stav, se dají dotazovat bez připojení.

Když je zařízení připojené k relaci, pokusy jiných zařízení, aby se připojily, selžou. Pokud se ale připojené zařízení odpojí, ať už dobrovolně nebo z důvodu určitého druhu selhání, bude relace akceptovat jinou žádost o připojení. Všechny předchozí stavy (načtené modely a takové) se zahodí tak, že další připojující zařízení získá čistou hodnotu SLAT. Proto mohou být relace opakovaně opakovaně používány různými zařízeními a může být možné skrýt režijní náklady na spuštění relace od koncového uživatele ve většině případů.

> [!IMPORTANT]
> Vzdálený server nikdy nemění stav dat na straně klienta. Všechny mutace dat (například aktualizace transformace a požadavky na zatížení) musí provádět klientská aplikace. Všechny akce okamžitě aktualizují stav klienta.

### <a name="session-end"></a>Konec relace

Když vyžádáte novou relaci, zadáte *maximální dobu zapůjčení*, obvykle v rozsahu 1 až 8 hodin. Toto je doba, po kterou bude hostitel akceptovat vaše zadání.

Relace končí dvěma pravidelnými důvody. Buď ručně vyžádáte relaci, která se má zastavit, nebo vyprší maximální doba zapůjčení. V obou případech se jakékoli aktivní připojení k hostiteli hned ukončí a služba se na tomto serveru vypne. Server se pak vrátí zpátky do fondu Azure a může získat další účely. Zastavení relace nelze vrátit zpět nebo zrušit. Dotazování **stavu relace** na zastavené relaci vrátí hodnotu **Zastaveno** nebo **vypršela jeho platnost** v závislosti na tom, zda byl ručně vypnut nebo protože byla dosažena maximální doba zapůjčení.

V důsledku nějaké chyby se může také zastavit relace.

Ve všech případech se po zastavení relace neúčtují další poplatky.

> [!WARNING]
> Bez ohledu na to, jestli se k relaci připojujete a jak dlouho nemá vliv na fakturaci. To, co platíte za službu, závisí na *době trvání relace*, což znamená čas, kdy je server exkluzivně vyhrazený, a požadované hardwarové schopnosti ( [přidělená velikost](../reference/vm-sizes.md)). Pokud spustíte relaci, připojíte po dobu pěti minut a pak relaci nezastavíte, takže zůstane spuštěná, dokud její zapůjčení nevyprší, bude se vám účtovat doba zapůjčení celé relace. Naopak *Maximální doba zapůjčení* je většinou bezpečnostní síť. Nezáleží na tom, jestli si vyžádáte relaci s časem zapůjčení na osm hodin, a pak ji používejte jenom pět minut, když relaci ručně zastavíte.

#### <a name="extend-a-sessions-lease-time"></a>Prodloužení doby zapůjčení relace

[Dobu zapůjčení](../how-tos/session-rest-api.md) aktivní relace můžete prodloužit, pokud se tak stane, že ji budete potřebovat.

## <a name="example-code"></a>Příklad kódu

Následující kód ukazuje jednoduchou implementaci spuštění relace, čekání na stav *připraveno* , připojení a odpojení a opětovné ukončení.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

SessionConfiguration sessionConfig = new SessionConfiguration();
// fill out sessionConfig details...

RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

RenderingSessionCreationOptions rendererOptions = new RenderingSessionCreationOptions();
// fill out rendererOptions...

CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(rendererOptions);

RenderingSession session = result.Session;
RenderingSessionProperties sessionProperties;
while (true)
{
    var propertiesResult = await session.GetPropertiesAsync();
    sessionProperties = propertiesResult.SessionProperties;
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
ConnectionStatus connectStatus = await session.ConnectAsync(new RendererInitOptions());

// Connected!

while (...)
{
    // per frame update

    session.Connection.Update();
}

// Disconnect
session.Disconnect();

// stop the session
await session.StopAsync();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Více `RemoteRenderingClient` `RenderingSession` instancí a lze spravovat, manipulovat a dotazovat z kódu. V jednom okamžiku se ale může připojit jenom jedno zařízení `RenderingSession` .

Životnost virtuálního počítače není vázaná na `RemoteRenderingClient` instanci nebo `RenderingSession` instanci. `RenderingSession.StopAsync` se musí volat, aby se zastavila relace.

Na ID trvalé relace se dá dotazovat `RenderingSession.SessionUuid()` místně pomocí mezipaměti. S tímto ID může aplikace volat `RemoteRenderingClient.OpenRenderingSessionAsync` , aby se k této relaci navázala.

Pokud `RenderingSession.IsConnected` je hodnota true, `RenderingSession.Connection` vrátí instanci `RenderingConnection` , která obsahuje funkce pro [načtení modelů](models.md), manipulaci s [entitami](entities.md)a dotazy na [informace](../overview/features/spatial-queries.md) o vykreslené scéně.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession)
* [C# RemoteRenderingClient. CreateNewRenderingSessionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.createnewrenderingsessionasync)
* [C# RemoteRenderingClient. OpenRenderingSessionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.openrenderingsessionasync)
* [Třída C++ RenderingSession](/cpp/api/remote-rendering/renderingsession)
* [C++ RemoteRenderingClient:: CreateNewRenderingSessionAsync](/cpp/api/remote-rendering/remoterenderingclient#createnewrenderingsessionasync)
* [C++ RemoteRenderingClient:: OpenRenderingSession](/cpp/api/remote-rendering/remoterenderingclient#openrenderingsession)

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Modely](models.md)