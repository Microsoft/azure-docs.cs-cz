---
title: Správce převzetí nespravované adresáře nebo stínové klienta v Azure Active Directory | Microsoft Docs
description: Jak převzít kontrolu nad název domény DNS do nespravovaných adresář (stínové klientů), který se v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 04/06/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: fa299603a14ef94ab4cf3eac2b1123def1a0b6de
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594879"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Převzít kontrolu nad adresář nespravované jako správce v Azure Active Directory
Tento článek popisuje dva způsoby, jak převzít kontrolu nad název domény DNS do adresář nespravované v Azure Active Directory (Azure AD). Když se samoobslužný uživatel zaregistruje ke cloudové službě, která využívá Azure AD, přidá se do nespravovaného adresáře Azure AD na základě svojí e-mailové domény. Další informace o samoobslužné nebo "virální" registrace pro služby najdete v tématu [co je samoobslužné registrace pro Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-self-service-signup)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Rozhodněte, jak chcete převzít kontrolu nad adresář nespravované
Během procesu převzetí správce můžete vlastnictví prokázat způsobem popsaným v tématu [Přidání vlastního názvu domény do Azure AD](add-custom-domain.md). Další části popisují prostředí pro správu podrobněji, ale tady je shrnutí:

* Když provedete [interní převzetí správce](#internal-admin-takeover) nespravovaného adresáře Azure, přidáte se jako globální správce tohoto nespravovaného adresáře. Do žádného jiného adresáře, který spravujete, se nemigrují žádní uživatelé, domény ani plány služeb.

* Když provedete [externí převzetí správce](#external-admin-takeover) nespravovaného adresáře Azure, přidáte název domény DNS nespravovaného adresáře do svého spravovaného adresáře Azure. Když přidáte název domény, ve vašem spravovaném adresáři Azure se vytvoří mapování uživatelů na prostředky, aby uživatelé měli i nadále přístup ke službám bez přerušení. 

## <a name="internal-admin-takeover"></a>Interní správce převzetí

Některé produkty, které patří SharePoint a OneDrive, např. Office 365, nepodporují externí převzetí. Pokud je váš scénář, nebo pokud jste správce a chcete převzít kontrolu nad nespravované, nebo "stínové" klienta vytvořit uživateli, kteří používají samoobslužné registrace, můžete k tomu se převzetí interní správce.

1. Vytvoření kontextu uživatele v nespravovaného tenanta prostřednictvím se přihlásíte jako je například Power BI. Pro usnadnění práce příkladu tento postup předpokládá této cestě.

2. Otevřete [webu Power BI](https://powerbi.com) a vyberte **spustit volné**. Zadejte uživatelský účet, který používá název domény pro organizaci; například `admin@fourthcoffee.xyz`. Když zadáte ověřovací kód, zkontrolujte e-mailech potvrzovací kód.

3. Potvrzení e-mailu z Power BI, vyberte **Ano, který je mi**.

4. Přihlaste se k [centra pro správu Office 365](https://portal.office.com/adminportal/Home) pomocí uživatelského účtu Power BI. Obdržíte zprávu, která se dá pokyn k **stát správce** názvu domény, který už je ověřený v nespravovaného tenanta. Vyberte **Ano, chci se správce**.
  
  ![První obrazovka stal správce](./media/domains-admin-takeover/become-admin-first.png)
  
5. Přidejte záznam TXT prokázat, že jste vlastníkem názvu domény **fourthcoffee.xyz** u registrátora názvu domény. V tomto příkladu je GoDaddy.com.
  
  ![Přidejte záznam txt pro název domény](./media/domains-admin-takeover/become-admin-txt-record.png)

Při ověření záznamů DNS TXT u registrátora názvu domény můžete spravovat klienta Azure AD.

Po dokončení předchozích kroků jste nyní globální správce klienta společnosti Fourth Coffee v Office 365. Název domény integraci s jinými službami Azure, můžete odebrat z Office 365 a přidejte ji do různých spravovaného klienta v Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Přidání názvu domény do spravovaného klienta ve službě Azure AD 

1. Otevřete [centra pro správu Office 365](https://portal.office.com/adminportal/Home).
2. Vyberte **uživatelé** kartě a vytvořit nový uživatelský účet s názvem jako *user@fourthcoffeexyz.onmicrosoft.com* nepoužívá vlastní název domény. 
3. Ujistěte se, že nový uživatelský účet má oprávnění globálního správce pro tenanta Azure AD.
4. Otevřete **domén** kartě v Centru pro správu Office 365, vyberte název domény a vyberte **odebrat**. 
  
  ![název domény odebrání Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Pokud máte uživatelé nebo skupiny v Office 365, které odkazují na název domény odebrána, musí být přejmenován na. onmicrosoft.com domény. Pokud vynutíte odstranit název domény, všichni uživatelé jsou automaticky přejmenovat v tomto příkladu k *user@fourthcoffeexyz.onmicrosoft.com*.
  
6. Přihlaste se k [centra pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který má oprávnění globálního správce pro tenanta Azure AD.
  
7. Vyberte **vlastní názvy domén**, poté přidejte název domény. Budete muset zadat záznamů DNS TXT ověřit vlastnictví názvu domény. 
  
  ![domény, které jsou přidány do služby Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Uživatelé služby Power BI nebo Azure Rights Management, kteří mají přiřazené v klientovi Office 365 licence musíte uložit svoje řídicí panely, pokud název domény je odebrán. Musíte se přihlásit s uživatelské jméno jako *user@fourthcoffeexyz.onmicrosoft.com* místo *user@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externí správu převzetí

Pokud už spravujete klienta pomocí služby Azure nebo Office 365, nelze přidat vlastní název domény, pokud je už ověřený v jiném klientovi Azure AD. Ale z vašeho spravovaného klienta ve službě Azure AD může trvat přes nespravovaného tenanta jako převzetí externí správu. Obecný postup odpovídá článek [přidat vlastní doménu do služby Azure AD](add-custom-domain.md).

Když je ověřit vlastnictví název domény, Azure AD Odebere název domény z nespravovaného tenanta a přesouvá ji do existujícího klienta. Externí správu převzetí nespravované adresáře vyžaduje stejný postup ověření DNS TXT jako interní správce převzetí. Rozdíl je, že následující se přesouvají s názvem domény:

- Uživatelé
- Předplatná
- Přiřazení licencí

### <a name="support-for-external-admin-takeover"></a>Podpora pro externí správu převzetí
Externí správu převzetí podporuje následujících online služeb:

- Power BI
- Azure Rights Management
- Exchange Online

Plány podporované služby patří:

- Power BI volné
- Power BI Pro
- Uvolněte PowerApps
- PowerFlow volné
- RMS pro jednotlivce
- Microsoft Stream
- Dynamics 365 bezplatné zkušební verze

Externí správu převzetí není podporována pro jakoukoli službu, která má plány služby, které obsahují služby SharePoint, OneDrive nebo Skype pro firmy; například prostřednictvím bezplatné předplatné Office nebo základní SKU Office. Volitelně můžete [ **ForceTakeover** možnost](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) pro název domény odebráním nespravovaného tenanta a ověření u požadované klienta. Tato možnost ForceTakeover nebude přesunout přes uživatelé nebo si zachovat přístup k předplatnému. Místo toho tuto možnost přesouvá pouze název domény. 

#### <a name="more-information-about-rms-for-individuals"></a>Další informace o RMS pro jednotlivce

Pro [RMS pro jednotlivce](/information-protection/understand-explore/rms-for-individuals), když nespravovaného tenanta je ve stejné oblasti jako klient, že jste vlastníkem, automaticky vytvořené [klíče klienta Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) a [výchozí šablony ochrany](/information-protection/deploy-use/configure-usage-rights#rights-included-in-the-default-templates) jsou kromě nepřesouvají s názvem domény. 

Klíče a šablony nejsou nepřesouvají po nespravovaného tenanta v jiné oblasti. Například nespravovaného tenanta je v Evropě a klienta, který vlastníte, je v Severní Americe. 

I když se RMS pro jednotlivce je navržen pro podporu ověřování Azure AD k otevření chráněného obsahu, nezabrání uživatelům ve rovněž chrání obsah. Pokud uživatelé chránit obsah pomocí služby RMS pro jednotlivce, a nebyly nepřesouvají klíče a šablony, nebude obsah dostupný po převzetí domény.    

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Rutiny Azure AD PowerShell pro možnost ForceTakeover
Zobrazí se tyto rutiny použít v [příklad PowerShell](#powershell-example).


Rutiny | Využití 
------- | -------
`connect-msolservice` | Pokud budete vyzváni, přihlaste se k spravovaného klienta.
`get-msoldomain` | Zobrazuje názvy domén přidružené k aktuální klientovi.
`new-msoldomain –name <domainname>` | Přidá název domény pro klienta jako Unverified (žádné ověření DNS dosud nebyl proveden).
`get-msoldomain` | Název domény je nyní zahrnutá v seznamu názvů domén, které jsou přidružené k spravovaného klienta, ale je uveden jako **Unverified**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Obsahuje informace, které umístí do nového záznamu DNS TXT pro doménu (MS = xxxxx). Ověření nemusí dojít okamžitě vzhledem k tomu, že bude trvat nějakou dobu záznam TXT, který chcete rozšířit, proto Počkejte několik minut před vzhledem k tomu **- ForceTakeover** možnost. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Pokud se název domény není stále ověřit, abyste mohli pokračovat **- ForceTakeover** možnost. Ověří, že byl vytvořen záznam TXT a zahájí proces převzetí.<li>**- ForceTakeover** možnost musí být přidaní do rutinu, pouze v případě, že vynucení převzetí externí správu, například když má nespravovaný klient blokování převzetí služeb Office 365.
`get-msoldomain` | Seznam domén se teď zobrazuje název domény jako **ověřeno**.

### <a name="powershell-example"></a>Příklad PowerShell

1. Připojení k Azure AD pomocí přihlašovacích údajů, které jste použili k reagovat na nabídku samoobslužné služby:
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Získáte seznam domén:
  
  ````
    Get-MsolDomain
  ````
3. Spusťte rutinu Get-MsolDomainVerificationDns vytvořit výzvu:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Zkopírujte hodnotu (výzva), která je vrácena z tohoto příkazu. Příklad:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. V oboru názvů veřejné DNS vytvořte záznam DNS txt, který obsahuje hodnotu, kterou jste zkopírovali v předchozím kroku. Název pro tento záznam je název nadřazené domény, tak pokud vytvoříte tento záznam o prostředku pomocí role DNS ze systému Windows Server, ponechte název prázdné, jenom vložení záznamu hodnotu do textového pole.
6. Spusťte rutinu potvrdit MsolDomain ověření výzvy:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Příklad:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

Úspěšné výzvy se vrátíte do příkazového řádku bez chyby.

## <a name="next-steps"></a>Další postup
* [Přidání vlastního názvu domény do Azure AD](add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
