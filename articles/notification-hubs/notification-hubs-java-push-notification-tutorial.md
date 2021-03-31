---
title: Jak používat Azure Notification Hubs s jazykem Java
description: Naučte se používat Azure Notification Hubs z back-endu Java.
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
ms.custom: devx-track-java
ms.openlocfilehash: 09553f587916e8204541b36f259a450d72c1b270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87322937"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Použití Notification Hubs z Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Toto téma popisuje klíčové funkce nové plně podporované oficiální sady SDK pro centra oznámení Azure.
Tento projekt je open source projekt a celý kód sady SDK si můžete prohlédnout v [sadě Java SDK].

Obecně platí, že ke všem funkcím Notification Hubs máte přístup z back-endu Java/PHP/Python/Ruby pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [Notification HUBS REST API](/previous-versions/azure/reference/dn223264(v=azure.100)). Tato sada Java SDK poskytuje na těchto rozhraních REST v jazyce Java tenké obálky.

Sada SDK aktuálně podporuje:

* CRUD při Notification Hubs
* CRUD při registracích
* Správa instalace
* Registrace importu/exportu
* Pravidelná odeslání
* Plánovaná odeslání
* Asynchronní operace přes Java NIO
* Podporované platformy: APNS (iOS), FCM (Android), WNS (Windows Store apps), MPNS (Windows Phone), ADM (Amazon Kindle požár), Baidu (Android bez služeb Google)

## <a name="sdk-usage"></a>Využití sady SDK

### <a name="compile-and-build"></a>Kompilace a sestavení

Použití [Maven]

Sestavení:

```cmd
mvn package
```

## <a name="code"></a>Kód

### <a name="notification-hub-cruds"></a>CRUD centra oznámení

**Vytvořte NamespaceManager:**

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

**Získat centrum oznámení:**

```java
hub = namespaceManager.getNotificationHub("hubname");
```

**Aktualizace Centra oznámení:**

```java
hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
hub = namespaceManager.updateNotificationHub(hub);
```

**Odstranit centrum oznámení:**

```java
namespaceManager.deleteNotificationHub("hubname");
```

### <a name="registration-cruds"></a>Registrace CRUD

**Vytvoření klienta centra oznámení:**

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

**Vytvořit registraci iOS:**

```java
AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

Podobně můžete vytvořit registrace pro Android (FCM), Windows Phone (MPNS) a Kindle Fire (ADM).

**Vytváření registrací šablon:**

```java
WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
reg.getHeaders().put("X-WNS-Type", "wns/toast");
hub.createRegistration(reg);
```

**Vytvoření registrací pomocí vytvoření ID registrace + vzor Upsert:**

Odebere duplicity z důvodu jakýchkoli ztracených odpovědí, pokud se v zařízení ukládají ID registrace:

```java
String id = hub.createRegistrationId();
WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
hub.upsertRegistration(reg);
```

**Registrace aktualizací:**

```java
hub.updateRegistration(reg);
```

**Odstranit registrace:**

```java
hub.deleteRegistration(regid);
```

**Registrace dotazů:**

* **Získat jednu registraci:**

```java
hub.getRegistration(regid);
```

* **Získat všechny registrace v centru:**

```java
hub.getRegistrations();
```

* **Získat registrace se značkou:**

```java
hub.getRegistrationsByTag("myTag");
```

* **Získat registrace podle kanálu:**

```java
hub.getRegistrationsByChannel("devicetoken");
```

Všechny dotazy kolekce podporují tokeny $top a pokračování.

### <a name="installation-api-usage"></a>Využití rozhraní API instalace

Rozhraní API pro instalaci je alternativním mechanismem pro správu registrací. Místo udržování více registrů, které nejsou triviální a je možné je snadno provést nesprávně nebo neefektivně, je nyní možné použít jeden objekt instalace.

Instalace obsahuje všechno, co potřebujete: kanál push (token zařízení), značky, šablony, sekundární dlaždice (pro WNS a APNS). Službu nemusíte volat, abyste mohli získat ID. můžete tak vygenerovat GUID nebo jakýkoli jiný identifikátor, ponechat ho v zařízení a poslat do back-endu společně s kanálem push (token zařízení).

V back-endu byste měli provést pouze jedno volání `CreateOrUpdateInstallation` . je plně idempotentní, takže v případě potřeby to zkuste znovu.

Příklad pro službu Amazon Kindle Fire:

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

V případě pokročilých scénářů použijte možnost částečné aktualizace, která umožňuje změnit pouze konkrétní vlastnosti objektu instalace. Částečná aktualizace je podmnožinou operací opravy JSON, které lze spustit proti instalačnímu objektu.

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

`CreateOrUpdate`, `Patch` a `Delete` jsou nakonec konzistentní s `Get` . Vaše požadovaná operace při volání přejde do fronty systému a spustí se na pozadí. Operace Get není navržena pro hlavní scénář modulu runtime, ale pouze pro účely ladění a řešení potíží je tato služba pevně omezená.

Tok odeslání pro instalace je stejný jako u registrací. Chcete-li směrovat oznámení na konkrétní instalaci, stačí použít značku InstallationId: {požadovaného-ID}. V tomto případě kód je:

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

Stejné jako běžné odeslání, ale s jedním dalším parametrem-scheduledTime, který říká, kdy by mělo být doručeno oznámení. Služba akceptuje libovolný časový okamžik mezi teď + 5 minutami a teď + 7 dny.

**Naplánování nativního oznámení systému Windows:**

```java
Calendar c = Calendar.getInstance();
c.add(Calendar.DATE, 1);
Notification n = Notification.createWindowsNotification("WNS body");
hub.scheduleNotification(n, c.getTime());
```

### <a name="importexport-available-for-standard-tier"></a>Import/Export (k dispozici pro úroveň STANDARD)

Je možné, že budete muset provést hromadnou operaci s registracemi. Obvykle se jedná o integraci s jiným systémem nebo obrovskými opravami, které tyto značky aktualizují. V případě, že jsou zapojeny tisíce registrací, nedoporučujeme používat tok Get/Update. Funkce importu/exportu v systému je navržena tak, aby pokryla scénář. Jako zdroj příchozích dat a umístění pro výstup budete poskytovat přístup k kontejneru objektů BLOB v účtu úložiště.

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

**Počkejte na dokončení úlohy:**

```java
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}
```

**Získat všechny úlohy:**

```java
List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
```

**Identifikátor URI s podpisem SAS:**

 Tato adresa URL je adresa URL souboru objektu BLOB nebo kontejneru objektů BLOB a sady parametrů, jako jsou oprávnění a doba vypršení platnosti, a navíc signatura všech těchto věcí pomocí klíče SAS účtu. Azure Storage Java SDK obsahuje bohatě funkční možnosti včetně vytváření těchto identifikátorů URI. Jako jednoduchá alternativa se podíváme na `ImportExportE2E` třídu testu (z umístění GitHubu), která má základní a kompaktní implementaci podpisového algoritmu.

### <a name="send-notifications"></a>Odeslat oznámení

Objekt oznámení je jednoduše tělo se záhlavími. některé obslužné metody vám pomůžou při sestavování nativních objektů oznámení a šablon.

* **Windows Store a Windows Phone 8,1 (ne Silverlight)**

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

* **Windows Phone 8,0 a 8,1 Silverlight**

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

* **Kindle požár**

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

* **Výraz odeslání do značky**

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

Spuštění kódu Java by teď mělo vytvořit oznámení na cílovém zařízení.

## <a name="next-steps"></a><a name="next-steps"></a>Další kroky

Toto téma ukazuje, jak vytvořit jednoduchého klienta Java REST pro Notification Hubs. Odsud můžete:

* Stáhněte si úplnou [sadu Java SDK]obsahující celý kód sady SDK.
* Začněte s ukázkami:
  * [Začínáme s Notification Hubs]
  * [Poslat novinky]
  * [Odeslat lokalizované novinky]
  * [Odesílání oznámení ověřeným uživatelům]
  * [Odesílání oznámení mezi platformami ověřeným uživatelům]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Začínáme s Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Poslat novinky]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Odeslat lokalizované novinky]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Odesílání oznámení ověřeným uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Odesílání oznámení mezi platformami ověřeným uživatelům]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
