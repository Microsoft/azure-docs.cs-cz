---
title: Azure AD Connect – Správa služby AD FS a vlastního nastavení | Dokumentace Microsoftu
description: Správa služby AD FS s Azure AD Connect a přizpůsobení uživatele AD FS přihlašování s Azure AD Connect a prostředí PowerShell.
keywords: Služba AD FS, AD FS, AD FS správy, AAD Connect, připojit, přihlášení, služby AD FS přizpůsobení, opravit důvěryhodnost mezi doménami, O365, federace, předávající strany
services: active-directory
documentationcenter: ''
author: billmath
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
ms.openlocfilehash: 5a36c239e296d6f393c49932f1a369d8aae80b83
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46311105"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Správa a přizpůsobení služby AD FS pomocí služby Azure AD Connect
Tento článek popisuje, jak spravovat a upravovat Active Directory Federation Services (AD FS) pomocí služby Azure Active Directory (Azure AD) Connect. Zahrnuje také dalších běžných úkolů služby AD FS, které možná bude potřeba provést kompletní konfiguraci farmy služby AD FS.

| Téma | Co zahrnuje |
|:--- |:--- |
| **Správa služby AD FS** | |
| [Opravit důvěryhodnost](#repairthetrust) |Popisuje, jak opravit důvěryhodnost federace s Office 365. |
| [Provést federaci se službou Azure AD pomocí alternativního přihlašovacího ID ](#alternateid) | Konfigurovat federaci pomocí alternativního přihlašovacího ID  |
| [Přidání serveru služby AD FS](#addadfsserver) |Jak rozbalit farmu služby AD FS s dalším serverem služby AD FS. |
| [Přidat server služby Proxy webových aplikací AD FS](#addwapserver) |Jak rozbalit farmu služby AD FS s dalším serverem Proxy webových aplikací (WAP). |
| [Přidat federované domény](#addfeddomain) |Postup přidání federovanou doménu. |
| [Aktualizace certifikátu SSL](how-to-connect-fed-ssl-update.md)| Postup aktualizace certifikátu SSL pro farmu služby AD FS. |
| **Přizpůsobení služby AD FS** | |
| [Přidat vlastní firemní logo a obrázek](#customlogo) |Jak přizpůsobit přihlašovací stránku služby AD FS s firemní logo a obrázek. |
| [Přidání popisu přihlášení](#addsignindescription) |Jak přidat popis přihlašovací stránky. |
| [Upravit pravidla deklarací identity služby AD FS](#modclaims) |Jak změnit deklarace identity služby AD FS pro různé scénáře federace. |

## <a name="manage-ad-fs"></a>Správa služby AD FS
Pomocí Průvodce Azure AD Connect, můžete provádět různé AD FS související úlohy ve službě Azure AD Connect bez zásahu uživatele. Po dokončení instalace služby Azure AD Connect pomocí průvodce, můžete spustit průvodce znovu a provádět další úkoly.

## <a name="repairthetrust"></a>Opravit důvěryhodnost 
Můžete zkontrolovat aktuální stav AD FS a Azure AD vztah důvěryhodnosti a přijmout vhodná opatření na opravu vztahu důvěryhodnosti Azure AD Connect. Následující postup použijte k opravě služby Azure AD a vztah důvěryhodnosti služby AD FS.

1. Vyberte **opravit AAD a kterým důvěřují služby AD FS** ze seznamu další úkoly.
   ![Opravit AAD a AD FS důvěřovat](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na **připojit ke službě Azure AD** stránky, zadejte svoje přihlašovací údaje globálního správce pro službu Azure AD a klikněte na tlačítko **Další**.
   ![Připojení k Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na **přihlašovacích údajů pro vzdálený přístup** stránky, zadejte přihlašovací údaje správce domény.

   ![Pověření vzdáleného přístupu](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Po kliknutí na **Další**, Azure AD Connect zkontroluje stav certifikátu a zobrazuje všechny problémy.

    ![Stav certifikátů](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    **Připraveno ke konfiguraci** stránka zobrazuje seznam akcí, které se provede pro opravu vztahu důvěryhodnosti.

    ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klikněte na tlačítko **nainstalovat** opravit vztah důvěryhodnosti.

> [!NOTE]
> Azure AD Connect může pouze opravit nebo act s certifikáty, které jsou podepsané svým držitelem. Azure AD Connect nejde opravit certifikáty třetích stran.

## <a name="alternateid"></a>Provést federaci se službou Azure AD pomocí AlternateID 
Doporučuje se, že Name(UPN) objektu zabezpečení uživatele v místním prostředím a cloudem hlavní název uživatele zůstanou stejné. Pokud hlavní název uživatele v místním používá nesměrovatelných domény (např.) Contoso.Local) nebo se nedá změnit z důvodu závislosti místních aplikací, doporučujeme nastavit alternativní přihlašovací ID. Alternativní přihlašovací ID umožňuje konfigurovat přihlašovací prostředí kde uživatelé můžou přihlásit se pomocí atributu než jejich hlavní název uživatele, jako jsou e-mailu. Volba pro hlavní název uživatele ve výchozím nastavení Azure AD Connect na atribut userPrincipalName ve službě Active Directory. Pokud zvolíte jiný atribut pro hlavní název uživatele a jsou federaci pomocí služby AD FS, pak Azure AD Connect nakonfiguruje služby AD FS pro alternativní přihlašovací ID. Níže je uveden příklad vybrat jiný atribut pro hlavní název uživatele:

![Výběr atributu alternativní ID](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurace alternativního přihlašovacího ID pro službu AD FS zahrnuje dva hlavní kroky:
1. **Konfigurace správnou sadu vystavování deklarací identity**: pravidel vystavování deklarací identity v Azure AD předávající straně důvěryhodnosti jsou upraveny pro použití vybraný atribut UserPrincipalName jako alternativní ID uživatele.
2. **Povolte alternativní přihlašovací ID v konfiguraci služby AD FS**: Konfigurace služby AD FS se aktualizuje tak, aby služba AD FS můžete vyhledávat uživatele v příslušné doménové struktury pomocí alternativní ID. Tato konfigurace je podporovaná pro službu AD FS na Windows Server 2012 R2 (s KB2919355) nebo novější. Pokud jsou servery služby AD FS 2012 R2, Azure AD Connect ověří přítomnost požadovaných KB. Pokud nebude nalezena KB, upozornění se zobrazí po dokončení konfigurace, jak je znázorněno níže:

    ![Upozornění na chybějící znalostní BÁZE na 2012 R2](./media/how-to-connect-fed-management/kbwarning.png)

    Kvůli opravě konfigurace v případě chybějících KB, instalaci požadovaných [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) a potom opravit pomocí vztahu důvěryhodnosti [opravit AAD a vztah důvěryhodnosti služby AD FS](#repairthetrust).

> [!NOTE]
> Další informace o alternateID a postup, jak ručně nakonfigurovat, najdete v článku [konfigurace alternativního přihlašovacího ID](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Přidání serveru služby AD FS 

> [!NOTE]
> Chcete-li přidat server služby AD FS, Azure AD Connect vyžaduje certifikát PFX. Proto můžete tuto operaci provést pouze v případě, že jste nakonfigurovali farmy služby AD FS pomocí služby Azure AD Connect.

1. Vyberte **nasadit další federační Server**a klikněte na tlačítko **Další**.

   ![Další federační server](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na **připojit ke službě Azure AD** stránky, zadejte svoje přihlašovací údaje globálního správce pro službu Azure AD a klikněte na tlačítko **Další**.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Zadejte přihlašovací údaje správce domény.

   ![Přihlašovací údaje správce domény](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect vyzve k zadání heslo souboru PFX, který jste zadali při konfiguraci nové farmě služby AD FS s Azure AD Connect. Klikněte na tlačítko **zadat heslo** zadávat heslo k souboru PFX.

   ![Heslo certifikátu](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Zadání certifikátu SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na **servery služby AD FS** stránky, zadejte název serveru nebo IP adresu přidat do farmy služby AD FS.

   ![Servery AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klikněte na tlačítko **Další**a projděte si poslední **konfigurovat** stránky. Po dokončení přidávání serverů do farmy služby AD FS Azure AD Connect budete mít možnost ověřit připojení.

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalace dokončena](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Přidání serveru AD FS WAP 

> [!NOTE]
> Chcete-li přidat WAP server, Azure AD Connect vyžaduje certifikát PFX. Pokud jste nakonfigurovali farmy služby AD FS pomocí služby Azure AD Connect, lze provést pouze tuto operaci.

1. Vyberte **nasadit Proxy webových aplikací** ze seznamu dostupných úloh.

   ![Nasazení služby Proxy webových aplikací](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Zadejte přihlašovací údaje globálního správce služby Azure.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na **certifikát SSL zadejte** stránky, zadejte heslo pro soubor PFX, který jste zadali při konfiguraci farmy služby AD FS s Azure AD Connect.
   ![Heslo certifikátu](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Zadání certifikátu SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Přidejte server přidat jako WAP server. Protože WAP server nemusí být připojen k doméně, v Průvodci vyzve k zadání přihlašovacích údajů správce ke přidávaný server.

   ![Přihlašovací údaje správce serveru](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na **přihlašovací údaje pro vztah důvěryhodnosti Proxy** zadejte pověření pro správu ke konfiguraci proxy serveru důvěřujete a přístup k primárnímu serveru ve farmě služby AD FS.

   ![Přihlašovací údaje pro vztah důvěryhodnosti proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na **připraveno ke konfiguraci** stránky, Průvodce zobrazí seznam akcí, které budou provedeny.

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Klikněte na tlačítko **nainstalovat** dokončete konfiguraci. Po dokončení konfigurace Průvodce poskytuje možnost ověřit připojení k serverům. Klikněte na tlačítko **ověřte** aby se ověřilo připojení.

   ![Instalace dokončena](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Přidat federované domény 

Je snadné přidání domény k federaci s Azure AD pomocí služby Azure AD Connect. Azure AD Connect přidá domény pro federování a upravit pravidla deklarací identity tak, aby správně odrážela vystavitele, když máte více domén, které jsou federované se službou Azure AD.

1. Pokud chcete přidat federované domény, vyberte úlohu **přidat další domény Azure AD**.

   ![Další domény Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na další stránku průvodce zadejte přihlašovací údaje globálního správce pro službu Azure AD.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na **přihlašovacích údajů pro vzdálený přístup** zadejte přihlašovací údaje správce domény.

   ![Pověření vzdáleného přístupu](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na další stránce průvodce obsahuje seznam domén Azure AD, které může provést federaci s vaší místní adresář. Zvolte doménu, ze seznamu.

   ![Doména služby Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Až zvolíte, domény, Průvodce vám poskytne příslušné informace o další akce, které průvodce provede a vliv konfiguraci. V některých případech Pokud vyberete domény, který ještě není ověřen ve službě Azure AD, Průvodce poskytuje informace o ověření domény. Zobrazit [přidání vlastního názvu domény do Azure Active Directory](../active-directory-domains-add-azure-portal.md) další podrobnosti.

5. Klikněte na **Další**. **Připraveno ke konfiguraci** stránka zobrazuje seznam akcí, které Azure AD Connect provede. Klikněte na tlačítko **nainstalovat** dokončete konfiguraci.

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Předtím, než budou moct přihlásit ke službě Azure AD, musí být synchronizovány uživatelům přidání federovanou doménu.

## <a name="ad-fs-customization"></a>Přizpůsobení AD FS
Následující části obsahují podrobné informace o některých běžných úloh, které možná budete muset provést, když si přizpůsobit přihlašovací stránku služby AD FS.

## <a name="customlogo"></a>Přidat vlastní firemní logo a obrázek 
Chcete-li změnit logo společnosti, která se zobrazí na **přihlášení** stránky, použijte následující rutinu Windows Powershellu a syntaxe.

> [!NOTE]
> Doporučené rozměry loga jsou 260 x 35 \@ 96 dpi a velikost souboru nepřesahovala 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* parametr je povinný. Výchozí motiv vydaný se službou AD FS se nazývá výchozí.

## <a name="addsignindescription"></a>Přidání popisu přihlášení 
Přidat přihlašovací stránku popis, který **přihlašovací stránku**, použijte následující rutinu Windows Powershellu a syntaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Upravit pravidla deklarací identity služby AD FS 
Služba AD FS podporuje jazyk bohaté deklarace identity, který vám pomůže vytvořit vlastní pravidla deklarací identity. Další informace najdete v tématu [Role jazyka pravidel deklarací identity](https://technet.microsoft.com/library/dd807118.aspx).

Následující části popisují, jak psát vlastní pravidla pro některé scénáře, které se týkají služby Azure AD a federační služba AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Neměnné ID na hodnotu byla vyžadována jeho přítomnost v atributu
Azure AD Connect vám umožní určit atribut, který má být použit jako zdrojové ukotvení při objekty se synchronizují do služby Azure AD. Pokud hodnota ve vlastním atributu není prázdná, můžete vydat deklaraci identity neměnné ID.

Například můžete vybrat **ms-ds-consistencyguid** jako atribut zdrojového ukotvení a problém **ImmutableID** jako **ms-ds-consistencyguid** v případě atribut má hodnotu před ním. Pokud není žádná hodnota pro atribut, **objectGuid** jako neměnné ID Můžete vytvořit sadu pravidel vlastních deklarací identity, jak je popsáno v následující části.

**Pravidlo 1: Atributy dotazu**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

V tomto pravidle dotazujete hodnoty **ms-ds-consistencyguid** a **objectGuid** pro uživatele ze služby Active Directory. Změňte název úložiště na název příslušné úložiště ve vašem nasazení služby AD FS. Také změnit typ deklarace identity, který správnou deklarace typu pro federaci, jak jsou definovány pro **objectGuid** a **ms-ds-consistencyguid**.

Navíc pomocí **přidat** a není **problém**, můžete se vyhnout přidávání odchozí problém pro entitu a hodnoty můžete použít jako hodnoty, ležící. Vydáte deklaraci identity v pravidle novější po navázání jaká hodnota se má použít jako neměnné ID

**Pravidlo 2: Zkontrolujte, jestli ms-ds-consistencyguid existuje pro uživatele**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Toto pravidlo definuje dočasné příznak, který volá **idflag** , která je nastavena na **useguid** Pokud neexistuje žádný **ms-ds-consistencyguid** pro uživatele. To logice je skutečnost, že služba AD FS neumožňuje prázdné deklarací identity. Ano, při přidání deklarace identity http://contoso.com/ws/2016/02/identity/claims/objectguid a http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid v Rule 1, skončíte se **msdsconsistencyguid** jenom deklarace identity, pokud hodnota se vyplní pro daného uživatele. Pokud není vyplněné, služby AD FS vidí, že bude obsahovat prázdnou hodnotu a zahodí okamžitě. Všechny objekty se mají **objectGuid**, takže tuto žádost bude vždy existuje po provedení Rule 1.

**Pravidlo 3: Pokud je k dispozici vydat ms-ds-consistencyguid jako neměnné ID**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Toto je implicitní **existují** zkontrolovat. Pokud hodnota deklarace identity existuje, pak problém jako neměnné ID V předchozím příkladu se používá **nameidentifier** deklarací identity. Budete muset změnit na typ odpovídající deklarace identity pro neměnné ID ve vašem prostředí.

**Pravidlo 4: Pokud není k dispozici ms-ds-consistencyGuid vydat objectGuid jako neměnné ID**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

V tomto pravidle, se jednoduše kontrola dočasné příznak **idflag**. Můžete rozhodnout, zda vydat deklaraci identity založenou na jeho hodnotu.

> [!NOTE]
> Je důležité pořadí těchto pravidel.

### <a name="sso-with-a-subdomain-upn"></a>Jednotné přihlašování se subdoménou hlavní název uživatele

Můžete přidat více než jednu doménu federovanou pomocí služby Azure AD Connect, jak je popsáno v [přidat nové federované domény](how-to-connect-fed-management.md#addfeddomain). Verze služby Azure AD Connect 1.1.553.0 a nejnovější pravidla deklarace identity správné pro issuerID automaticky vytvoří. Pokud nemůžete použít Azure AD Connect verze 1.1.553.0 nebo nejnovější, je doporučeno [pravidla deklarací identity Azure AD Záhlavím](https://aka.ms/aadrptclaimrules) nástroj se používá k vygenerování a nastavit pravidla správné deklarací pro vztah důvěryhodnosti přijímající strany Azure AD.

## <a name="next-steps"></a>Další postup
Další informace o [možnosti přihlášení uživatele](plan-connect-user-signin.md).
