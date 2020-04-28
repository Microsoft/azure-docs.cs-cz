---
title: Nástroj pro What If podmíněného přístupu – Azure Active Directory
description: Zjistěte, jak můžete pochopit dopad zásad podmíněného přístupu ve vašem prostředí.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77620691"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Řešení potíží pomocí nástroje What If v podmíněném přístupu

[Podmíněný přístup](../active-directory-conditional-access-azure-portal.md) je schopností Azure Active Directory (Azure AD), která vám umožní řídit, jak autorizovaným uživatelům přistupují k vašim cloudovým aplikacím. Jak se dozvíte, co očekávat od zásad podmíněného přístupu ve vašem prostředí? K zodpovězení této otázky můžete použít nástroj pro **What If podmíněného přístupu**.

Tento článek vysvětluje, jak můžete pomocí tohoto nástroje testovat zásady podmíněného přístupu.

## <a name="what-it-is"></a>Co to je

**Nástroj zásady podmíněného přístupu what if Policy** vám umožní pochopit dopad zásad podmíněného přístupu ve vašem prostředí. Místo testování zásad pomocí ručního provádění více přihlášení vám nástroj umožní vyhodnotit simulované přihlašování uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace. Tato sestava neobsahuje jenom seznam použitých zásad podmíněného přístupu, ale také [klasické zásady](policy-migration.md#classic-policies) , pokud existují.    

Nástroj **What If** poskytuje způsob, jak rychle určit zásady, které se vztahují na konkrétního uživatele. Tyto informace můžete použít například v případě, že potřebujete vyřešit problém.    

## <a name="how-it-works"></a>Jak to funguje

V **nástroji what if podmíněného přístupu**musíte nejprve nakonfigurovat nastavení scénáře přihlášení, který chcete simulovat. Mezi tato nastavení patří:

- Uživatel, kterého chcete testovat 
- Cloudové aplikace, se kterými se uživatel pokusí získat přístup
- Podmínky, za kterých se provádí přístup ke konfiguraci cloudových aplikací
     
V dalším kroku můžete iniciovat spuštění simulace, které vyhodnotí vaše nastavení. Pouze zásady, které jsou povoleny, jsou součástí zkušebního běhu.

Po dokončení vyhodnocení nástroj vygeneruje sestavu ovlivněných zásad.

## <a name="running-the-tool"></a>Spuštění nástroje

Nástroj **What If** můžete najít na stránce **[zásady podmíněného přístupu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** v Azure Portal.

Chcete-li spustit nástroj, klikněte na panelu nástrojů v seznamu zásad na položku **What If**.

![Citlivostní analýza](./media/what-if-tool/01.png)

Předtím, než můžete spustit vyhodnocení, je nutné nakonfigurovat nastavení.

## <a name="settings"></a>Nastavení

V této části najdete informace o nastavení spuštění simulace.

![Citlivostní analýza](./media/what-if-tool/02.png)

### <a name="user"></a>Uživatel

Můžete vybrat jenom jednoho uživatele. Toto je jediné povinné pole.

### <a name="cloud-apps"></a>Cloudové aplikace

Výchozí hodnota pro toto nastavení je **všechny cloudové aplikace**. Výchozí nastavení provádí vyhodnocení všech dostupných zásad ve vašem prostředí. Rozsah můžete zúžit na zásady ovlivňující konkrétní cloudové aplikace.

### <a name="ip-address"></a>IP adresa

IP adresa je jedna adresa IPv4 pro napodobení [podmínky umístění](location-condition.md). Adresa představuje internetovou adresu zařízení používaného vaším uživatelem k přihlášení. IP adresu zařízení můžete například ověřit tak, že přejdete na [adresu moje IP adresa](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy zařízení

Toto nastavení napodobuje [stav platforem zařízení](concept-conditional-access-conditions.md#device-platforms) a představuje ekvivalent **všech platforem (včetně nepodporovaných)**. 

### <a name="client-apps"></a>Klientské aplikace

Toto nastavení napodobuje [stav klientských aplikací](concept-conditional-access-conditions.md#client-apps-preview).
Ve výchozím nastavení toto nastavení způsobí vyhodnocení všech zásad s **prohlížečem** nebo **mobilními aplikacemi a desktopových klientů** buď jednotlivě, nebo podle obou vybraných. Také detekuje zásady, které vynutily **protokol Exchange ActiveSync (EAS)**. Toto nastavení můžete zúžit výběrem možnosti:

- **Prohlížeč** pro vyhodnocení všech zásad s aspoň vybraným **prohlížečem** . 
- **Mobilní aplikace a klienti klasické pracovní plochy** pro vyhodnocení všech zásad, které mají vybrané aspoň **mobilní aplikace a klientské počítače** . 

### <a name="sign-in-risk"></a>Riziko přihlášení

Toto nastavení napodobuje [rizikové podmínky přihlášení](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Posouzení 

Kliknutím na **What If**spustíte vyhodnocení. Výsledek vyhodnocení obsahuje sestavu, která se skládá z těchto součástí: 

![Citlivostní analýza](./media/what-if-tool/03.png)

- Indikátor, jestli ve vašem prostředí existují klasické zásady
- Zásady, které se vztahují na uživatele
- Zásady, které se nevztahují na uživatele

Pokud pro vybrané cloudové aplikace existují [klasické zásady](policy-migration.md#classic-policies) , zobrazí se vám indikátor. Kliknutím na indikátor budete přesměrováni na stránku klasických zásad. Na stránce klasické zásady můžete migrovat klasické zásady, nebo ji jenom zakázat. Na výsledek vyhodnocení se můžete vrátit zavřením této stránky.

V seznamu zásad, které se vztahují k vybranému uživateli, můžete také vyhledat seznam [ovládacích prvků](concept-conditional-access-grant.md) a [řízení relace](concept-conditional-access-session.md) , které musí váš uživatel splňovat.

V seznamu zásad, které se nevztahují na uživatele, můžete najít také důvody, proč se tyto zásady nevztahují. Pro každou uvedenou zásadu představuje důvod první podmínku, která nebyla splněna. Možný důvod, proč zásada, která není použita, je zakázaná zásada, protože se ještě nevyhodnotí.   

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 
- Pokud chcete migrovat klasické zásady, přečtěte si téma [migrace klasických zásad v Azure Portal](policy-migration.md)  
