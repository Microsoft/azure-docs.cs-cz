---
title: Analýza hrozeb v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat možnosti analýzy hrozeb v Azure Security Center k identifikaci potenciálních hrozeb na virtuálních počítačích a počítačích.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 34474892d35c2c0bc299bbc4a2fb6507ffb24ee9
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022216"
---
# <a name="security-alerts-map-azure-security-center"></a>Výstrahy zabezpečení mapování Azure Security Center
Tento článek vám umožní použít mapu výstrahy zabezpečení Azure Security Center a mapa analýzy hrozeb založený na událostech zabezpečení pro řešení potíží s související se zabezpečením.

## <a name="how-the-security-alerts-map-works"></a>Jak se mapují výstrahy zabezpečení funguje
Security Center zajišťuje, že jste s mapu, která vám pomůže identifikovat bezpečnostní hrozby pro prostředí. Můžete například určit, jestli je konkrétní počítač součástí botnetu, a tam, kde je hrozby přicházející z. Počítače se můžou stát uzly v botnetu, když útočníci neoprávněně nainstalují malware, který tajně komunikuje s příkazy a ovládání, které spravují botnetu. 

Pokud chcete vytvořit toto mapování, Security Center používá data přicházející z několika zdrojů v rámci Microsoft. Security Center tato data používá k mapování potenciálních hrozeb pro vaše prostředí. 

Jedním z kroků [procesu reakce na incidenty zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) je identifikace závažnosti napadených systémů. V této fázi byste měli provést následující úlohy:

- Určit povahu útoku.
- Určete místo původu útoku.
- Určit záměr útoku. Směřoval útok na vaši organizaci za účelem získání konkrétních informací, nebo se jednalo o náhodný útok?
- Identifikovat napadené systémy.
- Identifikujte soubory, ke kterým byl získán přístup, a závažnost takových souborů.

Mapování výstrah zabezpečení ve službě Security Center můžete pomoct s těmito úkoly.

## <a name="access-the-security-alerts-map"></a>Přístup k mapě výstrahy zabezpečení
Pokud chcete vizualizovat aktuální hrozby na vašem prostředí, spusťte nástroj Mapa upozornění zabezpečení:

1. Otevřete řídicí panel **Security Center**.
2. V levém podokně v části **ochrana před internetovými útoky** vyberte **mapy výstrahy zabezpečení**. Otevře se na mapě.
3. Chcete-li získat další informace o výstraze a přijímat nápravných kroků, klikněte na upozornění tečku na mapě a postupujte podle pokynů. 
 
Mapa upozornění zabezpečení je založená na výstrahy. Tyto výstrahy jsou založené na aktivitách komunikace pro síť, ve které byla přidružena IP adresu, která byla úspěšně, jestli je IP adresa známá rizikové IP adresy (například známé cryptominer) nebo IP adresu, která není rozpoznána dříve jako rizikovou. Na mapě poskytuje výstrahy v rámci žádné předplatné, které jste vybrali dříve v Azure. 

Upozornění na mapě se zobrazují podle zeměpisného umístění, kde jsou zjištěny jako pocházející z, a jsou barevně odlišeny podle závažnosti. 
 
## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Zobrazení založeného na událostech řídicímu panelu analýzy hrozeb
Chcete-li zobrazit mapa analýzy hrozeb na základě zabezpečení nezpracovaných událostí, můžete podle následujícího postupu. Toto mapování se zobrazí pouze události, které se týkají IP adresu, která se považuje za rizikové, například IP adresu známé botnety.

1. Otevřete řídicí panel **Security Center**.

1. V levém podokně v části **ochrana před internetovými útoky** vyberte **mapy výstrahy zabezpečení**. Otevře se na mapě.
2. V pravém horním rohu, klikněte na tlačítko **přejít do mapy událostí zabezpečení**.
3. Vyberte pracovní prostor, pro které chcete zobrazit řídicí panel.
4. V horní části stránky na mapě, vyberte **zobrazit classic hrozeb**. **Hrozeb** otevře se řídicí panel.

   > [!NOTE]
   > Pokud se ve sloupci úplně vpravo zobrazí **UPGRADUJTE PLÁN**, tento pracovní prostor používá bezplatné předplatné. Upgradujte na úroveň Standard, abyste mohli tuto funkci používat. Pokud se ve sloupci úplně vpravo zobrazí **VYŽADUJE AKTUALIZACI**, aktualizujte [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), abyste mohli tuto funkci používat. Další informace o cenovém plánu najdete v článku o cenách Azure Security Center.
   >
5. Pokud máte k prozkoumání více než jeden pracovní prostor, určete prioritu prozkoumání podle sloupce **Škodlivá IP adresa**. Zobrazuje se v něm aktuální počet škodlivých IP adres v tomto pracovním prostoru. Vyberte pracovní prostor, který chcete použít, a pak se zobrazí řídicí panel **Analýza hrozeb**.

    ![Informace Analýzy hrozeb](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Řídicí panel je rozdělen na čtyři dlaždice:

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


## <a name="see-also"></a>Další informace najdete v tématech
V tomto článku jste zjistili, jak používat analýzu hrozeb ve službě Security Center jako pomoc při identifikování podezřelých aktivit. Další informace o službě Security Center najdete v následujících článcích:

* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Vysvětlení výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Najděte odpovědi na nejčastější dotazy ohledně používání této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
