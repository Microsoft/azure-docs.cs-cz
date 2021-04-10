---
title: Přehled režimu sdíleného zařízení
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si o režimu sdílených zařízení, který umožňuje sdílení zařízení pro prvotní pracovníky.
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
ms.openlocfilehash: cf8869002fb3e0170331709af3da5b971a098740
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612394"
---
# <a name="overview-of-shared-device-mode"></a>Přehled režimu sdíleného zařízení

Režim sdíleného zařízení je funkce Azure Active Directory, která umožňuje sestavovat aplikace, které podporují prvotní pracovní procesy, a povolit režim sdíleného zařízení na zařízeních, která jsou do nich nasazená.

>[!IMPORTANT]
> Režim sdíleného zařízení pro iOS [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>Co jsou prvotní pracovní procesy?

Prvotní pracovní zaměstnanci představují maloobchodníky, údržbu a agenty terénů, lékařské pracovníky a další uživatele, kteří nesedí před počítačem, nebo nepoužívají firemní e-mail ke spolupráci. V následujících částech jsou uvedeny aspekty a výzvy pro podporu prvotních pracovních procesů, za kterými následuje Úvod k funkcím poskytovaným společností Microsoft, které umožňují vaší aplikaci využívat pracovní procesy prvotní organizace.

### <a name="challenges-of-supporting-frontline-workers"></a>Výzvy k podpoře prvotní pracovníků

Povolení pracovních postupů pracovních procesů prvotní zahrnuje výzvy, které obvykle neprezentují běžní pracovníci s informacemi. Takové výzvy můžou zahrnovat míru vysokého obratu a méně znalostí s nástroji pro zvýšení produktivity v rámci organizace. Organizace přijímají různé strategie, aby prvotní své pracovní procesy. Někteří si přijali strategii BYOD (Přineste si vlastní zařízení), ve které jejich zaměstnanci používají obchodní aplikace na svém osobním telefonu, zatímco jiní jejich zaměstnanci poskytují sdílená zařízení, jako jsou iPady nebo Android tablety.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Podpora více uživatelů na zařízeních určených pro jednoho uživatele

Vzhledem k tomu, že mobilní zařízení se systémem iOS nebo Androidem byla navržena pro jednotlivé uživatele, většina aplikací optimalizuje své prostředí pro použití jedním uživatelem. Součástí tohoto optimalizovaného prostředí je povolení jednotného přihlašování napříč aplikacemi a udržování uživatelů přihlášených na svém zařízení. Když uživatel z aplikace odebere svůj účet, aplikace to obvykle nebere v úvahu událost související se zabezpečením. Mnoho aplikací může pro rychlé přihlašování zůstat i přihlašovací údaje uživatele. I když jste odstranili aplikaci z mobilního zařízení a pak ji přeinstalovali, můžete ji dokonce sami napřed nainstalovat, jenom když jste si zjistili, že jste pořád přihlášení.

### <a name="global-sign-in-and-sign-out-sso"></a>Globální přihlášení a odhlášení (SSO)

Aby zaměstnanci organizace mohli používat své aplikace v rámci fondu zařízení, která jsou sdílená těmito zaměstnanci, potřebují vývojáři povolit opačné možnosti. Zaměstnanci by si měli být schopni vybrat zařízení z fondu a provést jedno gesto pro jeho posunutí po dobu trvání jejich posunu. Na konci jejich posunu by mělo být možné provést další gesto k tomu, aby se odhlásily globálně na zařízení a odebraly se všechny jejich osobní a firemní informace, aby se mohly vrátit do fondu zařízení. Pokud se navíc zaměstnanec zapomene odhlásit, zařízení by se mělo automaticky odhlásit na konci jejich posunu a/nebo po určité době nečinnosti.

Azure Active Directory tyto scénáře povoluje s funkcí nazvanou **režim sdíleného zařízení**.

## <a name="introducing-shared-device-mode"></a>Představujeme režim sdíleného zařízení

Jak už bylo zmíněno, režim sdíleného zařízení je funkce Azure Active Directory, která vám umožní:

* Sestavování aplikací, které podporují prvotní pracovní procesy
* Nasazení zařízení do prvotní pracovníků a zapnutí režimu sdíleného zařízení

### <a name="build-applications-that-support-frontline-workers"></a>Sestavování aplikací, které podporují prvotní pracovní procesy

V aplikacích můžete podporovat prvotní pracovní procesy pomocí knihovny Microsoft Authentication Library (MSAL) a [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) a povolit tak stav zařízení s názvem *režim sdíleného zařízení*. Když je zařízení v režimu sdíleného zařízení, Microsoft poskytuje vaší aplikaci informace, které jim umožní změnit její chování na základě stavu uživatele v zařízení a chránit data uživatelů.

Podporované funkce:

* **Přihlaste se k uživatelskému zařízení v rámci** libovolné podporované aplikace.
* **Odhlaste si uživatelské zařízení napříč** všemi podporovanými aplikacemi.
* **Dotaz na stav zařízení** , abyste zjistili, jestli je vaše aplikace na zařízení, které je v režimu sdíleného zařízení.
* **Dotaz na stav zařízení uživatele** na zařízení, abyste zjistili, jestli se od posledního použití vaší aplikace něco změnilo.

Podpora režimu sdíleného zařízení by se měla považovat za vylepšení zabezpečení a upgrade funkcí vaší aplikace a může přispět ke zvýšení jeho přijetí ve vysoce regulovaných prostředích, jako je zdravotní péče a finance.

Vaši uživatelé závisí na tom, abyste zajistili, že jejich data nebudou nevrácená jiným uživatelem. Sdílení režimu zařízení poskytuje užitečné signály k označení vaší aplikace, že došlo ke změně, kterou byste měli spravovat. Vaše aplikace zodpovídá za kontrolu stavu uživatele v zařízení při každém použití aplikace a vymazání dat předchozích uživatelů. To zahrnuje, pokud se znovu načte z pozadí při více úlohách. Při změně uživatele byste měli zkontrolovat, že se data předchozího uživatele vymažou a že se z aplikace odeberou všechna data uložená v mezipaměti. Po přidání funkce režimu sdíleného zařízení do aplikace doporučujeme vždy provést důkladný postup kontroly zabezpečení.

Podrobnosti o tom, jak upravit aplikace pro podporu režimu sdíleného zařízení, najdete v části [Další kroky](#next-steps) na konci tohoto článku.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>Nasazení zařízení do prvotní pracovníků a zapnutí režimu sdíleného zařízení

Jakmile vaše aplikace podporují režim sdíleného zařízení a budou zahrnovat požadovaná data a změny zabezpečení, můžete je inzerovat tak, aby je mohli používat prvotní pracovníci.

Správci zařízení organizace můžou svoje zařízení a své aplikace nasadit do úložišť a na pracoviště prostřednictvím řešení pro správu mobilních zařízení (MDM), jako je Microsoft Intune. Součástí procesu zřizování je označení zařízení jako *sdíleného zařízení*. Správci konfigurují režim sdíleného zařízení nasazením [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) a nastavením režimu sdíleného zařízení prostřednictvím parametrů konfigurace. Po provedení těchto kroků budou všechny aplikace, které podporují režim sdíleného zařízení, používat aplikaci Microsoft Authenticator ke správě stavu uživatele a poskytování funkcí zabezpečení pro zařízení a organizaci.

## <a name="next-steps"></a>Další kroky

Podporujeme platformy iOS a Android pro režim sdíleného zařízení. Projděte si dokumentaci níže pro vaši platformu, abyste mohli začít podporovat prvotní pracovní procesy ve vašich aplikacích.

* [Podpora režimu sdíleného zařízení pro iOS](msal-ios-shared-devices.md)
* [Podpora režimu sdíleného zařízení pro Android](msal-android-shared-devices.md)
