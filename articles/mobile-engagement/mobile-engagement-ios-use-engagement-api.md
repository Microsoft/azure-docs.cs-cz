---
title: Jak používat rozhraní API Engagement v systému iOS
description: Nejnovější iOS SDK - použití rozhraní API Engagement v systému iOS
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 189a3029449a3161da2a20f940b77a5bb63bd1ef
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Jak používat rozhraní API Engagement v systému iOS
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

Tento dokument je doplněk k dokumentu jak integrovat Engagement v systému iOS: poskytuje hloubka podrobnosti o tom, jak použít rozhraní API Engagement sestavy statistik vaší aplikace.

Mějte na paměti, že pokud chcete pouze Engagement ohlásí aplikace relací, aktivity, dojde k chybě a technické informace, pak nejjednodušší způsob, jak je aby vaše vlastní `UIViewController` objekty dědí odpovídající `EngagementViewController` třídy.

Pokud chcete informace, například pokud je třeba ohlásit určité události aplikace, chyb a úlohy, nebo pokud máte k hlášení aktivitami aplikace jiným způsobem než ten, implementované v `EngagementViewController` třídy, pak budete muset použít rozhraní API zapojení.

Rozhraní API Engagement poskytuje `EngagementAgent` třídy. Instance této třídy může načíst volání `[EngagementAgent shared]` statickou metodu (Všimněte si, že `EngagementAgent` objekt vrácený je typu singleton).

Před všechny volání rozhraní API `EngagementAgent` objekt je nutné inicializovat pomocí volání metody `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Koncepty engagementu
Následující části Upřesnit nejběžnější [koncepty Mobile Engagementu](mobile-engagement-concepts.md) pro platformu iOS.

### <a name="session-and-activity"></a>`Session` a `Activity`
*Aktivity* je obvykle spojovány s jednu obrazovku aplikace, to znamená *aktivity* spustí, když se zobrazí na obrazovce a zastaví, když je uzavřený obrazovky: to je případ, kdy je sady Engagement SDK integrovaná pomocí `EngagementViewController` třídy.

Ale *aktivity* můžete ručně kontrolovat také pomocí rozhraní API zapojení. To umožňuje rozdělit dané obrazovky v několik částí Sub – Chcete-li získat další podrobnosti o použití této obrazovky (například jak často známé a jak dlouho se používají dialogová okna v rámci této obrazovce).

## <a name="reporting-activities"></a>Sestavy aktivit
### <a name="user-starts-a-new-activity"></a>Uživatel spustí novou aktivitu
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Je třeba volat `startActivity()` pokaždé, když změny aktivity uživatelů. První volání této funkce spustí novou relaci uživatele.

### <a name="user-ends-his-current-activity"></a>Uživatel končí jeho aktuální aktivita
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Měli byste **nikdy** volání této funkce samotnými, s výjimkou, pokud chcete rozdělit jedno použití aplikace do několik relací: volání této funkce by ukončení aktuální relace okamžitě, tedy následných volání `startActivity()` by zahájit novou relaci. Tato funkce je automaticky volána sadou SDK, při zavření aplikace.
> 
> 

## <a name="reporting-events"></a>Události vytváření sestav
### <a name="session-events"></a>Události relací
Relace události se obvykle používají k hlášení akcí prováděná uživatelem během jeho relace.

**Příklad bez doplňující data:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Příklad s doplňující data:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Samostatné události
Rozporu s touto relace události lze použít samostatné události mimo kontext relace.

**Příklad:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Zasílání zpráv o chybách
### <a name="session-errors"></a>Chyby relace
Relace chyby jsou obvykle používají k hlášení chyb během jeho relace, které mají vliv uživatele.

**Příklad:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Samostatné chyby
Rozporu s touto relací chyby samostatné chyby lze mimo kontext relace.

**Příklad:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Úlohy sestav
**Příklad:**

Předpokládejme, že chcete nahlásit trvání přihlašovací proces:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Sestava chyb během úlohy
Chyby může souviset s probíhající úlohou místo má vztah k aktuální uživatelskou relaci.

**Příklad:**

Předpokládejme, že chcete vykázat chybu během procesu přihlášení:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Události během úlohy
Události může souviset s probíhající úlohou místo má vztah k aktuální uživatelskou relaci.

**Příklad:**

Předpokládejme, že máme sociálních sítí, a úlohu do sestavy používáme celkovou dobu, během kterého uživatel se připojí k serveru. Uživatel může přijímat zprávy z jeho přátel, jedná se o událost úlohy.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Další parametry
Libovolná data lze připojit k události, chyb, aktivity a úlohy.

Tato data mohou být strukturovaná, používá třída NSDictionary pro iOS.

Všimněte si, že funkce může obsahovat `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` či jiné `NSDictionary` instance.

> [!NOTE]
> Speciálním parametrem je serializováno ve formátu JSON. Pokud chcete předat různé objekty než ty, které jsou popsané výše, je nutné implementovat metodu v třídě:
> 
> -(NSString*)JSONRepresentation;
> 
> Metoda by měla vrátit reprezentaci JSON objektu.
> 
> 

### <a name="example"></a>Příklad:
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Omezení
#### <a name="keys"></a>Klíče
Každý klíč v `NSDictionary` musí odpovídat následujícímu regulárnímu výrazu:

`^[a-zA-Z][a-zA-Z_0-9]*`

Znamená to, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Funkce jsou omezeny na **1024** znaků na jednu volání (po zakódování ve formátu JSON pomocí agenta Engagement).

V předchozím příkladu je JSON odeslat na server 58 znaků:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Informace o vytváření sestav aplikace
Můžete ručně sestavy sledování informace (nebo všechny ostatní aplikace konkrétní informace) pomocí `sendAppInfo:` funkce.

Všimněte si, že tyto údaje lze odeslat přírůstkově: pouze nejnovější hodnotu pro daný klíč budou zachovány pro dané zařízení.

Události funkce, jako `NSDictionary` třída se používá k abstraktní informace o aplikaci, Všimněte si, že pole nebo dílčí slovník bude považována za plochý řetězce (pomocí serializace JSON).

**Příklad:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Omezení
#### <a name="keys"></a>Klíče
Každý klíč v `NSDictionary` musí odpovídat následujícímu regulárnímu výrazu:

`^[a-zA-Z][a-zA-Z_0-9]*`

Znamená to, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Informace o aplikaci jsou omezeny na **1024** znaků na jednu volání (po zakódování ve formátu JSON pomocí agenta Engagement).

V předchozím příkladu je JSON odeslat na server 44 znaků:

    {"birthdate":"1983-12-07","gender":"female"}
