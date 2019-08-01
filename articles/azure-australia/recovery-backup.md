---
title: Zálohování a zotavení po havárii v Azure Austrálie
description: Zálohování a zotavení po havárii v Microsoft Azure pro státní úřady Austrálie, které se vztahují k programu ASD, který je v podstatě 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571520"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Zálohování a zotavení po havárii v Azure Austrálie

Plán zálohování a zotavení po havárii, na kterém je podpůrná infrastruktura na místě, je pro všechny organizace zásadní. Důležitost řešení pro zálohování se zvýrazní jeho zahrnutím do australského internetového Security Center, který je v [podstatě 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

Microsoft Azure poskytuje dvě služby, které umožňují odolnost proti chybám: Azure Backup a Azure Site Recovery. Tyto služby umožňují chránit data v místním prostředí i v cloudu pro nejrůznější scénáře návrhu. Azure Backup a Azure Site Recovery jak použít společný prostředek úložiště a správy: Azure Recovery Services trezor. Tento trezor se používá ke správě, monitorování a oddělení dat Azure Backup a Azure Site Recovery.

Tento článek podrobně popisuje klíčové prvky návrhu pro implementaci Azure Backup a Azure Site Recovery v souladu s ovládacími prvky bezpečnostního programu (ISM) pro [ředitelství Austrálie (australských signálů)](https://acsc.gov.au/infosec/ism/index.htm).

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup se podobá tradičnímu místnímu řešení zálohování a poskytuje možnost zálohování místních i hostovaných dat v Azure. Azure Backup můžete použít k zálohování následujících datových typů do Azure:

* Soubory a složky
* Podporované operační systémy Windows a Linux hostované na:
  * Hypervisory technologie Hyper-V a VMWare
  * Fyzický hardware
* Podporované aplikace Microsoftu

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery replikuje úlohy sestávající z jediného virtuálního počítače nebo vícevrstvých aplikací. Replikace se podporuje z místního prostředí do Azure, mezi oblastmi Azure nebo mezi místními umístěními, které orchestruje Azure Site Recovery. Místní virtuální počítače se dají replikovat do Azure nebo do podporovaného místního hypervisoru. Po nakonfigurování Azure Site Recovery orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení.

## <a name="key-design-considerations"></a>Klíčové faktory návrhu

Při implementaci řešení pro zálohování nebo zotavení po havárii musí navržené řešení vzít v úvahu:

* Rozsah a objem dat, která mají být zachycena
* Jak dlouho se data uchovávají
* Jak se tato data bezpečně ukládají a spravují
* Zeměpisná místa, kde se data ukládají
* Rutinní postupy testování systému

ISM poskytuje pokyny k bezpečnostním hlediskům, které by měly být provedeny při návrhu řešení. Microsoft Azure poskytuje způsob, jak řešit tyto požadavky na zabezpečení.

### <a name="data-sovereignty"></a>Suverenita dat

Organizace musí zajistit, aby se při využívání cloudových úložišť na základě cloudu zachovala suverenita dat. Azure Policy poskytuje prostředky k omezení povolených umístění, kde se dá vytvořit prostředek Azure. Integrovaná Azure Policy "povolených umístění" slouží k zajištění, že prostředky Azure vytvořené v rámci rozsahu přiřazených Azure Policy lze vytvořit pouze v navržených geografických umístěních.

Azure Policy položky pro geografické omezení prostředků Azure:

* allowedLocations
* allowedSingleLocation

Tyto zásady umožňují správcům Azure omezit vytváření na seznam navržených umístění nebo dokonce na samostatné geografické umístění.

### <a name="redundant-and-geographically-dispersed-storage"></a>Redundantní a geograficky rozptýlené úložiště

Data uložená v trezoru služby Azure Recovery Services se vždycky ukládají do redundantního úložiště. Ve výchozím nastavení trezor služby Recovery Services používá geograficky redundantní úložiště Azure (GRS). Data uložená pomocí GRS se replikují do jiných datových center Azure v [sekundární spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)trezoru služby Recovery Services. Replikovaná data jsou uložená jen pro čtení a je možné je zapisovat jenom v případě, že dojde k události Azure Failover. V rámci datového centra Azure se data replikují mezi jednotlivými doménami selhání a upgradovacími doménami, aby se minimalizovala možnost výpadku hardwaru nebo údržby. GRS poskytuje roční dostupnost nejméně 99.99999999999999%.

Službu Azure Recovery Services trezor je možné nakonfigurovat tak, aby využívána místně redundantní úložiště (LRS). LRS je možnost úložiště s nižšími náklady s omezenou dostupností. Tento model redundance využívá stejnou replikaci mezi jednotlivými doménami selhání a upgradovacími doménami, ale mezi geografickými oblastmi se nereplikuje. Data umístěná v úložišti LRS, i když nejsou stejně odolná jako GRS, stále k dispozici alespoň 99,999999999% dostupnost.

Na rozdíl od tradičních technologií úložiště mimo lokalitu, jako jsou pásková média, jsou další kopie dat vytvářeny automaticky a nevyžadují žádné další administrativní režijní náklady.

### <a name="restricted-access-and-activity-monitoring"></a>Omezený přístup a monitorování aktivit

Zálohovaná data musí být chráněná před poškozením, úpravou a neschváleným odstraněním. Azure Backup i Azure Site Recovery využívají běžné prostředky infrastruktury pro správu Azure. Tato topologie Fabric poskytuje podrobné auditování, protokolování a Access Control na základě rolí (RBAC) na prostředky umístěné v rámci Azure. Přístup k zálohovaným datům je možné omezit a vybrat administrativní pracovníky a všechny akce zahrnující zálohovaná data se můžou protokolovat a auditovat.

Azure Backup i Azure Site Recovery mají integrované funkce protokolování a vytváření sestav. Všechny problémy, ke kterým dojde během zálohování nebo replikace, se oznamují správcům pomocí prostředků infrastruktury pro správu Azure.

Úložiště Azure Recovery Services má také následující dodatečné bezpečnostní míry:

* Data zálohy se uchovávají po dobu 14 dnů od provedení operace odstranění.
* Výstrahy a oznámení pro kritické operace, jako je například zastavení zálohování pomocí Delete data
* Požadavky na zabezpečení kódu PIN pro kritické operace
* Jsou zavedeny minimální kontroly rozsahu uchovávání.

Mezi tyto minimální kontroly rozsahu uchování patří:

* Denní uchování, minimálně sedm dnů uchovávání
* Týdenní uchování, minimálně čtyři týdny uchování
* V případě měsíčního uchování, minimálně po dobu uchovávání dat v průběhu tří měsíců
* Pro roční uchování, minimálně v jednom roce uchovávání

Všechna zálohovaná data uložená v Azure jsou v klidovém stavu zašifrovaná pomocí Azure Šifrování služby Storage (SSE). Tato možnost je ve výchozím nastavení povolená pro všechny nové a existující účty úložiště a nedá se zakázat. Šifrovaná data se při načtení automaticky dešifrují. Ve výchozím nastavení se data zašifrovaná pomocí SSE šifrují pomocí klíče poskytovaného a spravovaného společností Microsoft. Organizace se můžou rozhodnout pro poskytování a správu vlastního šifrovacího klíče pro použití s SSE. Díky tomu je k dispozici volitelná další vrstva zabezpečení šifrovaných dat. Tento klíč může být uložený v místním počítači nebo bezpečně v rámci trezoru klíčů Azure.

### <a name="secure-data-transport"></a>Zabezpečený přenos dat

Azure Backup šifrovaných dat při přenosu pomocí AES 256. Tato data jsou zabezpečená pomocí přístupového hesla vytvořeného administrativními pracovníky při prvním nakonfigurovaném zálohování. Microsoft nemá přístup k tomuto přístupovému heslu, což znamená, že zákazník musí zajistit, aby bylo toto heslo bezpečně uložené. Přenos dat pak probíhá mezi místním prostředím a trezorem služby Azure Recovery Services prostřednictvím zabezpečeného připojení HTTPS.  Data v rámci trezoru Recovery Services se pak zašifrují v klidovém stavu pomocí Azure SSE.

Data Azure Site Recovery se také vždy šifrují při přenosu. Všechna replikovaná data se bezpečně přepravují do Azure pomocí protokolu HTTPS a TLS. Když se zákazník Azure připojí k Azure pomocí připojení ExpressRoute, Azure Site Recovery data se odesílají prostřednictvím tohoto privátního připojení.  Když se zákazník Azure připojuje k Azure pomocí připojení VPN, replikují se data mezi místním serverem a trezorem Recovery Services bezpečně přes Internet.

Tento přenos zabezpečených síťových dat odstraní bezpečnostní riziko a požadavky na zmírnění pro správu tradičních řešení úložiště zálohování mimo lokalitu, jako jsou pásková média.

### <a name="data-retention-periods"></a>Doba uchovávání dat

Doporučuje se minimální doba uchovávání záloh po třech měsících, ale delší doba uchovávání se obvykle vyžaduje. Azure Backup může poskytnout až 9999 kopií zálohy. V případě, že jeden Azure Backup chráněné instance byl každý den, to by umožňovalo uchovávání po dobu 27 let denního zálohování. Jednotlivé měsíční zálohy chráněné instance umožňují dobu uchování 833 let. Jelikož jsou zálohovaná data zastaralá a v průběhu času se uchovávají méně podrobné zálohy, celkové okno uchování dat pro zálohování roste.  Azure neomezuje dobu, po kterou může data zůstat v úložišti Azure Recovery Services, jenom celkový počet záloh na instanci. Mezi obnovením ze starých nebo nových záloh současně nedochází k žádnému rozdílu ve výkonu, takže každé obnovení trvá stejné množství času.

Azure Recovery Services trezor má na začátku řadu výchozích zásad zálohování a uchovávání.  Administrativní pracovníci můžou také vytvářet vlastní zásady zálohování a uchovávání.

![Zásady Azure Backup](media/create-policy.png)

Při konfiguraci zásad Azure Backup a uchovávání informací je potřeba najít rovnováhu mezi četností zálohování a požadavky na dlouhodobé uchovávání.

### <a name="backup-and-restore-testing"></a>Zálohování a obnovení – testování

ISM doporučuje testování zálohovaných dat, aby se zajistilo, že chráněná data budou platná v případě, že je vyžadováno obnovení nebo převzetí služeb při selhání. Azure Backup a Azure Site Recovery také poskytují možnost testovat chráněná data po jejím zálohování nebo replikaci. Data spravovaná pomocí Azure Backup můžou být obnovená do navrženého umístění a konzistence dat se pak dají ověřit.

Azure Site Recovery má neintegrovanou schopnost provádět testování převzetí služeb při selhání. Úlohy replikované do trezoru Recovery Services lze obnovit do navrženého prostředí Azure. Cílové prostředí pro obnovení může být plně izolované od jakéhokoli produkčního prostředí, aby se zajistilo, že při provádění testu nedochází k žádným dopadům na produkční systémy. Po dokončení testu může být testovací prostředí a všechny prostředky okamžitě smazáno, aby se snížily provozní náklady.

Testování a ověřování převzetí služeb při selhání je možné automatizovat pomocí Azure Automation služby integrované do platformy Azure. To umožňuje, aby se testování převzetí služeb při selhání naplánovalo automaticky, aby se zajistilo, že se data úspěšně replikují do Azure.

## <a name="next-steps"></a>Další kroky

Přečtěte si článek o [zajištění zabezpečení pomocí Azure Policy](azure-policy.md).
