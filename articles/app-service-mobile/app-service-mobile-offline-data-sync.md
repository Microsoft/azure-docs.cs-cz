---
title: Synchronizace offline dat
description: Koncepční referenční informace a Přehled funkce synchronizace offline dat pro Azure Mobile Apps
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458745"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offline synchronizace dat pro Azure Mobile Apps

## <a name="what-is-offline-data-sync"></a>Co je offline synchronizace dat?
Offline synchronizace dat je funkce klient a server sady SDK služby Azure Mobile Apps usnadňující vývojářům vytvářet aplikace, které jsou funkční bez připojení k síti.

Když je vaše aplikace v režimu offline, můžete stále vytvářet a upravovat data, která se ukládají do místního úložiště. Když je aplikace zase online, může synchronizovat místní změny s back-endu mobilní aplikace Azure. Tato funkce také zahrnuje podporu zjišťování konfliktů při změně stejného záznamu v klientovi i v back-endu. Konflikty je pak možné zpracovat buď na serveru, nebo na klientovi.

Offline synchronizace má několik výhod:

* Vylepšení odezvy aplikace ukládáním dat serveru do mezipaměti místně na zařízení
* Vytvářejte robustní aplikace, které budou fungovat, když dojde k problémům se sítí
* Umožněte koncovým uživatelům vytvářet a upravovat data, i když nedochází k přístupu k síti, podporu scénářů s malým nebo žádným připojením.
* Synchronizace dat napříč více zařízeními a zjištění konfliktů při úpravě stejného záznamu dvěma zařízeními
* Omezení využití sítě v sítích s vysokou latencí nebo měřenými sítěmi

Následující kurzy ukazují, jak přidat offline synchronizaci do mobilních klientů pomocí Azure Mobile Apps:

* [Android: povolení offline synchronizace]
* [Apache Cordova: Povolit offline synchronizaci](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: povolení offline synchronizace]
* [Xamarin iOS: povolení offline synchronizace]
* [Xamarin Android: povolení offline synchronizace]
* [Xamarin. Forms: povolení offline synchronizace](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzální platforma Windows: Povolit offline synchronizaci]

## <a name="what-is-a-sync-table"></a>Co je synchronizovaná tabulka?
Pro přístup ke koncovému bodu "/Tables" poskytují sady SDK pro mobilní klienty Azure rozhraní, jako je například `IMobileServiceTable` (.NET Client SDK) nebo `MSTable` (klient iOS). Tato rozhraní API se připojují přímo k back-endu mobilní aplikace Azure a selžou, pokud v klientském zařízení není síťové připojení.

Aby bylo možné podporu offline použít, měla by vaše aplikace místo toho používat rozhraní API pro *synchronizaci tabulek* , jako je například `IMobileServiceSyncTable` (.NET Client SDK) nebo `MSSyncTable` (klient iOS). Všechny stejné operace CRUD (vytvoření, čtení, aktualizace, odstranění) práce s rozhraními API pro synchronizaci tabulek, s výjimkou případů, kdy se nyní čtou z *místního úložiště*nebo do něj zapisovat. Před provedením jakékoli operace synchronizace tabulek je nutné inicializovat místní úložiště.

## <a name="what-is-a-local-store"></a>Co je místní úložiště?
Místní úložiště je vrstva trvalosti dat v klientském zařízení. Sady SDK pro klienty Azure Mobile Apps poskytují výchozí implementaci místního úložiště. Ve Windows, Xamarin a Androidu je založena na SQLite. V systému iOS je založena na základních datech.

Chcete-li použít implementaci na základě SQLite na Windows Phone nebo Microsoft Store, je nutné nainstalovat rozšíření SQLite. Další informace najdete v tématu [Univerzální platforma Windows: Povolit offline synchronizaci]. Android a iOS se dodávají s verzí SQLite v samotném operačním systému zařízení, takže není nutné odkazovat na vlastní verzi SQLite.

Vývojáři můžou také implementovat své vlastní místní úložiště. Pokud například chcete ukládat data do šifrovaného formátu v mobilním klientovi, můžete pro šifrování definovat místní úložiště, které používá SQLCipher.

## <a name="what-is-a-sync-context"></a>Co je kontext synchronizace?
*Kontext synchronizace* je přidružený k objektu mobilního klienta (například `IMobileServiceClient` nebo `MSClient`) a sleduje změny provedené s tabulkami synchronizace. Kontext synchronizace udržuje *frontu operací*, která udržuje uspořádaný seznam operací CUD (vytvořit, aktualizovat, odstranit), který se později pošle na server.

Místní úložiště je přidruženo k kontextu synchronizace pomocí metody Initialize, jako je například `IMobileServicesSyncContext.InitializeAsync(localstore)` v [Sada SDK klienta .NET].

## <a name="how-sync-works"></a>Jak funguje synchronizace offline
Při použití synchronizačních tabulek váš klientský kód řídí, kdy se místní změny synchronizují s back-endu mobilní aplikace Azure. Do back-endu se nic nepošle, dokud se nespustí volání na *vložení* místních změn. Podobně se místní úložiště naplní novými daty pouze v případě, že dojde k volání dat pro *vyžádání* .

* **Push**: Push je operace v kontextu synchronizace a odesílá všechny CUD změny od posledního vložení. Všimněte si, že není možné odesílat pouze změny jednotlivých tabulek, protože jiné operace mohou být odeslány mimo pořadí. Příkaz push spustí sérii volání REST do back-endu mobilních aplikací Azure, což zase upraví databázi serveru.
* **Pull**: operace přijetí změn se provádí na bázi jednotlivých tabulek a dá se přizpůsobit pomocí dotazu, který načte jenom podmnožinu serverových dat. Sady SDK mobilních klientů Azure pak vloží výsledná data do místního úložiště.
* **Implicitní nabízená oznámení**: Pokud se u tabulky, která čeká na místní aktualizace, spustí operaci vyžádání obsahu, vyžádá si nejprve `push()` v kontextu synchronizace. Tato nabízená oznámení pomáhají minimalizovat konflikty mezi změnami, které jsou už ve frontě, a novými daty ze serveru.
* **Přírůstková synchronizace**: první parametr operace přijetí změn je *název dotazu* , který se používá pouze v klientovi. Pokud použijete název dotazu, který není null, Azure Mobile SDK provede *přírůstkovou synchronizaci*. Pokaždé, když operace vyžádání obsahu vrátí sadu výsledků, bude v tabulce v místních systémových tabulkách sady SDK uloženo nejnovější `updatedAt` časové razítko z této sady výsledků. Následné operace Pull načítají pouze záznamy po tomto časovém razítku.

  Chcete-li použít přírůstkovou synchronizaci, server musí vracet smysluplné `updatedAt` hodnoty a musí také podporovat řazení podle tohoto pole. Vzhledem k tomu, že sada SDK přidá své vlastní řazení do pole updatedAt, nemůžete použít dotaz Pull, který má svou vlastní klauzuli `orderBy`.

  Název dotazu může být libovolný řetězec, který si zvolíte, ale musí být jedinečný pro každý logický dotaz ve vaší aplikaci.
  V opačném případě můžou různé operace přijetí změn přepsat stejné časové razítko přírůstkového synchronizace a dotazy můžou vracet nesprávné výsledky.

  Pokud má dotaz parametr, jeden ze způsobů, jak vytvořit jedinečný název dotazu, je začlenit hodnotu parametru.
  Pokud například filtrujete podle ID uživatele, může to být váš název dotazu takto C#:

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Pokud se chcete odhlásit z přírůstkové synchronizace, předejte `null` jako ID dotazu. V tomto případě jsou všechny záznamy načteny při každém volání `PullAsync`, což je potenciálně neefektivní.
* **Vymazání**: obsah místního úložiště můžete vymazat pomocí `IMobileServiceSyncTable.PurgeAsync`.
  Vymazání může být nutné v případě, že máte v klientské databázi zastaralá data, nebo pokud chcete zahodit všechny nedokončené změny.

  Vymazání vymaže tabulku z místního úložiště. Pokud jsou operace čekající na synchronizaci s databází serveru, vygeneruje vyprázdnění výjimku, pokud není nastaven parametr *Vynutit mazání* .

  Jako příklad zastaralých dat na klientovi, Předpokládejme v příkladu "seznam úkolů", Zařízení1 pouze nedokončené položky. TodoItem "koupit mléko" je na serveru označeno jiným zařízením. Zařízení1 však stále obsahuje TodoItem "koupit mléko" v místním úložišti, protože probíhá pouze stahování položek, které nejsou označeny jako úplné. Vymaže tuto zastaralou položku.

## <a name="next-steps"></a>Další kroky
* [iOS: povolení offline synchronizace]
* [Xamarin iOS: povolení offline synchronizace]
* [Xamarin Android: povolení offline synchronizace]
* [Univerzální platforma Windows: Povolit offline synchronizaci]

<!-- Links -->
[Sada SDK klienta .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: povolení offline synchronizace]: app-service-mobile-android-get-started-offline-data.md
[iOS: povolení offline synchronizace]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: povolení offline synchronizace]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: povolení offline synchronizace]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzální platforma Windows: Povolit offline synchronizaci]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
