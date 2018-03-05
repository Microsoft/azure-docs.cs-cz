# [Dokumentace k zasílání zpráv služby Service Bus](index.md)

# Přehled
## [Co je Service Bus Messaging?](service-bus-messaging-overview.md)
## [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
## [Architektura služby Service Bus](service-bus-architecture.md)
## [Nejčastější dotazy](service-bus-faq.md)

# Šablony Rychlý start
## [Vytvoření oboru názvů](service-bus-create-namespace-portal.md)
## Použití front
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Použití témat a předplatných
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

# Postup
## Plánování a návrh
### [Identita spravované služby (Preview)](service-bus-managed-service-identity.md)
### [Řízení přístupu na základě rolí (Preview)](service-bus-role-based-access-control.md)
### [Zasílání zpráv úrovně Premium](service-bus-premium-messaging.md)
### [Srovnání front Azure a front Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Optimalizace výkonu](service-bus-performance-improvements.md)
### [Geografické zotavení po havárii a geografická replikace](service-bus-geo-dr.md)
### [Asynchronní zasílání zpráv a vysoká dostupnost](service-bus-async-messaging.md)
### [Zvládání výpadků a havárií](service-bus-outages-disasters.md)

## Vývoj
### [Sestavení vícevrstvé aplikace Service Bus](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
### Zpracování zpráv
#### [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md)
#### [Zprávy, datové části a serializace](service-bus-messages-payloads.md)
#### [Přenosy zpráv, zámky a vyrovnání](message-transfers-locks-settlement.md)
#### [Určování pořadí zpráv a časová razítka](message-sequencing.md)
#### [Vypršení platnosti zpráv (hodnota TTL)](message-expiration.md)
#### [Zpracování zpráv pomocí Azure Event Gridu](service-bus-to-event-grid-integration-concept.md)
#### [Příklady Azure Event Gridu](service-bus-to-event-grid-integration-example.md)
### [Ověřování a autorizace](service-bus-authentication-and-authorization.md)
#### [Migrace z ACS do SAS](service-bus-migrate-acs-sas.md)
#### [Ověřování sdílenými přístupovými podpisy](service-bus-sas.md)
### [Akce a filtry témat](topic-filters.md)
### [Dělené fronty a témata](service-bus-partitioning.md)
### [Relace zpráv](message-sessions.md)
### AMQP
#### [Přehled AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service a AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Průvodce protokolem AMQP](service-bus-amqp-protocol-guide.md)
#### [Operace na základě odpovědí na požadavky AMQP](service-bus-amqp-request-response.md)
### Pokročilé funkce
#### [Fronty nedoručených zpráv](service-bus-dead-letter-queues.md)
#### [Předběžné načtení zpráv](service-bus-prefetch.md)
#### [Detekce duplicitních zpráv](duplicate-detection.md)
#### [Čítače zpráv](message-counters.md)
#### [Odložení zpráv](message-deferral.md)
#### [Procházení zpráv](message-browsing.md)
#### [Spojení entit s automatickým přeposíláním](service-bus-auto-forwarding.md)
#### [Zpracování transakcí](service-bus-transactions.md)
#### [Implementace spárovaného oboru názvů](service-bus-paired-namespaces.md)
### [Kompletní trasování a diagnostika](service-bus-end-to-end-tracing.md)
## Spravovat
### [Monitorování služby Service Bus s využitím monitorování Azure](service-bus-metrics-azure-monitor.md)
### [Knihovny pro správu Service Bus](service-bus-management-libraries.md)
### [Diagnostické protokoly](service-bus-diagnostic-logs.md)
### [Pozastavení a opětovná aktivace entit zasílání zpráv](entity-suspend.md)
### [Použití šablon Azure Resource Manageru](service-bus-resource-manager-overview.md)
#### [Vytvoření oboru názvů](service-bus-resource-manager-namespace.md)
#### [Vytvoření oboru názvů a fronty](service-bus-resource-manager-namespace-queue.md)
#### [Vytvoření oboru názvů s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
#### [Vytvoření autorizačního pravidla pro obor názvů a frontu](service-bus-resource-manager-namespace-auth-rule.md)
#### [Vytvoření oboru názvů s tématem, předplatným a pravidlem](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Použití Azure PowerShellu ke zřizování entit](service-bus-manage-with-ps.md)

# Referenční informace
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Výjimky](service-bus-messaging-exceptions.md)
## [Kvóty](service-bus-quotas.md)
## [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
## [Syntaxe SQLRuleAction](service-bus-messaging-sql-rule-action.md)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blog](https://blogs.msdn.microsoft.com/servicebus/)
## [Fóra na webu MSDN](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Ceny](https://azure.microsoft.com/pricing/details/service-bus/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Podrobnosti o cenách](service-bus-pricing-billing.md)
## [Ukázky](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


