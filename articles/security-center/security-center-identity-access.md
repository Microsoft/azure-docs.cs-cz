---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552980"
---
# <a name="monitor-identity-and-access-preview"></a>Monitorovat identitu a přístup (Preview)
Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Tento článek popisuje stránku **Identita a přístup** v části věnované zabezpečení prostředků v Azure Security Center.

Úplný seznam doporučení, která se vám můžou zobrazit na této stránce, najdete v tématu [věnovaném doporučením pro identitu a přístup](recommendations-reference.md#recs-identity).

> [!NOTE]
> Identita monitorování a přístup je ve verzi Preview a dostupná jenom na úrovni Standard Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
>

Identita by měla být řídicí rovinou pro váš podnik a ochrana identit by měla být vaší nejvyšší prioritou. Hraniční zabezpečení se vyvinulo z hraniční sítě až po hranice identity. Zabezpečení se zasahuje do ochrany vaší sítě a další informace o tom, jak chrání vaše data, a také o správě zabezpečení vašich aplikací a uživatelů. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Monitorování aktivit souvisejících s identitou umožňuje proaktivně jednat, ještě než k incidentu dojde, nebo provést reaktivní akci k zastavení pokusu o útok. Řídicí panel identity & Access nabízí doporučení, například:

- Povolte MFA pro privilegované účty v předplatném
- Odeberte externí účty s oprávněním pro čtení z předplatného
- Odeberte z předplatného privilegované externí účty

> [!NOTE]
> Pokud má vaše předplatné více než 600 účtů, Security Center není možné spustit doporučení identity pro vaše předplatné. Doporučení, která nejsou spuštěná, jsou uvedená v části "nedostupná posouzení" níže.
Security Center není možné spustit doporučení identity pro agenty správce Cloud Solution Provider (CSP).
>

## <a name="monitor-identity-and-access"></a>Monitorování identity a přístupu

Otevřete seznam identifikovaných identit a potíží s přístupem výběrem **identity & přístupu** z Security Centerho bočního panelu (v části **prostředky**) nebo na stránce Přehled. 

V části **identita & přístup**jsou dvě karty:

- **Přehled**: doporučení identifikovaná Security Center.
- **Předplatná**: seznam vašich předplatných a aktuální stav zabezpečení každého.

[![identity & přístup](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Oddíl přehledu
V části **Přehled**najdete seznam doporučení. První sloupec uvádí doporučení. Druhý sloupec zobrazuje celkový počet odběrů, které jsou ovlivněny tímto doporučením. Třetí sloupec zobrazuje závažnost problému.

1. Vyberte doporučení. Otevře se okno doporučení a zobrazí:

   - Popis doporučení
   - Seznam špatných a zdravých předplatných
   - Seznam prostředků, které se nekontrolují z důvodu neúspěšného posouzení nebo je prostředek v rámci předplatného běžícího na bezplatné úrovni a není vyhodnocený

    [okno doporučení ![](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Pro další podrobnosti vyberte předplatné v seznamu.

### <a name="subscriptions-section"></a>Oddíl Subscriptions
V části **předplatná**je seznam předplatných. První sloupec obsahuje seznam předplatných. Druhý sloupec zobrazuje celkový počet doporučení pro každé předplatné. Třetí sloupec zobrazuje závažnost problémů.

[Karta Předplatná ![](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Vyberte předplatné. Souhrnné zobrazení otevřete tři karty:

   - **Doporučení**: založené na posouzení, které provádí služba Security Center, která selhala.
   - **Předaný posouzení**: seznam posouzení, které provádí služba Security Center, který předává.
   - **Nedostupná posouzení**: seznam posouzení, která se nepodařilo spustit z důvodu chyby nebo protože předplatné má více než 600 účtů.

   V části **doporučení** je seznam doporučení pro vybrané předplatné a závažnost každého doporučení.

   [![doporučení pro výběr předplatného](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Vyberte doporučení pro popis doporučení, seznam špatných a v předplatných a seznam nekontrolovaných prostředků.

   [![popis doporučení](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   V části **předaný posouzení** je seznam vyhovující posouzení.  Závažnost těchto hodnocení je vždy zelená.

   [![úspěšné posouzení](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. V seznamu vyberte úspěšné vyhodnocování, které popisuje posouzení a seznam v případě předplatných, která jsou v pořádku. K dispozici je karta pro bezstavové odběry, která obsahuje seznam všech předplatných, která selhala.

   [![úspěšné posouzení](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Pokud jste vytvořili zásadu podmíněného přístupu, která vyžaduje MFA, ale má nastavené vyloučení, vyhodnocování doporučení pro Security Center MFA posuzuje zásady, které nedodržují předpisy, protože umožňuje některým uživatelům přihlašovat se k Azure bez MFA.

## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících článcích:

- [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
- [Ochrana dat a služby SQL Azure v Azure Security Center](security-center-sql-service-recommendations.md)