---
title: Synchronizace offline dat v Azure Mobile Apps | Dokumentace Microsoftu
description: Reference konceptu postupu a Přehled funkce synchronizace offline dat pro Azure Mobile Apps
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
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471029"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offline synchronizace dat pro Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Co je synchronizace offline dat?
Synchronizace offline dat je klient a server funkce sady SDK služby Azure Mobile Apps, která umožňuje vývojářům snadno vytvářet aplikace, které fungují bez síťového připojení.

Pokud vaše aplikace je v režimu offline, můžete pořád vytvářet a upravovat data, která se uloží do místního úložiště. Jakmile je aplikace zpátky online, se synchronizuje místní změny s back-endu mobilní aplikace Azure. Tato funkce zároveň podporuje zjišťování konfliktů, pokud se stejný záznam změní na klientovi a back-endu. Konflikty může být zpracována klikněte buď na serveru nebo klienta.

Offline synchronizace má několik výhod:

* Zrychlete odezvu aplikace ukládáním dat ze serveru místní mezipaměti v zařízení
* Vytvářejte robustní aplikace, které budou fungovat i při problémech se sítí
* Povolit koncovým uživatelům vytvářet a upravovat data i v případě, že neexistuje žádný přístup k síti, podporovat scénáře s minimální nebo žádné připojení
* Synchronizovat data mezi různými zařízeními a je v konfliktu rozpoznat, kdy se stejný záznam se aktualizuje dvě zařízení
* Omezit využití sítě v sítích s vysokou latencí nebo účtované podle objemu dat

Následující kurzy vám ukážou, jak přidat offline synchronizace do vašich mobilních klientů pomocí Azure Mobile Apps:

* [Android: Povolení offline synchronizace]
* [Apache Cordova: Povolení offline synchronizace](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Povolení offline synchronizace]
* [Xamarin pro iOS: Povolení offline synchronizace]
* [Xamarin Android: Povolení offline synchronizace]
* [Xamarin.Forms: Offline synchronizace povolit](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzální platforma Windows: Povolení offline synchronizace]

## <a name="what-is-a-sync-table"></a>Co je tabulka synchronizace?
Pro přístup ke koncovému bodu "/ tabulky", Azure Mobile klientské sady SDK poskytují rozhraní, jako `IMobileServiceTable` (Klientská sada SDK rozhraní .NET) nebo `MSTable` (iOS klienta). Tato rozhraní API připojit přímo k back-endu mobilní aplikace Azure a selhat, pokud zařízení klienta nemá připojení k síti.

Pro podporu použití v offline režimu, by měla vaše aplikace místo toho použít *synchronizovat* rozhraní API, jako například `IMobileServiceSyncTable` (Klientská sada SDK rozhraní .NET) nebo `MSSyncTable` (iOS klienta). Všechny stejné operace CRUD (vytváření, čtení, Update, Delete), které využívají synchronizace tabulky rozhraní API, s výjimkou nyní budou přečíst z nebo zápis do *místní úložiště*. Před provedením jakékoli operace s tabulkou synchronizace místního úložiště musí být inicializována.

## <a name="what-is-a-local-store"></a>Co je místní úložiště?
Místní úložiště je vrstvu trvalosti dat v klientském zařízení. Klientské sady SDK Azure Mobile Apps poskytuje výchozí implementaci místní úložiště. Na Windows, Xamarin a s Androidem je založen na SQLite. V systémech iOS je založen na základní Data.

Použití implementace SQLite založené na Windows Phone nebo Microsoft Store, musíte nainstalovat rozšíření SQLite. Další informace najdete v tématu [Univerzální platforma Windows: Povolení offline synchronizace]. Zařízení s androidem a iOS dodávání verzí SQLite v operačním systému zařízení, takže není nutné odkazovat na vlastní verzi SQLite.

Vývojáři můžou také implementovat vlastní místní úložiště. Například pokud chcete ukládat data v šifrovaném formátu v mobilním klientovi, můžete definovat místní úložiště, který používá SQLCipher pro šifrování.

## <a name="what-is-a-sync-context"></a>Co je kontext synchronizace?
A *kontext synchronizace* je spojen s objektem mobilního klienta (například `IMobileServiceClient` nebo `MSClient`) a sleduje změny, které jsou provedeny s tabulkami synchronizace. Kontext synchronizace udržuje *operace fronty*, což zajišťuje uspořádaný seznam CUD operace (Create, Update, Delete), který je později odesláno na server.

Místní úložiště je přidružená ke kontextu synchronizace pomocí metodu initialize například `IMobileServicesSyncContext.InitializeAsync(localstore)` v [Klientská sada SDK pro .NET].

## <a name="how-sync-works"></a>Jak v režimu offline synchronizace funguje
Při použití synchronizace tabulek, váš klientský kód řídí, kdy místní změny se synchronizují s back-endu mobilní aplikace Azure. Nic posílá na back-end, dokud nedojde k volání *nabízených* místní změny. Obdobně místní úložiště se vyplní nová data pouze v případě, že volání *o přijetí změn* data.

* **Push**: nabízených oznámení je operace v kontextu synchronizace a odešle všechny změny CUD od poslední nasdílení změn. Všimněte si, že není možné odesílat pouze změny jednotlivé tabulky, protože jinak operace by mohla pošlou mimo pořadí. Push vykoná řadu volání REST pro back-endu mobilní aplikace Azure, který pak změní databázovém serveru.
* **O přijetí změn**: o přijetí změn se provádí na základě jednotlivých tabulek a je možné přizpůsobit pomocí dotaz pro načtení pouze podmnožinu dat serveru. Klientské sady SDK Azure Mobile vložte Výsledná data do místního úložiště.
* **Implicitní nabízených oznámení v ceně**: Pokud o přijetí změn se provede na tabulku, která se má čekající místní aktualizace, nejprve provede operace přijetí změn `push()` v kontextu synchronizace. Tato nabízená pomáhá minimalizovat konflikty mezi změnami, které jsou již zařazeny do fronty a nová data ze serveru.
* **Přírůstková synchronizace**: první parametr operace přijetí změn je *název dotazu* , který se používá pouze na straně klienta. Pokud použijete název dotazu jinou hodnotu než null, provede Azure Mobile SDK *Přírůstková synchronizace*. Pokaždé, když operace přijetí změn, vrátí sadu výsledků, nejnovější `updatedAt` časové razítko z této sady výsledků se ukládají do tabulek sadu SDK místního systému. Následujících vyžádanou operace načtení záznamů pouze po tomto časovém razítku.

  Používat přírůstkovou synchronizaci, musí vrátit smysluplné serveru `updatedAt` hodnoty a musí taky podporovat řazení podle tohoto pole. Ale vzhledem k tomu, že sada SDK přidá vlastní řazení v poli updatedAt, nemůžete použít dotaz o přijetí změn, který má vlastní `orderBy` klauzuli.

  Název dotazu může být libovolný řetězec, který zvolíte, ale musí být jedinečný pro každý logický dotaz ve vaší aplikaci.
  Jinak operace různých o přijetí změn by mohl přepsat stejné časové razítko Přírůstková synchronizace a dotazech může vrátit nesprávné výsledky.

  Pokud dotaz obsahuje parametr, začlenit hodnota tohoto parametru je jeden způsob, jak vytvořit dotaz jedinečný název.
  Například pokud provádíte filtrování na ID uživatele, může být název vašeho dotazu následujícím způsobem (v jazyce C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Pokud chcete vyjádřit výslovný nesouhlas Přírůstková synchronizace, předejte `null` jako ID dotazu. V tomto případě jsou načteny všechny záznamy pro každé volání do `PullAsync`, což je potenciálně neefektivní.
* **Vyprazdňování**: můžete vymazat obsah z místního úložiště pomocí `IMobileServiceSyncTable.PurgeAsync`.
  Odstranění může být nutné, pokud máte zastaralých dat v databázi klienta, nebo pokud chcete zahodit všechny neuložené změny.

  Vyprázdnění odstraní tabulku z místního úložiště. Pokud je operace čeká na synchronizaci s databází serveru, k vyprázdnění vyvolá výjimku, pokud *Vynutit vyprázdnění* je nastaven parametr.

  Jako příklad zastaralých dat na straně klienta Předpokládejme, že v příkladu "seznam úkolů" zařízení1 si vyžádá pouze položky, které nebyly dokončeny. Položka todoitem "Koupit mléka" je označen dokončena na serveru pomocí jiného zařízení. Ale zařízení1 stále má "Koupit mléka" todoitem v místním úložišti, protože táhne pouze položky, které nejsou označeny jako dokončené. Vyprázdnění vymaže této zastaralé položky.

## <a name="next-steps"></a>Další postup
* [iOS: Povolení offline synchronizace]
* [Xamarin pro iOS: Povolení offline synchronizace]
* [Xamarin Android: Povolení offline synchronizace]
* [Univerzální platforma Windows: Povolení offline synchronizace]

<!-- Links -->
[Klientská sada SDK pro .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Povolení offline synchronizace]: app-service-mobile-android-get-started-offline-data.md
[iOS: Povolení offline synchronizace]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin pro iOS: Povolení offline synchronizace]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Povolení offline synchronizace]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzální platforma Windows: Povolení offline synchronizace]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
