---
title: Přehled služby Azure Blockchain služby
description: Přehled služby Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 4416c30f57b469ee125400c696e8b34311a94926
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028182"
---
# <a name="what-is-azure-blockchain-service"></a>Co je služba Azure Blockchain?

Služba Azure Blockchain je registr s plně spravovanou službu, která umožňuje uživatelům možnost zvětšení a provozují blockchain sítě ve velkém měřítku v Azure. Tím, že poskytuje jednotný ovládací prvek pro správu infrastruktury i blockchain síťových zásad správného řízení, poskytuje služba Blockchain v Azure:

* Jednoduchá síť nasazení a provoz
* Integrované consortium správy
* Vývoj chytrých kontraktů pomocí známých nástrojů

Služba Azure Blockchain je navržen pro podporu více protokolů účetní knihy. V současné době poskytuje podporu pro ethereum během [kvora](https://www.jpmorgan.com/Quorum) pomocí účetní knihy [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) mechanismus shody.

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Můžete se soustředit na vývoj aplikací a obchodní logiku namísto vynakládání čas i prostředky ke správě virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat k vývoji aplikací pomocí opensourcových nástrojů a platformy podle vašeho výběru k poskytování řešení bez nutnosti učit nové dovednosti.

## <a name="network-deployment-and-operations"></a>Nasazení sítě a provoz

Nasazení služby Azure Blockchain lze provést prostřednictvím webu Azure portal, rozhraní příkazového řádku Azure, stejně jako prostřednictvím sady Visual Studio code pomocí rozšíření Azure Blockchain.  Nasazení je zjednodušený, včetně zřizování uzly transakce a program pro ověření, Azure Virtual Network pro izolaci zabezpečení stejně jako spravovaný službou storage.  Kromě toho při nasazování nového člena blockchain, uživatelé také vytvoření, nebo se připojit, konsorcium.  Konsorcia umožňují mít možnost zabezpečeně komunikovat mezi sebou na sdílené blockchain více stran v různých předplatných Azure.  Toto zjednodušené nasazení snižuje blockchain nasazení sítě ze dnů na minuty.

### <a name="performance-and-service-tiers"></a>Úrovně výkonu a služby

Služba Blockchain Azure nabízí dvě úrovně služeb: *Základní* a *standardní*. Každá úroveň nabízí různý výkon a možnosti pro podporu zjednodušené vývojové a testovací úlohy až rozsáhle škálovat blockchain nasazení v produkčním prostředí. Obě úrovně zahrnují aspoň jednu transakci uzel a jeden uzel program pro ověření (Basic) nebo dva uzly program pro ověření (Standard).

![Cenové úrovně](./media/overview/pricing-tiers.png)

Kromě nabízí dva uzly program pro ověření, *standardní* úroveň poskytuje 2 *virtuálních jader* pro každý uzel transakce a ověření že nabízí 1 konfigurace – vCore úrovně Basic.  Nabídkou 2 virtuální jádra pro uzly transakce a program pro ověření, může být vyhrazený 1 virtuální jádro do hlavní kvorum knihy, i když je zbývající 1 virtuální jádro můžete použít pro jiné související s infrastrukturou služby, zajistit optimální výkon pro produkční prostředí úloh blockchainu. Další informace o cenách najdete v tématu [ceny služby Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Zabezpečení a údržba

Po zřízení svůj první člen blockchain, máte možnost přidávat další transakce uzly na vaše člena.  Ve výchozím nastavení transakce uzly jsou zabezpečené pomocí pravidel brány firewall a bude je nutné nakonfigurovat pro přístup.  Kromě toho všechny uzly transakce šifrovat data přenášená data pomocí protokolu TLS.  Existuje více možností pro zabezpečení přístupu uzel transakce, včetně pravidel brány firewall, základní ověřování, přístupové klíče, jakož i integrace služby Azure Active Directory. Další informace najdete v tématu [konfigurace uzlů transakce](configure-transaction-nodes.md) a [konfigurovat přístup k Azure Active Directory](configure-aad.md).

Jako spravovaná služba se služba Blockchain v Azure zajistí, že jsou vaše blockchain členskými uzly opravit pomocí nejnovější hostitelského operačního systému a hlavní knihy zásobníku aktualizace softwaru, nakonfigurovaný pro vysokou dostupnost (jenom u vrstvy Standard), což eliminuje většinu DevOps vyžaduje se pro tradiční uzly blockchain IaaS.  Další informace o použití dílčích oprav a aktualizací najdete v tématu [podporované verze služby Azure Blockchain účetní knihy](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorování a protokolování

Kromě toho Azure Blockchain Service poskytuje bohaté metriky prostřednictvím služby Azure Monitor, poskytuje přehled o uzlů využití procesoru, paměti a úložiště, stejně jako užitečné přehledy o blockchain síťové aktivity, jako jsou transakce a bloky dolována, Hloubka fronty transakcí, jakož i aktivních připojení.  Metriky lze přizpůsobit, aby umožňují pohled na přehledy, které jsou důležité pro vaši aplikaci blockchain.  Kromě toho prahové hodnoty se dají definovat přes upozornění uživatelům umožňuje aktivovat akce, jako je odesílání e-mailu nebo textovou zprávu, spuštění aplikace logiky, funkce Azure Functions nebo odesílání do uživatelem definované webhooku.

![Metriky](./media/overview/metrics.png)

Díky službě Azure Log Analytics uživatelé mohou zobrazit protokoly týkající se kvora knihy nebo další důležité informace, jako například pokus o připojení k uzlům transakce.

## <a name="built-in-consortium-management"></a>Integrované consortium správy

Při nasazení vaší první člen blockchain, můžete buď připojení, nebo vytvořte novou consortium.  Konsorcium je logická skupina používají ke správě zásad správného řízení a připojení mezi blockchain členy, kteří transact více stran procesu.  Služba Azure Blockchain nabízí ovládací prvky předdefinované zásady správného řízení prostřednictvím předem definovaných chytrých kontraktů, které určují, co můžete provést akce členy v konsorcia.  Tyto ovládací prvky zásad správného řízení je možné přizpůsobit podle potřeby správcem konsorcia. Při vytváření nového consortium svůj blockchain člen je výchozího správce consortium, povolení umožňuje zvát jiné strany připojit vaše consortium.  Konsorcium můžete připojit pouze v případě, že jste dříve pozvánku.  Při připojování konsorcium, podléhá svůj blockchain člen zavedený správcem konsorcia ovládací prvky zásad správného řízení.

![Správa W3C](./media/overview/consortium.png)

Akce správy Consortium, jako například přidávání a odebírání členů ze konsorcium přístupné prostřednictvím prostředí PowerShell a rozhraní REST API. Můžete programově spravovat consortium pomocí společných rozhraní spíše než úpravy a odešlete na základě solidity chytrých kontraktů. Další informace najdete v tématu [consortium správu](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Vývoj pomocí známých nástrojů

Podle kvora Etherea knihy open source, můžete vyvíjet aplikace pro službu Azure Blockchain stejným způsobem jako u stávajících aplikací Etherea. Práce s předních oborových partnerů, rozšíření Azure Blockchain vývoje sady Visual Studio Code umožňuje vývojářům využívat známé nástroje, například Truffle Suite vytváření kontraktů uzpůsobených pro. Pomocí rozšíření Azure Blockchain Development Kit, vývojáře můžou vytvořit nebo připojit k a existující consortium tak, že můžete vytvářet a nasazovat vaše smart smlouvy vše z jednoho integrovaného vývojového prostředí. Pomocí rozšíření Azure Blockchain Visual Studio Code, můžete vytvořit nebo připojit k existující consortium tak, že můžete vytvářet a nasazovat vašich chytrých kontraktů vše z jednoho integrovaného vývojového prostředí. Další informace najdete v tématu [Azure Blockchain Development Kit v nástroji VS Code marketplace](http://aka.ms/vscodebcextension) a [uživatelská příručka k Azure Blockchain Development Kit](http://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Potřebujete pomoc nebo máte zpětnou vazbu?

* Přejděte [blogu Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [technické komunitě Microsoftu](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), a [fórum Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Další postup

Abyste mohli začít, vyzkoušejte si rychlé zprovoznění nebo zjistěte další podrobnosti o těchto prostředků.
* [Vytvoření blockchainové členu pomocí webu Azure portal](create-member.md) nebo [vytvoření blockchain členu pomocí rozhraní příkazového řádku Azure]()
* Porovnání nákladů a kalkulačky, najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/blockchain-service).
* Sestavit vaši první aplikaci s použitím [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* Rozšíření Azure Blockchain VSCode [uživatelská příručka](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
