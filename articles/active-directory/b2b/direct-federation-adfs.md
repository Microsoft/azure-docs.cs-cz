---
title: Nastavit přímé federace se službou AD FS pro B2B – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu AD FS jako zprostředkovatele identity pro federaci s přímým přístupem tak, že hosté se můžete přihlásit do aplikace Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544319"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Příklad: Přímé federace se službou Active Directory Federation Services (AD FS) (preview)
|     |
| --- |
| Federace s přímým přístupem je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Tento článek popisuje, jak nastavit [přímé federace](direct-federation.md) jako zprostředkovatele identity SAML 2.0 nebo WS-Fed pomocí služby Active Directory Federation Services (AD FS). Pro podporu federace s přímým přístupem, musí být nakonfigurován určité atributech a deklaracích identit u zprostředkovatele identity. Pro ilustraci, jak nakonfigurovat zprostředkovatele identity pro federaci s přímým přístupem, použijeme jako příklad služby Active Directory Federation Services (AD FS). Ukážeme, jak nastavit službu AD FS jako zprostředkovatele identity SAML i jako zprostředkovatele identity WS-Fed.

> [!NOTE]
> Tento článek popisuje, jak nastavit službu AD FS pro SAML a WS-Fed pro ilustraci. Integrace s přímým přístupem federace se zprostředkovatel identity služby AD FS doporučujeme pomocí WS-Fed jako protokol. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurace služby AD FS pro federaci s přímým přístupem SAML 2.0
Azure AD B2B je možné nakonfigurovat pro vytvoření federace pomocí zprostředkovatelů identity, které používají protokol SAML s konkrétní požadavky uvedené níže. Pro ilustraci kroků konfigurace SAML, tato část ukazuje, jak nastavení služby AD FS pro protokol SAML 2.0. 

Nastavení federace s přímým přístupem, musí být následující atributy dostali odpověď SAML 2.0, od poskytovatele identity. Tyto atributy lze nastavit odkazování na soubor XML služby tokenů zabezpečení online nebo ručním zadáním. V kroku 12 [vytvořit instanci test služby AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) popisuje, jak najít koncových bodů služby AD FS nebo třeba generovat adresu URL metadat `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Atribut  |Hodnota  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI partnera zprostředkovatele identity, například vystavitele `http://www.example.com/exk10l6w90DHM0yi...`         |

Následující deklarace identity je potřeba nakonfigurovat v tokenu SAML 2.0, Vystavitel zprostředkovatele identity:


|Atribut  |Hodnota  |
|---------|---------|
|Formát ID názvu položky     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


V další části ukazuje, jak nakonfigurovat požadované atributech a deklaracích identit pomocí služby AD FS s ukázkovým zprostředkovatele identity SAML 2.0.

### <a name="before-you-begin"></a>Než začnete

Server služby AD FS musí být nastavena již nahoru a funguje před zahájením tohoto postupu. Nápovědu k nastavení serveru služby AD FS najdete v tématu [vytvořit instanci testovací AD FS 3.0 na virtuálním počítači Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Přidat popis deklarace identity

1. Na serveru služby AD FS vyberte **nástroje** > **Správa služby AD FS**.
2. V navigačním podokně vyberte **služby** > **popisy deklarací**.
3. V části **akce**vyberte **přidat popis deklarace identity**.
4. V **přidání popisu deklarace** okno, zadejte následující hodnoty:

   - **Zobrazovaný název**: Trvalý identifikátor
   - **Identifikátor deklarace identity**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Zaškrtněte políčko pro **publikovat tento popis deklarace identity ve federačních metadatech jako typ deklarace identity, které tato služba FS může přijímat**.
   - Zaškrtněte políčko pro **publikovat tento popis deklarace identity ve federačních metadatech jako typ deklarace identity, který tato služba FS může odesílat**.

5. Klikněte na tlačítko **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Přidat vztah důvěryhodnosti předávající strany a pravidla deklarací identity

1. Na serveru služby AD FS, přejděte na **nástroje** > **Správa služby AD FS**.
2. V navigačním podokně vyberte **vztahy důvěryhodnosti** > **vztahy důvěryhodnosti předávající strany**.
3. V části **akce**vyberte **přidat vztah důvěryhodnosti předávající strany**. 
4. V průvodci Přidat předávající straně důvěryhodnosti pro **vybrat zdroj dat**, použijte možnost **importovat data o předávající straně publikovaná online nebo v místní síti**. Zadejte tento federačních metadat URL - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Nechte ostatní výchozí výběry. Vyberte **Zavřít**.
5. **Upravit pravidla deklarací identity** otevře se průvodce.
6. V **upravit pravidla deklarací identity** průvodce, vyberte **přidat pravidlo**. V **výběr typu pravidla**vyberte **odesílat atributy LDAP jako deklarace identity**. Vyberte **Další**.
7. V **konfigurace pravidla deklarace**, zadejte následující hodnoty: 

   - **Název pravidla deklarace identity**: Pravidlo deklarace identity e-mailu 
   - **Úložiště atributů**: Active Directory 
   - **Atribut LDAP**: E-Mail-Addresses 
   - **Typ odchozí deklarace**: E-mailová adresa

8. Vyberte **Finish** (Dokončit).
9. **Upravit pravidla deklarací identity** okna se zobrazí nové pravidlo. Klikněte na tlačítko **Použít**. 
10. Klikněte na tlačítko **OK**.  

### <a name="create-an-email-transform-rule"></a>Vytvoření pravidla transformace e-mailu
1. Přejděte na **upravit pravidla deklarací identity** a klikněte na tlačítko **přidat pravidlo**. V **výběr typu pravidla**vyberte **transformovat příchozí deklarace identity** a klikněte na tlačítko **Další**. 
2. V **konfigurace pravidla deklarace**, zadejte následující hodnoty: 

   - **Název pravidla deklarace identity**: Pravidel transformace pro e-mailu 
   - **Typ příchozí deklarace identity**: E-mailová adresa 
   - **Typ odchozí deklarace**: ID názvu 
   - **Formátu ID odchozího názvu**: Trvalý identifikátor 
   - Vyberte **Předávat všechny hodnoty deklarací identity**.

3. Klikněte na **Dokončit**. 
4. **Upravit pravidla deklarací identity** okna se zobrazí nová pravidla. Klikněte na tlačítko **Použít**. 
5. Klikněte na **OK**. Server služby AD FS je nyní nakonfigurován pro přímé federaci pomocí protokolu SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurace služby AD FS pro federaci WS-Fed s přímým přístupem 
Azure AD B2B je možné nakonfigurovat pro vytvoření federace pomocí zprostředkovatelů identity, které používají protokol WS-Fed s konkrétní požadavky uvedené níže. V současné době dva poskytovatelé WS-Fed jsme otestovali kompatibilitu s Azure AD zahrnují služby AD FS a Shibboleth. Tady Active Directory Federation Services (AD FS) použijeme jako příklad WS-Fed zprostředkovatele identity. Další informace o vytvoření vztahu důvěryhodnosti předávající strany mezi zprostředkovateli WS-Fed kompatibilní s Azure AD stáhněte si dokumentace kompatibility zprostředkovatele Azure AD Identity.

Nastavení federace s přímým přístupem, musí být přijata následující atributy ve zprávě WS-Fed od poskytovatele identity. Tyto atributy lze nastavit odkazování na soubor XML služby tokenů zabezpečení online nebo ručním zadáním. V kroku 12 [vytvořit instanci test služby AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) popisuje, jak najít koncových bodů služby AD FS nebo třeba generovat adresu URL metadat `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Atribut  |Hodnota  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI partnera zprostředkovatele identity, například vystavitele `http://www.example.com/exk10l6w90DHM0yi...`         |

Požadované deklarace identit pro token WS-Fed Vystavitel zprostředkovatele identity:

|Atribut  |Hodnota  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

V další části ukazuje, jak nakonfigurovat požadované atributy a deklarace identity pomocí služby AD FS s ukázkovým WS-Fed zprostředkovatele identity.

### <a name="before-you-begin"></a>Než začnete
Server služby AD FS musí být nastavena již nahoru a funguje před zahájením tohoto postupu. Nápovědu k nastavení serveru služby AD FS najdete v tématu [vytvořit instanci testovací AD FS 3.0 na virtuálním počítači Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Přidat vztah důvěryhodnosti předávající strany a pravidla deklarací identity 
1. Na serveru služby AD FS, přejděte na **nástroje** > **Správa služby AD FS**. 
1. V navigačním podokně vyberte **vztahy důvěryhodnosti** > **vztahy důvěryhodnosti předávající strany**. 
1. V části **akce**vyberte **přidat vztah důvěryhodnosti předávající strany**.  
1. V okně Přidat předávající strana Průvodce vztahu důvěryhodnosti, pro **vybrat zdroj dat**, použijte možnost **importovat data o předávající straně publikovaná online nebo v místní síti**. Zadejte tuto adresu URL federačních metadat: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Nechte ostatní výchozí výběry. Vyberte **Zavřít**.
1. **Upravit pravidla deklarací identity** otevře se průvodce. 
1. V **upravit pravidla deklarací identity** průvodce, vyberte **přidat pravidlo**. V **výběr typu pravidla**vyberte **odesílat deklarace pomocí vlastního pravidla**. Vyberte *Další*. 
1. V **konfigurace pravidla deklarace**, zadejte následující hodnoty:

   - **Název pravidla deklarace identity**: Neměnné Id problému  
   - **Vlastní pravidlo**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Vyberte **Finish** (Dokončit). 
1. **Upravit pravidla deklarací identity** okna se zobrazí nové pravidlo. Klikněte na tlačítko **Použít**.  
1. Ve stejném **upravit pravidla deklarací identity** průvodce, vyberte **přidat pravidlo**. V **typ pravidla Cohose**vyberte **odesílat atributy LDAP jako deklarace identity**. Vyberte **Další**.
1. V **konfigurace pravidla deklarace**, zadejte následující hodnoty: 

   - **Název pravidla deklarace identity**: Pravidlo deklarace identity e-mailu  
   - **Úložiště atributů**: Active Directory  
   - **Atribut LDAP**: E-Mail-Addresses  
   - **Typ odchozí deklarace**: E-mailová adresa 

1.  Vyberte **Finish** (Dokončit). 
1.  **Upravit pravidla deklarací identity** okna se zobrazí nové pravidlo. Klikněte na tlačítko **Použít**.  
1.  Klikněte na **OK**. Server služby AD FS je teď nakonfigurovaný pro s přímým přístupem pomocí WS-Fed federace.

## <a name="next-steps"></a>Další postup
V dalším kroku budete [konfigurace federace s přímým přístupem ve službě Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) buď na portálu Azure AD nebo pomocí prostředí PowerShell. 
