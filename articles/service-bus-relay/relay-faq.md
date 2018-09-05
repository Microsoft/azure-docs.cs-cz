---
title: Nejčastější dotazy k Azure Relay | Dokumentace Microsoftu
description: Získejte odpovědi na některé nejčastější dotazy týkající se Azure Relay.
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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e30e8c94547ac0f9106a69f1e99cf9a7c03abea5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695893"
---
# <a name="azure-relay-faqs"></a>Nejčastější dotazy k Azure Relay

Tohoto článku najdete odpovědi na některé nejčastější dotazy (FAQ) o [Azure Relay](https://azure.microsoft.com/services/service-bus/). Obecné informace o Azure ceny a podporu, najdete v článku [Azure nejčastějších dotazech k podpoře](https://azure.microsoft.com/support/faq/).

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-relay"></a>Co je Azure Relay?
[Služba Azure Relay](relay-what-is-it.md) hybridním aplikacím usnadňuje práci tím, že vám více bezpečně vystavit služby, které se nacházejí v podnikové síti, veřejnému cloudu. Služby můžete zveřejnit, bez nutnosti otevřít spojení ve firewallu a bez nutnosti nežádoucí změny infrastruktury podnikové sítě.

### <a name="what-is-a-relay-namespace"></a>Co je obor názvů služby Relay?
A [obor názvů](relay-create-namespace-portal.md) je kontejner oboru pro adresování prostředků Relay můžete použít v rámci vaší aplikace. Musíte vytvořit obor názvů pro Relay pomocí. Toto je jeden z prvních kroků v části Začínáme.

### <a name="what-happened-to-service-bus-relay-service"></a>Co se stalo s službu service Bus Relay?
Dříve pojmenovanou službu předávání přes Service Bus se teď nazývá [WCF Relay](relay-wcf-dotnet-get-started.md). Můžete nadále používat tuto službu jako obvykle. Funkce hybridní připojení je aktualizované verze služby, který je byl transplantované ze služby Azure BizTalk Services. WCF Relay a Hybrid Connections nadále podporovány.

## <a name="pricing"></a>Ceny
Tato část odpovídá na některé časté otázky ke správě přenosu Cenová struktura. Také můžete zobrazit [nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/) obecné informace o cenách Azure. Úplné informace o cenách služby Relay, najdete v části [podrobnosti o cenách služby Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak jste za hybridní připojení a WCF Relay?
Úplné informace o cenách služby Relay, najdete v článku [hybridními připojeními a přenosy WCF] [ Pricing overview] tabulky na stránce s podrobnostmi o cenách služby Service Bus. Kromě ceny uvedené na této stránce bude vám účtována přidružené datové přenosy pro výchozí přenos dat mimo datové centrum, ve které je zřízená vaší aplikace.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak se fakturují pro hybridní připojení?
Tady jsou tři ukázkové fakturační scénáře pro hybridní připojení:

*   Scénář 1:
    *   Máte jeden naslouchací proces, třeba instance Hybrid Connections Manageru nainstalované a průběžně běží celý měsíc.
    *   Odešlete 3 GB dat prostřednictvím tohoto připojení v měsíci. 
    *   Vaše celková cena je 5 USD.
*   Scénář 2:
    *   Máte jeden naslouchací proces, třeba instance Hybrid Connections Manageru nainstalované a průběžně běží celý měsíc.
    *   Odešlete 10 GB dat prostřednictvím tohoto připojení v měsíci.
    *   Vaše celková cena je 7.50 $. To je 5 USD za připojení a prvních 5 GB + 2,50 $ za dalších 5 GB dat.
*   Scénář 3:
    *   Máte dvě instance, A a B Hybrid Connections Manageru nainstalované a průběžně běží celý měsíc.
    *   Odešlete 3 GB dat prostřednictvím připojení A za daný měsíc.
    *   Odešlete 6 GB dat prostřednictvím připojení B v měsíci.
    *   Vaše celková cena je 10,50 $. To je 5 USD za připojení A + 5 USD za připojení B + 0,50 $ (pro šestého GB u připojení B).

Všimněte si, že ceny použité v případech platí jenom během období preview hybridní připojení. Ceny se můžou změnit při všeobecné dostupnosti hybridních připojení.

### <a name="how-are-hours-calculated-for-relay"></a>Jak se počítají hodiny přenosu?

WCF Relay je k dispozici pouze v oborech názvů úrovně Standard. Ceny a [připojení kvóty](../service-bus-messaging/service-bus-quotas.md) pro přenosy jinak nedošlo ke změně. To znamená, že předávání dál bude účtovat podle počtu zpráv (ne operations) a hodiny propojení. Další informace najdete v tématu ["Hybridní připojení a služby WCF Relay"](https://azure.microsoft.com/pricing/details/service-bus/) tabulky na stránce s cenami podrobnosti.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co když budu mít více než jeden naslouchací proces připojení ke konkrétnímu propojení?
V některých případech může mít jeden relay několik připojených naslouchacích procesů. Naslouchacího procesu přenosu alespoň jeden je připojen k němu považuje přenos otevřít. Přidání posluchače do otevřít relay výsledkem dalších počítají hodiny. Počet relay odesílatelů (klienti, kteří vyvolat nebo odesílat zprávy do předávací), které jsou připojeny k propojení nemá vliv na výpočet hodiny propojení.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak se počítá měření zpráv pro služby WCF Relay?
(**To platí pouze pro služby WCF Relay. Zprávy nejsou náklady pro hybridní připojení.** )

Obecně platí účtovaných zpráv pro propojení počítají pomocí stejné metody, který se používá u zprostředkovaných entit (fronty, témata a odběry), je popsáno výše. Existují však některé důležité rozdíly.

Posílání zprávy k předávání přes Service Bus je považován za "úplné prostřednictvím" Odeslat naslouchacího procesu propojení, která bude přijímat zprávy. Není považován za operace odeslání předávání přes Service Bus, za nímž následuje doručování do naslouchacího procesu propojení. Požadavek odpověď stylu služby vyvolání (až 64 KB) proti propojení výsledků naslouchací proces ve dvou účtované zprávy: jednu účtovanou zprávu žádosti a jednu účtovanou zprávu pro odpověď (za předpokladu, že odpověď je také 64 KB nebo menší). To se liší od které zprostředkovává mezi klientem a služby pomocí fronty. Pokud používáte fronty které zprostředkovává mezi klientem a službou, stejný vzor požadavek odpověď vyžaduje odesílání požadavku do fronty, za nímž následuje odebrání z fronty/doručování z fronty ve službě. Následuje odeslat odpověď do jiné fronty a vyřazování/doručování z této fronty do klienta. Pomocí stejné předpoklady velikost v průběhu (až 64 KB), model zprostředkovaného fronty výsledkem 4 účtované zprávy. Bude se účtovat dvojnásobný počet zpráv k implementaci stejný vzor, který můžete provádět pomocí relay. Samozřejmě existují výhody použití fronty k dosažení tohoto modelu, jako je odolnost a vyrovnávání zátěže. Mezi tyto výhody můžou zarovnat další prostředky.

Přenosy, které jsou otevřeny pomocí **netTCPRelay** vazby WCF zpracovávat zprávy není jako jednotlivé zprávy, ale jako datový proud dat protéká systémem. Pokud použijete tuto vazbu, odesílatel a naslouchacího procesu mít přehled o rámce jednotlivé zprávy odeslané a přijaté. Pro přenosu, které používají **netTCPRelay** vazby, všechna data zpracovávají jako datový proud pro výpočet účtované zprávy. V takovém případě služby Service Bus vypočítá celkové množství dat odeslaných nebo přijatých prostřednictvím každé jednotlivé relay v intervalech 5 minut. Potom ji vydělí celkové množství dat 64 KB k určení počet účtovaných zpráv pro propojení během tohoto časového období.

## <a name="quotas"></a>Kvóty
| Název kvóty | Rozsah |  Poznámky | Hodnota |
| --- | --- | --- | --- |
| Souběžné naslouchacích procesů na přenos |Entita |Odeslání dalších žádostí o další připojení budou odmítnuty a volající kód obdrží výjimku. |25 |
| Předávání souběžných připojení za všechny koncové body relay v oboru názvů služby |Obor názvů |- |5 000 |
| Obor názvů služby Relay koncových bodů |Obor názvů |- |10 000 |
| Velikost pro zprávy [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) a [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) přenosu |Obor názvů |Odmítne příchozí zprávy, které přesahují tyto kvóty a obdrží výjimku ve volajícím kódu. |64 kB |
| Velikost pro zprávy [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) přenosu |Obor názvů |Žádné omezení velikosti zpráv. |Unlimited |

### <a name="does-relay-have-any-usage-quotas"></a>Má propojení jakékoli využití kvóty?
Ve výchozím nastavení pro kteroukoli cloudovou službu nastaví Microsoft agregační měsíční využití kvóty, vypočtené ve všech předplatných zákazníka. Chápeme, že v některých případech vašim potřebám může tyto limity překročit. Abychom mohli o vašich potřebách a odpovídajícím způsobem nastavit limity, můžete kdykoli, požádejte služby zákazníkům. Agregované využití kvóty pro Service Bus, jsou následující:

* 5 miliard zprávy
* hodiny propojení 2 miliony operací

Vyhrazujeme si právo k zakázání účtu, který překračuje jeho měsíční kvóty využívání, poskytujeme e-mailové oznámení a provedeme několik pokusí kontaktovat zákazník před provedením jakékoli akce. Zákazníci, které přesahují tyto kvóty dál zodpovídají za nadbytečným poplatkům.

### <a name="naming-restrictions"></a>Omezení pojmenování
Název oboru názvů přenosu musí být dlouhý 6 až 50 znaků.

## <a name="subscription-and-namespace-management"></a>Správa předplatného a obor názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak migrovat obor názvů do jiného předplatného Azure?

Obor názvů přesunout z jednoho předplatného Azure do jiného předplatného, můžete použít [webu Azure portal](https://portal.azure.com) nebo použít příkazy prostředí PowerShell. Obor názvů přesunout do jiného předplatného, musí být obor názvů už aktivní. Uživatel s oprávněním správce zdrojové a cílové předplatné musí být uživatel, který spouští příkazy.

#### <a name="azure-portal"></a>portál Azure

Pomocí webu Azure portal migrovat z jednoho předplatného Azure Relay obory názvů do jiného předplatného, najdete v článku [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Pokud chcete přesunout oboru názvů z jednoho předplatného Azure do jiného předplatného pomocí prostředí PowerShell, použijte následující posloupnost příkazů. K provedení této operace, obor názvů, musí již být aktivní a uživatel, který spouští příkazy Powershellu musí být uživatel s oprávněním správce na zdrojovém i cílovém předplatná.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jaké jsou některé výjimky generované rozhraní API služby Azure Relay a navrhované akce, které můžete provést?
Popis výjimky běžné a doporučené akce můžete provést, naleznete v tématu [přenosu výjimek][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co je podpis sdíleného přístupu a jaké jazyky můžu použít k generovat signaturu?
Sdílených přístupových podpisů (SAS) je mechanismus ověřování na základě zabezpečené hodnoty hash SHA-256 nebo identifikátory URI. Informace o tom, jak generovat vlastní podpisy v Node, PHP, Java, C a C# najdete v tématu [ověřování služby Service Bus pomocí signatur sdíleného přístupu][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Je možné ke koncovým bodům seznamu povolených IP adres relay?
Ano. Přenosový klient vytvoří připojení ke službě Azure Relay s použitím plně kvalifikované názvy domény. Zákazníky můžete přidat záznam pro `*.servicebus.windows.net` na bránách firewall, které podporují DNS na seznam povolených.

## <a name="next-steps"></a>Další postup
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s aplikací Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
