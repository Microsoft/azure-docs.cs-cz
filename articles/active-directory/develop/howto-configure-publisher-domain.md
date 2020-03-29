---
title: Konfigurace domény vydavatele aplikace | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak nakonfigurovat doménu vydavatele aplikace tak, aby uživatelé věděli, kam se jejich informace posílají.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697128"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Postup: Konfigurace domény vydavatele aplikace

Doména vydavatele aplikace se uživatelům zobrazí na výzvu k [souhlasu aplikace,](application-consent-experience.md) aby uživatelé věděli, kam jsou odesílány jejich informace. Víceklientské aplikace registrované po **21.** Víceklientské aplikace jsou aplikace, které podporují účty mimo jeden organizační adresář; například podporovat všechny účty Azure AD nebo podporovat všechny účty Azure AD a osobní účty Microsoft.

## <a name="new-applications"></a>Nové aplikace

Když zaregistrujete novou aplikaci, může být doména vydavatele vaší aplikace nastavena na výchozí hodnotu. Hodnota závisí na tom, kde je aplikace registrována, zejména na tom, jestli je aplikace registrovaná v tenantovi a jestli má klient ověřené domény tenanta.

Pokud existují domény ověřené klientem, bude doména vydavatele aplikace ve výchozím nastavení nastavena na primární ověřenou doménu klienta. Pokud neexistují žádné domény ověřené klientem (což je případ, kdy aplikace není registrována v tenantovi), bude doména vydavatele aplikace nastavena na hodnotu null.

Následující tabulka shrnuje výchozí chování hodnoty domény vydavatele.  

| Domény ověřené klientem | Výchozí hodnota domény vydavatele |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primární) | domain2.com |

Pokud není nastavena doména vydavatele víceklientské aplikace nebo pokud je nastavená na doménu, která končí na .onmicrosoft.com, zobrazí se v příkazu souhlasu aplikace **neověřená** místo domény vydavatele.

## <a name="grandfathered-applications"></a>Děděné aplikace

Pokud byla vaše aplikace zaregistrována před 21. **unverified** Doporučujeme nastavit hodnotu domény vydavatele, aby se uživatelům tyto informace mohly zobrazit na výzvu k souhlasu vaší aplikace.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurace domény vydavatele pomocí webu Azure Portal

Pokud chcete nastavit doménu vydavatele aplikace, postupujte takto.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.

1. Pokud je váš účet k dispozici ve více než jednom tenantovi Azure AD:
   1. Vyberte svůj profil z nabídky v pravém horním rohu stránky a potom **přepněte adresář**.
   1. Změňte svou relaci na klienta Azure AD, kde chcete vytvořit aplikaci.

1. Přejděte na [Azure Active Directory > app registrace](https://go.microsoft.com/fwlink/?linkid=2083908) najít a vybrat aplikaci, kterou chcete nakonfigurovat.

   Jakmile aplikaci vyberete, zobrazí se stránka **Přehled** aplikace.

1. Na stránce **Přehled** aplikace vyberte oddíl **Branding.**

1. Vyhledejte pole **domény aplikace Publisher** a vyberte jednu z následujících možností:

   - Vyberte **Konfigurovat doménu,** pokud jste doménu ještě nenakonfigurovali.
   - Pokud již byla doména nakonfigurována, vyberte **aktualizovat doménu.**

Pokud je vaše aplikace registrovaná v tenantovi, zobrazí se dvě karty, ze kterých můžete vybírat: **Vyberte ověřenou doménu** a **Ověřte novou doménu**.

Pokud vaše aplikace není registrovaná v tenantovi, zobrazí se jenom možnost ověřit novou doménu pro vaši aplikaci.

### <a name="to-verify-a-new-domain-for-your-app"></a>Ověření nové domény pro vaši aplikaci

1. Vytvořte soubor `microsoft-identity-association.json` s názvem a vložte následující fragment kódu JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Nahraďte zástupný symbol *{YOUR-APP-ID-HERE}* ID aplikace (klienta), které odpovídá vaší aplikaci.

1. Hostovat soubor `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`na adrese: . Nahraďte zástupný symbol *{YOUR-DOMAIN-HERE},* aby odpovídal ověřené doméně.

1. Klepněte na tlačítko **Ověřit a uložit doménu.**

### <a name="to-select-a-verified-domain"></a>Výběr ověřené domény

- Pokud váš tenant ověřil domény, vyberte jednu z domén z rozevíracího **souboru Vybrat ověřenou doménu.**

>[!Note]
> Očekávané hlavičky typu Content-Type, které `application/json`by měly být vráceny, je . Můžete se dostat chybu, jak je uvedeno níže, pokud používáte něco jiného, jako je`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Důsledky výzvy k souhlasu aplikace

Konfigurace domény vydavatele má vliv na to, co uživatelé uvidí na výzvu k souhlasu aplikace. Chcete-li plně porozumět součástem výzvy k souhlasu, [přečtěte si informace o principu zkušeností se souhlasem aplikace](application-consent-experience.md).

Následující tabulka popisuje chování pro aplikace vytvořené před 21 května 2019.

![Výzva k souhlasu pro aplikace vytvořené před 21.](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Chování pro nové aplikace vytvořené po 21 května 2019 bude záviset na doméně vydavatele a typu aplikace. Následující tabulka popisuje změny, které byste měli očekávat s různými kombinacemi konfigurací.

![Výzva k souhlasu pro aplikace vytvořené po květnu 21, 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Důsledky pro přesměrování identifikátorů URI

Aplikace, které přihlašují uživatele pomocí libovolného pracovního nebo školního účtu nebo osobních účtů Microsoft[(víceklientů),](single-and-multi-tenant-apps.md)podléhají při zadávání identifikátorů URI přesměrování.

### <a name="single-root-domain-restriction"></a>Omezení jedné kořenové domény

Pokud je hodnota domény vydavatele pro aplikace s více klienty nastavená na hodnotu null, jsou aplikace omezeny na sdílení jedné kořenové domény pro identifikátory URI přesměrování. Například následující kombinace hodnot není povolena, protože kořenová doména, contoso.com, neodpovídá fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Omezení subdomény

Subdomény jsou povoleny, ale je nutné explicitně zaregistrovat kořenovou doménu. Například zatímco následující identifikátory URI sdílejí jednu kořenovou doménu, kombinace není povolena.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Pokud však vývojář explicitně přidá kořenovou doménu, je tato kombinace povolena.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Výjimky

Následující případy nepodléhají omezení jedné kořenové domény:

- Aplikace s jedním tenantem nebo aplikace, které cílí na účty v jednom adresáři
- Použití localhost jako přesměrování identifikátorů URI
- Přesměrování identifikátorů URI pomocí vlastních schémat (bez protokolu HTTP nebo HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programová konfigurace domény vydavatele

V současné době neexistuje žádné rozhraní REST API nebo podpora prostředí PowerShell pro programovou konfiguraci domény vydavatele.
