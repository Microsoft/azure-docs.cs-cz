---
title: Nakonfigurovat doménu vydavatele aplikace. | Azure
description: Zjistěte, jak nakonfigurovat doména vydavatele aplikace chcete uživatele informovat o tom, kde se informace o jejich odeslání.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47075f9e18b299341a98983ffb8a47389fd7063
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540247"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Postup: Nakonfigurovat doménu vydavatele aplikace (Preview)

Doména vydavatele aplikace se zobrazí uživatelům na [výzva k povolení spuštění aplikace](application-consent-experience.md) chcete uživatele informovat o tom, kde se informace o jejich odeslání. Aplikace s více tenanty, které jsou registrovány po 21. května 2019, které nemají doménu vydavatele se zobrazují jako **neověřené**. Víceklientské aplikace jsou aplikace, které podporují účty mimo jedné organizační adresáři. například všechny účty služby Azure AD nebo podporují všechny účty služby Azure AD a osobní účty Microsoft.

## <a name="new-applications"></a>Nové aplikace

Při registraci nové aplikace, doména vydavatele aplikace může být nastaven na výchozí hodnotu. Hodnota závisí na kde registraci aplikace, zvlášť jestli registraci aplikace v tenantovi a určuje, zda má klient tenanta ověřené doméně.

Pokud je ověření klienta domén, doména vydavatele aplikace bude ve výchozím nastavení primární ověřenou doménu tenanta. Pokud se žádný klient ověřené domény (což je případ, když aplikace není registrována v tenantovi), nastaví se doména vydavatele aplikace na hodnotu null.

Následující tabulka shrnuje chování výchozí hodnoty domény vydavatele.  

| Ověření klienta domény | Výchozí hodnota doména vydavatele |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| -*. onmicrosoft.com<br/>-Doménu1.cz<br/>-Doménu2.cz (primární) | domain2.com |

Pokud doména vydavatele aplikace s více tenanty není nastavená nebo pokud je nastavena na doméně, která končí na. onmicrosoft.com, zobrazí se výzva k povolení spuštění aplikace **neověřené** místo doména vydavatele.

## <a name="grandfathered-applications"></a>Grandfathered aplikací

Pokud vaše aplikace byla zaregistrována před 21. května 2019 nezobrazí výzva k povolení spuštění vaší aplikace **neověřené** Pokud jste nenastavili doménu vydavatele. Doporučujeme nastavit vydavatele hodnota domény tak, aby uživatelé uvidí tyto informace na výzva k povolení spuštění vaší aplikace.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurace vydavatele domény pomocí webu Azure portal

Nastavení domény vydavatele této aplikace, postupujte podle těchto kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.

1. Pokud je váš účet součástí více než jednoho tenanta Azure AD:
   1. Z nabídky v pravém horním rohu stránky vyberte váš profil a potom **přepnout adresář**.
   1. Změňte relaci na tenanta Azure AD, kde chcete vytvořit aplikaci.

1. Přejděte do [Azure Active Directory > Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) najděte a vyberte aplikaci, kterou chcete konfigurovat.

   Jakmile vyberete aplikace, zobrazí se vám aplikace **přehled** stránky.

1. Z aplikace **přehled** stránky, vyberte **značky** oddílu.

1. Najít **doména vydavatele** pole a vyberte jednu z následujících možností:

   - Vyberte **konfigurace domény** Pokud už jste neprovedli konfiguraci domény.
   - Vyberte **aktualizační doména** Pokud domény je již nakonfigurován.

Pokud vaše aplikace je registrován v tenantovi, uvidíte dvě karty můžete vybírat z: **Vyberte ověřenou doménu** a **ověření nové domény**.

Pokud vaše aplikace není registrován v tenantovi, uvidíte pouze možnost ověřit nové domény pro vaši aplikaci.

### <a name="to-verify-a-new-domain-for-your-app"></a>Chcete-li ověřit nové domény pro vaši aplikaci

1. Vytvořte soubor s názvem `microsoft-identity-association.json` a vložte následující fragment kódu JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Nahraďte zástupný text *{YOUR-APP-ID-TADY}* s ID aplikace (klient), která odpovídá vaší aplikace.

1. Hostování v souboru: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Nahraďte zástupný text *{YOUR-ZDEJSI-KORENOVA}* tak, aby odpovídaly ověřenou doménu.

1. Klikněte na tlačítko **ověřte a uložte domény** tlačítko.

### <a name="to-select-a-verified-domain"></a>Vyberte ověřenou doménu

- Pokud váš tenant má ověřené doméně, vyberte jednu z domén z **vyberte ověřenou doménu** rozevíracího seznamu.

## <a name="implications-on-the-app-consent-prompt"></a>Důsledky pro aplikace výzva k povolení spuštění

Doména vydavatele konfigurace má dopad na co uživatelé uvidí na výzva k povolení spuštění aplikace. Abyste úplně pochopili součásti výzva k povolení spuštění, naleznete v tématu [Principy souhlasu s aplikací vyskytne](application-consent-experience.md).

Následující tabulka popisuje chování pro aplikace vytvořené před 21. května 2019.

![Výzva k povolení spuštění pro aplikace vytvořené před 21. května 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Chování pro nové aplikace vytvořené po 21. května 2019 bude záviset na doméně vydavatele a na typu aplikace. Následující tabulka popisuje změny, které by se měl zobrazit jinou kombinaci parametrů konfigurace.

![Výzva k povolení spuštění pro aplikace vytvořené po 21. května 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Důsledky pro identifikátory URI pro přesměrování

Aplikace, které se přihlásit uživatele pomocí jakékoli pracovní nebo školní účet nebo osobní účty Microsoft ([víceklientské](single-and-multi-tenant-apps.md)) jsou v souladu s několika omezeními při zadávání identifikátory URI pro přesměrování.

### <a name="single-root-domain-restriction"></a>Omezení domény jeden kořenový

Pokud hodnota doméně vydavatele pro aplikace s více tenanty nastavená na hodnotu null, aplikace jsou omezeny na sdílení jedné kořenovou doménu pro identifikátory URI pro přesměrování. Například následující kombinace hodnot není povolená, protože kořenová doména, contoso.com, fabrikam.com se neshoduje.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Omezení subdomény

Jsou povoleny subdomény, ale je nutné explicitně zaregistrovat kořenové domény. Například následující identifikátory URI sdílet jeden kořenové domény, kombinace se nepovoluje.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Pokud vývojář přidá explicitně kořenové domény, je však povoleno kombinace.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Výjimky

Následujících případech nejsou v souladu s jednou kořenovou doménu omezení:

- Aplikace pro jednoho tenanta nebo aplikace, které cílí účty v jednom adresáři
- Použití místního hostitele jako identifikátory URI pro přesměrování
- Identifikátory URI přesměrování s vlastní schémata (jiným protokolem než HTTP nebo HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Doména vydavatele konfigurace prostřednictvím kódu programu

V současné době se nepodporuje rozhraní REST API nebo Powershellu ke konfiguraci doména vydavatele prostřednictvím kódu programu.
