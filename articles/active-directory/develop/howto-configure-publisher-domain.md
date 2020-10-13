---
title: Konfigurace domény vydavatele aplikace | Azure
titleSuffix: Microsoft identity platform
description: Naučte se konfigurovat doménu vydavatele aplikace, aby uživatelé věděli, kde se odesílají jejich informace.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: d62328534157a1e2d9e53830be9d6b87e08c9e40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87129266"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Postupy: Konfigurace domény vydavatele aplikace

Doména vydavatele aplikace se zobrazí uživatelům na [výzvu k vyjádření souhlasu aplikace](application-consent-experience.md) a umožní uživatelům, aby věděli, kde jsou odesílány informace. Víceklientské aplikace registrované po 21. května 2019, které nemají doménu vydavatele zobrazeno jako **Neověřeno** Víceklientské aplikace jsou aplikace, které podporují účty mimo jeden adresář organizace. například podporují všechny účty Azure AD nebo podporují všechny účty Azure AD a osobní účty Microsoft.

## <a name="new-applications"></a>Nové aplikace

Když zaregistrujete novou aplikaci, může být doména vydavatele vaší aplikace nastavená na výchozí hodnotu. Hodnota závisí na tom, kde je aplikace zaregistrovaná, zejména jestli je aplikace registrovaná v tenantovi a jestli má tenant ověřené domény tenanta.

Pokud jsou k dispozici domény ověřené klientem, bude doména vydavatele aplikace výchozím nastavením primární ověřená doména tenanta. Pokud neexistují žádné domény ověřené klienty (což je případ, kdy není aplikace registrovaná v tenantovi), bude doména vydavatele aplikace nastavená na hodnotu null.

Následující tabulka shrnuje výchozí chování hodnoty domény vydavatele.  

| Domény ověřené klientem | Výchozí hodnota domény vydavatele |
|-------------------------|----------------------------|
| null | null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| – *. onmicrosoft.com<br/>– domain1.com<br/>-domain2.com (primární) | domain2.com |

Pokud není nastavená doména vydavatele aplikace s více klienty, nebo pokud je nastavená na doménu, která končí na. onmicrosoft.com, zobrazí se výzva k vyjádření souhlasu aplikace na místě domény vydavatele **Neověřeno** .

## <a name="grandfathered-applications"></a>Grandfathered aplikace

Pokud se vaše aplikace zaregistrovala před 21. května 2019, zobrazí se výzva k vyjádření souhlasu vaší aplikace **neověřená** , pokud jste nezadali doménu vydavatele. Doporučujeme nastavit hodnotu domény vydavatele tak, aby uživatelé mohli zobrazit tyto informace na příkazovém řádku pro vyjádření souhlasu vaší aplikace.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurace domény vydavatele pomocí Azure Portal

Pokud chcete nastavit doménu vydavatele vaší aplikace, postupujte podle těchto kroků.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.

1. Pokud je váš účet přítomen ve více než jednom tenantovi služby Azure AD:
   1. V nabídce v pravém horním rohu stránky vyberte svůj profil a pak **Přepněte do adresáře**.
   1. Změňte svou relaci na tenanta Azure AD, ve kterém chcete vytvořit aplikaci.

1. Přejděte na [Azure Active Directory > registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) a vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat.

   Po výběru aplikace se zobrazí stránka **Přehled** aplikace.

1. Na stránce **Přehled** aplikace vyberte část **branding** .

1. Vyhledejte pole **doména vydavatele** a vyberte jednu z následujících možností:

   - Pokud jste ještě nenakonfigurovali doménu, vyberte **nakonfigurovat doménu** .
   - Pokud je doména už nakonfigurovaná, vyberte **aktualizovat doménu** .

Pokud je vaše aplikace registrovaná v tenantovi, zobrazí se dvě karty, ze kterých můžete vybrat: **Vyberte ověřenou doménu** a **ověřte novou doménu**.

Pokud vaše aplikace není registrovaná v tenantovi, zobrazí se jenom možnost ověřit novou doménu pro vaši aplikaci.

### <a name="to-verify-a-new-domain-for-your-app"></a>Ověření nové domény pro vaši aplikaci

1. Vytvořte soubor s názvem `microsoft-identity-association.json` a vložte následující fragment kódu JSON.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Zástupný symbol *{Your-App-ID-tady}* nahraďte ID aplikace (klienta), které odpovídá vaší aplikaci.

1. Hostovat soubor v umístění: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . Nahraďte zástupný symbol *{a-Domain}* , aby odpovídal ověřené doméně.

1. Klikněte na tlačítko **ověřit a uložit doménu** .

### <a name="to-select-a-verified-domain"></a>Výběr ověřené domény

- Pokud má váš tenant ověřené domény, vyberte jednu z domén v rozevíracím seznamu **Vybrat ověřenou doménu** .

>[!Note]
> Očekávala se hlavička Content-Type, která by se měla vrátit `application/json` . Může se zobrazit chyba, jak je uvedeno níže, pokud použijete něco jiného jako `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Důsledky pro výzvu k vyjádření souhlasu aplikace

Konfigurace domény vydavatele má vliv na to, co se uživatelům zobrazí ve výzvě k vyjádření souhlasu s aplikací. Pokud chcete plně pochopit komponenty výzvy k vyjádření souhlasu, přečtěte si téma [vysvětlení prostředí pro vyjádření souhlasu s aplikacemi](application-consent-experience.md).

Následující tabulka popisuje chování pro aplikace vytvořené před 21. května 2019.

![Výzva k zadání souhlasu pro aplikace vytvořené před 21. května 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Chování pro nové aplikace vytvořené po 21. května 2019 bude záviset na doméně vydavatele a na typu aplikace. Následující tabulka popisuje změny, které byste měli očekávat, abyste viděli různé kombinace konfigurací.

![Výzva k zadání souhlasu pro aplikace vytvořené po 21. května 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Důsledky pro identifikátory URI pro přesměrování

Aplikace, které se přihlásí uživatelům pomocí pracovního nebo školního účtu nebo osobních účtů Microsoft ([víceklientské](single-and-multi-tenant-apps.md)), podléhají malým omezením při zadávání identifikátorů URI pro přesměrování.

### <a name="single-root-domain-restriction"></a>Omezení jedné kořenové domény

Pokud je hodnota domény vydavatele pro víceklientské aplikace nastavená na hodnotu null, jsou aplikace omezené na sdílení jedné kořenové domény pro identifikátory URI přesměrování. Například následující kombinace hodnot není povolená, protože kořenová doména, contoso.com, neodpovídá fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Omezení subdomén

Subdomény jsou povoleny, ale je nutné explicitně zaregistrovat kořenovou doménu. Například následující identifikátory URI sdílí jednu kořenovou doménu, kombinace není povolena.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Pokud však vývojář explicitně přidá kořenovou doménu, je kombinace povolena.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Výjimky

V následujících případech nepodléhá omezení jedné kořenové domény:

- Aplikace s jedním klientem nebo aplikace cílené na účty v jednom adresáři
- Použití localhost jako identifikátorů URI přesměrování
- Přesměrování identifikátorů URI s vlastními schématy (jiné než HTTP nebo HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programové nakonfigurování domény vydavatele

V současné době není k dispozici žádné REST API ani podpora prostředí PowerShell pro konfiguraci domény vydavatele prostřednictvím kódu programu.
