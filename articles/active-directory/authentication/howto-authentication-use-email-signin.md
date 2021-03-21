---
title: Přihlaste se pomocí e-mailu jako alternativní přihlašovací ID pro Azure Active Directory
description: Přečtěte si, jak nakonfigurovat a povolit uživatelům přihlášení k Azure Active Directory pomocí své e-mailové adresy jako alternativní ID přihlášení (Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 4e39d7f15e3ca3c6e241c767a5f881d7170c6379
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255963"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Přihlášení k Azure Active Directory používání e-mailu jako alternativního přihlašovacího ID (Preview)

> [!NOTE]
> Přihlášení k Azure AD s e-mailem jako alternativním přihlašovacím ID je funkce Public Preview služby Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Mnoho organizací chce umožnit uživatelům přihlašovat se Azure Active Directory (Azure AD) pomocí stejných přihlašovacích údajů, jako je místní prostředí adresáře. U tohoto přístupu, označovaného jako hybridní ověřování, si uživatelé musí pamatovat jenom jednu sadu přihlašovacích údajů.

Některé organizace se nepřesunuly do hybridního ověřování z následujících důvodů:

* Ve výchozím nastavení je hlavní název uživatele (UPN) služby Azure AD nastavený na stejný hlavní název uživatele (UPN) jako místní adresář.
* Změna hlavního názvu uživatele (UPN) Azure AD vytvoří neshodu mezi prostředími Prem a Azure AD, která by mohla způsobit problémy s určitými aplikacemi a službami.
* Organizace nechce, aby se k Azure AD přihlásili pomocí místního hlavního názvu uživatele (UPN) nebo dodržování předpisů.

Abyste mohli přejít na hybridní ověřování, můžete teď nakonfigurovat Azure AD tak, aby se uživatelé mohli přihlašovat pomocí e-mailu v ověřené doméně jako alternativní přihlašovací ID. Pokud se například *Contoso* přesměruje na *Fabrikam*, místo toho, abyste se mohli dál přihlašovat pomocí staršího `balas@contoso.com` hlavního názvu uživatele (UPN), se teď dá použít alternativní přihlašovací ID. Pro přístup k aplikaci nebo službám se uživatelé přihlásí k Azure AD pomocí přiřazeného e-mailu, jako je například `balas@fabrikam.com` .

V tomto článku se dozvíte, jak povolit a používat e-maily jako alternativní přihlašovací ID. Tato funkce je k dispozici v edici Azure AD Free a vyšší.

> [!NOTE]
> Tato funkce je určena jenom pro uživatele Azure AD ověřené pro Cloud.

> [!NOTE]
> V současné době není tato funkce podporovaná na zařízeních s Windows 10 připojená k Azure AD pro klienty s ověřováním pomocí cloudu. Tato funkce se nevztahuje na zařízení připojená k hybridní službě Azure AD.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Přehled přístupů ke službě Azure AD pro přihlášení

Pokud se chcete přihlásit ke službě Azure AD, uživatelé zadají název, který jednoznačně identifikuje svůj účet. Historicky jste mohli jako přihlašovací jméno použít jenom hlavní název uživatele (UPN) služby Azure AD.

V případě organizací, kde je místní hlavní název uživatele upřednostňovaný přihlašovací e-mail uživatele, byl tento přístup skvělý. Tyto organizace by nastavily hlavní název uživatele (UPN) Azure AD na stejnou hodnotu jako místní hlavní název uživatele (UPN) a uživatelé budou mít konzistentní přihlašovací prostředí.

V některých organizacích se ale místní hlavní název uživatele (UPN) nepoužívá jako přihlašovací jméno. V místních prostředích byste měli nakonfigurovat místní služba AD DS, aby se povolilo přihlašovat pomocí alternativního přihlašovacího ID. Nastavení hlavního názvu uživatele (UPN) Azure AD na stejnou hodnotu jako místní hlavní název uživatele (UPN) není možnost, protože služba Azure AD by pak vyžadovala, aby se uživatelé přihlásili pomocí této hodnoty.

Obvyklým řešením tohoto problému bylo nastavení hlavního názvu uživatele (UPN) Azure AD na e-mailovou adresu, se kterou uživatel očekává přihlášení. Tento přístup funguje, ale výsledkem jsou různé hlavní názvy uživatelů (UPN) mezi místními službami AD a Azure AD a tato konfigurace není kompatibilní se všemi Microsoft 365 úlohami.

Jiným přístupem je synchronizace Azure AD a místních UPN na stejnou hodnotu a konfigurace Azure AD tak, aby se uživatelé mohli přihlásit k Azure AD pomocí ověřeného e-mailu. Chcete-li tuto možnost poskytnout, definujte jednu nebo více e-mailových adres v atributu *proxyAddresses* uživatele v místním adresáři. *ProxyAddresses* se pak automaticky synchronizuje se službou Azure AD pomocí Azure AD Connect.

## <a name="preview-limitations"></a>Omezení verze Preview

Přihlaste se k Azure AD pomocí e-mailu jako alternativní přihlašovací ID, které je k dispozici v edici Azure AD Free a vyšší.

Pokud se uživatel přihlásí pomocí e-mailu bez názvu UPN jako alternativní přihlašovací ID, platí v aktuálním stavu verze Preview tato omezení:

* Uživatelé se můžou podívat na hlavní název uživatele (UPN), i když se přihlásí pomocí e-mailu bez názvu UPN. Může se zobrazit následující příklad chování:
    * Uživatel se zobrazí výzva, abyste se přihlásili pomocí hlavního názvu uživatele (UPN) při přesměrování na přihlášení k Azure AD pomocí `login_hint=<non-UPN email>` .
    * Když se uživatel přihlásí pomocí e-mailu bez hlavního názvu uživatele (UPN) a zadá nesprávné heslo, změní se stránka *zadání hesla* , aby se zobrazil hlavní název uživatele (UPN).
    * Na některých webech a aplikacích společnosti Microsoft, jako je například [https://portal.azure.com](https://portal.azure.com) a systém Microsoft Office, se v pravém horním rohu může zobrazit ovládací prvek **správce účtů** , nikoli e-mailová adresa uživatele, která se používá k přihlášení.

* Některé toky nejsou aktuálně kompatibilní s e-mailem bez názvu UPN, například následující:
    * Identity Protection aktuálně neodpovídá ID alternativního přihlašovacího jména e-mailu s *Nevrácenými odhaleními přihlašovacích údajů* . Toto zjišťování rizik používá hlavní název uživatele (UPN) k vyhledání nevrácených přihlašovacích údajů. Další informace najdete v tématu [Azure AD Identity Protection detekci rizik a nápravě][identity-protection].
    * Pozvánky B2B odeslané na alternativní e-mailové přihlašovací ID nejsou plně podporované. Po přijetí pozvání odeslaného e-mailem jako alternativního přihlašovacího ID nemusí být přihlášení pomocí alternativního e-mailu pro uživatele na koncovém bodu klienta fungovat.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synchronizace e-mailových přihlašovacích adres do Azure AD

Tradiční ověřování Active Directory Domain Services (služba AD DS) nebo Active Directory Federation Services (AD FS) (AD FS) se provádí přímo v síti a je zpracováváno vaší služba AD DS infrastrukturou. Pomocí hybridního ověřování se uživatelé můžou místo toho přihlašovat přímo do Azure AD.

Pro podporu tohoto přístupu k hybridnímu ověřování synchronizujete své místní služba AD DS prostředí s Azure AD pomocí [Azure AD Connect][azure-ad-connect] a nakonfigurujte ho tak, aby používal synchronizaci hodnoty hash hesla (kosmetice) nebo ověřování Pass-Through (PTA).

V obou možnostech konfigurace odešle uživatel své uživatelské jméno a heslo do služby Azure AD, která ověří přihlašovací údaje a vydá lístek. Když se uživatelé přihlásí ke službě Azure AD, nepotřebují, aby vaše organizace mohla hostovat a spravovat infrastrukturu AD FS.

![Diagram hybridní identity Azure AD pomocí synchronizace hodnot hash hesel](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram hybridní identity Azure AD pomocí předávacího ověřování](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Jeden z atributů uživatele, který je automaticky synchronizován pomocí Azure AD Connect je *proxyAddresses*. Pokud má uživatel e-mailovou adresu definovanou v prostředí Prem služba AD DS jako součást atributu *proxyAddresses* , automaticky se synchronizuje se službou Azure AD. Tato e-mailová adresa se pak dá použít přímo v procesu přihlášení k Azure AD jako alternativní přihlašovací ID.

> [!IMPORTANT]
> Do služby Azure AD se synchronizují jenom e-maily v ověřených doménách pro tenanta. Každý tenant služby Azure AD má jednu nebo více ověřených domén, pro které máte prověřené vlastnictví, a je jednoznačně svázán se svým klientem.
>
> Další informace najdete v tématu [Přidání a ověření vlastního názvu domény ve službě Azure AD][verify-domain].

Další informace najdete v tématu [Volba správné metody ověřování pro řešení hybridní identity Azure AD][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Povolit přihlášení uživatele pomocí e-mailové adresy

Jakmile se uživatelé s použitým atributem *proxyAddresses* synchronizují do služby Azure AD pomocí Azure AD Connect, je nutné povolit funkci pro uživatele, aby se k vašemu tenantovi přihlásili pomocí e-mailu jako alternativní přihlašovací ID. Tato funkce oznamuje přihlašovacím serverům Azure AD, aby nekontrolovaly jenom přihlašovací jméno proti hodnotám hlavního názvu uživatele (UPN), ale taky proti *proxyAddresses* hodnotám e-mailové adresy.

Během období Preview můžete v současné době povolit jenom přihlášení pomocí e-mailu jako alternativní funkci přihlašovacího ID pomocí PowerShellu. K provedení následujících kroků potřebujete oprávnění *správce tenanta* :

1. Otevřete relaci PowerShellu jako správce a pak nainstalujte modul *AzureADPreview* pomocí rutiny [install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Pokud se zobrazí výzva, vyberte **Y** pro instalaci nástroje NuGet nebo pro instalaci z nedůvěryhodného úložiště.

1. Přihlaste se k tenantovi Azure AD jako *správce tenanta* pomocí rutiny [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Příkaz vrátí informace o vašem účtu, prostředí a ID tenanta.

1. Pomocí rutiny [Get-AzureADPolicy][Get-AzureADPolicy] ověřte, jestli zásady *HomeRealmDiscoveryPolicy* už ve vašem tenantovi existují:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Pokud není aktuálně nakonfigurována žádná zásada, příkaz nevrátí žádnou hodnotu. Pokud se vrátí zásada, přeskočte tento krok a přejděte k dalšímu kroku, abyste aktualizovali existující zásady.

    Pokud chcete do tenanta přidat zásady *HomeRealmDiscoveryPolicy* , použijte rutinu [New-AzureADPolicy][New-AzureADPolicy] a nastavte atribut *AlternateIdLogin* na *Enabled: true* , jak je znázorněno v následujícím příkladu:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    Po úspěšném vytvoření zásady vrátí příkaz ID zásady, jak je znázorněno v následujícím příkladu výstupu:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Pokud už je nakonfigurovaná zásada, ověřte, jestli **   je povolený atribut AlternateIdLogin, jak je znázorněné v následujícím příkladu výstupu zásady:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Pokud zásada existuje, ale atribut *AlternateIdLogin* , který není přítomný nebo povolený, nebo pokud u zásady, kterou chcete zachovat, existují jiné atributy, aktualizujte stávající zásady pomocí rutiny [set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > Když zásadu aktualizujete, ujistěte se, že jste zahrnuli stará nastavení a nový atribut *AlternateIdLogin* .

    Následující příklad přidá atribut *AlternateIdLogin* a zachová atribut *AllowCloudPasswordValidation* , který již mohl být nastaven:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Potvrďte, že aktualizovaná zásada zobrazuje vaše změny a že je teď povolený atribut *AlternateIdLogin* :

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

Při použití těchto zásad může trvat až hodinu, než se rozšíří a uživatelé se budou moct přihlašovat pomocí svého alternativního přihlašovacího ID.

## <a name="test-user-sign-in-with-email"></a>Testování přihlášení uživatele pomocí e-mailu

Pokud chcete otestovat, jestli se uživatelé můžou přihlašovat pomocí e-mailu, přejděte na [https://myprofile.microsoft.com][my-profile] adresu a přihlaste se pomocí uživatelského účtu na základě jejich e-mailové adresy, jako `balas@fabrikam.com` je například hlavní název uživatele (UPN) `balas@contoso.com` . Prostředí pro přihlašování by mělo vypadat stejně jako u přihlašovací události založené na UPN.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Povolit připravené zavedení pro testování přihlášení uživatele pomocí e-mailové adresy  

[Příprava na přípravu][staged-rollout] umožňuje správcům klientů povolit funkce pro konkrétní skupiny. Doporučuje se, aby správci klientů používali připravené zavedení k testování přihlášení uživatelů pomocí e-mailové adresy. Když jsou správci připraveni nasadit tuto funkci do celého tenanta, musí použít zásady zjišťování domovské sféry.  


K provedení následujících kroků potřebujete oprávnění *správce tenanta* :

1. Otevřete relaci PowerShellu jako správce a pak nainstalujte modul *AzureADPreview* pomocí rutiny [install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Pokud se zobrazí výzva, vyberte **Y** pro instalaci nástroje NuGet nebo pro instalaci z nedůvěryhodného úložiště.

2. Přihlaste se k tenantovi Azure AD jako *správce tenanta* pomocí rutiny [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Příkaz vrátí informace o vašem účtu, prostředí a ID tenanta.

3. Vypíše všechny existující zásady dvoufázové implementace pomocí následující rutiny:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Pokud pro tuto funkci neexistují žádné zásady dvoufázového zavedení, vytvořte nové zásady dvoufázové zavedení a poznamenejte si ID zásady:

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

5. Najděte ID directoryObject skupiny, která se má přidat do zásady dvoufázové zavedení. Všimněte si hodnoty vrácené pro parametr *ID* , protože se použije v dalším kroku.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Přidejte skupinu do zásady dvoufázové zavedení, jak je znázorněno v následujícím příkladu. Hodnotu v parametru *-ID* nahraďte hodnotou vrácenou pro ID zásady v kroku 4 a nahraďte hodnotu v parametru *-RefObjectId* *číslem ID* , které jste si poznamenali v kroku 5. Může trvat až 1 hodinu, než se uživatelé ve skupině můžou pomocí svých proxy adres přihlašovat.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Pro nové členy přidané do skupiny může trvat až 24 hodin, než se budou moct pomocí svých proxy adres přihlašovat.

### <a name="removing-groups"></a>Odebírání skupin

Pokud chcete skupinu odebrat ze zásady dvoufázové zavedení, spusťte následující příkaz:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Odebírají se zásady

Pokud chcete odebrat zásady dvoufázové zavedení, nejdřív zásadu zakažte a pak ji odeberte ze systému:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Řešení potíží

Pokud uživatelé mají problémy s přihlašovacími událostmi pomocí své e-mailové adresy, přečtěte si následující postup řešení potíží:

1. Ujistěte se, že uživatelský účet má svou e-mailovou adresu nastavenou pro atribut *proxyAddresses* v prostředí Prem služba AD DS.
1. Ověřte, že je konfigurace Azure AD Connect nakonfigurovaná, a úspěšně synchronizuje uživatelské účty z prostředí on-Prem služba AD DS do Azure AD.
1. Potvrďte, že zásada Azure AD *HomeRealmDiscoveryPolicy* má atribut *AlternateIdLogin* nastavený na *Enabled: true*:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

## <a name="next-steps"></a>Další kroky

Další informace o hybridní identitě, jako je Aplikace Azure AD proxy nebo Azure AD Domain Services, najdete v tématu [Azure AD Hybrid identity pro přístup a správu úloh Prem][hybrid-overview].

Další informace o hybridních operacích identity najdete v tématu Jak funguje synchronizace [hodnot hash hesel][phs-overview] nebo [předávacího ověřování][pta-overview] .

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
