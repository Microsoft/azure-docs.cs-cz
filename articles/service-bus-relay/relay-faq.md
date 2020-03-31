---
title: Nejčastější dotazy k přenosu Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje odpovědi na některé často kladené otázky týkající se služby Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: d5032b427316a3c4e07013af4e8214e239a6efb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513996"
---
# <a name="azure-relay-faqs"></a>Nejčastější dotazy k přenosu Azure

Tento článek odpovídá na některé nejčastější dotazy týkající se [Azure Relay](https://azure.microsoft.com/services/service-bus/). Obecné informace o cenách a podpoře Azure najdete v [častých dotazech k podpoře Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-relay"></a>Co je Azure Relay?
[Služba Azure Relay](relay-what-is-it.md) usnadňuje hybridní aplikace tím, že vám pomáhá bezpečněji zpřístupnit služby, které jsou umístěny v podnikové síti, do veřejného cloudu. Služby můžete vystavit bez otevření připojení brány firewall a bez nutnosti rušivých změn v infrastruktuře podnikové sítě.

### <a name="what-is-a-relay-namespace"></a>Co je obor názvů Relay?
[Obor názvů](relay-create-namespace-portal.md) je kontejner oborů, který můžete použít k adresování prostředků přenosu v rámci aplikace. Chcete-li použít přenos, je nutné vytvořit obor názvů. Toto je jeden z prvních kroků, jak začít.

### <a name="what-happened-to-service-bus-relay-service"></a>Co se stalo se službou Service Bus Relay?
Dříve pojmenovaná služba Relé sběrnice se nyní nazývá [WCF Relay](service-bus-relay-tutorial.md). Tuto službu můžete nadále používat obvyklým způsobem. Funkce Hybridní připojení je aktualizovaná verze služby, která byla transplantována ze služby Azure BizTalk. WCF relé a hybridní připojení jsou nadále podporovány.

## <a name="pricing"></a>Ceny
Tato část odpovídá na některé nejčastější dotazy týkající se cenové struktury relay. Můžete se také podívat na [nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/) pro obecné informace o cenách Azure. Úplné informace o cenách relé najdete v [tématu Podrobnosti o cenách service bus .][Pricing overview]

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak se účtuje hybridní připojení a WCF relay?
Úplné informace o cenách přenosu naleznete v tabulce [Hybridní připojení a Přenosy WCF][Pricing overview] na stránce s podrobnostmi o cenách service bus. Kromě cen uvedených na této stránce se vám účtují přidružené přenosy dat pro odchozí přenosy mimo datové centrum, ve kterém je vaše aplikace zřízena.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak se mi účtují hybridní připojení?
Tady jsou tři příklady scénářů fakturace pro hybridní připojení:

*   Scénář 1:
    *   Máte jeden naslouchací proces, například instanci Správce hybridních připojení nainstalován a nepřetržitě spuštěn po celý měsíc.
    *   Odesílat 3 GB dat přes připojení během měsíce. 
    *   Celkový poplatek je $5.
*   Scénář 2:
    *   Máte jeden naslouchací proces, například instanci Správce hybridních připojení nainstalován a nepřetržitě spuštěn po celý měsíc.
    *   Odesílat 10 GB dat přes připojení během měsíce.
    *   Celkový poplatek je $7.50. To je $ 5 pro připojení a první 5 GB + $ 2.50 pro dalších 5 GB dat.
*   Scénář 3:
    *   Máte dvě instance, A a B, správce hybridních připojení nainstalována a nepřetržitě spuštěna po celý měsíc.
    *   Odesílat 3 GB dat přes připojení A během měsíce.
    *   Odesíláte 6 GB dat přes připojení B během měsíce.
    *   Celkový poplatek je $10.50. To je $ 5 pro připojení A + $ 5 pro připojení B + $ 0.50 (pro šestý gigabajt při připojení B).

Všimněte si, že ceny použité v příkladech jsou použitelné pouze během období náhledu hybridní připojení. Ceny se mohou změnit v závislosti na obecné dostupnosti hybridních připojení.

### <a name="how-are-hours-calculated-for-relay"></a>Jak se hodiny počítají pro relé?

WCF Relay je k dispozici pouze v oborech názvů úrovně Standard. Ceny a [kvóty připojení](../service-bus-messaging/service-bus-quotas.md) pro přenosy jinak nezměnily. To znamená, že relé se nadále nabíjí na základě počtu zpráv (nikoli operací) a hodin přenosu. Další informace naleznete v tabulce [Hybridní připojení a Přenosy WCF](https://azure.microsoft.com/pricing/details/service-bus/) na stránce s podrobnostmi o cenách.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co když mám více než jeden posluchač připojený k určitému relé?
V některých případech může mít jedno relé více připojených naslouchacích procesů. Relé je považováno za otevřené, pokud je k němu připojen alespoň jeden naslouchací proces relé. Přidání posluchačů do otevřeného přenosu má za následek další hodiny přenosu. Počet odesílatelů přenosu (klientů, kteří vyvolávají nebo odesílají zprávy relé), které jsou připojeny k přenosu, nemá vliv na výpočet hodin přenosu.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak se počítá měřič zpráv pro wcf relé?
(**To platí pouze pro wcf relé. Zprávy nejsou náklady na hybridní připojení.**)

Obecně platí, že fakturovatelné zprávy pro přenosy se počítají pomocí stejné metody, která se používá pro zprostředkované entity (fronty, témata a odběry), popsané dříve. Nicméně, tam jsou některé pozoruhodné rozdíly.

Odeslání zprávy do přenosu sběrnice služby je považováno za "úplné prostřednictvím" odeslat naslouchací proces přenosu, který obdrží zprávu. Není považován za operaci odeslání relé sběrnice, následovanou doručením naslouchací proces relé. Vyvolání služby styl požadavek odpověď (až 64 KB) proti naslouchací proces přenosu výsledky ve dvou fakturovatelných zpráv: jedna fakturovatelná zpráva pro požadavek a jednu fakturovatelnou zprávu pro odpověď (za předpokladu, že odpověď je také 64 KB nebo menší). To je jiné než použití fronty k zprostředkování mezi klientem a službou. Pokud používáte frontu k zprostředkování mezi klientem a službou, stejný vzor požadavku a odpovědi vyžaduje požadavek odeslat do fronty, následovaný dequeue/doručení z fronty do služby. Následuje odpověď odeslat do jiné fronty a dequeue/delivery z této fronty klientovi. Pomocí předpokladů stejné velikosti v celém (až 64 KB) výsledkem zprostředkované fronty vzor fakturovatelné zprávy. Bude vám účtován dvojnásobek počtu zpráv k implementaci stejného vzoru, který provedete pomocí přenosu. Samozřejmě, existují výhody použití fronty k dosažení tohoto vzoru, jako je například odolnost a vyrovnávání zatížení. Tyto výhody by mohly ospravedlnit dodatečné náklady.

Přenosy, které jsou otevřeny pomocí **netTCPRelay** WCF vazby považovat zprávy nikoli jako jednotlivé zprávy, ale jako datový proud dat tok přes systém. Při použití této vazby pouze odesílatel a naslouchací proces mají přehled o rámování jednotlivých zpráv odeslaných a přijatých. Pro přenosy, které používají vazbu **netTCPRelay,** jsou všechna data považována za datový proud pro výpočet fakturovatelných zpráv. V tomto případě service bus vypočítá celkové množství dat odeslaných nebo přijatých prostřednictvím každého jednotlivého relé na základě 5 minut. Potom vydělí, že celkové množství dat 64 KB k určení počtu fakturovatelných zpráv pro toto předávání během tohoto časového období.

## <a name="quotas"></a>Kvóty
| Název kvóty | Rozsah |  Poznámky | Hodnota |
| --- | --- | --- | --- |
| Souběžné posluchače ve štafetě |Entita |Následné požadavky na další připojení jsou odmítnuty a volající kód přijímá výjimku. |25 |
| Souběžná přenosová připojení pro všechny koncové body přenosu v oboru názvů služby |Obor názvů |- |5 000 |
| Předávání koncových bodů na obor názvů služby |Obor názvů |- |10 000 |
| Velikost zprávy pro přenosová přenosy [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) a [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Obor názvů |Příchozí zprávy, které překračují tyto kvóty, jsou odmítnuty a volající kód přijímá výjimku. |64 kB |
| Velikost zprávy pro přenosy [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Obor názvů |Není omezena velikost zprávy. |Unlimited |

### <a name="does-relay-have-any-usage-quotas"></a>Má relé nějaké kvóty využití?
Ve výchozím nastavení pro všechny cloudové služby nastaví Microsoft agregovně měsíční kvótu využití, která se počítá ve všech předplatných zákazníka. Chápeme, že občas vaše potřeby mohou překročit tyto limity. Zákaznický servis můžete kdykoli kontaktovat, abychom mohli porozumět vašim potřebám a odpovídajícím způsobem upravit tyto limity. Pro service bus jsou souhrnné kvóty využití následující:

* 5 miliard zpráv
* 2 miliony reléhodin

Přestože si vyhrazujeme právo deaktivovat účet, který překračuje jeho měsíční kvóty využití, poskytujeme e-mailové oznámení a před přijetím jakékoli akce se několikrát pokusíme kontaktovat zákazníka. Zákazníci, kteří tyto kvóty překročí, jsou stále zodpovědní za nadměrné poplatky.

### <a name="naming-restrictions"></a>Omezení pojmenování
Název oboru názvů Předávání musí mít délku 6 až 50 znaků.

## <a name="subscription-and-namespace-management"></a>Správa předplatného a oboru názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak můžu migrovat obor názvů do jiného předplatného Azure?

Pokud chcete přesunout obor názvů z jednoho předplatného Azure do jiného předplatného, můžete buď použít [portál Azure,](https://portal.azure.com) nebo použít příkazy PowerShellu. Chcete-li přesunout obor názvů do jiného předplatného, musí být již aktivní. Uživatel spouštějící příkazy musí být správce uživatelem na zdrojové i cílové odběry.

#### <a name="azure-portal"></a>portál Azure

Pokud chcete použít portál Azure k migraci oborů názvů Azure Relay z jednoho předplatného do jiného předplatného, přečtěte si témat [u čl.](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal) 

#### <a name="powershell"></a>PowerShell

Pokud chcete pomocí PowerShellu přesunout obor názvů z jednoho předplatného Azure do jiného předplatného, použijte následující posloupnost příkazů. Chcete-li provést tuto operaci, musí být obor názvů již aktivní a uživatel s ovládacími příkazy prostředí PowerShell musí být uživatelem správce ve zdrojových i cílových předplatných.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Řešení potíží
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jaké jsou některé výjimky generované Azure Relay API a navrhované akce, které můžete provést?
Popis běžných výjimek a navrhovaných akcí, které můžete provést, naleznete v [tématu Předávání výjimek][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co je sdílený přístupový podpis a které jazyky lze použít ke generování podpisu?
Sdílené přístupové podpisy (SAS) jsou mechanismus ověřování založený na zabezpečených hashech SHA-256 nebo identifikátorech URI. Informace o tom, jak generovat vlastní podpisy v Node.js, PHP, Pythonu, Javě, C a C#, najdete v [tématu Ověřování služby Service Bus se sdílenými přístupovými podpisy][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Je možné whitelist relé koncové body?
Ano. Klient přenosu vytváří připojení ke službě Azure Relay pomocí plně kvalifikovaných názvů domén. Zákazníci mohou přidat `*.servicebus.windows.net` položku pro brány firewall, které podporují seznam povolených DNS.

## <a name="next-steps"></a>Další kroky
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
