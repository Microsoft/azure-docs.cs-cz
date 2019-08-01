---
title: Mapa hrozeb a výstraha zabezpečení v Azure Security Center | Microsoft Docs
description: Naučte se používat mapu výstrah zabezpečení a schopnost analýzy hrozeb v Azure Security Center k identifikaci potenciálních hrozeb ve vašich virtuálních počítačích a počítačích.
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
ms.openlocfilehash: 0740beb3b1ffc200c36ca4b5c15b25017821587e
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662339"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Analýza hrozeb a mapa výstrah zabezpečení
Tento článek vám pomůže při řešení problémů souvisejících se zabezpečením pomocí mapy výstrahy zabezpečení Azure Security Center a zabezpečení analýzy hrozeb na základě událostí zabezpečení.

> [!NOTE]
> Tlačítko Mapa *událostí* zabezpečení bylo vyřazeno z 31. července 2019. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Jak funguje mapa výstrah zabezpečení
Security Center vám poskytne mapu, která vám pomůže identifikovat bezpečnostní hrozby pro prostředí. Můžete například zjistit, jestli je konkrétní počítač součástí botnetu a kde hrozba přichází. Počítače se můžou stát uzly v botnetuu, když útočníci neoprávněně nainstalují malware, který tajně komunikuje s příkazy a ovládacím prvkem, který spravuje botnetu. 

Chcete-li vytvořit tuto mapu, Security Center používá data pocházející z více zdrojů v rámci společnosti Microsoft. Security Center tato data používá k namapování potenciálních hrozeb na vaše prostředí. 

Jedním z kroků [procesu reakce na incidenty zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) je identifikace závažnosti napadených systémů. V této fázi byste měli provést následující úlohy:

- Určit povahu útoku.
- Určete bod původu útoku.
- Určit záměr útoku. Směřoval útok na vaši organizaci za účelem získání konkrétních informací, nebo se jednalo o náhodný útok?
- Identifikovat napadené systémy.
- Identifikujte soubory, ke kterým byl získán přístup, a závažnost takových souborů.

K usnadnění těchto úloh můžete použít mapu výstrahy zabezpečení v Security Center.

## <a name="access-the-security-alerts-map"></a>Přístup k mapě výstrah zabezpečení
Pokud chcete vizualizovat aktuální hrozby ve vašem prostředí, otevřete mapu výstrah zabezpečení:

1. Otevřete řídicí panel **Security Center**.
2. V levém podokně v části **Ochrana před hrozbami** vyberte **Mapa výstrah zabezpečení**. Otevře se mapa.
3. Chcete-li získat další informace o výstrahách a přijetí kroků k nápravě, klikněte na položku tečka na mapě na mapě a postupujte podle pokynů. 
 
Mapa výstrah zabezpečení je založena na výstrahách. Tyto výstrahy jsou založené na aktivitách, pro které byla síťová komunikace přidružena k IP adrese, která byla úspěšně vyřešena, bez ohledu na to, zda je IP adresa známou (například známá cryptominer) nebo IP adresa, která nebyla rozpoznána. dříve jako rizikové. Mapa poskytuje výstrahy u všech předplatných, která jste dříve vybrali v Azure. 

Výstrahy na mapě se zobrazují v závislosti na geografickém umístění, kde se zjišťují, jak pocházejí od, a jsou barevně kódované podle závažnosti. 
    ![Informace analýzy hrozeb](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Viz také:
V tomto článku jste zjistili, jak používat analýzu hrozeb ve službě Security Center jako pomoc při identifikování podezřelých aktivit. Další informace o službě Security Center najdete v následujících článcích:

* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Vysvětlení výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Najděte odpovědi na nejčastější dotazy ohledně používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
