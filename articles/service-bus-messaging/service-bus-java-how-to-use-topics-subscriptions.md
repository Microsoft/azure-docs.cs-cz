---
title: Jak používat témata služby Azure Service Bus s Javou | Microsoft Docs
description: Použijte témata a odběry Service Bus v Azure.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/17/2017
ms.author: sethm
ms.openlocfilehash: 9c2501840b3c00a63b0344d48e3225fd2c9d1620
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30927665"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Jak používat témata a odběry Service Bus s Javou

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tato příručka popisuje, jak používat témata a odběry Service Bus. Ukázky jsou napsané v jazyce Java a použít [Azure SDK pro jazyk Java][Azure SDK for Java]. Pokryté scénáře zahrnují **vytváření témat a odběrů**, **vytváření filtrů odběrů**, **odesílání zpráv do tématu**, **přijímání zpráv z odběru**, a **odstranění témat a odběrů**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatné služby Service Bus?
Témata a předplatné služby Service Bus podporují komunikační model zasílání zpráv *publikování/přihlášení*. Součásti distribuované aplikace při používání témat a předplatných nekomunikují navzájem přímo. Místo toho si zprávy vyměňují prostřednictvím tématu, které slouží jako zprostředkovatel.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Na rozdíl od front služby Service Bus, ve kterých každou zprávu zpracuje jeden spotřebitel, témata a předplatné nabízejí komunikaci v podobě „1:N“ a používají vzor publikování/přihlášení. K jednomu tématu můžete zaregistrovat několik předplatných. Při odeslání zprávy do tématu bude zpráva zpřístupněná všem předplatným, aby ji nezávisle zpracovala.

Předplatné tématu se podobá virtuální frontě, která obdrží kopii zpráv, které byly odeslány do tématu. Volitelně můžete zaregistrovat pravidla filtru pro téma na základě za předplatné, které umožňuje filtru nebo omezit příjem zpráv pro téma podle předplatných tématu přijme.

Témata a odběry Service Bus umožní škálování ke zpracování velkého počtu zpráv mezi velký počet uživatelů a aplikací.

## <a name="create-a-service-namespace"></a>Vytvoření oboru názvů služby
Chcete-li začít používat témata a odběry Service Bus v Azure, musíte nejdřív vytvořit *obor názvů*, který poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Ujistěte se, že máte nainstalovanou [Azure SDK pro jazyk Java] [ Azure SDK for Java] před vytvořením této ukázce. Pokud používáte Eclipse, můžete nainstalovat [nástrojů Azure pro Eclipse] [ Azure Toolkit for Eclipse] , který obsahuje sadu Azure SDK pro jazyk Java. Poté můžete přidat **knihovny Microsoft Azure Libraries for Java** do projektu:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Přidejte následující `import` příkazů do horní části souboru Java:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Přidání knihovny Azure Libraries for Java do cestu k sestavení a její zahrnutí do vašeho projektu nasazení sestavení.

## <a name="create-a-topic"></a>Vytvoření tématu
Operace správy témat sběrnice Service Bus je možné provádět prostřednictvím **ServiceBusContract** třídy. A **ServiceBusContract** objektu je vytvořený pomocí odpovídající konfiguraci, který zapouzdřuje tokenu SAS s oprávněními ke správě, a **ServiceBusContract** třída je jediný bod komunikace s Azure.

**ServiceBusService** třída poskytuje metody pro vytvoření, výčet a odstranění témat. Následující příklad ukazuje způsob **ServiceBusService** objekt lze použít k vytvoření téma s názvem `TestTopic`, k oboru názvů s názvem `HowToSample`:

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

Způsoby na **TopicInfo** které umožňují vlastnosti tématu, které chcete nastavit (například: Chcete-li nastavit výchozí time to live (TTL) hodnotu se použije na zprávy odeslané do tématu). Následující příklad ukazuje, jak vytvořit téma s názvem `TestTopic` s maximální velikostí 5 GB:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Můžete použít **listTopics** metodu **ServiceBusContract** objekty, které chcete zkontrolovat, pokud téma se zadaným názvem již existuje v rámci oboru názvů služby.

## <a name="create-subscriptions"></a>Vytvořte odběr
Odběry témat taky jsou vytvořeny pomocí **ServiceBusService** třídy. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv odesílaných do virtuální fronty odběru.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
V případě, že při vytváření nového odběru nezadáte žádný filtr, použije se jako výchozí filtr **MatchAll**. Když **MatchAll** filtr se používá, všechny zprávy publikované do tématu jsou umístěny do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem „AllMessages“ a použije výchozí filtr **MatchAll**.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete taky vytvořit filtry, které umožňují obor, který by měl zprávy odeslané do tématu objeví v konkrétním odběru tématu.

Nejflexibilnější filtr, který odběry podporují je [SqlFilter][SqlFilter], který implementuje je podmnožinou SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazy, které lze použít s filtrem SQL, projděte si [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxe.

Následující příklad vytvoří odběr s názvem `HighMessages` s [SqlFilter] [ SqlFilter] objekt, který vybere jen zprávy, které mají vlastní **MessageNumber** Vlastnost větší než 3:

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

Podobně platí, tento příklad vytvoří odběr s názvem `LowMessages` s [SqlFilter] [ SqlFilter] objekt, který vybere jen zprávy, které mají **MessageNumber** Vlastnost menší než nebo rovné 3:

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

Když je nyní odeslána zpráva `TestTopic`, vždy se dodá příjemci `AllMessages` předplatného a selektivně příjemcům přihlásit k odběru `HighMessages` a `LowMessages` odběry (v závislosti na obsah zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
K odeslání zprávy do tématu Service Bus, vaše aplikace získává **ServiceBusContract** objektu. Následující kód ukazuje, jak odeslat zprávu `TestTopic` předtím vytvořili v tématu `HowToSample` obor názvů:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Zprávy odeslané do témat Service Bus jsou instance [BrokeredMessage] [ BrokeredMessage] třídy. [BrokeredMessage][BrokeredMessage]* objekty mají sadu standardních metod (například **setLabel** a **TimeToLive**), slovník, který se používá k ukládání vlastní vlastnosti specifické pro aplikace a tělo s libovolnými aplikačními daty. Aplikace může tělo zprávy nastavit podle předá jakýkoli serializovatelný objekt do konstruktoru objektu [BrokeredMessage][BrokeredMessage]a odpovídající **DataContractSerializer** se pak použije k serializaci objektu. Alternativně **java.io.InputStream** lze zadat.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv, které mají `TestTopic` **MessageSender** jsme získali v předchozím fragmentu kódu.
Poznámka: Jak **MessageNumber** hodnotu vlastnosti každé zprávy se liší na iteraci smyčky (Tato hodnota určuje, které odběry ji přijmou):

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

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Neexistuje žádné omezení počtu zpráv držených v tématu, ale je omezena na celková velikost zpráv držených v tématu. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Jak přijmout zprávy z odběru
Chcete-li přijímat zprávy z odběru, použijte **ServiceBusContract** objektu. Přijaté zprávy můžou pracovat ve dvou různých režimech: **ReceiveAndDelete** a **PeekLock** (výchozí).

Při použití **ReceiveAndDelete** režimu přijímat je jednorázová operace – to znamená, když Service Bus přijme požadavek čtení zprávy, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. **ReceiveAndDelete** režimu je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat se zpráva nezpracuje, pokud dojde k selhání. Zvažte například scénář, ve kterém spotřebitel vyšle požadavek na přijetí a pak dojde k chybě před zpracováním ho. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, zprávu, která se spotřebovala před havárii ho byl vynechán.

V **PeekLock** režimu, zobrazí se změní na dvě fáze operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání **odstranit** na přijatou zprávu. Když Service Bus uvidí **odstranit** volání, označí zprávu jako spotřebovávanou a odstraní ji z tématu.

Následující příklad ukazuje, jak můžete obdržet zprávy a zpracování pomocí **PeekLock** (výchozím režimu). V příkladu se provede smyčku a zpracuje zprávy v `HighMessages` předplatné a potom se ukončí, pokud nejsou žádné další zprávy (případně ji lze nastavit pro čekání nové zprávy).

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat **unlockMessage** metoda na přijatou zprávu (místo **deleteMessage** metoda). To způsobí, že Service Bus zprávu odemkne v tomto tématu a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu přidružené zpráva uzamčená v tématu, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak Service Bus zprávu automaticky odemkne a udělá z něj k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **deleteMessage** požadavku a potom zprávy je víckrát do aplikace odešle znovu. Tento proces se často nazývá **zpracování nejméně jednou**; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát stejnou zprávu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá **getMessageId** metoda zprávy, která zůstává konstantní mezi pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Primární způsob, jak odstranit témat a odběrů je použití **ServiceBusContract** objektu. Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy front Service Bus, najdete v části [Service Bus fronty, témata a odběry] [ Service Bus queues, topics, and subscriptions] Další informace.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
