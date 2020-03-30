---
title: Povolení místní ochrany heslem Azure AD
description: Zjistěte, jak povolit azure ad heslábovou ochranu pro místní prostředí služby Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263813"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Povolení místní ochrany heslem služby Azure Active Directory

Uživatelé často vytvářejí hesla, která používají běžná místní slova, například školu, sportovní tým nebo slavnou osobu. Tato hesla jsou snadno uhodnout, a slabé proti slovníku-založené útoky. Chcete-li vynutit silná hesla ve vaší organizaci, Azure Active Directory (Azure AD) Password Protection poskytuje globální a vlastní seznam zakázaných hesel. Požadavek na změnu hesla se nezdaří, pokud je v těchto seznamu zakázaných hesel shoda.

Chcete-li chránit místní prostředí služby Active Directory Domain Services (AD DS), můžete nainstalovat a nakonfigurovat ochranu heslem služby Azure AD tak, aby fungovala s řadičem domény. Tento článek ukazuje, jak povolit Azure AD heslem pro vaše místní prostředí.

Další informace o tom, jak funguje ochrana heslem Azure AD v místním prostředí, najdete v tématu [Jak vynutit ochranu heslem Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Než začnete

Tento článek ukazuje, jak povolit Azure AD heslem pro vaše místní prostředí. Před dokončením tohoto článku [nainstalujte a zaregistrujte službu proxy azure ad password protection a agenty řadiče domény](howto-password-ban-bad-on-premises-deploy.md) ve vašem místním prostředí služby AD DS.

## <a name="enable-on-premises-password-protection"></a>Povolení místní ochrany heslem

1. Přihlaste se na [portál Azure portal](https://portal.azure.com) a přejděte na Azure Active **Directory** > **Security** > **Authentication metody** > **Ochrana heslem**.
1. Nastavte možnost **Povolit ochranu heslem ve službě Windows Server Active Directory na** *ano*.

    Pokud je toto nastavení nastaveno na *ne*, všichni nasazení agenti řadiče domény ochrany heslem Azure AD přejdou do klidový režim, kde jsou všechna hesla přijímána tak, jak jsou. Nejsou prováděny žádné ověřovací aktivity a nejsou generovány události auditu.

1. Doporučujeme nejprve nastavit **režim** *auditování*. Poté, co jste spokojeni s funkcí a dopad na uživatele ve vaší organizaci, můžete přepnout **režim** na *vynucený*. Další informace naleznete v následující části o [provozních režimech](#modes-of-operation).
1. Až budete připraveni, vyberte **Uložit**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Režimy činností

Když povolíte místní azure ad ochranu heslem, můžete použít režim *auditování* nebo *vynucovat* režim. Doporučujeme, aby počáteční nasazení a testování vždy spustit v režimu auditu. Položky v protokolu událostí by pak měly být sledovány, aby bylo možné předvídat, zda budou po povolení režimu *vynucení* narušeny všechny existující provozní procesy.

### <a name="audit-mode"></a>Režim auditování

*Režim auditování* je určen jako způsob spuštění softwaru v režimu "co kdyby". Každá služba agenta řadiče domény ochrany hesel Azure AD vyhodnocuje příchozí heslo podle aktuálně aktivních zásad.

Pokud je aktuální zásada nakonfigurována jako v režimu auditování, "chybná" hesla mají za následek zprávy protokolu událostí, ale jsou zpracovány a aktualizovány. Toto chování je jediný rozdíl mezi auditování a vynucování režimu. Všechny ostatní operace běží stejně.

### <a name="enforced-mode"></a>Vynucený režim

*Vynucený* režim je určen jako konečná konfigurace. Stejně jako v režimu auditování, každá služba agenta azure ad password protection dc vyhodnocuje příchozí hesla podle aktuálně aktivní zásady. Pokud je však povolen vynucený režim, heslo, které je považováno za nezabezpečené podle zásady, je odmítnuto.

Když je heslo odmítnuto v vynuceném režimu agentem řadiče domény ochrany hesel Azure AD, koncový uživatel uvidí podobnou chybu, jako by viděli, kdyby jejich heslo bylo odmítnuto tradičním vynucením složitosti místního hesla. Uživateli se například může na přihlašovací obrazovce systému Windows přihlašovat nebo změnit heslo následující tradiční chybová zpráva:

*"Nelze aktualizovat heslo. Hodnota poskytnutá pro nové heslo nesplňuje požadavky na délku, složitost nebo historii domény."*

Tato zpráva je pouze jedním příkladem několika možných výsledků. Konkrétní chybová zpráva se může lišit v závislosti na skutečném softwaru nebo scénáři, který se pokouší nastavit nezabezpečené heslo.

Dotčení koncoví uživatelé možná budou muset spolupracovat se svými pracovníky IT, aby porozuměli novým požadavkům a zvolili bezpečná hesla.

> [!NOTE]
> Azure AD Password Protection nemá žádnou kontrolu nad konkrétní chybovou zprávu zobrazenou klientským počítačem při odmítnutí slabého hesla.

## <a name="next-steps"></a>Další kroky

Pokud chcete přizpůsobit seznam zakázaných hesel pro vaši organizaci, [přečtěte si informace o konfiguraci seznamu vlastních zakázaných hesel služby Azure AD](tutorial-configure-custom-password-protection.md).

Chcete-li sledovat události on-prem, přečtěte si informace [o monitorování ochrany heslem Azure AD](howto-password-ban-bad-on-premises-monitor.md)v oblasti azure ad .
