---
title: Převzetí správce nespravovaného adresáře – Azure AD | Microsoft Docs
description: Postup převzetí názvu domény DNS v nespravované organizaci Azure AD (stínový tenant).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739363"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Převzetí nespravovaného adresáře v roli správce v Azure Active Directory

Tento článek popisuje dva způsoby, jak převzít název domény DNS v nespravovaném adresáři v Azure Active Directory (Azure AD). Když se samoobslužný uživatel zaregistruje ke cloudové službě, která využívá Azure AD, přidá se do nespravovaného adresáře Azure AD na základě svojí e-mailové domény. Další informace o samoobslužné registraci a registraci ke službě najdete v tématu [co je samoobslužná registrace pro Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Rozhodněte, jak chcete převzít nespravovaný adresář
Během procesu převzetí správce můžete vlastnictví prokázat způsobem popsaným v tématu [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md). Další části popisují prostředí pro správu podrobněji, ale tady je shrnutí:

* Když provedete [interní převzetí správce](#internal-admin-takeover) nespravovaného adresáře Azure, přidáte se jako globální správce tohoto nespravovaného adresáře. Do žádného jiného adresáře, který spravujete, se nebudou migrovat žádní uživatelé, domény ani plány služeb.

* Když provedete [externí převzetí správce](#external-admin-takeover) nespravovaného adresáře Azure, přidáte název domény DNS nespravovaného adresáře do svého spravovaného adresáře Azure. Když přidáte název domény, ve vašem spravovaném adresáři Azure se vytvoří mapování uživatelů na prostředky, aby uživatelé měli i nadále přístup ke službám bez přerušení. 

## <a name="internal-admin-takeover"></a>Převzetí interním správcem

Některé produkty, které zahrnují SharePoint a OneDrive, jako je například Microsoft 365, nepodporují externí převzetí. Pokud se jedná o váš scénář, nebo pokud jste správcem a chcete převzít nespravovanou nebo "stínovou" organizaci Azure AD, kterou vytvořili uživatelé, kteří použili samoobslužné registrace, můžete to udělat pomocí interního převzetí služeb správce.

1. Vytvořte uživatelský kontext v nespravované organizaci pomocí registrace Power BI. Pro pohodlí například tyto kroky předpokládají tuto cestu.

2. Otevřete [Power BI web](https://powerbi.com) a vyberte **začít zdarma**. Zadejte uživatelský účet, který používá název domény pro organizaci. například `admin@fourthcoffee.xyz` . Po zadání v ověřovacím kódu si v e-mailu ověřte potvrzovací kód.

3. V e-mailu s potvrzením od Power BI vyberte **Ano, to jsem já**.

4. Přihlaste se k [centru pro správu Microsoft 365](https://portal.office.com/admintakeover) pomocí uživatelského účtu Power BI. Zobrazí se zpráva, která vám dává pokyn, abyste se **stali správcem** názvu domény, který už byl ověřený v nespravované organizaci. Vyberte možnost **Ano, chci být správcem**.
  
   ![první snímek obrazovky, který se stane správcem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Přidejte záznam TXT, abyste prokázali, že vlastníte název domény **fourthcoffee.xyz** ve svém registrátoru názvu domény. V tomto příkladu je GoDaddy.com.
  
   ![Přidat záznam TXT pro název domény](./media/domains-admin-takeover/become-admin-txt-record.png)

Při ověřování záznamů TXT DNS ve vašem registrátoru názvů domén můžete spravovat organizaci Azure AD.

Po dokončení předchozích kroků teď jste globálním správcem čtvrté kávy v Microsoft 365. Pokud chcete název domény integrovat s ostatními službami Azure, můžete ho odebrat z Microsoft 365 a přidat ho do jiné spravované organizace v Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Přidání názvu domény do spravované organizace ve službě Azure AD

1. Otevřete [Centrum pro správu Microsoft 365](https://admin.microsoft.com).
2. Vyberte kartu **Uživatelé** a vytvořte nový uživatelský účet s názvem, jako je například *Uživatel \@ fourthcoffeexyz.onmicrosoft.com* , který nepoužívá vlastní název domény. 
3. Ujistěte se, že nový uživatelský účet má pro organizaci Azure AD oprávnění globálního správce.
4. Otevřete kartu **domény** v centru pro správu Microsoft 365, vyberte název domény a vyberte **Odebrat**. 
  
   ![Odebrat název domény z Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Pokud máte v Microsoft 365 nějaké uživatele nebo skupiny, které odkazují na odebraný název domény, musí být přejmenovány do domény. onmicrosoft.com. Pokud vynutíte odstranění názvu domény, všichni uživatelé budou automaticky přejmenováni v tomto příkladu *na \@ fourthcoffeexyz.onmicrosoft.com uživatele*.
  
6. Přihlaste se k [centru pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globálním správcem pro organizaci Azure AD.
  
7. Vyberte **vlastní názvy domén** a pak přidejte název domény. Aby bylo možné ověřit vlastnictví názvu domény, budete muset zadat záznamy TXT DNS. 
  
   ![Doména ověřena jako přidaná do Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Všichni uživatelé Power BI nebo služby Azure Rights Management, kteří mají licence přiřazené v Microsoft 365 organizaci, musí uložit své řídicí panely, pokud je název domény odebraný. Musí se přihlásit pomocí uživatelského jména, jako je například *uživatel \@ fourthcoffeexyz.onmicrosoft.com* , nikoli *Uživatel \@ fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Převzetí externím správcem

Pokud už organizaci spravujete se službami Azure nebo Microsoft 365, nemůžete přidat vlastní název domény, pokud už je ověřený v jiné organizaci Azure AD. Ze spravované organizace ve službě Azure AD se ale můžete přebírat nespravovanou organizaci jako převzetí externích správců. Obecný postup najdete v článku [Přidání vlastní domény do Azure AD](../fundamentals/add-custom-domain.md).

Když ověříte vlastnictví názvu domény, Azure AD Odebere název domény z nespravované organizace a přesune ji do vaší stávající organizace. Převzetí externích správců nespravovaného adresáře vyžaduje stejný proces ověření TXT DNS jako interní převzetí správce. Rozdíl je v tom, že se v rámci názvu domény přesunuly taky následující:

- Uživatelé
- Předplatná
- Přiřazení licencí

### <a name="support-for-external-admin-takeover"></a>Podpora pro převzetí externích správců
Převzetí externích správců je podporované následujícími online služby:

- Azure Rights Management
- Exchange Online

Mezi podporované plány služby patří:

- PowerApps zdarma
- PowerFlow zdarma
- RMS pro jednotlivce
- Microsoft Stream
- Bezplatná zkušební verze Dynamics 365

Převzetí externích správců se nepodporuje u žádné služby, která obsahuje plány služeb, které zahrnují SharePoint, OneDrive nebo Skype pro firmy. například prostřednictvím bezplatného předplatného Office. 

Volitelně můžete použít [možnost **ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) pro odebrání názvu domény z nespravované organizace a její ověření v požadované organizaci. 

#### <a name="more-information-about-rms-for-individuals"></a>Další informace o RMS pro jednotlivce

V případě [služby RMS pro jednotlivce](/azure/information-protection/rms-for-individuals)platí, že pokud je nespravovaná organizace ve stejné oblasti jako organizace, kterou vlastníte, automaticky se vytvoří [Azure Information Protection klíč organizace](/azure/information-protection/plan-implement-tenant-key) a [výchozí šablony ochrany](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) se také přesouvají s názvem domény.

Klíč a šablony nejsou přesunuty, pokud je nespravovaná organizace v jiné oblasti. Například pokud je nespravovaná organizace v Evropě a organizace, kterou vlastníte, je v Severní Amerika.

I když je služba RMS pro jednotlivce navržená tak, aby podporovala ověřování Azure AD pro otevření chráněného obsahu, nebrání uživatelům také v ochraně obsahu. Pokud uživatelé prováděli ochranu obsahu pomocí předplatného služby RMS pro jednotlivce a klíč a šablony nebyly přesunuty, nebude tento obsah po převzetí domény přístupný.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Rutiny Azure AD PowerShellu pro možnost ForceTakeover
Tyto rutiny se zobrazují v [příkladu PowerShellu](#powershell-example).

rutiny | Využití
------- | -------
`connect-msolservice` | Po zobrazení výzvy se přihlaste ke svojí spravované organizaci.
`get-msoldomain` | Zobrazuje názvy domén přidružené k aktuální organizaci.
`new-msoldomain –name <domainname>` | Přidá název domény do organizace jako Neověřeno (zatím se neprovede žádné ověření DNS).
`get-msoldomain` | Název domény je teď zahrnutý v seznamu názvů domén přidružených ke spravované organizaci, ale je uvedený jako **Neověřeno**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Poskytuje informace, které se vloží do nového záznamu TXT DNS pro doménu (MS = xxxxx). K ověření se nemusí okamžitě docházet, protože nějaký čas může rozšířit záznam TXT, takže Počkejte pár minut, než se pustíte do úvahy možnosti **-ForceTakeover** . 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Pokud se název domény stále neověřuje, můžete pokračovat s možností **-ForceTakeover** . Ověřuje, že byl vytvořen záznam TXT a je odpojený od procesu převzetí.<li>Možnost **-ForceTakeover** by se měla přidat do rutiny jenom v případě, že je vynucené převzetí externích správců, například když má nespravovaná organizace Microsoft 365 Services blokující převzetí.
`get-msoldomain` | Seznam domén nyní zobrazuje název domény jako **ověřený**.

> [!NOTE]
> Nespravovaná organizace Azure AD se odstraní 10 dnů po uplatnění možnosti externí převzetí.

### <a name="powershell-example"></a>Příklad PowerShellu

1. Připojte se k Azure AD s použitím přihlašovacích údajů, které se použily k reakci na nabídku samoobslužných služeb:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Získat seznam domén:
  
   ```powershell
   Get-MsolDomain
   ```
3. Spusťte rutinu Get-MsolDomainVerificationDns pro vytvoření výzvy:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Například:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Zkopírujte hodnotu (Challenge), která se vrátí z tohoto příkazu. Například:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Ve svém veřejném oboru názvů DNS vytvořte záznam TXT DNS, který obsahuje hodnotu, kterou jste zkopírovali v předchozím kroku. Název tohoto záznamu je název nadřazené domény, takže pokud vytvoříte tento záznam o prostředku pomocí role DNS z Windows serveru, ponechte název záznamu prázdný a vložte hodnotu do textového pole.
6. Spusťte rutinu Confirm-MsolDomain pro ověření výzvy:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Například:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Úspěšná výzva vás vrátí do výzvy bez chyby.

## <a name="next-steps"></a>Další kroky

* [Přidání názvu vlastní domény do Azure AD](../fundamentals/add-custom-domain.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
