---
title: Jak používat centra oznámení s Javou
description: Naučte se používat Azure Notification Hubs z Java back-end.
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
ms.openlocfilehash: 88e3ab3cc03cc1e760672120bc5c484af1ba4722
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778371"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Jak používat centra oznámení z Java
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Toto téma popisuje klíčové funkce nové plně podporované oficiální Azure oznámení centra sady Java SDK. Tento projekt je projekt open source a je možné zobrazit celý kód SDK na [sady Java SDK]. 

Obecně platí, můžete ke všem funkcím centra oznámení z Java/PHP nebo Python nebo Ruby back-end pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [rozhraní API REST centra oznámení](http://msdn.microsoft.com/library/dn223264.aspx). Tuto sadu Java SDK poskytuje dynamické obálku přes tato rozhraní REST v jazyce Java. 

Sada SDK podporuje aktuálně:

* CRUD v Notification Hubs 
* CRUD při registraci
* Instalace správy
* Import a Export registrace
* Regulární zasílá
* Naplánované zasílá
* Asynchronní operace prostřednictvím Java NIO
* Podporované platformy: APNS (iOS), služby GCM (Android), WNS (aplikace pro Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle aktivuje), Baidu (bez služeb Google Android) 

## <a name="sdk-usage"></a>Použití sady SDK
### <a name="compile-and-build"></a>Kompilace a sestavení
Použití [Maven]

K sestavení:

    mvn package

## <a name="code"></a>Kód
### <a name="notification-hub-cruds"></a>CRUDs centra oznámení
**Vytvořte NamespaceManager:**

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

**Vytvořte registrace systému Windows:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**Vytvořte registrace iOS:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

Podobně můžete vytvořit registrace pro Android (GCM), Windows Phone (MPNS) a Kindle ještě efektivněji (ADM).

**Vytvořte šablonu registrace:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**Vytvořit registrace pomocí vytvořte ID registrace + upsert vzor**

Odebere duplicitní položky z důvodu libovolné ztraceny odpovědi při ukládání registrace ID na zařízení:

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Aktualizujte registrace:**

    hub.updateRegistration(reg);

**Odstraňte registrace:**

    hub.deleteRegistration(regid);

**Registrace dotazu:**

* **Získáte jeden registrace:**
  
        hub.getRegistration(regid);

* **V centru získáte všechny registrace:**
  
        hub.getRegistrations();

* **Získání registrace pomocí značky:**
  
        hub.getRegistrationsByTag("myTag");

* **Získáte registrací kanál:**
  
        hub.getRegistrationsByChannel("devicetoken");


Všechny dotazy na kolekce podporují $top a pokračování tokenů.

### <a name="installation-api-usage"></a>Instalace rozhraní API využití
Instalace rozhraní API je alternativní mechanismus pro správu registrace. Místo zachování více registrace, které nejsou v netriviálních a může se snadno provést neoprávněně nebo neefektivnímu, je možné použít objekt JEDNOTNOU instalaci. Instalace obsahuje všechno, co potřebujete: push kanál (token zařízení), značky, šablony, sekundární dlaždice (pro WNS a APNS). Nemusíte volání služby získat ID už – právě generovat identifikátor GUID nebo jakýkoli jiný identifikátor, uložte jej na zařízení a poslat váš back-end společně s nabízené kanál (token zařízení). Na back-end, použijte pouze v jediném volání: CreateOrUpdateInstallation, je plně idempotent, takže Nebojte se v případě potřeby opakujte.

Jako příklad pro Amazon Kindle ještě efektivněji.

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Pokud chcete aktualizovat: 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

Pro pokročilé scénáře pomocí funkce částečné aktualizace, která umožňuje změnit pouze konkrétní vlastnosti objektu instalace. Částečné aktualizace je podmnožinou JSON oprava operace, které můžete spustit instalaci objektu.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Odstraňte instalace:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, Patch a Delete jsou nakonec byl konzistentní se Get. Požadovaná operace právě přejde do fronty systému během volání a je proveden v pozadí. Get není určen pro scénář hlavní runtime, ale jenom pro ladění a odstraňování potíží se je úzce omezen pomocí služby.

Postup odeslání pro instalace je stejné jako registrace. Cíl oznámení na konkrétní instalaci-použijte značky "InstallationId: {desired-id}". Pro tento případ je kód:

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

Pro jednu z několika šablon:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>Naplánovat oznámení (k dispozici pro úroveň STANDARD)
Stejné jako regulární send, ale jeden další parametr - scheduledTime, s informacemi o tom, kdy bude doručena oznámení. Služba přijímá libovolného bodu čas mezi nyní + 5 minut a nyní + 7 dní.

**Plán Windows nativní oznámení:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Import a Export (k dispozici pro úroveň STANDARD)
Někdy je nutné provést hromadné operace s registrací. Obvykle je pro integraci v rámci jiného systému nebo stejně masivní opravu. Tím vyjádříte aktualizaci značek. Není doporučeno použít toku Get nebo aktualizovat, pokud se podílejí tisíce registrace. Funkce importu a exportu je navržené tak, aby pokrýval scénáře. V podstatě poskytnete přístup na některé kontejner objektů blob v rámci účtu úložiště jako zdroj příchozích dat a umístění pro výstup.

**Odeslání úlohy exportu:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Odeslání úlohy importu:**

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

**Získání všech úloh:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**Identifikátor URI s SAS podpis:** tato adresa URL je adresa URL některé objektu blob souboru nebo kontejneru objektů blob a sadu parametrů, jako jsou oprávnění a čas vypršení platnosti a podpis všechny tyto věci, které jsou vytvořené pomocí klíče SAS účtu. Azure SDK pro jazyk Java úložiště má bohaté možnosti, včetně vytváření takové druh identifikátorů URI. Jako alternativní jednoduché může trvat podívejte se na ImportExportE2E třídy testovací (z githubu umístění), který má základní a compact implementace podpisový algoritmus.

### <a name="send-notifications"></a>Odesílání oznámení
Objekt oznámení je jednoduše text se záhlavími, některé metody nástroj pomoci při vytváření objektů nativní a šablony oznámení.

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
* **Kindle ještě efektivněji**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **Poslat značky**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Poslat výraz značky**       
  
        hub.sendNotification(n, "foo && ! bar");
* **Odeslat oznámení šablony**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

Spuštěním kódu Java by měl nyní vytvořit oznámení, které jsou na cílovém zařízení.

## <a name="next-steps"></a>Další kroky
Toto téma ukázal, jak vytvořit jednoduché Java REST klienta pro centra oznámení. Odsud můžete:

* Stáhnout kompletní [sady Java SDK], který obsahuje celý kód SDK. 
* Přehrání s ukázky:
  * [Začínáme s Notification Hubs]
  * [Odesílání novinek]
  * [Odesílání lokalizované novinek]
  * [Odesílání oznámení ověřeným uživatelům]
  * [Odesílání oznámení napříč platformami ověřeným uživatelům]

[sady Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Začínáme s Notification Hubs]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Odesílání novinek]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Odesílání lokalizované novinek]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Odesílání oznámení ověřeným uživatelům]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Odesílání oznámení napříč platformami ověřeným uživatelům]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

