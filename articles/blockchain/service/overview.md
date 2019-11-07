---
title: Přehled služby Azure blockchain
description: Přehled služby Azure blockchain
services: azure-blockchain
keywords: Blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9fcf75a07d1caf7b411c315d11ce984cbe35df75
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577187"
---
# <a name="what-is-azure-blockchain-service"></a>Co je Azure Blockchain Service?

Služba Azure Blockchain je plně spravovaná služba v hlavní knize, která umožňuje uživatelům rozšiřovat a provozovat blockchain sítě ve velkém měřítku v Azure. Poskytováním jednotného řízení pro správu infrastruktury i zásad správného řízení sítě blockchain zajišťuje služba Azure blockchain:

* Jednoduché síťové nasazení a operace
* Integrovaná správa konsorcií
* Vývoj inteligentních smluv pomocí známých vývojářských nástrojů

Služba Azure Blockchain je navržená tak, aby podporovala více protokolů hlavní knihy. V současné době poskytuje podporu pro hlavní knihu [kvora](https://www.jpmorgan.com/Quorum) ethereem pomocí mechanismu [iBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) konsensu.

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Místo přidělení času a prostředků ke správě virtuálních počítačů a infrastruktury se můžete soustředit na vývoj aplikací a obchodní logiku. Kromě toho můžete pokračovat v vývoji aplikace pomocí Open source nástrojů a platformy podle vašeho výběru, abyste mohli doručovat vaše řešení, aniž byste se museli učit nové dovednosti.

## <a name="network-deployment-and-operations"></a>Síťové nasazení a operace

Nasazení služby Azure blockchain můžete provést prostřednictvím Azure Portal, Azure CLI a také prostřednictvím Visual Studio Code pomocí rozšíření Azure blockchain.  Nasazení je zjednodušené, včetně zřízení transakcí i ověřovacích uzlů, virtuálních sítí Azure pro izolaci zabezpečení i úložiště spravovaného službou.  Kromě toho, když se nasazuje nový člen blockchain, uživatelé také vytvoří nebo připojí konsorcium.  Konsorcia umožňují více stranám v různých předplatných Azure vzájemně komunikovat na sdílených blockchain.  Toto zjednodušené nasazení omezuje blockchain síťové nasazení ze dnů na minuty.

### <a name="performance-and-service-tiers"></a>Výkon a úrovně služeb

Služba Azure blockchain nabízí dvě úrovně služeb: *Basic* a *Standard*. Každá úroveň nabízí jiný výkon a možnosti pro podporu zjednodušeného vývoje a testování zatížení až po rozsáhlou škálu nasazení produkčního blockchainu. Obě úrovně zahrnují aspoň jeden uzel transakce a jeden uzel validátoru (základní) nebo dva uzly validátoru (Standard).

![Cenové úrovně](./media/overview/pricing-tiers.png)

Kromě nabídky dvou ověřovacích uzlů poskytuje úroveň *Standard* 2 *virtuální jádra* pro každou transakci a uzel validátoru, zatímco úroveň Basic nabízí 1 Vcore konfiguraci.  Díky nabídce 2 virtuální jádra pro transakce a ověřovací uzly může být 1 vCore vyhrazený pro hlavní knihu kvora, zatímco zbývající 1 vCore lze použít pro jiné služby související s infrastrukturou a zajistit optimální výkon pro produkční úlohy blockchain. Další informace o cenách najdete v článku o [cenách služby Azure blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Zabezpečení a údržba

Po zřízení prvního blockchain člena máte možnost přidávat do svého člena další uzly transakcí.  Ve výchozím nastavení jsou uzly transakcí zabezpečeny prostřednictvím pravidel brány firewall a je nutné je nakonfigurovat pro přístup.  Kromě toho všechny uzly transakcí šifrují data v pohybu přes protokol TLS.  Existuje více možností pro zabezpečení přístupu k uzlům v transakci, včetně pravidel brány firewall, základního ověřování, přístupových klíčů a Azure Active Directory integrace. Další informace najdete v tématech [Konfigurace uzlů transakcí](configure-transaction-nodes.md) a [Konfigurace přístupu Azure Active Directory](configure-aad.md).

Jako spravovaná služba Služba Azure blockchain zajišťuje, aby byly uzly členů blockchain opraveny pomocí nejnovějšího operačního systému hostitele a aktualizací softwarového zásobníku, který je nakonfigurovaný pro vysokou dostupnost (jenom na úrovni Standard), což eliminuje většinu DevOps. vyžaduje se pro tradiční IaaS uzly blockchain.  Další informace o opravách a aktualizacích najdete v článku [podporované verze hlavní knihy služby Azure blockchain](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorování a protokolování

Kromě toho služba Azure blockchain poskytuje bohatou metriku prostřednictvím služby Azure Monitor Service, která poskytuje přehledy o využití procesoru, paměti a úložiště a také užitečné poznatky o aktivitě blockchain sítě, jako jsou transakce a bloky dolována za účely. Hloubka fronty transakcí a také aktivní připojení.  Metriky je možné přizpůsobit tak, aby poskytovaly přehledy, které jsou důležité pro vaši aplikaci blockchain.  Kromě toho je možné definovat prahové hodnoty prostřednictvím výstrah umožňujících uživatelům aktivovat akce, jako je odeslání e-mailu nebo textové zprávy, spuštění aplikace logiky, funkce Azure nebo odeslání do vlastního Webhooku definovaného uživatelem.

![Metriky](./media/overview/metrics.png)

Uživatelé můžou prostřednictvím Azure Log Analytics zobrazovat protokoly související s hlavní knihou kvora nebo jiné důležité informace, jako je například pokus o připojení k uzlům transakcí.

## <a name="built-in-consortium-management"></a>Integrovaná správa konsorcií

Při nasazování prvního blockchain člena se buď připojte, nebo vytvořte novou konsorcium.  Konsorcium je logická skupina, která slouží ke správě zásad správného řízení a připojení mezi blockchain členy, kteří pracují v procesu s více stranami.  Služba Azure blockchain poskytuje předdefinované ovládací prvky zásad správného řízení prostřednictvím předem definovaných inteligentních kontraktů, které určují, jaké akce mohou členové konsorcia provádět.  Tyto ovládací prvky zásad správného řízení je možné přizpůsobit správcem konsorcia podle potřeby. Při vytváření nového konsorcia je váš blockchain člen výchozím správcem konsorcia a umožňuje tak pozvat jiné strany, aby se připojili k vašemu konsorciu.  Konsorcium můžete spojit pouze v případě, že jste byli dříve pozváni.  Při spojení s konsorciem se váš blockchain člen řídí ovládacími prvky zásad správného řízení, které provádí správce konsorcia.

![Správa konsorcia](./media/overview/consortium.png)

Akce správy konsorcia, jako je přidávání a odebírání členů z konsorcia, je možné použít prostřednictvím PowerShellu a REST API. Konsorcium můžete programově spravovat pomocí společných rozhraní, nikoli změnou a odesláním inteligentních kontraktů založených na pevné úrovni. Další informace najdete v tématu [Správa konsorcia](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Vývoj s využitím známých vývojářských nástrojů

Na základě open source hlavní knihy Ethereem kvora můžete vyvíjet aplikace pro službu Azure blockchain stejným způsobem jako u stávajících aplikací Ethereem. Při práci s předními průmyslovými partnery Visual Studio Code rozšíření Azure blockchain Development Kit umožňuje vývojářům využít známé nástroje, jako je Truffle Suite, k sestavování inteligentních smluv. Pomocí rozšíření Azure blockchain Development Kit můžou vývojáři vytvářet nebo se připojovat k a stávajícímu konsorciu, abyste mohli vytvářet a nasazovat své inteligentní smlouvy z jednoho integrovaného vývojového prostředí (IDE). Pomocí rozšíření Azure blockchain Visual Studio Code můžete vytvořit existující konsorcium nebo se k němu připojit, abyste mohli vytvářet a nasazovat své inteligentní smlouvy z jednoho integrovaného vývojového prostředí (IDE). Další informace najdete v tématu [Azure blockchain Development Kit na webu vs Code Marketplace](https://aka.ms/vscodebcextension) a v [uživatelské příručce k sadě Azure blockchain Development Kit](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Potřebujete pomáhat nebo mít zpětnou vazbu?

* Navštivte [blog Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)a [Fórum Azure blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít, vyzkoušejte si rychlý Start nebo si přečtěte další podrobnosti z těchto zdrojů.
* [Vytvoření členu blockchain pomocí Azure Portal](create-member.md) nebo [Vytvoření členu BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* Porovnání nákladů a kalkulačky najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/blockchain-service).
* Vytvoření první aplikace pomocí [vývojové sady Azure blockchain](https://github.com/Azure-Samples/blockchain-devkit)
* [Uživatelská příručka](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) k rozšíření Azure blockchain VSCode
