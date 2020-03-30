---
title: Analýza hrozeb & mapa výstrah zabezpečení – Azure Security Center
description: Zjistěte, jak pomocí mapy výstrah zabezpečení a funkce analýzy hrozeb v Centru zabezpečení Azure identifikovat potenciální hrozby ve virtuálních počítačích a počítačích.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603429"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Analýza hrozeb a mapa výstrah zabezpečení
Tento článek vám pomůže použít mapu výstrah zabezpečení Centra zabezpečení Azure a mapu analýzy hrozeb založené na událostech zabezpečení k řešení problémů souvisejících se zabezpečením.

> [!NOTE]
> 31. července 2019 bylo vyřazeno tlačítko Mapy *událostí* zabezpečení. Další informace a alternativní služby naleznete v [tématu Retirement of Security Center features (Červenec 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Jak mapa výstrah zabezpečení funguje
Security Center poskytuje mapu, která vám pomůže identifikovat bezpečnostní hrozby pro životní prostředí. Můžete například určit, zda je konkrétní počítač součástí botnetu a odkud pochází hrozba. Počítače se mohou stát uzly v botnetu, když útočníci nezákonně instalují malware, který tajně spolupracuje s příkazem a řízením, které spravují botnet. 

K vytvoření této mapy používá Centrum zabezpečení data, která pocházejí z více zdrojů v rámci společnosti Microsoft. Security Center používá tato data k mapování potenciálních hrozeb pro vaše prostředí. 

Jedním z kroků [procesu reakce na incidenty zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) je identifikace závažnosti napadených systémů. V této fázi byste měli provést následující úlohy:

- Určit povahu útoku.
- Určete místo původu útoku.
- Určit záměr útoku. Směřoval útok na vaši organizaci za účelem získání konkrétních informací, nebo se jednalo o náhodný útok?
- Identifikovat napadené systémy.
- Identifikujte soubory, ke kterým byl získán přístup, a závažnost takových souborů.

Pomocí mapy výstrah zabezpečení v Centru zabezpečení můžete tyto úkoly pomoci.

## <a name="access-the-security-alerts-map"></a>Přístup k mapě výstrah zabezpečení
Chcete-li vizualizovat aktuální hrozby ve vašem prostředí, otevřete mapu výstrah zabezpečení:

1. Otevřete řídicí panel **Security Center**.
2. V levém podokně vyberte v části **Ochrana před hrozbami** **mapa výstrah zabezpečení**. Mapa se otevře.
3. Chcete-li získat další informace o upozornění a přijímat nápravné kroky, klikněte na výstražnou tečku na mapě a postupujte podle pokynů. 
 
Mapa výstrah zabezpečení je založena na výstrahách. Tyto výstrahy jsou založeny na aktivitách, pro které byla síťová komunikace přidružena k ip adrese, která byla úspěšně vyřešena, zda je ip adresa známá riziková ADRESA IP (například známý cryptominer) nebo IP adresa, která není rozpoznána dříve jako riskantní. Mapa poskytuje výstrahy napříč všemi předplatnými, která jste dříve vybrali v Azure. 

Výstrahy na mapě jsou zobrazeny podle zeměpisné polohy, odkud jsou zjištěny jako pocházející, a jsou barevně odlišeny podle závažnosti. 
    ![Informace Analýzy hrozeb](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak používat analýzu hrozeb ve službě Security Center jako pomoc při identifikování podezřelých aktivit. Další informace o službě Security Center najdete v následujících článcích:

* [Správa výstrah zabezpečení a reakce na ně v Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Vysvětlení výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží centra zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.