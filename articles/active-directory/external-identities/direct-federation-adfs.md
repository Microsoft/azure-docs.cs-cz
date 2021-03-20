---
title: Nastavení přímé federace s AD FS pro B2B – Azure AD
description: Naučte se nastavit AD FS jako zprostředkovatele identity pro přímou federaci, aby se hosté mohli přihlásit k aplikacím Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87908683"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Příklad: Přímá federace s Active Directory Federation Services (AD FS) (AD FS) (Preview)

> [!NOTE]
> Přímá federace je funkce veřejné verze Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Tento článek popisuje, jak nastavit [přímou federaci](direct-federation.md) pomocí Active Directory Federation Services (AD FS) (AD FS) jako poskytovatele identity SAML 2,0 nebo WS-Fed. Aby bylo možné podporovat přímé federace, musí být na zprostředkovateli identity nakonfigurovány určité atributy a deklarace identity. K ilustraci, jak nakonfigurovat poskytovatele identity pro přímou federaci, použijeme jako příklad Active Directory Federation Services (AD FS) (AD FS). Ukážeme, jak nastavit AD FS jako zprostředkovatele identity SAML a jako WS-Fedho poskytovatele identity.

> [!NOTE]
> Tento článek popisuje, jak nastavit AD FS pro SAML i WS-Fed pro ilustrační účely. U přímých integrací federace, kde je poskytovatel identity AD FS, doporučujeme jako protokol používat WS-Fed. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurace AD FS pro 2,0 přímé federace SAML
Azure AD B2B se dá nakonfigurovat tak, aby federovat s poskytovateli identity, kteří používají protokol SAML s konkrétními požadavky uvedenými níže. V této části se dozvíte, jak nastavit AD FS pro SAML 2,0. 

Pokud chcete nastavit přímou federaci, musí se v odpovědi SAML 2,0 od poskytovatele identity přijmout následující atributy. Tyto atributy je možné nakonfigurovat tak, že propojíte se souborem XML služby token zabezpečení online nebo je zadáte ručně. Krok 12 v tématu [vytvoření instance testovacího AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) popisuje, jak najít koncové body AD FS nebo jak generovat adresu URL metadat, například `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Atribut  |Hodnota  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystavitele partnerského IdPu, například `http://www.example.com/exk10l6w90DHM0yi...`         |

V tokenu SAML 2,0 vydaném zprostředkovatelem identity je potřeba nakonfigurovat následující deklarace identity:


|Atribut  |Hodnota  |
|---------|---------|
|Formát NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


Další část ukazuje, jak nakonfigurovat požadované atributy a deklarace identity pomocí AD FS jako příklad zprostředkovatele identity SAML 2,0.

### <a name="before-you-begin"></a>Než začnete

Než začnete s tímto postupem, musí být již nastaven a funkční server AD FS. Nápovědu k nastavení serveru AD FS najdete v tématu věnovaném [Vytvoření testovací instance AD FS 3,0 na virtuálním počítači Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Přidat popis deklarace identity

1. Na serveru AD FS vyberte **nástroje**  >  **AD FS Správa**.
2. V navigačním podokně vyberte   >  **Popis deklarace identity** služby.
3. V části **Akce** vyberte **přidat popis deklarace identity**.
4. V okně **přidat popis deklarace** zadejte následující hodnoty:

   - **Zobrazovaný název**: trvalý identifikátor
   - **Identifikátor deklarace identity**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Zaškrtněte políčko **publikovat tento popis deklarace identity ve federačních metadatech jako typ deklarace identity, kterou může tato služba FS přijmout**.
   - Zaškrtněte políčko **publikovat tento popis deklarace identity ve federačních metadatech jako typ deklarace identity, kterou může tato služba FS odeslat**.

5. Klikněte na **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Přidání vztahu důvěryhodnosti předávající strany a pravidel deklarací identity

1. Na AD FS serveru přejdete na **nástroje**  >  **AD FS Správa**.
2. V navigačním podokně vyberte vztahy **důvěryhodnosti**  >  **předávající strany** vztahů důvěryhodnosti.
3. V části **Akce** vyberte **Přidat vztah důvěryhodnosti předávající strany**. 
4. V Průvodci přidáním vztahu důvěryhodnosti předávající strany pro **možnost vybrat zdroj dat** použijte možnost **importovat data o předávající straně, která je publikována online nebo v místní síti**. Zadejte tuto adresu URL federačních metadat – https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Ponechte ostatní výchozí výběry. Vyberte **Zavřít**.
5. Otevře se průvodce **úpravou pravidel deklarací identity** .
6. V průvodci **úpravou pravidel deklarací** vyberte **Přidat pravidlo**. V **Možnosti zvolit typ pravidla** vyberte **Odeslat atributy LDAP jako deklarace identity**. Vyberte **Další**.
7. V části **konfigurovat pravidlo deklarace identity** zadejte následující hodnoty: 

   - **Název pravidla deklarace identity**: pravidlo deklarace e-mailu 
   - **Úložiště atributů**: Služba Active Directory 
   - **Atribut LDAP**: E-mailové adresy 
   - **Typ odchozí deklarace**: e-mailová adresa

8. Vyberte **Dokončit**.
9. V okně **upravit pravidla deklarací** se zobrazí nové pravidlo. Klikněte na **Použít**. 
10. Klikněte na **OK**.  

### <a name="create-an-email-transform-rule"></a>Vytvořit pravidlo transformace e-mailu
1. Přejděte na **upravit pravidla deklarace identity** a klikněte na **Přidat pravidlo**. V **Možnosti zvolit typ pravidla** vyberte **transformovat příchozí deklaraci identity** a klikněte na **Další**. 
2. V části **konfigurovat pravidlo deklarace identity** zadejte následující hodnoty: 

   - **Název pravidla deklarace identity**: pravidlo transformace e-mailu 
   - **Typ příchozí deklarace identity**: e-mailová adresa 
   - **Typ odchozí deklarace**: název ID 
   - **Formát ID odchozího názvu**: trvalý identifikátor 
   - Vyberte **Předávat všechny hodnoty deklarací identity**.

3. Klikněte na **Finish** (Dokončit). 
4. V okně **upravit pravidla deklarací** se zobrazí nová pravidla. Klikněte na **Použít**. 
5. Klikněte na **OK**. AD FS Server je nyní nakonfigurován pro přímou federaci pomocí protokolu SAML 2,0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurace AD FS pro WS-Fed přímou federaci 
Azure AD B2B se dá nakonfigurovat tak, aby federovat s poskytovateli identity, kteří používají protokol WS-Fed s konkrétními požadavky uvedenými níže. V současné době byly u těchto dvou zprostředkovatelů WS-Fed testovány kompatibility s Azure AD zahrnutí AD FS a Shibboleth. V tomto příkladu použijeme jako příklad poskytovatele identity WS-Fed Active Directory Federation Services (AD FS) (AD FS). Další informace o tom, jak vytvořit vztah důvěryhodnosti předávající strany mezi poskytovatelem kompatibilního s WS-Fed s Azure AD, najdete v dokumentech ke kompatibilitě zprostředkovatele identit Azure AD.

Chcete-li nastavit přímou federaci, musí být v WS-Fed zprávě od poskytovatele identity přijaty následující atributy. Tyto atributy je možné nakonfigurovat tak, že propojíte se souborem XML služby token zabezpečení online nebo je zadáte ručně. Krok 12 v tématu [vytvoření instance testovacího AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) popisuje, jak najít koncové body AD FS nebo jak generovat adresu URL metadat, například `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Atribut  |Hodnota  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystavitele partnerského IdPu, například `http://www.example.com/exk10l6w90DHM0yi...`         |

Požadované deklarace identity pro token WS-Fed vydané IdPem:

|Atribut  |Hodnota  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

V další části najdete postup konfigurace požadovaných atributů a deklarací identity pomocí AD FS jako příklad WS-Fedho zprostředkovatele identity.

### <a name="before-you-begin"></a>Než začnete
Než začnete s tímto postupem, musí být již nastaven a funkční server AD FS. Nápovědu k nastavení serveru AD FS najdete v tématu věnovaném [Vytvoření testovací instance AD FS 3,0 na virtuálním počítači Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Přidání vztahu důvěryhodnosti předávající strany a pravidel deklarací identity 
1. Na AD FS serveru přejdete na **nástroje**  >  **AD FS Správa**. 
1. V navigačním podokně vyberte vztahy **důvěryhodnosti**  >  **předávající strany** vztahů důvěryhodnosti. 
1. V části **Akce** vyberte **Přidat vztah důvěryhodnosti předávající strany**.  
1. V Průvodci přidáním vztahu důvěryhodnosti předávající strany pro **možnost vybrat zdroj dat** použijte možnost **importovat data o předávající straně, která je publikována online nebo v místní síti**. Zadejte tuto adresu URL federačních metadat: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Ponechte ostatní výchozí výběry. Vyberte **Zavřít**.
1. Otevře se průvodce **úpravou pravidel deklarací identity** . 
1. V průvodci **úpravou pravidel deklarací** vyberte **Přidat pravidlo**. V **Možnosti zvolit typ pravidla** vyberte **Odeslat deklarace identity pomocí vlastního pravidla**. Vyberte *Další*. 
1. V části **konfigurovat pravidlo deklarace identity** zadejte následující hodnoty:

   - **Název pravidla deklarace identity**: neměnné ID problému  
   - **Vlastní pravidlo**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Vyberte **Dokončit**. 
1. V okně **upravit pravidla deklarací** se zobrazí nové pravidlo. Klikněte na **Použít**.  
1. Ve stejném průvodci **úpravou pravidel deklarací identity** vyberte **Přidat pravidlo**. V **typu pravidla kohadice** vyberte **Odeslat atributy LDAP jako deklarace identity**. Vyberte **Další**.
1. V části **konfigurovat pravidlo deklarace identity** zadejte následující hodnoty: 

   - **Název pravidla deklarace identity**: pravidlo deklarace e-mailu  
   - **Úložiště atributů**: Služba Active Directory  
   - **Atribut LDAP**: E-mailové adresy  
   - **Typ odchozí deklarace**: e-mailová adresa 

1.  Vyberte **Dokončit**. 
1.  V okně **upravit pravidla deklarací** se zobrazí nové pravidlo. Klikněte na **Použít**.  
1.  Klikněte na **OK**. Server AD FS je nyní nakonfigurován pro přímou federaci pomocí protokolu WS-dodaných.

## <a name="next-steps"></a>Další kroky
Dále [nakonfigurujete přímou federaci ve službě Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) buď na portálu Azure AD, nebo pomocí PowerShellu. 
