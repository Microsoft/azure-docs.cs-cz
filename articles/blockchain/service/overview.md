---
title: Přehled služby Azure blockchain
description: Přehled služby Azure blockchain
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: dc716b475fe85170d595463e0785743591e398c1
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874555"
---
# <a name="what-is-azure-blockchain-service"></a>Co je Azure Blockchain Service?

Služba Azure Blockchain je plně spravovaná služba v hlavní knize, která uživatelům umožňuje růst a provoz Blockchainch sítí v Azure ve velkém měřítku. Poskytováním jednotného řízení pro správu infrastruktury i zásad správného řízení sítě blockchain zajišťuje služba Azure blockchain:

* Jednoduché síťové nasazení a operace
* Integrovaná správa konsorcia
* Vývoj inteligentních smluv pomocí známých vývojářských nástrojů

Služba Azure Blockchain je navržená tak, aby podporovala více protokolů hlavní knihy. V současné době poskytuje podporu pro hlavní knihu [kvora](https://www.goquorum.com/) ethereem pomocí mechanismu shody [iBFT (Istanbul Byzantine odolnost proti chybám)](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) .

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Místo přidělení času a prostředků ke správě virtuálních počítačů a infrastruktury se můžete soustředit na vývoj aplikací a obchodní logiku. Kromě toho můžete pokračovat v vývoji aplikace pomocí Open source nástrojů a platformy podle vašeho výběru, abyste mohli doručovat vaše řešení, aniž byste se museli učit nové dovednosti.

## <a name="network-deployment-and-operations"></a>Nasazení a provoz sítě

Nasazení služby Azure blockchain se provádí prostřednictvím Azure Portal, Azure CLI nebo prostřednictvím Visual studia Code pomocí rozšíření Azure blockchain. Nasazení je zjednodušené, včetně zřízení transakcí i ověřovacích uzlů, virtuálních sítí Azure pro izolaci zabezpečení i úložiště spravovaného službou.  Kromě toho, když se nasazuje nový člen blockchain, uživatelé také vytvoří nebo připojí konsorcium.  Konsorcia umožňují více stranám v různých předplatných Azure vzájemně komunikovat na sdílených blockchain.  Toto zjednodušené nasazení omezuje blockchain síťové nasazení ze dnů na minuty.

### <a name="performance-and-service-tiers"></a>Výkon a úrovně služeb

Služba Azure blockchain nabízí dvě úrovně služeb: *Basic* a *Standard*. Každá úroveň nabízí jiný výkon a možnosti pro podporu zjednodušeného vývoje a testování zatížení až po rozsáhlou škálu nasazení produkčního blockchainu. Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní. Obě úrovně zahrnují aspoň jeden uzel transakce a jeden uzel validátoru (základní) nebo dva uzly validátoru (Standard). 

![Cenové úrovně](./media/overview/pricing-tiers.png)

Kromě nabídky dvou ověřovacích uzlů poskytuje úroveň *Standard* dvě *virtuální jádra* pro každý uzel transakce a validátoru, zatímco úroveň *Basic* nabízí 1 konfiguraci Vcore.  Díky nabídce 2 virtuální jádra pro transakce a ověřovací uzly může být 1 vCore vyhrazený pro hlavní knihu kvora, zatímco zbývající 1 vCore lze použít pro jiné služby související s infrastrukturou a zajistit optimální výkon pro produkční úlohy blockchain. Další informace o cenách najdete v článku o [cenách služby Azure blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Zabezpečení a údržba

Po zřízení prvního blockchain člena máte možnost přidávat do svého člena další uzly transakcí.  Ve výchozím nastavení jsou uzly transakcí zabezpečeny prostřednictvím pravidel brány firewall a vyžadují konfiguraci pro přístup.  Kromě toho všechny uzly transakcí šifrují data v pohybu přes protokol TLS.  Existuje více možností pro zabezpečení přístupu k uzlům v transakci, včetně pravidel brány firewall, základního ověřování, přístupových klíčů a Integrace Azure Active Directory. Další informace najdete v tématech [Konfigurace uzlů transakcí](configure-transaction-nodes.md) a [Konfigurace přístupu Azure Active Directory](configure-aad.md).

Jako spravovaná služba Služba Azure blockchain zajišťuje, že se uzly členů blockchain opraví s nejnovějšími aktualizacemi operačního systému hostitele a softwarového zásobníku, která je nakonfigurovaná na vysokou dostupnost (jenom na úrovni Standard), což eliminuje většinu DevOps požadovaných pro tradiční uzly IaaS blockchain.  Další informace o opravách a aktualizacích najdete v článku [podporované verze hlavní knihy služby Azure blockchain](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorování a protokolování

Kromě toho služba Azure blockchain poskytuje bohatou metriku prostřednictvím služby Azure Monitor Service, která poskytuje přehledy o využití procesoru, paměti a úložiště uzlů.  Azure Monitor taky poskytuje užitečné poznatky k blockchain síťové aktivitě, jako jsou transakce a bloky dolována za účely, hloubka fronty transakcí a aktivní připojení.  Metriky je možné přizpůsobit tak, aby poskytovaly přehledy, které jsou důležité pro vaši aplikaci blockchain.  Kromě toho je možné definovat prahové hodnoty prostřednictvím výstrah umožňujících uživatelům aktivovat akce, jako je odeslání e-mailu nebo textové zprávy, spuštění aplikace logiky, funkce Azure nebo odeslání do vlastního Webhooku definovaného uživatelem.

![Snímek obrazovky ukazuje monitorování s hodnotami pro bloky, transakce, nevyřízené transakce a zpracování požadavků.](./media/overview/metrics.png)

Uživatelé můžou prostřednictvím Azure Log Analytics zobrazovat protokoly související s hlavní knihou kvora nebo jiné důležité informace, jako je například pokus o připojení k uzlům transakcí.

## <a name="built-in-consortium-management"></a>Integrovaná správa konsorcia

Při nasazování prvního blockchain člena se buď připojte, nebo vytvořte novou konsorcium.  Konsorcium je logická skupina, která slouží ke správě zásad správného řízení a připojení mezi blockchain členy, kteří pracují v procesu s více stranami.  Služba Azure blockchain poskytuje předdefinované ovládací prvky zásad správného řízení prostřednictvím předem definovaných inteligentních kontraktů, které určují, jaké akce mohou členové konsorcia provádět.  Tyto ovládací prvky zásad správného řízení je možné přizpůsobit správcem konsorcia podle potřeby. Při vytváření nového konsorcia je váš blockchain člen výchozím správcem konsorcia a umožňuje tak pozvat jiné strany, aby se připojili k vašemu konsorciu.  Konsorcium můžete spojit pouze v případě, že jste byli dříve pozváni.  Při spojení s konsorciem se váš blockchain člen řídí ovládacími prvky zásad správného řízení, které provádí správce konsorcia.

![Správa konsorcia](./media/overview/consortium.png)

Akce správy konsorcia, jako je přidávání a odebírání členů z konsorcia, je možné použít prostřednictvím PowerShellu a REST API. Konsorcium můžete programově spravovat pomocí společných rozhraní, nikoli změnou a odesláním inteligentních kontraktů založených na pevné úrovni. Další informace najdete v tématu [Správa konsorcia](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Vývoj s využitím známých vývojových nástrojů

Na základě open source hlavní knihy Ethereem kvora můžete vyvíjet aplikace pro službu Azure blockchain stejným způsobem jako u stávajících aplikací Ethereem. Při práci s předními průmyslovými partnery Visual Studio Code rozšíření Azure blockchain Development Kit umožňuje vývojářům využít známé nástroje, jako je Truffle Suite, k sestavování inteligentních smluv. Pomocí rozšíření Azure blockchain Visual Studio Code můžete vytvořit existující konsorcium nebo se k němu připojit, abyste mohli vytvářet a nasazovat své inteligentní smlouvy z jednoho integrovaného vývojového prostředí (IDE). Další informace najdete v tématu [Azure blockchain Development Kit na webu vs Code Marketplace](https://aka.ms/vscodebcextension) a v [uživatelské příručce k sadě Azure blockchain Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Publikování dat blockchain

Blockchain Data Manager pro služby Azure blockchain Capture, transformuje a doručuje data transakcí služby Azure blockchain, aby Azure Event Grid témata poskytující spolehlivou a škálovatelnou integraci blockchain v hlavní knize se službami Azure. Pomocí blockchain Data Manager můžete integrovat aplikace a úložiště dat mimo řetěz. Další informace najdete v tématu [Blockchain data Manager pro službu Azure blockchain](data-manager.md).

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Novinky ke službě Azure blockchain News najdete na [blogu Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/) , abyste měli přehled o nabídkách služeb blockchain a informacích od týmu Azure blockchain Engineering.

Pokud chcete poskytnout zpětnou vazbu k produktu nebo požádat o nové funkce, vystavte nebo Hlasujte nápad prostřednictvím [fóra Azure Feedback pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spolupracujte s odborníky z Microsoftu a komunitou Azure blockchain.

* [Stránka s otázkou pro službu Azure blockchain&Microsoft Q](/answers/topics/azure-blockchain-service.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít, vyzkoušejte si rychlý Start nebo si přečtěte další podrobnosti z těchto zdrojů.
* [Vytvoření členu blockchain pomocí Azure Portal](create-member.md) nebo [Vytvoření členu BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* Porovnání nákladů a kalkulačky najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/blockchain-service).
* Vytvoření první aplikace pomocí [vývojové sady Azure blockchain](https://github.com/Azure-Samples/blockchain-devkit)
* [Uživatelská příručka](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) k rozšíření Azure blockchain VSCode
