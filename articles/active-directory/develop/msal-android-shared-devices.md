---
title: Režim sdíleného zařízení pro zařízení s Androidem
titleSuffix: Microsoft identity platform | Azure
description: Naučte se povolit režim sdíleného zařízení, aby prvotní pracovníci mohli sdílet zařízení s Androidem.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: 005f69473fa238d56cf7d582a8af4000166d6939
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612428"
---
# <a name="shared-device-mode-for-android-devices"></a>Režim sdíleného zařízení pro zařízení s Androidem

Prvotní pracovní procesy, jako jsou maloobchodníci, členové posádky a pracovníci polních služeb často používají ke své práci sdílené mobilní zařízení. To bude problematické, když začnou sdílet hesla nebo čísla PIN pro přístup k datům zákazníků a podnikových dat na sdíleném zařízení.

Režim sdíleného zařízení umožňuje nakonfigurovat zařízení se systémem Android tak, aby ho bylo možné snadno sdílet s více zaměstnanci. Zaměstnanci se můžou rychle přihlašovat a přistupovat k informacím o zákaznících. Až se dokončí s jejich přesunutím nebo úlohou, můžou se odhlásit ze zařízení a okamžitě se připraví k používání dalšího zaměstnance.

Režim sdíleného zařízení taky poskytuje zpětnou správu v rámci identity Microsoftu.

Pokud chcete vytvořit sdílenou aplikaci v režimu zařízení, vývojáři a správci cloudových zařízení spolupracují:

- Vývojáři napsané aplikace s jedním účtem (aplikace s více účty se v režimu sdíleného zařízení nepodporují), přidají `"shared_device_mode_supported": true` do konfigurace aplikace a napíší kód, který bude zpracovávat věci, jako je třeba přihlášení ke sdílenému zařízení.
- Správci zařízení připraví zařízení pro sdílení instalací ověřovací aplikace a nastavením zařízení do sdíleného režimu pomocí ověřovací aplikace. Pouze uživatelé, kteří jsou v roli [správce cloudového zařízení](../roles/permissions-reference.md#cloud-device-administrator) , mohou do sdíleného režimu umístit zařízení pomocí [ověřovací aplikace](../user-help/user-help-auth-app-overview.md). Členství v organizačních rolích můžete nakonfigurovat v Azure Portal prostřednictvím: **Azure Active Directory**  >  **role a správci**  >  **správce cloudového zařízení**.

 Tento článek se zaměřuje především na to, co by si vývojáři měli představit.

## <a name="single-vs-multiple-account-applications"></a>Jednotlivé aplikace s více účty vs.

Aplikace napsané pomocí sady Microsoft MSAL Library SDK () můžou spravovat jeden nebo víc účtů. Podrobnosti najdete v tématu [režim jednoho účtu nebo režim vícenásobného účtu](single-multi-account.md). Funkce platformy Microsoft identity, které jsou dostupné pro vaši aplikaci, se liší v závislosti na tom, jestli je aplikace spuštěná v režimu jednoho účtu nebo v režimu vícenásobného účtu.

**Aplikace v režimu sdíleného zařízení fungují pouze v režimu s jedním účtem**.

> [!IMPORTANT]
> Aplikace, které podporují jenom režim více účtů, nejde na sdíleném zařízení spustit. Pokud zaměstnanec načte aplikaci, která nepodporuje režim jednoho účtu, nespustí se na sdíleném zařízení.
>
> Aplikace napsané před vydáním sady MSAL SDK byly vydány v režimu více účtů a je nutné je aktualizovat tak, aby podporovaly režim jednoho účtu, než bude možné spustit na zařízení se sdíleným režimem.

**Podpora jednoho účtu i několika účtů**

Vaše aplikace může být sestavená tak, aby podporovala provoz na osobních zařízeních i ve sdílených zařízeních. Pokud vaše aplikace aktuálně podporuje víc účtů a chcete podporovat režim sdíleného zařízení, přidejte podporu pro režim jednoho účtu.

V závislosti na typu zařízení, na kterém je spuštěný, můžete také chtít, aby vaše aplikace změnila jeho chování. Použijte `ISingleAccountPublicClientApplication.isSharedDevice()` k určení, kdy se má spustit v režimu jednoho účtu.

Existují dvě různá rozhraní, která představují typ zařízení, ve kterém je vaše aplikace zapnutá. Když vyžádáte instanci aplikace z objektu pro vytváření aplikací MSAL, je automaticky poskytnut správný objekt aplikace.

Následující objektový model znázorňuje typ objektu, který můžete obdržet, a to znamená v kontextu sdíleného zařízení:

![model dědičnosti aplikace veřejného klienta](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Při získávání objektu budete muset provést kontrolu typu a přetypovat na příslušné rozhraní `PublicClientApplication` . Následující kód kontroluje režim více účtů nebo režim jednoho účtu a odpovídajícím způsobem přetypování objektu aplikace:

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

V závislosti na tom, jestli je vaše aplikace spuštěná na sdíleném nebo osobním zařízení, platí následující rozdíly:

|  | Zařízení sdíleného režimu  | Osobní zařízení |
|---------|---------|---------|
| **Účty**     | Jeden účet | Několik účtů |
| **Přihlášení** | Globální | Globální |
| **Odhlášení** | Globální | Každá aplikace může řídit, jestli je odhlášení místní pro aplikaci nebo pro rodinu aplikací. |
| **Podporované typy účtu** | Pouze pracovní účty | Podpora osobních a pracovních účtů  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Proč je možné, že budete chtít podporovat jenom režim jednoho účtu

Pokud vytváříte aplikaci, která se bude používat jenom pro pracovní pracovníky prvotní pomocí sdíleného zařízení, doporučujeme napsat aplikaci tak, aby podporovala pouze režim jednoho účtu. To zahrnuje většinu aplikací, které jsou zaměřeny na úlohy, jako jsou lékařské záznamy aplikací, fakturační aplikace a většina obchodních aplikací. Pouze podpora režimu jednoho účtu zjednodušuje vývoj, protože nebudete muset implementovat další funkce, které jsou součástí aplikací s více účty.

## <a name="what-happens-when-the-device-mode-changes"></a>Co se stane, když se změní režim zařízení

Pokud vaše aplikace běží v režimu více účtů a Správce zařízení vloží do režimu sdíleného zařízení, všechny účty v zařízení se z aplikace vymažou a aplikace přejde do režimu jednoho účtu.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Přihlášení ke sdílenému zařízení a celkový životní cyklus aplikace

Když se uživatel odhlásí, budete muset provést akci, která ochrání soukromí a data uživatele. Pokud například vytváříte aplikaci se zdravotnickými záznamy, budete chtít zajistit, aby se v případě, že uživatel odhlásí dříve zobrazené záznamy o pacientech, zrušili. Vaše aplikace musí být připravená pro tuto kontrolu a kontrolovat pokaždé, když vstoupí do popředí.

Když vaše aplikace používá MSAL k odhlášení uživatele v aplikaci běžící na zařízení, které je ve sdíleném režimu, přihlášený účet a tokeny uložené v mezipaměti se odeberou z aplikace i ze zařízení.

Následující diagram znázorňuje celkový životní cyklus aplikace a běžné události, které mohou nastat při spuštění aplikace. Diagram se zabývá tím, kdy se spustí aktivita, přihlašuje se a Odhlašujete k účtu a jakým způsobem se připadá na události, jako je pozastavení, obnovení a zastavování aktivity.

![Životní cyklus aplikace sdíleného zařízení](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si kurz [použití sdíleného zařízení v kurzu aplikace pro Android](tutorial-v2-shared-device-mode.md) , který ukazuje, jak spustit aplikaci prvotní Worker na zařízení s Androidem ve sdíleném režimu.
