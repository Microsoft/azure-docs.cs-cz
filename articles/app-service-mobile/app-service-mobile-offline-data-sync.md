---
title: Synchronizaci dat offline v Azure Mobile Apps | Microsoft Docs
description: Reference konceptu a Přehled funkce synchronizace offline dat pro Azure Mobile Apps
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 403b3289a6135a59e888c4a7bf1cd9cbe40c8ac0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597973"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offline synchronizace dat pro Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Co je offline synchronizací dat?
Synchronizace offline dat je klientských a serverových funkcí sady SDK Azure Mobile Apps, který usnadňuje vývojářům vytvářet aplikace, které jsou funkční bez připojení k síti.

Pokud vaše aplikace je v offline režimu, můžete přesto vytvořit a upravit data, která se uloží do místního úložiště. Pokud aplikace do režimu online, se synchronizovat místní změny se váš back-end mobilní aplikace Azure. Tato funkce zahrnuje taky podporu pro zjišťování konfliktů při změně stejný záznam na klientovi i back-end. Konflikty může následně ošetřit buď na serveru nebo klienta.

Offline synchronizace má několik výhod:

* Zlepšit odezvu aplikace ukládání do mezipaměti serveru data místně v zařízení
* Vytvářet robustní aplikace, které zůstávají užitečné, když jsou problémy s síti
* Povolit koncovým uživatelům vytvářet a upravovat data i v případě, že neexistuje žádný přístup k síti, podporuje scénáře s malým nebo žádným připojením
* Synchronizaci dat mezi různými zařízeními a rozpoznat je v konfliktu, kdy je stejný záznam změněn dvěma zařízeními
* Omezit využití sítě v sítích s vysokou latencí nebo měření podle objemu

Následující kurzy ukazují, jak přidat offline synchronizace mobilním klientům používat Azure Mobile Apps:

* [Android: Zapnutí offline synchronizace]
* [Apache Cordova: Zapnutí offline synchronizace](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: zapnutí offline synchronizace]
* [Xamarin iOS: zapnutí offline synchronizace]
* [Xamarin Android: Zapnutí offline synchronizace]
* [Xamarin.Forms: Offline synchronizace povolit](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzální platformu Windows: Zapnutí offline synchronizace]

## <a name="what-is-a-sync-table"></a>Co je synchronizace tabulky?
Pro přístup k "/ tabulky" koncový bod, klient sady SDK pro Azure Mobile poskytují rozhraní, jako `IMobileServiceTable` (klient .NET SDK) nebo `MSTable` (iOS klienta). Tato rozhraní API připojit přímo k back-end mobilní aplikace Azure a selhat, pokud zařízení klienta nemá připojení k síti.

Pro podporu použití v offline režimu, musí aplikace místo toho používat *synchronizace tabulky* rozhraní API, například `IMobileServiceSyncTable` (klient .NET SDK) nebo `MSSyncTable` (iOS klienta). Všechny stejné operace CRUD (vytvořit, číst, Update, Delete) pracovat s synchronizace tabulky rozhraní API, s výjimkou teď budou číst z nebo zapisovat *místního úložiště*. Před provedením jakékoli operace s tabulkou synchronizace, je nutné inicializovat místní úložiště.

## <a name="what-is-a-local-store"></a>Co je místní úložiště?
Místní úložiště je vrstvu trvalosti dat v klientském zařízení. Klientské sady SDK Azure Mobile Apps zadejte výchozí implementaci místního úložiště. V systému Windows, Xamarin a Android je založena na SQLite. V systému iOS je založena na základní Data.

Pokud chcete použít implementací na základě SQLite na Windows Phone nebo Microsoft Store, musíte nainstalovat rozšíření SQLite. Další informace najdete v tématu [univerzální platformu Windows: zapnutí offline synchronizace]. Android a iOS se dodávají spolu s verzi SQLite operačního systému zařízení samostatně, takže není nutné k odkazování svou vlastní verzi SQLite.

Vývojářům můžete taky implementovat vlastní místní úložiště. Například pokud chcete k ukládání dat v šifrovaném formátu v mobilního klienta, můžete definovat místní úložiště, které používá SQLCipher pro šifrování.

## <a name="what-is-a-sync-context"></a>Co je kontext synchronizace?
A *kontext synchronizace* souvisí s objektem mobilního klienta (například `IMobileServiceClient` nebo `MSClient`) a sleduje změny provedené s tabulkami synchronizace. Kontext synchronizace udržuje *operace fronty*, což zajišťuje uspořádaný seznam vytvoření operace (Create, Update, Delete), která je novější odeslat na server.

Místní úložiště je spojena s kontext synchronizace pomocí metodu initialize, jako třeba `IMobileServicesSyncContext.InitializeAsync(localstore)` v [klient .NET SDK].

## <a name="how-sync-works"></a>Jak offline synchronizace funguje
Při použití synchronizace tabulky, váš klientský kód řídí, kdy místní změny jsou synchronizovány s back-end mobilní aplikace Azure. Nic se posílá back-end, dokud nebude volání *nabízené* místní změny. Podobně místní úložiště obsahuje nová data jenom v případě, že je volání *vyžádání* data.

* **Nabízená**: nabízení je operace na kontext synchronizace a odešle všechny změny vytvoření od poslední nabízeného oznámení. Všimněte si, že není možné odeslat pouze změny jednotlivé tabulky, protože jinak by mohly být operace zasílány mimo pořadí. Nabízená provede řadu volání REST váš back-end mobilní aplikace Azure, který naopak upraví databázovém serveru.
* **Pro vyžádání obsahu**: vyžádané provádí na jednotlivých tabulek a je možné přizpůsobit prostřednictvím dotaz pro načtení pouze podmnožinu dat serveru. Klientské sady SDK Azure Mobile poté vložit Výsledná data do místního úložiště.
* **Implicitní nabízených oznámení**: Pokud je u tabulku, která má čekající místní aktualizace spustit vyžádání obsahu, vyžádání obsahu nejprve provede `push()` v kontextu synchronizace. Tato nabízené pomáhá minimalizovat konflikty mezi změny, které jsou již zařazeny do fronty a nová data ze serveru.
* **Přírůstkové synchronizace**: první parametr pro operaci pull je *název dotazu* používané pouze na straně klienta. Pokud použijete název dotazu jinou hodnotu než null, provede Azure Mobile SDK *přírůstkové synchronizace*. Pokaždé, když vyžádanou operaci vrátí celé sady výsledků, nejnovější `updatedAt` časové razítko z této sady výsledek je uložen v místní systémové tabulky SDK. Operace následné stažení načíst záznamy po této časové razítko.

  Můžete používat přírůstkovou synchronizaci, musí vracet serveru smysluplný `updatedAt` hodnoty a musí taky podporovat řazení podle tohoto pole. Ale vzhledem k tomu, že sada SDK přidá svůj vlastní řazení pole updatedAt, nemůžete použít dotaz vyžádání obsahu, který má svou vlastní `orderBy` klauzule.

  Název dotazu může být libovolný řetězec, který zvolíte, ale musí být jedinečný pro každý logický dotaz ve vaší aplikaci.
  Jinak operace různých stažení může přepsat stejné časové razítko přírůstkové synchronizace a vaše dotazy mohou vracet nesprávné výsledky.

  Pokud dotaz obsahuje parametr, jeden způsob, jak vytvořit název jedinečný dotazu je začlenit hodnotu parametru.
  Například filtrování na ID uživatele, název dotazu může být takto (v jazyku C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Pokud chcete pro vyjádření výslovného nesouhlasu přírůstkové synchronizace, předat `null` jako ID dotazu. V takovém případě jsou načteny všechny záznamy v každé volání `PullAsync`, která je potenciálně neefektivní.
* **Vymazání**: můžete vymazat obsah z místního úložiště pomocí `IMobileServiceSyncTable.PurgeAsync`.
  Pokud máte zastaralá data v databázi klienta, nebo pokud chcete zahodit všechny čekající změny, může být nutné vyprazdňování.

  Vyprázdnění vymaže tabulku z místního úložiště. Pokud existují operace čeká na synchronizaci s databází serveru, k vyprázdnění vyvolá výjimku, pokud *Vynutit vyprázdnění* parametr je nastaven.

  Jako příklad zastaralá data na straně klienta Předpokládejme v příkladu "seznam úkolů" Device1 vrátí pouze položky, které nebyly dokončeny. Úkolu "Koupit mléka" je označena dokončit na serveru jiné zařízení. Ale Device1 stále má todoitem "Koupit mlékem" v místním úložišti, protože ho je pouze stahování položky, které nejsou označeny jako dokončené. Vyprázdnění vymaže této zastaralé položky.

## <a name="next-steps"></a>Další postup
* [iOS: zapnutí offline synchronizace]
* [Xamarin iOS: zapnutí offline synchronizace]
* [Xamarin Android: Zapnutí offline synchronizace]
* [Univerzální platformu Windows: Zapnutí offline synchronizace]

<!-- Links -->
[Klient .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Zapnutí offline synchronizace]: app-service-mobile-android-get-started-offline-data.md
[iOS: zapnutí offline synchronizace]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: zapnutí offline synchronizace]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Zapnutí offline synchronizace]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzální platformu Windows: Zapnutí offline synchronizace]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
