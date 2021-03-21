---
title: Nástroj pro What If podmíněného přístupu – Azure Active Directory
description: Zjistěte, jak můžete pochopit dopad zásad podmíněného přístupu ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3ac799203cade3a907acbe28dee3a8023891db2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077638"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Řešení potíží pomocí nástroje What If v podmíněném přístupu

[Podmíněný přístup](./overview.md) je schopností Azure Active Directory (Azure AD), která vám umožní řídit, jak autorizovaným uživatelům přistupují k vašim cloudovým aplikacím. Jak se dozvíte, co očekávat od zásad podmíněného přístupu ve vašem prostředí? K zodpovězení této otázky můžete použít nástroj pro **What If podmíněného přístupu**.

Tento článek vysvětluje, jak můžete pomocí tohoto nástroje testovat zásady podmíněného přístupu.

## <a name="what-it-is"></a>Co to je

**Nástroj zásady podmíněného přístupu what if Policy** vám umožní pochopit dopad zásad podmíněného přístupu ve vašem prostředí. Místo testování zásad pomocí ručního provádění více přihlášení vám nástroj umožní vyhodnotit simulované přihlašování uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace. Tato sestava neobsahuje jenom seznam použitých zásad podmíněného přístupu, ale také [klasické zásady](policy-migration.md#classic-policies) , pokud existují.    

Nástroj **What If** poskytuje způsob, jak rychle určit zásady, které se vztahují na konkrétního uživatele. Tyto informace můžete použít například v případě, že potřebujete vyřešit problém.    

## <a name="how-it-works"></a>Jak to funguje

V **nástroji what if podmíněného přístupu** musíte nejprve nakonfigurovat nastavení scénáře přihlášení, který chcete simulovat. Mezi tato nastavení patří:

- Uživatel, kterého chcete testovat 
- Cloudové aplikace, se kterými se uživatel pokusí získat přístup
- Podmínky, za kterých se provádí přístup k nakonfigurovaným cloudovým aplikacím
     
V dalším kroku můžete iniciovat spuštění simulace, které vyhodnotí vaše nastavení. Pouze zásady, které jsou povoleny, jsou součástí zkušebního běhu.

Po dokončení vyhodnocení nástroj vygeneruje sestavu ovlivněných zásad. Pokud chcete získat další informace o zásadách podmíněného přístupu, [přehledy podmíněného přístupu a vytváření sestav](howto-conditional-access-insights-reporting.md) můžou poskytnout další podrobnosti o zásadách v režimu pouze sestavy a aktuálně povolených zásadách.

## <a name="running-the-tool"></a>Spuštění nástroje

Nástroj **What If** můžete najít na stránce **[zásady podmíněného přístupu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** v Azure Portal.

Chcete-li spustit nástroj, klikněte na panelu nástrojů v seznamu zásad na položku **What If**.

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Snímek obrazovky se stránkou podmíněného přístupu – zásady v Azure Portal. Na panelu nástrojů je zvýrazněna položka co když je zvýrazněna." border="false":::

Předtím, než můžete spustit vyhodnocení, je nutné nakonfigurovat nastavení.

## <a name="settings"></a>Nastavení

V této části najdete informace o nastavení spuštění simulace.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Snímek obrazovky stránky Azure Portal What If s poli pro uživatele, cloudové aplikace, adresu I P, platformu zařízení, klientskou aplikací a riziko pro přihlášení." border="false":::

### <a name="user"></a>User

Můžete vybrat jenom jednoho uživatele. Toto je jediné povinné pole.

### <a name="cloud-apps"></a>Cloudové aplikace

Výchozí hodnota pro toto nastavení je **všechny cloudové aplikace**. Výchozí nastavení provádí vyhodnocení všech dostupných zásad ve vašem prostředí. Rozsah můžete zúžit na zásady ovlivňující konkrétní cloudové aplikace.

### <a name="ip-address"></a>IP adresa

IP adresa je jedna adresa IPv4 pro napodobení [podmínky umístění](location-condition.md). Adresa představuje internetovou adresu zařízení používaného vaším uživatelem k přihlášení. IP adresu zařízení můžete například ověřit tak, že přejdete na [adresu moje IP adresa](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Platformy zařízení

Toto nastavení napodobuje [stav platforem zařízení](concept-conditional-access-conditions.md#device-platforms) a představuje ekvivalent **všech platforem (včetně nepodporovaných)**. 

### <a name="client-apps"></a>Klientské aplikace

Toto nastavení napodobuje [stav klientských aplikací](concept-conditional-access-conditions.md#client-apps).
Ve výchozím nastavení toto nastavení způsobí vyhodnocení všech zásad s **prohlížečem** nebo **mobilními aplikacemi a desktopových klientů** buď jednotlivě, nebo podle obou vybraných. Také detekuje zásady, které vynutily **protokol Exchange ActiveSync (EAS)**. Toto nastavení můžete zúžit výběrem možnosti:

- **Prohlížeč** pro vyhodnocení všech zásad s aspoň vybraným **prohlížečem** . 
- **Mobilní aplikace a klienti klasické pracovní plochy** pro vyhodnocení všech zásad, které mají vybrané aspoň **mobilní aplikace a klientské počítače** . 

### <a name="sign-in-risk"></a>Riziko přihlášení

Toto nastavení napodobuje [rizikové podmínky přihlášení](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Hodnocení 

Kliknutím na **What If** spustíte vyhodnocení. Výsledek vyhodnocení obsahuje sestavu, která se skládá z těchto součástí: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Snímek obrazovky se zkušební sestavou. Text označuje, že je nakonfigurovaná aspoň jedna klasická zásada. Karty jsou k dispozici pro zobrazení zásad." border="false":::

- Indikátor, jestli ve vašem prostředí existují klasické zásady
- Zásady, které se vztahují na uživatele
- Zásady, které se nevztahují na uživatele

Pokud pro vybrané cloudové aplikace existují [klasické zásady](policy-migration.md#classic-policies) , zobrazí se vám indikátor. Kliknutím na indikátor budete přesměrováni na stránku klasických zásad. Na stránce klasické zásady můžete migrovat klasické zásady, nebo ji jenom zakázat. Na výsledek vyhodnocení se můžete vrátit zavřením této stránky.

V seznamu zásad, které se vztahují k vybranému uživateli, můžete také vyhledat seznam [ovládacích prvků](concept-conditional-access-grant.md) a [řízení relace](concept-conditional-access-session.md) , které musí váš uživatel splňovat.

V seznamu zásad, které se nevztahují na uživatele, můžete najít také důvody, proč se tyto zásady nevztahují. Pro každou uvedenou zásadu představuje důvod první podmínku, která nebyla splněna. Možný důvod, proč zásada, která není použita, je zakázaná zásada, protože se ještě nevyhodnotí.   

## <a name="next-steps"></a>Další kroky

- Další informace o aplikaci zásad podmíněného přístupu najdete v režimu pouze sestavy s použitím [přehledů a vytváření sestav podmíněného přístupu](howto-conditional-access-insights-reporting.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si téma [společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md).
