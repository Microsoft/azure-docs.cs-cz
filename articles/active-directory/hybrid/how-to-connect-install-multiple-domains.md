---
title: Azure AD připojit více domén
description: Tento dokument popisuje nastavení a konfiguraci více domén nejvyšší úrovně pomocí O365 a Azure AD.
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
ms.openlocfilehash: 18b5f19e3e994aa05fa99caf360d0c1be69ec7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049776"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Podpora více domén pro federaci s Azure AD
Následující dokumentace obsahuje pokyny, jak používat více domén nejvyšší úrovně a subdomén při federace s doménami Office 365 nebo Azure AD.

## <a name="multiple-top-level-domain-support"></a>Podpora více domén nejvyšší úrovně
Federating více domén nejvyšší úrovně s Azure AD vyžaduje některé další konfigurace, která není vyžadována při federace s jednou doménou nejvyšší úrovně.

Když je doména federovaná pomocí Azure AD, několik vlastností se na staví v doméně v Azure.  Jedním z důležitých je IssuerUri.  Tato vlastnost je identifikátor URI, který používá Azure AD k identifikaci domény, ke které je token přidružen.  Identifikátor URI nemusí nic přeložit, ale musí se jednat o platný identifikátor URI.  Ve výchozím nastavení Azure AD nastaví identifikátor URI na hodnotu identifikátoru federační služby v místní konfiguraci služby AD FS.

> [!NOTE]
> Identifikátor federační služby je identifikátor URI, který jednoznačně identifikuje federační službu.  Federační služba je instancí služby AD FS, která funguje jako služba tokenů zabezpečení.
>
>

IssuerUri můžete zobrazit pomocí příkazu `Get-MsolDomainFederationSettings -DomainName <your domain>`PowerShell .

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Problém nastává, když přidáte více než jednu doménu nejvyšší úrovně.  Řekněme například, že jste nastavili federaci mezi Azure AD a místním prostředím.  Pro tento dokument se používá doména bmcontoso.com.  Nyní byla přidána druhá doména nejvyšší úrovně, bmfabrikam.com.

![Domény](./media/how-to-connect-install-multiple-domains/domains.png)

Při pokusu o převod bmfabrikam.com domény, která má být federována, dojde k chybě.  Důvodem je, Azure AD má omezení, které neumožňuje IssuerUri vlastnost mít stejnou hodnotu pro více než jednu doménu.  

![Chyba federace](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain Parametr
Chcete-li obejít toto omezení, je třeba přidat jiný IssuerUri, které lze provést pomocí parametru. `-SupportMultipleDomain`  Tento parametr se používá s následujícími rutinami:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Tento parametr umožňuje Azure AD konfigurovat IssuerUri tak, aby je založen na názvu domény.  IssuerUri bude jedinečný napříč adresáři ve službě Azure AD.  Použití parametru umožňuje úspěšné dokončení příkazu Prostředí PowerShell.

![Chyba federace](./media/how-to-connect-install-multiple-domains/convert.png)

Při pohledu na nastavení bmfabrikam.com domény můžete vidět následující:

![Chyba federace](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`nezmění ostatní koncové body, které jsou stále nakonfigurovány tak, aby ukazovaly na službu federation service v adfs.bmcontoso.com.

Další věc, která `-SupportMultipleDomain` dělá, je, že zajišťuje, že systém AD FS obsahuje správnou hodnotu vystavittele v tokenech vydaných pro Azure AD. Tato hodnota je nastavena tak, že se část domény hlavního názvu uživatele a nastavení jako domény v IssuerUri, tj.

Proto při ověřování azure ad nebo Office 365, IssuerUri prvek v tokenu uživatele se používá k vyhledání domény ve službě Azure AD.  Pokud shoda nebyla nalezena, ověření se nezdaří.

Například pokud je bsimon@bmcontoso.comhlavní název uživatele , issuerUri prvek v tokenu, ad FS problémy, bude nastavena na `http://bmcontoso.com/adfs/services/trust`. Tento prvek bude odpovídat konfiguraci Azure AD a ověřování bude úspěšné.

Následuje vlastní pravidlo deklarace, které implementuje tuto logiku:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Chcete-li použít přepínač -SupportMultipleDomain při pokusu o přidání nových nebo převést již existující domény, federované důvěryhodnosti musí již byly nastaveny na jejich podporu.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak aktualizovat vztah důvěryhodnosti mezi službou AD FS a službou Azure AD
Pokud jste nenastavili federovaný vztah důvěryhodnosti mezi službou AD FS a vaší instancí služby Azure AD, bude pravděpodobně nutné tento vztah důvěryhodnosti znovu vytvořit.  Důvodem je, když je původně `-SupportMultipleDomain` nastaven bez parametru, IssuerUri je nastaven a výchozí hodnotu.  Na následujícím snímku obrazovky můžete vidět, že `https://adfs.bmcontoso.com/adfs/services/trust`Je nastavena na .

Pokud jste úspěšně přidali novou doménu na portálu Azure AD a pak se pokusíte převést pomocí `Convert-MsolDomaintoFederated -DomainName <your domain>`, zobrazí se následující chyba.

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust1.png)

Pokud se pokusíte `-SupportMultipleDomain` přidat přepínač, zobrazí se následující chyba:

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust2.png)

Jednoduše se `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` snaží spustit na původní doméně bude také mít za následek chybu.

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust3.png)

Pomocí následujících kroků přidejte další doménu nejvyšší úrovně.  Pokud jste již přidali doménu a `-SupportMultipleDomain` nepoužili jste parametr, začněte kroky pro odebrání a aktualizaci původní domény.  Pokud jste ještě nepřidali doménu nejvyšší úrovně, můžete začít s postupem přidání domény pomocí PowerShellu Azure AD Connect.

Pomocí následujících kroků odeberte důvěryhodnost služby Microsoft Online a aktualizujte původní doménu.

1. Na federačním serveru služby AD FS otevřete **službu AD FS Management.**
2. Vlevo rozbalte **vztahy důvěryhodnosti** a **vztahy důvěryhodnosti předávající strany**
3. Vpravo odstraňte položku **Microsoft Office 365 Identity Platform.**
   ![Odebrání služby Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. V počítači s nainstalovaným [modulem Azure Active Directory Module pro Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) je spuštěno následující: `$cred=Get-Credential`.  
5. Zadejte uživatelské jméno a heslo globálního správce domény Azure AD, se kterou federování.
6. V PowerShellu zadejte`Connect-MsolService -Credential $cred`
7. V prostředí PowerShell zadejte `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Tato aktualizace je pro původní doménu.  Takže pomocí výše uvedených domén by bylo:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Pomocí následujících kroků přidejte novou doménu nejvyšší úrovně pomocí Prostředí PowerShell

1. V počítači s nainstalovaným [modulem Azure Active Directory Module pro Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) je spuštěno následující: `$cred=Get-Credential`.  
2. Zadejte uživatelské jméno a heslo globálního správce domény Azure AD, se kterou federování snímáte.
3. V PowerShellu zadejte`Connect-MsolService -Credential $cred`
4. V PowerShellu zadejte`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Pomocí následujících kroků přidejte novou doménu nejvyšší úrovně pomocí služby Azure AD Connect.

1. Spuštění služby Azure AD Connect z nabídky plochy nebo start
2. Zvolte "Přidat další doménu ![Azure AD" Přidání další domény Azure AD](./media/how-to-connect-install-multiple-domains/add1.png)
3. Zadání přihlašovacích údajů služby Azure AD a služby Active Directory
4. Vyberte druhou doménu, kterou chcete nakonfigurovat pro federaci.
   ![Přidání další domény Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kliknutí na Nainstalovat

### <a name="verify-the-new-top-level-domain"></a>Ověření nové domény nejvyšší úrovně
Pomocí příkazu `Get-MsolDomainFederationSettings -DomainName <your domain>`PowerShell můžete zobrazit aktualizovaný IssuerUri.  Následující snímek obrazovky ukazuje, že nastavení federace byla aktualizována v původní doméně`http://bmcontoso.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

A IssuerUri na nové doméně byla nastavena na`https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Podpora subdomén
Když přidáte subdoménu, kvůli způsobu, jakým Azure AD zpracovává domény, zdědí nastavení nadřazené.  Takže, IssuerUri, musí odpovídat rodičům.

Řekněme například, že jsem bmcontoso.com a pak přidat corp.bmcontoso.com.  IssuerUri pro uživatele z corp.bmcontoso.com bude muset být ** http://bmcontoso.com/adfs/services/trust.**  Však standardní pravidlo implementované výše pro Azure AD, vygeneruje token s vystavitelem jako ** http://corp.bmcontoso.com/adfs/services/trust.** která nebude odpovídat požadované hodnotě domény a ověřování se nezdaří.

### <a name="how-to-enable-support-for-subdomains"></a>Jak povolit podporu pro subdomény
Chcete-li toto chování obejít, je třeba aktualizovat důvěryhodný certifikát předávající strany služby AD FS pro službu Microsoft Online.  Chcete-li to provést, je nutné nakonfigurovat vlastní pravidlo deklarace tak, aby při vytváření vlastní hodnoty vystavitního objektu odstranilo všechny subdomény z přípony upn uživatele.

Následující nárok bude dělat toto:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Poslední číslo v sadě regulárních výrazů je, kolik nadřazených domén je v kořenové doméně. Zde bmcontoso.com se používá, takže jsou nezbytné dvě nadřazené domény. Pokud by měly být zachovány tři nadřazené domény (tj. corp.bmcontoso.com), pak by číslo bylo tři. Nakonec může být indikován rozsah, shoda bude vždy provedena tak, aby odpovídala maximálnímu počtu domén. "{2,3}" bude odpovídat dvěma až třem doménám (tj.: bmfabrikam.com a corp.bmcontoso.com).

Pomocí následujících kroků přidejte vlastní deklaraci pro podporu subdomén.

1. Otevřít správu ad FS
2. Klikněte pravým tlačítkem myši na důvěryhodnost microsoft online rp a zvolte Upravit pravidla deklarace.
3. Vyberte třetí pravidlo deklarace a nahraďte ![upravit nárok.](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Nahradit aktuální nárok:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Nahradit nárok](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klikněte na Ok.  Klikněte na Použít.  Klikněte na Ok.  Zavřete správu služby AD FS.

## <a name="next-steps"></a>Další kroky
Nyní, když máte nainstalovanou službu Azure AD Connect, si můžete [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Zjistěte více o těchto funkcích, které byly povoleny v rámci instalace: [Automatický upgrade](how-to-connect-install-automatic-upgrade.md), [Prevence náhodných odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a spouštění synchronizace](how-to-connect-sync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
