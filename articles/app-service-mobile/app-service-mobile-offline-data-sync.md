---
title: Synchronizace dat offline
description: Koncepční reference a přehled funkce synchronizace offline dat pro Mobilní aplikace Azure
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458745"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offline synchronizace dat pro Azure Mobile Apps

## <a name="what-is-offline-data-sync"></a>Co je synchronizace offline dat?
Synchronizace offline dat je funkce sady Azure Mobile Apps pro klienta a server, která vývojářům usnadňuje vytváření aplikací, které jsou funkční bez připojení k síti.

Když je aplikace v režimu offline, můžete stále vytvářet a upravovat data, která se ukládají do místního obchodu. Když je aplikace zase online, může synchronizovat místní změny s back-endem mobilní aplikace Azure. Funkce také obsahuje podporu pro zjišťování konfliktů při změně stejného záznamu v klientovi i back-endu. Konflikty pak mohou být zpracovány na serveru nebo v klientovi.

Offline synchronizace má několik výhod:

* Zlepšete odezvu aplikací ukládáním dat serveru místně do mezipaměti v zařízení
* Vytváření robustních aplikací, které zůstávají užitečné i v případě problémů se sítí
* Povolit koncovým uživatelům vytvářet a upravovat data i v případě, že neexistuje přístup k síti, podporující scénáře s malým nebo žádným připojením
* Synchronizace dat mezi více zařízeními a zjišťování konfliktů při změně stejného záznamu dvěma zařízeními
* Omezení využití sítě v sítích s vysokou latencí nebo v sítích účtovaných podle objemu dat

Následující kurzy ukazují, jak přidat offline synchronizaci do mobilních klientů pomocí Mobilních aplikací Azure:

* [Android: Povolení offline synchronizace]
* [Apache Cordova: Povolit offline synchronizaci](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Povolení offline synchronizace]
* [Xamarin iOS: Povolení offline synchronizace]
* [Xamarin Android: Povolit offline synchronizaci]
* [Xamarin.Forms: Povolení offline synchronizace](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzální platforma Windows: Povolení offline synchronizace]

## <a name="what-is-a-sync-table"></a>Co je synchronizační tabulka?
Pro přístup ke koncovému bodu "/tables" poskytují sady SDK `IMobileServiceTable` pro klienta Azure Mobile `MSTable` rozhraní rozhraní, jako je například (klient.NET SDK) nebo (iOS klient). Tato rozhraní API se připojují přímo k back-endu mobilní aplikace Azure Mobile App a selhání, pokud klientské zařízení nemá síťové připojení.

Pro podporu offline použití by vaše aplikace měla místo `IMobileServiceSyncTable` toho používat rozhraní API *synchronizační tabulky,* například (klient.NET SDK) nebo `MSSyncTable` (klient iOS). Všechny stejné operace CRUD (Vytvořit, Číst, Aktualizovat, Odstranit) fungují proti synchronizačním souborům API tabulky, s výjimkou, že nyní čtou nebo zapisují do *místního úložiště*. Před provedením všech operací synchronizační tabulky musí být inicializováno místní úložiště.

## <a name="what-is-a-local-store"></a>Co je místní obchod?
Místní úložiště je vrstva trvalost dat na klientském zařízení. Sady SDK klienta Azure Mobile Apps poskytují výchozí implementaci místního úložiště. Na Windows, Xamarin a Android, je založen na SQLite. V systému iOS je založen na základních datech.

Chcete-li používat implementaci založenou na SQLite ve Windows Phone nebo Microsoft Store, musíte nainstalovat rozšíření SQLite. Další informace naleznete [v tématu Univerzální platforma Windows: Povolení offline synchronizace]. Android a iOS loď s verzí SQLite v operačním systému samotném zařízení, takže není nutné odkazovat na vlastní verzi SQLite.

Vývojáři mohou také implementovat své vlastní místní úložiště. Například pokud chcete ukládat data v šifrovaném formátu v mobilním klientovi, můžete definovat místní úložiště, které používá SQLCipher pro šifrování.

## <a name="what-is-a-sync-context"></a>Co je kontext synchronizace?
*Kontext synchronizace* je přidružen k objektu `IMobileServiceClient` `MSClient`mobilního klienta (například nebo ) a sleduje změny provedené pomocí synchronizačních tabulek. Kontext synchronizace udržuje *frontu operací*, která udržuje seřazený seznam operací CUD (Vytvořit, Aktualizovat, Odstranit), který je později odeslán na server.

Místní úložiště je přidruženo k kontextu synchronizace pomocí `IMobileServicesSyncContext.InitializeAsync(localstore)` metody inicializovat, například v [sada SDK klienta .NET].

## <a name="how-offline-synchronization-works"></a><a name="how-sync-works"></a>Jak funguje offline synchronizace
Při použití synchronizačních tabulek řídí váš klientský kód při synchronizaci místních změn s back-endem mobilní aplikace Azure. Nic je odeslána do back-endu, dokud je volání *push* místní změny. Podobně místní úložiště je naplněna nová data pouze v případě, že je volání *vyžádat* data.

* **Push**: Push je operace v kontextu synchronizace a odešle všechny změny CUD od posledního nabízení. Všimněte si, že není možné odeslat pouze změny jednotlivých tabulek, protože jinak by operace mohly být odeslány mimo pořadí. Push provede řadu volání REST do back-endu mobilní aplikace Azure, což zase upraví databázi serveru.
* **Vyžádat**: Pull se provádí na základě tabulky a lze přizpůsobit pomocí dotazu načíst pouze podmnožinu dat serveru. Sady Azure Mobile client SDK s následným vložením výsledných dat do místního úložiště.
* **Implicitní pushy:** Pokud je vyžádat je spuštěna proti tabulce, `push()` která má čekající místní aktualizace, vyžádat nejprve provede v kontextu synchronizace. Tento nabízený výraz pomáhá minimalizovat konflikty mezi změnami, které jsou již zařazeny do fronty, a novými daty ze serveru.
* **Přírůstková synchronizace**: první parametr operace vyžádat je *název dotazu,* který se používá pouze v klientovi. Pokud použijete název dotazu, který není null, azure mobile sdk provede *přírůstkovou synchronizaci*. Pokaždé, když operace vyžádat vrátí `updatedAt` sadu výsledků, nejnovější časové razítko z této sady výsledků je uloženo v místních systémových tabulkách sady SDK. Následné operace vyžádat načíst pouze záznamy po tomto časovém razítku.

  Chcete-li použít přírůstkovou synchronizaci, musí server vracet smysluplné `updatedAt` hodnoty a musí také podporovat řazení podle tohoto pole. Však vzhledem k tomu, že sada SDK přidá vlastní řazení na `orderBy` updatedAt pole, nelze použít dotaz vyžádat, který má vlastní klauzuli.

  Název dotazu může být libovolný řetězec, který zvolíte, ale musí být jedinečný pro každý logický dotaz ve vaší aplikaci.
  V opačném případě by různé operace vyžádat přepíší stejné časové razítko přírůstkové synchronizace a vaše dotazy mohou vrátit nesprávné výsledky.

  Pokud dotaz obsahuje parametr, jedním ze způsobů, jak vytvořit jedinečný název dotazu, je zahrnout hodnotu parametru.
  Například pokud filtrujete na userid, název dotazu může být následující (v C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Pokud se chcete odhlásit z `null` přírůstkové synchronizace, předejte jako ID dotazu. V tomto případě jsou všechny záznamy načteny při `PullAsync`každém volání , což je potenciálně neefektivní.
* **Vyprazdňování**: Obsah místního úložiště můžete vymazat pomocí `IMobileServiceSyncTable.PurgeAsync`.
  Vyprázdnění může být nezbytné, pokud máte zastaralá data v databázi klienta nebo pokud chcete zahodit všechny čekající změny.

  Vyčištění vymaže tabulku z místního úložiště. Pokud existují operace čekající na synchronizaci s databází serveru, vyprázdnění vyvolá výjimku, pokud není nastaven parametr *vynucení vymazání.*

  Jako příklad zastaralých dat na straně klienta předpokládejme, že v příkladu "todo list" Device1 pouze vytahuje položky, které nejsou dokončeny. Todoitem "Koupit mléko" je označen dokončena na serveru jiným zařízením. Zařízení1 však stále má "Koupit mléko" todoitem v místním obchodě, protože je pouze tahání položky, které nejsou označeny jako dokončena. Vymazání vymaže tuto zatuchlou položku.

## <a name="next-steps"></a>Další kroky
* [iOS: Povolení offline synchronizace]
* [Xamarin iOS: Povolení offline synchronizace]
* [Xamarin Android: Povolit offline synchronizaci]
* [Univerzální platforma Windows: Povolení offline synchronizace]

<!-- Links -->
[Sada SDK klienta .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Povolení offline synchronizace]: app-service-mobile-android-get-started-offline-data.md
[iOS: Povolení offline synchronizace]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Povolení offline synchronizace]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Povolit offline synchronizaci]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzální platforma Windows: Povolení offline synchronizace]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
