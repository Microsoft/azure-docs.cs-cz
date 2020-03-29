---
title: Jak používat Azure Notification Hubs s Javou
description: Přečtěte si, jak používat Azure Notification Hubs z back-endu java.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d48973cc7c5ed1fc7ae3f96128d488f3f1df3a05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263859"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak používat centra oznámení z Javy

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Toto téma popisuje klíčové funkce nové plně podporované oficiální Azure Notification Hub Java SDK.
Tento projekt je open-source projekt a můžete zobrazit celý kód SDK na [Java SDK].

Obecně platí, že můžete přistupovat ke všem funkcím centra oznámení z back-endu Java/PHP/Python/Ruby pomocí rozhraní REST centra oznámení, jak je popsáno v rozhraní [REST ROZHRANÍ Centra oznámení](https://msdn.microsoft.com/library/dn223264.aspx)MSDN . Tato sada Java SDK poskytuje tenký obal přes tyto rozhraní REST v jazyce Java.

Sada SDK aktuálně podporuje:

* CRUD na oznamovacích centrech
* CRUD o registracích
* Správa instalace
* Registrace importu a exportu
* Pravidelné odeslání
* Plánované odeslání
* Asynchronní operace přes Java NIO
* Podporované platformy: APNS (iOS), FCM (Android), WNS (aplikace pro Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android bez služeb Google)

## <a name="sdk-usage"></a>Využití sady SDK

### <a name="compile-and-build"></a>Kompilace a sestavení

Použít [Maven]

Chcete-li vytvořit:

    mvn package

## <a name="code"></a>kód

### <a name="notification-hub-cruds"></a>Crud centra oznámení

**Vytvořte správce oboru názvů:**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**Vytvořit centrum oznámení:**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 NEBO

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Centrum získání oznámení:**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**Aktualizovat centrum oznámení:**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**Odstranit centrum oznámení:**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>Registrace CRUD

**Vytvoření klienta Centra oznámení:**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**Vytvořit registraci systému Windows:**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**Vytvoření registrace iOS:**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

Podobně můžete vytvořit registrace pro Android (FCM), Windows Phone (MPNS) a Kindle Fire (ADM).

**Vytvořit registrace šablon:**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**Vytvořit registrace pomocí vytvořit ID registrace + upsert vzor:**

Odstraní duplikáty z důvodu všech ztracených odpovědí, pokud ukládání ID registrace na zařízení:

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**Aktualizovat registrace:**

    ```java
    hub.updateRegistration(reg);
    ```

**Odstranit registrace:**

    ```java
    hub.deleteRegistration(regid);
    ```

**Registrace dotazů:**

* **Získejte jednu registraci:**

    ```java
    hub.getRegistration(regid);
    ```

* **Získejte všechny registrace v centru:**

    ```java
    hub.getRegistrations();
    ```

* **Získejte registrace se značkou:**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **Získejte registrace podle kanálu:**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

Všechny dotazy na kolekci podporují $top a tokeny pokračování.

### <a name="installation-api-usage"></a>Využití rozhraní API instalace

Instalační rozhraní API je alternativní mechanismus pro správu registrace. Namísto udržování více registrací, které nejsou triviální a může být snadno provést nesprávně nebo neefektivně, je nyní možné použít jeden objekt instalace.

Instalace obsahuje vše, co potřebujete: push kanál (token zařízení), značky, šablony, sekundární dlaždice (pro WNS a APNS). Nemusíte volat službu získat ID už - stačí generovat GUID nebo jakýkoli jiný identifikátor, uchovávat jej na zařízení a odeslat do back-endu spolu s push kanál (token zařízení).

V back-endu byste měli provést `CreateOrUpdateInstallation`pouze jedno volání ; je plně idempotentní, takže se v případě potřeby zopakujte.

Jako příklad pro Amazon Kindle Fire:

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

Pokud ji chcete aktualizovat:

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

Pro pokročilé scénáře použijte funkci částečné aktualizace, která umožňuje upravit pouze určité vlastnosti instalačního objektu. Částečná aktualizace je podmnožinou operací opravy JSON, které lze spustit proti objektu Installation.

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

Odstranit instalaci:

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`, `Patch`a `Delete` jsou nakonec `Get`v souladu s . Požadovaná operace pouze přejde do systémové fronty během volání a je spuštěna na pozadí. Get není určen pro hlavní scénář runtime, ale pouze pro účely ladění a řešení potíží, je pevně omezen službou.

Odeslat tok pro instalace je stejný jako pro registrace. Chcete-li cílit na oznámení na konkrétní instalaci - stačí použít značku "InstallationId:{desired-id}". V tomto případě je kód:

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

Pro jednu z několika šablon:

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>Oznámení plánu (k dispozici pro úroveň STANDARD)

Stejné jako pravidelné odesílání, ale s jedním dalším parametrem - scheduledTime, který říká, kdy by mělo být oznámení doručeno. Služba přijímá jakýkoli časový bod mezi dneškem + 5 minutami a nyní + 7 dny.

**Naplánování nativního oznámení systému Windows:**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>Import/export (k dispozici pro úroveň STANDARD)

Možná budete muset provést hromadnou operaci proti registracím. Obvykle je to pro integraci s jiným systémem nebo masivní opravu aktualizovat značky. Nedoporučujeme používat tok získat nebo aktualizovat, pokud se jedná o tisíce registrací. Možnost importu a exportu systému je navržena tak, aby pokrývala scénář. Budete poskytovat přístup ke kontejneru objektů blob pod vaším účtem úložiště jako zdroj příchozích dat a umístění pro výstup.

**Odeslat úlohu exportu:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Odeslat úlohu importu:**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**Počkejte, až bude práce hotová:**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**Získejte všechny úlohy:**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**Identifikátor URI s podpisem SAS:**

 Tato adresa URL je adresa URL objektu blob nebo kontejneru objektů blob plus sada parametrů, jako jsou oprávnění a čas vypršení platnosti plus podpis všech těchto věcí provedených pomocí klíče SAS účtu. Azure Storage Java SDK má bohaté možnosti, včetně vytvoření těchto identifikátorů URI. Jako jednoduchá alternativa se `ImportExportE2E` podívejte na testovací třídu (z umístění GitHub), která má základní a kompaktní implementaci algoritmu podepisování.

### <a name="send-notifications"></a>Odeslat oznámení

Objekt Oznámení je jednoduše tělo se záhlavími, některé metody nástroje pomáhají při vytváření objektů nativní a šablony oznámení.

* **Windows Store a Windows Phone 8.1 (jiné než Silverlight)**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 a 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Oheň**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **Odeslat do značek**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **Odeslat do výrazu značky**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **Odeslat oznámení šablony**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

Spuštění kódu Jazyka Java by nyní mělo vést k oznámení, které se zobrazí na cílovém zařízení.

## <a name="next-steps"></a><a name="next-steps"></a>Další kroky

Toto téma vám ukázalo, jak vytvořit jednoduchého klienta Java REST pro centra oznámení. Odsud můžete:

* Stáhněte si celou [java sdk ,]která obsahuje celý kód Sady SDK.
* Hrát si se vzorky:
  * [Začínáme s centry oznámení]
  * [Posílat nejnovější zprávy]
  * [Odeslat lokalizované nejnovější zprávy]
  * [Odesílání oznámení ověřeným uživatelům]
  * [Odesílání oznámení pro různé platformy ověřeným uživatelům]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Začínáme s centry oznámení]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Posílat nejnovější zprávy]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Odeslat lokalizované nejnovější zprávy]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Odesílání oznámení ověřeným uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Odesílání oznámení pro různé platformy ověřeným uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
