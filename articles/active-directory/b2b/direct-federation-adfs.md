---
title: Nastavení přímé federace se službou AD FS pro B2B – Azure AD
description: Přečtěte si, jak nastavit službu AD FS jako poskytovatele identity pro přímou federaci, aby se hosté mohli přihlásit k vašim aplikacím Azure AD.
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
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272833"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Příklad: Přímé federace se službou AD FS (AD FS) (preview)
|     |
| --- |
| Přímá federace je funkce veřejného náhledu služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Tento článek popisuje, jak nastavit [přímou federaci](direct-federation.md) pomocí služby AD FS (Active Directory Federation Services) jako zprostředkovatele identity SAML 2.0 nebo WS-Fed. Pro podporu přímé federace musí být u zprostředkovatele identity nakonfigurovány určité atributy a deklarace identity. Pro ilustraci konfigurace zprostředkovatele identity pro přímou federaci použijeme jako příklad službu AD FS (Active Directory Federation Services). Ukážeme vám, jak nastavit službu AD FS jako poskytovatele identity SAML i jako poskytovatele identity WS-Fed.

> [!NOTE]
> Tento článek popisuje, jak nastavit ad FS pro SAML a WS-Fed pro ilustrační účely. Pro přímé federace integrace, kde zprostředkovatel identity je AD FS, doporučujeme použít WS-Fed jako protokol. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurace služby AD FS pro přímou federaci SAML 2.0
Azure AD B2B lze nakonfigurovat tak, aby federate s poskytovateli identity, které používají protokol SAML se specifickými požadavky uvedenými níže. Pro ilustraci kroků konfigurace SAML, tato část ukazuje, jak nastavit Službu AD FS pro SAML 2.0. 

Chcete-li nastavit přímé federace, musí být přijata následující atributy v odpovědi SAML 2.0 od poskytovatele identity. Tyto atributy lze nakonfigurovat propojením se souborem XML služby tokenů zabezpečení online nebo ručním zadáním. Krok 12 v [souboru Vytvoření instance služby AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) popisuje, jak najít koncové body služby AD FS nebo jak generovat adresu URL metadat, například `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Atribut  |Hodnota  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystaviteli partnerského idp, například`http://www.example.com/exk10l6w90DHM0yi...`         |

V tokenu SAML 2.0 vydaném poskytovatelem identity je třeba nakonfigurovat následující deklarace identity:


|Atribut  |Hodnota  |
|---------|---------|
|NameID formát     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|Emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


V další části je znázorněno, jak nakonfigurovat požadované atributy a deklarace identity pomocí služby AD FS jako příklad poskytovatele identity SAML 2.0.

### <a name="before-you-begin"></a>Než začnete

Server služby AD FS musí být před zahájením tohoto postupu již nastaven a funkční. Nápovědu k nastavení serveru služby AD FS najdete [v tématu Vytvoření testovací instance služby AD FS 3.0 na virtuálním počítači Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Přidání popisu deklarace

1. Na serveru služby AD FS vyberte nástroj pro správu **služby** > **AD FS**.
2. V navigačním podokně vyberte **Popisy** > **deklarací služby**.
3. V části **Akce**vyberte **Přidat popis deklarace .**
4. V okně **Přidat popis deklarace** zadejte následující hodnoty:

   - **Zobrazovaný název**: Trvalý identifikátor
   - **Identifikátor deklarace :**`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Zaškrtněte políčko **Publikovat tento popis deklarace v metadatech federace jako typ deklarace, který může tato federační služba přijmout**.
   - Zaškrtněte políčko **Publikovat tento popis deklarace v metadatech federace jako typ deklarace, který může tato federační služba odeslat**.

5. Klikněte na **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Přidání pravidel důvěryhodnosti a deklarací důvěryhodnosti předávající strany

1. Na serveru služby AD FS přejděte na nástroj ovou správu **služby** > **Tools AD FS**.
2. V navigačním podokně vyberte **možnost Vztahy důvěryhodnosti vztahů** > důvěryhodnosti vztahů**důvěryhodnosti**.
3. V části **Akce**vyberte **Přidat důvěryhodnost předávající strany**. 
4. V průvodci přidáním důvěryhodného vztahu předávající strany pro **možnost Vybrat zdroj dat**použijte možnost **Importovat data o předávající straně publikovanou online nebo v místní síti**. Zadejte adresu URL https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlmetadat federace- . Ponechte jiné výchozí výběry. Vyberte **Zavřít**.
5. Otevře se průvodce **Upravit pravidla deklarací.**
6. V průvodci **Upravit pravidla deklarací** vyberte **Přidat pravidlo**. V **rozezvolte Typ pravidla**vyberte **Odeslat atributy LDAP jako deklarace identity**. Vyberte **další**.
7. V **poli Konfigurovat pravidlo deklarace**zadejte následující hodnoty: 

   - **Název pravidla deklarace**tvrdí : Pravidlo deklarace e-mailové deklarace 
   - **Úložiště atributů**: Služba Active Directory 
   - **Atribut LDAP**: E-mailové adresy 
   - **Typ odchozí reklamy**: E-mailová adresa

8. Vyberte **Finish** (Dokončit).
9. V okně **Upravit pravidla deklarací** se zobrazí nové pravidlo. Klikněte na **Použít**. 
10. Klikněte na **OK**.  

### <a name="create-an-email-transform-rule"></a>Vytvoření pravidla transformace e-mailu
1. Přejděte na **Upravit pravidla deklarací a** klepněte na tlačítko Přidat **pravidlo**. V **poleM Zvolte Typ pravidla**vyberte Transformovat příchozí **deklaraci a** klepněte na tlačítko **Další**. 
2. V **poli Konfigurovat pravidlo deklarace**zadejte následující hodnoty: 

   - **Název pravidla deklarace**tvrdí : Pravidlo transformace e-mailu 
   - **Typ příchozí reklamace**: E-mailová adresa 
   - **Typ odchozí deklarace**: ID jména 
   - **Formát ID odchozího jména**: Trvalý identifikátor 
   - Vyberte **Předávat všechny hodnoty deklarací identity**.

3. Klikněte na **Finish** (Dokončit). 
4. V okně **Upravit pravidla deklarací** se zobrazí nová pravidla. Klikněte na **Použít**. 
5. Klikněte na tlačítko **OK**. Server služby AD FS je nyní nakonfigurován pro přímou federaci pomocí protokolu SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurace služby AD FS pro přímou federaci WS-Fed 
Azure AD B2B lze nakonfigurovat tak, aby federate s poskytovateli identity, které používají protokol WS Fed s konkrétní požadavky uvedené níže. V současné době dva poskytovatelé WS-Fed byly testovány na kompatibilitu s Azure AD patří AD FS a Shibboleth. Zde použijeme službu AD FS (Active Directory Federation Services) jako příklad poskytovatele identity WS-Fed. Další informace o vytvoření vztahu důvěryhodnosti předávající strany mezi poskytovatelem kompatibilním s WS-Fed s Azure AD si stáhněte dokumenty kompatibility zprostředkovatele identity Azure AD.

Chcete-li nastavit přímou federaci, musí být ve zprávě WS-Fed od poskytovatele identity přijaty následující atributy. Tyto atributy lze nakonfigurovat propojením se souborem XML služby tokenů zabezpečení online nebo ručním zadáním. Krok 12 v [souboru Vytvoření instance služby AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) popisuje, jak najít koncové body služby AD FS nebo jak generovat adresu URL metadat, například `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Atribut  |Hodnota  |
|---------|---------|
|PasivníPožadavek     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystaviteli partnerského idp, například`http://www.example.com/exk10l6w90DHM0yi...`         |

Požadované nároky pro token WS-Fed vydaný idp:

|Atribut  |Hodnota  |
|---------|---------|
|ID neměnné     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|Emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

V další části je znázorněno, jak nakonfigurovat požadované atributy a deklarace identity pomocí služby AD FS jako příklad u poskytovatele identity WS-Fed.

### <a name="before-you-begin"></a>Než začnete
Server služby AD FS musí být před zahájením tohoto postupu již nastaven a funkční. Nápovědu k nastavení serveru služby AD FS najdete [v tématu Vytvoření testovací instance služby AD FS 3.0 na virtuálním počítači Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Přidání pravidel důvěryhodnosti a deklarací důvěryhodnosti předávající strany 
1. Na serveru služby AD FS přejděte na nástroj ovou správu **služby** > **Tools AD FS**. 
1. V navigačním podokně vyberte **možnost Vztahy důvěryhodnosti vztahů** > důvěryhodnosti vztahů**důvěryhodnosti**. 
1. V části **Akce**vyberte **Přidat důvěryhodnost předávající strany**.  
1. V průvodci přidáním důvěryhodného vztahu předávající strany použijte v části **Vybrat zdroj dat**možnost **Importovat data o předávající straně publikované online nebo v místní síti**. Zadejte tuto adresu `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`URL metadat federace: .  Ponechte jiné výchozí výběry. Vyberte **Zavřít**.
1. Otevře se průvodce **Upravit pravidla deklarací.** 
1. V průvodci **Upravit pravidla deklarací** vyberte **Přidat pravidlo**. V **poleM Zvolte Typ pravidla**vyberte **Odeslat deklarace pomocí vlastního pravidla**. Vyberte *další*. 
1. V **poli Konfigurovat pravidlo deklarace**zadejte následující hodnoty:

   - **Název pravidla deklarace**: Id neměnného problému  
   - **Vlastní pravidlo**:`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Vyberte **Finish** (Dokončit). 
1. V okně **Upravit pravidla deklarací** se zobrazí nové pravidlo. Klikněte na **Použít**.  
1. Ve stejném **průvodci Upravit pravidla deklarací** vyberte **Přidat pravidlo**. V **pole Typ pravidla cohose**vyberte **Odeslat atributy LDAP jako deklarace identity**. Vyberte **další**.
1. V **poli Konfigurovat pravidlo deklarace**zadejte následující hodnoty: 

   - **Název pravidla deklarace**tvrdí : Pravidlo deklarace e-mailové deklarace  
   - **Úložiště atributů**: Služba Active Directory  
   - **Atribut LDAP**: E-mailové adresy  
   - **Typ odchozí reklamy**: E-mailová adresa 

1.  Vyberte **Finish** (Dokončit). 
1.  V okně **Upravit pravidla deklarací** se zobrazí nové pravidlo. Klikněte na **Použít**.  
1.  Klikněte na tlačítko **OK**. Server Služby AD FS je nyní nakonfigurován pro přímou federaci pomocí služby WS-Fed.

## <a name="next-steps"></a>Další kroky
Dále [nakonfigurujete přímou federaci ve službě Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) buď na portálu Azure AD, nebo pomocí PowerShellu. 
