---
title: Jak používat Engagement rozhraní API na Windows Phone Silverlight
description: Jak používat Engagement rozhraní API na Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 03673924ee7734fcf6f1f4f7c744616844fdc87a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Jak používat Engagement rozhraní API na Windows Phone Silverlight
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento dokument je doplněk k dokumentu [jak integrovat Mobile Engagement v aplikaci Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Poskytuje hloubka podrobnosti o tom, jak použít rozhraní API Engagement sestavy statistik vaší aplikace.

Pokud chcete Engagement ohlásí aplikace relací, aktivity, dojde k chybě a technické informace, pak se nejjednodušší způsob, jak provést všechny vaše `PhoneApplicationPage` dílčí třídy dědí `EngagementPage` třídy.

Pokud chcete informace, například pokud je třeba ohlásit určité události aplikace, chyb a úlohy, nebo pokud máte k hlášení aktivitami aplikace jiným způsobem než ten, implementované v `EngagementPage` třídy, pak budete muset použít rozhraní API zapojení.

Rozhraní API Engagement poskytuje `EngagementAgent` třídy. Můžete přístup k těmto metodám prostřednictvím `EngagementAgent.Instance`.

I v případě, že modul agenta nebyla inicializována, každé volání rozhraní API je odložení a budou spuštěny znovu, pokud je agent k dispozici.

## <a name="engagement-concepts"></a>Koncepty engagementu
Následující části Upřesnit koncepty Mobile Engagementu pro platformu Windows Phone.

### <a name="session-and-activity"></a>`Session` a `Activity`
*Aktivity* je obvykle spojovány s jednu stránku aplikace, to znamená *aktivity* spustí, když se zobrazí na stránce a zastaví, když je uzavřený stránky: je tomu tak při sady Engagement SDK je integrován s použitím `EngagementPage` třídy.

Ale *aktivity* můžete ručně kontrolovat také pomocí rozhraní API zapojení. To umožňuje rozdělit danou stránku v několik částí Sub – Chcete-li získat další podrobnosti o použití této stránce (například jak často známé a jak dlouho se používají dialogová okna v rámci této stránce).

## <a name="reporting-activities"></a>Sestavy aktivit
### <a name="user-starts-a-new-activity"></a>Uživatel spustí novou aktivitu
#### <a name="reference"></a>Referenční informace
            void StartActivity(string name, Dictionary<object, object> extras = null)

Je třeba volat `StartActivity()` pokaždé, když změny aktivity uživatelů. První volání této funkce spustí novou relaci uživatele.

> [!IMPORTANT]
> Sada SDK automaticky volejte metodu EndActivity při ukončení aplikace. Proto důrazně volat metodu StartActivity vždy, když aktivita uživatele změny a nikdy volat metodu EndActivity, protože voláním této metody vynutí ukončit aktuální relaci.
> 
> 

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Uživatel končí jeho aktuální aktivita
#### <a name="reference"></a>Referenční informace
            void EndActivity()

Je třeba volat `EndActivity()` alespoň jednou po dokončení uživatele poslední aktivita. Tento příkaz informuje sady Engagement SDK, že je uživatel aktuálně nečinnosti, a že relaci uživatele, který je třeba ukončit jednou časový limit relace vyprší (při volání `StartActivity()` předtím, než vyprší časový limit relace, relace se jednoduše pokračuje).

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Úlohy sestav
### <a name="start-a-job"></a>Spustit úlohu
#### <a name="reference"></a>Referenční informace
            void StartJob(string name, Dictionary<object, object> extras = null)

Úlohy můžete sledovat certains úlohy v časovém intervalu.

#### <a name="example"></a>Příklad:
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Ukončit úlohu
#### <a name="reference"></a>Referenční informace
            void EndJob(string name)

Jakmile úloha sledovány v rámci úlohy byla ukončena, by měly volat metodu EndJob pro tuto úlohu zadáním název úlohy.

#### <a name="example"></a>Příklad:
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Události vytváření sestav
Je k dispozici tři typy událostí:

* Samostatné události
* Události relací
* Události úlohy

### <a name="standalone-events"></a>Samostatné události
#### <a name="reference"></a>Referenční informace
            void SendEvent(string name, Dictionary<object, object> extras = null)

Samostatné události může dojít mimo kontext relace.

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Události relací
#### <a name="reference"></a>Referenční informace
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Relace události se obvykle používají k hlášení akcí prováděná uživatelem během jeho relace.

#### <a name="example"></a>Příklad:
**Bez dat:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**S daty:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Události úlohy
#### <a name="reference"></a>Referenční informace
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Události úlohy jsou obvykle používají k hlášení akcí prováděná uživatelem během úlohy.

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Zasílání zpráv o chybách
Je k dispozici tři typy chyb:

* Samostatné chyby
* Chyby relace
* Chyby úloh

### <a name="standalone-errors"></a>Samostatné chyby
#### <a name="reference"></a>Referenční informace
            void SendError(string name, Dictionary<object, object> extras = null)

Rozporu s touto relací chyb může dojít k chybám samostatné mimo kontext relace.

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Chyby relace
#### <a name="reference"></a>Referenční informace
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Relace chyby jsou obvykle používají k hlášení chyb během jeho relace, které mají vliv uživatele.

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Chyby úloh
#### <a name="reference"></a>Referenční informace
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Chyby může souviset s probíhající úlohou místo má vztah k aktuální uživatelskou relaci.

#### <a name="example"></a>Příklad:
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Generování sestav havárií
Agent nabízí dvě metody jak nakládat s havárií.

### <a name="send-an-exception"></a>Odeslat výjimku
#### <a name="reference"></a>Referenční informace
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Příklad:
Výjimku kdykoli můžete odeslat voláním:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Můžete také volitelný parametr ukončit relaci engagement ve stejnou dobu než odesílání havárii. Chcete-li tak učinit, zavolejte:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Pokud tak učiníte, úlohy a relace budou uzavřeny právě po odeslání havárii.

### <a name="send-an-unhandled-exception"></a>Odeslat k neošetřené výjimce
#### <a name="reference"></a>Referenční informace
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement také poskytuje metodu pro odeslání neošetřených výjimek. To je obzvláště užitečná při použití uvnitř obslužné rutiny události UnhandledException silverlight.

Tato metoda bude **vždy** ukončit relaci engagement a úlohy po volání.

#### <a name="example"></a>Příklad:
Můžete ho implementovat vlastní obslužnou rutinu UnhandledException (hlavně pokud mají zakázáno automatické hlášení funkci Engagement vytváření sestav). Například v `Application_UnhandledException` metodu `App.xaml.cs` souboru:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>Referenční informace
            void OnActivated(ActivatedEventArgs e)

Když uživatel přejde dopředu, mimo aplikaci, po deaktivováno událost se vyvolá, operační systém se pokusí aplikaci převést do stavu neaktivní. Aplikace je označení. V tomto procesu je aplikace ukončena, ale některá data o stavu aplikace a na jednotlivých stránkách v aplikaci je zachovaná.

Je nutné vložit `EngagementAgent.Instance.OnActivated(e)` v `Application_Activated` metoda ze souboru App.xaml.cs resetovat Engagement agenta, když aplikace bylo označeno jako neplatné.

### <a name="example"></a>Příklad:
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>ID zařízení
            String GetDeviceId()

Voláním této metody můžete získat id zařízení v engagement.

## <a name="extras-parameters"></a>Parametry funkce
Libovolná data lze připojit k události, k chybě, aktivity nebo úlohy. Tato data můžete strukturu pomocí slovníku. Klíče a hodnoty můžou být jakéhokoli typu.

Data funkce se serializují, takže pokud chcete, která se přidá vlastní typ funkce budete muset přidat kontraktu dat pro tento typ.

### <a name="example"></a>Příklad:
Vytvoříme novou třídu "Osoba".

            using System.Runtime.Serialization;

            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Potom přidáme `Person` instance na další.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Když vložíte jiné typy objektů, ujistěte se, že jejich metodu ToString() je implementována vrátit lidského čitelných řetězců.
> 
> 

### <a name="limits"></a>Omezení
#### <a name="keys"></a>Klíče
Každý klíč v objektu musí odpovídat následujícímu regulárnímu výrazu:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Znamená to, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Funkce jsou omezeny na **1024** znaků na jednu volání.

## <a name="reporting-application-information"></a>Informace o vytváření sestav aplikace
### <a name="reference"></a>Referenční informace
            void SendAppInfo(Dictionary<object, object> appInfos)

Můžete ručně sestavy sledování informace (nebo všechny ostatní aplikace konkrétní informace) pomocí SendAppInfo() funkce.

Všimněte si, že tyto údaje lze odeslat přírůstkově: pouze nejnovější hodnotu pro daný klíč budou zachovány pro dané zařízení. Jako funkce událostí použití slovníku\<objektu, objekt\> připojit – informace.

### <a name="example"></a>Příklad:
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Omezení
#### <a name="keys"></a>Klíče
Každý klíč v objektu musí odpovídat následujícímu regulárnímu výrazu:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Znamená to, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Informace o aplikaci jsou omezeny na **1024** znaků na jednu volání.

V předchozím příkladu je JSON odeslat na server 44 znaků:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Protokolování
### <a name="enable-logging"></a>Povolení protokolování
Sada SDK se dá nakonfigurovat k vytvoření protokolů testování v konzole IDE.
Tyto protokoly nejsou ve výchozím nastavení aktivované. Chcete-li přizpůsobit tím, aktualizujte vlastnost `EngagementAgent.Instance.TestLogEnabled` na jednu z hodnota dostupná z `EngagementTestLogLevel` výčtu pro instanci:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
