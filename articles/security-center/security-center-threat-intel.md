---
title: Analýza hrozeb v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat možnosti analýzy hrozeb v Azure Security Center k identifikaci potenciálních hrozeb na virtuálních počítačích a počítačích.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 9654c25bf6b51d48dc195d6a536b61312fdcbeff
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="threat-intelligence-in-azure-security-center"></a>Analýza hrozeb v Azure Security Center
Tento článek vám pomůže s používáním analýzy hrozeb v Azure Security Center k vyřešení problémů souvisejících se zabezpečením.

## <a name="what-is-threat-intelligence"></a>Co je analýza hrozeb?
Pomocí možnosti analýzy hrozeb, která je dostupná ve službě Security Center, můžou správci IT identifikovat bezpečnostní hrozby pro prostředí. Například můžou určit, jestli je konkrétní počítač součástí botnetu. Z počítačů se můžou stát uzly v botnetu, když útočníci neoprávněně nainstalují malware, který tajně připojí počítač k řídicímu serveru. Analýza hrozeb dokáže identifikovat také potenciální hrozby přicházející z alternativních komunikačních kanálů, jako je například dark web.

Služba Security Center k sestavení této analýzy hrozeb používá data přicházející z několika zdrojů v rámci Microsoftu. Služba Security Center tato data používá k identifikaci potenciálních hrozeb pro vaše prostředí. Podokno **Analýza hrozeb** se skládá ze tří hlavních možností:

- Zjištěné typy hrozeb
- Původ hrozeb
- Mapa analýzy hrozeb


## <a name="when-should-you-use-threat-intelligence"></a>Kdy byste měli použít analýzu hrozeb?
Jedním z kroků [procesu reakce na incidenty zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) je identifikace závažnosti napadených systémů. V této fázi byste měli provést následující úlohy:

- Určit povahu útoku.
- Určit, odkud útok přišel.
- Určit záměr útoku. Směřoval útok na vaši organizaci za účelem získání konkrétních informací, nebo se jednalo o náhodný útok?
- Identifikovat napadené systémy.
- Identifikujte soubory, ke kterým byl získán přístup, a závažnost takových souborů.

S těmito úlohami vám můžou pomoct informace analýzy hrozeb ve službě Security Center. 

## <a name="access-the-threat-intelligence"></a>Přístup k analýze hrozeb
Pokud chcete vizualizovat aktuální analýzu hrozeb pro vaše prostředí, musíte nejprve vybrat pracovní prostor, kde se nacházejí vaše informace. Pokud nemáte více pracovních prostorů, vynecháte selektor pracovního prostoru a přejdete přímo na řídicí panel **Analýza hrozeb**. Přístup k řídicímu panelu:

1. Otevřete řídicí panel **Security Center**.

2. V levém podokně v části **Detekce** vyberte **Analýza hrozeb**. Zobrazí se řídicí panel **Analýza hrozeb**.

    ![Řídicí panel Analýza hrozeb](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Pokud se ve sloupci úplně vpravo zobrazí **UPGRADUJTE PLÁN**, tento pracovní prostor používá bezplatné předplatné. Upgradujte na úroveň Standard, abyste mohli tuto funkci používat. Pokud se ve sloupci úplně vpravo zobrazí **VYŽADUJE AKTUALIZACI**, aktualizujte [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), abyste mohli tuto funkci používat. Další informace o cenovém plánu najdete v článku o cenách Azure Security Center. 
    > 
3. Pokud máte k prozkoumání více než jeden pracovní prostor, určete prioritu prozkoumání podle sloupce **Škodlivá IP adresa**. Zobrazuje se v něm aktuální počet škodlivých IP adres v tomto pracovním prostoru. Vyberte pracovní prostor, který chcete použít, a pak se zobrazí řídicí panel **Analýza hrozeb**.

    ![Informace Analýzy hrozeb](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Řídicí panel je rozdělen na čtyři dlaždice:

    a.  **Typy hrozeb**. Obsahuje souhrn typů hrozeb detekovaných ve vybraném pracovním prostoru.

    b.  **Země původu**. Agreguje objem přenosů podle zdrojového místa.

    c.  **Poloha hrozeb**. Pomáhá identifikovat aktuální místa po celém světě, ze kterých probíhá komunikace s vaším prostředím. Oranžové (příchozí) a červené (odchozí) šipky zobrazené na mapě určují směr přenosu. Pokud některou z těchto šipek vyberete, zobrazí se typ hrozby a směr přenosu.

    d.  **Podrobnosti o hrozbě**. Zobrazuje více podrobností o hrozbě, kterou jste vybrali na mapě.

Řídicí panel, který se zobrazí po výběru jakékoli dlaždice možností, vychází z dotazu prohledávání protokolu. Jediným rozdílem je typ dotazu a výsledek.

### <a name="threat-types"></a>Typy hrozeb
Výběrem dlaždice **Typy hrozeb** otevřete řídicí panel **Prohledávání protokolů**. Vlevo se zobrazí možnosti filtru a vpravo výsledky dotazu.

![Prohledávání protokolů](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Výsledek dotazu ukazuje hrozby podle názvu. V levém podokně můžete vybrat atribut, podle kterého chcete filtrovat. Pokud například chcete zobrazit pouze hrozby, které jsou aktuálně připojené k počítačům, v části **STAV RELACE** vyberte **Připojeno** > **Použít**.

![Stav relace](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Pro virtuální počítače Azure se na řídicím panelu **Analýza hrozeb** zobrazují pouze data sítě, která prochází přes agenta. Analýza hrozeb používá také následující typy dat:

- Data CEF (Type=CommonSecurityLog)
- WireData (Type=WireData)
- Protokoly služby IIS (Type=W3CIISLog)
- Brána Windows Firewall (Type=WindowsFirewall)
- Události DNS (Type=DnsEvents)


## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak používat analýzu hrozeb ve službě Security Center jako pomoc při identifikování podezřelých aktivit. Další informace o službě Security Center najdete v následujících článcích:

* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Vysvětlení výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center. 
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Najděte odpovědi na nejčastější dotazy ohledně používání této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

