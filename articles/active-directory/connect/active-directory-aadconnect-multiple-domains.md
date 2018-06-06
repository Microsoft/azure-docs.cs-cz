---
title: Azure AD Connect více domén
description: Tento dokument popisuje nastavení a konfiguraci víc domén nejvyšší úrovně s O365 a Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2f596f64041b3d429b99db482cd635ab441bf468
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801503"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Podpora více domén pro federaci s Azure AD
Následující dokumentace obsahuje pokyny k použití více domén nejvyšší úrovně a subdomény při federaci s Office 365 nebo Azure AD domény.

## <a name="multiple-top-level-domain-support"></a>Podpora více domén nejvyšší úrovně
Federaci několika, domény nejvyšší úrovně s Azure AD vyžaduje určitou další konfiguraci, který není nutný, pokud federaci s jednu doménu nejvyšší úrovně.

Když domény federovaný pomocí služby Azure AD, několik vlastností, které jsou nastavené na doménu v Azure.  Jeden z nich důležité je IssuerUri.  Tato vlastnost je identifikátor URI, který se používá Azure AD k identifikaci domény, který je přidružený token.  Identifikátor URI nepotřebuje přeložit na cokoli ale musí být platný identifikátor URI.  Ve výchozím nastavení, Azure AD Nastaví identifikátor URI na hodnotu identifikátor federační služby v místní službě AD FS konfigurace.

> [!NOTE]
> Identifikátor služby FS je identifikátor URI, který jednoznačně identifikuje federační služby.  Služby federation service je instance služby AD FS, která slouží jako služba tokenů zabezpečení.
>
>

Můžete zobrazit pomocí příkazu Powershellu IssuerUri `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Problému dojde, když přidáte více než jedné domény nejvyšší úrovně.  Předpokládejme například, že jste nastavili federace mezi službou Azure AD a místní prostředí.  K tomuto dokumentu, domény, je používán bmcontoso.com.  Nyní druhou, nejvyšší úrovně domény, bmfabrikam.com byla přidána.

![Domény](./media/active-directory-multiple-domains/domains.png)

Při pokusu o převod bmfabrikam.com domény na federovanou dojde k chybě.  Důvodem je, Azure AD má omezení, který neumožňuje vlastnost IssuerUri do mají stejnou hodnotu pro více než jedné domény.  

![Chyba Federation](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parametr SupportMultipleDomain
Toto omezení obejít, je nutné přidat jiné IssuerUri, což lze provést pomocí `-SupportMultipleDomain` parametr.  Tento parametr se používá s následující rutiny:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Tento parametr umožňuje nakonfigurovat IssuerUri tak, aby je založen na názvu domény služby Azure AD.  IssuerUri bude jedinečný mezi adresářů ve službě Azure AD.  Pomocí parametru umožňuje příkaz prostředí PowerShell úspěšně dokončit.

![Chyba Federation](./media/active-directory-multiple-domains/convert.png)

Prohlížení nastavení bmfabrikam.com domény můžete v tomto tématu:

![Chyba Federation](./media/active-directory-multiple-domains/settings.png)

`-SupportMultipleDomain` Další koncové body, které jsou stále nakonfigurované tak, aby odkazoval na federační službu na adfs.bmcontoso.com nezmění.

Dalším krokem, `-SupportMultipleDomain` nemá je, že zajišťuje, že v systému služby AD FS obsahuje správnou hodnotu vystavitele v tokeny vydané pro Azure AD. Tato hodnota nastavena tak, že trvá část domény uživatelů (UPN) a jeho nastavení jako doménu v IssuerUri, tj. https://{upn příponu} / adfs/services/vztah důvěryhodnosti.

Proto při ověřování do služby Azure AD nebo Office 365, element IssuerUri v token uživatele se používá k vyhledání domény ve službě Azure AD.  Pokud odpovídající nelze nalézt, ověření se nezdaří.

Například pokud uživatele (UPN) je bsimon@bmcontoso.com, element IssuerUri v tokenu služby AD FS problémy, bude nastavena pro http://bmcontoso.com/adfs/services/trust. Tento prvek bude odpovídat konfiguraci Azure AD a ověřování bude úspěšný.

Toto je pravidlo vlastní deklarace identity, který implementuje tuto logiku:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Chcete-li použít přepínač - SupportMultipleDomain při pokusu o můžete přidat nové nebo převést již existující domény, musí vaše federovaného vztahu důvěryhodnosti již byl nastaven na je podporují.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Postup aktualizace vztah důvěryhodnosti mezi AD FS a Azure
Pokud jste nenastavili federovaný vztah důvěryhodnosti mezi AD FS a vaší instanci Azure AD, musíte znovu vytvořit tohoto vztahu důvěryhodnosti.  Důvodem je, když původně je nastavený bez `-SupportMultipleDomain` parametr, výchozí hodnotou je nastaven IssuerUri.  Na tomto snímku obrazovky, uvidíte IssuerUri je nastaven na https://adfs.bmcontoso.com/adfs/services/trust.

Pokud jste úspěšně přidali novou doménu na portálu Azure AD a pak se pokusíte převést pomocí `Convert-MsolDomaintoFederated -DomainName <your domain>`, zobrazí se následující chyba.

![Chyba Federation](./media/active-directory-multiple-domains/trust1.png)

Pokud se pokusíte přidat `-SupportMultipleDomain` přepnout, zobrazí následující chybová zpráva:

![Chyba Federation](./media/active-directory-multiple-domains/trust2.png)

Jednoduše pokusu o spuštění `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` v původní doméně také dojde chybě.

![Chyba Federation](./media/active-directory-multiple-domains/trust3.png)

Použijte uvedený postup pro přidání další domény nejvyšší úrovně.  Pokud jste již přidali domény a nepoužili `-SupportMultipleDomain` parametr, začněte s kroky pro odebírání a aktualizace vašeho původního domény.  Pokud zatím jste nepřidali doména nejvyšší úrovně, můžete spustit s kroky pro přidání domény pomocí prostředí PowerShell Azure AD Connect.

Pomocí následujících kroků odeberte vztah důvěryhodnosti Microsoft Online a aktualizujte vaší původní doméně.

1. Na federačním serveru služby AD FS otevřete **Správa služby AD FS.**
2. Na levé straně, rozbalte položku **vztahy důvěryhodnosti** a **vztahy důvěryhodnosti předávající strany**
3. Na pravé straně, odstraňte **Microsoft Office 365 Identity Platform** položku.
   ![Odeberte Online Microsoft](./media/active-directory-multiple-domains/trust4.png)
4. Na počítači, který má [Azure Active Directory modul pro prostředí Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) nainstalován spusťte následující příkaz: `$cred=Get-Credential`.  
5. Zadejte uživatelské jméno a heslo pro globálního správce pro doménu služby Azure AD, které jsou federaci s.
6. V prostředí PowerShell zadejte `Connect-MsolService -Credential $cred`
7. V prostředí PowerShell, zadejte `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Tato aktualizace je pro původní doméně.  Proto pomocí výše uvedených domén, které má být:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Použijte následující postup pro přidání nové domény nejvyšší úrovně pomocí prostředí PowerShell

1. Na počítači, který má [Azure Active Directory modul pro prostředí Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) nainstalován spusťte následující příkaz: `$cred=Get-Credential`.  
2. Zadejte uživatelské jméno a heslo pro globálního správce pro doménu služby Azure AD, které jsou federaci s
3. V prostředí PowerShell zadejte `Connect-MsolService -Credential $cred`
4. V prostředí PowerShell zadejte `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Použijte následující postup pro přidání nové domény nejvyšší úrovně pomocí služby Azure AD Connect.

1. Spusťte Azure AD Connect z plochy a nabídky start
2. Zvolte "Přidat další domény Azure AD" ![přidat další doménu služby Azure AD](./media/active-directory-multiple-domains/add1.png)
3. Zadejte služby Azure AD a pověření služby Active Directory
4. Vyberte druhý doménu, kterou chcete nakonfigurovat pro federaci.
   ![Přidat další doménu služby Azure AD](./media/active-directory-multiple-domains/add2.png)
5. Kliknutí na Nainstalovat

### <a name="verify-the-new-top-level-domain"></a>Ověření nové domény nejvyšší úrovně
Pomocí příkazu prostředí PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`můžete zobrazit aktualizované IssuerUri.  Následující snímek obrazovky ukazuje na federaci, nastavení bylo aktualizováno v původní doméně http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

A IssuerUri v nové doméně byla nastavena na https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Podpora pro subdomény
Když přidáte subdoména, z důvodu domén způsobem Azure AD, které jsou zpracovány, zdědí nastavení nadřazeného objektu.  Ano IssuerUri, musí odpovídat nadřazené položky.

Ano umožňuje Řekněme například, že mám bmcontoso.com a poté přidejte corp.bmcontoso.com.  Bude nutné IssuerUri pro uživatele z corp.bmcontoso.com  **http://bmcontoso.com/adfs/services/trust.**  Ale standardní pravidlo implementována výše pro Azure AD, vygeneruje token s vystavitele jako  **http://corp.bmcontoso.com/adfs/services/trust.** ověření se nezdaří a která nebude shodovala s doménou vyžadované hodnotu.

### <a name="how-to-enable-support-for-subdomains"></a>Povolení podpory pro subdomény
Chcete-li tento problém vyřešit, je třeba aktualizovat vztah důvěryhodnosti předávající strany pro Microsoft Online služby AD FS.  To pokud chcete udělat, musíte nakonfigurovat vlastní pravidlo deklarace identity, aby ji odstraní vypnout všechny subdomény od uživatele (UPN) příponu při vytváření vlastní hodnotu vystavitele.

Následující deklarace identity se to udělat:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Poslední číslo v sadě regulární výraz je, kolik domény nadřazené jsou v kořenové doméně. Tady se používá bmcontoso.com, proto jsou nezbytné nadřazené domény. V případě, že tři nadřazené domény byly budou muset zůstat (tj.: corp.bmcontoso.com), pak číslo by byl tři. Nakonec rozsah můžete zadat, shody bude vždy provést tak, aby odpovídala maximální počet domén. "{2,3}" bude odpovídat dvě až tři domény (např: bmfabrikam.com a corp.bmcontoso.com).

Pomocí následujících kroků přidáte vlastních deklarací identity pro podporu subdomény.

1. Správa služby otevřete AD FS
2. Klikněte pravým tlačítkem na vztah důvěryhodnosti Microsoft Online RP a zvolte pravidla upravit deklarace identity
3. Vyberte třetí pravidlo deklarace identity a nahraďte ![úpravy deklarací identity](./media/active-directory-multiple-domains/sub1.png)
4. Nahraďte aktuální deklarace identity:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Nahraďte deklarace identity](./media/active-directory-multiple-domains/sub2.png)

5. Klikněte na tlačítko Ok.  Kliknutím na tlačítko použít.  Klikněte na tlačítko Ok.  Zavřete správu služby AD FS.

## <a name="next-steps"></a>Další postup
Nyní, když máte nainstalovanou službu Azure AD Connect, si můžete [ověřit instalaci a přiřadit licence](active-directory-aadconnect-whats-next.md).

Zjistěte více o těchto funkcích, které byly povoleny v rámci instalace: [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md), [Prevence náhodných odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a spouštění synchronizace](active-directory-aadconnectsync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).