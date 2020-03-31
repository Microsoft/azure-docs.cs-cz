---
title: Režim sdíleného zařízení pro zařízení se systémem Android | Azure
description: Informace o režimu sdíleného zařízení, který umožňuje pracovníkům první linie sdílet zařízení Android
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085619"
---
# <a name="shared-device-mode-for-android-devices"></a>Režim sdíleného zařízení pro zařízení s Androidem

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pracovníci firstline, jako jsou maloobchodní partneři, členové letových posádek a pracovníci terénních služeb, často používají ke své práci sdílené mobilní zařízení. To se stává problematickým, když začnou sdílet hesla nebo pinová čísla pro přístup k zákaznickým a obchodním datům na sdíleném zařízení.

Režim sdíleného zařízení umožňuje konfigurovat zařízení Android tak, aby jej mohlo snadno sdílet více zaměstnanců. Zaměstnanci se mohou rychle přihlásit a získat přístup k informacím o zákaznících. Po dokončení směny nebo úkolu se mohou odhlásit ze zařízení a bude okamžitě připraveno pro dalšího zaměstnance.

Režim sdíleného zařízení také poskytuje správu zařízení podporovanou identitou společnosti Microsoft.

Pokud chcete vytvořit aplikaci pro režim sdíleného zařízení, vývojáři a správci cloudových zařízení spolupracují:

- Vývojáři píší aplikaci s jedním účtem (aplikace s více `"shared_device_mode_supported": true` s is mařivé matné služby nejsou v režimu sdíleného zařízení podporované), přidají se do konfigurace aplikace a napíší kód pro zpracování věcí, jako je odhlášení ze sdíleného zařízení.
- Správci zařízení připraví zařízení ke sdílení instalací ověřovací aplikace a nastavením zařízení do sdíleného režimu pomocí ověřovací aplikace. Pouze uživatelé, kteří jsou v roli [Správce cloudových zařízení,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) mohou zařízení převést do sdíleného režimu pomocí [aplikace Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Členství v organizačních rolích na webu Azure Portal můžete nakonfigurovat prostřednictvím: Role **Služby Azure Active Directory** > a**Správce cloudových zařízení****Správci správců služby Administrators** > .

 Tento článek se zaměřuje především na to, co by vývojáři měli přemýšlet.

## <a name="single-vs-multiple-account-applications"></a>Aplikace s jedním vs více účtů

Aplikace napsané pomocí sady Microsoft Authentication Library SDK (MSAL) mohou spravovat jeden účet nebo více účtů. Podrobnosti viz [režim jednoho účtu nebo režim více účtů](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Funkce platformy identit microsoftu, které jsou k dispozici pro vaši aplikaci, se liší v závislosti na tom, jestli je aplikace spuštěná v režimu s jedním účtem nebo v režimu s více svícením.

**Aplikace v režimu sdíleného zařízení fungují pouze v režimu s jedním účtem**.

> [!IMPORTANT]
> Aplikace, které podporují pouze režim více účtů, nelze spustit na sdíleném zařízení. Pokud zaměstnanec načte aplikaci, která nepodporuje režim s jedním účtem, nespustí se na sdíleném zařízení.
>
> Aplikace napsané před vydáním sady MSAL SDK byly spuštěny v režimu s více účtůmi a před spuštěním na zařízení se sdíleným režimem je nutné je aktualizovat tak, aby podporovaly režim s jedním účtem.

**Podpora jak s jedním, tak s více účty**

Vaše aplikace může být vytvořena tak, aby podporovala běh na osobních i sdílených zařízeních. Pokud vaše aplikace aktuálně podporuje více účtů a chcete podporovat režim sdíleného zařízení, přidejte podporu pro režim jednoho účtu.

Můžete také chtít, aby vaše aplikace změnila své chování v závislosti na typu zařízení, na kterém běží. Slouží `ISingleAccountPublicClientApplication.isSharedDevice()` k určení, kdy se má spustit v režimu s jedním účtem.

Existují dvě různá rozhraní, které představují typ zařízení, na kterých je vaše aplikace. Když požadujete instanci aplikace z továrny aplikace MSAL, je automaticky k dispozici správný aplikační objekt.

Následující objektový model ilustruje typ objektu, který můžete obdržet, a jeho prostředky v kontextu sdíleného zařízení:

![model dědičnosti aplikace veřejného klienta](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Budete muset udělat kontrolu typu a obsazení na příslušné rozhraní, když se dostanete objekt. `PublicClientApplication` Následující kód kontroluje režim více účtů nebo režim jednoho účtu a přetypování aplikačního objektu odpovídajícím způsobem:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

V závislosti na tom, jestli je aplikace spuštěná na sdíleném nebo osobním zařízení, platí následující rozdíly:

|  | Zařízení sdíleného režimu  | Osobní zařízení |
|---------|---------|---------|
| **Účty**     | Jeden účet | Více účtů |
| **Přihlášení** | Globální | Globální |
| **Odhlašovací** | Globální | Každá aplikace může řídit, pokud je odhlášení místní do aplikace nebo pro rodinu aplikací. |
| **Podporované typy účtu** | Pouze pracovní účty | Podporované osobní a pracovní účty  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Proč můžete chtít podporovat pouze režim jednoho účtu

Pokud píšete aplikaci, která se bude používat pouze pro pracovníky první linie, kteří používají sdílené zařízení, doporučujeme napsat aplikaci pouze pro podporu režimu s jedním účtem. To zahrnuje většinu aplikací, které jsou zaměřené na úkoly, jako jsou aplikace lékařských záznamů, fakturační aplikace a většina obchodních aplikací. Vývoj zjednodušuje pouze podpora režimu s jedním účtem, protože nebudete muset implementovat další funkce, které jsou součástí aplikací s více účtůmi.

## <a name="what-happens-when-the-device-mode-changes"></a>Co se stane, když se změní režim zařízení

Pokud je aplikace spuštěna v režimu s více účty a správce přepne zařízení do režimu sdíleného zařízení, všechny účty v zařízení jsou vymazány z aplikace a aplikace přechází do režimu jednoho účtu.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Odhlášení sdíleného zařízení a celkový životní cyklus aplikace

Když se uživatel odhlásí, budete muset podniknout kroky k ochraně soukromí a dat uživatele. Pokud například vytváříte aplikaci pro lékařské záznamy, budete se chtít ujistit, že když se uživatel odhlásí z dříve zobrazených záznamů o pacientech, budou vymazány. Vaše žádost musí být připravena pro toto a zkontrolujte pokaždé, když vstoupí do popředí.

Když vaše aplikace používá MSAL k odhlášení uživatele v aplikaci spuštěné na zařízení, které je ve sdíleném režimu, přihlašovací účet a tokeny uložené v mezipaměti se odeberou z aplikace i zařízení.

Následující diagram znázorňuje celkový životní cyklus aplikace a běžné události, které mohou nastat při spuštění aplikace. Diagram popisuje od okamžiku spuštění aktivity, přihlášení a odhlášení účtu a způsob, jakým události, jako je pozastavení, obnovení a zastavení aktivity, zapadají.

![Životní cyklus aplikace sdíleného zařízení](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Další kroky

Zkuste [použít režim sdíleného zařízení v](tutorial-v2-shared-device-mode.md) kurzu aplikace pro Android, který ukazuje, jak spustit pracovní aplikaci firstline na sdíleném režimu zařízení Android.
