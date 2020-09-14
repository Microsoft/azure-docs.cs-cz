---
title: Přidejte a ověřte vlastní názvy domén – Azure Active Directory | Microsoft Docs
description: Koncepce správy a postupy pro správu názvu domény v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f924cef12db974faae8fb8ed73f01ff8c9a3f8
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056247"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Správa vlastních názvů domén v Azure Active Directory

Název domény je důležitou součástí identifikátoru pro mnoho prostředků adresáře: Jedná se o část uživatelského jména nebo e-mailové adresy uživatele, část adresy pro skupinu a je někdy součástí identifikátoru URI ID aplikace pro aplikaci. Prostředek ve službě Azure Active Directory (Azure AD) může zahrnovat název domény, který patří do adresáře, který obsahuje prostředek. Pouze globální správce může spravovat domény v Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Nastavte primární název domény pro adresář služby Azure AD.

Při vytvoření adresáře se jako primární název domény použije i počáteční název domény, například ' contoso.onmicrosoft.com '. Primární doména je výchozí název domény pro nového uživatele při vytváření nového uživatele. Když se nastaví primární název domény, zjednoduší se tím proces pro správce vytváření nových uživatelů na portálu. Změna primárního názvu domény:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu, který je globálním správcem adresáře.
2. Vyberte **Azure Active Directory**.
3. Vyberte **Názvy vlastních domén**.
  
   ![Otevření stránky správy uživatelů](./media/domains-manage/add-custom-domain.png)
4. Vyberte název domény, kterou chcete být primární doménou.
5. Vyberte příkaz **vytvořit primární** . Po zobrazení výzvy potvrďte svůj výběr.
  
   ![Nastavit název domény jako primární](./media/domains-manage/make-primary-domain.png)

Můžete změnit primární název domény pro váš adresář tak, aby byla ověřená vlastní doména, která není federované. Změna primární domény pro váš adresář nemění uživatelské jméno pro žádné existující uživatele.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Přidání vlastních názvů domén do vaší organizace Azure AD

Můžete přidat až 900 názvů spravovaných domén. Pokud konfigurujete všechny domény pro federaci s místní službou Active Directory, můžete do každého adresáře přidat až 450 názvů domén.

## <a name="add-subdomains-of-a-custom-domain"></a>Přidat subdomény vlastní domény

Pokud chcete do adresáře přidat název domény třetí úrovně, například ' europe.contoso.com ', měli byste nejdřív přidat a ověřit doménu druhé úrovně, jako je například contoso.com. Subdoména je automaticky ověřena službou Azure AD. Chcete-li zjistit, zda je poddoménou, kterou jste přidali, ověřena, aktualizujte seznam domén v prohlížeči.

Poznámka

Pokud jste již přidali doménu contoso.com do tenanta služby Azure AD, můžete také přidat europe.contoso.com subdomény do druhého tenanta Azure AD. Při přidávání subdomény se zobrazí výzva k přidání záznamu TXT do poskytovatele hostingu DNS.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Jak postupovat v případě, že změníte registrátora DNS pro vlastní název domény

Pokud změníte registrátory DNS, neexistují žádné další konfigurační úlohy ve službě Azure AD. Název domény můžete dál používat s Azure AD bez přerušení. Pokud používáte vlastní název domény s Microsoft 365, Intune nebo dalšími službami, které spoléhají na vlastní názvy domén v Azure AD, přečtěte si dokumentaci k těmto službám.

## <a name="delete-a-custom-domain-name"></a>Odstranění vlastního názvu domény

Vlastní název domény můžete z Azure AD odstranit, pokud vaše organizace už tento název domény nepoužívá, nebo pokud potřebujete použít tento název domény s jinou službou Azure AD.

Pokud chcete odstranit vlastní název domény, musíte nejdřív zajistit, aby v adresáři nespoléhá na název domény žádné prostředky. Název domény nemůžete odstranit z adresáře, pokud:

* Každý uživatel má uživatelské jméno, e-mailovou adresu nebo adresu proxy serveru, který obsahuje název domény.
* Libovolná skupina má e-mailovou adresu nebo adresu proxy serveru, která zahrnuje název domény.
* Všechny aplikace v Azure AD mají identifikátor URI ID aplikace, který obsahuje název domény.

Než budete moct odstranit vlastní název domény, musíte změnit nebo odstranit tento prostředek v adresáři služby Azure AD.

### <a name="forcedelete-option"></a>ForceDelete – možnost

Název domény můžete **ForceDelete** v [centru pro správu Azure AD](https://aad.portal.azure.com) nebo pomocí [rozhraní Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta). Tyto možnosti používají asynchronní operace a aktualizují všechny odkazy z vlastního názvu domény jako user@contoso.com na počáteční výchozí název domény, například user@contoso.onmicrosoft.com . 

Chcete-li volat **ForceDelete** v Azure Portal, je nutné zajistit, aby bylo méně než 1000 odkazů na název domény a všechny odkazy, kde Exchange je služba zřizování, musí být aktualizována nebo odebrána v centru pro [správu serveru Exchange](https://outlook.office365.com/ecp/). To zahrnuje skupiny zabezpečení s povoleným e-mailem Exchange a distribuované seznamy. Další informace najdete v tématu [Odebrání skupin zabezpečení s povolenými e-maily](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups). Operace **ForceDelete** se taky nezdaří, pokud je splněná některá z následujících podmínek:

* Zakoupili jste doménu prostřednictvím služby Microsoft 365 Domain Subscription Services
* Jste partnerskou správou jménem jiné organizace pro zákazníky.

V rámci operace **ForceDelete** se provádí následující akce:

* Přejmenuje hlavního názvu uživatele (UPN), EmailAddress a ProxyAddress uživatelů odkazy na název vlastní domény na počáteční výchozí název domény.
* Přejmenuje EmailAddress skupin pomocí odkazů na název vlastní domény na počáteční výchozí název domény.
* Přejmenuje identifierUris aplikací pomocí odkazů na název vlastní domény na počáteční výchozí název domény.

Vrátí se chyba, když:

* Počet objektů, které mají být přejmenovány, je větší než 1000.
* Jedna z aplikací, která má být přejmenována, je aplikace s více klienty.

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč se odstranění domény nepodaří s chybou, která uvádí, že mám v tomto názvu domény hlavní skupiny Exchange?** <br>
**A:** V dnešní době jsou některé skupiny, jako jsou skupiny zabezpečení s povolenými poštou a distribuované seznamy, zřízené Exchangem a je potřeba je ručně vyčistit v [centru pro správu Exchange (EAC)](https://outlook.office365.com/ecp/). Může se jednat o záviset na ProxyAddresses, která spoléhá na vlastní název domény a bude nutné ji ručně aktualizovat na jiný název domény. 

**Otázka: jsem přihlášen jako správce contoso.com, \@ ale nemůžu odstranit název domény "contoso.com"?**<br>
**A:** Nejde odkazovat na název vlastní domény, který se pokoušíte odstranit v názvu uživatelského účtu. Zajistěte, aby účet globálního správce používal počáteční výchozí název domény (. onmicrosoft.com), například admin@contoso.onmicrosoft.com . Přihlaste se pomocí jiného účtu globálního správce, jako admin@contoso.onmicrosoft.com je například nebo jiný vlastní název domény, například "fabrikam.com", kde je účet admin@fabrikam.com .

**Otázka: kliknuli jste na tlačítko Odstranit doménu a zobrazí se `In Progress` stav operace odstranění. Jak dlouho to trvá? Co se stane, když dojde k chybě?**<br>
**A:** Operace odstranit doménu je asynchronní úloha na pozadí, která přejmenuje všechny odkazy na název domény. Měla by být dokončena do jedné nebo dvou minut. Pokud dojde k odstranění domény, ujistěte se, že nemáte:

* Aplikace nakonfigurované v názvu domény pomocí appIdentifierURI
* Libovolná poštovní skupina s odkazem na vlastní název domény
* Více než 1000 odkazů na název domény

Pokud zjistíte, že některá z podmínek nebyla splněna, ručně vyčistěte odkazy a pokuste se doménu odstranit znovu.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Použití PowerShellu nebo rozhraní Microsoft Graph API ke správě názvů domén

Většinu úloh správy pro názvy domén v Azure Active Directory lze také dokončit pomocí prostředí Microsoft PowerShell nebo programově pomocí Microsoft Graphho rozhraní API.

* [Použití PowerShellu ke správě názvů domén ve službě Azure AD](/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Typ prostředku domény](/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Další kroky

* [Přidání vlastních názvů domén](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Odebrání skupin zabezpečení s povoleným e-mailem Exchange v centru pro správu Exchange na vlastním názvu domény v Azure AD](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups)
* [ForceDelete vlastní název domény s rozhraním API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta)