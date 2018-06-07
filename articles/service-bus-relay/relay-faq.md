---
title: Nejčastější dotazy k Azure předávání | Microsoft Docs
description: Získejte odpovědi na některé nejčastější dotazy týkající se předávání přes Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: sethm
ms.openlocfilehash: 1795c1ec0b4129e3c99fff3fc893148e191ce83e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641488"
---
# <a name="azure-relay-faqs"></a>Nejčastější dotazy k Azure předávání

Tento článek obsahuje odpovědi na některé nejčastější dotazy (FAQ) o [předávání přes Azure](https://azure.microsoft.com/services/service-bus/). Obecné Azure – ceny a podporu informace najdete v tématu [podporu nejčastější dotazy k Azure](https://azure.microsoft.com/support/faq/).

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-relay"></a>Co je Azure Relay?
[Azure předávací služba](relay-what-is-it.md) usnadňuje hybridní aplikace tím, že vám pomáhá s více bezpečně vystavit služby, které se nacházejí v podnikové síti, veřejnému cloudu. Služby můžou zpřístupnit, aniž byste museli otevřít spojení ve firewallu a bez nutnosti provádět nežádoucí změny infrastruktury podnikové sítě.

### <a name="what-is-a-relay-namespace"></a>Co je předávání názvů?
A [obor názvů](relay-create-namespace-portal.md) je kontejner oboru, který můžete použít pro adresování prostředků předávání v rámci vaší aplikace. Musíte vytvořit obor názvů používat předávání. Toto je jedna z první kroky v části Začínáme.

### <a name="what-happened-to-service-bus-relay-service"></a>Co se stalo službě předávání přes Service Bus?
Dříve pojmenované služby předávání přes Service Bus se nyní označuje jako [WCF předávání](relay-wcf-dotnet-get-started.md). Můžete tuto službu využívat jako obvykle. Funkce hybridní připojení je aktualizovaná verze služby, která je byla transplantované ze služby Azure BizTalk Services. Předávání WCF a hybridní připojení i nadále podporována.

## <a name="pricing"></a>Ceny
Tato část odpovídá na některé nejčastější dotazy týkající se předávání cenové struktury. Také se zobrazí [Azure podporu nejčastější dotazy k](https://azure.microsoft.com/support/faq/) pro obecné Azure informace o cenách. Úplné informace o cenách předávání najdete v tématu [Service Bus podrobnosti o cenách][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak se vám účtují pro hybridní připojení a přenosu WCF?
Úplné informace o cenách předávání, najdete v článku [hybridní připojení a předávací službu WCF] [ Pricing overview] tabulky v Service Bus, podrobnosti o stránce s cenami. Kromě ceny uvedené na této stránce budou se vám účtovat pro přenosy přidružená data o sazbách za odchozí mimo datové centrum, ve kterém je aplikace zřízený.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak se fakturuje pro hybridní připojení?
Tady jsou tři scénáře fakturace příklad pro hybridní připojení:

*   Scénář 1:
    *   Máte jeden naslouchací proces, jako je například instanci správce hybridního připojení, nainstalována a spuštěna nepřetržitě za každý měsíc.
    *   Odešlete 3 GB dat napříč připojením v měsíci. 
    *   Celkový poplatek je 5.
*   Scénář 2:
    *   Máte jeden naslouchací proces, jako je například instanci správce hybridního připojení, nainstalována a spuštěna nepřetržitě za každý měsíc.
    *   Odešlete 10 GB dat napříč připojením v měsíci.
    *   Celkový poplatek je 7,50 $. To je 5 pro připojení a prvních 5 GB + 2.50 pro další 5 GB dat.
*   Scénář 3:
    *   Máte dvě instance, A a B, správce hybridního připojení nainstalována a spuštěna nepřetržitě za každý měsíc.
    *   Odešlete 3 GB dat napříč připojení A v měsíci.
    *   Odešlete 6 GB dat napříč připojení B v měsíci.
    *   Celkový poplatek je $10.50. To je 5 pro připojení A + 5 pro připojení B + 0,50 (pro šesté gigabajt připojení B).

Všimněte si, že použité v ukázkách ceny platí pouze během období preview hybridní připojení. Ceny se může měnit při obecné dostupnosti hybridní připojení.

### <a name="how-are-hours-calculated-for-relay"></a>Jak jsou vypočítávány hodin pro předávání?

Předávání WCF je k dispozici pouze v oborech názvů úrovně Standard. Ceny a [připojení kvóty](../service-bus-messaging/service-bus-quotas.md) pro předávání, jinak hodnota nezměnily. To znamená, že předávání dál budou účtovat podle počtu zpráv (není operace) a hodiny přenosu. Další informace najdete v tématu ["Hybridní připojení a WCF předávací službu"](https://azure.microsoft.com/pricing/details/service-bus/) tabulky na stránce s cenami podrobnosti.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co když máte více než jeden naslouchací proces připojené ke konkrétnímu propojení?
V některých případech může mít jeden předávání více připojených naslouchací procesy. Relé je považováno za otevřené, když předávání alespoň jeden naslouchací proces je připojený ke svému. Přidání naslouchací procesy výsledky otevřete předávání do hodiny další přenosu. Počet předávání odesílatelé (klientů, které vyvolání nebo odesíláte zprávy do předávací) připojených k relé nemá vliv na výpočtu hodiny přenosu.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Výpočtu měření zprávy pro předávací službu WCF
(**Vztahuje se pouze na předávací službu WCF. Zprávy nejsou náklady pro hybridní připojení.** )

Obecně platí se počítají fakturovatelný zprávy pro předávání pomocí stejného postupu, který se používá pro zprostředkované entity (fronty, témata a odběry), které jsou popsané. Existují však určité významné rozdíly.

Odesílání zprávy pro předávání přes Service Bus je zpracovaná jako "úplná prostřednictvím" předávací naslouchací proces, který obdrží zprávu poslat. Nepovažuje se za operaci odeslání k předávání přes Service Bus, za nímž následuje doručení pro naslouchací proces předávání. Požadavek odpověď stylu služby vyvolání (až 64 KB) proti relé naslouchací proces má za následek dvě fakturovatelný zprávy: jednu fakturovatelný zprávu žádosti a jednu fakturovatelný zprávu pro odpověď (za předpokladu, že odpověď je také 64 KB nebo menší). To se liší od používání fronty, které zprostředkovává mezi klientem a služby. Pokud používáte fronty které zprostředkovává mezi klientem a služby, vyžaduje stejného vzoru požadavku a odpovědi odesílání požadavku do fronty, následuje dequeue nebo doručení z fronty ve službě. Následují odeslání odpovědi do jiné fronty a dequeue nebo doručení z této fronty, do klienta. Pomocí stejné předpoklady velikost v rámci (až 64 KB), 4 zprávy fakturovatelný výsledkem vzoru zprostředkovaného fronty. Bude platit pro dvakrát počet zpráv implementovat stejný vzor, které můžete provádět i pomocí předávání. Samozřejmě existují výhody fronty k dosažení tohoto vzoru, jako je například odolnost a vyrovnávání zátěže. Tyto výhody může justify další náklady.

Předávání, které jsou otevřené pomocí **netTCPRelay** vazby WCF považovat zprávy, ne jako jednotlivé zprávy, ale jako datový proud dat odesílaných prostřednictvím systému. Při použití této vazbě odesílatele a naslouchací proces mít přehled o rámcovacích jednotlivých zpráv odesílaných i přijímaných. Pro předává využívající **netTCPRelay** vazby, všechna data je považován za datového proudu pro výpočet fakturovatelný zprávy. V takovém případě Service Bus vypočítá celkové množství dat odesílané nebo přijímané prostřednictvím každé jednotlivé předávání na základě 5 minut. Potom vydělí celkové množství dat podle 64 KB k určení počtu fakturovatelný zprávy pro tento předávání během tohoto časového období.

## <a name="quotas"></a>Kvóty
| Název kvóty | Rozsah |  Poznámky | Hodnota |
| --- | --- | --- | --- |
| Souběžné moduly pro naslouchání na přenos |Entita |Odeslání dalších žádostí o další připojení odmítnuty a volající kód přijme výjimku. |25 |
| Připojení souběžných předávání za všechny koncové body předávání v oboru názvů služby |Obor názvů |- |5 000 |
| Předávání koncových bodů na jeden obor názvů služby |Obor názvů |- |10 000 |
| Velikost zprávy [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) a [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) předává |Obor názvů |Odmítne příchozí zprávy, které překračují těchto kvót a volající kód přijme výjimku. |64 kB |
| Velikost zprávy [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) předává |Obor názvů |Velikost zprávy není nijak omezena. |Unlimited |

### <a name="does-relay-have-any-usage-quotas"></a>Má předávání žádné kvóty využití?
Ve výchozím nastavení pro všechny cloudové služby společnosti Microsoft nastaví agregační měsíční využití kvóta, která je vypočtená ve všech předplatných zákazníka. Chápeme, že v některých případech potřeb může tato omezení překročí. Kontaktovat oddělení služeb zákazníkům kdykoli, proto jsme pochopení potřeb a odpovídajícím způsobem nastavit tyto limity. Agregační využití kvóty pro Service Bus, jsou následující:

* miliardy 5 zprávy
* hodiny přenosu milionů 2

I když jsme si vyhrazuje právo zakázat účet, který překračuje jeho měsíční využití kvóty, poskytujeme e-mailová oznámení a jsme zviditelnit několik pokusí kontaktovat zákazník před provedením jakékoli akce. Zákazníci, které překračují těchto kvót jsou stále zodpovědní za nadbytečné poplatky.

### <a name="naming-restrictions"></a>Omezení pojmenování
Název oboru názvů předávání musí být mezi 6 až 50 znaků.

## <a name="subscription-and-namespace-management"></a>Správa předplatného a obor názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak migrace oboru do jiného předplatného Azure

Pro přesun oboru názvů z jedno předplatné do jiného předplatného, můžete použít [portál Azure](https://portal.azure.com) nebo použít příkazy prostředí PowerShell. Obor názvů přesunout do jiného předplatného, musí být obor názvů již aktivní. Uživatel s oprávněním správce na zdrojovém i cílovém odběry musí být uživatel, který spouští příkazy.

#### <a name="azure-portal"></a>Azure Portal

Pomocí portálu Azure pro migraci oborů názvů předávání přes Azure z jedno předplatné do jiného předplatného naleznete v části [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Pomocí prostředí PowerShell pro přesun oboru názvů z jedno předplatné do jiného předplatného, použijte následující sekvence příkazů. K provedení této operace, obor názvů musí již být aktivní, a uživatel, který spouští příkazy prostředí PowerShell musí být uživatel s oprávněním správce na zdrojovém i cílovém odběry.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Řešení potíží
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Co jsou některé výjimky generované Azure předávání přes rozhraní API a doporučené postupy, které můžete provést?
Popis běžné výjimky a doporučované akce můžete provést, najdete v části [předávání výjimky][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co je sdílený přístupový podpis a jazyky, které slouží ke generování podpis?
Podpisy sdíleného přístupu (SAS) se mechanismus ověřování na základě zabezpečeného hodnoty hash SHA-256 nebo identifikátory URI. Informace o tom, jak generovat vlastní podpisy v uzlu, PHP, Java, C a C# najdete v tématu [ověření sběrnice s podpisy sdíleného přístupu][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Je možné do seznamu povolených IP adres předávání koncových bodů?
Ano. Přenosový klient vytvoří připojení ke službě předávání přes Azure pomocí plně kvalifikovaných názvů domény. Zákazníci můžete přidat záznam pro `*.servicebus.windows.net` na brány firewall, které podporují vytvoření seznamu povolených DNS.

## <a name="next-steps"></a>Další postup
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
