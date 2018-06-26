---
title: Co je co Pokud nástroje Azure Active Directory podmíněného přístupu?
description: Zjistěte, jak můžete porozumět dopadu vaší zásady podmíněného přístupu na vašem prostředí.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 897f1b96f0861fb13ba0e6c2427b621ea6a12a06
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750078"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Co je co Pokud nástroje Azure Active Directory podmíněného přístupu?

[Podmíněný přístup](active-directory-conditional-access-azure-portal.md) je funkce služby Azure Active Directory (Azure AD), jak vám umožní řídit oprávnění uživatelům přístup cloudových aplikací. Jak poznáte, co mají očekávat formuláře zásady podmíněného přístupu v prostředí? Na tuto otázku odpovědět, můžete použít **podmíněný přístup jak postupovat, pokud nástroj**.

Tento článek vysvětluje, jak tento nástroj můžete použít k otestování vašich zásad podmíněného přístupu.

## <a name="what-it-is"></a>Co to je

**Podmíněný přístup, jaké Pokud zásada nástroj** můžete porozumět dopadu vaší zásady podmíněného přístupu na vašem prostředí. Místo testovací řídí zásad tak, že provedete několik přihlášení ručně tento nástroj umožňuje vyhodnotit u simulované přihlášení uživatele. Simulace odhadne dopad na tomto přihlášení má na vaše zásady a generuje sestavy simulace. Sestava neobsahuje pouze seznam použitých podmíněného zásady přístupu ale i [classic zásady](active-directory-conditional-access-migration.md#classic-policies) Pokud existují.    

Co v případě nástrojů taky poskytuje způsob, jak rychle určuje zásadách, které platí pro konkrétního uživatele. Informace, můžete použít například, pokud potřebujete vyřešit nějaký problém.  

## <a name="how-it-works"></a>Jak to funguje

V **podmíněného přístupu nástroje co když**, je nutné nejprve nakonfigurovat nastavení chcete simulovat scénář přihlášení. Tato nastavení zahrnují:

- Uživatele, kterého chcete testovat 

- Cloudové aplikace, které by uživatel pokusí o přístup

- Podmínky, za které přístup k nakonfiguruje cloudové aplikace se provádí
     
Jako další krok můžete zahájit spustit simulaci, které vyhodnotí nastavení. Pouze zásady, které jsou povolené jsou součástí spusťte zkušební verzi.


Po dokončení vyhodnocení Nástroj generuje sestavy ovlivněných zásad.


## <a name="running-the-tool"></a>Spuštění nástroje

Můžete najít **co když** ve **[podmíněný přístup – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** na portálu Azure.

Chcete-li spustit nástroj, na panelu nástrojů nad seznam zásad, klikněte na tlačítko **co když**.

![Co když](./media/active-directory-conditional-access-whatif/01.png)

Před spuštěním zkušební verzi, musíte nakonfigurovat nastavení.

## <a name="settings"></a>Nastavení

Tato část poskytuje informace o nastavení simulace spustit.

![Co když](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Uživatel

Můžete zvolit pouze jednoho uživatele. Toto je pouze požadované pole.

### <a name="cloud-apps"></a>Cloudové aplikace

Výchozí hodnota pro toto nastavení je **všech cloudových aplikací**. Ve výchozím nastavení provádí vyhodnocení všech dostupných zásad ve vašem prostředí. Můžete zúžit obor zásad, které mají vliv na konkrétní cloudové aplikace.


### <a name="ip-address"></a>IP adresa

IP adresa je jedna adresa IPv4 tak, aby napodoboval [umístění podmínku](active-directory-conditional-access-locations.md). Adresa představuje internetové adresa zařízení, které používá vaše uživatele k přihlášení. Můžete ověřit IP adresa zařízení, například přejdete na [co je adresa IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy zařízení

Napodobuje toto nastavení [podmínku platformy zařízení](active-directory-conditional-access-conditions.md#device-platforms) a představuje ekvivalent **všechny platformy (včetně nepodporovaný)**. 
### <a name="client-apps"></a>Klientské aplikace

Napodobuje toto nastavení [klienta aplikace podmínku](active-directory-conditional-access-conditions.md#client-apps).
Ve výchozím nastavení, toto nastavení způsobí, že vyhodnocení všechny zásady s **prohlížeče** nebo **mobilní aplikace a klienti vzdálené plochy** buď jednotlivě nebo obojí vybrané. Navíc rozpozná zásady, které vynucují **Exchange ActiveSync (EAS)**. Toto nastavení můžete zúžit výběrem:

- **Prohlížeč** vyhodnotit všechny zásady s nejméně **prohlížeče** vybrané. 

- **Mobilní aplikace a klienti vzdálené plochy** vyhodnotit všechny zásady s nejméně **mobilní aplikace a klienti vzdálené plochy** vybrané. 


### <a name="sign-in-risk"></a>Riziko přihlášení

Napodobuje toto nastavení [podmínka přihlášení riziko](active-directory-conditional-access-conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Vyhodnocení 

Spuštění zkušební verzi klepnutím na **co když**. Výsledek vyhodnocení vám poskytne sestavu, která se skládá z: 

![Co když](./media/active-directory-conditional-access-whatif/03.png)

- Slouží jako ukazatel jestli classic zásady existují ve vašem prostředí
- Zásady, které platí pro vaše uživatele
- Zásady, které se nevztahují na uživatele


Pokud [classic zásady](active-directory-conditional-access-migration.md#classic-policies) neexistuje pro aplikace pro vybraný cloud, se prezentuje jako ukazatel. Klepnutím na indikátor, budete přesměrováni na stránku classic zásady. Na stránce classic zásady můžete migrovat zásadu classic nebo právě ji zakázat. Můžete se vrátit k vaší výsledek vyhodnocení podle zavřením tohoto dialogového okna.

V seznamu zásad, které platí pro vybraného uživatele, můžete také získat seznam [udělit ovládací prvky](active-directory-conditional-access-controls.md#grant-controls) a [relace](active-directory-conditional-access-controls.md#session-controls) ovládací prvky, musí splňovat vaše uživatele.

V seznamu zásad, které se nevztahují na uživatele můžete a také najít z důvodů, proč se tyto zásady nevztahují. U každé uvedené zásady z důvodu představuje první podmínka, která nebyla splněná. Důvodem pro zásadu, která není použita je zásadu zakázáno, protože další nebudou vyhodnocovány.   



## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Pokud chcete migrovat classic zásady, přečtěte si téma [migraci na portálu Azure classic zásad](active-directory-conditional-access-migration.md)  
