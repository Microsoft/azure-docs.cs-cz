---
title: Povolení ochrany heslem místní služby Azure AD
description: Přečtěte si, jak povolit ochranu heslem Azure AD pro místní Active Directory Domain Services prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741775"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Povolení ochrany heslem v místní Azure Active Directory

Uživatelé často vytvářejí hesla, která používají běžná místní slova, jako je škola, sportovní tým nebo slavných osoba. Tato hesla se dají snadno uhodnout a slabá na základě slovníkových útoků. K vynucení silných hesel ve vaší organizaci poskytuje Azure Active Directory (Azure AD) Ochrana heslem globální a vlastní seznam zakázaných hesel. Pokud se v seznamu zakázaných hesel vyskytne shoda, žádost o změnu hesla se nezdařila.

K ochraně místního prostředí Active Directory Domain Services (služba AD DS) můžete nainstalovat a nakonfigurovat ochranu heslem Azure AD pro práci s vaším Prem řadičem domény. V tomto článku se dozvíte, jak povolit ochranu heslem Azure AD pro místní prostředí.

Další informace o tom, jak funguje ochrana heslem Azure AD v místním prostředí, najdete v tématu věnovaném [vymáhání ochrany heslem Azure AD pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Než začnete

V tomto článku se dozvíte, jak povolit ochranu heslem Azure AD pro místní prostředí. Před dokončením tohoto článku [nainstalujte a zaregistrujte službu proxy ochrany heslem Azure AD a agenty řadiče domény](howto-password-ban-bad-on-premises-deploy.md) v místním služba AD DSovém prostředí.

## <a name="enable-on-premises-password-protection"></a>Povolit místní ochranu heslem

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do **Azure Active Directory**  >    >  **metody ověřování** zabezpečení  >  **ochrana heslem**.
1. Nastavte možnost **Povolit ochranu heslem ve službě Windows Server Active Directory** na *hodnotu Ano*.

    Pokud je toto nastavení nastaveno na *ne*, všechny nasazené agenty řadiče domény Azure AD se přejdou do režimu quiescent, ve kterém jsou všechna hesla přijímána tak, jak jsou. Neprovádí se žádné aktivity ověření a události auditu se nevygenerují.

1. Doporučuje se nejdřív nastavit **režim** na *audit*. Až budete s funkcí spokojeni a dopad na uživatele ve vaší organizaci, můžete **režim** přepnout do *vymáhání*. Další informace najdete v následující části o [režimech provozu](#modes-of-operation).
1. Až budete připraveni, vyberte **Uložit**.

    [![Povolte místní ochranu heslem v části metody ověřování v Azure Portal](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Režimy činností

Pokud povolíte místní ochranu heslem služby Azure AD, můžete použít režim *auditování* nebo režim *prosazování* . Doporučujeme, aby počáteční nasazení a testování bylo vždy spuštěno v režimu auditování. Položky v protokolu událostí by se pak měly monitorovat, aby se předpokládalo, jestli se po povolení režimu *vykonání* všech stávajících provozních procesů nenarušilo.

### <a name="audit-mode"></a>Režim auditování

Režim *auditu* je určený jako způsob, jak software spustit v režimu "co if". Každá služba agenta řadiče domény Azure AD pro ochranu heslem vyhodnocuje příchozí heslo podle aktuálně aktivních zásad.

Pokud je aktuální zásada nakonfigurovaná tak, aby byla v režimu auditování, je výsledkem "špatných" hesla zpráva protokolu událostí, ale jsou zpracovaná a aktualizovaná. Toto chování je jediným rozdílem mezi režimem auditu a vymáhání. Všechny ostatní operace mají stejný provoz.

### <a name="enforced-mode"></a>Vynucované režim

*Vynutilný* režim je určený jako konečná konfigurace. Podobně jako v režimu auditování každá služba agenta řadiče domény Azure AD pro ochranu hesel vyhodnocuje příchozí hesla podle aktuálně aktivních zásad. Pokud je povolený režim vymáhání, je heslo, které se považuje za nezabezpečené podle zásad, zamítnuto.

Když agent řadiče domény Azure AD pro ochranu heslem v vynuceném režimu zamítl heslo, zobrazí se koncovým uživatelům podobná chyba, pokud se jim heslo zamítlo tradičním vynuceným nastavením složitosti hesel. Uživatel může například zobrazit následující tradiční chybovou zprávu na obrazovce přihlášení k systému Windows nebo změna hesla:

*"Nelze aktualizovat heslo. Hodnota zadaná pro nové heslo nesplňuje požadavky na délku, složitost a historii domény.*

Tato zpráva je pouze jedním z příkladů možných výsledků. Konkrétní chybová zpráva se může lišit v závislosti na skutečném softwaru nebo scénáři, který se pokouší nastavit nezabezpečené heslo.

Ovlivnění koncoví uživatelé můžou potřebovat spolupracovat s pracovníky IT a pochopit nové požadavky a vybírat zabezpečená hesla.

> [!NOTE]
> Ochrana heslem Azure AD nemá žádné řízení pro konkrétní chybovou zprávu, která se zobrazí v klientském počítači, když se odmítne slabé heslo.

## <a name="next-steps"></a>Další kroky

Pokud chcete přizpůsobit seznam zakázaných hesel pro vaši organizaci, přečtěte si téma [Konfigurace seznamu vlastních hesel pro ochranu heslem Azure AD](tutorial-configure-custom-password-protection.md).

Pokud chcete monitorovat události Prem, přečtěte si téma [monitorování ochrany heslem v Prem Azure AD](howto-password-ban-bad-on-premises-monitor.md).
