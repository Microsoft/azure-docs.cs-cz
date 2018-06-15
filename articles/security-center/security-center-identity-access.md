---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 5ee263ef8fb0f20049215eda53e0d58a45342b7e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774824"
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Monitorování identity a přístupu v Azure Security Center
Tento článek vám pomůže používat Azure Security Center k monitorování identit a aktivit přístupu uživatelů.

## <a name="why-monitor-identity-and-access"></a>Proč monitorovat identitu a přístup?
Identita by měla být základní „řídicí plochou“ vaší organizace a její ochrana je tou nejvyšší prioritou. V minulosti byly kolem organizací vytyčeny perimetry, které byly hlavní obrannou linií. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Monitorování aktivit souvisejících s identitou umožňuje proaktivně jednat, ještě než k incidentu dojde, nebo provést reaktivní akci k zastavení pokusu o útok. Řídicí panel Identita a přístup poskytuje přehled stavu vaší identity a uvádí mimo jiné:

* Počet neúspěšných pokusů o přihlášení. 
* Uživatelské účty použité při těchto pokusech.
* Uzamčené účty.
* Účty se změněným nebo resetovaným heslem. 
* Aktuální počet účtů, jejichž uživatelé jsou přihlášeni.

## <a name="monitor-identity-and-access-activities"></a>Monitorování identity a aktivit přístupu
Pokud chcete zobrazit aktuální aktivity související s identitou a přístupem, potřebujete přístup k řídicímu panelu **Identita a přístup**.

1. Otevřete řídicí panel **Security Center**.

2. V levém podokně v části **Prevence** vyberte **Identita a přístup**. Pokud máte více pracovních prostorů, zobrazí se selektor pracovního prostoru.

    ![Výběr pracovního prostoru](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Pokud se ve sloupci úplně vpravo zobrazí **UPGRADUJTE PLÁN**, tento pracovní prostor používá bezplatné předplatné. Upgradujte na předplatné úrovně Standard, abyste mohli tuto funkci používat. Pokud se ve sloupci úplně vpravo zobrazí **VYŽADUJE AKTUALIZACI**, aktualizujte [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), abyste mohli tuto funkci používat. Další informace o cenovém plánu najdete v článku o cenách služby Security Center. 
    > 
3. Pokud máte k prozkoumání více než jeden pracovní prostor, můžete určit prioritu prozkoumání podle sloupce **NEÚSPĚŠNÁ PŘIHLÁŠENÍ**. Zobrazuje se v něm aktuální počet neúspěšných pokusů o přihlášení v tomto pracovním prostoru. Vyberte pracovní prostor, který chcete použít, a zobrazí se řídicí panel **Identita a přístup**.

    ![Identita a přístup](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Informace, které jsou k dispozici na tomto řídicím panelu, vám okamžitě můžou pomoct identifikovat potenciálně podezřelé aktivity. Řídicí panel je rozdělen na tři hlavní oblasti:

    a. **Stav identity**. Obsahuje souhrn aktivit souvisejících s identitou, které probíhají v tomto pracovním prostoru.

    b. **Neúspěšná přihlášení**. Pomáhá rychle identifikovat hlavní příčinu neúspěšného pokusu o přihlášení. Zobrazuje seznam 10 účtů s největším počtem neúspěšných pokusů o přihlášení.

    c. **Přihlášení v průběhu času**. Pomáhá rychle identifikovat počet přihlášení v průběhu času. Zobrazuje seznam účtů počítače s největším počtem pokusů o přihlášení.
    
Řídicí panel, který se zobrazí po výběru jakékoli dlaždice, vychází z dotazu prohledávání protokolu. Jediným rozdílem je typ dotazu a výsledek. Stále můžete vybrat položku, například počítač, a zobrazit související data. 

## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak monitorovat identitu a přístup ve službě Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Vysvětlení výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center. 
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Najděte odpovědi na nejčastější dotazy ohledně používání služby Security Center.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

