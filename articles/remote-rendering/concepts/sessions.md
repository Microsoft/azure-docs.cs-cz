---
title: Relace Remote Renderingu
description: Popisuje, co je relace vzdáleného vykreslování.
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e7f441a727bea8d1a5d401fb5e9757dee1044fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613808"
---
# <a name="remote-rendering-sessions"></a>Relace Remote Renderingu

Ve vzdáleném vykreslování Azure (ARR) je *relace* Klíčovým konceptem. Tento článek vysvětluje, co je přesně relace.

## <a name="overview"></a>Přehled

Vzdálené vykreslování Azure funguje pomocí přesměrování složitých úloh vykreslování do cloudu. Tyto úlohy vykreslování nemůžou být splněné pouhým serverem, protože většina cloudových serverů nemá GPU. Z důvodu množství dat a pevného požadavku na zpracování výsledků v rámci interaktivních snímkových sazeb se na základě toho, který server zpracovává, který požadavek uživatele nedokáže přenášet do jiného počítače průběžně, jako je to možné pro častější webový provoz.

To znamená, že když používáte vzdálené vykreslování Azure, musí být cloudový server s potřebnými hardwarovými funkcemi vyhrazený výhradně pro zpracování vašich požadavků na vykreslování. *Relace* odkazuje na všechno, co se zabývá interakcí s tímto serverem. Začíná počáteční žádostí o rezervaci (*zapůjčení*) počítače pro vaše použití, pokračuje všemi příkazy pro načtení a manipulaci s modely a končí uvolněním zapůjčení na cloudovém serveru.

## <a name="managing-sessions"></a>Správa relací

Existuje několik způsobů, jak spravovat relace a pracovat s nimi. Nezávisle na způsobu vytváření, aktualizace a vypínání relací jsou prostřednictvím [REST API správy relací](../how-tos/session-rest-api.md). V jazyce C# a C++ se tyto operace zveřejňují prostřednictvím tříd `AzureFrontend` a `AzureSession` . Pro aplikace Unity existují další obslužné funkce poskytované `ARRServiceUnity` komponentou.

Jakmile budete *připojeni* k aktivní relaci, operace jako [načítání modelů](models.md) a interakce s scénou jsou zpřístupněny prostřednictvím `AzureSession` třídy.

### <a name="managing-multiple-sessions-simultaneously"></a>Správa více relací současně

*Připojení* k několika relacím z jednoho zařízení není možné. V rámci jedné aplikace ale můžete vytvořit, sledovat a vypnout libovolný počet relací. Dokud se aplikace nechce připojit k relaci, nemusíte ji spouštět na zařízení jako HoloLens 2, a to buď. Případ použití pro takovou implementaci může být, pokud chcete řídit relace prostřednictvím centrálního mechanismu. Například jedna může vytvořit webovou aplikaci, do které se může přihlásit víc tabletů a zařízení HoloLens. Pak se aplikace může zobrazit na tabletech, například na to, který model CAD se má zobrazit. Pokud uživatel provede výběr, budou tyto informace sděleny všem zařízením HoloLens, aby bylo možné vytvořit sdílené prostředí.

## <a name="session-phases"></a>Fáze relace

Každá relace projde několik fází.

### <a name="session-startup"></a>Spuštění relace

Když požádáte o [Vytvoření nové relace](../how-tos/session-rest-api.md#create-a-session)pomocí ARR, je první věc, kterou vrátí [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)relace. Tento identifikátor UUID vám umožní zadat dotaz na informace o relaci. Identifikátor UUID a některé základní informace o relaci jsou trvalé po dobu 30 dnů, takže je můžete zadat dotaz na tyto informace i po zastavení relace. V tomto okamžiku bude **stav relace** hlášen jako **spuštění**.

V dalším kroku se vzdálené vykreslování Azure pokusí najít server, který může hostovat vaši relaci. Pro toto hledání existují dva parametry. Nejprve bude vyhradit jenom servery ve vaší [oblasti](../reference/regions.md). Důvodem je, že latence sítě v různých oblastech může být příliš vysoká, aby se zaručilo dáté prostředí. Druhý faktor je požadovaná *Velikost* , kterou jste určili. V každé oblasti je k dispozici omezený počet serverů, které mohou splňovat požadavky na velikost [*Standard*](../reference/vm-sizes.md) nebo [*Premium*](../reference/vm-sizes.md) . V důsledku toho platí, že pokud jsou všechny servery požadované velikosti aktuálně používány ve vaší oblasti, vytvoření relace se nezdaří. Důvodem selhání [může být dotazování](../how-tos/session-rest-api.md#get-sessions-properties).

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

Relace končí dvěma pravidelnými důvody. Buď ručně vyžádáte relaci, která se má zastavit, nebo vyprší maximální doba zapůjčení. V obou případech se jakékoli aktivní připojení k hostiteli hned ukončí a služba se na tomto serveru vypne. Server se pak vrátí zpátky do fondu Azure a může získat další účely. Zastavení relace nelze vrátit zpět nebo zrušit. Dotazování **stavu relace** na zastavené relaci vrátí hodnotu **Zastaveno** nebo **vypršela jeho platnost**v závislosti na tom, zda byl ručně vypnut nebo protože byla dosažena maximální doba zapůjčení.

V důsledku nějaké chyby se může také zastavit relace.

Ve všech případech se po zastavení relace neúčtují další poplatky.

> [!WARNING]
> Bez ohledu na to, jestli se k relaci připojujete a jak dlouho nemá vliv na fakturaci. To, co platíte za službu, závisí na *době trvání relace*, což znamená čas, kdy je server exkluzivně vyhrazený, a požadované hardwarové schopnosti ( [přidělená velikost](../reference/vm-sizes.md)). Pokud spustíte relaci, připojíte po dobu pěti minut a pak relaci nezastavíte, takže zůstane spuštěná, dokud její zapůjčení nevyprší, bude se vám účtovat doba zapůjčení celé relace. Naopak *Maximální doba zapůjčení* je většinou bezpečnostní síť. Nezáleží na tom, jestli si vyžádáte relaci s časem zapůjčení na osm hodin, a pak ji používejte jenom pět minut, když relaci ručně zastavíte.

#### <a name="extend-a-sessions-lease-time"></a>Prodloužení doby zapůjčení relace

[Dobu zapůjčení](../how-tos/session-rest-api.md#modify-and-query-session-properties) aktivní relace můžete prodloužit, pokud se tak stane, že ji budete potřebovat.

## <a name="example-code"></a>Příklad kódu

Následující kód ukazuje jednoduchou implementaci spuštění relace, čekání na stav *připraveno* , připojení a odpojení a opětovné ukončení.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
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
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Více `AzureFrontend` `AzureSession` instancí a lze spravovat, manipulovat a dotazovat z kódu. V jednom okamžiku se ale může připojit jenom jedno zařízení `AzureSession` .

Životnost virtuálního počítače není vázaná na `AzureFrontend` instanci nebo `AzureSession` instanci. `AzureSession.StopAsync` se musí volat, aby se zastavila relace.

Na ID trvalé relace se dá dotazovat `AzureSession.SessionUUID()` místně pomocí mezipaměti. S tímto ID může aplikace volat `AzureFrontend.OpenSession` , aby se k této relaci navázala.

Pokud `AzureSession.IsConnected` je hodnota true, `AzureSession.Actions` vrátí instanci `RemoteManager` , která obsahuje funkce pro [načtení modelů](models.md), manipulaci s [entitami](entities.md)a dotazy na [informace](../overview/features/spatial-queries.md) o vykreslené scéně.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# AzureSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azuresession)
* [C# AzureFrontend. CreateNewRenderingSessionAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azurefrontend.createnewrenderingsessionasync)
* [C# AzureFrontend. OpenRenderingSession ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azurefrontend.openrenderingsession)
* [Třída C++ AzureSession](https://docs.microsoft.com/cpp/api/remote-rendering/azuresession)
* [C++ AzureFrontend:: CreateNewRenderingSessionAsync](https://docs.microsoft.com/cpp/api/remote-rendering/azurefrontend#createnewrenderingsessionasync)
* [C++ AzureFrontend:: OpenRenderingSession](https://docs.microsoft.com/cpp/api/remote-rendering/azurefrontend#openrenderingsession)

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Modely](models.md)
