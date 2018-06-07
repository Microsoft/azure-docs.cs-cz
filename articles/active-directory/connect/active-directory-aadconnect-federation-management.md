---
title: Azure AD Connect – přizpůsobení a Správa služby AD FS | Microsoft Docs
description: Správa služby AD FS s Azure AD Connect a přizpůsobení uživatele AD FS přihlašování uživatelů s Azure AD Connect a prostředí PowerShell.
keywords: Služba AD FS, služba AD FS, služby AD FS správy, AAD Connect, připojení, přihlášení, služby AD FS přizpůsobení, opravte federačního vztahu důvěryhodnosti, O365, předávající strany
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 276e53784b30c2196ad7455cf9fd801a103fdc30
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590850"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Spravovat a přizpůsobit Active Directory Federation Services přes Azure AD Connect
Tento článek popisuje, jak spravovat a přizpůsobit Active Directory Federation Services (AD FS) pomocí služby Azure Active Directory (Azure AD) připojit. Zahrnuje také dalších běžných úkolů služby AD FS, které možná budete muset udělat pro celou konfiguraci farmy služby AD FS.

| Téma | Co pokrývá |
|:--- |:--- |
| **Správa služby AD FS** | |
| [Opravit vztah důvěryhodnosti](#repairthetrust) |Jak opravit vztah důvěryhodnosti federace s Office 365. |
| [Vytvořit federaci s Azure AD pomocí alternativního přihlašovacího ID ](#alternateid) | Konfigurace federace pomocí alternativního přihlašovacího ID  |
| [Přidejte server služby AD FS](#addadfsserver) |Jak rozbalit farmu služby AD FS s dalším serverem služby AD FS. |
| [Přidat server proxy webové aplikace služby AD FS](#addwapserver) |Jak rozbalit farmu služby AD FS s dalším serverem Proxy webových aplikací (WAP). |
| [Přidání federované domény](#addfeddomain) |Postup přidání federovanou doménu. |
| [Aktualizovat certifikát SSL](active-directory-aadconnectfed-ssl-update.md)| Jak aktualizovat certifikát SSL pro farmu služby AD FS. |
| **Přizpůsobení služby AD FS** | |
| [Přidat vlastní logo nebo obrázku](#customlogo) |Tom, jak přizpůsobit přihlašovací stránku služby AD FS s firemní logo a ilustrace. |
| [Přidejte popis přihlášení](#addsignindescription) |Jak přidat popis přihlašovací stránku. |
| [Upravit pravidla deklarace identity služby AD FS](#modclaims) |Postup úpravy deklarací identity služby AD FS pro různé scénáře federace. |

## <a name="manage-ad-fs"></a>Správa služby AD FS
Pomocí Průvodce službou Azure AD Connect můžete provádět různé AD FS související úlohy v Azure AD Connect s zásah uživatele. Po dokončení instalace služby Azure AD Connect spuštěním průvodce, můžete spustit průvodce znovu a provádět další úkoly.

## <a name="repairthetrust"></a>Opravit vztah důvěryhodnosti 
Zkontrolujte aktuální stav služby AD FS a Azure AD důvěřujete a proveďte příslušné akce k opravě vztahu důvěryhodnosti můžete použít Azure AD Connect. Postupujte podle těchto kroků k opravě služby Azure AD a vztah důvěryhodnosti služby AD FS.

1. Vyberte **AAD opravy a služby AD FS důvěřují** ze seznamu další úkoly.
   ![Opravit AAD a AD FS vztah důvěryhodnosti](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. Na **připojit k Azure AD** stránky, zadejte svoje přihlašovací údaje globálního správce pro Azure AD a klikněte na tlačítko **Další**.
   ![Připojení k Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. Na **přihlašovací údaje vzdálený přístup** stránky, zadejte přihlašovací údaje správce domény.

   ![Přihlašovací údaje vzdálený přístup](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Po kliknutí na tlačítko **Další**, Azure AD Connect kontroluje stav certifikátu a zobrazuje všechny problémy.

    ![Stav certifikátů](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    **Připraveno ke konfiguraci** stránka zobrazuje seznam akcí, které budou provedeny k opravě vztah důvěryhodnosti.

    ![Připraveno ke konfiguraci](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Klikněte na tlačítko **nainstalovat** k opravě vztah důvěryhodnosti.

> [!NOTE]
> Azure AD Connect může pouze opravit nebo zákona o certifikáty, které jsou podepsané svým držitelem. Azure AD Connect nelze opravit, certifikáty třetích stran.

## <a name="alternateid"></a>Vytvořit federaci s Azure AD pomocí AlternateID 
Doporučujeme, aby Name(UPN) místní uživatele objektu zabezpečení a cloudem hlavní název uživatele udržely stejné. Pokud hlavní název uživatele místní používá směrovat domény (např. Contoso.Local) nebo se nedá změnit z důvodu závislosti místních aplikací, doporučujeme nastavení alternativního přihlašovacího ID. Alternativního přihlašovacího ID umožňuje nakonfigurovat přihlašování kde mohou uživatelé přihlásit pomocí atributu než jejich UPN, jako je například e-mailu. Volba pro hlavní název uživatele v Azure AD Connect výchozí atribut userPrincipalName ve službě Active Directory. Pokud můžete vybrat jiný atribut pro hlavní název uživatele a jsou federaci pomocí služby AD FS, pak Azure AD Connect se konfigurace služby AD FS pro alternativním přihlašovacím ID Níže je uveden příklad vybrat jiný atribut pro hlavní název uživatele:

![Výběr atributu alternativní ID](media/active-directory-aadconnect-federation-management/attributeselection.png)

Konfigurace alternativního přihlašovacího ID pro službu AD FS zahrnuje dva hlavní kroky:
1. **Konfigurace správnou sadu vystavování deklarací identity**: důvěřovat pravidel vystavování deklarací identity v předávající strany služby Azure AD jsou upravit tak, aby použít vybraný atribut UserPrincipalName jako alternativní ID uživatele.
2. **Povolit alternativního přihlašovacího ID v konfiguraci služby AD FS**: Konfigurace služby AD FS je aktualizována tak, aby služba AD FS můžete vyhledat uživatele v příslušné doménové struktury pomocí alternativní ID. Tato konfigurace je podporována pro službu AD FS v systému Windows Server 2012 R2 (s KB2919355) nebo novější. Pokud jsou servery služby AD FS 2012 R2, Azure AD Connect ověří přítomnost požadovaných KB. Pokud není zjištěna KB, upozornění zobrazí se po dokončení konfigurace, jak je uvedeno níže:

    ![Upozornění na 2012R2 chybí KB](media/active-directory-aadconnect-federation-management/kbwarning.png)

    K nápravě nastavení v případě chybějící KB, nainstalujte požadované [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) a pak opravte důvěryhodnosti pomocí [opravit AAD a vztah důvěryhodnosti služby AD FS](#repairthetrust).

> [!NOTE]
> Další informace o alternateID a kroky, jak ručně nakonfigurovat, najdete v tématu [konfigurace alternativního přihlašovacího ID](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Přidejte server služby AD FS 

> [!NOTE]
> Pokud chcete přidat server služby AD FS, Azure AD Connect vyžaduje certifikát PFX. Proto můžete tuto operaci provést pouze v případě, že jste nakonfigurovali farmu služby AD FS pomocí Azure AD Connect.

1. Vyberte **nasadit další federační Server**a klikněte na tlačítko **Další**.

   ![Další federační server](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. Na **připojit k Azure AD** stránky, zadejte svoje přihlašovací údaje globálního správce pro Azure AD a klikněte na tlačítko **Další**.

   ![Připojení k Azure AD](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Zadejte přihlašovací údaje správce domény.

   ![Pověření správce domény](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect požádá o heslo souboru PFX, který jste zadali při konfiguraci nové farmě služby AD FS službou Azure AD Connect. Klikněte na tlačítko **zadejte heslo** k zadání hesla pro soubor PFX.

   ![Heslo certifikátu](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Zadání certifikátu SSL](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. Na **servery služby AD FS** stránky, zadejte název serveru nebo IP adresu pro přidání do farmy služby AD FS.

   ![Servery AD FS](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Klikněte na tlačítko **Další**a projít konečné **konfigurace** stránky. Po dokončení přidávání serverů do farmy služby AD FS na Azure AD Connect bude mít možnost ověřit připojení.

   ![Připraveno ke konfiguraci](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Instalace byla dokončena](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Přidání serveru AD FS WAP 

> [!NOTE]
> Chcete-li přidat WAP server, Azure AD Connect vyžaduje certifikát PFX. Pokud jste nakonfigurovali farmu služby AD FS pomocí Azure AD Connect tedy můžete provádět jenom tuto operaci.

1. Vyberte **nasadit Proxy webových aplikací** ze seznamu dostupných úloh.

   ![Nasazení služby Proxy webových aplikací](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Zadejte přihlašovací údaje Azure globálního správce.

   ![Připojení k Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. Na **certifikát SSL zadejte** stránky, zadejte heslo pro soubor PFX, který jste zadali při konfiguraci farmy služby AD FS službou Azure AD Connect.
   ![Heslo certifikátu](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Zadání certifikátu SSL](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Přidáte server, který se má přidat jako WAP server. Protože serveru WAP nemusí být připojené k doméně, Průvodce zobrazí pro pověření pro správu pro server, který chcete přidat.

   ![Přihlašovací údaje správce serveru](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. Na **přihlašovací údaje pro vztah důvěryhodnosti Proxy** zadejte pověření pro správu pro proxy server nakonfigurovat vztah důvěryhodnosti a přístup na primární server ve farmě služby AD FS.

   ![Přihlašovací údaje pro vztah důvěryhodnosti proxy](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. Na **připraveno ke konfiguraci** stránky, Průvodce zobrazí seznam akcí, které budou provedeny.

   ![Připraveno ke konfiguraci](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Klikněte na tlačítko **nainstalovat** na dokončení konfigurace. Po dokončení konfigurace Průvodce vám dává možnost k ověření připojení k serverům. Klikněte na tlačítko **ověřte** zkontrolujte připojení.

   ![Instalace byla dokončena](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Přidání federované domény 

Je snadné přidání domény na federovanou se službou Azure AD pomocí Azure AD Connect. Azure AD Connect přidá domény pro federaci a upravit pravidla deklarací identity tak, aby správně odrážela vystavitele, pokud máte více domén sdružených se službou Azure AD.

1. Pokud chcete přidat federovanou doménu, vyberte úlohu **přidat další doménu služby Azure AD**.

   ![Další doménu služby Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. Na další stránce průvodce zadejte přihlašovací údaje globálního správce pro Azure AD.

   ![Připojení k Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. Na **přihlašovací údaje vzdálený přístup** stránky, zadejte přihlašovací údaje správce domény.

   ![Přihlašovací údaje vzdálený přístup](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. Na další stránce Průvodce poskytuje seznam domén služby Azure AD, které můžete vytvořit federaci vašeho místního adresáře s. Vyberte doménu v seznamu.

   ![Azure AD domain](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Když vyberete domény, Průvodce vám poskytne příslušné informace o další akce, které průvodce provede a dopad konfigurace. V některých případech Pokud vyberete domény, který ještě není ověřit ve službě Azure AD, Průvodce vám poskytne informace, které umožňují ověření domény. V tématu [přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md) další podrobnosti.

5. Klikněte na **Další**. **Připraveno ke konfiguraci** stránka zobrazuje seznam akcí, které provádí Azure AD Connect. Klikněte na tlačítko **nainstalovat** na dokončení konfigurace.

   ![Připraveno ke konfiguraci](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Uživatelé z přidané federované domény musí být synchronizovány, než bude moci přihlásit ke službě Azure AD.

## <a name="ad-fs-customization"></a>Přizpůsobení AD FS
Následující části obsahují podrobnosti o některé běžné úlohy, které možná budete muset provést v případě, že přizpůsobit přihlašovací stránku služby AD FS.

## <a name="customlogo"></a>Přidat vlastní logo nebo obrázku 
Chcete změnit logo společnosti, která se zobrazí na **přihlášení** stránky, použijte následující rutinu prostředí Windows PowerShell a syntaxe.

> [!NOTE]
> Doporučené dimenze pro logo jsou 260 x 35 při 96 dpi a velikost souboru nepřesahovala 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* parametr je povinný. Výchozí motiv vydaný se službou AD FS se nazývá výchozí.

## <a name="addsignindescription"></a>Přidejte popis přihlášení 
Přidat přihlašovací stránku popis, který **přihlašovací stránce**, pomocí následující rutiny prostředí Windows PowerShell a syntaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Upravit pravidla deklarace identity služby AD FS 
Služba AD FS podporuje bohaté deklarace jazyk, který vám pomůže vytvořit vlastní pravidla deklarací identity. Další informace najdete v tématu [Role jazyka pravidel deklarací identity](https://technet.microsoft.com/library/dd807118.aspx).

Následující části popisují, jak můžete napsat vlastní pravidla pro některé scénáře, které se týkají služby Azure AD a federační služby AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Neměnné ID podmíněného na hodnotu, která se nachází v atributu
Azure AD Connect umožňuje zadat atribut má být použit jako zdrojové ukotvení při objekty se synchronizují do Azure AD. Pokud hodnota ve vlastním atributu není prázdná, můžete vydat deklaraci identity neměnné ID.

Například můžete vybrat **ms-ds-consistencyguid** jako atribut pro zdrojové ukotvení a problém **ImmutableID** jako **ms-ds-consistencyguid** v případě, že atribut má hodnotu před ním. Pokud není žádná hodnota pro atribut, **objectGuid** jako neměnné ID. Můžete vytvořit sadu pravidel vlastních deklarací identity, jak je popsáno v následující části.

**Pravidlo 1: Atributy dotazu**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

V tomto pravidle, že dotazování hodnoty **ms-ds-consistencyguid** a **objectGuid** pro uživatele ze služby Active Directory. Změňte název úložiště na název příslušné úložiště ve vašem nasazení služby AD FS. Také změnit typ deklarace identity, který má správný deklarace typu pro federační, jak jsou definovány pro **objectGuid** a **ms-ds-consistencyguid**.

Navíc pomocí **přidat** a není **problém**, vyhnete se přidávání odchozí problém pro entitu a můžete použít hodnoty jako pomocných hodnot. Po stanovení hodnotu, která chcete použít jako neměnné ID vydáte deklaraci identity v pravidle novější

**Pravidlo 2: Zkontrolujte, jestli ms-ds-consistencyguid existuje pro uživatele**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Toto pravidlo definuje dočasné příznak **idflag** , je nastaven na **useguid** Pokud neexistuje žádné **ms-ds-consistencyguid** vyplněný pro uživatele. Logika za to je fakt, že služba AD FS neumožňuje prázdné deklarací identity. Takže když přidání deklarací identity, http://contoso.com/ws/2016/02/identity/claims/objectguid a http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid v pravidlo 1, v níž se **msdsconsistencyguid** deklarace identity jenom, pokud je hodnota vyplněný pro uživatele. Pokud není vyplněné, služby AD FS uvidí, že bude mít prázdnou hodnotu a okamžitě se zahodí. Všechny objekty se mají **objectGuid**, tak, že deklarace bude vždy existovat po provedení pravidla 1.

**Pravidlo 3: Vystavení ms-ds-consistencyguid jako neměnné ID, pokud je k dispozici**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Jedná se implicitní **existují** zkontrolujte. Pokud hodnota deklarace identity existuje, potom vydat, jako neměnné ID. Předchozí příklad používá **nameidentifier** deklarací identity. Budete muset změnit typ odpovídající deklarace na neměnné ID ve vašem prostředí.

**Pravidlo 4: Pokud není k dispozici ms-ds-consistencyGuid vydání objectGuid jako neměnné ID**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

V tomto pravidle, se jednoduše kontrola příznak dočasné **idflag**. Můžete rozhodnout, zda vydat deklaraci identity založenou na jeho hodnotu.

> [!NOTE]
> Pořadí těchto pravidel je důležité.

### <a name="sso-with-a-subdomain-upn"></a>Jednotné přihlašování s subdoména UPN
Můžete přidat více než jedné domény na federovanou pomocí Azure AD Connect, jak je popsáno v [přidání nové federované domény](active-directory-aadconnect-federation-management.md#addfeddomain). Deklarace hlavní název (UPN) uživatele musí změnit tak, aby ID vystavitele odpovídá kořenové domény a není subdomény, protože federované kořenové domény platí i pro podřízený objekt.

Ve výchozím nastavení je jako sady pravidel deklarací identity pro vystavitele s ID:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Deklarace ID výchozí vystavitele](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

Výchozí pravidlo jednoduše trvá příponu UPN a používá je v deklarace ID vystavitele. Například Jan je uživatel v sub.contoso.com a je contoso.com sdružených se službou Azure AD. Jan zadá john@sub.contoso.com jako uživatelské jméno při přihlášení k Azure AD. Výchozí pravidlo deklarace identity ID vystavitele ve službě AD FS ji zpracovává následujícím způsobem:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Hodnota deklarace identity:**  http://sub.contoso.com/adfs/services/trust/

Pokud chcete, aby kořenové domény v hodnotě vystavitele deklarace, změňte pravidlo deklarace identity tak, aby odpovídala následující:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Další postup
Další informace o [možnosti přihlášení uživatele](active-directory-aadconnect-user-signin.md).
