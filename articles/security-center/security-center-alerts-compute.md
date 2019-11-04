---
title: Detekce hrozeb pro nativní výpočetní prostředí cloudu v Azure Security Center | Microsoft Docs
description: Tento článek představuje nativní výstrahy cloudu, které jsou k dispozici v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f6fba7bc8e8b7d040805f0be62d436caebf638af
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520905"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Detekce hrozeb pro nativní výpočetní prostředí cloudu v Azure Security Center

Jako nativní řešení má Azure Security Center jedinečný přehled o interních protokolech pro identifikaci metodologie útoků napříč několika cíli. V tomto článku jsou uvedené výstrahy pro tyto služby Azure:

* [Azure App Service](#app-services)
* [Kontejnery Azure](#azure-containers) 

> [!NOTE]
> Tyto služby nejsou aktuálně k dispozici v oblastech cloudu Azure a svrchovaného cloudu.

## Azure App Service<a name="app-services"></a>

Security Center používá škálování cloudu k identifikaci útoků cílících na aplikace běžící přes App Service. Útočníci hledají webové aplikace a využívají slabých míst. Před směrováním do konkrétních prostředí požadavky na aplikace běžící v Azure procházejí několika branami, kde jsou zkontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití a útočníků a k získání nových vzorů, které se použijí později.

Díky viditelnosti, kterou Azure nabízí jako poskytovatel cloudu, Security Center analyzuje App Service interní protokoly k identifikaci metodologie útoku na více cílů. Například metodologie zahrnuje rozšířenou kontrolu a distribuované útoky. Tento typ útoku obvykle pochází z malé podmnožiny IP adres a ukazuje vzory procházení k podobným koncovým bodům na více hostitelích. Útoky hledají zranitelnou stránku nebo modul plug-in a nelze je identifikovat z hlediska jednoho hostitele.

Security Center má také přístup k podkladovým izolovaným prostorům a virtuálním počítačům. Společně s pamětí forenzní může infrastruktura sdělit příběh z nového útoku, který je v nevolném případě napadený v zákaznických počítačích. Proto i v případě, že je Security Center nasazena po zneužití webové aplikace, může být schopna detekovat průběžné útoky.

> [!div class="mx-tableFixed"]

|Upozornění|Popis|
|---|---|
|**Zjistila se podezřelá vyvolání motivu WordPress.**|Protokol aktivity App Service označuje možnou aktivitu vkládání kódu na prostředek App Service.<br/> Tato podezřelá aktivita se podobá aktivitě, která pracuje s motivem WordPress, aby podporovala spouštění kódu na straně serveru následovaný přímým webovým požadavkem na vyvolání souboru s tímto tématem. Tento typ aktivity může být součástí kampaně útoku přes WordPress.|
|**Bylo zjištěno připojení k webové stránce z neobvyklé IP adresy.**|Protokol aktivit App Service označuje připojení k citlivé webové stránce ze zdrojové adresy, která se k němu ještě nepřipojila. To může znamenat, že se někdo pokusí o útok hrubou silou na stránky pro správu vaší webové aplikace. Může to být také výsledkem legitimního uživatele, který používá novou IP adresu.|
|**V nástroji Analýza hrozeb se našla IP adresa, která se připojila k vašemu Azure App Service rozhraní FTP.**|Analýza protokolů App Service FTP zjistila připojení ze zdrojové adresy, která byla nalezena v informačním kanálu analýzy hrozeb. Během tohoto připojení uživatel přistupoval k uvedeným stránkám.|
|**Zjistilo se webové otisky prstů.**|Protokol aktivit App Service označuje možnou aktivitu otisku webu na prostředku App Service. <br/>Tato podezřelá aktivita je přidružená k nástroji s názvem blind slonů. Nástroj vyhledá webové servery otiskem prstů a pokusí se zjistit nainstalované aplikace a jejich verze. Útočníci často používají tento nástroj k tomu, aby vyhledali chyby zabezpečení webových aplikací.|
|**Zjištěn podezřelý přístup k potenciálně ohroženým webovým stránkám**|Protokol aktivity App Service označuje, že se použila webová stránka, na kterou se jeví citlivý. <br/>Tato podezřelá aktivita pochází ze zdrojové adresy, jejíž model přístupu se podobá webovému skeneru. Tento druh aktivity se často přidruží k pokusu útočníka, aby zkontroloval vaši síť a pokusil se získat přístup k citlivým nebo ohroženým webovým stránkám.|
|**Soubor PHP ve složce pro nahrání**|Protokol aktivity App Service indikuje, že se k nějakému objektu PHP ve složce pro nahrání získala podezřelá stránka PHP. <br/>Tento typ složky obvykle neobsahuje soubory PHP. Existence tohoto typu souboru může ukazovat na zneužití s využitím libovolných chyb zabezpečení při nahrávání souborů.|
|**Pokus o spuštění příkazů systému Linux na App Service Windows**|Analýza App Servicech procesů zjistila pokus o spuštění příkazu pro Linux v App Service Windows. Tuto akci spustila webová aplikace. Toto chování se často zobrazuje při kampaních, které využívají chybu zabezpečení ve společné webové aplikaci.|
|**Zjistilo se podezřelé spuštění PHP.**|Protokoly počítače označují, že je spuštěný podezřelý proces PHP. Akce zahrnovala pokus o spuštění příkazů operačního systému nebo kódu PHP z příkazového řádku pomocí procesu PHP. I když může být toto chování legitimní, ve webových aplikacích může toto chování znamenat škodlivé aktivity, například pokusy o nainfikování webů pomocí webových prostředí.|
|**Zpracovat zpracování z dočasné složky**|Analýza App Servicech procesů zjistila spuštění procesu z dočasné složky aplikace. I když může být toto chování legitimní, ve webových aplikacích může toto chování znamenat škodlivé aktivity.|
|**Byl zjištěn pokus o spuštění příkazu s vysokým oprávněním.**|Analýza App Servicech procesů zjistila, že došlo k pokusu o spuštění příkazu, který vyžaduje vysoké oprávnění. Příkaz běžel v kontextu webové aplikace. I když může být toto chování legitimní, ve webových aplikacích může toto chování znamenat škodlivé aktivity.|

## Kontejnery Azure<a name="azure-containers"></a>

Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnerová prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Zjišťují se hrozby na různých úrovních: 

* Agent Security Center **úrovně hostitele** (dostupný na úrovni Standard, viz [ceny](security-center-pricing.md) ) monitoruje Linux pro podezřelé aktivity. Agent aktivuje výstrahy pro podezřelé aktivity pocházející z uzlu nebo kontejneru, který je v něm spuštěný. Příklady takových aktivit zahrnují detekci webového prostředí a připojení se známými podezřelými IP adresami. </br>
Pro lepší přehled o zabezpečení vašich kontejnerových prostředí agent sleduje analýzu specifickou pro kontejnery. Aktivuje výstrahy pro události, jako je vytvoření privilegovaného kontejneru, podezřelý přístup k serverům rozhraní API a servery Secure Shell (SSH) běžící uvnitř kontejneru Docker.

    >[!NOTE]
    > Pokud se rozhodnete neinstalovat agenty na hostitele, bude se vám zobrazovat jenom podmnožina výhod a upozornění detekce hrozeb. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.

* Pro **úroveň clusteru AKS**je sledování detekce hrozeb založené na analýze protokolů auditu Kubernetes. Pokud chcete toto monitorování bez **agentů** povolit, přidejte na stránce s **nastavením cenové &** možnost Kubernetes (podívejte se na [ceny](security-center-pricing.md)). Pokud chcete na této úrovni generovat výstrahy, Security Center monitorovat služby spravované AKS pomocí protokolů načtených pomocí AKS. Mezi příklady událostí na této úrovni patří exponované řídicí panely Kubernetes, vytváření vysoce privilegovaných rolí a citlivých připojení. 

    >[!NOTE]
    > Security Center generuje upozornění na detekci pro akce a nasazení služby Azure Kubernetes, ke kterým dochází po povolení možnosti Kubernetes v nastaveních předplatného. 

Náš globální tým výzkumných pracovníků pro zabezpečení nepřetržitě monitoruje i na šířku hrozby. Přidávají výstrahy a chyby specifické pro kontejner při jejich zjištění.