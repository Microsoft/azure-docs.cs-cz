---
title: Přehled režimu sdíleného zařízení
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si o režimu sdíleného zařízení, který umožňuje sdílení zařízení pro vaše pracovníky Firstline.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550237"
---
# <a name="overview-of-shared-device-mode"></a>Přehled režimu sdíleného zařízení

Režim sdíleného zařízení je funkce služby Azure Active Directory, která umožňuje vytvářet aplikace, které podporují firstline workers a povolují režim sdíleného zařízení na zařízeních, která jsou k nim nasazena.

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>Co jsou firstline pracovníci?

Firstline Workers jsou maloobchodní zaměstnanci, pracovníci údržby a terénní agenti, zdravotnický personál a další uživatelé, kteří nesedí před počítačem nebo nepoužívají firemní e-mail pro spolupráci. V následujících částech jsou uvedeny aspekty a výzvy podpory firstline workers, následované úvodem k funkcím poskytovaným společností Microsoft, které umožňují vaši aplikaci používat pracovníci firstline organizace.

### <a name="challenges-of-supporting-firstline-workers"></a>Výzvy podpory firstline pracovníků

Povolení pracovních postupů firstline worker zahrnuje výzvy, které obvykle nepředstavují pracovníci s typickými informacemi. Tyto výzvy mohou zahrnovat vysokou míru fluktuace a menší obeznámenost s klíčovými nástroji produktivity organizace. Aby posílily své firstline pracovníky, organizace přijímají různé strategie. Někteří přijímají strategii "přines si vlastní zařízení) (BYOD), ve které jejich zaměstnanci používají obchodní aplikace na svém osobním telefonu, zatímco jiní poskytují svým zaměstnancům sdílená zařízení, jako jsou iPady nebo tablety Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Podpora více uživatelů na zařízeních určených pro jednoho uživatele

Vzhledem k tomu, že mobilní zařízení se systémem iOS nebo Android byla navržena pro jednotlivé uživatele, většina aplikací optimalizuje jejich prostředí pro použití jedním uživatelem. Součástí tohoto optimalizovaného prostředí je povolení jednotného přihlašování napříč aplikacemi a udržování uživatelů přihlášených na svém zařízení. Když uživatel odebere svůj účet z aplikace, aplikace obvykle nepovažuje za událost související se zabezpečením. Mnoho aplikací si dokonce uchovává přihlašovací údaje uživatele pro rychlé přihlášení. Možná jste to dokonce zažili sami, když jste odstranili aplikaci z mobilního zařízení a pak ji přeinstalovali, jen abyste zjistili, že jste stále přihlášeni.

### <a name="global-sign-in-and-sign-out-sso"></a>Globální přihlášení a odhlášení (SSO)

Aby mohli zaměstnanci organizace používat své aplikace ve fondu zařízení sdílených těmito zaměstnanci, musí vývojáři povolit opačné prostředí. Zaměstnanci by měli být schopni vybrat zařízení z bazénu a provést jediné gesto, aby "aby to jejich" po dobu jejich směny. Na konci své směny by měli být schopni provést další gesto, aby se na zařízení globálně odhlašovali, přičemž všechny jejich osobní a firemní informace budou odebrány, aby je mohli vrátit do fondu zařízení. Kromě toho, pokud zaměstnanec zapomene odhlásit, zařízení by mělo být automaticky odhlášení na konci své směny a / nebo po určité době nečinnosti.

Služba Azure Active Directory umožňuje tyto scénáře pomocí funkce nazývané **režim sdíleného zařízení**.

## <a name="introducing-shared-device-mode"></a>Zavedení režimu sdíleného zařízení

Jak již bylo zmíněno, režim sdíleného zařízení je funkce služby Azure Active Directory, která umožňuje:

* Vytváření aplikací, které podporují firstline pracovníky
* Nasazení zařízení do firstline pracovníků a zapnutí režimu sdíleného zařízení

### <a name="build-applications-that-support-firstline-workers"></a>Vytváření aplikací, které podporují firstline pracovníky

Pracovníci Firstline ve vašich aplikacích můžete podporovat pomocí microsoft ověřovací knihovny (MSAL) a [aplikace Microsoft Authenticator,](../user-help/user-help-auth-app-overview.md) abyste povolili stav zařízení nazývaný *režim sdíleného zařízení*. Pokud je zařízení v režimu sdíleného zařízení, společnost Microsoft poskytuje vaší aplikaci informace, které jí umožňují měnit chování na základě stavu uživatele v zařízení a chránit uživatelská data.

Podporované funkce jsou:

* **Přihlaste se k uživatelskému zařízení v celém** zařízení prostřednictvím libovolné podporované aplikace.
* **Odhlaste se od uživatele celého zařízení** prostřednictvím libovolné podporované aplikace.
* **Dotaz na stav zařízení** k určení, pokud je vaše aplikace na zařízení, které je v režimu sdíleného zařízení.
* **Dotaz stavu zařízení uživatele** v zařízení k určení, pokud se něco změnilo od posledního použití aplikace.

Podpora režimu sdíleného zařízení by měla být považována za vylepšení zabezpečení a upgrade funkcí pro vaši aplikaci a může pomoci zvýšit jeho přijetí ve vysoce regulovaných prostředích, jako je zdravotní péče a finance.

Uživatelé jsou na vás závislí, aby zajistili, že jejich data neprosakují jinému uživateli. Režim sdíleného zařízení poskytuje užitečné signály, které vaší aplikaci oznamují, že došlo ke změně, kterou byste měli spravovat. Vaše aplikace je zodpovědná za kontrolu stavu uživatele v zařízení při každém použití aplikace, vymazání dat předchozího uživatele. To zahrnuje, pokud je znovu načten z pozadí v multi-tasking. Při změně uživatele byste měli zajistit, aby byla vymazána data předchozího uživatele a aby byla odebrána všechna data uložená v mezipaměti zobrazená v aplikaci. Doporučujeme vždy provést důkladný proces kontroly zabezpečení po přidání funkce režimu sdíleného zařízení do aplikace.

Podrobnosti o tom, jak upravit aplikace tak, aby podporovaly režim sdíleného zařízení, naleznete v části [Další kroky](#next-steps) na konci tohoto článku.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Nasazení zařízení do firstline pracovníků a zapnutí režimu sdíleného zařízení

Jakmile vaše aplikace podporují režim sdíleného zařízení a obsahují požadované změny dat a zabezpečení, můžete je inzerovat jako použitelné pro pracovníky Firstline.

Správci zařízení organizace mohou nasadit svá zařízení a vaše aplikace do svých obchodů a pracovišť prostřednictvím řešení pro správu mobilních zařízení (MDM), jako je Microsoft Intune. Součástí procesu zřizování je označení zařízení jako *sdíleného zařízení*. Správci konfigurují režim sdíleného zařízení nasazením [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) a nastavením režimu sdíleného zařízení pomocí konfiguračních parametrů. Po provedení těchto kroků budou všechny aplikace, které podporují režim sdíleného zařízení, používat aplikaci Microsoft Authenticator ke správě stavu uživatele a poskytování funkcí zabezpečení pro zařízení a organizaci.

## <a name="next-steps"></a>Další kroky

Podporujeme platformy iOS a Android pro režim sdílených zařízení. Projděte si níže uvedenou dokumentaci, kde vaše platforma začne podporovat firstline pracovníky ve vašich aplikacích.

* [Podpora režimu sdíleného zařízení pro iOS](msal-ios-shared-devices.md)
* [Podpora režimu sdíleného zařízení pro Android](msal-android-shared-devices.md)
