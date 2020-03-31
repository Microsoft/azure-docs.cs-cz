---
title: Převzetí nespravovaného adresáře správcem – Azure AD | Dokumenty společnosti Microsoft
description: Jak převzít název domény DNS v nespravované organizaci Azure AD (stínový tenant).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834174"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Převzetí nespravovaného adresáře jako správce ve službě Azure Active Directory

Tento článek popisuje dva způsoby převzetí názvu domény DNS v nespravovaném adresáři ve službě Azure Active Directory (Azure AD). Když se samoobslužný uživatel zaregistruje ke cloudové službě, která využívá Azure AD, přidá se do nespravovaného adresáře Azure AD na základě svojí e-mailové domény. Další informace o samoobslužné nebo "virové" registraci ke službě najdete v tématu [Co je samoobslužná registrace pro Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Rozhodněte se, jak chcete převzít nespravovaný adresář
Během procesu převzetí správce můžete vlastnictví prokázat způsobem popsaným v tématu [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md). Další části popisují prostředí pro správu podrobněji, ale tady je shrnutí:

* Když provedete [interní převzetí správce](#internal-admin-takeover) nespravovaného adresáře Azure, přidáte se jako globální správce tohoto nespravovaného adresáře. Do žádného jiného adresáře, který spravujete, se nemigrují žádní uživatelé, domény ani plány služeb.

* Když provedete [externí převzetí správce](#external-admin-takeover) nespravovaného adresáře Azure, přidáte název domény DNS nespravovaného adresáře do svého spravovaného adresáře Azure. Když přidáte název domény, ve vašem spravovaném adresáři Azure se vytvoří mapování uživatelů na prostředky, aby uživatelé měli i nadále přístup ke službám bez přerušení. 

## <a name="internal-admin-takeover"></a>Převzetí interního správce

Některé produkty, které zahrnují SharePoint a OneDrive, například Office 365, externí převzetí nepodporují. Pokud je váš scénář, nebo pokud jste správce a chcete převzít nespravované nebo "stínové" tenantvytvořit uživatelé, kteří používají samoobslužné registrace, můžete to udělat s interní správce převzetí.

1. Vytvořte uživatelský kontext v nespravovaném tenantovi prostřednictvím registrace do Power BI. Pro usnadnění například tyto kroky předpokládají, že cesta.

2. Otevřete [web Power BI](https://powerbi.com) a vyberte Spustit **zdarma**. Zadejte uživatelský účet, který používá název domény pro organizaci. například `admin@fourthcoffee.xyz`. Po zadání ověřovacího kódu zkontrolujte potvrzovací kód v e-mailu.

3. V potvrzovacím e-mailu z Power BI vyberte **Ano, to jsem já**.

4. Přihlaste se do [Centra pro správu Microsoftu 365](https://portal.office.com/admintakeover) pomocí uživatelského účtu Power BI. Zobrazí se zpráva s pokynem, abyste **se stali správcem** názvu domény, který byl již ověřen v nespravovaném tenantovi. vyberte **Ano, chci být správcem**.
  
   ![první snímek obrazovky pro Staňte se správcem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Přidejte záznam TXT, abyste prokázali, že vlastníte název domény **fourthcoffee.xyz** u registrátora doménového jména. V tomto příkladu je GoDaddy.com.
  
   ![Přidání záznamu txt pro název domény](./media/domains-admin-takeover/become-admin-txt-record.png)

Když se záznamy DNS TXT ověří u vašeho doménového registrátora, můžete spravovat klienta Azure AD.

Po dokončení předchozích kroků jste teď globálním správcem klienta čtvrté kávy v Office 365. Pokud chcete integrovat název domény s dalšími službami Azure, můžete ho odebrat z Office 365 a přidat ho do jiného spravovaného klienta v Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Přidání názvu domény do spravovaného klienta ve službě Azure AD

1. Otevřete [Centrum pro správu Microsoftu 365](https://admin.microsoft.com).
2. Vyberte kartu **Uživatelé** a vytvořte nový uživatelský účet s názvem, jako *je\@uživatel fourthcoffeexyz.onmicrosoft.com,* který nepoužívá vlastní název domény. 
3. Ujistěte se, že nový uživatelský účet má oprávnění globálního správce pro klienta Azure AD.
4. Otevřete kartu **Domény** v Centru pro správu Microsoftu 365, vyberte název domény a vyberte **Odebrat**. 
  
   ![odebrání názvu domény z Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Pokud máte v Office 365 nějaké uživatele nebo skupiny, které odkazují na odebraný název domény, musí být přejmenováni na doménu .onmicrosoft.com. Pokud vynutíte odstranění názvu domény, budou všichni uživatelé automaticky přejmenováni, v tomto příkladu na *uživatele\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Přihlaste se k [Centru pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globálním správcem pro klienta Azure AD.
  
7. Vyberte **Vlastní názvy domén**a přidejte název domény. Chcete-li ověřit vlastnictví názvu domény, budete muset zadat záznamy DNS TXT. 
  
   ![doména ověřena jako přidaná do Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Všichni uživatelé Power BI nebo služby Azure Rights Management, kteří mají licence přiřazené v tenantovi Office 365, musí uložit řídicí panely, pokud je název domény odebrán. Musí se přihlásit pomocí uživatelského jména, jako *je uživatel\@fourthcoffeexyz.onmicrosoft.com* spíše než uživatel *\@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Převzetí externího správce

Pokud už spravujete tenanta se službami Azure nebo Office 365, nemůžete přidat vlastní název domény, pokud je už ověřený v jiném tenantovi Azure AD. Ale z vašeho spravovaného tenanta ve službě Azure AD můžete převzít nespravovaného klienta jako převzetí externího správce. Obecný postup následuje podle článku [Přidání vlastní domény do služby Azure AD](../fundamentals/add-custom-domain.md).

Když ověříte vlastnictví názvu domény, Azure AD odebere název domény z nespravovaného klienta a přesune ho do stávajícího klienta. Převzetí nespravovaného adresáře externím správcem vyžaduje stejný proces ověření DNS TXT jako převzetí interního správce. Rozdíl je v tom, že následující jsou také přesunuty přes s názvem domény:

- Uživatelé
- Předplatná
- Přiřazení licencí

### <a name="support-for-external-admin-takeover"></a>Podpora převzetí externího správce
Převzetí externího správce je podporováno následujícími online službami:

- Azure Rights Management
- Exchange Online

Podporované plány služeb zahrnují:

- PowerApps zdarma
- PowerFlow zdarma
- RMS pro jednotlivce
- Microsoft Stream
- Bezplatná zkušební verze Dynamics 365

Převzetí externího správce není podporováno pro žádnou službu, která má plány služeb, které zahrnují SharePoint, OneDrive nebo Skype pro firmy. například prostřednictvím bezplatného předplatného Office. 

Volitelně můžete použít [ **možnost ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) pro odebrání názvu domény z nespravovaného klienta a jeho ověření v požadovaném tenantovi. 

#### <a name="more-information-about-rms-for-individuals"></a>Více informací o RMS pro jednotlivce

Pro [službu RMS pro jednotlivce](/azure/information-protection/rms-for-individuals), pokud je nespravovaný tenant ve stejné oblasti jako klient, který vlastníte, automaticky vytvořený klíč [klienta Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) a výchozí šablony [ochrany](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) se navíc přesunou s názvem domény.

Klíč a šablony nejsou přesunuty, pokud je nespravovaný klient v jiné oblasti. Například pokud nespravovaného klienta je v Evropě a organizace, které vlastníte je v Severní Americe.

Přestože služba RMS pro jednotlivce je navržena tak, aby podporovala ověřování Azure AD při otevírání chráněného obsahu, nebrání uživatelům také v ochraně obsahu. Pokud uživatelé chránili obsah službou RMS pro jednotlivce a klíč a šablony nebyly přesunuty, není tento obsah po převzetí domény přístupný.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Rutiny prostředí Azure AD PowerShell pro možnost Vynucené převzetí
Tyto rutiny použité v [příkladu prostředí PowerShell](#powershell-example)uvidíte.

Rutina | Využití
------- | -------
`connect-msolservice` | Po zobrazení výzvy se přihlaste ke spravovanému tenantovi.
`get-msoldomain` | Zobrazuje názvy domén přidružené k aktuálnímu klientovi.
`new-msoldomain –name <domainname>` | Přidá název domény do klienta jako Neověřené (zatím nebylo provedeno žádné ověření DNS).
`get-msoldomain` | Název domény je nyní zahrnut v seznamu doménových jmen přidružených ke spravovanému klientovi, ale je uveden jako **Neověřený**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Obsahuje informace, které mají být vloženy do nového záznamu DNS TXT pro doménu (MS =xxxxx). Ověření nemusí proběhnout okamžitě, protože trvá nějakou dobu, než se záznam TXT rozšíří, takže počkejte několik minut, než zvážíte možnost **-ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Pokud název domény stále není ověřen, můžete pokračovat s možností **-ForceTakeover.** Ověří, že záznam TXT byl vytvořen a zahajuje proces převzetí.<li>Možnost **-ForceTakeover** by měla být přidána do rutiny pouze při vynucení převzetí externího správce, například když nespravovaný tenant má služby Office 365 blokující převzetí.
`get-msoldomain` | V seznamu domén je nyní uveden název domény jako **Ověřený**.

> [!NOTE]
> Nespravovaná organizace Azure AD se odstraní 10 dní po uplatnění možnosti vnější možnost imitace převzetí.

### <a name="powershell-example"></a>Příklad PowerShellu

1. Připojte se k Azure AD pomocí přihlašovacích údajů, které byly použity k odpovědi na samoobslužné nabídky:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Získejte seznam domén:
  
   ```powershell
   Get-MsolDomain
   ```
3. Spusťte rutinu Get-MsolDomainVerificationDns a vytvořte výzvu:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Například:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Zkopírujte hodnotu (výzvu), která je vrácena z tohoto příkazu. Například:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Ve veřejném oboru názvů DNS vytvořte záznam TXT DNS obsahující hodnotu, kterou jste zkopírovali v předchozím kroku. Název tohoto záznamu je název nadřazené domény, takže pokud vytvoříte tento záznam o prostředku pomocí role DNS ze systému Windows Server, ponechejte název záznamu prázdný a vložte hodnotu do textového pole.
6. Spusťte rutinu Confirm-MsolDomain a ověřte výzvu:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Například:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Úspěšná výzva vás vrátí do výzvy bez chyby.

## <a name="next-steps"></a>Další kroky

* [Přidání vlastního názvu domény do Služby Azure AD](../fundamentals/add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
