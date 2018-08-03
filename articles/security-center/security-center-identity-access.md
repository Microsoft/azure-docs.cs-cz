---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: terrylan
ms.openlocfilehash: b2243b10c20a0c8ed0faccbcc82e24193bd4adac
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460265"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorování identity a přístupu v Azure Security Center (Preview)
Tento článek vám pomůže používat Azure Security Center k monitorování identit a aktivit přístupu uživatelů.

> [!NOTE]
> Monitorování identity a přístupu je ve verzi preview a je k dispozici jenom na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>
>

Identita by měla být základní „řídicí plochou“ vaší organizace a její ochrana je tou nejvyšší prioritou. Bezpečnostní hraniční sítě se vyvinula z hraniční sítě do hraniční identity. Zabezpečení bude méně o ochraně sítě a další informace o ochrana vašich dat, jakož i Správa zabezpečení aplikací a uživatelů. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Monitorování aktivit souvisejících s identitou umožňuje proaktivně jednat, ještě než k incidentu dojde, nebo provést reaktivní akci k zastavení pokusu o útok. Řídicí panel identita a přístup poskytuje doporučení, jako:

- Povolte MFA pro privilegované účty v rámci předplatného
- Z předplatného odebrat externí účty s oprávněním pro zápis
- Z předplatného odebrat privilegované externí účty.

> [!NOTE]
> Pokud vaše předplatné má víc než 250 účtů, Security Center se nepodařilo spustit doporučení Identity ve vašem předplatném. Doporučení, která se nespustí, jsou uvedeny v části "nedostupná posouzení", které jsou popsány níže.
Security Center se nepodařilo spustit doporučení Identity správce agentů partnerský Cloud Solution Provider (CSP).
>
>

Zobrazit [doporučení](security-center-identity-access.md#recommendations) seznam identit a přístupu doporučení poskytovaných službou Security Center.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Můžete monitorovat stav zabezpečení vašich prostředků na **Security Center – přehled** řídicího panelu. **Prostředky** oddíl je indikátor stavu zobrazující závažnosti pro každý typ prostředku.

Seznam všech problémů můžete zobrazit tak, že vyberete **doporučení**. V části **prostředky**, můžete zobrazit seznam problémů, které jsou specifické pro výpočetní prostředky a aplikace, data zabezpečení, sítě, nebo identita a přístup. Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Úplný seznam doporučení pro identitu a přístup, najdete v části [doporučení](security-center-identity-access.md#recommendations).

Chcete-li pokračovat, vyberte **identita a přístup** pod **prostředky** nebo hlavní nabídce služby Security Center.

![Řídicí panel Security Center][1]

## <a name="monitor-identity-and-access"></a>Monitorování identity a přístupu
V části **identita a přístup**, existují dvě karty:

- **Přehled**: doporučení identifikované pomocí služby Security Center.
- **Předplatná**: seznam předplatných a aktuální stav zabezpečení jednotlivých.

![Identita a přístup][2]

### <a name="overview-section"></a>Přehled oddílu
V části **přehled**, není k dispozici seznam doporučení. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet předplatných, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému.

1. Vyberte doporučení. Otevře se okno a zobrazí doporučení:

  - Popis doporučení
  - Seznam předplatných není v pořádku a v dobrém stavu
  - Seznam prostředků, které jsou z důvodu selhání posouzení nezkontrolované nebo prostředek spadá pod předplatné běžící na úrovni Free a není použit k vyhodnocení

  ![Okno doporučení.][3]

1. Vyberte předplatné, v seznamu pro další podrobnosti.

### <a name="subscriptions-section"></a>Části předplatných
V části **předplatná**, je seznam předplatných. První sloupec uvádí předplatná. Druhý sloupec zobrazuje celkový počet doporučení pro každé předplatné. Třetí sloupec zobrazuje stupně závažnosti problémů.

![Karta předplatného.][4]

1.  Vyberte předplatné. Souhrnné zobrazení otevřete tři karty:

  - **Doporučení**: založené na posouzení, které provádí služba Security Center, která selhala.
  - **Předaný posouzení**: seznam posouzení, které provádí služba Security Center, který předává.
  - **Nedostupná posouzení**: seznam vyhodnocení, které se nepovedlo spustit kvůli chybě nebo proto, že předplatné má víc než 250 účtů.

  V části **doporučení** se seznam doporučení pro vybrané předplatné a závažnost jednotlivých doporučení.

  ![Doporučení pro výběr předplatného][5]

1. Vyberte doporučení pro popis doporučení, seznam předplatných není v pořádku a v pořádku a seznam nezkontrolované prostředky.

  ![Popis doporučení][6]

  V části **předaný posouzení** je seznam vyhovující posouzení.  Závažnost těchto hodnocení je vždy zelená.

  ![Vyhovující posouzení][7]

1. Předaný posouzení vyberte ze seznamu popis posouzení a seznam předplatných v pořádku. Je na kartě pro předplatné není v pořádku, který obsahuje seznam všech předplatných, které se nezdařilo.

  ![Vyhovující posouzení][8]

## <a name="recommendations"></a>Doporučení
V následující tabulce použijte jako odkaz vám pomůžou pochopit dostupné doporučení identita a přístup a každý z nich, co dělá když je použijete.

| Doporučení | Popis |
| --- | --- |
| Určení více než jednoho vlastníka předplatného | Doporučuje se určit více než jednoho vlastníka předplatného abyste měli redundance přístup správce. |
| Určit vlastníky až 3 v rámci předplatného | Doporučuje se určit méně než 3 na vlastníky těchto předplatných za účelem snížení riziko porušení zabezpečení ohroženým uživatelem. |
| Povolte MFA pro účty s oprávněními vlastníka v rámci předplatného | Doporučuje povolit Vícefaktorové ověřování (MFA) pro všechny účty předplatných s oprávněními správce nedošlo k porušení zabezpečení účtů nebo prostředků. |
| Povolte MFA pro účty s oprávněním pro zápis v předplatném | Doporučuje povolit Vícefaktorové ověřování (MFA) pro všechny účty předplatných s oprávněními pro zápis do nedošlo k porušení zabezpečení účtů nebo prostředků. |
| Povolte MFA pro účty s oprávněním pro čtení v rámci předplatného | Doporučuje povolit Vícefaktorové ověřování (MFA) pro všechny účty předplatných s oprávněními pro čtení k nedošlo k porušení zabezpečení účtů nebo prostředků. |
| Z předplatného odebrat externí účty s oprávněním pro čtení | Doporučuje, aby nedocházelo k nemonitorovanému přístupu odebrat externí účty s oprávněními pro čtení z předplatného. |
| Z předplatného odebrat externí účty s oprávněním pro zápis | Doporučuje, aby nedocházelo k nemonitorovanému přístupu odebrat externí účty s oprávněními pro zápis z předplatného. |
| Z předplatného odebrat externí účty s oprávněními vlastníka | Doporučuje, aby nedocházelo k nemonitorovanému přístupu odebrat externí účty s oprávněními vlastníka ze svého předplatného. |
| Odeberte zastaralé účty z předplatného | Doporučuje odebrat zastaralé účty z vašich předplatných. |
| Z předplatného odebrat zastaralé účty s oprávněními vlastníka | Doporučuje odebrat zastaralé účty s oprávněními vlastníka z vašich předplatných. |

## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

- [Ochrana vašich počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
- [Ochrana služby Azure SQL a data ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Vysvětlení výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Najděte odpovědi na nejčastější dotazy ohledně používání služby Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
