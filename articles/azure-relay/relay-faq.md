---
title: Nejčastější dotazy k Azure Relay | Microsoft Docs
description: Tento článek obsahuje odpovědi na některé z nejčastějších dotazů týkajících se služby Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 83cbc4b02bb8b63878abd046ed2314728546e87a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250043"
---
# <a name="azure-relay-faqs"></a>Nejčastější dotazy k Azure Relay

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se [Azure Relay](https://azure.microsoft.com/services/service-bus/). Obecné informace o cenách a podpoře Azure najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-relay"></a>Co je Azure Relay?
[Služba Azure Relay](relay-what-is-it.md) usnadňuje vašim hybridním aplikacím tím, že pomáhá bezpečněji vystavovat služby, které se nacházejí v podnikové síti, do veřejného cloudu. Služby můžete vystavit bez otevření připojení brány firewall a bez nutnosti rušivých změn v infrastruktuře podnikové sítě.

### <a name="what-is-a-relay-namespace"></a>Co je obor názvů Relay?
[Obor názvů](relay-create-namespace-portal.md) je kontejner oboru, který můžete použít k adresování prostředků přenosu v rámci aplikace. Musíte vytvořit obor názvů pro použití Relay. Toto je jeden z prvních kroků při zahájení práce.

### <a name="what-happened-to-service-bus-relay-service"></a>Co se stalo s Service Bus Relay služby?
Dříve pojmenovaná služba Service Bus Relay se nyní označuje jako [Azure Relay](service-bus-relay-tutorial.md). Tuto službu můžete dál používat jako obvykle. Funkce Hybrid Connections je aktualizovaná verze služby, která je předaná z Azure BizTalk Services. WCF Relay a Hybrid Connections obojí i nadále podporovat.

## <a name="pricing"></a>Ceny
Tato část obsahuje odpovědi na některé nejčastější dotazy týkající se cenové struktury přenosu. Také se můžete podívat na [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/) pro obecné informace o cenách Azure. Úplné informace o cenách přenosu najdete v tématu [Service Bus podrobnosti o cenách][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak se účtují Hybrid Connections a WCF Relay?
Podrobné informace o cenách přenosu najdete v tabulce [Hybrid Connections a WCF Relay][Pricing overview] na stránce Service Bus s podrobnostmi o cenách. Kromě cen uvedených na této stránce se vám budou účtovat přenosy dat pro výstup mimo datacentrum, ve kterém se vaše aplikace zřídí.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak se fakturuje Hybrid Connections?
Tady jsou tři příklady scénářů fakturace pro Hybrid Connections:

*   Scénář 1:
    *   Máte jeden naslouchací proces, jako je například instance Hybrid Connections Manageru nainstalovaná a nepřetržitě spuštěný celý měsíc.
    *   V průběhu tohoto měsíce prostřednictvím připojení odesíláte 3 GB dat. 
    *   Vaše celková cena je $5.
*   Scénář 2:
    *   Máte jeden naslouchací proces, jako je například instance Hybrid Connections Manageru nainstalovaná a nepřetržitě spuštěný celý měsíc.
    *   Během tohoto měsíce prostřednictvím připojení odesíláte 10 GB dat.
    *   Vaše celková cena je $7,50. To je $5 pro připojení a prvních 5 GB + $2,50 pro další 5 GB dat.
*   Scénář 3:
    *   Máte dvě instance, a a B, Hybrid Connections Manager nainstalované a průběžně běží celý měsíc.
    *   Během tohoto měsíce odesíláte 3 GB dat napříč připojením.
    *   V průběhu tohoto měsíce odesíláte 6 GB dat napříč připojením B.
    *   Vaše celková cena je $10,50. To je $5 pro připojení A + $5 pro připojení B + $0,50 (za šest GB u připojení B).


### <a name="how-are-hours-calculated-for-relay"></a>Jak se počítají hodiny pro přenos?

WCF Relay je k dispozici pouze v oborech názvů úrovně Standard. Ceny a [kvóty připojení](../service-bus-messaging/service-bus-quotas.md) pro přenosy v opačném případě se nezměnily. To znamená, že se přenosy dál účtují na základě počtu zpráv (nikoli operací) a hodin přenosu. Další informace najdete v tabulce ["Hybrid Connections a WCF Relay"](https://azure.microsoft.com/pricing/details/service-bus/) na stránce s podrobnostmi o cenách.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co když mám k určitému přenosu připojeno více než jeden naslouchací proces?
V některých případech může mít jeden Relay několik připojených posluchačů. Předávání se považuje za otevřené, pokud je k němu připojen alespoň jeden naslouchací proces přenosu. Přidání posluchačů do otevřeného přenosu vede k dalším hodinám přenosu. Počet odesílacích odesílatelů (klientů, kteří vyvolají nebo odesílají zprávy do přenosu), který je připojený ke službě Relay, nemá vliv na výpočet hodin přenosu.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak se počítá měřič zpráv pro WCF Relay?
(**Týká se jenom přenosů WCF. Zprávy nejsou náklady na Hybrid Connections.**)

Obecně platí, že Fakturovatelné zprávy pro Relay se počítají pomocí stejné metody, která se používá pro zprostředkované entity (fronty, témata a odběry) popsané dříve. Existují však některé významné rozdíly.

Odeslání zprávy do Azure Relay je považováno za "úplného" odesílané naslouchací službě přenosu, která obdrží zprávu. Nepovažuje se za operaci odeslání do Azure Relay, po které následuje doručení naslouchacího procesu přenosu. Volání služby stylu požadavek-odpověď (od až 64 KB) proti naslouchacímu programu přenosu má za následek dvě Fakturovatelné zprávy: jednu fakturovatelnou zprávu pro požadavek a jednu fakturovatelnou zprávu pro odpověď (za předpokladu, že odpověď je také 64 KB nebo menší). To se liší od použití fronty k napravení mezi klientem a službou. Pokud používáte frontu k napravení mezi klientem a službou, stejný vzor požadavku a odpovědi vyžaduje, aby se žádost odeslala do fronty a za ní následovala vyřazení z fronty nebo doručování z fronty do služby. Následuje odpověď odeslání odpovědi do jiné fronty a její vyřazení z fronty a jejich doručování klientovi. S využitím stejných nastavení pro celou velikost (až 64 KB) se ve vzorech vydaných ve frontě zobrazí 4 Fakturovatelné zprávy. Bude se vám účtovat dvojnásobek počtu zpráv, které implementují stejný vzor, který můžete provést pomocí přenosu. K dosažení tohoto modelu se samozřejmě využívají výhody, jako je například odolnost a vyrovnávání zatížení. Tyto výhody můžou zvýšit náklady.

Přenosy, které jsou otevřeny pomocí vazby WCF **netTCPRelay** , považují zprávy za jednotlivé zprávy, ale jako proud dat přenášených systémem. Použijete-li tuto vazbu, pouze odesílatel a naslouchací proces budou mít přehled o rámcích jednotlivých odeslaných a přijímaných zpráv. U přenosů, které používají vazbu **netTCPRelay** , se všechna data považují za datový proud pro výpočet fakturovatelných zpráv. V takovém případě Service Bus vypočítá celkové množství dat odesílaných nebo přijatých prostřednictvím každého jednotlivého přenosu po dobu 5 minut. Pak rozdělí celkový objem dat o 64 KB, aby bylo možné určit počet fakturovaných zpráv pro tento přenos během daného časového období.

## <a name="quotas"></a>Kvóty
| Název kvóty | Obor |  Poznámky | Hodnota |
| --- | --- | --- | --- |
| Souběžné naslouchací procesy na Relay |Entita (hybridní připojení nebo WCF Relay) |Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. |25 |
| Souběžná připojení přenosu na všechny koncové body přenosu v oboru názvů služby |Obor názvů |- |5 000 |
| Koncové body Relay na obor názvů služby |Obor názvů |- |10 000 |
| Velikost zprávy pro předávání [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) a [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Obor názvů |Příchozí zprávy, které překračují tyto kvóty, se odmítnou a volající kód obdrží výjimku. |64 kB |
| Velikost zprávy pro předávání [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Obor názvů |Velikost zprávy není nijak omezena. |Unlimited |

### <a name="does-relay-have-any-usage-quotas"></a>Má přenos nějaké kvóty využití?
Ve výchozím nastavení pro jakoukoli cloudovou službu Microsoft nastavuje agregovanou kvótu měsíčního využití, která se počítá napříč předplatnými zákazníka. Chápeme, že v době, kdy vaše potřeby můžou tato omezení překročit. Služby zákazníkům můžete kdykoli kontaktovat, abychom vám porozuměli vašim potřebám a odpovídajícím způsobem upravili tato omezení. Pro Service Bus agregované kvóty využití jsou následující:

* zprávy 5 000 000 000
* 2 000 000 hodin přenosu

I když Vyhrazujeme si právo zakázat účet, který přesáhne jeho měsíční kvóty využití, poskytneme vám e-mailové oznámení a provedeme několik pokusů o kontaktování zákazníka před provedením jakékoli akce. Zákazníkům, kteří překračují tyto kvóty, se stále zodpovídá za nadměrné poplatky.

### <a name="naming-restrictions"></a>Omezení pojmenování
Název oboru názvů přenosu musí mít délku 6 až 50 znaků.

## <a name="subscription-and-namespace-management"></a>Správa předplatného a oboru názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Návody migrovat obor názvů do jiného předplatného Azure?

Pokud chcete přesunout obor názvů z jednoho předplatného Azure do jiného předplatného, můžete použít [Azure Portal](https://portal.azure.com) nebo použít příkazy PowerShellu. Chcete-li přesunout obor názvů do jiného předplatného, obor názvů již musí být aktivní. Uživatel, který spouští příkazy, musí být uživatel s oprávněním správce na zdrojovém i cílovém předplatném.

#### <a name="azure-portal"></a>portál Azure

Pokud chcete použít Azure Portal k migraci Azure Relay oborů názvů z jednoho předplatného do jiného předplatného, přečtěte si téma [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Pokud chcete pomocí PowerShellu přesunout obor názvů z jednoho předplatného Azure do jiného předplatného, použijte následující posloupnost příkazů. Aby bylo možné provést tuto operaci, musí být obor názvů již aktivní a uživatel, který spouští příkazy prostředí PowerShell, musí být uživatel s oprávněním správce na zdrojovém i cílovém předplatném.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jaké jsou některé výjimky generované rozhraními API Azure Relay a doporučené akce, které můžete provést?
Popis běžných výjimek a navrhovaných akcí, které můžete provést, najdete v tématu [výjimky přenosu][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co je sdílený přístupový podpis a které jazyky můžu použít k vygenerování podpisu?
Sdílené přístupové podpisy (SAS) jsou mechanismy ověřování založené na zabezpečených hodnotách hash nebo identifikátorech URI SHA-256. Informace o tom, jak generovat vlastní signatury v Node.js, PHP, Pythonu, Java, C a C#, najdete v tématu [ověřování Service Bus pomocí sdílených přístupových podpisů][Shared Access Signatures].

### <a name="is-it-possible-to-allow-only-some-relay-endpoints"></a>Je možné umožnit pouze některé koncové body přenosu?
Ano. Předávací klient umožňuje připojení ke službě Azure Relay pomocí plně kvalifikovaných názvů domén. Zákazníci můžou přidat položku pro `*.servicebus.windows.net` na brány firewall, které podporují seznam schválení DNS.

## <a name="next-steps"></a>Další kroky
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
