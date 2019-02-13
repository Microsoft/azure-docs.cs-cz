---
title: Více domén pro Azure AD Connect
description: Tento dokument popisuje nastavení a konfiguraci více domén nejvyšší úrovně s O365 a Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cde6f9d17eb44fefae10d8694a89abf51540a5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182049"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Podpora více domén pro federaci s Azure AD
Následující dokumentace obsahuje pokyny týkající se použití více domén nejvyšší úrovně a subdomény, při federaci s Office 365 nebo k doménám Azure AD.

## <a name="multiple-top-level-domain-support"></a>Podpora více domén nejvyšší úrovně
Federování několika, domény nejvyšší úrovně s Azure AD vyžaduje určitou další konfiguraci, který není nutný, když federování s využitím jedné domény nejvyšší úrovně.

Při domény je Federovaná pomocí služby Azure AD, jsou na doméně v Azure nastavit několik vlastností.  Jeden z nich důležité je IssuerUri.  Tato vlastnost je identifikátor URI, který se používá Azure AD k identifikaci domény, který je přidružen token.  Identifikátor URI není nutné přeložit na jakoukoli jinou musí být platný identifikátor URI.  Ve výchozím nastavení, Azure AD Nastaví identifikátor URI k hodnotě identifikátor federační služby ve vaší místní služby AD FS konfigurace.

> [!NOTE]
> Identifikátor služby FS je identifikátor URI, který jednoznačně identifikuje federační služby.  Služba federation service je instance služby AD FS, který funguje jako služba tokenů zabezpečení.
>
>

Pomocí příkazu Powershellu můžete zobrazit IssuerUri `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

K problému dochází při přidání více domén nejvyšší úrovně.  Například Řekněme, že jste nastavili federace mezi službami Azure AD a v místním prostředí.  Pro tento dokument, domény, se používá bmcontoso.com.  Nyní druhou nejvyšší úrovně domény, byl přidán bmfabrikam.com.

![Domény](./media/how-to-connect-install-multiple-domains/domains.png)

Při pokusu o převod bmfabrikam.com domény k federaci, dojde k chybě.  Důvodem je, Azure AD má omezení, který nepovoluje vlastnost IssuerUri mít stejnou hodnotu pro více než jedné domény.  

![Chyba federace](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parametr SupportMultipleDomain
Toto omezení obejít, budete muset přidat jiný IssuerUri, která se dá dělat pomocí `-SupportMultipleDomain` parametru.  Tento parametr se používá s použitím následujících rutin:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Tento parametr umožňuje nakonfigurovat IssuerUri tak, aby je založen na názvu domény služby Azure AD.  IssuerUri bude jedinečný mezi adresáře ve službě Azure AD.  Pomocí parametru umožňuje příkaz prostředí PowerShell úspěšně dokončil.

![Chyba federace](./media/how-to-connect-install-multiple-domains/convert.png)

Prohlížení nastavení bmfabrikam.com domény můžete v tomto tématu:

![Chyba federace](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` ostatní koncové body, které jsou pořád nakonfigurované tak, aby odkazoval na federační službu na adfs.bmcontoso.com nezmění.

Další věc, která `-SupportMultipleDomain` nemá je, že zajišťuje, že systému služby AD FS obsahuje správnou hodnotu Issuer v tokenů vydaných pro službu Azure AD. Tato hodnota nastavena podle trvá doména uživatele (UPN) a nastavíte ho jako doménu v IssuerUri, to znamená https://{upn přípona} / adfs/services/vztah důvěryhodnosti.

Proto při ověřování do služby Azure AD nebo Office 365, IssuerUri element v tokenu uživatele se používá k vyhledání domény ve službě Azure AD.  Pokud je nalezena shoda, ověření se nezdaří.

Například pokud uživatele (UPN) je bsimon@bmcontoso.com, IssuerUri element v tokenu služby AD FS problémy, bude nastavena na http://bmcontoso.com/adfs/services/trust. Tento prvek bude odpovídat konfiguraci Azure AD a ověřování bude úspěšné.

Pravidla vlastní deklarace identity, který implementuje tuto logiku je následující:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Chcete-li použít přepínač – SupportMultipleDomain při pokusu o můžete přidat nové nebo převést již existující domény, musí federovanému vztahu důvěryhodnosti již byl nastaven na jejich podporu.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak aktualizovat vztah důvěryhodnosti mezi AD FS a Azure AD
Pokud jste nenastavili federovaný vztah důvěryhodnosti mezi AD FS a vaší instancí Azure AD, budete muset znovu vytvořit tento vztah důvěryhodnosti.  Důvodem je, když ho původně nastavovat bez `-SupportMultipleDomain` parametr, výchozí hodnotou je nastaven IssuerUri.  Na snímku obrazovky níže, uvidíte IssuerUri je nastavena na https://adfs.bmcontoso.com/adfs/services/trust.

Pokud jste úspěšně přidali novou doménu na portálu Azure AD a pak se pokusíte převést pomocí `Convert-MsolDomaintoFederated -DomainName <your domain>`, zobrazí se následující chyba.

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust1.png)

Pokud se pokusíte přidat `-SupportMultipleDomain` přepnout, zobrazí se následující chybová zpráva:

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust2.png)

Jednoduše pokusu o spuštění `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` v původní doméně také způsobí chybu.

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust3.png)

Pomocí následujících kroků přidáte další domény nejvyšší úrovně.  Pokud jste už přidali domény a nepoužívá `-SupportMultipleDomain` parametr, začněte s kroky pro odebrání a aktualizuje váš původním domény.  Pokud ještě jste nepřidali domény nejvyšší úrovně, můžete začít s kroky pro přidání domény pomocí prostředí PowerShell Azure AD Connect.

Následujícím postupem odeberte vztah důvěryhodnosti Microsoft Online a aktualizovat váš původním domény.

1. Na federačním serveru AD FS otevřete **Správa služby AD FS.**
2. Na levé straně rozbalte **vztahy důvěryhodnosti** a **vztahy důvěryhodnosti předávající strany**
3. Na pravé straně, odstranit **platforma identit Microsoft Office 365** položka.
   ![Odebrání Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Na počítači, který má [modulu Azure Active Directory pro Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) nainstalované na něm spusťte následující příkaz: `$cred=Get-Credential`.  
5. Zadejte uživatelské jméno a heslo globálního správce pro doménu služby Azure AD, které jsou federování s využitím.
6. V prostředí PowerShell zadejte `Connect-MsolService -Credential $cred`
7. V prostředí PowerShell, zadejte `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Tato aktualizace je v původní doméně.  Pomocí výše uvedených domén, které by bylo:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Pomocí následujícího postupu přidat novou doménu nejvyšší úrovně pomocí Powershellu

1. Na počítači, který má [modulu Azure Active Directory pro Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) nainstalované na něm spusťte následující příkaz: `$cred=Get-Credential`.  
2. Zadejte uživatelské jméno a heslo globálního správce pro doménu služby Azure AD, které jsou federování s využitím
3. V prostředí PowerShell zadejte `Connect-MsolService -Credential $cred`
4. V prostředí PowerShell zadejte `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Chcete-li přidat novou doménu nejvyšší úrovně pomocí Azure AD Connect, postupujte následovně.

1. Spuštění služby Azure AD Connect z plochy nebo nabídka start
2. Zvolte "Přidat další domény Azure AD" ![přidat další domény Azure AD](./media/how-to-connect-install-multiple-domains/add1.png)
3. Zadejte vaši službu Azure AD a pověření služby Active Directory
4. Vyberte druhou doménu, kterou chcete nakonfigurovat pro federaci.
   ![Přidat další domény Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kliknutí na Nainstalovat

### <a name="verify-the-new-top-level-domain"></a>Ověření nové domény nejvyšší úrovně
Pomocí příkazu Powershellu `Get-MsolDomainFederationSettings -DomainName <your domain>`můžete zobrazit aktualizované IssuerUri.  Následující snímek obrazovky ilustruje federace, nastavení bylo aktualizováno v původní doméně http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

A byla nastavena IssuerUri v nové doméně https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Podpora pro subdomény
Při přidání subdomény, z důvodu domén zpracovány stejně, jako Azure AD, zdědí nastavení nadřazeného objektu.  IssuerUri, tedy musí odpovídat rodiče.

Umožňuje tak Řekněme například, že mám bmcontoso.com a pak přidejte corp.bmcontoso.com.  IssuerUri pro uživatele corp.bmcontoso.com bude muset být  **http://bmcontoso.com/adfs/services/trust.**  Ale standardních pravidel implementované výše pro službu Azure AD, bude generovat token s vystavitele jako  **http://corp.bmcontoso.com/adfs/services/trust.** které nebudou odpovídat domény vyžaduje hodnotu a ověřování se nezdaří.

### <a name="how-to-enable-support-for-subdomains"></a>Jak povolit podporu pro subdomény
Pokud chcete tento problém vyřešit, je potřeba aktualizovat vztah důvěryhodnosti předávající strany pro Microsoft Online služby AD FS.  Chcete-li to provést, musíte nakonfigurovat vlastní pravidlo deklarace identity tak, aby odstraní vypnout všechny subdomény, které od uživatele (UPN) příponu při vytváření vlastní hodnotu Issuer.

Následující deklarace identity provede toto:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Poslední číslo v sadě regulárních výrazů jsou kolik nadřazené domény existuje v kořenové doméně. Tady se používá bmcontoso.com, takže jsou nezbytné nadřazené domény. Pokud tři nadřazené domény byly uchovávat (např: corp.bmcontoso.com), pak číslo by byl tři. Nakonec rozsah mohou být označeny, shoda vždy provést tak, aby odpovídaly maximální počet domén. "{2,3}" bude odpovídat dvě až tři domény (např: bmfabrikam.com a corp.bmcontoso.com).

Pomocí následujících kroků přidáte vlastních deklarací identity pro podporu subdomény.

1. Správa služby otevřete AD FS
2. Klikněte pravým tlačítkem na vztah důvěryhodnosti předávající strany Online společnosti Microsoft a vybrat pravidla úpravy deklarací identity
3. Vyberte třetí pravidlo deklarace identity a nahraďte ![úpravy deklarací identity](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Nahraďte aktuální deklarace identity:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Nahradit deklarace identity](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klikněte na tlačítko Ok.  Kliknutím na tlačítko použít.  Klikněte na tlačítko Ok.  Zavřete správu služby AD FS.

## <a name="next-steps"></a>Další postup
Nyní, když máte nainstalovanou službu Azure AD Connect, můžete si [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Další informace o těchto funkcích, které byly povoleny v rámci instalace: [Automatický upgrade](how-to-connect-install-automatic-upgrade.md), [prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md), a [Azure AD Connect Health](how-to-connect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a jak aktivovat synchronizaci](how-to-connect-sync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
