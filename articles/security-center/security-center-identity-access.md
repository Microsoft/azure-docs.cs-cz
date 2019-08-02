---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: v-mohabe
ms.openlocfilehash: 73480c6e24ab13b7764c5b72280c19971eb3d039
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296490"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorování identity a přístupu v Azure Security Center (Preview)
Tento článek vám pomůže používat Azure Security Center k monitorování identit a aktivit přístupu uživatelů.

> [!NOTE]
> Odkaz "zobrazení *klasické* identity & přístupu" se vyřadí z 31. července 2019. Pokud se chcete dozvědět o alternativních službách, klikněte [sem](security-center-features-retirement-july2019.md#menu_classicidentity) .

> [!NOTE]
> Identita monitorování a přístup je ve verzi Preview a dostupná jenom na úrovni Standard Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>

Identita by měla být základní „řídicí plochou“ vaší organizace a její ochrana je tou nejvyšší prioritou. Hraniční zabezpečení se vyvinulo z hraniční sítě až po hranice identity. Zabezpečení se zasahuje do ochrany vaší sítě a další informace o tom, jak chrání vaše data, a také o správě zabezpečení vašich aplikací a uživatelů. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Monitorování aktivit souvisejících s identitou umožňuje proaktivně jednat, ještě než k incidentu dojde, nebo provést reaktivní akci k zastavení pokusu o útok. Řídicí panel identity & Access nabízí doporučení, například:

- Povolte MFA pro privilegované účty v předplatném
- Odeberte z předplatného externí účty s oprávněním pro zápis
- Odeberte z předplatného privilegované externí účty

> [!NOTE]
> Pokud má vaše předplatné více než 600 účtů, Security Center není možné spustit doporučení identity pro vaše předplatné. Doporučení, která nejsou spuštěná, jsou uvedená v části nedostupná posouzení, která jsou popsána níže.
Security Center není možné spustit doporučení identity pro agenty správce Cloud Solution Provider (CSP).
>

Seznam [](security-center-identity-access.md#recommendations) doporučení identity a přístupu, která poskytuje Security Center, najdete v tématu věnovaném doporučením.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Můžete monitorovat stav zabezpečení vašich prostředků na **Security Center – přehled** řídicího panelu. Oddíl **** Resources je indikátor stavu ukazující závažnost pro každý typ prostředku.

Seznam všech problémů můžete zobrazit tak, že vyberete **doporučení**. V části **prostředky**můžete zobrazit seznam problémů, které jsou specifické pro výpočetní & aplikace, zabezpečení dat, sítě nebo identity & přístup. Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Úplný seznam doporučení identity a přístupu najdete v tématu věnovaném [doporučením](security-center-identity-access.md#recommendations).

Chcete-li pokračovat, vyberte možnost **identita & přístup** v části **prostředky** nebo v hlavní nabídce Security Center.

![Řídicí panel Security Center][1]

## <a name="monitor-identity-and-access"></a>Monitorování identity a přístupu
V části **identita & přístup**jsou dvě karty:

- **Přehled**: doporučení identifikovaná Security Center.
- **Předplatná**: seznam vašich předplatných a aktuální stav zabezpečení každého.

![Identita a přístup][2]

### <a name="overview-section"></a>Oddíl přehled
V části **Přehled**najdete seznam doporučení. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet odběrů, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému.

1. Vyberte doporučení. Otevře se okno doporučení a zobrazí:

   - Popis doporučení
   - Seznam špatných a zdravých předplatných
   - Seznam prostředků, které se nekontrolují z důvodu neúspěšného posouzení nebo je prostředek v rámci předplatného běžícího na bezplatné úrovni a není vyhodnocený

   ![Okno doporučení][3]

1. Pro další podrobnosti vyberte předplatné v seznamu.

### <a name="subscriptions-section"></a>Oddíl Subscriptions
V **** části Předplatná je seznam předplatných. První sloupec obsahuje seznam předplatných. Druhý sloupec zobrazuje celkový počet doporučení pro každé předplatné. Třetí sloupec zobrazuje závažnost problémů.

![Karta předplatného][4]

1. Vyberte předplatné. Souhrnné zobrazení otevřete tři karty:

   - **Doporučení**: založené na posouzení, které provádí služba Security Center, která selhala.
   - **Předaný posouzení**: seznam posouzení, které provádí služba Security Center, který předává.
   - **Nedostupná posouzení**: seznam posouzení, která se nepodařilo spustit z důvodu chyby nebo protože předplatné má více než 600 účtů.

   V části **doporučení** je seznam doporučení pro vybrané předplatné a závažnost každého doporučení.

   ![Doporučení pro výběr předplatného][5]

1. Vyberte doporučení pro popis doporučení, seznam špatných a v předplatných a seznam nekontrolovaných prostředků.

   ![Popis doporučení][6]

   V části **předaný posouzení** je seznam vyhovující posouzení.  Závažnost těchto hodnocení je vždy zelená.

   ![Vyhovující posouzení][7]

1. V seznamu vyberte úspěšné vyhodnocování, které popisuje posouzení a seznam v případě předplatných, která jsou v pořádku. K dispozici je karta pro bezstavové odběry, která obsahuje seznam všech předplatných, která selhala.

   ![Vyhovující posouzení][8]

## <a name="recommendations"></a>Doporučení
Následující tabulku použijte jako pomůcku, která vám pomůže pochopit dostupnou & doporučení pro přístup k identitám a jejich použití.

|Typ prostředku|Bezpečnostní skóre|Doporučení|Popis|
|----|----|----|----|
|Subscription|50|Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.|Povolte službu Multi-Factor Authentication (MFA) pro všechny účty předplatného s oprávněními správce, aby se zabránilo narušení účtů nebo prostředků.|
|Subscription|40|Vícefaktorové ověřování by mělo být povolené u účtů předplatného s oprávněním k zápisu.|Povolte službu Multi-Factor Authentication (MFA) pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků.|
|Subscription|30|Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.|Odeberte externí účty s oprávněním vlastníka z předplatného, aby nedocházelo k nemonitorovanému přístupu.|
|Subscription|30|Vícefaktorové ověřování by mělo být povolené u účtů předplatného s oprávněním ke čtení|Povolte službu Multi-Factor Authentication (MFA) pro všechny účty předplatného s oprávněním ke čtení, abyste zabránili narušení účtů nebo prostředků.|
|Subscription|25|Z předplatného by se měly odebrat externí účty s oprávněními pro zápis|Odeberte externí účty s oprávněním k zápisu z předplatného, aby nedocházelo k nemonitorovanému přístupu. |
|Subscription|20|Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Odeberte zastaralé účty s oprávněním vlastníka z vašich předplatných.|
|Subscription|5|Zastaralé účty by se měly odebírat z předplatného.|Odeberte zastaralé účty z vašich předplatných, abyste mohli povolit přístup jenom k aktuálním uživatelům. |
|Subscription|5|K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.|Chcete-li mít přístup správce k redundanci, určete více než jednoho vlastníka předplatného.|
|Subscription|5|Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.|Určete méně než 3 vlastníky předplatného, aby se snížila pravděpodobnost porušení napadeného vlastníka.|
|Trezor klíčů|5|Měly by být povolené diagnostické protokoly v Key Vault.|Povolení protokolů a uchovávat až po roce. To umožňuje znovu vytvořit záznamy aktivit za účelem šetření, když dojde k incidentu zabezpečení nebo dojde k ohrožení vaší sítě. |
|Subscription|15|Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.|Odeberte externí účty s oprávněním ke čtení z předplatného, aby nedocházelo k nemonitorovanému přístupu.| 

> [!NOTE]
> Pokud jste vytvořili zásadu podmíněného přístupu, která vyžaduje MFA, ale má nastavené vyloučení, vyhodnocování doporučení pro Security Center MFA posuzuje zásady, které nedodržují předpisy, protože umožňuje některým uživatelům přihlašovat se k Azure bez MFA.

## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

- [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
- [Ochrana dat a služby SQL Azure v Azure Security Center](security-center-sql-service-recommendations.md)

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
