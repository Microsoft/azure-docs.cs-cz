---
title: Detekce hrozeb pro nativní výpočetní prostředky cloudu v Azure Security Center | Microsoft Docs
description: Toto téma prezentuje nativní výpočetní výstrahy cloudu, které jsou dostupné v Azure Security Center. v Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295840"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Detekce hrozeb pro nativní výpočetní prostředky cloudu v Azure Security Center

Jako poskytovatel cloudu Security Center využívá jedinečnou viditelnost, kterou potřebuje k analýze interních protokolů a identifikaci metodologie útoku na více cílů. V tomto tématu jsou uvedeny výstrahy, které jsou k dispozici pro následující služby Azure:

* [Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center využívá škálování cloudu k identifikaci útoků cílících na aplikace pro zákazníky běžící přes Azure App Service. U webových aplikací, které jsou prakticky v jakékoli moderní síti, se útočníci snaží najít tyto a zneužití slabých stránek. Před směrováním do konkrétních prostředí požadavky na aplikace běžící v Azure procházejí několika branami, kde jsou zkontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití, útočníků a k získání nových vzorů, které se použijí později.

Díky využití viditelnosti, kterou Azure nabízí jako poskytovatel cloudu, Security Center analyzuje App Service interní protokoly k identifikaci metodologie útoku na více cílů. Například rozšířené prohledávání a distribuované útoky. Tento typ útoku obvykle pochází z malé podmnožiny IP adres a vykazuje vzory procházení s podobnými koncovými body na více hostitelích a hledá zranitelnou stránku nebo modul plug-in. To lze zjistit pomocí cloudu, ale nelze je identifikovat z hlediska jednoho hostitele.

Security Center má také přístup k podkladovým izolovaným prostorům a virtuálním počítačům. Společně s pamětí forenzní může infrastruktura sdělit příběh z nového útoku, který je v nevolném případě napadený v zákaznických počítačích. Proto Security Center můžou detekovat útoky na webové aplikace dlouho po zneužití.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjistila se podezřelá vyvolání motivu WordPress.**|Protokol aktivity Azure App Service označuje možnou aktivitu vkládání kódu na prostředek App Service.<br/> Tato podezřelá aktivita se podobá aktivitě, která pracuje s motivem WordPress pro podporu spouštění kódu na straně serveru následovaný přímým webovým požadavkem na vyvolání souboru s tímto motivem. Tento typ aktivity se v minulosti zobrazil jako součást kampaně útoku přes WordPress.|
|**Bylo zjištěno připojení k webové stránce z neobvyklé IP adresy.**|Protokol aktivit Azure App Service označuje připojení k citlivé webové stránce ze zdrojové adresy, která se k němu ještě nepřipojila. To může znamenat, že se někdo pokusí o útok hrubou silou na stránky pro správu vaší webové aplikace. Může to být také výsledkem nové IP adresy, kterou používá oprávněný uživatel.|
|**V nástroji Analýza hrozeb se našla IP adresa, která se připojila k vašemu Azure App Service rozhraní FTP.**|Analýza protokolů Azure App Service FTP zjistila připojení ze zdrojové adresy, která byla nalezena v kanálu pro analýzu hrozeb. Během tohoto připojení uživatel navštívil stránky uvedené níže.|
|**Zjistilo se webové otisky prstů.**|Protokol aktivit Azure App Service označuje možnou aktivitu otisku webu na prostředku App Service. <br/>Zjištěná podezřelá aktivita je přidružená k nástroji s názvem blind slonů. Nástroj vyhledá webové servery otiskem prstů a pokusí se zjistit nainstalované aplikace a jejich verze. Útočníci často používají tento nástroj k tomu, aby vyhledali chyby zabezpečení webových aplikací.|
|**Zjištěn podezřelý přístup k potenciálně ohroženým webovým stránkám**|Protokol aktivity Azure App Service označuje, že se použila webová stránka, na kterou se jeví citlivý. <br/>Tato podezřelá aktivita pochází ze zdrojové adresy, jejíž model přístupu se podobá webovému skeneru. Tento druh aktivity se často přidruží k pokusu útočníka, aby zkontroloval vaši síť a mohl se pokusit získat přístup k citlivým nebo ohroženým webovým stránkám.|
|**Soubor PHP ve složce pro nahrání**|Protokol aktivit Azure App Service označuje, že k nějakému objektu v PHP, který se nachází ve složce pro nahrání, došlo k podezřelé stránce. <br/>Tento typ složky obvykle neobsahuje soubory PHP. Existence tohoto typu souboru může ukazovat na zneužití s využitím libovolných chyb zabezpečení při nahrávání souborů.|
|**Pokus o spuštění příkazů systému Linux na App Service Windows**|Analýza App Servicech procesů zjistila pokus o spuštění příkazu pro Linux v App Service Windows. Tuto akci spustila webová aplikace. Toto chování se často zobrazuje při kampaních, které zneužívá chybu zabezpečení ve společné webové aplikaci.|
|**Zjistilo se podezřelé spuštění PHP.**|Protokoly počítače označují, že je spuštěný podezřelý proces PHP. Akce zahrnovala pokus o spuštění příkazů operačního systému nebo kódu PHP z příkazového řádku pomocí procesu PHP. I když toto chování může být legitimní, ve webových aplikacích je toto chování také pozorováno v škodlivých aktivitách, jako jsou pokusy o nainfikování webů pomocí webových prostředí.|
|**Zpracovat zpracování z dočasné složky**|Analýza App Servicech procesů zjistila spuštění procesu z dočasné složky aplikace. I když toto chování může být legitimní, ve webových aplikacích je toto chování také pozorováno v škodlivých aktivitách.|
|**Byl zjištěn pokus o spuštění příkazu s vysokým oprávněním.**|Analýza App Servicech procesů zjistila, že došlo k pokusu o spuštění příkazu, který vyžaduje vysoké oprávnění. Příkaz běžel v kontextu webové aplikace. I když toto chování může být legitimní, ve webových aplikacích je toto chování také pozorováno v škodlivých aktivitách.|

> [!NOTE]
> Security Center detekce hrozeb pro App Service v současnosti není k dispozici v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Další informace o výstrahách detekce hrozeb App Service najdete v tématu Ochrana App Service pomocí Azure Security Center a přečtěte si, jak povolit monitorování a ochranu vašich App Service úloh.

## Kontejnery<a name="azure-containers"></a>

Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnery na počítačích se systémem Linux na základě auditovaného rozhraní. Výstrahy identifikují několik podezřelých aktivit Docker, například vytvoření privilegovaného kontejneru na hostiteli, indikaci serveru Secure Shell (SSH) běžícího v kontejneru Docker nebo použití šifrovacího dolování hlásíu. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů. Kromě detekce Linux Security Center také nabízí analýzy, které jsou konkrétnější pro nasazení kontejnerů.
