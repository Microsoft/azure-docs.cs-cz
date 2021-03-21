---
title: Co je nového Poznámky k verzi – služba Azure blockchain
description: Zjistěte, co je nového ve službě Azure blockchain, jako je například nejnovější zpráva k vydání verze, verze, známé problémy a nadcházející změny.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94335022"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Co je nového ve službě Azure blockchain?

> Přečtěte si informace o tom, kdy se tato stránka na aktualizace znovu navštíví zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` do [ ![ ikony čtečky kanálů RSS](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522)čtečky kanálů RSS.

Služba Azure blockchain se průběžně dostává na základě vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nové funkce
- Upgrady verze
- Známé problémy

---

## <a name="june-2020"></a>Červen 2020

### <a name="version-upgrades"></a>Upgrady verze

- Upgrade verze kvora na 2.6.0. S verzí 2.6.0 můžete odesílat podepsané privátní transakce. Další informace o odesílání privátních transakcí najdete v [dokumentaci k rozhraní API kvora](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis).
- Upgrade verze Tessera na 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Velikost kontraktu a velikost transakce se zvýšily na 128 KB.

Typ: Změna konfigurace

Velikost kontraktu (MaxCodeSize) se zvýšila na 128 KB, abyste mohli nasazovat větší objemy inteligentních kontraktů. Také se zvýšila velikost transakce (txnSizeLimit) na 128 KB. Změny konfigurace se vztahují na nové konsorcia vytvořené ve službě Azure blockchain po června 19 2020.

### <a name="trietimeout-value-reduced"></a>Snížená hodnota TrieTimeout

Typ: Změna konfigurace

Hodnota TrieTimeout se snížila, takže stav v paměti se zapisuje na disk častěji. Nižší hodnota zajišťuje rychlejší obnovení uzlu v nečastém případě havárie uzlu.

## <a name="may-2020"></a>Květen 2020

### <a name="version-upgrades"></a>Upgrady verze

- Upgrade verze Ubuntu na 18,04
- Upgrade verze kvora na 2.5.0
- Tessera aktualizace verze 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Služba Azure blockchain podporuje odesílání transakcí rawPrivate.

Typ: funkce

Zákazníci mohou podepisovat soukromé transakce mimo účet na uzlu.

### <a name="two-phase-member-provisioning"></a>Zřizování dvou fází členů

Typ: vylepšení

Dvě fáze vám pomůžou optimalizovat scénáře, kdy je člen vytvořen v dlouhodobém stávajícím konsorciu. Infrastruktura členů je zřízena v první fázi. Ve druhé fázi je člen synchronizován s blockchain. Dvoufázové zřizování pomáhá předcházet selhání vytvoření člena z důvodu vypršení časových limitů.

## <a name="known-issues"></a>Známé problémy

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>funkce ETH. estimateGas vyvolá výjimku v kvoru v 2.6.0

V kvoru v 2.6.0 volání funkce *ETH. estimateGas* bez zadání dalšího parametru *hodnoty* způsobí výjimku *selhání obslužné rutiny metody* . Tým kvora byl upozorněn a na konci července 2020 byla opravena. Dokud není k dispozici oprava, můžete použít následující alternativní řešení:

- Vyhněte se použití *ETH. estimateGas* , protože může ovlivnit výkon. Další informace o potížích s výkonem ETH. estimateGas najdete v tématu [volání funkce ETH. estimateGas snižuje výkon](#calling-ethestimategas-function-reduces-performance). Zahrňte hodnotu plynu pro každou transakci. Většina knihoven bude volat ETH. estimateGas, pokud není poskytnuta hodnota plynů, což způsobí selhání kvora v 2.6.0.
- Pokud potřebujete volat *ETH. estimateGas*, tým kvora navrhne, abyste jako alternativní řešení předávali další *hodnotu* parametru jako *0* .

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>Dolování se zastaví, pokud je méně než čtyři uzly validátoru.

Provozní sítě by měly mít aspoň čtyři uzly validátoru. Kvorum doporučuje aspoň čtyři uzly validátoru, aby splnily odolnost proti chybám IBFT selhání (3F + 1). Měli byste mít aspoň dva uzly úrovně *Standard* služby Azure blockchain, abyste získali čtyři uzly validátoru. Standardní uzel je zřízen se dvěma uzly validátoru.  

Pokud síť blockchain ve službě Azure blockchain nemá čtyři uzly validátoru, pak se může dolování v síti zastavit. Můžete zjistit, že se dolování zastavilo nastavením výstrahy na zpracované bloky. V dobré síti bude zpracovaný blok 60 bloků na jeden uzel za pět minut.

Tým služby Azure Blockchain je jako zmírnění rizik nutné restartovat uzel. Zákazníci musí otevřít žádost o podporu pro restartování uzlu. Tým služby Azure blockchain pracuje na automatickém zjišťování a odstraňování problémů dolování.

Použijte úroveň *Standard* pro nasazení produkčních úrovní. Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Data Manager vyžaduje uzel úrovně Standard.

Pokud používáte blockchain Data Manager, použijte úroveň *Standard* . Úroveň *Basic* má pouze 4 GB paměti. Proto není možné škálovat na použití vyžadované blockchain Data Manager a dalšími službami, které jsou v něm spuštěné.

Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Velký objem volání účtu Unlock způsobí selhání geth.

Velký objem volání účtu Unlock při odeslání transakce může způsobit selhání geth v uzlu transakce. V důsledku toho je nutné zastavit přijímání transakcí. V opačném případě se fronta čekající transakce zvýší.

Geth se automaticky restartuje během méně než minuty. V závislosti na uzlu může synchronizace trvat dlouhou dobu. Tým služby Azure blockchain umožňuje funkci archivace, která zkrátí čas na synchronizaci.

Chcete-li identifikovat havárie geth, můžete v protokolech aplikací vyhledat všechny chybové zprávy v protokolech blockchain. Můžete také zjistit, zda se zpracované bloky snížily během nárůstu probíhajících transakcí.

Chcete-li tento problém zmírnit, odešlete podepsané transakce namísto odeslání nepodepsaných transakcí pomocí příkazu k odemknutí účtu. Pro transakce, které už jsou podepsané externě, není potřeba účet odemknout.

Pokud chcete odeslat nepodepsané transakce, odemkněte účet pro nekonečný čas odesláním 0 jako parametru Time v příkazu odemknout. Po odeslání všech transakcí můžete účet uzamknout zpátky.  

Níže jsou uvedené parametry geth, které používá služba Azure blockchain. Tyto parametry nemůžete upravit.

- Doba přiblokování Istanbul: 5 sekund
- Limit podlahového plynu: 700000000
- Limit ceil – plynů: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Velký objem privátních transakcí snižuje výkon

Pokud používáte úroveň Basic a privátní transakce služby Azure blockchain, může dojít k chybě Tessera.

Chybu Tessera můžete zjistit tak, že zkontrolujete protokoly aplikace blockchain a vyhledáte zprávu `Tessera crashed. Restarting Tessera...` .

Služba Azure blockchain se restartuje Tessera, když dojde k chybě. Restartování trvá přibližně minutu.

Pokud posíláte velký objem privátních transakcí, použijte úroveň *Standard* . Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.

### <a name="calling-ethestimategas-function-reduces-performance"></a>Volání funkce ETH. estimateGas snižuje výkon.

Vícenásobné volání funkce *ETH. estimateGas* snižuje počet transakcí za sekundu drasticky. Nepoužívejte funkci *ETH. estimateGas* pro každé odeslání transakce. Funkce *ETH. estimateGas* je náročné na paměť.

Pokud je to možné, použijte hodnotu konzervativního plynu pro odeslání transakcí a minimalizujte použití *ETH. estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Neohraničené smyčky v inteligentních kontraktech snižují výkon

Vyhněte se neohraničeným smyčkám v rámci inteligentních kontraktů, protože mohou snížit výkon. Další informace naleznete v následujících zdrojích:

- [Vyhněte se neohraničeným smyčkám](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Doporučené postupy zabezpečení pro inteligentní kontrakty](https://github.com/ConsenSys/smart-contract-best-practices)
- [Pokyny pro inteligentní kontrakty poskytované kvorem](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Pokyny týkající se limitů a smyček plynů, které poskytuje plná pevnost](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)