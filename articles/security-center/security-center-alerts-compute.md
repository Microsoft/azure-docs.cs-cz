---
title: Pro nativní výpočetní prostředí cloud ve službě Azure Security Center detekce hrozeb | Dokumentace Microsoftu
description: Toto téma představuje cloudu výstrah nativní výpočetní prostředí ve službě Azure Security Center. ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571683"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Detekce hrozeb pro cloudové nativní výpočetních prostředků ve službě Azure Security Center

Security Center bude využívat jako poskytovatele cloudu, jedinečné viditelnost, které je interní protokoly analyzovat a identifikovat metodologie útoku na více cílů. Toto téma představuje výstrahy k dispozici pro následující služby Azure:

* [Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Azure App Service <a name="app-services"></a>

Security Center bude využívat škálovatelnosti cloudu k identifikaci útoků cílení na zákazníky aplikace běžící v Azure App Service. U webových aplikací je téměř v jakékoli moderní síťové probe útočníci zneužít slabá místa a vyhledat. Před přesměrováním do konkrétních prostředí, požadavky na aplikace běžící v Azure projít několik bran, kde jsou zkontrolovány a protokoluje. Tato data se pak použije k identifikaci zneužití, útoků a další nová schémata, které se později použijí.

Využitím viditelnost obsahující jako poskytovatele cloudových služeb Azure Security Center analyzuje protokoly interní služby App Service k identifikaci metodologie útoku na více cílů. Například rozsáhlou kontrolu a distribuovanými útoky. Tento typ útoku obvykle pochází z malou část IP adresy a ukazuje způsoby procházení na podobné koncové body na více hostitelích, hledání zranitelné stránky nebo modulu plug-in. To lze zjistit pomocí cloudu, ale není možné zjistit z pohledu jednoho hostitele.

Security Center má také přístup k základní karantény a virtuální počítače. Spolu s paměti forenzní infrastruktury můžete vyprávíte ke grafu příběh od nové útoku v reálném světě na ohrožení v počítače zákazníků. Proto se Security Center dokáže zjistit útoky na dlouhé webových aplikací, po zneužití.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Podezřelé vyvolání motiv WordPress zjistil**|Protokol aktivit Azure App Service označuje možné kódu vkládání činnost pro váš prostředek služby App Service.<br/> Toto podezřelé aktivity se podobá aktivitu, která zpracovává WordPress motiv pro podporu na straně serveru provádění kódu, za nímž následuje přímé webový požadavek pro vyvolání souboru motivu ní manipulováno. Tento typ aktivity ukázala v minulosti jako součást kampaních obsahujících útoky přes WordPress.|
|**Připojení k webové stránce z neobvyklé IP adresy, které zjistil**|Protokol aktivit Azure App Service určuje připojení citlivé webovou stránku z zdrojové adresy, které se ještě nikdy nepřipojili k němu před. To může znamenat, že se někdo o útok hrubou silou na stránky pro správu vaší webové aplikace. Může být také výsledkem legitimní uživatel používá novou IP adresu.|
|**Integrační balíček, který připojený k rozhraní Azure App Service FTP nebyl nalezen v analýzy hrozeb**|Analýzy protokolů Azure App Service FTP zjistila připojení z zdrojové adresy, která byla nalezena v kanálu analýzy hrozeb. Během tohoto připojení přístupu uživatele k stránky uvedených níže.|
|**Web otisků zjistil**|Protokol aktivit Azure App Service určuje možné webové otisků aktivity pro váš prostředek služby App Service. <br/>Zjistila se tento podezřelá aktivita je přidružen nástroj zvaný Slon skrytá. Nástroj otisky prstů webové servery a pokusí se zjistit nainstalovaných aplikací a jejich verze. Útočníci často používají tento nástroj pro zjišťování webové aplikace najít ohrožení zabezpečení.|
|**Podezřelý přístup k zjištěna možná ohrožené webové stránky**|Protokol aktivit Azure App Service určuje, že se použila webovou stránku, která se zdá být citlivé. <br/>Tento pochází z zdrojové adresy, jejichž vzory přístupu vypadá podobně jako u skener webové podezřelou aktivitu. Tento druh činnosti je často přidružený pokus ze strany útočníka kontrola vaší sítě a zkuste získat přístup k citlivým nebo ohrožené webové stránky.|
|**Soubor PHP v nahrát složku**|Protokol aktivit Azure App Service určuje, že něco použil podezřelé stránku PHP umístěný ve složce pro nahrání. <br/>Tento typ složka neobsahuje obvykle soubory PHP. Tento typ souboru existenci může znamenat využívání výhod nahrát libovolný soubor ohrožení zabezpečení.|
|**Pokus o spuštění Linuxové příkazy ve službě App Service Windows**|Analýzy služby App Service procesů zjistil pokus o spuštění příkazu Linux ve službě App Service Windows. Tato akce byla spuštěna webová aplikace. Toto chování se často vyskytují během kampaně, které zneužije chybu zabezpečení v běžné webové aplikace.|
|**Podezřelé spuštění PHP zjistil**|Protokoly počítače ukazují, pole, které podezřelý proces PHP běží. Akce zahrnuté pokus o spuštění příkazů operačního systému nebo kód PHP z příkazového řádku pomocí procesu PHP. Toto chování může být legitimní, ve webových aplikacích toto chování se taky vyskytuje při škodlivých aktivit, jako jsou pokusy o terčem útoků webů pomocí webových prostředí.|
|**Spouštění procesů z dočasné složky**|App Service procesy analýzy zjistil spuštění procesu z dočasné složky aplikace. Když toto chování může být legitimní ve webových aplikacích, které toto chování se vyskytuje také při škodlivých aktivit.|
|**Pokus o spuštění příkazu vysoká oprávnění zjistil**|Analýza služby App Service procesů zjistil pokus o spuštění příkazu, která vyžaduje vysokou úrovní oprávnění. Tento příkaz spustili v kontextu webové aplikace. Když toto chování může být legitimní ve webových aplikacích, které toto chování se vyskytuje také při škodlivých aktivit.|

> [!NOTE]
> Detekce hrozeb Security Center pro službu App Service není momentálně k dispozici v oblastech suverénních cloudů a Azure government.

Další informace o detekce hrozeb služby App Service výstrahy navštivte chránit služby App Service pomocí Azure Security Center a přečtěte si, jak povolit monitorování a ochranu úloh služby App Service.

## Kontejnery <a name="azure-containers"></a>

Security Center poskytuje Reálný čas detekce hrozeb pro vaše kontejnery na počítačích s Linuxem založená na platformě pro démona auditd. Výstrahy Identifikujte několik podezřelých aktivit Dockeru, jako je například vytvoření privilegovaných kontejneru na hostiteli, údaj o běžící uvnitř kontejneru Dockeru nebo s využitím kryptografických modulů pro dolování server Secure Shell (SSH). Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů. Kromě Linux detekce Security Center také nabízí analýzy, které jsou určené pro nasazení kontejnerů.
