---
title: Azure Service Bus – nejčastější dotazy (FAQ) | Dokumentace Microsoftu
description: Odpovědi na některé nejčastější dotazy o Azure Service Bus.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: 312d92994e4932e854ec77681da2d4dbd14da2aa
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391997"
---
# <a name="service-bus-faq"></a>Nejčastější dotazy k Service Bus

Tento článek popisuje některé nejčastější dotazy o Microsoft Azure Service Bus. Můžete také navštívit [Azure nejčastějších dotazech k podpoře](https://azure.microsoft.com/en-us/support/faq/) obecné informace o Azure ceny a podporu.

## <a name="general-questions-about-azure-service-bus"></a>Obecné dotazy týkající se služby Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co je Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) je asynchronní zasílání zpráv Cloudová platforma, která vám umožní odesílat data mezi systémy oddělení. Společnost Microsoft nabízí jako služba, což znamená, že není potřeba hostovat vlastní hardware použít tuto funkci.

### <a name="what-is-a-service-bus-namespace"></a>Co je obor názvů služby Service Bus?
A [obor názvů](service-bus-create-namespace-portal.md) poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace. Vytvoření oboru názvů, je potřeba pomocí služby Service Bus a je jedním z prvních kroků v části Začínáme.

### <a name="what-is-an-azure-service-bus-queue"></a>Co je fronty služby Azure Service Bus?
A [frontu služby Service Bus](service-bus-queues-topics-subscriptions.md) je entita, ve kterém jsou zprávy uloženy. Fronty jsou užitečné, pokud máte více aplikací nebo více částí distribuované aplikace, které potřebují komunikovat mezi sebou. Fronta je podobný Centrum distribuce více produktů (zprávy) jsou přijata a potom je odešlete z tohoto umístění.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Co jsou Azure Service Bus, témat a odběrů?
Téma lze vizualizovat jako fronty a při použití více předplatných, stane se modelu; bohatší zasílání zpráv v podstatě jeden mnoho komunikační nástroj. Tento model publikování a přihlášení k odběru (nebo *pub/sub*) umožňuje aplikaci, která odesílá zprávy do tématu, s několika předplatnými, aby tuto zprávu obdrží několik aplikací.

### <a name="what-is-a-partitioned-entity"></a>Co je rozdělené entity?
Konvenční fronty nebo tématu je zpracovat zprostředkovatele a uložená do jednoho úložiště pro přenos zpráv. Podporováno pouze v základní a standardní úrovní, zasílání zpráv [dělená fronta nebo téma](service-bus-partitioning.md) se ukládají do více úložišť pro přenos zpráv a zpracovat více zprostředkovatele zpráv. Tato funkce znamená, že celkovou propustnost dělená fronta nebo téma je už omezený výkon zprostředkovatele nebo úložiště zpráv. Kromě toho dočasnému výpadku úložiště nevykresluje dělené fronty nebo tématu není k dispozici.

Řazení není zajištěno, že při použití segmentované entity. V případě, že oddíl je k dispozici, můžete stále odesílání a příjem zpráv z ostatních oddílů.

 Dělené entity v již nejsou podporovány [SKU úrovně Premium](service-bus-premium-messaging.md). 

## <a name="best-practices"></a>Osvědčené postupy
### <a name="what-are-some-azure-service-bus-best-practices"></a>Jaké jsou některé osvědčené postupy Azure Service Bus?
Zobrazit [osvědčené postupy pro zlepšení výkonu pomocí služby Service Bus] [ Best practices for performance improvements using Service Bus] – Tento článek popisuje, jak optimalizovat výkon při výměně zpráv.

### <a name="what-should-i-know-before-creating-entities"></a>Co bych měl vědět před vytvořením entity?
Následující vlastnosti frontu a téma jsou neměnné. Při zřizování entity, jak tyto vlastnosti nelze změnit bez vytvoření nové entity nahrazení, zvažte toto omezení.

* Dělení
* Relace
* Vyhledávání duplicit
* Expresní entity

## <a name="pricing"></a>Ceny
Tato část odpovídá na některé časté otázky ke správě služby Service Bus Cenová struktura.

[Služby Service Bus, ceny a fakturace](service-bus-pricing-billing.md) článku vysvětluje měřiče ve službě Service Bus. Konkrétní informace o možnostech cen Service Bus najdete v tématu [podrobnosti o cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Můžete také navštívit [nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/en-us/support/faq/) obecné informace o cenách Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Jak se vám účtovat za služby Service Bus?
Podrobnější informace o cenách služby Service Bus, najdete v článku [podrobnosti o cenách služby Service Bus][Pricing overview]. Kromě ceny uvedené bude vám účtována přidružené datové přenosy pro výchozí přenos dat mimo datové centrum, ve které je zřízená vaší aplikace.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Jaké použití služby Service Bus je v souladu s přenosy dat? Co je?
Bez poplatků, stejně jako přenosy příchozích dat je k dispozici přenosů dat v rámci dané oblasti Azure. Přenos dat mimo oblast se vztahují poplatky za výchozí přenos dat, které najdete [tady](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Služby Service Bus účtovat úložiště?
Ne, Service Bus neúčtují úložiště. Je však kvótu omezení maximální množství dat, které můžete nastavit jako trvalý, za fronty nebo tématu. Zobrazit další nejčastější dotazy.

## <a name="quotas"></a>Kvóty

Seznam a kvótám služby Service Bus, najdete v článku [přehled kvóty služby Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Má služby Service Bus jakékoli využití kvóty?
Ve výchozím nastavení pro každý cloud a nastaví služba Microsoft agregační měsíční využití kvóty, vypočtené ve všech předplatných zákazníka. Pokud budete potřebovat vyšší limity, můžete kontaktovat zákaznickou podporu o vašich potřebách a tyto limity odpovídajícím způsobem nastavit kdykoli. Agregované využití kvóty pro služby Service Bus, je 5 miliard zpráv za měsíc.

I když společnost Microsoft si vyhrazuje právo zakázat účet zákazníka, který překročil jeho využití kvót v daném měsíci, e-mailová oznámení se odesílají a více pokusům ke kontaktování zákazníka před provedením jakékoli akce. Zákazníci překročení kvóty dál zodpovídají za poplatky na rámec kvót.

Stejně jako u jiných služeb v Azure Service Bus vynucuje sadu konkrétní kvót, aby se zajistilo, že veletrh využití prostředků. Další podrobnosti o těchto kvót v můžete najít [přehled kvóty služby Service Bus][Quotas overview].

## <a name="troubleshooting"></a>Řešení potíží
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované API služby Azure Service Bus a jejich doporučené akce?
Seznam možných výjimkách služby Service Bus najdete v tématu [výjimky přehled][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co je sdílený přístupový podpis a jazyky, které podporují generování podpis?
Sdílené přístupové podpisy jsou mechanismus ověřování založený na SHA-256 zabezpečené hodnoty hash nebo identifikátory URI. Informace o tom, jak generovat vlastní podpisy v Node, PHP, Java a C\#, najdete v článku [sdílené přístupové podpisy] [ Shared Access Signatures] článku.

## <a name="subscription-and-namespace-management"></a>Správa předplatného a obor názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak migrovat obor názvů do jiného předplatného Azure?

Můžete přesunout oboru názvů z jednoho předplatného Azure na jiný, buď pomocí [webu Azure portal](https://portal.azure.com) nebo příkazů Powershellu. Aby bylo možné provést operaci, musí být obor názvů už aktivní. Uživatelské příkazy spustíte, musíte být správcem na zdrojovém i cílovém předplatná.

#### <a name="portal"></a>Portál

Chcete-li migrovat obory názvů služby Service Bus do jiného předplatného pomocí webu Azure portal, postupujte podle pokynů [tady](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Následující sekvence příkazů Powershellu přesune oboru názvů z jednoho předplatného Azure do jiného. K provedení této operace, obor názvů, musí již být aktivní a uživatel, který spouští příkazy Powershellu, musíte být správcem na zdrojovém i cílovém předplatná.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Další postup
Další informace o Service Bus, najdete v následujících článcích:

* [Úvod do Azure Service Bus Premium (příspěvek na blogu)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Úvod do Azure Service Bus úrovně Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Přehled služby Service Bus](service-bus-messaging-overview.md)
* [Přehled architektury služby Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
