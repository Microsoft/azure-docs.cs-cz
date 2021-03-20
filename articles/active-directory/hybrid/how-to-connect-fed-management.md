---
title: Azure AD Connect – Správa AD FS a přizpůsobení | Microsoft Docs
description: AD FS Správa pomocí Azure AD Connect a přizpůsobení uživatelského AD FS přihlašovacího prostředí s využitím Azure AD Connect a PowerShellu.
keywords: AD FS, ADFS, Správa AD FS, AAD Connect, připojení, přihlášení, AD FS přizpůsobení, oprava důvěryhodnosti, M365, federace, předávající strana
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
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91274108"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Správa a přizpůsobení Active Directory Federation Services (AD FS) pomocí Azure AD Connect
Tento článek popisuje, jak spravovat a přizpůsobovat Active Directory Federation Services (AD FS) (AD FS) pomocí Azure Active Directory (Azure AD) Connect. Zahrnuje taky další běžné AD FS úlohy, které může být potřeba udělat pro kompletní konfiguraci AD FS farmy.

| Téma | Co pokrývá |
|:--- |:--- |
| **Správa AD FS** | |
| [Opravit vztah důvěryhodnosti](#repairthetrust) |Jak opravit vztah důvěryhodnosti federace s Microsoft 365. |
| [Federovat se službou Azure AD s použitím alternativního přihlašovacího ID](#alternateid) | Konfigurace federace pomocí alternativního přihlašovacího ID  |
| [Přidání serveru AD FS](#addadfsserver) |Jak rozšířit AD FSovou farmu s využitím dalšího serveru AD FS. |
| [Přidat AD FS proxy server webové aplikace](#addwapserver) |Jak rozšířit AD FSovou farmu pomocí dalšího serveru proxy webových aplikací (WAP). |
| [Přidání federované domény](#addfeddomain) |Jak přidat federované domény |
| [Aktualizace certifikátu TLS/SSL](how-to-connect-fed-ssl-update.md)| Postup aktualizace certifikátu TLS/SSL pro AD FS farmu |
| **Přizpůsobení AD FS** | |
| [Přidat vlastní logo společnosti nebo obrázek](#customlogo) |Postup přizpůsobení AD FS přihlašovací stránky s logem společnosti a obrázkem. |
| [Přidat popis přihlášení](#addsignindescription) |Přidání popisu přihlašovací stránky |
| [Upravit pravidla deklarace AD FS](#modclaims) |Postup úpravy deklarací identity AD FS pro různé scénáře federace. |

## <a name="manage-ad-fs"></a>Správa AD FS
Pomocí Průvodce Azure AD Connect můžete provádět různé úlohy související s AD FS v Azure AD Connect s minimálním zásahem uživatele. Po dokončení instalace Azure AD Connect spuštěním průvodce můžete spustit průvodce znovu a provést další úlohy.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Opravit vztah důvěryhodnosti 
Azure AD Connect můžete použít ke kontrole aktuálního stavu AD FS a vztahu důvěryhodnosti Azure AD a k provedení příslušných akcí k opravě vztahu důvěryhodnosti. Pomocí těchto kroků opravíte AD FS důvěryhodnosti Azure AD.

1. V seznamu dalších úloh vyberte **opravit AAD a důvěryhodnost služby ADFS** .
   ![Oprava AAD a vztahu důvěryhodnosti služby ADFS](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na stránce **připojit ke službě Azure AD** zadejte přihlašovací údaje globálního správce služby Azure AD a klikněte na **Další**.
   ![Snímek obrazovky zobrazující stránku připojit k Azure AD s ukázkovými přihlašovacími údaji](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na stránce **přihlašovací údaje vzdáleného přístupu** zadejte přihlašovací údaje správce domény.

   ![Snímek obrazovky se stránkou přihlašovací údaje pro vzdálený přístup, která obsahuje příklady zadaných přihlašovacích údajů](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Po kliknutí na tlačítko **další** Azure AD Connect zkontroluje stav certifikátu a zobrazí všechny problémy.

    ![Stav certifikátů](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Stránka **připraveno ke konfiguraci** zobrazuje seznam akcí, které budou provedeny k opravě vztahu důvěryhodnosti.

    ![Snímek obrazovky, na které se zobrazí stránka připraveno ke konfiguraci se seznamem akcí](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Kliknutím na **instalovat** opravíte vztah důvěryhodnosti.

> [!NOTE]
> Azure AD Connect může opravit nebo jednat jenom o certifikáty, které jsou podepsané svým držitelem. Azure AD Connect nemůže opravit certifikáty třetích stran.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federovat se službou Azure AD s využitím AlternateID 
Doporučuje se, aby místní hlavní název uživatele (UPN) a hlavní název uživatele v cloudu byly stejné. Pokud místní hlavní název uživatele (UPN) používá jinou než směrovatelný doménu (např. Contoso. Local) nebo se nedá změnit kvůli závislostem místních aplikací, doporučujeme nastavit alternativní přihlašovací ID. Alternativní ID přihlášení vám umožní nakonfigurovat přihlašovací prostředí, kde se uživatelé můžou přihlašovat pomocí atributu, který je jiný než jeho hlavní název uživatele (UPN), jako je například pošta. Volba pro hlavní název uživatele v Azure AD Connect ve výchozím nastavení nastavena na atribut userPrincipalName ve službě Active Directory. Pokud zvolíte jakýkoli jiný atribut pro hlavní název uživatele a federování je pomocí AD FS, Azure AD Connect se nakonfiguruje AD FS pro alternativní ID přihlášení. Příklad výběru jiného atributu pro hlavní název uživatele je uveden níže:

![Výběr atributu alternativního ID](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurace alternativního přihlašovacího ID pro AD FS se skládá ze dvou hlavních kroků:
1. **Konfigurace správné sady deklarací pro vystavování**: pravidla deklarace identity ve vztahu důvěryhodnosti předávající strany Azure AD se upravují tak, aby jako alternativní ID uživatele používala vybraný atribut userPrincipalName.
2. **Povolit alternativní přihlašovací ID v konfiguraci AD FS**: Konfigurace AD FS se aktualizuje tak, aby AD FS mohli vyhledat uživatele v příslušných doménových strukturách pomocí alternativního ID. Tato konfigurace je podporovaná pro AD FS v systému Windows Server 2012 R2 (s KB2919355) nebo novějším. Pokud jsou servery AD FS 2012 R2, Azure AD Connect zkontroluje přítomnost požadované znalostní báze. Pokud není zjištěna KB, zobrazí se po dokončení konfigurace upozornění, jak je znázorněno níže:

    ![Upozornění pro chybějící KB v 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Pokud chcete opravit konfiguraci v případě chybějících KB, nainstalujte požadované [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) a pak opravte vztah důvěryhodnosti pomocí [opravy AAD a AD FS Trust](#repairthetrust).

> [!NOTE]
> Další informace o alternateID a krocích pro ruční konfiguraci najdete v tématu [Konfigurace alternativního přihlašovacího ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) .

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Přidání serveru AD FS 

> [!NOTE]
> Chcete-li přidat server AD FS, Azure AD Connect vyžaduje certifikát PFX. Proto můžete tuto operaci provést pouze v případě, že jste nakonfigurovali AD FS farmu pomocí Azure AD Connect.

1. Vyberte **nasadit další federační server** a klikněte na **Další**.

   ![Další federační server](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na stránce **připojit ke službě Azure AD** zadejte přihlašovací údaje globálního správce pro Azure AD a klikněte na **Další**.

   ![Snímek obrazovky zobrazující stránku připojit k Azure AD s zadanými ukázkovými přihlašovacími údaji](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Zadejte přihlašovací údaje správce domény.

   ![Přihlašovací údaje správce domény](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect požádá o heslo k souboru PFX, který jste zadali při konfiguraci nové farmy AD FS pomocí Azure AD Connect. Kliknutím na **zadat heslo** zadejte heslo pro soubor PFX.

   ![Snímek obrazovky zobrazující stránku zadat certifikát SSL s otevřeným oknem heslo certifikátu](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Snímek obrazovky, který po zadání hesla pro soubor PFX zobrazuje stránku zadat certifikát SSL.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na stránce **AD FS servery** zadejte název nebo IP adresu serveru, který chcete přidat do farmy AD FS.

   ![AD FS servery](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klikněte na **Další** a přejděte na stránku finální **Konfigurace** . Až Azure AD Connect dokončí přidávání serverů do farmy AD FS, budete mít možnost ověřit připojení.

   ![Snímek obrazovky, na které se zobrazí stránka připraveno ke konfiguraci se seznamem akcí, které mají být dokončeny po kliknutí na tlačítko nainstalovat.](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Snímek obrazovky, na které se zobrazí stránka Instalace byla dokončena, se zobrazí zpráva "konfigurace intranetu byla úspěšně ověřena". ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Přidat AD FS Server WAP 

> [!NOTE]
> Chcete-li přidat server WAP, Azure AD Connect vyžaduje certifikát PFX. Proto můžete tuto operaci provést pouze v případě, že jste nakonfigurovali AD FS farmu pomocí Azure AD Connect.

1. V seznamu dostupných úloh vyberte **nasadit proxy webových aplikací** .

   ![Nasadit proxy webových aplikací](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Zadejte přihlašovací údaje globálního správce Azure.

   ![Snímek obrazovky zobrazující stránku připojit k Azure AD s uvedeným příkladem uživatelského jména a hesla.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na stránce **zadat certifikát SSL** zadejte heslo k souboru PFX, který jste zadali při konfiguraci AD FS farmy pomocí Azure AD Connect.
   ![Heslo certifikátu](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Zadat certifikát TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Přidejte server, který se má přidat jako server WAP. Vzhledem k tomu, že je server WAP pravděpodobně připojen k doméně, Průvodce zobrazí výzvu k zadání přihlašovacích údajů správce do přidávaného serveru.

   ![Přihlašovací údaje serveru pro správu](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na stránce **pověření důvěryhodnosti proxy** zadejte přihlašovací údaje správce pro konfiguraci vztahu důvěryhodnosti proxy serveru a přístup k primárnímu serveru ve farmě AD FS.

   ![Přihlašovací údaje vztahu důvěryhodnosti proxy serveru](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na stránce **připraveno ke konfiguraci** se v průvodci zobrazí seznam akcí, které budou provedeny.

   ![Snímek obrazovky, na které se zobrazí stránka připraveno ke konfiguraci se seznamem akcí, které mají být provedeny.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Kliknutím na **instalovat** dokončete konfiguraci. Po dokončení konfigurace vám průvodce nabídne možnost ověřit připojení k serverům. Kliknutím na **ověřit** zkontrolujte připojení.

   ![Instalace dokončena](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Přidání federované domény 

Pomocí Azure AD Connect můžete snadno přidat doménu, která se bude federované pomocí Azure AD. Azure AD Connect přidá doménu pro federaci a upraví pravidla deklarace identity tak, aby správně odrážela vystavitele, když máte více domén federované s Azure AD.

1. Pokud chcete přidat federované domény, vyberte úlohu **Přidat další doménu služby Azure AD**.

   ![Další doména Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na další stránce průvodce zadejte přihlašovací údaje globálního správce pro Azure AD.

   ![Připojení k Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na stránce **přihlašovací údaje vzdáleného přístupu** zadejte přihlašovací údaje správce domény.

   ![Přihlašovací údaje vzdáleného přístupu](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na další stránce průvodce poskytuje seznam domén Azure AD, pomocí kterých můžete federovat svůj místní adresář. V seznamu vyberte doménu.

   ![Doména Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Po výběru domény vám průvodce poskytne příslušné informace o dalších akcích, které průvodce provede, a o vlivu konfigurace. Pokud v některých případech vyberete doménu, která ve službě Azure AD ještě není ověřená, průvodce vám poskytne informace, které vám pomůžou ověřit doménu. Další podrobnosti najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../fundamentals/add-custom-domain.md) .

5. Klikněte na **Next** (Další). Stránka **připraveno ke konfiguraci** zobrazuje seznam akcí, které Azure AD Connect provede. Kliknutím na **instalovat** dokončete konfiguraci.

   ![Připraveno ke konfiguraci](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Než se budou moct uživatelé z přidané federované domény přihlašovat k Azure AD, musí se synchronizovat.

## <a name="ad-fs-customization"></a>Přizpůsobení AD FS
V následujících částech najdete podrobné informace o některých běžných úlohách, které je třeba provést při přizpůsobování AD FS přihlašovací stránky.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Přidat vlastní logo společnosti nebo obrázek 
Chcete-li změnit logo společnosti zobrazené na **přihlašovací** stránce, použijte následující rutinu prostředí Windows PowerShell a syntaxi.

> [!NOTE]
> Doporučené dimenze pro logo jsou 260 x 35 \@ 96 dpi a velikost souboru nesmí být větší než 10 KB.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> Parametr *TargetName* je povinný. Výchozí motiv vydaný pomocí AD FS se nazývá výchozí.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Přidat popis přihlášení 
K přidání popisu přihlašovací stránky na **přihlašovací stránku** použijte následující rutinu Windows PowerShellu a syntaxi.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Upravit pravidla deklarace AD FS 
AD FS podporuje bohatý jazyk deklarací identity, který můžete použít k vytvoření vlastních pravidel deklarací identity. Další informace najdete v tématu [Role jazyka pravidel deklarací identity](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11)).

Následující části popisují, jak můžete psát vlastní pravidla pro některé scénáře, které se vztahují k Azure AD a AD FS federaci.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Neměnné ID je podmíněné pro hodnotu, která se nachází v atributu.
Azure AD Connect umožňuje zadat atribut, který má být použit jako zdrojový kotva v případě, že jsou objekty synchronizovány do služby Azure AD. Pokud hodnota ve vlastním atributu není prázdná, může být vhodné vystavit neproměnlivou deklaraci ID.

Můžete například vybrat **MS-DS-consistencyguid** jako atribut pro zdrojové ukotvení a vystavení **ImmutableID** jako **MS-DS-consistencyguid** pro případ, že atribut na něj má hodnotu. Pokud není k atributu žádná hodnota, vystavte identifikátor objektu **objectGUID** jako neproměnlivé ID. Můžete vytvořit sadu vlastních pravidel deklarací identity, jak je popsáno v následující části.

**Pravidlo 1: atributy dotazu**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

V tomto pravidle se dotazuje na hodnoty **MS-DS-consistencyguid** a **objectGUID** pro uživatele ze služby Active Directory. V nasazení AD FS změňte název úložiště na příslušný název úložiště. Změňte také typ deklarace identity na správný typ deklarací identity pro vaši federaci, jak je definováno pro **objectGUID** a **MS-DS-consistencyguid**.

Kromě **toho se** můžete vyhnout přidáním odchozího problému pro entitu **a použít** hodnoty jako mezilehlé hodnoty. Tuto deklaraci budete vydávat v pozdějším pravidle po stanovení hodnoty, která se má použít jako neproměnlivé ID.

**Pravidlo 2: Ověřte, jestli pro uživatele existuje ms-DS-consistencyguid.**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Toto pravidlo definuje dočasný příznak s názvem **idflag** , který je nastaven na **useguid** , pokud pro uživatele není naplněno žádné y **MS-DS-consistencyguid** . Logika za tímto je fakt, že AD FS nepovoluje prázdné deklarace identity. Takže když přidáte deklarace identity `http://contoso.com/ws/2016/02/identity/claims/objectguid` a `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` v pravidle 1, skončíte s deklarací **msdsconsistencyguid** jenom v případě, že se hodnota naplní pro uživatele. Pokud není naplněno, AD FS uvidí, že bude mít prázdnou hodnotu a okamžitě ho sníží. Všechny objekty budou mít **objectGUID**, takže po provedení pravidla 1 bude deklarace identity vždycky.

**Pravidlo 3: vystavení ms-DS-consistencyguid jako neproměnlivé ID, pokud je k dispozici**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Toto je implicitní **existence** kontroly. Pokud hodnota deklarace identity existuje, pak ji vydejte jako neproměnlivé ID. Předchozí příklad používá deklaraci identity **NameIdentifier** . Budete je muset změnit na příslušný typ deklarace identity pro neměnné ID ve vašem prostředí.

**Pravidlo 4: vystavení objectGuid jako neměnné ID, pokud není k dispozici služba MS-DS-consistencyGuid.**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

V tomto pravidle jednoduše kontrolujete dočasný příznak **idflag**. Rozhodnete, zda chcete vystavit deklaraci identity na základě její hodnoty.

> [!NOTE]
> Pořadí těchto pravidel je důležité.

### <a name="sso-with-a-subdomain-upn"></a>Jednotné přihlašování s hlavním názvem uživatele subdomény

Pomocí Azure AD Connect můžete přidat více než jednu doménu, jak je popsáno v tématu [Přidání nové federované domény](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect verze 1.1.553.0 a nejnovější vytvoří správné pravidlo deklarace identity pro issuerID automaticky. Pokud nemůžete použít Azure AD Connect verze 1.1.553.0 nebo nejnovější, doporučujeme, abyste pro generování a nastavení správných pravidel deklarací identity pro vztah důvěryhodnosti předávající strany Azure AD použili nástroj [pravidla deklarace identity Azure AD RPT](https://aka.ms/aadrptclaimrules) .

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [možnostech přihlašování uživatelů](plan-connect-user-signin.md).