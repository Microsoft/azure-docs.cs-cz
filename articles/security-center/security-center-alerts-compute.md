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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: f2fda893ad84aaf9d11d26d761f5395c7f5650d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666368"
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

Seznam výstrah Azure App Service najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureappserv).

## Kontejnery Azure<a name="azure-containers"></a>

Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnerová prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Zjišťují se hrozby na různých úrovních: 

* Agent Security Center **úrovně hostitele** (dostupný na úrovni Standard, viz [ceny](security-center-pricing.md) ) monitoruje Linux pro podezřelé aktivity. Agent aktivuje výstrahy pro podezřelé aktivity pocházející z uzlu nebo kontejneru, který je v něm spuštěný. Příklady takových aktivit zahrnují detekci webového prostředí a připojení se známými podezřelými IP adresami.

    Pro lepší přehled o zabezpečení vašich kontejnerových prostředí agent sleduje analýzu specifickou pro kontejnery. Aktivuje výstrahy pro události, jako je vytvoření privilegovaného kontejneru, podezřelý přístup k serverům rozhraní API a servery Secure Shell (SSH) běžící uvnitř kontejneru Docker.

    >[!NOTE]
    > Pokud se rozhodnete neinstalovat agenty na hostitele, bude se vám zobrazovat jenom podmnožina výhod a upozornění detekce hrozeb. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.

    Seznam výstrah na úrovni hostitele najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-containerhost).


* Pro **úroveň clusteru AKS**je sledování detekce hrozeb založené na analýze protokolů auditu Kubernetes. Pokud chcete toto monitorování bez **agentů** povolit, přidejte na stránce s **nastavením cenové &** možnost Kubernetes (podívejte se na [ceny](security-center-pricing.md)). Pokud chcete na této úrovni generovat výstrahy, Security Center monitorovat služby spravované AKS pomocí protokolů načtených pomocí AKS. Mezi příklady událostí na této úrovni patří exponované řídicí panely Kubernetes, vytváření vysoce privilegovaných rolí a vytváření citlivých připojení.

    >[!NOTE]
    > Security Center generuje upozornění na detekci pro akce a nasazení služby Azure Kubernetes, ke kterým dochází po povolení možnosti Kubernetes v nastaveních předplatného. 

    Seznam výstrah na úrovni clusteru AKS najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-akscluster).

Náš globální tým výzkumných pracovníků pro zabezpečení nepřetržitě monitoruje i na šířku hrozby. Přidávají výstrahy a chyby specifické pro kontejner při jejich zjištění.