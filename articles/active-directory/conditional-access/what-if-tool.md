---
title: Nástroj Co když podmíněného přístupu – Azure Active Directory
description: Zjistěte, jak můžete pochopit dopad zásad podmíněného přístupu na vaše prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620691"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Poradce při potížích s použitím nástroje Co-li v podmíněném přístupu

[Podmíněný přístup](../active-directory-conditional-access-azure-portal.md) je funkce Služby Azure Active Directory (Azure AD), která umožňuje řídit, jak oprávnění uživatelé přistupují k vašim cloudovým aplikacím. Jak víte, co můžete očekávat od zásad podmíněného přístupu ve vašem prostředí? Chcete-li odpovědět na tuto otázku, můžete použít **nástroj Podmíněný přístup Co-li**.

Tento článek vysvětluje, jak můžete tento nástroj použít k testování zásad podmíněného přístupu.

## <a name="what-it-is"></a>Co to je

Nástroj **zásad y podmíněného přístupu Co-li** umožňuje pochopit dopad zásad podmíněného přístupu na vaše prostředí. Namísto testování řízení zásad ručním provedením více přihlášení umožňuje tento nástroj vyhodnotit simulované přihlášení uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace. Sestava neuvádí pouze použité zásady podmíněného přístupu, ale také [klasické zásady,](policy-migration.md#classic-policies) pokud existují.    

Nástroj **Co když** poskytuje způsob, jak rychle určit zásady, které se vztahují na konkrétního uživatele. Tyto informace můžete použít například v případě, že potřebujete vyřešit problém.    

## <a name="how-it-works"></a>Jak to funguje

V **nástroji Podmíněný přístup Co-li**je třeba nejprve nakonfigurovat nastavení scénáře přihlášení, který chcete simulovat. Mezi tato nastavení patří:

- Uživatel, kterého chcete otestovat 
- Cloudové aplikace, ke které by se uživatel pokusil získat přístup
- Podmínky, za kterých se provádí přístup ke konfiguračně cloudovým aplikacím
     
Jako další krok můžete zahájit simulační běh, který vyhodnotí vaše nastavení. Pouze zásady, které jsou povoleny jsou součástí spuštění hodnocení.

Po dokončení vyhodnocení nástroj vygeneruje zprávu o ovlivněných zásad.

## <a name="running-the-tool"></a>Spuštění nástroje

Nástroj Co **když** najdete na stránce **[Podmíněný přístup – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** na webu Azure Portal.

Chcete-li nástroj spustit, klepněte na panelu nástrojů v horní části seznamu zásad na **položku Co kdyby**.

![Citlivostní analýza](./media/what-if-tool/01.png)

Před spuštěním vyhodnocení je nutné nakonfigurovat nastavení.

## <a name="settings"></a>Nastavení

Tato část obsahuje informace o nastavení spuštění simulace.

![Citlivostní analýza](./media/what-if-tool/02.png)

### <a name="user"></a>Uživatel

Můžete vybrat pouze jednoho uživatele. Toto je jediné povinné pole.

### <a name="cloud-apps"></a>Cloudové aplikace

Výchozí nastavení je **Všechny cloudové aplikace**. Výchozí nastavení provádí vyhodnocení všech dostupných zásad ve vašem prostředí. Můžete zúžit rozsah na zásady ovlivňující konkrétní cloudové aplikace.

### <a name="ip-address"></a>IP adresa

IP adresa je jedna adresa IPv4, která napodobuje [podmínku umístění](location-condition.md). Adresa představuje internetovou adresu zařízení, které uživatel používá k přihlášení. IP adresu zařízení můžete ověřit například navigací na [What is my IP address](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy zařízení

Toto nastavení napodobuje [podmínku platforem zařízení](concept-conditional-access-conditions.md#device-platforms) a představuje ekvivalent **všech platforem (včetně nepodporovaných)**. 

### <a name="client-apps"></a>Klientské aplikace

Toto nastavení napodobuje [podmínku klientských aplikací](concept-conditional-access-conditions.md#client-apps-preview).
Ve výchozím nastavení toto nastavení způsobí vyhodnocení všech zásad, které mají **prohlížeče** nebo **mobilní aplikace a desktopové klienty** buď jednotlivě nebo oba vybrané. Také zjistí zásady, které vynucují **Exchange ActiveSync (EAS)**. Toto nastavení můžete zúžit výběrem:

- **Prohlížeč** pro vyhodnocení všech zásad, které mají alespoň **prohlížeč** vybrán. 
- **Mobilní aplikace a klienti pro stolní počítače vyhodnocují** všechny zásady, které mají vybrané alespoň **mobilní aplikace a klienty pro stolní počítače.** 

### <a name="sign-in-risk"></a>Riziko přihlášení

Toto nastavení napodobuje [podmínku rizika přihlášení](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Posouzení 

Hodnocení zahájíte klepnutím na tlačítko **Co když**. Výsledek hodnocení poskytuje sestavu, která se skládá z: 

![Citlivostní analýza](./media/what-if-tool/03.png)

- Indikátor, zda ve vašem prostředí existují klasické zásady
- Zásady, které se vztahují na uživatele
- Zásady, které se nevztahují na uživatele

Pokud pro vybrané cloudové aplikace existují [klasické zásady,](policy-migration.md#classic-policies) zobrazí se vám indikátor. Kliknutím na indikátor budete přesměrováni na stránku klasických zásad. Na stránce klasických zásad můžete migrovat klasické zásady nebo je prostě zakázat. K výsledku hodnocení se můžete vrátit zavřením této stránky.

V seznamu zásad, které se vztahují k vybranému uživateli, můžete také najít seznam [ovládacích prvků udělení](concept-conditional-access-grant.md) a [ovládacích prvků relace, které](concept-conditional-access-session.md) musí uživatel splňovat.

V seznamu zásad, které se nevztahují na uživatele, můžete a také najít důvody, proč tyto zásady neplatí. Pro každou uvedenou zásadu představuje důvod první podmínku, která nebyla splněna. Možným důvodem zásady, která není použita, je zásada zakázané, protože nejsou dále vyhodnocovány.   

## <a name="next-steps"></a>Další kroky

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si informace [o vyžadování vícefaktorové žádosti pro konkrétní aplikace s podmíněným přístupem služby Azure Active Directory](app-based-mfa.md).
- Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, podívejte se na [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 
- Pokud chcete migrovat klasické zásady, přečtěte si témat [imigrace klasických zásad na webu Azure Portal](policy-migration.md)  
