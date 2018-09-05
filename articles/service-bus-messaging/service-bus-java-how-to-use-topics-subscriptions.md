---
title: Jak používat témata služby Azure Service Bus pomocí Javy | Dokumentace Microsoftu
description: Použijte témata a odběry Service Bus v Azure.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/17/2017
ms.author: spelluru
ms.openlocfilehash: 0dc0ebd94abaa9dacd685034a46da1a7f204bfff
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700067"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Jak používat témata a odběry Service Bus pomocí Javy

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tato příručka popisuje, jak používat témata a odběry Service Bus. Ukázky jsou napsané v jazyce Java a použití [sady Azure SDK pro Javu][Azure SDK for Java]. Mezi popsané scénáře patří **vytváření témat a odběrů**, **vytváření filtrů odběrů**, **odesílání zpráv do tématu**, **přijetí zprávy z odběru**, a **odstranění témat a odběrů**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatné služby Service Bus?
Témata a předplatné služby Service Bus podporují komunikační model zasílání zpráv *publikování/přihlášení*. Součásti distribuované aplikace při používání témat a předplatných nekomunikují navzájem přímo. Místo toho si zprávy vyměňují prostřednictvím tématu, které slouží jako zprostředkovatel.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Na rozdíl od front služby Service Bus, ve kterých každou zprávu zpracuje jeden spotřebitel, témata a předplatné nabízejí komunikaci v podobě „1:N“ a používají vzor publikování/přihlášení. K jednomu tématu můžete zaregistrovat několik předplatných. Při odeslání zprávy do tématu bude zpráva zpřístupněná všem předplatným, aby ji nezávisle zpracovala.

Předplatné tématu se podobá virtuální frontě, která obdrží kopii zpráv, které byly odeslány do tématu. Máte taky možnost zaregistrovat pravidla filtrování určitého tématu na bázi předplatného, který umožňuje filtrovat nebo omezit přijímaných zpráv pro téma podle předplatných tématu.

Témata a odběry Service Bus umožňují škálovat pro zpracování velkého počtu zpráv ve velkém počtu uživatelů a aplikací.

## <a name="create-a-service-namespace"></a>Vytvoření oboru názvů služby
Chcete-li začít používat témata a odběry Service Bus v Azure, musíte nejdřív vytvořit *obor názvů*, který poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Ujistěte se, že jste nainstalovali [sady Azure SDK pro Javu] [ Azure SDK for Java] před vytvořením této ukázky. Pokud používáte Eclipse, můžete nainstalovat [sady Azure Toolkit pro Eclipse] [ Azure Toolkit for Eclipse] , který obsahuje sadu Azure SDK pro Javu. Poté můžete přidat **knihovny Microsoft Azure Libraries for Java** do projektu:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Přidejte následující `import` příkazy do horní části souboru Java:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Přidejte knihovny Azure Libraries for Java do cesty sestavení a zahrnout do nasazení sestavení vašeho projektu.

## <a name="create-a-topic"></a>Vytvoření tématu
Operace správy témat sběrnice Service Bus je možné provádět prostřednictvím **ServiceBusContract** třídy. A **ServiceBusContract** objekt je vytvořen s odpovídající, který zapouzdřuje token SAS s oprávněními ke správě, konfiguraci a **ServiceBusContract** třídy je jediný bod komunikace s Azure.

**ServiceBusService** třída poskytuje metody pro vytvoření, výčet a odstranění témat. Následující příklad ukazuje způsob **ServiceBusService** objekt lze použít k vytvoření tématu s názvem `TestTopic`, se obor názvů s názvem `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
      "HowToSample",
      "RootManageSharedAccessKey",
      "SAS_key_value",
      ".servicebus.windows.net"
      );

ServiceBusContract service = ServiceBusService.create(config);
TopicInfo topicInfo = new TopicInfo("TestTopic");
try  
{
    CreateTopicResult result = service.createTopic(topicInfo);
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Způsoby na **TopicInfo** umožňující vlastnosti tématu, které chcete nastavit (například: Chcete-li nastavit výchozí time to live (TTL) hodnotu se použije na zprávy odeslané do tématu). Následující příklad ukazuje, jak vytvořit téma s názvem `TestTopic` s maximální velikostí 5 GB:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Můžete použít **listTopics** metoda **ServiceBusContract** objekty ke kontrole, jestli téma se zadaným názvem již existuje v rámci oboru názvů služby.

## <a name="create-subscriptions"></a>Vytvořit odběry
Odběry témat taky jsou vytvořeny pomocí **ServiceBusService** třídy. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv odesílaných do virtuální fronty odběru.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
V případě, že při vytváření nového odběru nezadáte žádný filtr, použije se jako výchozí filtr **MatchAll**. Když **MatchAll** se používá filtr, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem „AllMessages“ a použije výchozí filtr **MatchAll**.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete taky vytvořit filtry, které vám umožní obor, který měla zprávy odeslané do tématu zobrazit v konkrétním odběru tématu.

Nejflexibilnější filtr předplatných je [SqlFilter][SqlFilter], která implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o výrazech, které lze použít s filtrem SQL, přečtěte si [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxe.

Následující příklad vytvoří odběr s názvem `HighMessages` s [SqlFilter] [ SqlFilter] objekt, který vybere jen zprávy, které mají vlastní **MessageNumber** Vlastnost je větší než 3:

```java
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Obdobně následující příklad vytvoří odběr s názvem `LowMessages` s [SqlFilter] [ SqlFilter] objekt, který vybere jen zprávy, které mají **MessageNumber** Vlastnost menší než nebo rovné 3:

```java
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Když je teď odeslána zpráva `TestTopic`, vždy se doručí do příjemcům `AllMessages` předplatného a selektivně příjemcům `HighMessages` a `LowMessages` předplatných (v závislosti na obsah zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Odeslat zprávu do tématu služby Service Bus, vaše aplikace získá **ServiceBusContract** objektu. Následující kód ukazuje, jak odeslat zprávu pro `TestTopic` do dříve vytvořeného tématu `HowToSample` obor názvů:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Zprávy odeslané do témat Service Bus jsou instance [BrokeredMessage] [ BrokeredMessage] třídy. [BrokeredMessage][BrokeredMessage]* objekty mají sadu standardních metod (například **setLabel** a **TimeToLive**), slovník, který se používá k ukládání vlastní vlastnosti specifické pro aplikace a tělo libovolnými aplikačními daty. Aplikace může tělo zprávy nastavit tak předá jakýkoli serializovatelný objekt do konstruktoru [BrokeredMessage][BrokeredMessage]a odpovídající **DataContractSerializer** se pak použije k serializaci objektu. Můžete také **java.io.InputStream** lze zadat.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `TestTopic` **MessageSender** jsme získali v předchozím fragmentu kódu.
Poznámka: Jak **MessageNumber** hodnota vlastnosti každé zprávy se liší v iteraci smyčky (Tato hodnota určuje, které odběry ji přijmou):

```java
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Neexistuje žádné omezení na počet zpráv držených v tématu, ale celková velikost zpráv držených v tématu, je omezený. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Jak přijmout zprávy z odběru
Chcete-li přijímat zprávy z odběru, použijte **ServiceBusContract** objektu. Přijaté zprávy můžete pracovat ve dvou různých režimech: **ReceiveAndDelete** a **PeekLock** (výchozí).

Při použití **ReceiveAndDelete** režimu přijímat je jednorázová operace – to znamená, když Service Bus přijme požadavek čtení pro zprávu, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. **ReceiveAndDelete** režimu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat možnost, není zpracovává zprávu, pokud dojde k chybě. Zvažte například scénář, ve kterém spotřebitel požadavek na přijetí a poté dojde k chybě před její zpracování. Vzhledem k tomu, že Service Bus označil zprávu jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, byl vynechán zprávu, která se spotřebovala před pádem vynechá.

V **PeekLock** režimu, zobrazí se stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním **odstranit** na přijatou zprávu. Když Service Bus uvidí **odstranit** volání, označí zprávu jako spotřebovávanou a odstraní ji z tématu.

Následující příklad ukazuje, jak lze přijímat zprávy a zpracovaná pomocí **PeekLock** (výchozím režimu). V příkladu se provádí smyčku a zpracovává zprávy v `HighMessages` předplatné a potom se ukončí, když nejsou žádné další zprávy (případně můžete nastavit na nové zprávy).

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat **unlockMessage** metoda na přijatou zprávu (místo **deleteMessage** metoda). To způsobí, že služba Service Bus zprávu odemkne v tomto tématu a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu zpráva uzamčená v tomto tématu, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), Service Bus zprávu automaticky odemkne a díky tomu k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **deleteMessage** požadavku a pak je víckrát do aplikace při restartování. Tento proces se často nazývá **zpracování nejméně jednou**; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá **getMessageId** metoda zprávy, která zůstává konstantní pokusu o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Primárním způsob, jak odstranění témat a odběrů je určený **ServiceBusContract** objektu. Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy front Service Bus, najdete v článku [fronty služby Service Bus, témat a odběrů] [ Service Bus queues, topics, and subscriptions] Další informace.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
