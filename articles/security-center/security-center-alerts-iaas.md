---
title: Detekce hrozeb pro virtuální počítače a servery v Azure Security Center | Microsoft Docs
description: V tomto tématu se zobrazí výstrahy virtuálního počítače a serveru, které jsou dostupné v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: b101fd30ddbdbf0f8fdf6e02394cb10b9af5f4b0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666359"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Detekce hrozeb pro virtuální počítače a servery v Azure Security Center

V tomto tématu jsou uvedeny různé typy metod detekce a upozornění, která jsou k dispozici pro virtuální počítače a servery s následujícími operačními systémy. Seznam podporovaných verzí najdete v tématu [platformy a funkce podporované nástrojem Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Azure Security Center se integruje se službami Azure za účelem monitorování a ochrany počítačů se systémem Windows. Security Center prezentuje výstrahy a návrhy oprav ze všech těchto služeb ve snadno použitelném formátu.

* **ATP** <a name="windows-atp"></a> v programu Microsoft Defender – Security Center rozšiřuje své cloudové platformy na ochranu díky integraci s rozšířenou ochranou před internetovými útoky v programu Microsoft Defender (ATP). Poskytuje komplexní možnosti detekce a odezvy koncových bodů (EDR).

    > [!NOTE]
    > Senzor ATP v programu Microsoft Defender je automaticky povolen na serverech se systémem Windows, které používají Security Center.

    Když ATP v programu Microsoft Defender zjistí hrozbu, aktivuje výstrahu. Výstraha se zobrazí na řídicím panelu Security Center. Z řídicího panelu se můžete překlopit do konzoly ATP v programu Microsoft Defender a provést podrobné šetření, které odhalí rozsah útoku. Další informace o ochraně ATP v programu Microsoft Defender najdete v tématu připojení [serverů ke službě Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* <a name="windows-dump"></a> **Analýza výpisu stavu systému** – Pokud dojde k chybě softwaru, výpis stavu systému zaznamená část paměti v době selhání.

    Selhání mohlo být způsobeno malwarem nebo obsahem malwaru. Aby nedocházelo k detekci v produktech zabezpečení, různé formy malwaru používají útok bez souborů, což zabraňuje psaní na disk nebo šifrování softwarových součástí zapsaných na disk. Tento typ útoku se obtížně detekuje pomocí tradičních přístupů založených na disku.

    Pomocí analýzy paměti ale můžete zjistit tento druh útoku. Díky analýze paměti ve výpisu stavu systému může Security Center detekovat techniky, které útok používá. Útok se například může pokusit zneužít ohrožení zabezpečení softwaru, přistupovat k důvěrným datům a nenápadně uchovat v napadeném počítači. Security Center to funguje s minimálním dopadem na výkon hostitelů.

    Seznam výstrah analýzy výpisu stavu systému najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-crashdump).

* **Detekce** <a name="windows-fileless"></a> útoků typu soubor – společné útoky pomocí souborů, které cílí na vaše koncové body, jsou běžné. Aby nedocházelo k detekci, útoky bez souborů vkládají do paměti škodlivá datová data. V paměti napadených procesů a provádění široké škály škodlivých aktivit může dojít k trvalému uložení datových částí.

    Díky detekci útoků bez souborů můžou automatizované techniky forenzní paměti identifikovat sady nástrojů, techniky a chování pro útoky bez souborů. Toto řešení pravidelně kontroluje počítač za běhu a extrahuje přehledy přímo z paměti důležitých procesů, které jsou kritické pro zabezpečení.

    Najde důkazy o zneužití, vkládání kódu a spuštění škodlivých datových částí. Detekce útoků bez souborů generuje podrobné výstrahy zabezpečení, které urychlují jejich třídění, korelace a dobu odezvy za provozu. Tento přístup doplňuje řešení EDR založené na událostech a zajišťuje větší pokrytí detekce.

    Seznam výstrah detekce útoků bez souborů najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-filelessattackdetect).

> [!NOTE]
> Výstrahy Windows můžete simulovat stažením [Azure Security Center PlayBook: výstrahy zabezpečení](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

## Linux <a name="linux-machines"></a>

Security Center shromažďuje záznamy auditu z počítačů se systémem Linux pomocí **auditu**, jeden z nejběžnějších rozhraní pro auditování Linux. auditované života v jádru hlavní. 

* <a name="linux-auditd"></a> Integrovaná **auditovaná upozornění a Microsoft Monitoring Agent (MMA)** – auditované systémy se skládají z podsystému na úrovni jádra, který zodpovídá za monitorování volání systému. Filtruje je podle zadané sady pravidel a zapisuje zprávy pro ně do soketu. Security Center integruje funkce z auditovaného balíčku v rámci Microsoft Monitoring Agent (MMA). Tato integrace umožňuje shromažďování auditovaných událostí ve všech podporovaných distribucích systému Linux bez jakýchkoli požadavků.  

    auditované záznamy jsou shromažďovány, obohaceny a agregovány do událostí pomocí agenta MMA pro Linux. Security Center průběžně přidává nové analýzy, které používají signály Linux k detekci škodlivého chování na cloudových a místních počítačích se systémem Linux. Podobně jako funkce Windows tyto analýzy přesahují podezřelé procesy, pokusy o přihlášení k podezřelých, načítání modulů jádra a další činnosti. Tyto aktivity mohou znamenat, že počítač je buď napadený, nebo byl napadený.  

    Níže jsou uvedeny některé příklady analýz, které jsou rozloženy v různých fázích životního cyklu útoku.

    Seznam výstrah pro Linux najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-linux).

> [!NOTE]
> Výstrahy pro Linux můžete simulovat stažením [Azure Security Center PlayBook: detekce Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Další kroky

Příklady a další informace o detekci Security Center najdete v tématech:

* [Jak Azure Security Center automatizuje detekci internetoví útoků](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Jak Azure Security Center detekuje chyby zabezpečení pomocí nástrojů pro správu](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Využití Azure Security Center k detekci napadení napadených počítačů se systémem Linux](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center dokáže detekovat nově vznikající chyby zabezpečení v systému Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)