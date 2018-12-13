---
title: Jak používat Notification Hubs s Javou
description: Zjistěte, jak používat Azure Notification Hubs z back endem v Javě.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 3251e2ecc9171081c5128dd0782eecdf83064114
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312251"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak používat Notification Hubs z Javy
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Toto téma popisuje klíčové funkce nové plně podporované oficiální Azure Notification Hub Java SDK.
Tento projekt je projekt open source a je možné zobrazit celý kód SDK na [Java SDK].

Obecně platí, dostanete všechny funkce Notification Hubs z Javy/PHP nebo Python nebo Ruby back endem pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [rozhraní REST API Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx). Tato sada Java SDK poskytuje obálku dynamického zajišťování prostřednictvím těchto rozhraní REST v jazyce Java.

Sada SDK aktuálně podporuje:

* CRUD do Notification Hubs
* CRUD registrací
* Instalace správy
* Import/Export registrace
* Pravidelně zasílá
* Naplánované odešle
* Asynchronní operace prostřednictvím Java NIO
* Podporované platformy: APNS (iOS), GCM (Android), služby nabízených oznámení Windows (aplikace pro Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android bez služby Google)

## <a name="sdk-usage"></a>Použití sady SDK
### <a name="compile-and-build"></a>Kompilace a buildu
Použití [Maven]

Sestavení:

    mvn package

## <a name="code"></a>Kód
### <a name="notification-hub-cruds"></a>CRUDs centra oznámení
**Vytvoření NamespaceManager:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Vytvoření centra oznámení:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 NEBO

    hub = new NotificationHub("connection string", "hubname");

**Získáte Centrum oznámení:**

    hub = namespaceManager.getNotificationHub("hubname");

**Aktualizujte Centrum oznámení:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Odstraňte Centrum oznámení:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>Registrace CRUDs
**Vytvoření centra oznámení klienta:**

    hub = new NotificationHub("connection string", "hubname");

**Vytvoření registrace Windows:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

**Vytvoření registrace iOS:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

Podobně můžete vytvořit registrace pro Android (GCM), Windows Phone (MPNS) a Kindle Fire (ADM).

**Vytvořte šablonu registrace:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**Vytvoření registrace pomocí ID registrace + upsert vytvořte model**

Odebere duplicitní položky z důvodu žádné ztráty odpovědi, pokud ukládání ID registrací zařízení:

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Aktualizace registrace:**

    hub.updateRegistration(reg);

**Odstraňte registrace:**

    hub.deleteRegistration(regid);

**Registrace dotazu:**

* **Získejte jednotné registrace:**

        hub.getRegistration(regid);

* **Získáte všechny registrace v centru:**

        hub.getRegistrations();

* **Získání registrace pomocí značky:**

        hub.getRegistrationsByTag("myTag");

* **Získáte registrace kanálu:**

        hub.getRegistrationsByChannel("devicetoken");


Všechny dotazy sběr dat podporují tokeny $top a pokračování.

### <a name="installation-api-usage"></a>Instalace rozhraní API využití
Instalace rozhraní API je alternativní mechanismus pro správu registrace. Namísto zachování více registrace, které nejsou jednoduchého dotazu a může snadno provést nesprávně nebo neefektivně, je nyní možné použít objekt jedna instalace. Instalace obsahuje všechno, co potřebujete: push kanálu (token zařízení), značky, šablony, sekundární dlaždice (pro služby nabízených oznámení Windows a APNS). Není nutné volat službu k získání ID už – právě generovat identifikátor GUID nebo jakýkoli jiný identifikátor, uchovávejte na zařízení a odeslat do back-endu spolu s nabízenou kanálu (token zařízení).
Na back-endu byste měli provést pouze jedno volání: CreateOrUpdateInstallation, je plně idempotentní, takže můžete bez obav opakovat v případě potřeby.

Jako příklad pro Amazon Kindle Fire.

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Pokud chcete ji aktualizovat:

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

Pro pokročilé scénáře použijte částečnou aktualizaci schopnost, která umožňuje upravit pouze určité vlastnosti objektu instalace. Částečné aktualizace jsou podmnožinou operace oprava JSON, které je možné spustit proti objektu instalace.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Odstraňte instalace:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, Patch a Delete jsou konzistentní s Get. Požadovaná operace stačí přejde do fronty systému během volání a je proveden v pozadí. Get není určen pro scénář hlavní modulu runtime, ale pouze pro ladění a odstraňování potíží, bude úzce omezený službou.

Odeslat toku pro instalace je stejná jako registrace. Cílení na konkrétní instalaci oznámení – stačí použít značku "InstallationId: {desired-id}". V takovém případě je kód:

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

Pro jednu z několika šablon:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>Naplánovat oznámení (k dispozici pro úroveň STANDARD)
Stejné jako regulární odeslat, ale jeden další parametr - hodnotou scheduledTime, která uvádí, že pokud má se doručit oznámení. Služba přijímá libovolný bod v čase mezi now + 5 minut a nyní + 7 dní.

**Plán nativní oznámení Windows:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Import/Export (k dispozici pro úroveň STANDARD)
Někdy je potřeba provést hromadné operace s registrací. Obvykle je pro integraci s jiným systémem nebo právě masivní opravy promluvil si aktualizovat značky. Není doporučeno pro získání/aktualizaci tok použít, pokud se podílejí tisíce registrací. Funkce importu/exportu je navržená pro tento scénář. V podstatě poskytují přístup k některé kontejner objektů blob v rámci vašeho účtu úložiště jako zdroj příchozích dat a umístění pro výstup.

**Odeslání úlohy exportu:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Odeslání úlohy import úložiště:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**Počkejte, dokud se provádí úlohy:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }

**Získá všechny úlohy:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**Identifikátor URI s podpisem SAS:** Tato adresa URL je adresa URL některé souboru objektu blob nebo kontejneru objektů blob a sadu parametrů, jako jsou oprávnění a čas vypršení platnosti a podpis těchto věcí, které bylo vytvořeno s použitím klíče SAS účtu. Azure Java SDK úložiště má bohaté možnosti, jako je vytváření těchto druh identifikátorů URI. Jednoduché alternativou může trvat podívat ImportExportE2E testovací třídy (od umístění Githubu), která obsahuje základní a compact provádění podpisový algoritmus.

### <a name="send-notifications"></a>Odesílání oznámení
Objekt oznámení je jednoduše text záhlaví, některé metody nástroje pomáhají při vytváření objektů nativní a šablonu oznámení.

* **Windows Store a Windows Phone 8.1 (bez Silverlight)**

        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**

        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**

        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0 a 8.1 Silverlight**

        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Kindle Fire**

        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **Odeslání se značkami**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Odeslat do výrazu značky**

        hub.sendNotification(n, "foo && ! bar");
* **Odeslání šablony oznámení**

        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

Spouštění kódu Java by měl nyní vytvořit oznámení uvedených na cílovém zařízení.

## <a name="next-steps"></a>Další kroky
Toto téma vám ukázal vytvoření jednoduchého klienta REST Javy pro Notification Hubs. Odsud můžete:

* Stáhněte si kompletní [Java SDK], který obsahuje celý kód SDK.
* Pohrajte si s ukázky:
  * [Začínáme s Notification Hubs]
  * [Odesílání mimořádných zpráv]
  * [Odesílání lokalizovaných mimořádných zpráv]
  * [Odesílání oznámení ověřeným uživatelům]
  * [Odesílání oznámení napříč platformami ověřeným uživatelům]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Začínáme s Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Odesílání mimořádných zpráv]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Odesílání lokalizovaných mimořádných zpráv]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Odesílání oznámení ověřeným uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Odesílání oznámení napříč platformami ověřeným uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: http://maven.apache.org/

