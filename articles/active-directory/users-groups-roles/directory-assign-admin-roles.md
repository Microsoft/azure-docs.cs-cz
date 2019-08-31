---
title: Popis role správce a oprávnění – Azure Active Directory | Microsoft Docs
description: Role správce může přidávat uživatele, přiřazovat administrativní role, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/23/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f860b234db8cb0b40747ba042f1a845f58379597
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193867"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Oprávnění role správce v Azure Active Directory

Pomocí Azure Active Directory (Azure AD) můžete určit omezené správce ke správě úloh identity v méně privilegovaných rolích. Správcům je možné přiřadit tyto účely, jako je přidání nebo změna uživatelů, přiřazování rolí pro správu, Resetování uživatelských hesel, Správa uživatelských licencí a správa názvů domén. Výchozí uživatelská oprávnění se dají změnit jenom v nastavení uživatele v Azure AD.

## <a name="limit-the-use-of-global-administrator"></a>Omezení použití globálního správce

Uživatelé, kteří mají přiřazenou roli globálního správce, můžou číst a upravovat všechna nastavení správy ve vaší organizaci Azure AD. Ve výchozím nastavení má osoba, která se zaregistruje k předplatnému Azure, přiřazenou roli globálního správce pro organizaci Azure AD. Role správce můžou delegovat jenom globální správci a správci privilegovaných rolí. Pokud chcete snížit riziko pro vaši firmu, doporučujeme přiřadit tuto roli co nejmenším možným lidem ve vaší organizaci.

Jako osvědčený postup doporučujeme přiřadit tuto roli méně než 5 lidem ve vaší organizaci. Pokud máte více než pět uživatelů přiřazených k roli globálního správce ve vaší organizaci, je zde několik způsobů, jak omezit jejich používání.

### <a name="find-the-role-you-need"></a>Najděte roli, kterou potřebujete.

Pokud se frustrující, jak najít roli, kterou potřebujete pro seznam mnoha rolí, může Azure AD zobrazit podmnožiny rolí na základě kategorií rolí. Podívejte se na náš nový filtr **typu** pro [role a správce Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) , abyste viděli jenom role ve vybraném typu.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Role existuje nyní, která neexistuje, když jste přiřadili roli globálního správce.

Je možné, že se do služby Azure AD přidala role nebo role, které poskytují přesnější oprávnění, která se nedají použít při zvýšení úrovně uživatelů na globálního správce. V průběhu času zavádíme další role, které provádějí úlohy, které by mohly provádět jenom role globálního správce. Uvidíte ty, které se projeví v následujících [dostupných rolích](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Přiřazení nebo odebrání rolí správce

Další informace o přiřazování rolí pro správu uživateli v Azure Active Directory najdete v tématu [zobrazení a přiřazení rolí správce v Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostupné role

K dispozici jsou následující role správce:

### <a name="application-administratorapplication-administrator-permissions"></a>[Správce aplikace](#application-administrator-permissions)

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy aplikací. Tato role také uděluje možnost vyjádřit souhlas s delegovanými oprávněními a oprávnění aplikací, kromě Microsoft Graph a Azure AD Graph. Uživatelé přiřazení k této roli nebudou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé přiřazení k této roli můžou do aplikace přidat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Pokud identitě aplikace byl udělen přístup k Azure Active Directory, jako je například možnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, může uživatel přiřazený k této roli provádět tyto akce při zosobnění aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšením oprávnění, přes co může uživatel dělat prostřednictvím přiřazení rolí v Azure AD. Je důležité pochopit, že přiřazení uživatele k roli správce aplikace jim dává možnost zosobnit identitu aplikace.

### <a name="application-developerapplication-developer-permissions"></a>[Vývojář aplikace](#application-developer-permissions)

Uživatelé v této roli můžou vytvářet registrace aplikací, když je nastavení "uživatelé můžou registrovat aplikace" nastavené na ne. Tato role také uděluje oprávnění k souhlasu jednoho vlastního uživatele, pokud "uživatelé můžou udělit souhlas s aplikacemi, které přistupují k firemním datům" jejich jménem "nastavení je nastaveno na ne. Uživatelé přiřazení k této roli jsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

### <a name="authentication-administratorauthentication-administrator-permissions"></a>[Správce ověřování](#authentication-administrator-permissions)

Uživatelé s touto rolí můžou nastavovat nebo resetovat přihlašovací údaje hesla bez hesla a můžou aktualizovat hesla pro všechny uživatele. Správci ověřování můžou vyžadovat, aby se uživatelé znovu zaregistrovali u stávajících přihlašovacích údajů, které nepoužívají heslo (například MFA nebo FIDO), a odvolat **MFA MFA na zařízení**, které pro další přihlášení uživatelů, kteří nejsou správci, výzvu k MFA. přiřazené pouze následující role:

* Správce ověřování
* Uživatelé s oprávněním ke čtení adresářů
* Odesílatel pozvánek hostů
* Čtenář Centra zpráv
* Čtenář sestav

Role Správce ověřování je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit přihlašovací údaje pro uživatele, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna přihlašovacích údajů uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Příklad:

* Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům ověřování. Prostřednictvím této cesty může správce ověřování předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
* Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
* Skupiny zabezpečení a vlastníci skupiny Office 365, kteří můžou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
* Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
* Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

### <a name="azure-information-protection-administratorazure-information-protection-administrator-permissions"></a>[Správce Azure Information Protection](#azure-information-protection-administrator-permissions)

Uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfigurovat popisky pro zásady Azure Information Protection, spravovat šablony ochrany a aktivovat ochranu. Tato role neuděluje žádná oprávnění v centru ochrany identit, Privileged Identity Management, monitorování Service Health Office 365 nebo Office 365 Centrum zabezpečení a dodržování předpisů.

### <a name="b2c-user-flow-administratorb2c-user-flow-administrator-permissions"></a>[Správce toku B2C uživatele](#b2c-user-flow-administrator-permissions)

Uživatelé s touto rolí můžou vytvářet a spravovat Toky uživatelů B2C (označované také jako integrované zásady) na webu Azure Portal. Vytvořením nebo úpravou toků uživatelů mohou tito uživatelé měnit obsah HTML/CSS/JavaScript uživatelského prostředí, měnit požadavky MFA na tok uživatelů, měnit deklarace identity v tokenu a upravovat nastavení relace pro všechny zásady v tenantovi. Na druhé straně tato role nezahrnuje možnost kontrolovat data uživatelů ani měnit atributy, které jsou součástí schématu tenanta. Změny v architektuře prostředí identity (neboli vlastní) zásady jsou také mimo rozsah této role.

### <a name="b2c-user-flow-attribute-administratorb2c-user-flow-attribute-administrator-permissions"></a>[Správce atributů toku uživatele B2C](#b2c-user-flow-attribute-administrator-permissions)

Uživatelé s touto rolí přidají nebo odstraní vlastní atributy dostupné všem uživatelským tokům v tenantovi. Uživatelé s touto rolí mohou například měnit nebo přidávat nové prvky do schématu koncového uživatele a ovlivnit chování všech toků uživatelů a nepřímo způsobit změny v tom, jaká data mohou být požádána o koncové uživatele a která jsou nakonec odeslána jako deklarace do aplikací. Tato role nemůže upravovat toky uživatelů.

### <a name="b2c-ief-keyset-administratorb2c-ief-keyset-administrator-permissions"></a>[B2C IEF, správce sady klíčů](#b2c-ief-keyset-administrator-permissions)

Uživatel může vytvářet a spravovat klíče a tajné kódy zásad pro šifrování tokenů, signatury tokenů a šifrování a dešifrování deklarací identity. Přidáním nových klíčů do existujících kontejnerů klíčů může tento omezený správce podle potřeby přeměnit tajné klíče, aniž by to mělo vliv na stávající aplikace. Tento uživatel může zobrazit úplný obsah těchto tajných kódů a jejich datum vypršení platnosti i po jejich vytvoření.

> [!IMPORTANT]
> Tato role je citlivá. Role správce sady klíčů by měla být pečlivě auditována a přiřazena se opatrně během předprodukčních a produkčních verzí.

### <a name="b2c-ief-policy-administratorb2c-ief-policy-administrator-permissions"></a>[Správce zásad IEF B2C](#b2c-ief-policy-administrator-permissions)

Uživatelé v této roli mají možnost vytvářet, číst, aktualizovat a odstraňovat všechny vlastní zásady v Azure AD B2C a mají proto úplnou kontrolu nad architekturou prostředí identity v příslušném Azure AD B2C tenantovi. Tím, že uživatel upraví zásady, může vytvořit přímou federaci s externími zprostředkovateli identity, změnit schéma adresáře, změnit veškerý obsah směřující na uživatele (HTML, CSS, JavaScript), změnit požadavky na dokončení ověřování, vytvořit nové uživatele, odeslat uživatelská data do externích systémů včetně úplných migrací a upravují všechny informace o uživateli, včetně citlivých polí, jako jsou hesla a telefonní čísla. Naopak tato role nemůže měnit šifrovací klíče ani upravovat tajné klíče používané pro federaci v tenantovi.

> [!IMPORTANT]
> Správce zásad IEF B2 je vysoce citlivá role, kterou je třeba přiřadit pro klienty v produkčním prostředí velmi omezeného základu. Aktivity těchto uživatelů by měly být pečlivě auditovány, zejména pro klienty v produkčním prostředí.

### <a name="billing-administratorbilling-administrator-permissions"></a>[Správce fakturace](#billing-administrator-permissions)

Umožňuje nákupy, spravovat předplatná, spravovat lístky podpory a monitorovat stav služby.

### <a name="cloud-application-administratorcloud-application-administrator-permissions"></a>[Správce cloudové aplikace](#cloud-application-administrator-permissions)

Uživatelé v této roli mají stejná oprávnění jako role správce aplikace, kromě možnosti spravovat proxy aplikace. Tato role uděluje možnost vytvářet a spravovat všechny aspekty podnikových aplikací a registrací aplikací. Tato role také uděluje možnost vyjádřit souhlas s delegovanými oprávněními a oprávnění aplikací, kromě Microsoft Graph a Azure AD Graph. Uživatelé přiřazení k této roli nebudou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé přiřazení k této roli můžou do aplikace přidat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Pokud identitě aplikace byl udělen přístup k Azure Active Directory, jako je například možnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, může uživatel přiřazený k této roli provádět tyto akce při zosobnění aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšením oprávnění, přes co může uživatel dělat prostřednictvím přiřazení rolí v Azure AD. Je důležité pochopit, že přiřazení uživatele k roli správce cloudové aplikace jim dává možnost zosobnit identitu aplikace.

### <a name="cloud-device-administratorcloud-device-administrator-permissions"></a>[Správce cloudového zařízení](#cloud-device-administrator-permissions)

Uživatelé v této roli můžou povolit, zakázat a odstranit zařízení ve službě Azure AD a číst klíče Windows 10 BitLocker (pokud jsou k dispozici) v Azure Portal. Role neuděluje oprávnění ke správě dalších vlastností v zařízení.

### <a name="compliance-administratorcompliance-administrator-permissions"></a>[Správce dodržování předpisů](#compliance-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících s dodržováním předpisů v Microsoft 365 centrum dodržování předpisů, Microsoft 365 centrum pro správu, Azure a Office 365 Centrum zabezpečení a dodržování předpisů. Assignees může také spravovat všechny funkce v centru pro správu Exchange a týmy & centra pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365. Další informace najdete v informacích [o rolích správce Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

V | Může
----- | ----------
[Centrum kompatibility Microsoft 365](https://protection.office.com) | Chraňte a spravujte data vaší organizace napříč Microsoft 365 službami<br>Spravovat výstrahy dodržování předpisů
[Správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledovat, přiřazovat a ověřovat aktivity dodržování předpisů právními předpisy vaší organizace
[Sada Office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Provádět právní a vyšetřovací data<br>Správa požadavku subjektu dat
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Může vytvářet a upravovat zásady souborů a umožňovat akce zásad správného řízení souborů.<br> Může zobrazit všechny předdefinované sestavy v části Správa dat

### <a name="compliance-data-administratorcompliance-data-administrator-permissions"></a>[Správce dat dodržování předpisů](#compliance-data-administrator-permissions)

Uživatelé s touto rolí mají oprávnění sledovat data v Microsoft 365 centrum dodržování předpisů, Microsoft 365 centrum pro správu a Azure. Uživatelé také mohou sledovat data o dodržování předpisů v centru pro správu Exchange, správců dodržování předpisů a týmech & centru pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365.

V | Může
----- | ----------
[Centrum kompatibility Microsoft 365](https://protection.office.com) | Monitorování zásad souvisejících s dodržováním předpisů napříč Microsoft 365 službami<br>Spravovat výstrahy dodržování předpisů
[Správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledovat, přiřazovat a ověřovat aktivity dodržování předpisů právními předpisy vaší organizace
[Sada Office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Provádět právní a vyšetřovací data<br>Správa požadavku subjektu dat
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Může vytvářet a upravovat zásady souborů a umožňovat akce zásad správného řízení souborů.<br> Může zobrazit všechny předdefinované sestavy v části Správa dat

### <a name="conditional-access-administratorconditional-access-administrator-permissions"></a>[Správce podmíněného přístupu](#conditional-access-administrator-permissions)

Uživatelé s touto rolí mají možnost spravovat Azure Active Directory nastavení podmíněného přístupu.
> [!NOTE]
> Aby bylo možné nasadit zásady podmíněného přístupu Exchange ActiveSync v Azure, musí být uživatel také globálním správcem.

### <a name="customer-lockbox-access-approvercustomer-lockbox-access-approver-permissions"></a>[Schvalovatel přístupu Customer Lockbox](#customer-lockbox-access-approver-permissions)

Spravuje [žádosti Customer Lockbox](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) ve vaší organizaci. Přijímají e-mailová oznámení týkající se Customer Lockbox požadavků a můžou schvalovat a zamítat žádosti z centra pro správu Microsoft 365. Také je možné zapnout nebo vypnout funkci Customer Lockbox. Pouze globální Správci mohou resetovat hesla uživatelů přiřazených k této roli.

### <a name="desktop-analytics-administratordesktop-analytics-administrator-permissions"></a>[Správce Desktop Analytics](#desktop-analytics-administrator-permissions)


Uživatelé v této roli můžou spravovat služby zásad & pro stolní počítače a přizpůsobení Office. Pro Desktop Analytics to zahrnuje možnost Zobrazit inventář assetů, vytvářet plány nasazení, zobrazovat stav nasazení a stavu. V případě služby Office Customization & Policy tato role umožňuje uživatelům spravovat zásady Office.

### <a name="device-administratordevice-administrators-permissions"></a>[Správce zařízení](#device-administrators-permissions)

Tato role je k dispozici pro přiřazení pouze jako další místní správce v [nastavení zařízení](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Uživatelé s touto rolí se stanou Správci místních počítačů na všech zařízeních s Windows 10, která jsou připojená k Azure Active Directory. Neposkytují možnost spravovat objekty zařízení v Azure Active Directory.

### <a name="directory-readersdirectory-readers-permissions"></a>[Čtečky adresářů](#directory-readers-permissions)

Toto je role, která by se měla přiřadit jenom k starším aplikacím, které nepodporují [architekturu souhlasu](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nepřiřazujte ji uživatelům.

### <a name="directory-synchronization-accountsdirectory-synchronization-accounts-permissions"></a>[Účty synchronizace adresářů](#directory-synchronization-accounts-permissions)

Nepoužívejte. Tato role je automaticky přiřazena ke službě Azure AD Connect a není určena ani podporována pro jiné použití.

### <a name="directory-writersdirectory-writers-permissions"></a>[Zapisovače adresářů](#directory-writers-permissions)

Toto je starší role, která má být přiřazena aplikacím, které nepodporují [rámec souhlasu](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Neměl by být přiřazený žádnému uživateli.

### <a name="dynamics-365-administrator--crm-administratorcrm-service-administrator-permissions"></a>[Správce Dynamics 365/správce CRM](#crm-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Dynamics 365 online, pokud je služba k dispozici, a také možnost spravovat lístky podpory a monitorovat stav služby. Další informace o [správě tenanta pomocí role správce služby](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako správce služby Dynamics 365. Ve [Azure Portal](https://portal.azure.com)je to "Dynamics 365 Administrator".

### <a name="exchange-administratorexchange-service-administrator-permissions"></a>[Správce Exchange](#exchange-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Exchange Online, pokud je tato služba k dispozici. Má také možnost vytvářet a spravovat všechny skupiny sady Office 365, spravovat lístky podpory a monitorovat stav služby. Další informace najdete v informacích [o rolích správce Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Exchange". Je to "správce Exchange" v [Azure Portal](https://portal.azure.com). Je to "Exchange Online Administrator" v [centru pro správu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

### <a name="external-identity-provider-administratorexternal-identity-provider-administrator-permissions"></a>[Správce externích zprostředkovatelů identity](#external-identity-provider-administrator-permissions)

Tento správce spravuje federaci mezi klienty Azure Active Directory a externími zprostředkovateli identity. S touto rolí můžou uživatelé přidávat nové zprostředkovatele identity a konfigurovat všechna dostupná nastavení (například cestu pro ověřování, ID služby, přiřazené kontejnery klíčů). Tento uživatel může klientovi povolit, aby důvěřoval ověřování od externích zprostředkovatelů identity. Výsledný dopad na činnost koncového uživatele závisí na typu tenanta:

* Azure Active Directory klienty pro zaměstnance a partnery: Přidání federace (např. Gmail) okamžitě ovlivní všechny pozvánky hostů, které ještě nebyly uplatněny. Viz téma [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Klienti Azure Active Directory B2C: Přidání federace (například s Facebookem nebo s jinou organizací Azure AD) nemá okamžitě vliv na toky koncového uživatele, dokud se poskytovatel identity nepřidá jako možnost v toku uživatele (neboli vestavěné zásady). Příklad najdete v tématu [konfigurace účet Microsoft jako zprostředkovatele identity](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) . Pro změnu toků uživatelů se vyžaduje omezená role "správce toku uživatelů B2C".

### <a name="global-administrator--company-administratorcompany-administrator-permissions"></a>[Globální správce/správce společnosti](#company-administrator-permissions)

Uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory a také službám, které používají Azure Active Directory identity, jako je Microsoft 365 Security Center, Microsoft 365 Center pro dodržování předpisů, Exchange Online, SharePoint Online a Online Skype pro firmy. Osoba, která se zaregistruje do tenanta Azure Active Directory, se stal globálním správcem. Další role správců můžou přiřazovat jenom globální správci. Ve vaší společnosti může být víc než jeden globální správce. Globální správci můžou resetovat heslo každého uživatele a všech ostatních správců.

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako správce společnosti. Jedná se o globální správce v [Azure Portal](https://portal.azure.com).
>
>

### <a name="guest-inviterguest-inviter-permissions"></a>[Pozvánka hosta](#guest-inviter-permissions)

Uživatelé v této roli můžou spravovat Azure Active Directory pozvání uživatele hosta B2B, když **můžou členové pozvat** uživatele na hodnotu ne. Další informace o spolupráci B2B v rámci spolupráce B2B v [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nezahrnuje žádná další oprávnění.

### <a name="helpdesk-administratorhelpdesk-administrator-permissions"></a>[Správce helpdesku](#helpdesk-administrator-permissions)

Uživatelé s touto rolí můžou měnit hesla, zrušit platnost tokenů aktualizace, spravovat žádosti o služby a monitorovat stav služby. Zrušení platnosti aktualizačního tokenu vynutí, aby se uživatel znovu přihlásil. Správci helpdesku můžou resetovat hesla a zrušit platnost tokenů aktualizace jiných uživatelů, kteří nejsou správci, nebo jim přiřadit jenom následující role:

* Uživatelé s oprávněním ke čtení adresářů
* Odesílatel pozvánek hostů
* Správce technické podpory
* Čtenář Centra zpráv
* Čtenář sestav

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit hesla pro lidi, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna hesla uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Příklad:

* Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům helpdesku. Prostřednictvím této cesty může správce helpdesku předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
* Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
* Skupiny zabezpečení a vlastníci skupiny Office 365, kteří můžou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
* Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
* Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

> [!NOTE]
> Delegování oprávnění pro správu pro podmnožiny uživatelů a používání zásad u podmnožiny uživatelů je možné s [jednotkami pro správu (Preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
>
> Tato role se dřív nazývala "správce hesel" v [Azure Portal](https://portal.azure.com/). Změnili jsme název na "správce helpdesku" tak, aby odpovídal názvu ve službě Azure AD PowerShell, službě Azure AD Graph API a Microsoft Graph API.

### <a name="intune-administratorintune-service-administrator-permissions"></a>[Správce Intune](#intune-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Intune online, pokud je tato služba k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení, aby bylo možné přidružit zásady, a také vytvářet a spravovat skupiny. Další informace najdete v [řízení správy na základě rolí (RBAC) pomocí Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Intune". Ve [Azure Portal](https://portal.azure.com)je to správce Intune.

### <a name="kaizala-administratorkaizala-administrator-permissions"></a>[Správce Kaizala](#kaizala-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění ke správě nastavení v rámci Microsoft Kaizala, kdy je služba k dispozici, a také možnost spravovat lístky podpory a monitorovat stav služby. Kromě toho může uživatel získat přístup k sestavám souvisejícím s přijetím & používání Kaizala podle členů organizace a obchodních sestav vytvořených pomocí akcí Kaizala.

### <a name="license-administratorlicense-administrator-permissions"></a>[Správce licencí](#license-administrator-permissions)

Uživatelé v této roli můžou přidávat, odebírat a aktualizovat přiřazení licencí pro uživatele, skupiny (pomocí licencování na základě skupin) a spravovat umístění používání u uživatelů. Role neuděluje možnost kupovat ani spravovat odběry, vytvářet a spravovat skupiny ani vytvářet ani spravovat uživatele nad rámec tohoto místa použití. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="message-center-privacy-readermessage-center-privacy-reader-permissions"></a>[Čtenář ochrany osobních údajů centra zpráv](#message-center-privacy-reader-permissions)

Uživatelé v této roli můžou monitorovat všechna oznámení v centru zpráv, včetně zpráv o soukromí dat. Čtenáři ochrany osobních údajů centra zpráv získají e-mailová oznámení, včetně těch, které se týkají ochrany osobních údajů a můžou zrušit odběr pomocí předvoleb centra zpráv. Jenom globální správce a čtenář ochrany osobních údajů centra zpráv můžou číst zprávy o ochraně dat. Tato role navíc obsahuje možnost zobrazení skupin, domén a předplatných. Tato role nemá oprávnění k zobrazení, vytvoření nebo správě žádostí o služby.

### <a name="message-center-readermessage-center-reader-permissions"></a>[Čtenář centra zpráv](#message-center-reader-permissions)

Uživatelé v této roli můžou monitorovat oznámení a poradenské aktualizace stavu v [centru zpráv Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro jejich organizaci na konfigurovaných službách, jako je Exchange, Intune a Microsoft Teams. Čtečky centra zpráv obdrží týdenní e-mailové zprávy o příspěvcích, aktualizacích a můžou sdílet příspěvky centra zpráv v Office 365. Uživatelé přiřazení k této roli ve službě Azure AD budou mít přístup jen pro čtení ke službám Azure AD, jako jsou uživatelé a skupiny. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="partner-tier1-supportpartner-tier1-support-permissions"></a>[Podpora partnerů Tier1](#partner-tier1-support-permissions)

Nepoužívejte. Tato role se už nepoužívá a v budoucnu se odebere z Azure AD. Tato role je určená pro použití malým počtem partnerů Microsoftu pro prodej a není určená pro obecné použití.

### <a name="partner-tier2-supportpartner-tier2-support-permissions"></a>[Podpora partnerů 2](#partner-tier2-support-permissions)

Nepoužívejte. Tato role se už nepoužívá a v budoucnu se odebere z Azure AD. Tato role je určená pro použití malým počtem partnerů Microsoftu pro prodej a není určená pro obecné použití.

### <a name="password-administratorpassword-administrator-permissions"></a>[Správce hesel](#password-administrator-permissions)

Uživatelé s touto rolí mají omezené možnosti správy hesel. Tato role neuděluje schopnost spravovat žádosti o služby nebo monitorovat stav služby. Správci hesel můžou resetovat hesla jiných uživatelů, kteří nejsou správci, nebo členové následujících rolí:

* Uživatelé s oprávněním ke čtení adresářů
* Odesílatel pozvánek hostů
* Správce hesel

### <a name="power-bi-administratorpower-bi-service-administrator-permissions"></a>[Správce Power BI](#power-bi-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Power BI, když je služba přítomná, a také možnost spravovat lístky podpory a monitorovat stav služby. Další informace o [principu role správce Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako "Power BI Správce služby". V [Azure Portal](https://portal.azure.com)je "Power BI správce".

### <a name="privileged-authentication-administratorprivileged-authentication-administrator-permissions"></a>[Správce privilegovaného ověřování](#privileged-authentication-administrator-permissions)

Uživatelé s touto rolí můžou nastavit nebo resetovat přihlašovací údaje hesla pro všechny uživatele, včetně globálních správců, a můžou aktualizovat hesla pro všechny uživatele. Správci privilegovaného ověřování můžou vynutit, aby se uživatelé znovu zaregistrovali proti stávajícím přihlašovacím údajům bez hesla (například MFA, FIDO) a odvolali si MFA na zařízení. při příštím přihlášení všech uživatelů se zobrazí dotaz na MFA.

### <a name="privileged-role-administratorprivileged-role-administrator-permissions"></a>[Správce privilegovaných rolí](#privileged-role-administrator-permissions)

Uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure Active Directory a taky v Azure AD Privileged Identity Management. Kromě toho tato role umožňuje správu všech aspektů Privileged Identity Management a jednotek pro správu.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přiřazení pro všechny role Azure AD, včetně role globálního správce. Tato role nezahrnuje žádné další privilegované možnosti ve službě Azure AD, jako je vytváření nebo aktualizace uživatelů. Uživatelé přiřazení k této roli ale můžou udělit další role nebo jiné další oprávnění.

### <a name="reports-readerreports-reader-permissions"></a>[Čtečka sestav](#reports-reader-permissions)

Uživatelé s touto rolí mohou zobrazovat data vytváření sestav o využití a řídicí panel sestavy v centru pro správu Microsoft 365 a kontext pro přijetí v Power BI. Role navíc poskytuje přístup k sestavám přihlašování a aktivitám v Azure AD a datům vráceným rozhraním API pro vytváření sestav Microsoft Graph. Uživatel přiřazený k roli čtenáře sestav má přístup pouze k relevantním metrikám využití a přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu k centrům pro správu specifickým pro konkrétní produkt, jako je Exchange. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="search-administratorsearch-administrator-permissions"></a>[Správce hledání](#search-administrator-permissions)

Uživatelé v této roli mají plný přístup ke všem funkcím pro správu služby Microsoft Search v centru pro správu Microsoft 365. Správce vyhledávání může delegovat role správců vyhledávání a hledání na uživatele a vytvářet a spravovat obsah, jako jsou záložky, Q & jako a umístění. Kromě toho mohou uživatelé zobrazit Centrum zpráv, monitorovat stav služeb a vytvářet žádosti o služby.

### <a name="search-editorsearch-editor-permissions"></a>[Editor hledání](#search-editor-permissions)

Uživatelé v této roli můžou vytvářet, spravovat a odstraňovat obsah pro Microsoft Search v centru pro správu Microsoft 365, včetně záložek, Q & jako a umístění.

### <a name="security-administratorsecurity-administrator-permissions"></a>[Správce zabezpečení](#security-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících se zabezpečením v Microsoft 365 Centrum zabezpečení a dodržování předpisů Security Center, Azure Active Directory Identity Protection, Azure Information Protection a Office 365. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v sadě office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Monitorování zásad souvisejících se zabezpečením napříč Microsoft 365 službami<br>Správa bezpečnostních hrozeb a výstrah<br>Zobrazit sestavy
Centrum Identity Protection | Všechna oprávnění role čtenář zabezpečení<br>Kromě toho možnost provádět všechny operace centra služby Identity Protection s výjimkou resetování hesel
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Všechna oprávnění role čtenář zabezpečení<br>**Nejde** spravovat přiřazení rolí nebo nastavení role Azure AD.
[Sada Office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad zabezpečení<br>Zobrazení, zkoumání a reakce na bezpečnostní hrozby<br>Zobrazit sestavy
Rozšířená ochrana před internetovými útoky Azure | Monitorování a reakce na podezřelé aktivity zabezpečení
Ochrana ATP a EDR v programu Windows Defender | Přiřazení rolí<br>Správa skupin počítačů<br>Konfigurace detekce hrozeb a automatizované nápravy koncových bodů<br>Zobrazit, prozkoumat a reagovat na výstrahy
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazí informace o uživatelích, zařízeních, registraci, konfiguraci a aplikacích.<br>Nejde dělat změny v Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Přidávání správců, přidávání zásad a nastavení, nahrávání protokolů a provádění akcí zásad správného řízení
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Může zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.
[Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

### <a name="security-operatorsecurity-operator-permissions"></a>[Operátor zabezpečení](#security-operator-permissions)

Uživatelé s touto rolí můžou spravovat výstrahy a mít globální přístup jen pro čtení k funkci související se zabezpečením, včetně všech informací v Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management a Office 365. Centrum zabezpečení a dodržování předpisů. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v sadě office 365 Centrum zabezpečení a dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení hrozeb
Centrum Identity Protection | Všechna oprávnění role čtenář zabezpečení<br>Kromě toho možnost provádět všechny operace centra služby Identity Protection s výjimkou resetování hesel
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Všechna oprávnění role čtenář zabezpečení
[Sada Office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení
Ochrana ATP a EDR v programu Windows Defender | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Všechna oprávnění role čtenář zabezpečení
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Všechna oprávnění role čtenář zabezpečení
[Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

### <a name="security-readersecurity-reader-permissions"></a>[Čtecí modul zabezpečení](#security-reader-permissions)

Uživatelé s touto rolí mají globální přístup jen pro čtení k funkci související se zabezpečením, včetně všech informací v Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management a také možnost číst Azure Active. Sestavy pro přihlášení k adresáři a protokoly auditu a v sadě Office 365 Centrum zabezpečení a dodržování předpisů. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v sadě office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Zobrazení zásad souvisejících se zabezpečením napříč Microsoft 365 službami<br>Zobrazit bezpečnostní hrozby a výstrahy<br>Zobrazit sestavy
Centrum Identity Protection | Čtení všech sestav zabezpečení a informací o nastavení pro funkce zabezpečení<br><ul><li>Ochrana proti nevyžádané poště<li>Šifrování<li>Prevence ztráty dat<li>Anti-malware<li>Rozšířená ochrana před internetovými útoky<li>Ochrana proti podvodným zprávám<li>Pravidla mailflow
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Má přístup jen pro čtení ke všem informacím, které jsou v Azure AD Privileged Identity Management. Zásady a sestavy pro přiřazení rolí Azure AD a kontroly zabezpečení.<br>**Nelze** se zaregistrovat k Azure AD Privileged Identity Management ani provádět žádné změny. V portálu Privileged Identity Management nebo prostřednictvím PowerShellu může osoba v této roli aktivovat další role (například správce globálních správců nebo privilegovaných rolí), pokud je pro ně uživatel oprávněný.
[Sada Office 365 Centrum zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zobrazení zásad zabezpečení<br>Zobrazit a prozkoumat bezpečnostní hrozby<br>Zobrazit sestavy
Ochrana ATP a EDR v programu Windows Defender | Zobrazit a prozkoumat výstrahy. Když zapnete řízení přístupu na základě role v ochraně ATP v programu Windows Defender, uživatelé s oprávněním jen pro čtení, jako je role čtenář zabezpečení Azure AD, ztratí přístup, dokud nebudou přiřazeni k roli ochrany ATP v programu Windows Defender.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazí informace o uživatelích, zařízeních, registraci, konfiguraci a aplikacích. V Intune nejde dělat změny.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Může zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny.
[Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

### <a name="service-support-administratorservice-support-administrator-permissions"></a>[Správce služby Service support](#service-support-administrator-permissions)

Uživatelé s touto rolí můžou otevřít žádosti o podporu od Microsoftu pro Azure a služby Office 365 a zobrazit řídicí panel služby a Centrum zpráv v [centru pro správu](https://admin.microsoft.com) [Azure Portal](https://portal.azure.com) a Microsoft 365. Další informace najdete v informacích [o rolích správce Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell se tato role identifikuje jako správce služby Service support. Je to "Správce služeb" v [Azure Portal](https://portal.azure.com), centru pro [správu Microsoft 365](https://admin.microsoft.com)a na portálu Intune.

### <a name="sharepoint-administratorsharepoint-service-administrator-permissions"></a>[Správce služby SharePoint](#sharepoint-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft SharePoint Online, pokud je služba k dispozici, a také možnost vytvářet a spravovat všechny skupiny sady Office 365, spravovat lístky podpory a monitorovat stav služby. Další informace najdete v informacích [o rolích správce Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako "Správce služby SharePoint". Je to "Správce služby SharePoint" v [Azure Portal](https://portal.azure.com).

### <a name="skype-for-business--lync-administratorlync-service-administrator-permissions"></a>[Správce Skypu pro firmy/Lync](#lync-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, když je služba přítomná, a také spravovat atributy uživatelů pro Skype v Azure Active Directory. Kromě toho tato role uděluje možnost spravovat lístky podpory a monitorovat stav služeb a přistupovat k týmům a centru pro správu Skypu pro firmy. Účet musí být také licencován pro týmy nebo nemůže spustit rutiny prostředí PowerShell pro týmy. Další informace najdete v části informace o licencích [pro role správce Skypu pro firmy](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) a týmy na [webu Skype pro firmy a v doplňku pro licencování Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) .

> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Lync". Ve [Azure Portal](https://portal.azure.com/)je to správce Skypu pro firmy.

### <a name="teams-administratorteams-service-administrator-permissions"></a>[Správce týmů](#teams-service-administrator-permissions)

Uživatelé v této roli můžou spravovat všechny aspekty úloh Microsoft Teams prostřednictvím Microsoft Teams & centra pro správu Skypu pro firmy a příslušné moduly PowerShellu. Mezi další oblasti patří i všechny nástroje pro správu, které se týkají telefonního subsystému, zasílání zpráv, schůzek a týmů. Tato role navíc uděluje možnost vytvářet a spravovat všechny skupiny Office 365, spravovat lístky podpory a monitorovat stav služby.
> [!NOTE]
> V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označená jako "Správce služby týmu". Je to "správce týmů" v [Azure Portal](https://portal.azure.com).

### <a name="teams-communications-administratorteams-communications-administrator-permissions"></a>[Správce komunikace týmů](#teams-communications-administrator-permissions)

Uživatelé v této roli můžou spravovat aspekty úloh Microsoft Teams souvisejících se službou Voice & telefonního subsystému. To zahrnuje nástroje pro správu přiřazení telefonního čísla, zásady hlasu a schůzky a úplný přístup ke sadě nástrojů pro analýzu volání.

### <a name="teams-communications-support-engineerteams-communications-support-engineer-permissions"></a>[Týmy Communications support inženýr](#teams-communications-support-engineer-permissions)

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelem v Microsoft Teams & v centru pro správu Skypu pro firmy. Uživatelé v této roli mohou zobrazit úplné informace o záznamech volání pro všechny účastnící se účastníky. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="teams-communications-support-specialistteams-communications-support-specialist-permissions"></a>[Týmy Communications support specialisty](#teams-communications-support-specialist-permissions)

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelem v Microsoft Teams & v centru pro správu Skypu pro firmy. Uživatelé v této roli můžou zobrazit jenom podrobnosti o uživateli ve volání pro konkrétního uživatele, který si vyhledali. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="user-administratoruser-administrator-permissions"></a>[Správce uživatelů](#user-administrator-permissions)

Uživatelé s touto rolí můžou vytvářet uživatele a spravovat všechny aspekty uživatelů s některými omezeními (viz níže) a můžou aktualizovat zásady vypršení platnosti hesla. Uživatelé s touto rolí můžou navíc vytvářet a spravovat všechny skupiny. Tato role také zahrnuje možnost vytvářet a spravovat zobrazení uživatelů, spravovat lístky podpory a monitorovat stav služby.

| | |
| --- | --- |
|Obecná oprávnění|<p>Vytváření uživatelů a skupin</p><p>Vytváření a Správa uživatelských zobrazení</p><p>Správa lístků podpory Office<p>Aktualizace zásad vypršení platnosti hesla|
|<p>U všech uživatelů, včetně všech správců</p>|<p>Spravovat licence</p><p>Spravovat všechny vlastnosti uživatele kromě hlavního názvu uživatele</p>
|Pouze pro uživatele, kteří nejsou správci, nebo v některé z následujících rolí s omezeným oprávněním správce:<ul><li>Uživatelé s oprávněním ke čtení adresářů<li>Odesílatel pozvánek hostů<li>Správce technické podpory<li>Čtenář Centra zpráv<li>Čtenář sestav<li>Správce uživatelů|<p>Odstranění a obnovení</p><p>Zakázat a povolit</p><p>Zrušit platnost obnovovacích tokenů</p><p>Spravovat všechny vlastnosti uživatele včetně hlavního názvu uživatele</p><p>Resetovat heslo</p><p>Aktualizace (FIDO) – klíče zařízení</p>

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit hesla pro lidi, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna hesla uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Příklad:

* Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům uživatelů. Prostřednictvím této cesty může správce uživatelů předpokládat identitu vlastníka aplikace a pak dále předpokládat identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
* Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
* Skupiny zabezpečení a vlastníci skupiny Office 365, kteří můžou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
* Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
* Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

## <a name="role-permissions"></a>Oprávnění role

Následující tabulky popisují specifická oprávnění v Azure Active Directory daných rolím. Některé role mohou mít další oprávnění ve službách Microsoftu mimo Azure Active Directory.

### <a name="application-administrator-permissions"></a>Oprávnění správce aplikace

Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| microsoft.aad.directory/applications/create | Vytváření aplikací v Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Odstraní aplikace v Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Vytvořte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Přečtěte si appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Odstraní appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Vytvořte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Odstraňte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualizuje vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Vytvořte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Odstraní servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="application-developer-permissions"></a>Oprávnění pro vývojáře aplikace

Může vytvořit registrace aplikací nezávisle na nastavení uživatelé můžou registrovat aplikace.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Vytváření aplikací v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Vytvořte appRoleAssignments v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Vytvořte oAuth2PermissionGrants v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Vytvořte servicePrincipals v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |

### <a name="authentication-administrator-permissions"></a>Oprávnění správce ověřování

Povoleno zobrazení, nastavení a obnovení informací o metodě ověřování pro libovolného uživatele bez oprávnění správce.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aktualizujte vlastnosti silného ověřování jako informace o přihlašovacích údajích MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v organizaci Office 365. Další podrobnosti najdete v online dokumentaci. |

### <a name="azure-information-protection-administrator-permissions"></a>Azure Information Protection oprávnění správce

Může spravovat všechny aspekty služby Azure Information Protection.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu [Popis role](#) výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="b2c-user-flow-administrator-permissions"></a>Oprávnění správce toku B2C uživatele

Vytvářejte a spravujte všechny aspekty toků uživatelů.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Čtení a konfigurace toků uživatelů v Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Oprávnění správce toku B2C uživatele

Vytvořte a spravujte schéma atributů dostupné pro všechny toky uživatelů.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Čtení a konfigurace uživatelských atributů v Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF, oprávnění správce sady klíčů

Spravujte tajné klíče pro federaci a šifrování v architektuře prostředí identity.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. B2C/trustFramework/-Sets/allTasks | Přečte a nakonfiguruje sady klíčů v Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Oprávnění správce zásad IEF B2C

Vytváření a Správa zásad pro pravidla důvěryhodnosti v architektuře prostředí identity

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Čtení a konfigurace vlastních zásad v Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Oprávnění správce fakturace

Může provádět běžné úkoly související s fakturací, jako je aktualizace platebních údajů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Aktualizuje základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Commerce. fakturace/allEntities/allTasks | Spravujte všechny aspekty fakturace Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Oprávnění správce cloudové aplikace

Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací kromě proxy aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| microsoft.aad.directory/applications/create | Vytváření aplikací v Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Odstraní aplikace v Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Vytvořte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Odstraní appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Vytvořte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Odstraňte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualizuje vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Vytvořte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Odstraní servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Oprávnění správce cloudového zařízení

Úplný přístup ke správě zařízení ve službě Azure AD.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Odstraňte zařízení v Azure Active Directory. |
| Microsoft. aad. Directory/Devices/Disable | Zakáže zařízení v Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Povolte zařízení v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |

### <a name="company-administrator-permissions"></a>Oprávnění správce společnosti

Může spravovat všechny aspekty Azure AD a služeb Microsoftu, které používají identity Azure AD.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. aad. cloudAppSecurity. |
| Microsoft. aad. Directory/administrativeUnits/allProperties/allTasks | Vytvořit a odstranit administrativeUnits a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Vytvářejte a odstraňujte aplikace a čtěte a aktualizujte všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Vytvořit a odstranit appRoleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Vytváření a odstraňování kontaktů a čtení a aktualizace všech vlastností v Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Vytváření a odstraňování smluv a čtení a aktualizace všech vlastností v Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Vytváření a odstraňování zařízení a čtení a aktualizace všech vlastností v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Vytvořit a odstranit directoryRoles a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Vytvořit a odstranit directoryRoleTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Vytváření a odstraňování domén a čtení a aktualizace všech vlastností v Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Vytváření a odstraňování skupin a čtení a aktualizace všech vlastností v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Vytvořit a odstranit groupSettings a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Vytvořit a odstranit groupSettingTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Vytvořit a odstranit loginTenantBranding a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Vytvořit a odstranit oAuth2PermissionGrants a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Vytvářejte a odstraňujte organizaci a čtěte a aktualizujte všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Vytváření a odstraňování zásad a čtení a aktualizace všech vlastností v Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Vytvořit a odstranit roleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Vytvořit a odstranit roleDefinitions a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Vytvořit a odstranit scopedRoleMemberships a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Může provádět akci služby Activateservice v Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Může provádět akci služby Disabledirectoryfeature v Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Může provádět akci služby Enabledirectoryfeature v Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Může provádět akci služby Getavailableextentionproperties v Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Vytvořit a odstranit servicePrincipals a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Vytvořit a odstranit subscribedSkus a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Umožňuje vytvářet a odstraňovat uživatele a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Provede všechny akce v Azure AD Connect. |
| Microsoft. aad. identityProtection/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. aad. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Azure. advancedThreatProtection/allEntities/Read | Načte všechny prostředky v Microsoft. Azure. advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Commerce. fakturace/allEntities/allTasks | Spravujte všechny aspekty fakturace Office 365. |
| Microsoft. Intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Spravujte všechny aspekty Desktop Analytics. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Správa všech aspektů Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Přečtěte si securityMessages v Microsoft. Office 365. messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Spravujte všechny aspekty centra ochrany Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Čtení všech prostředků v Microsoft. Windows. defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Oprávnění správce dodržování předpisů

Může číst a spravovat konfiguraci a sestavy dodržování předpisů v Azure AD a Office 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="compliance-data-administrator-permissions"></a>Oprávnění správce dat dodržování předpisů

Vytvoří a spravuje obsah dodržování předpisů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Čtení a konfigurace Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. SharePoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="conditional-access-administrator-permissions"></a>Oprávnění správce podmíněného přístupu

Může spravovat funkce podmíněného přístupu.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Aktualizuje vlastnost policies. conditionalAccess v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Vytvořte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Odstraňte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Aktualizuje vlastnost policies. conditionalAccess v Azure Active Directory. |
| Microsoft. aad. Directory/policies/conditionalAccess/policiesAppliedTo/Read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Aktualizuje vlastnost policies. conditionalAccess v Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Oprávnění správce služby CRM

Může spravovat všechny aspekty produktu Dynamics 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Oprávnění schvalovatele pro přístup k bezpečnostnímu modulu zákazníka

Může schvalovat žádosti o podporu Microsoftu pro přístup k datům organizace zákazníka.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.lockbox/allEntities/allTasks | Správa všech aspektů Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator-permissions"></a>Oprávnění správce pro Desktop Analytics

Může spravovat služby zásad & Desktop Analytics a Office Customization. Pro Desktop Analytics to zahrnuje možnost Zobrazit inventář assetů, vytvářet plány nasazení, zobrazovat stav nasazení a stavu. V případě služby Office Customization & Policy tato role umožňuje uživatelům spravovat zásady Office.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Spravujte všechny aspekty Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="device-administrators-permissions"></a>Oprávnění pro správce zařízení

Uživatelé přiřazení k této roli budou přidáni do místní skupiny Administrators na zařízeních připojených k Azure AD.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Přečte základní vlastnosti groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Přečte základní vlastnosti groupSettingTemplates v Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Oprávnění čtenáře adresářů
Může číst základní informace o adresáři. Pro udělení přístupu k aplikacím, které nejsou určené pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. Directory/administrativeUnits/Basic/Read | Přečte základní vlastnosti administrativeUnits v Azure Active Directory. |
| Microsoft. aad. Directory/administrativeUnits/Members/Read | Načte vlastnost administrativeUnits. Members v Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Přečte základní vlastnosti aplikací v Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Číst vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. aad. Directory/Applications/policies/Read | Přečte vlastnost Applications. policies v Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Přečte základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. aad. Directory/Contacts/memberOf/Read | Číst vlastnost Contacts. memberOf v Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Přečte základní vlastnosti smluv v Azure Active Directory. |
| Microsoft. aad. Directory/Devices/Basic/Read | Přečte základní vlastnosti na zařízeních v Azure Active Directory. |
| Microsoft. aad. Directory/Devices/memberOf/Read | Číst vlastnost Devices. memberOf v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Číst vlastnost Devices. registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Číst vlastnost Devices. registeredUsers v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Přečte základní vlastnosti directoryRoles v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Číst vlastnost directoryRoles. eligibleMembers v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Načte vlastnost directoryRoles. Members v Azure Active Directory. |
| Microsoft. aad. Directory/doméns/Basic/Read | Přečte základní vlastnosti domén v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Načte vlastnost groups. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Přečte základní vlastnosti pro skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Načte vlastnost groups. memberOf v Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Přečte vlastnost groups. Members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Číst vlastnost groups. Owners v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Číst vlastnost groups. Settings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Přečte základní vlastnosti groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Přečte základní vlastnosti groupSettingTemplates v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Přečte základní vlastnosti oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. aad. Directory/Organization/Basic/Read | Přečte základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Číst vlastnost Organization. trustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Přečte základní vlastnosti roleAssignments v Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Přečte základní vlastnosti roleDefinitions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Číst vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Číst vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Přečte základní vlastnosti servicePrincipals v Azure Active Directory. |
| Microsoft. aad. Directory/servicePrincipals/memberOf/Read | Načte vlastnost servicePrincipals. memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Číst vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Číst vlastnost servicePrincipals. ownedObjects v Azure Active Directory. |
| Microsoft. aad. Directory/servicePrincipals/Owners/Read | Číst vlastnost servicePrincipals. Owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Číst vlastnost servicePrincipals. policies v Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Přečte základní vlastnosti subscribedSkus v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Číst vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Číst základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Číst vlastnost Users. directReports v Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Umožňuje číst vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. aad. Directory/Users/memberOf/Read | Číst vlastnost Users. memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Číst vlastnost Users. oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Číst vlastnost Users. ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Číst vlastnost Users. ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Číst vlastnost Users. registeredDevices v Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Oprávnění pro účty synchronizace adresářů

Používáno pouze službou Azure AD Connect.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Aktualizuje vlastnost Organization. dirSync v Azure Active Directory. |
| microsoft.aad.directory/policies/create | Vytvořte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Odstraňte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Přečte základní vlastnosti zásad v Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aktualizuje základní vlastnosti zásad v Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Číst vlastnost policies. Owners v Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | V Azure Active Directory vlastnost Update policies. Owners. |
| Microsoft. aad. Directory/policies/policiesAppliedTo/Read | Číst zásady. policiesAppliedTo vlastnosti v Azure Active Directory. |
| Microsoft. aad. Directory/policies/tenantDefault/Update | Aktualizuje vlastnost policies. tenantDefault v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Číst vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Číst vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aktualizuje vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Přečte základní vlastnosti servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Vytvořte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. aad. Directory/servicePrincipals/memberOf/Read | Načte vlastnost servicePrincipals. memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Číst vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. aad. Directory/servicePrincipals/Owners/Read | Číst vlastnost servicePrincipals. Owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Číst vlastnost servicePrincipals. ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Číst vlastnost servicePrincipals. policies v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Provede všechny akce v Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Oprávnění k zapisovači adresářů

Může číst & informace o adresáři Basic Write. Pro udělení přístupu k aplikacím, které nejsou určené pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/create | Vytvořte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Aktualizuje základní vlastnosti v groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Vytvořte groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Odstraní groupSettings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Oprávnění správce služby Exchange

Může spravovat všechny aspekty produktu Exchange.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualizuje vlastnost groups. Unified v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aktualizuje základní vlastnosti skupin Office 365. |
| microsoft.aad.directory/groups/unified/create | Vytvořte skupiny Office 365. |
| microsoft.aad.directory/groups/unified/delete | Odstraňte skupiny Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aktualizuje členství skupin Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aktualizujte vlastnictví skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="external-identity-provider-administrator-permissions"></a>Oprávnění správce externího zprostředkovatele identity

Nakonfigurujte zprostředkovatele identity pro použití v přímé federaci.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Číst a konfigurovat zprostředkovatele identity v Azure Active Directory B2C. |

### <a name="guest-inviter-permissions"></a>Oprávnění pozvat hosta
Může pozvat uživatele typu Host bez ohledu na nastavení členové můžou pozvat hosty.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Číst vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Číst základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Číst vlastnost Users. directReports v Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Pozvání uživatelů typu Host v Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Umožňuje číst vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. aad. Directory/Users/memberOf/Read | Číst vlastnost Users. memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Číst vlastnost Users. oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Číst vlastnost Users. ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Číst vlastnost Users. ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Číst vlastnost Users. registeredDevices v Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Oprávnění správce helpdesku

Může resetovat hesla pro správce bez oprávnění správce a helpdesku.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="intune-service-administrator-permissions"></a>Oprávnění správce služby Intune

Může spravovat všechny aspekty produktu Intune.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Vytvoří kontakty v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Odstraní kontakty v Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Aktualizuje základní vlastnosti na zařízeních v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/devices/create | Vytvořte zařízení v Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Odstraňte zařízení v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | V Azure Active Directory aktualizovat vlastnost Devices. registeredOwners. |
| microsoft.aad.directory/devices/registeredUsers/update | V Azure Active Directory aktualizovat vlastnost Devices. registeredUsers. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Vytvořte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/groups/delete | Odstraní skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Obnovte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="kaizala-administrator-permissions"></a>Oprávnění správce Kaizala

Může spravovat nastavení pro Microsoft Kaizala.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečtěte si centrum pro správu Office 365. |

### <a name="license-administrator-permissions"></a>Oprávnění správce licence

Může spravovat licence na produkty pro uživatele a skupiny.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Umožňuje aktualizovat vlastnost Users. usageLocation v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |

### <a name="lync-service-administrator-permissions"></a>Oprávnění správce služby Lync

Může spravovat všechny aspekty produktu Skype pro firmy.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="message-center-privacy-reader-permissions"></a>Oprávnění čtenářů ochrany osobních údajů centra zpráv

Může číst příspěvky centra zpráv, zprávy o ochraně dat, skupiny, domény a odběry.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.messageCenter/messages/read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Přečtěte si securityMessages v Microsoft. Office 365. messageCenter. |

### <a name="message-center-reader-permissions"></a>Oprávnění čtenáře centra zpráv
Může číst zprávy a aktualizace pro svou organizaci jenom v centru zpráv Office 365. 

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.messageCenter/messages/read | Přečte zprávy v Microsoft. Office 365. messageCenter. |

### <a name="partner-tier1-support-permissions"></a>Oprávnění podpory partnerského Tier1u

Nepoužívejte – Neurčeno pro obecné použití.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Vytvoří kontakty v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Odstraní kontakty v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Vytvořte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/groups/members/update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/delete | Umožňuje odstranit uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| microsoft.aad.directory/users/restore | Obnovení odstraněných uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="partner-tier2-support-permissions"></a>Oprávnění podpory partnerského 2U

Nepoužívejte – Neurčeno pro obecné použití.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Vytvoří kontakty v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Odstraní kontakty v Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Vytváření a odstraňování domén a čtení a aktualizace standardních vlastností v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Vytvořte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Odstraní skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Obnovte skupiny v Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Aktualizuje základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/delete | Umožňuje odstranit uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| microsoft.aad.directory/users/restore | Obnovení odstraněných uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="password-administrator-permissions"></a>Oprávnění správce hesel

Může resetovat hesla správců, kteří nejsou správci a hesla.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI oprávnění správce služby

Může spravovat všechny aspekty Power BI produktu.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="privileged-authentication-administrator-permissions"></a>Oprávnění správce privilegovaného ověřování

Povoleno zobrazení, nastavení a resetování informací o metodě ověřování pro libovolného uživatele (správce nebo jiný správce).

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aktualizujte vlastnosti silného ověřování jako informace o přihlašovacích údajích MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v organizaci Office 365. Další podrobnosti najdete v online dokumentaci. |

### <a name="privileged-role-administrator-permissions"></a>Oprávnění správce privilegovaných rolí

Může spravovat přiřazení rolí v Azure AD a všechny aspekty Privileged Identity Management.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. aad. privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Přečte a nakonfiguruje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Přečte a nakonfiguruje vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. aad. Directory/administrativeUnits/allProperties/allTasks | Vytváření a Správa jednotek pro správu (včetně členů) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Vytváření a správa přiřazení rolí. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Vytvářejte a spravujte definice rolí. |

### <a name="reports-reader-permissions"></a>Oprávnění čtenáře sestav

Může číst sestavy pro přihlášení a audit.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |

### <a name="search-administrator-permissions"></a>Hledat oprávnění správce

Může vytvářet a spravovat všechny aspekty nastavení služby Microsoft Search.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat všechny vlastnosti v Microsoft. Office 365. Search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="search-editor-permissions"></a>Oprávnění editoru hledání

Může vytvářet a spravovat redakční obsah, jako jsou záložky, Q a as, Locations, FloorPlan.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Vytváření a odstraňování obsahu a čtení a aktualizace všech vlastností v Microsoft. Office 365. Search. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |

### <a name="security-administrator-permissions"></a>Oprávnění správce zabezpečení

Může číst informace o zabezpečení a sestavy a spravovat konfiguraci v Azure AD a Office 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aktualizuje základní vlastnosti zásad v Azure Active Directory. |
| microsoft.aad.directory/policies/create | Vytvořte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Odstraňte zásady v Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | V Azure Active Directory vlastnost Update policies. Owners. |
| Microsoft. aad. Directory/policies/tenantDefault/Update | Aktualizuje vlastnost policies. tenantDefault v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. aad. identityProtection/allEntities/Read | Načte všechny prostředky v Microsoft. aad. identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Aktualizuje všechny prostředky v Microsoft. aad. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.protectionCenter/allEntities/read | Přečtěte si všechny aspekty centra ochrany Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Aktualizuje všechny prostředky v Microsoft. Office 365. protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |

### <a name="security-operator-permissions"></a>Oprávnění operátora zabezpečení

Vytvoří a spravuje události zabezpečení.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Čtení a konfigurace Microsoft Cloud App Security. |
| Microsoft. aad. identityProtection/allEntities/Read | Načte všechny prostředky v Microsoft. aad. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Azure. advancedThreatProtection/allEntities/Read | Čtení a konfigurace rozšířené ochrany před internetovými útoky Azure AD |
| Microsoft. Intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Čtení a konfigurace Centrum zabezpečení a dodržování předpisů. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Čtení a konfigurace rozšířené ochrany před internetovými útoky v programu Windows Defender |

### <a name="security-reader-permissions"></a>Oprávnění čtenáře zabezpečení

Může číst informace o zabezpečení a sestavy v Azure AD a Office 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. aad. identityProtection/allEntities/Read | Načte všechny prostředky v Microsoft. aad. identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.protectionCenter/allEntities/read | Přečtěte si všechny aspekty centra ochrany Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |

### <a name="service-support-administrator-permissions"></a>Služba Service support – oprávnění správce

Může číst informace o stavu služby a spravovat lístky podpory.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Oprávnění správce služby SharePoint

Může spravovat všechny aspekty služby SharePoint.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualizuje vlastnost groups. Unified v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aktualizuje základní vlastnosti skupin Office 365. |
| microsoft.aad.directory/groups/unified/create | Vytvořte skupiny Office 365. |
| microsoft.aad.directory/groups/unified/delete | Odstraňte skupiny Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aktualizuje členství skupin Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aktualizujte vlastnictví skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. SharePoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="teams-communications-administrator-permissions"></a>Teams Communications – oprávnění správce

Může spravovat funkce volání a schůzek ve službě Microsoft Teams.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Komunikace týmů podporuje oprávnění inženýra

Může řešit problémy s komunikací v rámci týmů pomocí pokročilých nástrojů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |

### <a name="teams-communications-support-specialist-permissions"></a>Komunikace týmů podpora Specialistových oprávnění

Může řešit problémy s komunikací v rámci týmů pomocí základních nástrojů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |

### <a name="teams-service-administrator-permissions"></a>Oprávnění správce služby Teams

Může spravovat službu Microsoft Teams.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aktualizuje vlastnost groups. Unified v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aktualizuje základní vlastnosti skupin Office 365. |
| microsoft.aad.directory/groups/unified/create | Vytvořte skupiny Office 365. |
| microsoft.aad.directory/groups/unified/delete | Odstraňte skupiny Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aktualizuje členství skupin Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aktualizujte vlastnictví skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy o využití Office 365. |

### <a name="user-administrator-permissions"></a>Oprávnění správce uživatele
Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Vytvořte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Odstraní appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Vytvoří kontakty v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Odstraní kontakty v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Vytvořte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| microsoft.aad.directory/groups/delete | Odstraní skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Obnovte skupiny v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/create | Umožňuje vytvořit uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/delete | Umožňuje odstranit uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| microsoft.aad.directory/users/restore | Obnovení odstraněných uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

## <a name="role-template-ids"></a>ID šablon rolí

ID šablon rolí se používají hlavně pomocí Graph API nebo uživatelů PowerShellu.

Zobrazovaný název grafu | Zobrazované jméno Azure Portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Správce aplikace | Správce aplikace | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Vývojář aplikací | Vývojář aplikace | CF1C38E5-3621-4004-A7CB-879624DCED7C
Správce ověřování | Správce ověřování | c4e39bd9-1100-46d3-8c65-fb160da0071f
Správce Azure Information Protection | Správce Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Správce toku B2C uživatele | Správce toku B2C uživatele | 6e591065-9bad-43ed-90f3-e9424366d2f0
Správce atributů uživatelských toků B2C | Správce atributů uživatelských toků B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Správce sady klíčů IEF B2C | Správce sady klíčů IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
Správce zásad IEF B2C | Správce zásad IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Správce fakturace | Správce fakturace | b0f54661-2d74-4c50-afa3-1ec803f12efe
Správce cloudových aplikací | Správce cloudové aplikace | 158c047a-c907-4556-b7ef-446551a6b5f7
Správce cloudových zařízení | Správce cloudových zařízení | 7698a772-787b-4ac8-901f-60d6b08affd2
Správce společnosti | Globální správce | 62e90394-69f5-4237-9190-012177145e10
Správce dodržování předpisů | Správce dodržování předpisů | 17315797-102d-40b4-93e0-432062caca18
Správce dat dodržování předpisů | Správce dat dodržování předpisů | e6d1a23a-da11-4be4-9570-befc86d067a7
Správce podmíněného přístupu | Správce podmíněného přístupu | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Správce služeb CRM | Správce Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Schvalovatel přístupu ke Customer LockBoxu | Schvalovatel přístupu Customer Lockbox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Správce Desktop Analytics | Správce Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Správci zařízení | Správci zařízení | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Připojení zařízení | Připojení zařízení | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Správci zařízení | Správci zařízení | 2b499bcd-da44-4968-8aec-78e1674fa64d
Uživatelé zařízení | Uživatelé zařízení | d405c6df-0af8-4e3b-95e4-4d06e542189e
Uživatelé s oprávněním ke čtení adresářů | Uživatelé s oprávněním ke čtení adresářů | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Účty synchronizace adresářů | Účty pro synchronizaci adresářů | d29b2b05-8046-44ba-8758-1e26182fcf32
Zapisovače do adresáře | Uživatelé s oprávněním k zápisu do adresářů | 9360feb5-f418-4baa-8175-e2a00bac4301
Správce služby Exchange | Správce Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Správce externích zprostředkovatelů identity | Správce externích zprostředkovatelů identity | be2f45a1-457d-42af-a067-6ec1fa63bc45
Odesílatel pozvánek hostů | Odesílatel pozvánky hostů | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Správce technické podpory | Správce hesel | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Správce služby Intune | Správce Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Správce Kaizala | Správce Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Správce licencí | Správce licencí | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Správce služeb Lyncu | Správce Skypu pro firmy | 75941009-915a-4869-abe7-691bff18279e
Čtenář ochrany osobních údajů centra zpráv | Čtenář ochrany osobních údajů centra zpráv | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Čtenář Centra zpráv | Čtenář Centra zpráv | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Podpora partnerů úrovně 1 | Partnerská podpora úrovně 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Podpora partnerů úrovně 2 | Partnerská podpora úrovně 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Správce hesel | Správce hesel | 966707d0-3269-4727-9be2-8c3a10f19b9d
Správce služeb Power BI | Správce Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Privilegovaný správce ověřování | Privilegovaný správce ověřování | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Správce privilegované role | Správce privilegovaných rolí | e8611ab8-c189-46e8-94e1-60213ab1f814
Čtenář sestav | Čtečka sestav | 4a5d8f65-41da-4de4-8968-e035b65339cf
Správce hledání | Správce hledání | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor hledání | Editor hledání | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Správce zabezpečení | Správce zabezpečení | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operátor zabezpečení | Operátor zabezpečení | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Čtenář zabezpečení | Čtenář zabezpečení | 5d6b6bb7-de71-4623-b4af-96380a352509
Správce podpory služeb | Správce služeb | f023fd81-a637-4b56-95fd-791ac0226033
Správce služeb SharePointu | Správce SharePointu | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Správce komunikace ve službě Teams | Správce komunikace ve službě Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Odborný technik podpory komunikace ve službě Teams | Odborný technik podpory komunikace ve službě Teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialista technické podpory komunikace ve službě Teams | Specialista technické podpory komunikace ve službě Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Správce služby Teams | Správce služby Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Uživatel | Uživatel | a0b1b346-4d3e-4e8b-98f8-753987be4970
Správce uživatelských účtů | Správce uživatelů | fe930be7-5e62-47db-91af-98c3a49a38b1
Připojení pracovních zařízení | Připojení zařízení na pracovišti | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Zastaralé role

Následující role by se neměly používat. Jsou zastaralé a v budoucnu se odeberou z Azure AD.

* Správce ad hoc licencí
* Připojení zařízení
* Správci zařízení
* Uživatelé zařízení
* Tvůrce uživatele ověřený e-mailem
* Správce poštovní schránky
* Připojení pracovních zařízení

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak přiřadit uživatele jako správce předplatného Azure, najdete v tématu [Správa přístupu pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
