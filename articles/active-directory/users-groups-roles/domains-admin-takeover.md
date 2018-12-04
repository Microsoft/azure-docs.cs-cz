---
title: Správce převzetí nespravovaného adresáře nebo stínové tenanta v Azure Active Directory | Dokumentace Microsoftu
description: Jak převzít kontrolu nad název domény DNS nespravovaného adresáře (stínové tenanta) v Azure Active Directory.
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
ms.openlocfilehash: c07b87d88b884b22f44fe108bb28877cb30f973e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837167"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Převzít kontrolu nad nespravovaného adresáře jako správce v Azure Active Directory
Tento článek popisuje dva způsoby, jak převzít kontrolu nad název domény DNS nespravovaného adresáře v Azure Active Directory (Azure AD). Když se samoobslužný uživatel zaregistruje ke cloudové službě, která využívá Azure AD, přidá se do nespravovaného adresáře Azure AD na základě svojí e-mailové domény. Další informace o samoobslužné nebo "virálního" registraci služby najdete v tématu [co je Samoobslužná registrace do služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Rozhodnutí o způsobu převzít kontrolu nad nespravovaného adresáře
Během procesu převzetí správce můžete vlastnictví prokázat způsobem popsaným v tématu [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md). Další části popisují prostředí pro správu podrobněji, ale tady je shrnutí:

* Když provedete [interní převzetí správce](#internal-admin-takeover) nespravovaného adresáře Azure, přidáte se jako globální správce tohoto nespravovaného adresáře. Do žádného jiného adresáře, který spravujete, se nemigrují žádní uživatelé, domény ani plány služeb.

* Když provedete [externí převzetí správce](#external-admin-takeover) nespravovaného adresáře Azure, přidáte název domény DNS nespravovaného adresáře do svého spravovaného adresáře Azure. Když přidáte název domény, ve vašem spravovaném adresáři Azure se vytvoří mapování uživatelů na prostředky, aby uživatelé měli i nadále přístup ke službám bez přerušení. 

## <a name="internal-admin-takeover"></a>Vnitřní Správce převzetí

Některé produkty, které obsahují služby SharePoint a OneDrive, jako je Office 365, nepodporují externí převzetí. Pokud je to váš scénář, nebo pokud jste správce a chcete převzít kontrolu nad nespravované "stínové" tenanta s vytvořením uživatele, kteří používají samoobslužnou registraci, musíte se interní správce převzetí.

1. Vytvořte uživatelský kontext v nespravovaného tenanta, až se přihlásíte jako je Power BI. Ke zvýšení pohodlí. Příklad tento postup předpokládá, že cesta.

2. Otevřít [web Power BI](https://powerbi.com) a vyberte **začít zdarma**. Zadejte uživatelský účet, který používá název domény pro organizaci; například `admin@fourthcoffee.xyz`. Když zadáte ověřovací kód, zkontrolujte e-mailu pro potvrzovací kód.

3. Potvrzení e-mailu z Power BI, vyberte **Ano, to jsem já**.

4. Přihlaste se k [centra pro správu Office 365](https://portal.office.com/adminportal/Home) pomocí uživatelského účtu Power BI. Obdržíte zprávu, která dává pokyn k **stát se správcem** názvu domény, který už je ověřený v nespravovaného tenanta. Vyberte **Ano, chci se stát správcem**.
  
  ![První obrazovka pro stát se správcem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Přidání záznamu TXT prokázat, že jste vlastníkem názvu domény **fourthcoffee.xyz** registrátora názvu domény. V tomto příkladu je GoDaddy.com.
  
  ![Přidejte záznam txt pro název domény](./media/domains-admin-takeover/become-admin-txt-record.png)

Při ověření záznamů DNS TXT vašeho registrátora názvu domény můžete spravovat tenanta Azure AD.

Po dokončení předchozích kroků nyní jste globální správce tenanta Fourth Coffee v Office 365. Název domény integrovat s dalšími službami Azure, můžete ho odebrat z Office 365 a přidejte ho do jiného tenanta spravované v Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Přidání názvu domény do nespravovaného tenanta na spravovaného ve službě Azure AD 

1. Otevřít [centra pro správu Office 365](https://portal.office.com/adminportal/Home).
2. Vyberte **uživatelé** kartu a vytvořit nový uživatelský účet s názvem, jako je *user@fourthcoffeexyz.onmicrosoft.com* , který nepoužívá vlastního názvu domény. 
3. Ujistěte se, že nový uživatelský účet má oprávnění globálního správce pro tenanta Azure AD.
4. Otevřít **domén** kartě v Centru pro správu Office 365, vyberte název domény a vyberte **odebrat**. 
  
  ![odebrat název domény z Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Pokud máte uživatelé nebo skupiny v Office 365, které odkazují na název domény odebrané, musí být přejmenován na. domény onmicrosoft.com. Pokud vynutíte odstranění názvu domény, všichni uživatelé jsou automaticky přejmenovány v tomhle příkladu *user@fourthcoffeexyz.onmicrosoft.com*.
  
6. Přihlaste se k [centrum pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který má oprávnění globálního správce pro tenanta Azure AD.
  
7. Vyberte **vlastní názvy domén**, pak přidejte název domény. Budete muset zadat záznamy DNS TXT ověřit vlastnictví názvu domény. 
  
  ![doména přidána do služby Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Všechny uživatele služby Power BI nebo Azure Rights Management, kteří mají licenci přidělenou v tenantovi Office 365 musíte uložit svoje řídicí panely, pokud je odebrat název domény. Musíte se přihlásit pomocí uživatelského jména, jako je *user@fourthcoffeexyz.onmicrosoft.com* spíše než *user@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externí správu převzetí

Pokud už spravujete tenanta služby Azure nebo Office 365, nelze přidat vlastní název domény, pokud už je ověřený v jiném tenantovi Azure AD. Ale ve svém tenantovi spravované ve službě Azure AD můžete si je převezme nespravovaného tenanta jako externí správu převzetí. Obecný postup následující článek [přidání vlastní domény do Azure AD](../fundamentals/add-custom-domain.md).

Při ověření vlastnictví názvu domény, odebere název domény z nespravovaného tenanta Azure AD a přesouvá ji do vašeho existujícího tenanta. Externí správu převzetí nespravovaného adresáře vyžaduje stejný proces ověření DNS TXT jako interní správce převzetí. Rozdíl je, že následující se přesouvají s názvem domény:

- Uživatelé
- Předplatná
- Přiřazení licencí

### <a name="support-for-external-admin-takeover"></a>Podpora pro převzetí externí správce
Externí správu převzetí podporuje následujících online služeb:

- Power BI
- Azure Rights Management
- Exchange Online

Podporované služby plány zahrnují:

- Power BI zdarma
- Power BI Pro
- PowerApps zdarma
- PowerFlow zdarma
- RMS pro jednotlivce
- Microsoft Stream
- Bezplatná zkušební verze Dynamics 365

Převzetí externí správce se nepodporuje pro všemi službami, které má plánů služeb, které patří SharePoint, OneDrive nebo Skype pro firmy; například pomocí bezplatné předplatné Office nebo základní SKU Office. Volitelně můžete [ **ForceTakeover** možnost](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) pro odstranění názvu domény z nespravovaného tenanta a ověřování na požadovaný tenant. Tato možnost ForceTakeover nebude přesouvat uživatele nebo zachovat přístup k předplatnému. Místo toho tuto možnost přesune pouze název domény. 

#### <a name="more-information-about-rms-for-individuals"></a>Další informace o RMS pro jednotlivce

Pro [RMS pro jednotlivce](/azure/information-protection/rms-for-individuals), když nespravovaný tenant je ve stejné oblasti jako klient, že jste vlastníkem, automaticky vytvořený [klíč tenanta Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) a [výchozí šablony ochrany](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) kromě přesunuty názvem domény. 

Klíč a šablony nejsou přesunuta při nespravovaný tenant je v jiné oblasti. Nespravovaného tenanta je například v Evropě a tenanta, které vlastníte, je v řešení ze Severní Ameriky. 

I když se RMS pro jednotlivce je navržen pro podporu ověřování Azure AD chráněný obsah otevřít, nezabrání uživatelům ve také chrání obsah. Pokud uživatelé chránit obsah pomocí služby RMS pro předplatná jednotlivých uživatelů a klíčů a šablon nepřesunula, nebude tento obsah přístupná po převzetí domény.

#### <a name="more-information-about-power-bi"></a>Další informace o Power BI

Když provádíte externí převzetí, Power BI obsah, který byl vytvořen ještě před převzetí, nachází ve [Power BI archivovaného pracovního prostoru](/power-bi/service-admin-power-bi-archived-workspace). Musíte ručně migrovat veškerý obsah, který chcete použít v nového tenanta.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Rutiny Azure AD PowerShell pro možnost ForceTakeover
Zobrazí se tyto rutiny používané v [příklad Powershellu](#powershell-example).


Rutiny | Využití 
------- | -------
`connect-msolservice` | Po zobrazení výzvy, přihlaste se do nespravovaného tenanta.
`get-msoldomain` | Zobrazuje názvy domén spojené s aktuálním tenantovi.
`new-msoldomain –name <domainname>` | Přidá název domény pro tenanta jako neověřené (bez ověření DNS dosud nebyl proveden).
`get-msoldomain` | Název domény je nyní součástí seznamu názvů domén, které jsou přidružené k nespravovaného tenanta, ale je uveden jako **neověřené**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Obsahuje informace k vložení do nový záznam DNS TXT pro doménu (MS = xxxxx). Ověření nemusí možné okamžitě vzhledem k tomu, že bude trvat nějakou dobu záznam TXT rozšíření, tak po několika minutách předtím, než **- ForceTakeover** možnost. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Pokud ještě není ověřený název domény, abyste mohli pokračovat **- ForceTakeover** možnost. Ověřuje, že byl vytvořen záznam TXT a zahajuje proces převzetí.<li>**- ForceTakeover** možnost měla být přidána do rutiny jenom v případě, že vynucení převzetí externí správu, například pokud má nespravovaného tenanta blokování převzetí služeb Office 365.
`get-msoldomain` | Seznam domén nyní zobrazuje název domény jako **ověřeno**.

### <a name="powershell-example"></a>Příklad PowerShellu

1. Připojení k Azure AD pomocí přihlašovacích údajů, které byly použity na nabídku samoobslužné služby:
  ````
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Získáte seznam domén:
  
  ````
    Get-MsolDomain
  ````
3. Spuštěním rutiny Get-MsolDomainVerificationDns způsobit:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Zkopírujte hodnotu (výzva), která je vrácena z tohoto příkazu. Příklad:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. Ve vašem oboru názvů veřejné DNS vytvořte záznam DNS txt, který obsahuje hodnotu, kterou jste zkopírovali v předchozím kroku. Název tohoto záznamu je název nadřazené domény, takže pokud vytvoříte tento záznam o prostředku DNS role ze systému Windows Server, ponechte název prázdné a stačí vložit záznam hodnotu do textového pole.
6. Spusťte rutinu Confirm-MsolDomain ověření před obrovskou výzvou –:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Příklad:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

Úspěšné výzvy se vrátíte na příkazový řádek bez chyby.

## <a name="next-steps"></a>Další postup
* [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
