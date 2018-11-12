---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 01c8f864d25a35d42abcd624e31728f4fee0d80c
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012067"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorování identity a přístupu v Azure Security Center (Preview)
Tento článek vám pomůže používat Azure Security Center k monitorování identit a aktivit přístupu uživatelů.

> [!NOTE]
> Monitorování identity a přístupu je ve verzi preview a je k dispozici jenom na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>

Identita by měla být základní „řídicí plochou“ vaší organizace a její ochrana je tou nejvyšší prioritou. Bezpečnostní hraniční sítě se vyvinula z hraniční sítě do hraniční identity. Zabezpečení bude méně o ochraně sítě a další informace o ochrana vašich dat, jakož i Správa zabezpečení aplikací a uživatelů. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Monitorování aktivit souvisejících s identitou umožňuje proaktivně jednat, ještě než k incidentu dojde, nebo provést reaktivní akci k zastavení pokusu o útok. Řídicí panel identita a přístup poskytuje doporučení, jako:

- Povolte MFA pro privilegované účty v předplatném
- Odeberte z předplatného externí účty s oprávněním pro zápis
- Odeberte z předplatného privilegované externí účty

> [!NOTE]
> Pokud vaše předplatné má více než 600 účty, Security Center se nepodařilo spustit doporučení Identity ve vašem předplatném. Doporučení, která se nespustí, jsou uvedeny v části "nedostupná posouzení", které jsou popsány níže.
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
  - **Nedostupná posouzení**: seznam vyhodnocení, které se nepovedlo spustit kvůli chybě nebo proto, že předplatné má více než 600 účty.

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

|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|Předplatné|50|Povolte MFA pro aplikace pro správu Azure účty s oprávněními vlastníka v rámci předplatného|Povolení služby Multi-Factor Authentication (MFA) pro všechny účty předplatných s oprávněními správce nedošlo k porušení zabezpečení účtů nebo prostředků.|
|Předplatné|50|Povolit službu security center ve vašich předplatných |Povolit službu Security center na všechna předplatná pro rozšířenou detekci hrozeb, JIT, seznamu povolených aplikací a pokročilé doporučení |
|Předplatné|50|Povolit security center úrovně standard na předplatná |Povolte službu Security center úrovně Standard na všechna předplatná pro rozšířenou detekci hrozeb, JIT, seznamu povolených aplikací a pokročilé doporučení.|
|Předplatné|40|Povolte MFA pro aplikace pro správu Azure účty s oprávněním pro zápis v předplatném|Povolení služby Multi-Factor Authentication (MFA) pro všechny účty předplatných s oprávněními pro zápis do nedošlo k porušení zabezpečení účtů nebo prostředků.|
|Předplatné|30|Z předplatného odebrat externí účty s oprávněními vlastníka|Z předplatného odeberte externí účty s oprávněními vlastníka, aby nedocházelo k nemonitorovanému přístupu. |
|Předplatné|30|Povolte MFA pro aplikace pro správu Azure účty s oprávněním pro čtení v rámci předplatného|Povolení služby Multi-Factor Authentication (MFA) pro všechny účty předplatných s oprávněními pro čtení k nedošlo k porušení zabezpečení účtů nebo prostředků.|
|Předplatné|25|Odeberte z předplatného externí účty s oprávněním pro zápis|Z předplatného odeberte externí účty s oprávněním pro zápis, aby nedocházelo k nemonitorovanému přístupu. |
|Předplatné|20|Z předplatného odebrat zastaralé účty s oprávněními vlastníka|Odeberte zastaralé účty s oprávněními vlastníka z vašich předplatných.|
|Předplatné|5|Odeberte zastaralé účty z předplatného|Odeberte zastaralé účty z vašich předplatných chcete povolit přístup pouze aktuálního uživatele. |
|Předplatné|5|Určení více než jednoho vlastníka předplatného|Pokud chcete mít redundance přístupu správce určete více než jednoho vlastníka předplatného.|
|Předplatné|5|Určit vlastníky až 3 v rámci předplatného|Pokud chcete snížit riziko porušení zabezpečení ohroženým uživatelem, určete méně než 3 na vlastníky těchto předplatných.|
|Trezor klíčů|5|Povolení diagnostických protokolů v Key Vault|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Předplatné|15|Z předplatného odebrat externí účty s oprávněním pro čtení|Z předplatného odeberte externí účty s oprávněními pro čtení, aby nedocházelo k nemonitorovanému přístupu.|
|Předplatné|1|Poskytnutí podrobností kontaktů zabezpečení|Zadejte kontaktní informace o zabezpečení pro každé z vašich předplatných. Kontaktní údaje je e-mailovou adresu a telefonní číslo. Informace, používá se vás kontaktovat, pokud náš tým zabezpečení zjistí ohrožení vašich prostředků|

> ! [POZNÁMKA] Pokud jste nevytvořili zásady podmíněného přístupu, který vyžaduje vícefaktorové ověřování, ale má nastavení vyloučení, posouzení zabezpečení Center MFA doporučení bere v úvahu zásady nedodržují předpisy, protože umožňuje některé uživatele k přihlášení do Azure bez MFA.
>

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
