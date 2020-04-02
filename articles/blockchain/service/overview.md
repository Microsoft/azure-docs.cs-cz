---
title: Přehled služby Azure Blockchain Service
description: Přehled služby Azure Blockchain
ms.date: 03/30/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: 821bac0da13209e5126f5bab109aa0895ade840a
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529145"
---
# <a name="what-is-azure-blockchain-service"></a>Co je Azure Blockchain Service?

Azure Blockchain Service je plně spravovaná služba hlavní knihy, která uživatelům umožňuje růst a provozovat blockchainové sítě ve velkém měřítku v Azure. Poskytováním jednotného řízení pro správu infrastruktury i zásad správného řízení blockchainových sítí poskytuje služba Azure Blockchain Service:

* Jednoduché nasazení a provoz v síti
* Integrovaná správa konsorcia
* Vyvíjejte inteligentní smlouvy se známými vývojovými nástroji

Služba Azure Blockchain service je navržena tak, aby podporovala více protokolů hlavní knihy. V současné době poskytuje podporu pro ethereum [Kvorum](https://www.goquorum.com/) pomocí [Istanbul byzantské odolnosti proti chybám (IBFT)](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) konsensu mechanismus.

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Můžete se zaměřit na vývoj aplikací a obchodní logiku, spíše než přidělování času a prostředků pro správu virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat ve vývoji aplikace pomocí nástrojů a platformy s otevřeným zdrojovým kódem, které si vyberete, abyste mohli poskytovat svá řešení, aniž byste se museli učit nové dovednosti.

## <a name="network-deployment-and-operations"></a>Nasazení a provoz sítě

Nasazení služby Azure Blockchain se provádí prostřednictvím portálu Azure, cli Azure nebo kódu Visual Studia pomocí rozšíření Azure Blockchain. Nasazení je zjednodušené, včetně zřizování uzly transakce a validátoru, Virtuální sítě Azure pro izolaci zabezpečení, stejně jako úložiště spravované službou.  Kromě toho uživatelé při nasazování nového člena blockchainu také vytvářejí nebo se připojují k konsorciu.  Konsorcia umožňují více stranám v různých předplatných Azure, aby mohli bezpečně komunikovat mezi sebou na sdíleném blockchainu.  Toto zjednodušené nasazení snižuje nasazení blockchainové sítě ze dnů na minuty.

### <a name="performance-and-service-tiers"></a>Úrovně výkonu a služeb

Služba Azure Blockchain service nabízí dvě úrovně služeb: *Basic* a *Standard*. Každá úroveň nabízí jiný výkon a funkce pro podporu lehkých vývojových a testovacích úloh až po masivně škálovaná produkční nasazení blockchainu. Vrstvu *Basic* použijte pro vývoj, testování a testování konceptů. Použijte *úroveň Standard* pro nasazení výrobní třídy. Obě vrstvy obsahují alespoň jeden uzel transakce a jeden uzel validátoru (Basic) nebo dva uzly validátoru (Standard). 

![Cenové úrovně](./media/overview/pricing-tiers.png)

Kromě toho, že nabízí dva validátoru uzly, *Standard* úroveň poskytuje dvě *virtuální jádra* pro každou transakci a validátor uzel vzhledem k tomu, *základní* úroveň nabízí 1 virtuální jádro konfigurace.  Tím, že nabízí 2 virtuální jádra pro transakční a validátoru uzly, 1 virtuální jádro může být vyhrazeno pro ledger kvora, zatímco zbývající 1 virtuální jádro lze použít pro další služby související s infrastrukturou, což zajišťuje optimální výkon pro produkční blockchainové úlohy. Další informace o podrobnostech o cenách najdete v [tématu Ceny služby Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Bezpečnost a údržba

Po zřízení prvního člena blockchainu máte možnost přidat do svého člena další transakční uzly.  Ve výchozím nastavení jsou uzly transakcí zabezpečeny prostřednictvím pravidel brány firewall a vyžadují konfiguraci pro přístup.  Kromě toho všechny uzly transakce šifrovat data v pohybu přes TLS.  Existuje několik možností pro zabezpečení přístupu k transakčním uzlům, včetně pravidel brány firewall, základního ověřování, přístupových klíčů a integrace služby Azure Active Directory. Další informace naleznete v [tématu Konfigurace uzlů transakcí](configure-transaction-nodes.md) a [konfigurace přístupu služby Azure Active Directory](configure-aad.md).

Služba Azure Blockchain Service jako spravovaná služba zajišťuje, že uzly člena blockchainu jsou opraveny nejnovějšími aktualizacemi softwaru hostitelského operačního systému a hlavní knihy, které jsou nakonfigurované pro vysokou dostupnost (pouze úroveň Standard), což eliminuje velkou část devopů požadovaných pro tradiční blockchainové uzly IaaS.  Další informace o opravách a aktualizacích najdete v [tématu podporované verze hlavní knihy služby Azure Blockchain .](ledger-versions.md)

### <a name="monitoring-and-logging"></a>Monitorování a protokolování

Služba Azure Blockchain navíc poskytuje bohaté metriky prostřednictvím služby Azure Monitor Service, která poskytuje přehled o využití procesoru, paměti a úložiště uzlů.  Azure Monitor také poskytuje užitečné přehledy o aktivitě blockchainové sítě, jako jsou transakce a bloky, které se inkasují, hloubka fronty transakcí a aktivní připojení.  Metriky lze přizpůsobit tak, aby poskytovaly zobrazení přehledů, které jsou důležité pro vaši blockchainovou aplikaci.  Kromě toho prahové hodnoty lze definovat prostřednictvím výstrah, které uživatelům umožňují aktivovat akce, jako je odeslání e-mailu nebo textové zprávy, spuštění aplikace logiky, funkce Azure nebo odeslání do vlastního definovaného webhooku.

![Metriky](./media/overview/metrics.png)

Prostřednictvím Azure Log Analytics mohou uživatelé zobrazit protokoly související s hlavní knihou kvora nebo jiné důležité informace, jako je pokus o připojení k uzlům transakcí.

## <a name="built-in-consortium-management"></a>Integrovaná správa konsorcia

Při nasazování prvního člena blockchainu se buď připojíte, nebo vytvoříte nové konsorcium.  Konsorcium je logická skupina používaná ke správě správy a propojení mezi členy blockchainu, kteří provádějí činnost v procesu s více stranami.  Služba Azure Blockchain poskytuje integrované ovládací prvky zásad správného řízení prostřednictvím předdefinovaných inteligentních smluv, které určují, jaké akce mohou členové konsorcia provést.  Tyto ovládací prvky zásad správného řízení lze přizpůsobit podle potřeby správce konsorcia. Když vytvoříte nové konsorcium, váš člen blockchainu je výchozím správcem konsorcia, což umožňuje pozvat další strany, aby se připojily k vašemu konsorciu.  Ke konsorciu se můžete připojit pouze v případě, že jste byli pozváni dříve.  Když se připojíte ke konsorciu, váš člen blockchainu podléhá ovládacím prvkům zásad správného řízení zavedeným správcem konsorcia.

![Vedení konsorcia](./media/overview/consortium.png)

Akce správy konsorcia, jako je přidávání a odebírání členů z konsorcia, jsou přístupné prostřednictvím prostředí PowerShell a rozhraní REST API. Konsorcium můžete programově spravovat pomocí společných rozhraní, nikoli upravovat a odesílat inteligentní kontrakty založené na spolehlivosti. Další informace naleznete v [tématu správa konsorcia](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Vývoj s využitím známých vývojových nástrojů

Na základě knihy ethereum s otevřeným zdrojovým kódem můžete vyvíjet aplikace pro službu Azure Blockchain stejně jako pro stávající aplikace Ethereum. Rozšíření Azure Blockchain Development Kit Visual Studio Code extension ve spolupráci s předními průmyslovými partnery umožňuje vývojářům využívat známé nástroje, jako je Truffle Suite, k vytváření inteligentních smluv. Pomocí rozšíření Azure Blockchain Development Kit mohou vývojáři vytvářet nebo se připojovat k existujícímu konsorciu, abyste mohli vytvářet a nasazovat inteligentní kontrakty z jednoho rozhraní IDE. Pomocí rozšíření Azure Blockchain Visual Studio Code můžete vytvořit nebo se připojit k existujícímu konsorciu, abyste mohli vytvářet a nasazovat inteligentní kontrakty z jednoho ide. Další informace najdete [v tématu Azure Blockchain Development Kit na trhu VS Code marketplace](https://aka.ms/vscodebcextension) a v uživatelské [příručce k Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Publikování dat o blockchainu

Blockchain Data Manager pro službu Azure Blockchain Service zachycuje, transformuje a doručuje transakční data služby Azure Blockchain service do témat Sítě událostí Azure a poskytuje spolehlivou a škálovatelnou integraci blockchainové knihy se službami Azure. Správce dat Blockchain můžete použít k integraci aplikací a úložišť dat mimo řetězec. Další informace najdete [v tématu Blockchain Data Manager for Azure Blockchain Service](data-manager.md).

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Novinky z Azure Blockchain najdete na [blogu Azure Blockchain,](https://azure.microsoft.com/blog/topics/blockchain/) kde si můžete dát aktuální informace o nabídkách blockchainových služeb a informacích od technického týmu Azure Blockchain.

Chcete-li poskytnout zpětnou vazbu od produktů nebo požádat o nové funkce, pošlete si návrh na návrh prostřednictvím [fóra pro zpětnou vazbu Azure pro blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Podpora komunity

Spojte se s inženýry Microsoftu a odborníky na komunitu Azure Blockchain.

* [Fórum Azure Blockchain MSDN](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Technická komunita Microsoftu](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/AzureBlockchainService)

## <a name="next-steps"></a>Další kroky

Chcete-li začít, vyzkoušejte rychlý start nebo se dozvíte další podrobnosti z těchto zdrojů.
* [Vytvoření člena blockchainu pomocí portálu Azure](create-member.md) nebo [vytvoření člena blockchainu pomocí azure cli](create-member-cli.md)
* Porovnání nákladů a kalkulačky naleznete na [stránce s cenami](https://azure.microsoft.com/pricing/details/blockchain-service).
* Sestavte si svou první aplikaci pomocí [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Uživatelská příručka rozšíření](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) Azure Blockchain VSCode Extension
