---
title: Detekce hrozeb pro nativní výpočetní prostředky cloudu v Azure Security Center | Microsoft Docs
description: Toto téma prezentuje nativní výpočetní výstrahy cloudu, které jsou dostupné v Azure Security Center.
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
ms.openlocfilehash: 78f7633af1631eab8fdfb21fb8ff94eafc0247a9
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013360"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Detekce hrozeb pro nativní výpočetní prostředky cloudu v Azure Security Center

Jako poskytovatel cloudu Azure Security Center používá jedinečnou viditelnost, kterou potřebuje k analýze interních protokolů a identifikaci metodologie útoku na více cílů. V tomto tématu jsou uvedeny výstrahy, které jsou k dispozici pro následující služby Azure:

* [Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center používá škálování cloudu k identifikaci útoků cílících na aplikace běžící přes App Service. Webové aplikace jsou běžné v moderních sítích a sondy pro útočníky k nalezení těchto a zneužití slabých stránek. Před směrováním do konkrétních prostředí požadavky na aplikace běžící v Azure procházejí několika branami, kde jsou zkontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití a útočníků a k získání nových vzorů, které se použijí později.

Díky viditelnosti, kterou Azure nabízí jako poskytovatel cloudu, Security Center analyzuje App Service interní protokoly k identifikaci metodologie útoku na více cílů. Například metodologie zahrnuje rozšířenou kontrolu a distribuované útoky. Tento typ útoku obvykle pochází z malé podmnožiny IP adres a vykazuje vzory procházení s podobnými koncovými body na více hostitelích. Útoky hledají zranitelnou stránku nebo modul plug-in a nelze je identifikovat z hlediska jednoho hostitele.

Security Center má také přístup k podkladovým izolovaným prostorům a virtuálním počítačům. Společně s pamětí forenzní může infrastruktura sdělit příběh z nového útoku, který je v nevolném případě napadený v zákaznických počítačích. Proto Security Center můžou detekovat útoky na webové aplikace dlouho po zneužití.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjistila se podezřelá vyvolání motivu WordPress.**|Protokol aktivity App Service označuje možnou aktivitu vkládání kódu na prostředek App Service.<br/> Tato podezřelá aktivita se podobá aktivitě, která pracuje s motivem WordPress, aby podporovala spouštění kódu na straně serveru následovaný přímým webovým požadavkem na vyvolání souboru s tímto tématem. Tento typ aktivity může být součástí kampaně útoku přes WordPress.|
|**Bylo zjištěno připojení k webové stránce z neobvyklé IP adresy.**|Protokol aktivit App Service označuje připojení k citlivé webové stránce ze zdrojové adresy, která se k němu ještě nepřipojila. To může znamenat, že se někdo pokusí o útok hrubou silou na stránky pro správu vaší webové aplikace. Může to být také výsledkem legitimního uživatele, který používá novou IP adresu.|
|**V nástroji Analýza hrozeb se našla IP adresa, která se připojila k vašemu Azure App Service rozhraní FTP.**|Analýza protokolů App Service FTP zjistila připojení ze zdrojové adresy, která byla nalezena v informačním kanálu analýzy hrozeb. Během tohoto připojení uživatel přistupoval k uvedeným stránkám.|
|**Zjistilo se webové otisky prstů.**|Protokol aktivit App Service označuje možnou aktivitu otisku webu na prostředku App Service. <br/>Tato podezřelá aktivita je přidružená k nástroji s názvem blind slonů. Nástroj vyhledá webové servery otiskem prstů a pokusí se zjistit nainstalované aplikace a jejich verze. Útočníci často používají tento nástroj k tomu, aby vyhledali chyby zabezpečení webových aplikací.|
|**Zjištěn podezřelý přístup k potenciálně ohroženým webovým stránkám**|Protokol aktivity App Service označuje, že se použila webová stránka, na kterou se jeví citlivý. <br/>Tato podezřelá aktivita pochází ze zdrojové adresy, jejíž model přístupu se podobá webovému skeneru. Tento druh aktivity se často přidruží k pokusu útočníka, aby zkontroloval vaši síť a mohl se pokusit získat přístup k citlivým nebo ohroženým webovým stránkám.|
|**Soubor PHP ve složce pro nahrání**|Protokol aktivity App Service indikuje, že se k nějakému objektu PHP ve složce pro nahrání získala podezřelá stránka PHP. <br/>Tento typ složky obvykle neobsahuje soubory PHP. Existence tohoto typu souboru může ukazovat na zneužití s využitím libovolných chyb zabezpečení při nahrávání souborů.|
|**Pokus o spuštění příkazů systému Linux na App Service Windows**|Analýza App Servicech procesů zjistila pokus o spuštění příkazu pro Linux v App Service Windows. Tuto akci spustila webová aplikace. Toto chování se často zobrazuje při kampaních, které využívají chybu zabezpečení ve společné webové aplikaci.|
|**Zjistilo se podezřelé spuštění PHP.**|Protokoly počítače označují, že je spuštěný podezřelý proces PHP. Akce zahrnovala pokus o spuštění příkazů operačního systému nebo kódu PHP z příkazového řádku pomocí procesu PHP. I když může být toto chování legitimní, ve webových aplikacích může toto chování znamenat škodlivé aktivity, například pokusy o nainfikování webů pomocí webových prostředí.|
|**Zpracovat zpracování z dočasné složky**|Analýza App Servicech procesů zjistila spuštění procesu z dočasné složky aplikace. I když může být toto chování legitimní, ve webových aplikacích může toto chování znamenat škodlivé aktivity.|
|**Byl zjištěn pokus o spuštění příkazu s vysokým oprávněním.**|Analýza App Servicech procesů zjistila, že došlo k pokusu o spuštění příkazu, který vyžaduje vysoké oprávnění. Příkaz běžel v kontextu webové aplikace. I když může být toto chování legitimní, ve webových aplikacích může toto chování znamenat škodlivé aktivity.|

> [!NOTE]
> Security Center detekce hrozeb pro App Service v současnosti není k dispozici v oblastech cloudu Azure pro státní správu a svrchované oblasti.

## Azure Container Service<a name="azure-containers"></a>

Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnery na počítačích se systémem Linux na základě auditovaného rozhraní. Výstrahy identifikují několik podezřelých aktivit Docker, jako je vytvoření privilegovaného kontejneru na hostiteli, označení serveru Secure Shell (SSH) spuštěného uvnitř kontejneru Docker nebo použití šifrovacího dolování hlásíu. 

Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů. Kromě detekce Linux Security Center také nabízí analýzy, které jsou konkrétnější pro nasazení kontejnerů.
