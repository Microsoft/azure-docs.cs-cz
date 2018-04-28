---
title: Synchronizace hosta uživatelé uživatelských účtů, které používají e-mailu pro přihlášení k Azure | Microsoft Docs
description: Tento článek vysvětluje, jak synchronizovat hosta uživatelské účty, které používají alternativní ID přihlášení do aplikace.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: f450747cac06a416950efc6c37c1058c1cb40775
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Synchronizace uživatelských účtů hosta, které používají e-mailu pro přihlášení (Preview)

>[!NOTE]
> Tato funkce je aktuálně ve verzi public preview.

V následujícím scénáři adres situaci, kdy můžete mít externí uživatele v místní AD prostředí, například partnery, kteří používají alternativní metoda přihlašování.

V předchozím příkladu Marie Morin funguje pro společnost Fabrikam a uživatel má následující e-mailová adresa: nmorin@fabrikam.com. Jana potřebuje přístup k určitým aplikacím, které Contoso má Marie je partnera s Contoso. Contoso vytvořil účet pro Marie a má směrované Marie svůj e-mailovou adresu používat k přihlášení do aplikace.

Pro své místní aplikace tento scénář pracuje skvěle. Ale nyní Contoso přechází tyto aplikace do cloudu a chce mít stejné prostředí pro jejich partnery.

Tento scénář řeší tuto situaci.


## <a name="pre-requisites-and-assumptions"></a>Požadavky a předpoklady
Tato část obsahuje seznam požadavky a předpoklady, které potřebujete znát před pokusem o nastavení tento scénář.

### <a name="pre-requisites"></a>Požadavky
- Přihlašovací údaje globálního správce pro konfiguraci Azure AD Connect, ověření domén a nakonfigurovat nastavení domény federation
- Azure AD Connect verze 1.1.524.0 nebo vyšší
- Nastavení cloudu UPN externích uživatelů ověřené domény (Příklad: bmcontoso.com).
- Federační služba ověření externí uživatele. Pokud používáte službu AD FS, musí být 2012 R2 nebo vyšší
- Prostředí PowerShell MSOL v1.1 je nainstalována na počítače, a ověřte nastavení federace. Další informace najdete v tématu [Azure Active Directory (MSOnline)](https://docs.microsoft.com/en-us/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Předpoklady 
- Azure AD Connect již nastavit a úspěšně nainstalován. Informace o tom, jak nainstalovat Azure AD Connect najdete v tématu [Azure AD Connect a federační](active-directory-aadconnectfed-whatis.md).
Tento dokument neposkytuje následující předpoklady:
- jestli máte k službě federation service, nastavení a úspěšně ověřování uživatelů.
- externí uživatelé můžete ověřit pomocí jejich externí e-mailovou adresu.
- - Použití atributu alternativní ID pro přihlášení je nastavit a nakonfigurovaná. Uživatelé se mohou ověřovat pomocí jejich alternativní ID. Další informace o nastavení atributu alternativní ID se službou AD FS najdete v tématu [konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Úloha 1: Příprava prostředí
Následující úloha je více informační tak, že jste připravení zahájit synchronizaci externí účtů, aby se můžete přihlásit pomocí alternativního i například atribut mail.

Definujte položky, než přejdete na druhé úloze v následující tabulce.

![Architektura](media/active-directory-aadconnect-guest-sync/guest2.png)

|Aspekt prostředí|Co se použije pro?|Implementace ve vašem prostředí|
|-----|-----|-----|
|Atribut UPN cloudu|Atribut, který naplní hlavní název uživatele objektů externího uživatele v cloudu. Přípona UPN o externích účtech musí být jedna je definována v požadavky. Toto je ověřené domény.|* Příklad: UserPrincipalName (nmorin@bmcontoso.com)|
|Přihlašovací adresa|Atribut, který bude externí uživatelé zadají při přihlášení. Tento atribut musí mít formát e-mailové adresy a ve většině případů se shoduje s e-mailovou adresu externího uživatele.|* Příklad: pošty (nmorin@fabrikam.com)|
|Azure AD Connect oboru filtru|Filtr, který umožňuje cílení na externí identity do oboru synchronizační pravidla definovaná v této příručce. Obvyklé způsoby do oboru zahrnout: předem definované organizační jednotky v organizaci, určité zásady vytváření názvů, konkrétní domény, atd.|* Příklad: Organizační jednotky obsahuje externích typů|
|Klientovi služby Azure AD|Název klienta Azure AD, protože se zobrazí na Azure AD Connect.|Příklad: contoso.onmicrosoft.com|

Na následujícím snímku obrazovky má tři označená pole.
- **Externals** organizační jednotku, která se používá v Azure AD Connect obor filtru a umístění externí uživatele.
- **E-mailu** atribut, který používá externí uživatele k přihlášení.
- **UserPrincipalName** atribut, který je ověřené domény, který je v místním prostředí sdružených se službou.

![Uživatel](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Úloha 2: Konfigurace Azure AD Connect
Jakmile máte výše uvedené informace definované, se můžeme přesunout k nastavení pravidla synchronizace Azure AD Connect. Chcete nastavit pravidla, pomocí editoru pravidla synchronizace Azure AD Connect. Další informace o editoru najdete v tématu [deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Postup konfigurace synchronizačního pravidla
Použijte následující postup ke konfiguraci Azure AD Connect.

1. Otevřete editor pravidlo synchronizace Azure AD Connect přechodem na **editoru pravidel synchronizace Start - Azure AD Connect –**.
2. Na **editoru pravidel synchronizace** obrazovky, ujistěte se, směr je **příchozí** a na pravé straně, klikněte na tlačítko **přidat nové pravidlo**.
3. Na **popis** , nakonfigurujte následující nastavení a klikněte na tlačítko **Další**.
    - **Název** – zadejte název pravidla 
    - **Připojený systém:** -místní prostředí AD
    - **Typ objektu připojené systému:** -uživatel
    - **Typ objektu úložiště Metaverse:** -osoba
    - **Typ propojení:** – připojení
    - **Priorita:** – 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. Na **filtru pro vytváření oboru** obrazovky, klikněte na tlačítko **přidat skupinu**.
5. Rozevírací seznamy slouží ke konfiguraci filtru. Zadejte následující příkaz a klikněte na tlačítko **Další**. Tato akce vytvoří filtr, který se vztahuje pouze na objekty, které jsou umístěné v externí organizační jednotce.
    - **Atribut** -hodnoty rozlišující název
    - **Operátor** – obsahuje
    - **Hodnota** -externích typů
 
 ![Filtr](media/active-directory-aadconnect-guest-sync/guest4.png)

6. Na **připojení pravidla** obrazovky, klikněte na tlačítko **Další**.
7. Na **transformace** obrazovky, klikněte na tlačítko **přidat transformace**. Zadejte následující informace:
    - **Typ toku** – konstanta
    - **Cíle atributů** -userType
    - **Zdroj** – Guest
8. Na **transformace** obrazovky, klikněte na tlačítko **přidat transformace**. Zadejte následující informace:
    - **Typ toku** – přímé
    - **Cíle atributů** -onPremisesUserPrincipalName
    - **Zdroj** -e-mailu
9. Na **transformace** obrazovky, klikněte na tlačítko **přidat transformace**. Zadejte následující informace:
    - **Typ toku** – přímé
    - **Cíle atributů** -userPrincipalName
    - **Zdroj** -userPrincipalName ![transformace](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Klikněte na tlačítko **Add** (Přidat). 
11. Na **editoru pravidel synchronizace** obrazovky, ujistěte se, směr je **odchozí** a na pravé straně, klikněte na tlačítko **přidat nové pravidlo**.
12. Na **popis** stránky nakonfigurujte následující nastavení a klikněte na tlačítko **Další**.
    - **Název** – zadejte název pravidla 
    - **Připojený systém:** – klient Azure AD
    - **Typ objektu připojené systému:** -uživatel
    - **Typ objektu úložiště Metaverse:** -osoba
    - **Typ vazby** – připojení
    - **Priorita:** – 90
    - 
![Pravidlo](media/active-directory-aadconnect-guest-sync/guest6.png)

13. Na **Scoping filtru** obrazovky, klikněte na tlačítko **Další**.
14. Na **připojení pravidla** obrazovky, klikněte na tlačítko **Další**.
15. Na **transformace** obrazovky, klikněte na tlačítko **přidat transformace**.  Zadejte následující informace:
    - **Typ toku** – přímé
    - **Cíle atributů** -userType
    - **Zdroj** -userType
9. Na **transformace** obrazovky, klikněte na tlačítko **přidat transformace**. Zadejte následující informace:
    - **Typ toku** – přímé
    - **Cíle atributů** -onPremisesUserPrincipalName
    - **Zdroj** -onPremisesUserPrincipalName ![transformace](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Klikněte na tlačítko **Add** (Přidat). 
11. Po nakonfigurování těchto pravidel, budete muset spustit úplnou synchronizaci. Pomocí prostředí PowerShell spustit úplnou synchronizaci. Po dokončení synchronizace mohou pokračovat k dalšímu kroku.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Úloha 3: Federation
Následující úloha je informační na pár věcí, které je potřeba mít na místě v pořadí pro scénář fungovat.

Nastavení federačního můžete ověřit pomocí Azure pomocí Azure AD PowerShell. Tento dokument používá v1.1 MSOL prostředí PowerShell. Tuto verzi si můžete nainstalovat [zde](https://docs.microsoft.com/en-us/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Ověřte nastavení federace
Pomocí následujícího postupu ověřte nastavení federace.
1. Otevřete prostředí Windows PowerShell a připojte se ke službě Azure AD pomocí následujícího příkazu:
``` powershell
      Connect-MSOLservice
```
2.  Zadejte přihlašovací údaje globální správce
3.  Nyní zadejte následující příkaz:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Všimněte si, že má být vrácen federační informace. Poznámka: **ActiveLogonUri** je adresa URL federačního serveru.

  ![metadata](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Ověřte alternativním přihlašovacím ID
Tento dokument služby AD FS používá jako zprostředkovatel identity (Idp). Pokud používáte jiný Idp, může se velmi tyto kroky.

1. Otevřete prostředí Windows PowerShell a zadejte následující příkaz:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Měli byste vidět informace služby AD FS.  Poznámka: **AlternateLoginID** a **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Úloha 4: testování
Chcete-li ověřit, že to funguje správně, budete muset přihlásit se na koncový bod, který byl nakonfigurován pro ověřování pomocí rozšíření Idp. Abyste to mohli otestovat, můžeme nasadit webu v Azure a používají následující adresy url: contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Ověřte, že můžete přihlášení s alternativní ID
1. Přihlaste se do koncového bodu.</br>
![koncový bod](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Zadejte svoje uživatelské jméno a budete znovu přesměrováni na přihlašovací stránku federace.
![Federace](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Zadejte své přihlašovací údaje.
2. Můžete by měl nyní se úspěšně přihlášeni.
![Úspěch](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Další postup
- [Vlastnosti uživatele spolupráce Azure Active Directory s B2B](../../active-directory/active-directory-b2b-user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: Historie vydaných verzí](active-directory-aadconnect-version-history.md)
