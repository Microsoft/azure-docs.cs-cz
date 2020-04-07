---
title: Relace vzdáleného vykreslování
description: Popisuje, co je relace vzdáleného vykreslování
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681685"
---
# <a name="remote-rendering-sessions"></a>Relace vzdáleného vykreslování

Ve vzdáleném vykreslování Azure (ARR) je *relace* klíčovým konceptem. Tento článek vysvětluje, co přesně relace je.

## <a name="overview"></a>Přehled

Azure Remote Rendering funguje tak, že překládá složité úlohy vykreslování do cloudu. Tyto úlohy vykreslování nemohou být splněny pouze libovolným serverem, protože většina cloudových serverů nemá grafické procesory. Vzhledem k množství dat a pevnému požadavku na vytváření výsledků při interaktivních snímkových frekvencích nelze odpovědnost, kterou server zpracovává, který požadavek uživatele také předat jinému počítači za chodu, což může být možné pro běžnější webový provoz.

To znamená, že při použití vzdáleného vykreslování Azure musí být cloudový server s potřebnými hardwarovými funkcemi vyhrazen výhradně pro zpracování vašich požadavků na vykreslování. *Relace* odkazuje na vše, co se týká interakce s tímto serverem. Začíná počáteční mačkáním *(zapůjčením)* stroje pro vaše použití, pokračuje všemi příkazy pro načítání a manipulaci s modely a končí uvolněním zapůjčení na cloudovém serveru.

## <a name="managing-sessions"></a>Správa relací

Existuje několik způsobů správy a interakce s relacemi. Jazykově nezávislý způsob vytváření, aktualizace a vypínání relací je prostřednictvím [rozhraní REST API pro správu relací](../how-tos/session-rest-api.md). V jazyce C# a C++ jsou `AzureFrontend` `AzureSession`tyto operace vystaveny prostřednictvím tříd a . Pro aplikace Unity existují další funkce nástroje `ARRServiceUnity` poskytované komponentou.

Jakmile jste *připojeni* k aktivní relaci, operace, jako je [například načítání modelů](models.md) a interakci s scénou `AzureSession` jsou vystaveny prostřednictvím třídy.

### <a name="managing-multiple-sessions-simultaneously"></a>Správa více relací současně

Není možné plně *připojit* k více relacím z jednoho zařízení. Můžete však vytvořit, sledovat a vypnout tolik relací, kolik chcete z jedné aplikace. Dokud aplikace není určena k tomu, aby se někdy připojila k relaci, nemusí běžet ani na zařízení, jako je HoloLens 2. Případ použití pro takové implementace může být, pokud chcete řídit relace prostřednictvím centrálnímechanismus. Například, jeden mohl vytvořit webovou aplikaci, kde více tabletů a HoloLenses můžete přihlásit. Pak aplikace může zobrazit možnosti na tabletech, například který cad model se má zobrazit. Pokud uživatel provede výběr, tyto informace jsou sděleny všem HoloLenses vytvořit sdílené prostředí.

## <a name="session-phases"></a>Fáze relace

Každá relace prochází několika fázemi.

### <a name="session-startup"></a>Spuštění relace

Když požádáte ARR k [vytvoření nové relace](../how-tos/session-rest-api.md#create-a-session), první věc, kterou dělá, je vrátit relaci [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Toto UUID umožňuje dotazovat se na informace o relaci. UUID a některé základní informace o relaci jsou trvalé po dobu 30 dnů, takže můžete dotaz ovat informace i po ukončení relace. V tomto okamžiku bude **stav relace** vykázán jako **Starting**.

V dalším případě Azure vzdálené vykreslování se pokusí najít server, který může hostit vaše relace. Existují dva parametry pro toto hledání. Za prvé, bude rezervovat pouze servery ve vaší [oblasti](../reference/regions.md). Je to proto, že latence sítě napříč oblastmi může být příliš vysoká, aby zaručila slušné prostředí. Druhý faktor je požadovaná *velikost,* kterou jste zadali. V každé oblasti existuje omezený počet serverů, které mohou splnit požadavek *na standardní* nebo *prémiovou* velikost. V důsledku toho pokud všechny servery požadované velikosti jsou aktuálně používány ve vaší oblasti, vytvoření relace se nezdaří. Důvod selhání [lze dotazovat](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Pokud požadujete *standardní* velikost virtuálního počítače a požadavek se nezdaří z důvodu vysoké poptávky, neznamená to, že se nezdaří i požadavek na *prémiový* server. Takže pokud je to možnost pro vás, můžete zkusit vrátit se k *prémiovému* virtuálnímu účtu.

Když služba najde vhodný server, musí zkopírovat správný virtuální počítač (VM) na něj převést na hostitele vzdáleného vykreslování Azure. Tento proces trvá několik minut. Poté je virtuální virtuální medailon spuštěn a **stav relace** přechází na **Připraveno**.

V tomto okamžiku server výhradně čeká na váš vstup. To je také bod, ze kterého se vám účtuje za službu.

### <a name="connecting-to-a-session"></a>Připojení k relaci

Jakmile je relace *připravena*, můžete *se k ní připojit.* Při připojení může zařízení odesílat příkazy k načtení a úpravám modelů. Každý hostitel ARR vždy obsluhuje pouze jedno klientské zařízení najednou, takže když se klient připojí k relaci, má výhradní kontrolu nad vykresleným obsahem. To také znamená, že výkon vykreslování se nikdy nebude lišit z důvodů mimo vaši kontrolu.

> [!IMPORTANT]
> Přestože pouze jeden klient se může *připojit k* relaci, základní informace o relacích, jako je například jejich aktuální stav, lze dotazovat bez připojení.

Pokud je zařízení připojeno k relaci, pokusy jiných zařízení o připojení se nezdaří. Jakmile se však připojené zařízení odpojí, dobrovolně nebo z důvodu nějakého selhání, relace přijme další požadavek na připojení. Všechny předchozí stav (načtené modely a tak) je zahozen tak, že další připojovací zařízení dostane čistý štít. Relace tedy mohou být znovu použity mnohokrát různými zařízeními a ve většině případů může být možné skrýt režii při spuštění relace před koncovým uživatelem.

> [!IMPORTANT]
> Vzdálený server nikdy nezmění stav dat na straně klienta. Všechny mutace dat (například aktualizace transformace a požadavky na nanačtení) musí být provedeny klientskou aplikací. Všechny akce okamžitě aktualizovat stav klienta.

### <a name="session-end"></a>Ukončení relace

Při požadavku na novou relaci zadáte *maximální dobu zapůjčení*, obvykle v rozsahu jedna až osm hodin. Toto je doba, po kterou hostitel přijme váš vstup.

Existují dva pravidelné důvody pro ukončení relace. Buď ručně požádáte o zastavení relace, nebo vyprší maximální doba zapůjčení. V obou případech je jakékoli aktivní připojení k hostiteli uzavřena ihned a služba je ukončena na tomto serveru. Server je pak věnována zpět do fondu Azure a může získat žádanky pro jiné účely. Zastavení relace nelze vrátit zpět nebo zrušit. Dotazování na **stav relace** v zastavené relaci vrátí buď **Zastaveno** nebo **Vypršela**platnost , v závislosti na tom, zda byla ručně vypnuta nebo protože bylo dosaženo maximální doby zapůjčení.

Relace může být také zastavena z důvodu některých selhání.

Ve všech případech vám nebudou účtovány další, jakmile je relace zastavena.

> [!WARNING]
> To, zda se připojíte k relaci a na jak dlouho, nemá vliv na fakturaci. To, co platíte za službu, závisí na *době trvání relace*, to znamená čas, kdy je server vyhrazen výhradně pro vás, a na požadovaných hardwarových možnostech (velikost virtuálního počítače). Pokud zahájíte relaci, připojíte se na pět minut a potom ji nezastavujte, takže bude spuštěna, dokud nevyprší její zapůjčení, bude se vám účtovat celá doba zapůjčení relace. Naopak maximální *doba pronájmu* je většinou záchrannou sítí. Nezáleží na tom, zda požadujete relaci s dobou zapůjčení osm hodin, pak ji používejte pouze po dobu pěti minut, pokud později ručně zastavíte relaci.

#### <a name="extend-a-sessions-lease-time"></a>Prodloužení doby zapůjčení relace

Můžete [prodloužit dobu zapůjčení](../how-tos/session-rest-api.md#update-a-session) aktivní relace, pokud se ukáže, že ji potřebujete déle.

## <a name="example-code"></a>Příklad kódu

Níže uvedený kód ukazuje jednoduchou implementaci spuštění relace, čekání na *stav připravenosti,* připojení a následné odpojení a vypnutí znovu.

``` cs
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

Více `AzureFrontend` `AzureSession` a instance mohou být udržovány, manipulovat a dotazován z kódu. Ale pouze jedno zařízení se `AzureSession` může připojit najednou.

Životnost virtuálního počítače není vázána `AzureFrontend` na instanci nebo `AzureSession` instanci. `AzureSession.StopAsync`musí být volána k zastavení relace.

Trvalé ID relace lze dotazovat prostřednictvím `AzureSession.SessionUUID()` a ukládání do mezipaměti místně. S tímto ID aplikace `AzureFrontend.OpenSession` může volat vazbu na tuto relaci.

Pokud `AzureSession.IsConnected` je `AzureSession.Actions` true, vrátí `RemoteManager`instanci aplikace , která obsahuje funkce pro [načtení modelů](models.md), manipulaci s [entitami](entities.md)a [informace](../overview/features/spatial-queries.md) o vykreslené scéně.

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Modely](models.md)
