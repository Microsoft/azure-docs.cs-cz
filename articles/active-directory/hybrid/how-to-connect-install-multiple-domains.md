---
title: Azure AD Connect více domén
description: Tento dokument popisuje nastavení a konfiguraci více domén nejvyšší úrovně pomocí Microsoft 365 a Azure AD.
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
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306357"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Podpora více domén pro federaci s Azure AD
Následující dokumentace poskytuje pokyny k používání více domén nejvyšší úrovně a subdomén při federováníí Microsoft 365 s doménami služby Azure AD.

## <a name="multiple-top-level-domain-support"></a>Podpora více domén na nejvyšší úrovni
Federování více domén nejvyšší úrovně s Azure AD vyžaduje určitou další konfiguraci, která se při federování s jednou doménou nejvyšší úrovně nevyžaduje.

Když je doména federované pomocí Azure AD, v doméně v Azure se nastaví několik vlastností.  Jedna důležitá je IssuerUri.  Tato vlastnost je identifikátor URI, který používá služba Azure AD k identifikaci domény, ke které je token přidružený.  Identifikátor URI není nutné překládat na cokoli, ale musí se jednat o platný identifikátor URI.  Ve výchozím nastavení Azure AD nastaví identifikátor URI na hodnotu identifikátoru federační služby v konfiguraci vaší místní AD FS.

> [!NOTE]
> Identifikátor federační služby je identifikátor URI, který jedinečně identifikuje službu FS (Federation Service).  Služba FS (Federation Service) je instance AD FS, která funguje jako služba tokenů zabezpečení.
>
>

IssuerUri můžete zobrazit pomocí příkazu PowerShellu `Get-MsolDomainFederationSettings -DomainName <your domain>` .

![Snímek obrazovky, který zobrazuje výsledky po zadání příkazu Get-MsolDomainFederationSettings v prostředí PowerShell.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

K problému dojde, když přidáte více než jednu doménu nejvyšší úrovně.  Řekněme například, že jste nastavili federaci mezi Azure AD a vaším místním prostředím.  Pro tento dokument se používá doména bmcontoso.com.  Teď se přidala druhá doména nejvyšší úrovně, bmfabrikam.com.

![Snímek obrazovky zobrazující více domén nejvyšší úrovně](./media/how-to-connect-install-multiple-domains/domains.png)

Když se pokusíte převést doménu bmfabrikam.com na federované, dojde k chybě.  Důvodem je, že Azure AD má omezení, které neumožňuje, aby vlastnost IssuerUri měla stejnou hodnotu pro více než jednu doménu.  

![Snímek obrazovky znázorňující chybu federace v PowerShellu](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parametr SupportMultipleDomain
Pokud chcete toto omezení obejít, musíte přidat jiný IssuerUri, který se dá provést pomocí `-SupportMultipleDomain` parametru.  Tento parametr se používá s následujícími rutinami:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Tento parametr zpřístupňuje IssuerUri Azure AD, aby byl založen na názvu domény.  IssuerUri bude jedinečný napříč adresáři v Azure AD.  Použití parametru umožňuje úspěšné dokončení příkazu PowerShellu.

![Snímek obrazovky, který zobrazuje úspěšné dokončení příkazu prostředí PowerShell.](./media/how-to-connect-install-multiple-domains/convert.png)

Podívejte se na nastavení domény bmfabrikam.com, kde vidíte následující:

![Snímek obrazovky, který zobrazuje nastavení pro doménu "bmfabrikam.com".](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` nemění ostatní koncové body, které jsou pořád nakonfigurované tak, aby odkazovaly na službu Federation Service v adfs.bmcontoso.com.

Dalším krokem `-SupportMultipleDomain` je to, že zajišťuje, aby systém AD FS zahrnoval v tokenech vydaných pro Azure AD správnou hodnotu vystavitele. Tato hodnota se nastaví tak, že se postará o část domény hlavního názvu uživatele (UPN) uživatelů a nastaví se jako doména v IssuerUri, tj. https://{přípona UPN}/ADFS/Services/Trust.

Proto při ověřování do služby Azure AD nebo Microsoft 365 se k vyhledání domény v Azure AD použije element IssuerUri v tokenu uživatele. Pokud není nalezena shoda, ověřování se nezdaří.

Pokud je například hlavní název uživatele (UPN) bsimon@bmcontoso.com , element IssuerUri v tokenu, AD FS problémy, bude nastaven na `http://bmcontoso.com/adfs/services/trust` . Tento element se bude shodovat s konfigurací Azure AD a ověření proběhne úspěšně.

Následuje vlastní pravidlo deklarace identity, které implementuje tuto logiku:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> Aby bylo možné použít přepínač-SupportMultipleDomain při pokusu o přidání nových nebo převádění již existujících domén, musí být federovaný vztah důvěryhodnosti již nastaven na podporu.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Jak aktualizovat vztah důvěryhodnosti mezi AD FS a Azure AD
Pokud jste nevytvořili federovaný vztah důvěryhodnosti mezi AD FS a vaší instancí služby Azure AD, bude pravděpodobně nutné tento vztah důvěryhodnosti znovu vytvořit.  Důvodem je, že pokud je původně nastaven bez `-SupportMultipleDomain` parametru, je IssuerUri nastavena s výchozí hodnotou.  Na následujícím snímku obrazovky vidíte, že je IssuerUri nastaven na hodnotu `https://adfs.bmcontoso.com/adfs/services/trust` .

Pokud jste úspěšně přidali novou doménu na portálu Azure AD a pokusíte se ji převést pomocí, zobrazí se `Convert-MsolDomaintoFederated -DomainName <your domain>` následující chyba.

![Snímek obrazovky zobrazující chybu federace v prostředí PowerShell po pokusu o převod nové domény pomocí příkazu Convert-MsolDomaintoFederated](./media/how-to-connect-install-multiple-domains/trust1.png)

Pokud se pokusíte přidat `-SupportMultipleDomain` přepínač, zobrazí se následující chyba:

![Snímek obrazovky, který zobrazuje chybu federace po přidání přepínače "-SupportMultipleDomain".](./media/how-to-connect-install-multiple-domains/trust2.png)

Pouhým pokusem o spuštění `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` v původní doméně dojde také k chybě.

![Chyba federace](./media/how-to-connect-install-multiple-domains/trust3.png)

Pomocí následujících kroků přidejte další doménu nejvyšší úrovně.  Pokud jste už přidali doménu a tento parametr nepoužili `-SupportMultipleDomain` , začněte kroky pro odebrání a aktualizaci původní domény.  Pokud jste ještě nepřidali doménu nejvyšší úrovně, můžete začít s postupem přidání domény pomocí prostředí PowerShell Azure AD Connect.

Pomocí následujících kroků odeberte Microsoft Online Trust a aktualizujte původní doménu.

1. Na AD FS federační Server otevřete **AD FS správu.**
2. Na levé straně rozbalte **vztahy důvěryhodnosti** a **vztahy důvěryhodnosti předávající strany** .
3. Na pravé straně odstraňte položku **platformy Identity systém Microsoft Office 365** .
   ![Odebrat Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Na počítači, na kterém je nainstalovaný [modul Azure Active Directory pro prostředí Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) , spusťte následující příkaz: `$cred=Get-Credential` .  
5. Zadejte uživatelské jméno a heslo globálního správce pro doménu Azure AD, se kterou jste federování.
6. V PowerShellu zadejte `Connect-MsolService -Credential $cred`
7. V PowerShellu zadejte `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` .  Tato aktualizace je určena pro původní doménu.  Pomocí výše uvedených domén by to bylo:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Pomocí následujících kroků přidejte novou doménu nejvyšší úrovně pomocí prostředí PowerShell.

1. Na počítači, na kterém je nainstalovaný [modul Azure Active Directory pro prostředí Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) , spusťte následující příkaz: `$cred=Get-Credential` .  
2. Zadejte uživatelské jméno a heslo globálního správce pro doménu Azure AD, se kterou jste federování
3. V PowerShellu zadejte `Connect-MsolService -Credential $cred`
4. V PowerShellu zadejte `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Pomocí následujících kroků přidejte novou doménu nejvyšší úrovně pomocí Azure AD Connect.

1. Spustit Azure AD Connect z plochy nebo nabídky Start
2. Klikněte na tlačítko Přidat další doménu Azure AD ![ , na které se zobrazí stránka další úkoly s vybranou možnost přidat další doménu Azure AD.](./media/how-to-connect-install-multiple-domains/add1.png)
3. Zadejte svoje přihlašovací údaje pro Azure AD a Active Directory.
4. Vyberte druhou doménu, kterou chcete konfigurovat pro federaci.
   ![Přidat další doménu služby Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kliknutí na Nainstalovat

### <a name="verify-the-new-top-level-domain"></a>Ověřte novou doménu nejvyšší úrovně.
Pomocí příkazu prostředí PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>` můžete zobrazit aktualizované IssuerUri.  Na následujícím snímku obrazovky vidíte, že nastavení federace se v původní doméně aktualizovala. `http://bmcontoso.com/adfs/services/trust`

![Snímek obrazovky, který zobrazuje nastavení federace aktualizované v původní doméně.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

A IssuerUri v nové doméně je nastavené na `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Podpora subdomén
Když přidáte subdoménu, z důvodu způsobu, jakým služba Azure AD zpracuje domény, zdědí nastavení nadřazeného objektu.  IssuerUri musí proto odpovídat nadřazeným prvkům.

Takže to znamená například, že mám bmcontoso.com a pak přidat corp.bmcontoso.com.  Bude muset být IssuerUri pro uživatele z corp.bmcontoso.com **`http://bmcontoso.com/adfs/services/trust`** .  Standardní pravidlo implementované výše pro Azure AD však vygeneruje token s vystavitelem **`http://corp.bmcontoso.com/adfs/services/trust`** . neshoduje se s požadovanou hodnotou domény a ověření se nezdaří.

### <a name="how-to-enable-support-for-subdomains"></a>Jak povolit podporu pro subdomény
Chcete-li tento problém obejít, je nutné aktualizovat AD FS vztah důvěryhodnosti předávající strany pro Microsoft Online.  Chcete-li to provést, musíte nakonfigurovat vlastní pravidlo deklarace identity tak, aby při vytváření vlastní hodnoty vystavitele z přípony hlavního názvu uživatele (UPN) odříznout jakékoli subdomény.

Tato deklarace se provede tímto způsobem:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
Poslední číslo v sadě regulárních výrazů je počet nadřazených domén, které jsou v kořenové doméně. Zde se používá bmcontoso.com, takže jsou nutné dvě nadřazené domény. Pokud by se měly zachovat tři nadřazené domény (například: corp.bmcontoso.com), pak se toto číslo zaznamenalo tři. V takovém případě lze určit rozsah, přičemž shoda bude vždy provedena tak, aby odpovídala maximálnímu počtu domén. " {2,3} " bude odpovídat dvěma až třem doménám (tj.: bmfabrikam.com a Corp.bmcontoso.com).

Pomocí následujících kroků přidejte vlastní deklaraci identity pro podporu subdomén.

1. Otevřít správu AD FS
2. Klikněte pravým tlačítkem na Microsoft online RP Trust a vyberte upravit pravidla deklarací identity.
3. Vyberte třetí pravidlo deklarace identity a nahraďte ![ Upravit deklaraci identity.](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Nahradit aktuální deklaraci identity:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    with

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Nahradit deklaraci identity](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klikněte na tlačítko OK.  Klikněte na Použít.  Klikněte na tlačítko OK.  Zavřete správu služby AD FS.

## <a name="next-steps"></a>Další kroky
Nyní, když máte nainstalovanou službu Azure AD Connect, si můžete [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Zjistěte více o těchto funkcích, které byly povoleny v rámci instalace: [Automatický upgrade](how-to-connect-install-automatic-upgrade.md), [Prevence náhodných odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](how-to-connect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a spouštění synchronizace](how-to-connect-sync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).