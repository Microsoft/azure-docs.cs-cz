---
title: Azure AD Connect – správa a přizpůsobení služby AD FS | Dokumenty společnosti Microsoft
description: Správa služby AD FS s Azure AD Connect a přizpůsobení uživatelského prostředí pro přihlášení do služby AD FS pomocí Azure AD Connect a PowerShellu.
keywords: Služba AD FS, Služba ADFS, správa služby AD FS, služba AAD Connect, připojení, přihlášení, přizpůsobení služby AD FS, vztah důvěryhodnosti oprav, O365, federace, předávající strana
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcbeedddc65a916f869a778616779917a9571181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331982"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Správa a přizpůsobení služby AD FS pomocí služby Azure AD Connect
Tento článek popisuje, jak spravovat a přizpůsobovat služby AD FS (AD FS) pomocí služby Azure Active Directory (Azure AD) Connect. Obsahuje také další běžné úlohy služby AD FS, které může být nutné provést pro úplnou konfiguraci farmy služby AD FS.

| Téma | Co pokrývá |
|:--- |:--- |
| **Správa ad fs** | |
| [Oprava vztahu důvěryhodnosti](#repairthetrust) |Jak opravit federační vztah důvěryhodnosti s Office 365. |
| [Federate s Azure AD pomocí alternativní přihlašovací ID](#alternateid) | Konfigurace federace pomocí alternativního přihlašovacího ID  |
| [Přidání serveru služby AD FS](#addadfsserver) |Jak rozšířit farmu služby AD FS o další server služby AD FS. |
| [Přidání serveru proxy webové aplikace služby AD FS](#addwapserver) |Jak rozšířit farmu služby AD FS o další server wap (Web Application Proxy). |
| [Přidání federované domény](#addfeddomain) |Jak přidat federovovovku. |
| [Aktualizace certifikátu TLS/SSL](how-to-connect-fed-ssl-update.md)| Jak aktualizovat certifikát TLS/SSL pro farmu služby AD FS. |
| **Přizpůsobení služby AD FS** | |
| [Přidání vlastního firemního loga nebo ilustrace](#customlogo) |Jak přizpůsobit přihlašovací stránku služby AD FS s logem společnosti a ilustrací. |
| [Přidání popisu přihlášení](#addsignindescription) |Jak přidat popis přihlašovací stránky. |
| [Úprava pravidel deklarací ad FS](#modclaims) |Jak upravit deklarace identity ad FS pro různé scénáře federace. |

## <a name="manage-ad-fs"></a>Správa ad fs
Pomocí průvodce Azure AD Connect můžete provádět různé úlohy související se službou AD FS ve službě Azure AD Connect s minimálním zásahem uživatele. Po dokončení instalace služby Azure AD Connect spuštěním průvodce můžete průvodce spustit znovu a provést další úlohy.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Oprava vztahu důvěryhodnosti 
Pomocí služby Azure AD Connect můžete zkontrolovat aktuální stav vztahu důvěryhodnosti služby AD FS a Azure AD a provést příslušné akce k opravě vztahu důvěryhodnosti. Podle těchto kroků opravíte vztah důvěryhodnosti Azure AD a AD FS.

1. Ze seznamu dalších úkolů vyberte Opravit službu **AAD a vztah důvěryhodnosti služby ADFS.**
   ![Oprava vztahu důvěryhodnosti aslužbě AAD a adfs](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na stránce **Připojit k Azure AD** zadejte pověření globálního správce pro Azure AD a klikněte na **další**.
   ![Připojení k Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na stránce **Pověření vzdáleného přístupu** zadejte pověření správce domény.

   ![Pověření pro vzdálený přístup](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Po klepnutí na tlačítko **Další**Azure AD Connect zkontroluje stav certifikátu a zobrazí všechny problémy.

    ![Stav osvědčení](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Stránka **Připraveno ke konfiguraci** zobrazuje seznam akcí, které budou provedeny za účelem opravy vztahu důvěryhodnosti.

    ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Chcete-li vztah důvěryhodnosti opravit, klepněte na tlačítko **Nainstalovat.**

> [!NOTE]
> Azure AD Connect můžete opravit nebo jednat pouze na certifikáty, které jsou podepsané svým držitelem. Azure AD Connect nemůže opravit certifikáty třetích stran.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federate s Azure AD pomocí AlternateID 
Doporučuje se, aby místní uživatelské hlavní jméno (HLAVNÍ NÁZEV) a primární název uživatele cloudu jsou zachovány stejné. Pokud místní název domény používá nesměrovatelnou doménu (např. Contoso.local) nebo nelze změnit z důvodu závislostí místních aplikací, doporučujeme nastavit alternativní přihlašovací ID. Alternativní přihlašovací ID umožňuje nakonfigurovat přihlašovací prostředí, kde se uživatelé mohou přihlásit pomocí jiného atributu, než je jejich upn, jako je například pošta. Volba pro hlavní uživatelské jméno ve službě Azure AD Connect výchozí atribut userPrincipalName ve službě Active Directory. Pokud zvolíte jiný atribut pro hlavní uživatelské jméno a federace pomocí Služby AD FS, pak Azure AD Connect nakonfiguruje Službu AD FS pro alternativní přihlašovací ID. Příklad výběru jiného atributu pro hlavní uživatelské jméno je uveden níže:

![Výběr atributu Alternativní ID](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurace alternativního přihlašovacího ID pro službu AD FS se skládá ze dvou hlavních kroků:
1. **Konfigurace správné sady deklarací vystavování**: Pravidla deklarací identity vystavování ve vztahu důvěryhodnosti předávající strany služby Azure AD jsou upravena tak, aby používala vybraný atribut UserPrincipalName jako alternativní ID uživatele.
2. **Povolit alternativní přihlašovací ID v konfiguraci služby AD FS**: Konfigurace služby AD FS je aktualizována tak, aby služba AD FS mohla vyhledat uživatele v příslušných doménových strukturách pomocí alternativního ID. Tato konfigurace je podporována pro službu AD FS v systému Windows Server 2012 R2 (s kb2919355) nebo novějším. Pokud servery služby AD FS jsou 2012 R2, Azure AD Connect zkontroluje přítomnost požadované KB. Pokud kb není detekován, zobrazí se po dokončení konfigurace upozornění, jak je znázorněno níže:

    ![Upozornění pro chybějící KB na 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Chcete-li opravit konfiguraci v případě chybějící kb, nainstalujte požadovanou [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) a pak opravte vztah důvěryhodnosti pomocí [oprav AAD a AD FS Trust](#repairthetrust).

> [!NOTE]
> Další informace o alternativním id a postupech ruční konfigurace našly další informace o [konfiguraci alternativního přihlášení](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Přidání serveru služby AD FS 

> [!NOTE]
> Chcete-li přidat server služby AD FS, azure ad connect vyžaduje certifikát PFX. Proto můžete provést tuto operaci pouze v případě, že jste nakonfigurovali farmu služby AD FS pomocí služby Azure AD Connect.

1. Vyberte **Nasadit další federační server**a klepněte na tlačítko **Další**.

   ![Další federační server](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce pro Azure AD a klikněte na **Další**.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Zadejte pověření správce domény.

   ![Pověření správce domény](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect požádá o heslo souboru PFX, který jste zadali při konfiguraci nové farmy Služby AD FS pomocí Azure AD Connect. Chcete-li zadat heslo pro soubor PFX, klepněte na **tlačítko Zadat heslo.**

   ![Heslo certifikátu](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Určení certifikátu TLS/SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na stránce **Servery služby AD FS** zadejte název serveru nebo adresu IP, která má být přidána do farmy služby AD FS.

   ![Servery služby AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klepněte na tlačítko **Další**a projděte závěrečnou stránku **Konfigurace.** Po dokončení přidání serverů azure aslužby Služby a služby AD FS budete mít možnost ověřit připojení.

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalace dokončena](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Přidání serveru WAP služby AD FS 

> [!NOTE]
> Chcete-li přidat wap server, Azure AD Connect vyžaduje certifikát PFX. Proto můžete provést pouze v případě, že jste nakonfigurovali farmu služby AD FS pomocí služby Azure AD Connect.

1. Ze seznamu dostupných úloh vyberte **Nasadit proxy webové aplikace.**

   ![Nasazení proxy webových aplikací](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Zadejte přihlašovací údaje globálního správce Azure.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na stránce **Zadejte certifikát SSL** zadejte heslo pro soubor PFX, který jste zadali při konfiguraci farmy služby AD FS pomocí služby Azure AD Connect.
   ![Heslo certifikátu](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Určení certifikátu TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Přidejte server, který chcete přidat jako server WAP. Vzhledem k tomu, že server WAP nemusí být připojen k doméně, požádá průvodce o pověření pro správu přidávaný server.

   ![Pověření serveru pro správu](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na stránce **Pověření důvěryhodnosti serveru Proxy** zadejte pověření pro správu, která nakonfigurují vztah důvěryhodnosti serveru proxy a přistupují k primárnímu serveru ve farmě služby AD FS.

   ![Pověření pro důvěryhodnost serveru proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na stránce **Připraveno ke konfiguraci** průvodce zobrazí seznam akcí, které budou provedeny.

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Chcete-li dokončit konfiguraci, klepněte na tlačítko **Nainstalovat.** Po dokončení konfigurace vám průvodce poskytne možnost ověřit připojení k serverům. Chcete-li zkontrolovat připojení, klepněte na **tlačítko Ověřit.**

   ![Instalace dokončena](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Přidání federované domény 

Je snadné přidat doménu, která se federuje s Azure AD pomocí Azure AD Connect. Azure AD Connect přidá doménu pro federaci a upraví pravidla deklarace tak, aby správně odrážela vystavittele, když máte více domén federovaných pomocí Azure AD.

1. Pokud chcete přidat federovovovku, vyberte úkol **Přidat další doménu Azure AD**.

   ![Další doména Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na další stránce průvodce zadejte pověření globálního správce pro Azure AD.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na stránce **Pověření vzdáleného přístupu** zadejte pověření správce domény.

   ![Pověření pro vzdálený přístup](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na další stránce průvodce poskytuje seznam domén Azure AD, které můžete federate místní adresář s. Vyberte doménu ze seznamu.

   ![Doména Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Po výběru domény vám průvodce poskytne příslušné informace o dalších akcích, které průvodce provede, a o dopadu konfigurace. V některých případech pokud vyberete doménu, která ještě není ověřena ve službě Azure AD, průvodce vám poskytne informace, které vám pomohou ověřit doménu. Další podrobnosti najdete v tématu [Přidání vlastního názvu domény do služby Azure Active Directory.](../active-directory-domains-add-azure-portal.md)

5. Klikněte na **Další**. Stránka **Připravená ke konfiguraci** zobrazuje seznam akcí, které azure ad connect provede. Chcete-li dokončit konfiguraci, klepněte na tlačítko **Nainstalovat.**

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Uživatelé z přidané federované domény musí být synchronizovány před tím, než se budou moci přihlásit do služby Azure AD.

## <a name="ad-fs-customization"></a>Přizpůsobení ad FS
V následujících částech jsou uvedeny podrobnosti o některých běžných úkolech, které může být možné provést při přizpůsobení přihlašovací stránky služby AD FS.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Přidání vlastního firemního loga nebo ilustrace 
Chcete-li změnit logo společnosti, která je zobrazena na **přihlašovací** stránce, použijte následující rutinu a syntaxi prostředí Windows PowerShell.

> [!NOTE]
> Doporučené rozměry loga jsou 260 \@ x 35 96 dpi s velikostí souboru ne větší než 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Je vyžadován parametr *TargetName.* Výchozí motiv vydaný se schodištním FS má název Výchozí.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Přidání popisu přihlášení 
Chcete-li přidat popis přihlašovací stránky na **přihlašovací stránku**, použijte následující rutinu a syntaxi prostředí Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Úprava pravidel deklarací ad FS 
AD FS podporuje bohatý jazyk deklarací, který můžete použít k vytvoření vlastních pravidel deklarací. Další informace naleznete [v tématu Role jazyka pravidel deklarací nároku](https://technet.microsoft.com/library/dd807118.aspx).

Následující části popisují, jak můžete psát vlastní pravidla pro některé scénáře, které se vztahují k Azure AD a AD FS federace.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Neměnné ID podmíněné přítomností hodnoty v atributu
Azure AD Connect umožňuje určit atribut, který se má použít jako zdrojová kotva při synchronizaci objektů do Azure AD. Pokud hodnota ve vlastním atributu není prázdná, můžete vydat neměnnou deklaraci ID.

Můžete například vybrat **ms-ds-consistencyguid** jako atribut pro zdrojovou kotvu a vydat **ImmutableID** jako **ms-ds-consistencyguid** v případě, že atribut má hodnotu proti němu. Pokud neexistuje žádná hodnota proti atributu, problém **objectGuid** jako neměnné ID. Můžete vytvořit sadu vlastních pravidel deklarací, jak je popsáno v následující části.

**Pravidlo 1: Atributy dotazu**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

V tomto pravidle se dotazujete na hodnoty **ms-ds-consistencyguid** a **objectGuid** pro uživatele ze služby Active Directory. Změňte název obchodu na příslušný název obchodu v nasazení služby AD FS. Také změnit typ deklarací na správný typ deklarací pro vaše federace, jak je definováno pro **objectGuid** a **ms-ds-consistencyguid**.

Také pomocí **přidat** a ne **problém**, vyhnete přidání odchozí problém pro entitu a můžete použít hodnoty jako zprostředkující hodnoty. Deklaraci vystavíte v pozdějším pravidle poté, co zjistíte, jakou hodnotu použít jako neměnné ID.

**Pravidlo 2: Zkontrolujte, zda pro uživatele existuje ms-ds-consistencyguid.**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Toto pravidlo definuje dočasný příznak s názvem **idflag,** který je nastaven na **useguid,** pokud pro uživatele není žádný **ms-ds-consistencyguid.** Logika za to je skutečnost, že AD FS neumožňuje prázdné deklarace identity. Takže když přidáte `http://contoso.com/ws/2016/02/identity/claims/objectguid` `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` deklarace identity a v pravidle 1, skončíte s **deklarací msdsconsistencyguid** pouze v případě, že je hodnota naplněna pro uživatele. Pokud není naplněn, služby AD FS zjistí, že bude mít prázdnou hodnotu a okamžitě ji klesne. Všechny objekty budou mít **objectGuid**, takže deklarace bude vždy k dispozici po spuštění pravidla 1.

**Pravidlo 3: Vydání ms-ds-consistencyguid jako neměnné ID, pokud je k dispozici**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Toto je implicitní **exist** kontrola. Pokud hodnota deklarace existuje, pak problém, který jako neměnné ID. Předchozí příklad používá deklaraci **nameidentifier.** Budete muset změnit na příslušný typ deklarace pro neměnné ID ve vašem prostředí.

**Pravidlo 4: Problém objektGuid jako neměnné ID, pokud není k dispozici ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

V tomto pravidle jednoduše kontrolujete dočasný **příznak idflag**. Můžete se rozhodnout, zda reklamaci vydat na základě její hodnoty.

> [!NOTE]
> Posloupnost těchto pravidel je důležitá.

### <a name="sso-with-a-subdomain-upn"></a>Přiřazovací síť s hlavní název sítě domény

Pomocí služby Azure AD Connect můžete přidat více než jednu doménu, jak je popsáno v [doplňku Přidat novou federovovně](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect verze 1.1.553.0 a nejnovější vytvoří správné pravidlo deklarace pro issuerID automaticky. Pokud nemůžete použít Azure AD Connect verze 1.1.553.0 nebo nejnovější, se doporučuje, aby se nástroj [pravidla deklarací deklarací Azure AD RPT](https://aka.ms/aadrptclaimrules) používal ke generování a nastavení správných pravidel deklarací pro důvěryhodný certifikát předávající strany Azure AD.

## <a name="next-steps"></a>Další kroky
Další informace o [možnostech přihlášení uživatelů](plan-connect-user-signin.md).
