---
title: Nakonfigurovat možnost zůstat přihlášeni? vyzvat k zadání Azure Active Directory účtů
description: Přečtěte si o tom, jak mám přihlášení (políčko zůstat přihlášeni), které zobrazuje zůstat přihlášeni? vyzvat, jak ji nakonfigurovat na portálu Azure Active Directory a jak řešit problémy s přihlašováním.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: a82f81888828cb5edd42c37a6e8b2c2ee51fe603
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339554"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Nakonfigurovat možnost zůstat přihlášeni? vyzvat k zadání účtů Azure AD

Zůstat přihlášeni (políčko zůstat přihlášeni) zobrazuje **zůstat přihlášený** uživatel, který se přihlásí? po úspěšném přihlášení uživatele se zobrazí výzva. Pokud **uživatel odpoví na** tuto výzvu, přihlášený služba si jim umožní [token trvalé aktualizace](../develop/developer-glossary.md#refresh-token). Pro federované klienty se výzva zobrazí po úspěšném ověření uživatele se službou federované identity.

Následující diagram znázorňuje tok přihlašování uživatelů pro spravovaného tenanta a federovaného tenanta a nové zobrazení výzvy k přihlášení. Tento tok obsahuje inteligentní logiku, takže možnost **zůstat přihlášená?** se nezobrazí, pokud systém strojového učení detekuje přihlášení s vysokým rizikem nebo se přihlašuje ze sdíleného zařízení.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagram znázorňující tok přihlašování uživatelů pro spravovaného vs. federovaného tenanta":::

> [!NOTE]
> Když nakonfigurujete možnost zůstat přihlášeni, budete potřebovat použít Azure Active Directory (Azure AD) Premium 1, Premium 2 nebo Basic nebo licenci Microsoft 365. Další informace o licencování a edicích najdete v tématu [Registrace pro Azure AD Premium](active-directory-get-started-premium.md).<br><br>Edice Azure AD Premium a Basic jsou k dispozici pro zákazníky v Číně pomocí světové instance Azure AD. Edice Premium a Basic služby Azure AD v současnosti nejsou podporované ve službě Azure provozované v Číně společností 21Vianet. Další informace vám poskytne [Fórum Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/)s využitím diskuze.

## <a name="configure-kmsi"></a>Konfigurace políčko zůstat přihlášeni

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.
1. Vyberte **Azure Active Directory**, vyberte **Branding společnosti**a pak vyberte **Konfigurovat**.
1. V části **Upřesnit nastavení** vyhledejte **možnost zobrazit, která zůstane pro přihlášení** nastavená.

   Toto nastavení umožňuje zvolit, jestli uživatelé zůstanou přihlášení ke službě Azure AD, dokud se explicitně odhlásí.
   * Pokud zvolíte **ne**, možnost **zůstat přihlášená?** zůstane skrytá, až se uživatel úspěšně přihlásí a uživatel se musí přihlásit pokaždé, když se prohlížeč zavře a znovu otevře.
   * Pokud zvolíte **Ano**, zobrazí se uživateli možnost **zůstat přihlášeni?** .

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Snímek obrazovky ukazuje možnost zobrazit, která zůstane pro přihlášení nastavená.":::

## <a name="troubleshoot-sign-in-issues"></a>Řešení potíží s přihlašováním

Pokud uživatel nefunguje na příkazovém řádku **zůstat přihlášeni?** , jak je znázorněno v následujícím diagramu, ale nepřesouvá pokus o přihlášení, zobrazí se položka protokolu přihlášení, která indikuje přerušení.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Zobrazuje zůstat přihlášený? výzv":::

Podrobnosti o chybě při přihlašování jsou následující a v příkladu jsou zvýrazněné.

* **Kód chyby přihlášení**: 50140
* **Důvod selhání**: k této chybě došlo v důsledku přerušení přihlášení uživatele, když se uživatel přihlašuje.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Příklad záznamu přihlašovacího protokolu s přerušením přihlášení":::

Uživatelům můžete zabránit v zobrazení přerušení nastavením **možnosti zobrazit možnost zůstat přihlášeni** na hodnotu **ne** v nastavení Pokročilé značky.

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších nastaveních, která mají vliv na časový limit relace přihlášení:

* Microsoft 365 – [časový limit nečinné relace](https://docs.microsoft.com/sharepoint/sign-out-inactive-users)
* Podmíněný přístup Azure AD – [frekvence přihlašování uživatelů](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)
* Azure Portal – [časový limit nečinnosti na úrovni adresáře](https://docs.microsoft.com/azure/azure-portal/admin-timeout)
