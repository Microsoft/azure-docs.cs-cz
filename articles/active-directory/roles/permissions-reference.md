---
title: Popis a oprávnění role Azure AD – Azure Active Directory | Microsoft Docs
description: Role správce může přidávat uživatele, přiřazovat administrativní role, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40269f6b1553d73af43b4cf86127eaa97524dd57
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740903"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Oprávnění role správce v Azure Active Directory

Pomocí Azure Active Directory (Azure AD) můžete určit omezené správce ke správě úloh identity v méně privilegovaných rolích. Správcům je možné přiřadit tyto účely, jako je přidání nebo změna uživatelů, přiřazování rolí pro správu, Resetování uživatelských hesel, Správa uživatelských licencí a správa názvů domén. [Výchozí uživatelská oprávnění](../fundamentals/users-default-permissions.md) se dají změnit jenom v nastavení uživatele v Azure AD.

## <a name="limit-use-of-global-administrator"></a>Omezení použití globálního správce

Uživatelé, kteří mají přiřazenou roli globálního správce, můžou číst a upravovat všechna nastavení správy ve vaší organizaci Azure AD. Když se uživatel přihlásí ke cloudové službě Microsoftu, vytvoří se ve výchozím nastavení tenant Azure AD a uživatel se stane členem role globální správci. Když přidáte předplatné do existujícího tenanta, nebudete přiřazeni k roli globálního správce. Role správce můžou delegovat jenom globální správci a správci privilegovaných rolí. Pokud chcete snížit riziko pro vaši firmu, doporučujeme přiřadit tuto roli co nejmenším možným lidem ve vaší organizaci.

Jako osvědčený postup doporučujeme přiřadit tuto roli méně než pěti lidem ve vaší organizaci. Pokud máte ve vaší organizaci více než pět správců, kteří mají přiřazenou roli globálního správce, je zde několik způsobů, jak omezit jejich používání.

### <a name="find-the-role-you-need"></a>Najděte roli, kterou potřebujete.

Pokud se frustrující, jak najít roli, kterou potřebujete pro seznam mnoha rolí, může Azure AD zobrazit podmnožiny rolí na základě kategorií rolí. Podívejte se na náš nový filtr **typu** pro [role a správce Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) , abyste viděli jenom role ve vybraném typu.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Role existuje nyní, která neexistuje, když jste přiřadili roli globálního správce.

Je možné, že se do služby Azure AD přidala role nebo role, které poskytují přesnější oprávnění, která se nedají použít při zvýšení úrovně uživatelů na globálního správce. V průběhu času zavádíme další role, které provádějí úlohy, které by mohly provádět jenom role globálního správce. Uvidíte ty, které se projeví v následujících [dostupných rolích](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Přiřazení nebo odebrání rolí správce

Další informace o přiřazování rolí pro správu uživateli v Azure Active Directory najdete v tématu [zobrazení a přiřazení rolí správce v Azure Active Directory](manage-roles-portal.md).

> [!Note]
> Pokud máte licenci Azure AD Premium P2 a už jste uživatel Privileged Identity Management (PIM), provedou se všechny úlohy správy rolí v rámci správy identit oprávnění, nikoli v Azure AD.
>
> ![Role Azure AD spravované v PIM pro uživatele, kteří už používají PIM a mají licenci Premium P2](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>Dostupné role

K dispozici jsou následující role správce:

### <a name="application-administrator"></a>[Správce aplikace](#application-administrator-permissions)

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy aplikací. Všimněte si, že uživatelé přiřazení k této roli se při vytváření nových registrací aplikací nebo podnikových aplikací nepřidali jako vlastníci.

Tato role také uděluje možnost _vyjádřit souhlas_ s delegovanými oprávněními a oprávněními aplikace s výjimkou oprávnění aplikace v rozhraní Microsoft Graph API.

> [!IMPORTANT]
> Tato výjimka znamená, že stále můžete vyjádřit souhlas s oprávněními pro _jiné_ aplikace (například aplikace od jiných společností než Microsoft nebo aplikace, které jste zaregistrovali), ale ne oprávnění k samotné službě Azure AD. Tato oprávnění si můžete i přesto _vyžádat_ jako součást registrace aplikace, ale _udělení_ (tj. souhlasu s) Tato oprávnění vyžaduje správce Azure AD. To znamená, že uživatel se zlými úmysly nemůže snadno zvýšit oprávnění, například vytvořením a souhlasem s aplikací, která může zapisovat do celého adresáře a prostřednictvím oprávnění této aplikace, aby se stala globálním správcem.
>
>Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé přiřazení k této roli můžou do aplikace přidat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Pokud má identita aplikace udělen přístup k prostředku, jako je třeba možnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, může uživatel přiřazený k této roli provádět tyto akce při zosobnění aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšením oprávnění, přes co může uživatel dělat prostřednictvím přiřazení rolí. Je důležité pochopit, že přiřazení uživatele k roli správce aplikace jim dává možnost zosobnit identitu aplikace.

### <a name="application-developer"></a>[Vývojář aplikace](#application-developer-permissions)

Uživatelé v této roli můžou vytvářet registrace aplikací, když je nastavení "uživatelé můžou registrovat aplikace" nastavené na ne. Tato role také uděluje oprávnění k souhlasu jednoho vlastního uživatele, pokud "uživatelé můžou udělit souhlas s aplikacemi, které přistupují k firemním datům" jejich jménem "nastavení je nastaveno na ne. Uživatelé přiřazení k této roli jsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

### <a name="authentication-administrator"></a>[Správce ověřování](#authentication-administrator-permissions)

Uživatelé s touto rolí můžou pro některé uživatele nastavit nebo resetovat přihlašovací údaje neheslem a můžou aktualizovat hesla pro všechny uživatele. Správci ověřování můžou vyžadovat, aby uživatelé, kteří jsou bez oprávnění správce nebo se k některým rolím přiřadili, zaregistrovali v rámci stávajících přihlašovacích údajů, které nepoužívají heslo (například MFA nebo FIDO), a taky můžou **zapomenout** MFA na zařízení, která při příštím přihlášení VYZVE k MFA. Tyto akce platí pouze pro uživatele, kteří nejsou správci, nebo kteří mají přiřazenu jednu nebo více následujících rolí:

* Správce ověřování
* Čtečky adresářů
* Pozvánka hosta
* Čtenář centra zpráv
* Čtečka sestav

Role [správce privilegovaného ověřování](#privileged-authentication-administrator) má oprávnění k vynucení opakované registrace a ověřování Multi-Factor Authentication pro všechny uživatele.

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit přihlašovací údaje pro uživatele, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna přihlašovacích údajů uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>* Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům ověřování. Prostřednictvím této cesty může správce ověřování předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>* Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>* Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>* Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>* Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

### <a name="attack-payload-author"></a>[Autor datové části útoku](#attack-payload-author-permissions)

Uživatelé v této roli můžou vytvářet datovou část útoku, ale nemůžou je ve skutečnosti spouštět nebo naplánovat. Datová část útoku je pak k dispozici všem správcům v tenantovi, kteří je můžou použít k vytvoření simulace.

### <a name="attack-simulation-administrator"></a>[Správce simulace útoků](#attack-simulation-administrator-permissions)

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty vytvoření simulace útoku, spustit nebo naplánovat simulaci a zkontrolovat výsledky simulace. Členové této role mají tento přístup pro všechny simulace v tenantovi.

### <a name="azure-devops-administrator"></a>[Správce Azure DevOps](#azure-devops-administrator-permissions)

Uživatelé s touto rolí můžou spravovat zásady Azure DevOps a omezit tak nové vytváření organizací Azure DevOps na sadu konfigurovatelných uživatelů nebo skupin. Uživatelé v této roli mohou spravovat tuto zásadu prostřednictvím jakékoli organizace Azure DevOps, která je zajištěna organizací Azure AD společnosti. Tato role neuděluje žádná další oprávnění specifická pro Azure DevOps (například správce kolekce projektů) ve všech organizacích Azure DevOps zajištěných organizací Azure AD společnosti.

Všechny zásady Enterprise Azure DevOps můžou spravovat uživatelé v této roli.

### <a name="azure-information-protection-administrator"></a>[Správce Azure Information Protection](#azure-information-protection-administrator-permissions)

Uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfigurovat popisky pro zásady Azure Information Protection, spravovat šablony ochrany a aktivovat ochranu. Tato role neuděluje žádná oprávnění v centru služby Identity Protection Center, Privileged Identity Management, monitor Microsoft 365 Service Health nebo v centru pro dodržování předpisů Office 365 Security &.

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF, správce sady klíčů](#b2c-ief-keyset-administrator-permissions)

Uživatel může vytvářet a spravovat klíče a tajné kódy zásad pro šifrování tokenů, signatury tokenů a šifrování a dešifrování deklarací identity. Přidáním nových klíčů do existujících kontejnerů klíčů může tento omezený správce podle potřeby přeměnit tajné klíče, aniž by to mělo vliv na stávající aplikace. Tento uživatel může zobrazit úplný obsah těchto tajných kódů a jejich datum vypršení platnosti i po jejich vytvoření.

> [!IMPORTANT]
> Tato role je citlivá. Role správce sady klíčů by měla být pečlivě auditována a přiřazena se opatrně během předprodukčních a produkčních verzí.

### <a name="b2c-ief-policy-administrator"></a>[Správce zásad IEF B2C](#b2c-ief-policy-administrator-permissions)

Uživatelé v této roli mají možnost vytvářet, číst, aktualizovat a odstraňovat všechny vlastní zásady v Azure AD B2C a mají proto úplnou kontrolu nad architekturou prostředí identity v příslušné Azure AD B2C organizaci. Tím, že uživatel upraví zásady, může vytvořit přímou federaci s externími zprostředkovateli identity, měnit schéma adresáře, měnit obsah směřující do uživatele (HTML, CSS, JavaScript), měnit požadavky na dokončení ověřování, vytvářet nové uživatele, odesílat uživatelská data do externích systémů včetně úplných migrací a upravovat všechny informace o uživateli, včetně citlivých polí, jako jsou hesla a telefonní čísla. Naopak tato role nemůže měnit šifrovací klíče ani upravovat tajné klíče používané pro federaci v organizaci.

> [!IMPORTANT]
> Správce zásad IEF B2 je vysoce citlivá role, kterou je třeba pro organizace v produkčním prostředí přiřadit velmi omezenému základu. Aktivity těchto uživatelů by měly být pečlivě auditovány, zejména pro organizace v produkčním prostředí.

### <a name="billing-administrator"></a>[Správce fakturace](#billing-administrator-permissions)

Může dělat nákupy, spravovat předplatná, spravovat lístky žádostí o podporu a sledovat stav služeb.

### <a name="cloud-application-administrator"></a>[Správce cloudové aplikace](#cloud-application-administrator-permissions)

Uživatelé v této roli mají stejná oprávnění jako role správce aplikace, kromě možnosti spravovat proxy aplikace. Tato role uděluje možnost vytvářet a spravovat všechny aspekty podnikových aplikací a registrací aplikací. Tato role také uděluje možnost vyjádřit souhlas s delegovanými oprávněními a oprávnění aplikací, kromě Microsoft Graph a Azure AD Graph. Uživatelé přiřazení k této roli nebudou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé přiřazení k této roli můžou do aplikace přidat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Pokud má identita aplikace udělen přístup k prostředku, jako je třeba možnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, může uživatel přiřazený k této roli provádět tyto akce při zosobnění aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšením oprávnění, přes co může uživatel dělat prostřednictvím přiřazení rolí. Je důležité pochopit, že přiřazení uživatele k roli správce cloudové aplikace jim dává možnost zosobnit identitu aplikace.


### <a name="cloud-device-administrator"></a>[Správce cloudového zařízení](#cloud-device-administrator-permissions)

Uživatelé v této roli můžou povolit, zakázat a odstranit zařízení ve službě Azure AD a číst klíče Windows 10 BitLocker (pokud jsou k dispozici) v Azure Portal. Role neuděluje oprávnění ke správě dalších vlastností v zařízení.

### <a name="compliance-administrator"></a>[Správce dodržování předpisů](#compliance-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících s dodržováním předpisů v centru pro Microsoft 365 dodržování předpisů, Microsoft 365 centrum pro správu, Azure a zabezpečení Office 365 v centru dodržování předpisů &. Assignees může také spravovat všechny funkce v centru pro správu Exchange a týmy & centra pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365. Další informace najdete v informacích [o Microsoft 365 rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

V | Může
----- | ----------
[Centrum kompatibility Microsoft 365](https://protection.office.com) | Chraňte a spravujte data vaší organizace napříč Microsoft 365 službami<br>Spravovat výstrahy dodržování předpisů
[Správce dodržování předpisů](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledovat, přiřazovat a ověřovat aktivity dodržování předpisů právními předpisy vaší organizace
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Provádět právní a vyšetřovací data<br>Správa požadavku subjektu dat<br><br>Tato role má stejná oprávnění jako [role správce dodržování předpisů](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) v sadě Office 365 Security & řízení přístupu na základě role v centru kompatibility.
[Intune](/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Může vytvářet a upravovat zásady souborů a umožňovat akce zásad správného řízení souborů.<br>Může zobrazit všechny předdefinované sestavy v části Správa dat

### <a name="compliance-data-administrator"></a>[Správce dat dodržování předpisů](#compliance-data-administrator-permissions)

Uživatelé s touto rolí mají oprávnění sledovat data v Microsoft 365 centrum dodržování předpisů, Microsoft 365 centrum pro správu a Azure. Uživatelé také mohou sledovat data o dodržování předpisů v centru pro správu Exchange, správců dodržování předpisů a týmech & centru pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365. [Tato dokumentace](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) obsahuje podrobnosti o rozdílech mezi správcem dodržování předpisů a správcem dat dodržování předpisů.

V | Může
----- | ----------
[Centrum kompatibility Microsoft 365](https://protection.office.com) | Monitorování zásad souvisejících s dodržováním předpisů napříč Microsoft 365 službami<br>Spravovat výstrahy dodržování předpisů
[Správce dodržování předpisů](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledovat, přiřazovat a ověřovat aktivity dodržování předpisů právními předpisy vaší organizace
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Provádět právní a vyšetřovací data<br>Správa požadavku subjektu dat<br><br>Tato role má stejná oprávnění jako [role správce dat dodržování předpisů](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) v sadě Office 365 Security & řízení přístupu na základě role v centru dodržování předpisů.
[Intune](/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Může vytvářet a upravovat zásady souborů a umožňovat akce zásad správného řízení souborů.<br>Může zobrazit všechny předdefinované sestavy v části Správa dat

### <a name="conditional-access-administrator"></a>[Správce podmíněného přístupu](#conditional-access-administrator-permissions)

Uživatelé s touto rolí mají možnost spravovat Azure Active Directory nastavení podmíněného přístupu.

### <a name="customer-lockbox-access-approver"></a>[Schvalovatel přístupu Customer Lockbox](#customer-lockbox-access-approver-permissions)

Spravuje [žádosti Customer Lockbox](/office365/admin/manage/customer-lockbox-requests) ve vaší organizaci. Přijímají e-mailová oznámení týkající se Customer Lockbox požadavků a můžou schvalovat a zamítat žádosti z centra pro správu Microsoft 365. Také je možné zapnout nebo vypnout funkci Customer Lockbox. Pouze globální Správci mohou resetovat hesla uživatelů přiřazených k této roli.

### <a name="desktop-analytics-administrator"></a>[Správce Desktop Analytics](#desktop-analytics-administrator-permissions)


Uživatelé v této roli můžou spravovat služby zásad & pro stolní počítače a přizpůsobení Office. Pro Desktop Analytics to zahrnuje možnost Zobrazit inventář assetů, vytvářet plány nasazení, zobrazovat stav nasazení a stavu. V případě služby Office Customization & Policy tato role umožňuje uživatelům spravovat zásady Office.

### <a name="device-administrators"></a>[Správci služeb](#device-administrators-permissions)

Tato role je k dispozici pro přiřazení pouze jako další místní správce v [nastavení zařízení](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Uživatelé s touto rolí se stanou Správci místních počítačů na všech zařízeních s Windows 10, která jsou připojená k Azure Active Directory. Neposkytují možnost spravovat objekty zařízení v Azure Active Directory.

### <a name="directory-readers"></a>[Čtečky adresářů](#directory-readers-permissions)

Uživatelé v této roli mohou číst základní informace o adresáři. Tato role by se měla použít pro:

* Udělení přístupu ke čtení konkrétní sady uživatelů typu host místo udělení oprávnění všem uživatelům typu Host.
* Udělení konkrétní sady uživatelů bez oprávnění správce přístup k Azure Portal, když je možnost omezit přístup k portálu Azure AD jenom na správce, nastavená na Ano.
* Udělení instančních objektů přístup k adresáři, kde Directory. Read. All není možnost.

### <a name="directory-synchronization-accounts"></a>[Účty synchronizace adresářů](#directory-synchronization-accounts-permissions)

Nepoužívat. Tato role je automaticky přiřazena ke službě Azure AD Connect a není určena ani podporována pro jiné použití.

### <a name="directory-writers"></a>[Zapisovače adresářů](#directory-writers-permissions)
Uživatelé v této roli mohou číst a aktualizovat základní informace o uživatelích, skupinách a instančních objektech. Tuto roli přiřaďte pouze aplikacím, které nepodporují [rámec souhlasu](../develop/quickstart-register-app.md). Neměl by být přiřazený žádnému uživateli.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Správce Dynamics 365/správce CRM](#crm-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Dynamics 365 online, pokud je služba k dispozici, a také možnost spravovat lístky podpory a monitorovat stav služby. Další informace najdete v [části použití role správce služby ke správě vaší organizace Azure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako správce služeb Dynamics 365. Ve [Azure Portal](https://portal.azure.com)je to "Dynamics 365 Administrator".

### <a name="exchange-administrator"></a>[Správce Exchange](#exchange-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Exchange Online, pokud je tato služba k dispozici. Má také možnost vytvářet a spravovat všechny Microsoft 365 skupiny, spravovat lístky podpory a monitorovat stav služby. Další informace [o Microsoft 365 rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Exchange". Je to "správce Exchange" v [Azure Portal](https://portal.azure.com). Je to "Exchange Online Administrator" v [centru pro správu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).


### <a name="external-id-user-flow-administrator"></a>[Správce toku externího ID uživatele](#external-id-user-flow-administrator-permissions)

Uživatelé s touto rolí můžou vytvářet a spravovat toky uživatelů (označované taky jako předdefinované zásady) v Azure Portal. Tito uživatelé mohou přizpůsobit obsah HTML/CSS/JavaScript, měnit požadavky MFA, vybírat deklarace identity v tokenu, spravovat konektory rozhraní API a konfigurovat nastavení relace pro všechny toky uživatelů v organizaci Azure AD. Na druhé straně tato role nezahrnuje možnost kontrolovat uživatelská data ani provádět změny atributů, které jsou součástí schématu organizace. Změny zásad architektury identity Framework (označované také jako vlastní zásady) jsou také mimo rozsah této role.

### <a name="external-id-user-flow-attribute-administrator"></a>[Správce atributů toku uživatele externího ID](#external-id-user-flow-attribute-administrator-permissions)

Uživatelé s touto rolí můžou přidávat nebo odstraňovat vlastní atributy dostupné všem uživatelským tokům v organizaci Azure AD. Uživatelé s touto rolí mohou například měnit nebo přidávat nové prvky do schématu koncového uživatele a ovlivnit chování všech toků uživatelů a nepřímo způsobit změny v tom, jaká data mohou být požádána o koncové uživatele a která jsou nakonec odeslána jako deklarace do aplikací. Tato role nemůže upravovat toky uživatelů.

### <a name="external-identity-provider-administrator"></a>[Správce externích zprostředkovatelů IDentity](#external-identity-provider-administrator-permissions)

Tento správce spravuje federace mezi organizacemi Azure AD a externími zprostředkovateli identity. S touto rolí můžou uživatelé přidávat nové zprostředkovatele identity a konfigurovat všechna dostupná nastavení (například cestu pro ověřování, ID služby, přiřazené kontejnery klíčů). Tento uživatel může organizaci Azure AD povolit, aby důvěřoval ověřování od externích zprostředkovatelů identity. Výsledný dopad na činnost koncového uživatele závisí na typu organizace:

* Organizace Azure AD pro zaměstnance a partnery: Přidání federace (např. Gmail) okamžitě ovlivní všechny pozvánky hostů, které ještě nebyly uplatněny. Viz téma [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](../external-identities/google-federation.md).
* Azure Active Directory B2C organizace: Přidání federace (například s Facebookem nebo s jinou organizací Azure AD) okamžitě nemá vliv na toky koncového uživatele, dokud se poskytovatel identity nepřidá jako možnost v toku uživatele (označované také jako předdefinované zásady). Příklad najdete v tématu [konfigurace účet Microsoft jako zprostředkovatele identity](../../active-directory-b2c/identity-provider-microsoft-account.md) . Pro změnu toků uživatelů se vyžaduje omezená role "správce toku uživatelů B2C".

### <a name="global-administrator--company-administrator"></a>[Globální správce/správce společnosti](#company-administrator-permissions)

Uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory a také službám, které používají Azure Active Directory identity, jako je Microsoft 365 Security Center, Microsoft 365 Center pro dodržování předpisů, Exchange Online, SharePoint Online a Online Skype pro firmy. Kromě toho můžou globální správci [zvýšit úroveň přístupu](../../role-based-access-control/elevate-access-global-admin.md) ke správě všech předplatných Azure a skupin pro správu. Díky tomu můžou globální správci získat úplný přístup ke všem prostředkům Azure pomocí příslušného tenanta Azure AD. Osoba, která se zaregistruje do organizace Azure AD, se stal globálním správcem. Ve vaší společnosti může být víc než jeden globální správce. Globální správci můžou resetovat heslo kteréhokoliv uživatele a všech ostatních správců.

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako správce společnosti. Jedná se o globální správce v [Azure Portal](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Globální čtenář](#global-reader-permissions)

Uživatelé v této roli můžou číst nastavení a informace pro správu napříč Microsoft 365 službami, ale nemůžou provádět akce správy. Globální čtenář je protistranou, která je jen pro čtení, globální správce. Přiřaďte globální čtenáře místo globálního správce pro plánování, audity nebo vyšetřování. Pomocí globálního čtecího zařízení v kombinaci s jinými omezenými rolemi správců, jako je třeba správce Exchange, můžete usnadnit práci bez přiřazení role globálního správce. Globální čtečka spolupracuje s centrem pro správu Microsoft 365, centrem pro správu serveru Exchange, centrem pro správu SharePointu, centrem pro správu týmů, centrem zabezpečení, centrem dodržování předpisů, centrem pro správu Azure AD a centrem pro správu správy zařízení.

> [!NOTE]
> Role globálního čtecího modulu teď má v současné době několik omezení –
>
>- [Centrum pro správu OneDrivu](https://admin.onedrive.com/) – centrum pro správu OneDrive nepodporuje roli globálního čtecího modulu.
>- [Centrum pro správu M365](https://admin.microsoft.com/Adminportal/Home#/homepage) – globální Čtenář nemůže přečíst žádosti o bezpečnostní modul zákazníka. V levém podokně centra pro správu M365 nenajdete kartu **žádosti o bezpečnostní modul pro zákazníky** v části **Podpora** .
>- [Office Security & – centrum dodržování předpisů](https://sip.protection.office.com/homepage) – globální Čtenář nemůže číst protokoly auditu SCC, provádět hledání obsahu nebo se podívat na zabezpečené skóre.
>- [Centrum pro správu týmů](https://admin.teams.microsoft.com) – globální Čtenář nemůže přečíst **životní cyklus týmů**, **analýzu & sestav**, **správu IP adres zařízení** a **Katalog aplikací**.
>- [Privileged Access Management (pam)](/office365/securitycompliance/privileged-access-management-overview) nepodporuje roli globálního čtecího modulu.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) – globální čtečka se podporuje jenom [pro centrální vytváření sestav](/azure/information-protection/reports-aip) a když vaše organizace Azure AD není na [jednotné platformě pro označování](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Tyto funkce jsou momentálně ve vývoji.
>

### <a name="groups-administrator"></a>[Správce skupin](#groups-administrator-permissions)

Uživatelé v této roli můžou vytvářet a spravovat skupiny a její nastavení, jako jsou zásady pro pojmenování a vypršení platnosti. Je důležité pochopit, že přiřazení uživatele k této roli dává možnost spravovat všechny skupiny v organizaci napříč různými úlohami, jako jsou týmy, SharePoint, Yammer kromě Outlooku. Uživatel bude také moci spravovat různá nastavení skupin na různých portálech pro správu, jako je například centrum pro správu Microsoft, Azure Portal a také konkrétní úlohy, jako jsou týmy a centra pro správu služby SharePoint.

### <a name="guest-inviter"></a>[Pozvánka hosta](#guest-inviter-permissions)

Uživatelé v této roli můžou spravovat Azure Active Directory pozvání uživatele hosta B2B, když **můžou členové pozvat** uživatele na hodnotu ne. Další informace o spolupráci B2B v rámci spolupráce B2B v [Azure AD](../external-identities/what-is-b2b.md). Nezahrnuje žádná další oprávnění.

### <a name="helpdesk-administrator"></a>[Správce helpdesku](#helpdesk-administrator-permissions)

Uživatelé s touto rolí můžou měnit hesla, zrušit platnost tokenů aktualizace, spravovat žádosti o služby a monitorovat stav služby. Zrušení platnosti aktualizačního tokenu vynutí, aby se uživatel znovu přihlásil. Správci helpdesku můžou resetovat hesla a zrušit platnost tokenů aktualizace jiných uživatelů, kteří nejsou správci, nebo jim přiřadit jenom následující role:

* Čtečky adresářů
* Pozvánka hosta
* Správce helpdesku
* Čtenář centra zpráv
* Správce hesel
* Čtečka sestav

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit hesla pro lidi, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna hesla uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>- Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům helpdesku. Prostřednictvím této cesty může správce helpdesku předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>- Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>- Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

Delegování oprávnění pro správu pro podmnožiny uživatelů a používání zásad u podmnožiny uživatelů je možné s [jednotkami pro správu (nyní ve verzi Public Preview)](administrative-units.md).

Tato role se dřív nazývala "správce hesel" ve [Azure Portal](https://portal.azure.com/). Název "správce helpdesku" ve službě Azure AD se teď shoduje s názvem v Azure AD PowerShellu a rozhraním Microsoft Graph API.

### <a name="hybrid-identity-administrator"></a>[Správce hybridní identity](#hybrid-identity-administrator-permissions)

Uživatelé v této roli můžou vytvářet, spravovat a nasazovat nastavení konfigurace zřizování z AD do Azure AD pomocí zřizování cloudu a také spravovat nastavení federace. Uživatelé mohou také řešit a monitorovat protokoly pomocí této role.  

### <a name="insights-administrator"></a>[Správce Insights](#insights-administrator-permissions)
Uživatelé v této roli mají přístup k plné sadě možností správy v [aplikaci M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Tato role má možnost číst informace o adresáři, monitorovat stav služby, lístky podpory souborů a přistupovat k aspektům nastavení pro správu Insights.

### <a name="insights-business-leader"></a>[Vedoucí firmy pro Insights](#insights-business-leader-permissions)
Uživatelé v této roli mají přístup k sadě řídicích panelů a přehledů prostřednictvím [aplikace M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). To zahrnuje plný přístup ke všem řídicím panelům a prezentovaným přehledům a funkcím průzkumu dat. Uživatelé v této roli nemají přístup k nastavení konfigurace produktu, což je zodpovědnost role správce Insights.

### <a name="intune-administrator"></a>[Správce Intune](#intune-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Intune online, pokud je tato služba k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení, aby bylo možné přidružit zásady, a také vytvářet a spravovat skupiny. Další informace najdete v [rámci řízení správy na základě rolí (RBAC) pomocí Microsoft Intune](/intune/role-based-access-control).

Tato role může vytvářet a spravovat všechny skupiny zabezpečení. Správce Intune ale nemá oprávnění správce přes skupiny Office. To znamená, že správce nemůže aktualizovat vlastníky ani členství všech skupin Office v organizaci. Může ale spravovat skupinu Office, kterou vytvoří, která je součástí svých oprávnění koncových uživatelů. Všechny skupiny Office (nikoli skupina zabezpečení), které vytvoří, by se měly počítat s kvótou 250.

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Intune". Ve [Azure Portal](https://portal.azure.com)je to správce Intune.

### <a name="kaizala-administrator"></a>[Správce Kaizala](#kaizala-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění ke správě nastavení v rámci Microsoft Kaizala, kdy je služba k dispozici, a také možnost spravovat lístky podpory a monitorovat stav služby. Kromě toho může uživatel získat přístup k sestavám souvisejícím s přijetím & používání Kaizala podle členů organizace a obchodních sestav vytvořených pomocí akcí Kaizala.

### <a name="license-administrator"></a>[Správce licencí](#license-administrator-permissions)

Uživatelé v této roli můžou přidávat, odebírat a aktualizovat přiřazení licencí pro uživatele, skupiny (pomocí licencování na základě skupin) a spravovat umístění používání u uživatelů. Role neuděluje možnost kupovat ani spravovat odběry, vytvářet a spravovat skupiny ani vytvářet ani spravovat uživatele nad rámec tohoto místa použití. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="message-center-privacy-reader"></a>[Čtenář ochrany osobních údajů centra zpráv](#message-center-privacy-reader-permissions)

Uživatelé v této roli můžou monitorovat všechna oznámení v centru zpráv, včetně zpráv o soukromí dat. Čtenáři ochrany osobních údajů centra zpráv získají e-mailová oznámení, včetně těch, které se týkají ochrany osobních údajů a můžou zrušit odběr pomocí předvoleb centra zpráv. Jenom globální správce a čtenář ochrany osobních údajů centra zpráv můžou číst zprávy o ochraně dat. Tato role navíc obsahuje možnost zobrazení skupin, domén a předplatných. Tato role nemá oprávnění k zobrazení, vytvoření nebo správě žádostí o služby.

### <a name="message-center-reader"></a>[Čtenář centra zpráv](#message-center-reader-permissions)

Uživatelé v této roli můžou monitorovat oznámení a poradenské aktualizace stavu v [centru zpráv](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro jejich organizaci na konfigurovaných službách, jako je Exchange, Intune a Microsoft Teams. Čtečky centra zpráv obdrží týdenní e-mailové zprávy o příspěvcích, aktualizacích a můžou sdílet příspěvky centra zpráv v Microsoft 365. Uživatelé přiřazení k této roli ve službě Azure AD budou mít přístup jen pro čtení ke službám Azure AD, jako jsou uživatelé a skupiny. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="modern-commerce-user"></a>[Moderní uživatel pro obchodování](#modern-commerce-user-permissions)

Nepoužívat. Tato role se automaticky přiřadí z obchodu a není určená ani podporovaná pro jiné použití. Níže najdete podrobnosti.

Role uživatele moderního obchodování poskytuje určitým uživatelům oprávnění k přístupu k centru pro správu Microsoft 365 a zobrazení levého navigačního panelu pro **domovskou stránku**, **fakturaci** a **podporu**. Obsah, který je dostupný v těchto oblastech, se řídí [rolemi specifickými pro obchod](../../cost-management-billing/manage/understand-mca-roles.md) , které jsou přiřazené uživatelům pro správu produktů, které si koupili pro sebe nebo vaši organizaci. To může zahrnovat úkoly, jako jsou platby za účty, nebo přístup k fakturačním účtům a profilům fakturace. 

Uživatelé s uživatelskou rolí moderního obchodování mají obvykle oprávnění správce v dalších nákupních systémech Microsoftu, ale nemají role globálního správce nebo správce fakturace používané pro přístup do centra pro správu. 

**Kdy je role uživatele moderního obchodování přiřazená?**

* **Nákup samoobslužných služeb v centru pro správu Microsoft 365** – nákup samoobslužných služeb dává uživatelům možnost vyzkoušet si nové produkty, a to tak, že si je zakoupí nebo zaregistrují sami. Tyto produkty se spravují v centru pro správu. Uživatelům, kteří si zakoupí samoobslužné služby, se přiřadí role v systému pro obchodování a moderní obchodní role, aby mohli spravovat své nákupy v centru pro správu. Správci můžou blokovat nákupy samoobslužných služeb (pro Power BI, Power Apps, Power Automate) prostřednictvím [PowerShellu](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Další informace najdete v [nejčastějších dotazech k samoobslužnému nákupu](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).  
* **Nákupy od komerčního tržiště Microsoftu**  – podobně jako při nákupu samoobslužných služeb, když uživatel koupí produkt nebo službu z Microsoft AppSource nebo Azure Marketplace, přiřadí se moderní obchodní role uživatele, pokud nemají roli globálního správce nebo správce fakturace. V některých případech je možné, že uživatelé můžou tyto nákupy zablokovat. Další informace najdete v tématu [komerční tržiště Microsoftu](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* **Návrhy od Microsoftu**  – návrh je formální nabídka Microsoftu, která vaší organizaci koupí produkty a služby Microsoftu. Když osoba, která přijímá návrh, nemá roli globálního správce nebo správce fakturace ve službě Azure AD, přiřadí se mu role specifická pro obchod pro dokončení návrhu a role uživatele moderního obchodování pro přístup k centru pro správu. Když přistupují k centru pro správu, můžou používat jenom funkce, které jsou autorizované v rámci své obchodní role.
* **Role specifické pro obchod** – někteří uživatelé mají přiřazeny role specifické pro obchod. Pokud uživatel není globálním správcem nebo správcem fakturace, získá uživatel moderní obchodní role, aby mohl získat přístup k centru pro správu.  

Pokud je role uživatele moderního obchodování Nepřiřazená uživateli, ztratí přístup k centru pro správu Microsoft 365. Pokud by nějaké produkty spravovaly buď pro sebe, nebo pro vaši organizaci, nebudou je moct spravovat. To může zahrnovat přiřazení licencí, změnu způsobů platby, placení účtů nebo jiné úlohy pro správu předplatných.

### <a name="network-administrator"></a>[Správce sítě](#network-administrator-permissions)

Uživatelé v této roli můžou zkontrolovat doporučení k architektuře hraničního sítě od Microsoftu, která jsou založená na telemetrie sítě od jejich uživatelských umístění. Výkon sítě pro Microsoft 365 spoléhá na pečlivou architekturu hraniční sítě zákazníka v podniku, která je obecně specifická pro konkrétní uživatelské umístění. Tato role umožňuje upravovat zjištěná umístění uživatelů a konfiguraci síťových parametrů pro tato umístění, aby se usnadnila Vylepšená měření telemetrie a doporučení pro návrh.
### <a name="office-apps-administrator"></a>[Správce aplikací Office](#office-apps-administrator-permissions)

Uživatelé v této roli můžou spravovat nastavení cloudu pro Microsoft 365 aplikace. To zahrnuje správu zásad cloudu, samoobslužné správy stahování a možnosti Zobrazit sestavu související s aplikacemi Office. Tato role navíc uděluje možnost spravovat lístky podpory a monitorovat stav služby v hlavním centru pro správu. Uživatelé přiřazení k této roli můžou také spravovat komunikaci s novými funkcemi v aplikacích Office. 

### <a name="partner-tier1-support"></a>[Podpora partnerů Tier1](#partner-tier1-support-permissions)

Nepoužívat. Tato role se už nepoužívá a v budoucnu se odebere z Azure AD. Tato role je určená pro použití malým počtem partnerů Microsoftu pro prodej a není určená pro obecné použití.

### <a name="partner-tier2-support"></a>[Podpora partnerů 2](#partner-tier2-support-permissions)

Nepoužívat. Tato role se už nepoužívá a v budoucnu se odebere z Azure AD. Tato role je určená pro použití malým počtem partnerů Microsoftu pro prodej a není určená pro obecné použití.

### <a name="password-administrator"></a>[Správce hesel](#password-administrator-permissions)

Uživatelé s touto rolí mají omezené možnosti správy hesel. Tato role neuděluje schopnost spravovat žádosti o služby nebo monitorovat stav služby. Správci hesel můžou resetovat hesla jiných uživatelů, kteří nejsou správci, nebo členové následujících rolí:

* Čtečky adresářů
* Pozvánka hosta
* Správce hesel

### <a name="power-bi-administrator"></a>[Správce Power BI](#power-bi-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Power BI, když je služba přítomná, a také možnost spravovat lístky podpory a monitorovat stav služby. Další informace o [principu role správce Power BI](/power-bi/service-admin-role).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Power BI Správce služby". V [Azure Portal](https://portal.azure.com)je "Power BI správce".

### <a name="power-platform-administrator"></a>[Správce Power Platform](#power-platform-administrator-permissions)

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty prostředí, PowerApps, toků a zásady ochrany před únikem informací. Kromě toho uživatelé s touto rolí mají možnost spravovat lístky podpory a monitorovat stav služby.

### <a name="printer-administrator"></a>[Správce tiskárny](#printer-administrator-permissions)

Uživatelé v této roli můžou registrovat tiskárny a spravovat všechny aspekty všech konfigurací tiskáren v rámci univerzálního tiskového řešení Microsoftu, včetně nastavení univerzálního tiskového konektoru. Můžou si udělit souhlas s všemi delegovanými žádostmi o oprávnění k tisku. Správci tiskáren mají také přístup k tiskovým sestavám.

### <a name="printer-technician"></a>[Technik tiskárny](#printer-technician-permissions)

Uživatelé s touto rolí můžou registrovat tiskárny a spravovat stav tiskáren v rámci univerzálního tiskového řešení Microsoftu. Můžou si taky přečíst všechny informace o konektoru. Klíčová úloha: technik tiskárny nemůže provést nastavení uživatelských oprávnění na tiskárnách a sdílení tiskáren.

### <a name="privileged-authentication-administrator"></a>[Správce privilegovaného ověřování](#privileged-authentication-administrator-permissions)

Uživatelé s touto rolí můžou nastavit nebo resetovat přihlašovací údaje hesla pro všechny uživatele, včetně globálních správců, a můžou aktualizovat hesla pro všechny uživatele. Správci privilegovaného ověřování můžou vynutit, aby uživatelé znovu zaregistrovali u stávajících přihlašovacích údajů bez hesla (například MFA nebo FIDO) a odvolali si MFA na zařízení. při příštím přihlášení všech uživatelů se zobrazí dotaz na MFA. Role [Správce ověřování](#authentication-administrator) může vynutit opětovnou registraci a MFA jenom pro uživatele, kteří jsou přiřazeni k těmto rolím Azure AD:

* Správce ověřování
* Čtečky adresářů
* Pozvánka hosta
* Čtenář centra zpráv
* Čtečka sestav

### <a name="privileged-role-administrator"></a>[Správce privilegovaných rolí](#privileged-role-administrator-permissions)

Uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure Active Directory a taky v Azure AD Privileged Identity Management. Můžou vytvářet a spravovat skupiny, které se dají přiřadit k rolím Azure AD. Kromě toho tato role umožňuje správu všech aspektů Privileged Identity Management a jednotek pro správu.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přiřazení pro všechny role Azure AD, včetně role globálního správce. Tato role nezahrnuje žádné další privilegované možnosti ve službě Azure AD, jako je vytváření nebo aktualizace uživatelů. Uživatelé přiřazení k této roli ale můžou udělit další role nebo jiné další oprávnění.

### <a name="reports-reader"></a>[Čtečka sestav](#reports-reader-permissions)

Uživatelé s touto rolí mohou zobrazovat data vytváření sestav o využití a řídicí panel sestavy v centru pro správu Microsoft 365 a kontext pro přijetí v Power BI. Role navíc poskytuje přístup k sestavám přihlašování a aktivitám v Azure AD a datům vráceným rozhraním API pro vytváření sestav Microsoft Graph. Uživatel přiřazený k roli čtenáře sestav má přístup pouze k relevantním metrikám využití a přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu k centrům pro správu specifickým pro konkrétní produkt, jako je Exchange. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="search-administrator"></a>[Správce hledání](#search-administrator-permissions)

Uživatelé v této roli mají plný přístup ke všem funkcím pro správu služby Microsoft Search v centru pro správu Microsoft 365. Kromě toho mohou uživatelé zobrazit Centrum zpráv, monitorovat stav služeb a vytvářet žádosti o služby.

### <a name="search-editor"></a>[Editor hledání](#search-editor-permissions)

Uživatelé v této roli můžou vytvářet, spravovat a odstraňovat obsah pro Microsoft Search v centru pro správu Microsoft 365, včetně záložek, Q&jako a umístění.

### <a name="security-administrator"></a>[Správce zabezpečení](#security-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících se zabezpečením ve službě Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection a Office 365 Security & Security Center. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v centru zabezpečení & dodržování předpisů](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Monitorování zásad souvisejících se zabezpečením napříč Microsoft 365 službami<br>Správa bezpečnostních hrozeb a výstrah<br>Zobrazení sestav
Centrum služby Identity Protection | Všechna oprávnění role čtenář zabezpečení<br>Kromě toho možnost provádět všechny operace centra služby Identity Protection s výjimkou resetování hesel
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Všechna oprávnění role čtenář zabezpečení<br>**Nejde** spravovat přiřazení rolí nebo nastavení role Azure AD.
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad zabezpečení<br>Zobrazení, zkoumání a reakce na bezpečnostní hrozby<br>Zobrazení sestav
Azure Advanced Threat Protection | Monitorování a reakce na podezřelé aktivity zabezpečení
Ochrana ATP a EDR v programu Windows Defender | Přiřazení rolí<br>Správa skupin počítačů<br>Konfigurace detekce hrozeb a automatizované nápravy koncových bodů<br>Zobrazit, prozkoumat a reagovat na výstrahy
[Intune](/intune/role-based-access-control) | Zobrazí informace o uživatelích, zařízeních, registraci, konfiguraci a aplikacích.<br>Nejde dělat změny v Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Přidávání správců, přidávání zásad a nastavení, nahrávání protokolů a provádění akcí zásad správného řízení
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Může zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.
[Stav služby Microsoft 365](/office365/enterprise/view-service-health) | Zobrazit stav služby Microsoft 365 Services
[Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md) | Zadejte prahovou hodnotu a dobu uzamčení, pokud dojde k neúspěšným událostem přihlášení.
[Ochrana heslem](../authentication/concept-password-ban-bad.md) | Nakonfigurujte vlastní seznam zakázaných hesel nebo místní ochrana heslem.

### <a name="security-operator"></a>[Operátor zabezpečení](#security-operator-permissions)

Uživatelé s touto rolí můžou spravovat výstrahy a mít globální přístup jen pro čtení k funkcím spojeným se zabezpečením, včetně všech informací v Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management a Office 365 Security & Center pro dodržování předpisů. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v centru zabezpečení & dodržování předpisů](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení hrozeb
Centrum služby Identity Protection | Všechna oprávnění role čtenář zabezpečení<br>Kromě toho možnost provádět všechny operace centra služby Identity Protection s výjimkou resetování hesel
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Všechna oprávnění role čtenář zabezpečení
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení
Ochrana ATP a EDR v programu Windows Defender | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení
[Intune](/intune/role-based-access-control) | Všechna oprávnění role čtenář zabezpečení
[Cloud App Security](/cloud-app-security/manage-admins) | Všechna oprávnění role čtenář zabezpečení
[Stav služby Microsoft 365](/office365/enterprise/view-service-health) | Zobrazit stav služby Microsoft 365 Services

### <a name="security-reader"></a>[Čtenář zabezpečení](#security-reader-permissions)

Uživatelé s touto rolí mají globální přístup jen pro čtení k funkcím souvisejícím se zabezpečením, včetně všech informací v Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management, a také možnost číst Azure Active Directory sestav a protokolů auditu a v centru zabezpečení pro & 365 zabezpečení pro Office. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v centru zabezpečení & dodržování předpisů](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Zobrazení zásad souvisejících se zabezpečením napříč Microsoft 365 službami<br>Zobrazit bezpečnostní hrozby a výstrahy<br>Zobrazení sestav
Centrum služby Identity Protection | Čtení všech sestav zabezpečení a informací o nastavení pro funkce zabezpečení<br><ul><li>Ochrana proti nevyžádané poště<li>Šifrování<li>Prevence ztráty dat<li>Ochrana proti malwaru<li>Rozšířená ochrana před internetovými útoky<li>Ochrana proti podvodným zprávám<li>Pravidla mailflow
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Má přístup jen pro čtení ke všem informacím, které jsou v Azure AD Privileged Identity Management: zásady a sestavy pro přiřazení rolí Azure AD a recenze zabezpečení.<br>**Nelze** se zaregistrovat k Azure AD Privileged Identity Management ani provádět žádné změny. V portálu Privileged Identity Management nebo prostřednictvím PowerShellu může osoba v této roli aktivovat další role (například správce globálních správců nebo privilegovaných rolí), pokud je pro ně uživatel oprávněný.
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zobrazení zásad zabezpečení<br>Zobrazit a prozkoumat bezpečnostní hrozby<br>Zobrazení sestav
Ochrana ATP a EDR v programu Windows Defender | Zobrazit a prozkoumat výstrahy. Když zapnete řízení přístupu na základě role v ochraně ATP v programu Windows Defender, uživatelé s oprávněním jen pro čtení, jako je role čtenář zabezpečení Azure AD, ztratí přístup, dokud nebudou přiřazeni k roli ochrany ATP v programu Windows Defender.
[Intune](/intune/role-based-access-control) | Zobrazí informace o uživatelích, zařízeních, registraci, konfiguraci a aplikacích. Nemůže provádět změny v Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Může zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny.
[Stav služby Microsoft 365](/office365/enterprise/view-service-health) | Zobrazit stav služby Microsoft 365 Services

### <a name="service-support-administrator"></a>[Správce služby Service support](#service-support-administrator-permissions)

Uživatelé s touto rolí můžou otevřít žádosti o podporu od Microsoftu pro Azure a Microsoft 365 služby a zobrazit řídicí panel služby a Centrum zpráv v [centru pro správu](https://admin.microsoft.com) [Azure Portal](https://portal.azure.com) a Microsoft 365. Další informace najdete v informacích [o rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Dříve byla tato role v [Azure Portal](https://portal.azure.com) a v [centru pro správu Microsoft 365](https://admin.microsoft.com)se nazývá "Správce služby". Přejmenovali jsme ho na správce služby Service support, aby se v Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell rovnal s názvem existujícího.

### <a name="sharepoint-administrator"></a>[Správce služby SharePoint](#sharepoint-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft SharePoint Online, pokud je tato služba k dispozici, a také možnost vytvářet a spravovat všechny Microsoft 365 skupiny, spravovat lístky podpory a monitorovat stav služby. Další informace najdete v informacích [o rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby SharePoint". Je to "Správce služby SharePoint" v [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Tato role také uděluje vymezená oprávnění Microsoft Graph rozhraní API pro Microsoft Intune a umožňuje tak správu a konfiguraci zásad souvisejících se zdroji na SharePointu a OneDrive.

### <a name="skype-for-business--lync-administrator"></a>[Správce Skypu pro firmy/Lync](#lync-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, když je služba přítomná, a také spravovat atributy uživatelů pro Skype v Azure Active Directory. Kromě toho tato role uděluje možnost spravovat lístky podpory a monitorovat stav služeb a přistupovat k týmům a centru pro správu Skypu pro firmy. Účet musí být také licencován pro týmy nebo nemůže spustit rutiny prostředí PowerShell pro týmy. Další informace najdete v části informace o licencích [pro role správce Skypu pro firmy](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) a týmy na [webu Skype pro firmy a v doplňku pro licencování Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) .

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Lync". Ve [Azure Portal](https://portal.azure.com/)je to správce Skypu pro firmy.

### <a name="teams-communications-administrator"></a>[Správce komunikace týmů](#teams-communications-administrator-permissions)

Uživatelé v této roli můžou spravovat aspekty úloh Microsoft Teams souvisejících se službou Voice & telefonního subsystému. To zahrnuje nástroje pro správu přiřazení telefonního čísla, zásady hlasu a schůzky a úplný přístup ke sadě nástrojů pro analýzu volání.

### <a name="teams-communications-support-engineer"></a>[Týmy Communications support inženýr](#teams-communications-support-engineer-permissions)

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelem v Microsoft Teams & v centru pro správu Skypu pro firmy. Uživatelé v této roli mohou zobrazit úplné informace o záznamech volání pro všechny účastnící se účastníky. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="teams-communications-support-specialist"></a>[Týmy Communications support specialisty](#teams-communications-support-specialist-permissions)

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelem v Microsoft Teams & v centru pro správu Skypu pro firmy. Uživatelé v této roli můžou zobrazit jenom podrobnosti o uživateli ve volání pro konkrétního uživatele, který si vyhledali. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="teams-devices-administrator"></a>[Správci zařízení Teams](#teams-devices-administrator-permissions)

Uživatelé s touto rolí můžou spravovat [zařízení s certifikací pro týmy](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) z centra pro správu týmů. Tato role umožňuje zobrazit všechna zařízení na jednom první pohled, s možností vyhledávání a filtrování zařízení. Uživatel může zkontrolovat podrobnosti každého zařízení, včetně přihlášeného účtu, a vytvořit a model zařízení. Uživatel může změnit nastavení na zařízení a aktualizovat verze softwaru. Tato role neuděluje oprávnění kontrolovat aktivity týmů a zavolá kvalitu zařízení. 

### <a name="teams-service-administrator"></a>[Správce služby Teams](#teams-service-administrator-permissions)

Uživatelé v této roli můžou spravovat všechny aspekty úloh Microsoft Teams prostřednictvím Microsoft Teams & centra pro správu Skypu pro firmy a příslušné moduly PowerShellu. Mezi další oblasti patří i všechny nástroje pro správu, které se týkají telefonního subsystému, zasílání zpráv, schůzek a týmů. Tato role navíc uděluje možnost vytvářet a spravovat všechny Microsoft 365 skupiny, spravovat lístky podpory a monitorovat stav služby.

### <a name="usage-summary-reports-reader"></a>[Čtečka sestav Souhrn využití](#usage-summary-reports-reader-permissions)

Uživatelé s touto rolí mají přístup ke agregovaným datům na úrovni tenanta a k souvisejícím přehledům v centru pro správu Microsoft 365 pro využití a zvýšení produktivity, ale nemají přístup k podrobnostem na úrovni uživatele nebo přehledům. V centru pro správu Microsoft 365 pro tyto dvě sestavy rozlišujeme mezi agregovanými daty úrovně tenanta a podrobnostmi na úrovni uživatele. Tato role poskytuje dodatečnou vrstvu ochrany na jednotlivých uživatelem identifikovatelných dat, která požadovali zákazníci i právní týmy. 

### <a name="user-administrator"></a>[Správce uživatele](#user-administrator-permissions)

Uživatelé s touto rolí můžou vytvářet uživatele a spravovat všechny aspekty uživatelů s některými omezeními (viz tabulku) a můžou aktualizovat zásady vypršení platnosti hesla. Uživatelé s touto rolí můžou navíc vytvářet a spravovat všechny skupiny. Tato role také zahrnuje možnost vytvářet a spravovat zobrazení uživatelů, spravovat lístky podpory a monitorovat stav služby. Správci uživatelů nemají oprávnění ke správě některých uživatelských vlastností pro uživatele ve většině rolí správce. Uživatel s touto rolí nemá oprávnění ke správě vícefaktorového ověřování. Role, které jsou výjimkou tohoto omezení, jsou uvedeny v následující tabulce.

| **Oprávnění** | **Může** |
| --- | --- |
|Obecná oprávnění|<p>Vytváření uživatelů a skupin</p><p>Vytvářet a spravovat zobrazení uživatelů</p><p>Správa lístků podpory Office<p>Aktualizace zásad vypršení platnosti hesla|
| <p>U všech uživatelů, včetně všech správců</p>|<p>Správa licencí</p><p>Spravovat všechny vlastnosti uživatele kromě hlavního názvu uživatele</p>
| Pouze pro uživatele, kteří nejsou správci, nebo v některé z následujících rolí s omezeným oprávněním správce:<ul><li>Čtečky adresářů<li>Správce skupin<li>Pozvánka hosta<li>Správce helpdesku<li>Čtenář centra zpráv<li>Správce hesel<li>Čtečka sestav<li>Správce uživatelů|<p>Odstranění a obnovení</p><p>Zakázat a povolit</p><p>Zrušit platnost obnovovacích tokenů</p><p>Spravovat všechny vlastnosti uživatele včetně hlavního názvu uživatele</p><p>Resetování hesla</p><p>Aktualizace (FIDO) – klíče zařízení</p>|

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit hesla pro lidi, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna hesla uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>- Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům uživatelů. Prostřednictvím této cesty může správce uživatelů předpokládat identitu vlastníka aplikace a pak dále předpokládat identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>- Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>- Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

## <a name="role-permissions"></a>Oprávnění role

Následující tabulky popisují specifická oprávnění v Azure Active Directory daných rolím. Některé role mohou mít další oprávnění ve službách Microsoftu mimo Azure Active Directory.

### <a name="application-administrator-permissions"></a>Oprávnění správce aplikace

Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Application/appProxyAuthentication/Update | Aktualizujte vlastnosti ověřování proxy aplikací u instančních objektů v Azure Active Directory. |
| Microsoft. Directory/Application/appProxyUrlSettings/Update | Aktualizuje interní a externí adresy URL proxy aplikace v Azure Active Directory. |
| Microsoft. Directory/Applications/applicationProxy/Read | Načte všechny vlastnosti proxy aplikací. |
| Microsoft. Directory/Applications/applicationProxy/Update | Aktualizuje všechny vlastnosti proxy aplikací. |
| Microsoft. Directory/Applications/publikum/Update | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vytvořit | Vytváření aplikací v Azure Active Directory. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| Microsoft. Directory/aplikace/odstranit | Odstraní aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Vytvořte appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Read | Přečtěte si appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/DELETE | Odstraní appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/connectorGroups/allProperties/Read | Číst vlastnosti skupiny konektoru proxy aplikací v Azure Active Directory. |
| Microsoft. Directory/connectorGroups/allProperties/Update | Aktualizovat vlastnosti skupiny konektorů proxy aplikací v Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Create | Vytvořte skupiny konektorů proxy aplikací v Azure Active Directory. |
| Microsoft. Directory/connectorGroups/DELETE | Odstraní skupiny konektorů proxy aplikací v Azure Active Directory. |
| Microsoft. Directory/Connectors/allProperties/Read | Přečte všechny vlastnosti konektoru proxy aplikací v Azure Active Directory. |
| Microsoft. Directory/konektory/vytvořit | Vytvořte konektory proxy aplikací v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Vytvořte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/Read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Vytvořte servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/DELETE | Odstraní servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="application-developer-permissions"></a>Oprávnění pro vývojáře aplikace

Může vytvořit registrace aplikací nezávisle na nastavení uživatelé můžou registrovat aplikace.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Applications/createAsOwner | Vytváření aplikací v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/appRoleAssignments/createAsOwner | Vytvořte appRoleAssignments v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/oAuth2PermissionGrants/createAsOwner | Vytvořte oAuth2PermissionGrants v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/servicePrincipals/createAsOwner | Vytvořte servicePrincipals v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |

### <a name="authentication-administrator-permissions"></a>Oprávnění správce ověřování

Povoleno zobrazení, nastavení a obnovení informací o metodě ověřování pro libovolného uživatele bez oprávnění správce.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Update | Aktualizujte vlastnosti silného ověřování jako informace o přihlašovacích údajích MFA. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v organizaci Microsoft 365. Další podrobnosti najdete v online dokumentaci. |

### <a name="attack-payload-author-permissions"></a>Oprávnění autora datové části útoku

Může vytvořit datovou část útoku, kterou může správce nasadit později.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. protectionCenter/attackSimulator/datové části/allProperties/allTasks | Vytvářejte a spravujte datovou část útoků v simulátoru útoku. |
| Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Přečtěte si sestavy simulace útoků, odpovědí a přidružených školení. |

### <a name="attack-simulation-administrator-permissions"></a>Oprávnění správce simulace útoků

Může vytvářet a spravovat všechny aspekty simulace útoků.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. protectionCenter/attackSimulator/datové části/allProperties/allTasks | Vytvářejte a spravujte datovou část útoků v simulátoru útoku. |
| Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Přečtěte si sestavy simulace útoků, odpovědí a přidružených školení. |
| Microsoft. Office 365. protectionCenter/attackSimulator/simulace/allProperties/allTasks | Vytváření a Správa šablon simulace útoků v simulátoru útoků. |

### <a name="azure-devops-administrator-permissions"></a>Oprávnění správce Azure DevOps

Může spravovat zásady a nastavení organizace Azure DevOps.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu [Popis role](#azure-devops-administrator) výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. devOps/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Azure Information Protection oprávnění správce

Může spravovat všechny aspekty služby Azure Information Protection.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu [Popis role](#) výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF, oprávnění správce sady klíčů

Spravujte tajné klíče pro federaci a šifrování v architektuře prostředí identity.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. B2C/trustFramework/-Sets/allTasks | Přečte a nakonfiguruje sady klíčů v Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Oprávnění správce zásad IEF B2C

Vytváření a Správa zásad pro pravidla důvěryhodnosti v architektuře prostředí identity

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. B2C/trustFramework/policies/allTasks | Čtení a konfigurace vlastních zásad v Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Oprávnění správce fakturace

Může provádět běžné úkoly související s fakturací, jako je aktualizace platebních údajů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Organization/Basic/Update | Aktualizuje základní vlastnosti v organizaci v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Commerce. fakturace/allEntities/allTasks | Spravujte všechny aspekty fakturace. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Oprávnění správce cloudové aplikace

Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací kromě proxy aplikací.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Applications/publikum/Update | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vytvořit | Vytváření aplikací v Azure Active Directory. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| Microsoft. Directory/aplikace/odstranit | Odstraní aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Vytvořte appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/DELETE | Odstraní appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Vytvořte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Owners/Update | Aktualizuje vlastnost policies. applicationConfiguration v Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/Read | Číst zásady. applicationConfiguration vlastnosti v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Vytvořte servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/DELETE | Odstraní servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Oprávnění správce cloudového zařízení

Úplný přístup ke správě zařízení ve službě Azure AD.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Čtení objektů a vlastností klíče nástroje BitLocker (včetně obnovovacího klíče) v Azure Active Directory. |
| Microsoft. Directory/Devices/DELETE | Odstraňte zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Zakáže zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/Enable | Povolte zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/extensionAttributes/Update | Aktualizujte všechny hodnoty vlastnosti Devices. extensionAttributes v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |

### <a name="company-administrator-permissions"></a>Oprávnění správce společnosti

Může spravovat všechny aspekty Azure AD a služeb Microsoftu, které používají identity Azure AD. Tato role je také známá jako role globálního správce. 

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. cloudAppSecurity/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. aad. cloudAppSecurity. |
| Microsoft. Directory/administrativeUnits/allProperties/allTasks | Vytvořit a odstranit administrativeUnits a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/Applications/allProperties/allTasks | Vytvářejte a odstraňujte aplikace a čtěte a aktualizujte všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Vytvořit a odstranit appRoleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Čtení objektů a vlastností klíče nástroje BitLocker (včetně obnovovacího klíče) v Azure Active Directory. |
| Microsoft. Directory/Contacts/allProperties/allTasks | Vytváření a odstraňování kontaktů a čtení a aktualizace všech vlastností v Azure Active Directory. |
| Microsoft. Directory/Contracts/allProperties/allTasks | Vytváření a odstraňování smluv a čtení a aktualizace všech vlastností v Azure Active Directory. |
| Microsoft. Directory/Devices/allProperties/allTasks | Vytváření a odstraňování zařízení a čtení a aktualizace všech vlastností v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/allProperties/allTasks | Vytvořit a odstranit directoryRoles a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Vytvořit a odstranit directoryRoleTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/domény/allProperties/allTasks | Vytváření a odstraňování domén a čtení a aktualizace všech vlastností v Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/allTasks | Vytvářejte a odstraňujte prostředky a přečtěte si a aktualizujte všechny vlastnosti ve správě nároků ve službě Azure AD. |
| Microsoft. Directory/Groups/allProperties/allTasks | Vytváření a odstraňování skupin a čtení a aktualizace všech vlastností v Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Aktualizujte skupiny s vlastností isAssignableToRole nastavenou na hodnotu true v Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/Create | V Azure Active Directory vytvořit skupiny s vlastností isAssignableToRole nastavenou na hodnotu true. |
| Microsoft. Directory/groupsAssignableToRoles/DELETE | V Azure Active Directory odstraňte skupiny s vlastností isAssignableToRole nastavenou na hodnotu true. |
| Microsoft. Directory/groupSettings/allProperties/allTasks | Vytvořit a odstranit groupSettings a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Vytvořit a odstranit groupSettingTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/loginTenantBranding/allProperties/allTasks | Vytvořit a odstranit loginTenantBranding a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Vytvořit a odstranit oAuth2PermissionGrants a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/Organization/allProperties/allTasks | Vytvářejte a odstraňujte organizaci a čtěte a aktualizujte všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/allProperties/allTasks | Vytváření a odstraňování zásad a čtení a aktualizace všech vlastností v Azure Active Directory. |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Vytvořit a odstranit roleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Vytvořit a odstranit roleDefinitions a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Vytvořit a odstranit scopedRoleMemberships a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/serviceAction/activateService | Může provádět akci služby Activateservice v Azure Active Directory |
| Microsoft. Directory/serviceAction/disableDirectoryFeature | Může provádět akci služby Disabledirectoryfeature v Azure Active Directory |
| Microsoft. Directory/serviceAction/enableDirectoryFeature | Může provádět akci služby Enabledirectoryfeature v Azure Active Directory |
| Microsoft. Directory/serviceAction/getAvailableExtentionProperties | Může provádět akci služby Getavailableextentionproperties v Azure Active Directory |
| Microsoft. Directory/servicePrincipals/allProperties/allTasks | Vytvořit a odstranit servicePrincipals a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/allProperties/allTasks | Vytvořit a odstranit subscribedSkus a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. Directory/Users/allProperties/allTasks | Umožňuje vytvářet a odstraňovat uživatele a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| Microsoft. directorySync/allEntities/allTasks | Provede všechny akce v Azure AD Connect. |
| Microsoft. aad. identityProtection/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. aad. identityProtection. |
| Microsoft. aad. privilegedIdentityManagement/allEntities/Read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Azure. advancedThreatProtection/allEntities/Read | Načte všechny prostředky v Microsoft. Azure. advancedThreatProtection. |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Commerce. fakturace/allEntities/allTasks | Spravujte všechny aspekty fakturace. |
| Microsoft. Intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| Microsoft. Office 365. complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
| Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Spravujte všechny aspekty Desktop Analytics. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| Microsoft. Office 365. bezpečnostní modul/allEntities/allTasks | Správa všech aspektů Office 365 Customer Lockbox |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/Read | Přečtěte si securityMessages v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. protectionCenter/allEntities/allTasks | Spravujte všechny aspekty centra ochrany Office 365. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. securityComplianceCenter. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. SharePoint/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. usageReports/allEntities/Read | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| Microsoft. powerApps. powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/Read | Čtení všech prostředků v Microsoft. Windows. defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Oprávnění správce dodržování předpisů

Může číst a spravovat konfiguraci a sestavy dodržování předpisů v Azure AD a Microsoft 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/entitlementManagement/allProperties/Read | Načte všechny vlastnosti v Azure AD – Správa nároků. |
| Microsoft. Office 365. complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="compliance-data-administrator-permissions"></a>Oprávnění správce dat dodržování předpisů

Vytvoří a spravuje obsah dodržování předpisů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory. cloudAppSecurity/allEntities/allTasks | Čtení a konfigurace Microsoft Cloud App Security. |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="conditional-access-administrator-permissions"></a>Oprávnění správce podmíněného přístupu

Může spravovat funkce podmíněného přístupu.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/policies/conditionalAccess/Basic/Read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Basic/Update | Aktualizuje vlastnost policies. conditionalAccess v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Create | Vytvořte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Owners/Read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Owners/Update | Aktualizuje vlastnost policies. conditionalAccess v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/policiesAppliedTo/Read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/tenantDefault/Update | Aktualizuje vlastnost policies. conditionalAccess v Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Oprávnění správce služby CRM

Může spravovat všechny aspekty produktu Dynamics 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Oprávnění schvalovatele pro přístup k bezpečnostnímu modulu zákazníka

Může schvalovat žádosti o podporu Microsoftu pro přístup k datům organizace zákazníka.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. bezpečnostní modul/allEntities/allTasks | Správa všech aspektů Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator-permissions"></a>Oprávnění správce pro Desktop Analytics

Může spravovat služby zásad & Desktop Analytics a Office Customization. Pro Desktop Analytics to zahrnuje možnost Zobrazit inventář assetů, vytvářet plány nasazení, zobrazovat stav nasazení a stavu. V případě služby Office Customization & Policy tato role umožňuje uživatelům spravovat zásady Office.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Spravujte všechny aspekty Desktop Analytics. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="device-administrators-permissions"></a>Oprávnění pro správce zařízení

Uživatelé přiřazení k této roli budou přidáni do místní skupiny Administrators na zařízeních připojených k Azure AD.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/groupSettings/Basic/Read | Přečte základní vlastnosti groupSettings v Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Přečte základní vlastnosti groupSettingTemplates v Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Oprávnění čtenáře adresářů
Může číst základní informace o adresáři. Pro udělení přístupu k aplikacím, které nejsou určené pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/administrativeUnits/Basic/Read | Přečte základní vlastnosti administrativeUnits v Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/Members/Read | Načte vlastnost administrativeUnits. Members v Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Read | Přečte základní vlastnosti aplikací v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/čtení | Číst vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/číst | Přečte vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Read | Přečte základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/Read | Číst vlastnost Contacts. memberOf v Azure Active Directory. |
| Microsoft. Directory/Contracts/Basic/Read | Přečte základní vlastnosti smluv v Azure Active Directory. |
| Microsoft. Directory/zařízení/Basic/číst | Přečte základní vlastnosti na zařízeních v Azure Active Directory. |
| Microsoft. Directory/zařízení/memberOf/číst | Číst vlastnost Devices. memberOf v Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Read | Číst vlastnost Devices. registeredOwners v Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Read | Číst vlastnost Devices. registeredUsers v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/Read | Přečte základní vlastnosti directoryRoles v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/eligibleMembers/Read | Číst vlastnost directoryRoles. eligibleMembers v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Members/Read | Načte vlastnost directoryRoles. Members v Azure Active Directory. |
| Microsoft. Directory/domény/Basic/číst | Přečte základní vlastnosti domén v Azure Active Directory. |
| Microsoft. Directory/Groups/appRoleAssignments/Read | Načte vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Read | Přečte základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/memberOf/Read | Načte vlastnost groups. memberOf v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Read | Přečte vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Read | Číst vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Read | Číst vlastnost groups. Settings v Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Read | Přečte základní vlastnosti groupSettings v Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Přečte základní vlastnosti groupSettingTemplates v Azure Active Directory. |
| Microsoft. Directory/oAuth2PermissionGrants/Basic/Read | Přečte základní vlastnosti oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/Organization/Basic/Read | Přečte základní vlastnosti v organizaci v Azure Active Directory. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read | Číst vlastnost Organization. trustedCAsForPasswordlessAuth v Azure Active Directory. |
| Microsoft. Directory/roleAssignments/Basic/Read | Přečte základní vlastnosti roleAssignments v Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/Basic/Read | Přečte základní vlastnosti roleDefinitions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Read | Číst vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Read | Číst vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Read | Přečte základní vlastnosti servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/memberOf/Read | Načte vlastnost servicePrincipals. memberOf v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/Basic/Read | Číst vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/ownedObjects/Read | Číst vlastnost servicePrincipals. ownedObjects v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Read | Číst vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Read | Číst vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/Basic/Read | Přečte základní vlastnosti subscribedSkus v Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Read | Číst vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/číst | Číst základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read | Číst vlastnost Users. directReports v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Správce/číst | Umožňuje číst vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/uživatelé/memberOf/číst | Číst vlastnost Users. memberOf v Azure Active Directory. |
| Microsoft. Directory/Users/oAuth2PermissionGrants/Basic/Read | Číst vlastnost Users. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read | Číst vlastnost Users. ownedDevices v Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read | Číst vlastnost Users. ownedObjects v Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read | Číst vlastnost Users. registeredDevices v Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Oprávnění pro účty synchronizace adresářů

Používáno pouze službou Azure AD Connect.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Organization/dirSync/Update | Aktualizuje vlastnost Organization. dirSync v Azure Active Directory. |
| Microsoft. Directory/policies/Create | Vytvořte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Read | Přečte základní vlastnosti zásad v Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Update | Aktualizuje základní vlastnosti zásad v Azure Active Directory. |
| Microsoft. Directory/policies/Owners/Read | Číst vlastnost policies. Owners v Azure Active Directory. |
| Microsoft. Directory/policies/Owners/Update | V Azure Active Directory vlastnost Update policies. Owners. |
| Microsoft. Directory/policies/policiesAppliedTo/Read | Číst zásady. policiesAppliedTo vlastnosti v Azure Active Directory. |
| Microsoft. Directory/policies/tenantDefault/Update | Aktualizuje vlastnost policies. tenantDefault v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Read | Číst vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Read | Číst vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Read | Přečte základní vlastnosti servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Vytvořte servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/memberOf/Read | Načte vlastnost servicePrincipals. memberOf v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/Basic/Read | Číst vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Read | Číst vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/ownedObjects/Read | Číst vlastnost servicePrincipals. ownedObjects v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Read | Číst vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. directorySync/allEntities/allTasks | Provede všechny akce v Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Oprávnění k zapisovači adresářů

Může číst & informace o adresáři Basic Write. Pro udělení přístupu k aplikacím, které nejsou určené pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/assignLicense | Spravujte licence na skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory.  |
| Microsoft. Directory/Groups/klasifikací/Update | Aktualizuje vlastnost klasifikace skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Vytvořte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/groupType/Update | Aktualizuje vlastnost groupType skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/Groups/reprocessLicenseAssignment | Znovu zpracovat přiřazení licencí pro skupinu v Azure Active Directory. |
| Microsoft. Directory/Groups/securityEnabled/Update | Aktualizuje vlastnost secutiryEnabled skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| Microsoft. Directory/Groups/Visibility/Update | Aktualizovat vlastnost viditelnosti skupiny |
| Microsoft. Directory/groupSettings/Basic/Update | Aktualizuje základní vlastnosti v groupSettings v Azure Active Directory. |
| Microsoft. Directory/groupSettings/Create | Vytvořit groupSettings v Azure Active Directory.. |
| Microsoft. Directory/groupSettings/DELETE | Odstraní groupSettings v Azure Active Directory. |
| Microsoft. Directory/oAuth2PermissionGrants/Basic/Update | Aktualizuje základní vlastnosti oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/oAuth2PermissionGrants/Create | Vytvořte oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Správa tajných klíčů a přihlašovacích údajů pro zřizování aplikací |
| Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Spusťte, restartujte a pozastavte úlohy synchronizace zřizování aplikací. |
| Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Vytváření a správa úloh synchronizace zřizování aplikací a schématu. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/Update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/vytvořit | Vytvářejte uživatele ve službě Azure Active Directory. |
| Microsoft. Directory/Users/Disable | Zakáže uživatelský účet v Azure Active Directory. |
| Microsoft. Directory/uživatelé/povolit | Povolení uživatelského účtu v Azure Active Directory |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zrušení platnosti všech tokenů aktualizace uživatele v Azure Active Directory, vyžadování opětovného ověření uživatelů při příštím přihlášení |
| Microsoft. Directory/uživatelé/správce/aktualizace | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/Users/reprocessLicenseAssignment | Znovu zpracovat přiřazení licencí pro uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Aktualizujte vlastnost Users. userPrincipalName v Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Oprávnění správce služby Exchange

Může spravovat všechny aspekty produktu Exchange.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizuje vlastnost groups. Unified v Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/Basic/Update | Aktualizuje základní vlastnosti Microsoft 365 skupin. |
| Microsoft. Directory/Groups/Unified/Create | Vytvořte Microsoft 365 skupiny. |
| Microsoft. Directory/Groups/Unified/DELETE | Odstraní Microsoft 365 skupiny. |
| Microsoft. Directory/Groups/Unified/Members/Update | Aktualizuje členství Microsoft 365ch skupin. |
| Microsoft. Directory/Groups/Unified/Owners/Update | Aktualizuje vlastnictví skupin Microsoft 365. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Přečtěte si stránky o výkonu sítě v centru pro správu Microsoft 365. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. usageReports/allEntities/Read | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="external-id-user-flow-administrator-permissions"></a>Oprávnění správce toku externích ID uživatele

Vytvářejte a spravujte všechny aspekty toků uživatelů.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. B2C/userFlows/allTasks | Čtení a konfigurace toků uživatelů v Azure Active Directory B2C. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>Oprávnění správce atributu toku uživatele externího ID

Vytvořte a spravujte schéma atributů dostupné pro všechny toky uživatelů.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. B2C/userAttributes/allTasks | Čtení a konfigurace uživatelských atributů v Azure Active Directory B2C. |

### <a name="external-identity-provider-administrator-permissions"></a>Oprávnění správce externího zprostředkovatele identity

Nakonfigurujte zprostředkovatele identity pro použití v přímé federaci.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. aad. B2C/identityProviders/allTasks | Číst a konfigurovat zprostředkovatele identity v Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Oprávnění pro globální čtenáře
Může číst vše, co globální správce může, ale ne nic upravovat.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu [Popis role](#global-reader) výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Commerce. fakturace/allEntities/čtení    | Přečtěte si všechny aspekty fakturace. |
| Microsoft. Directory/administrativeUnits/Basic/Read    | Přečte základní vlastnosti administrativeUnits v Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/Members/Read    | Načte vlastnost administrativeUnits. Members v Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Read    | Přečte základní vlastnosti aplikací v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/čtení    | Číst vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/číst    | Přečte vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Čtení objektů a vlastností klíče nástroje BitLocker (včetně obnovovacího klíče) v Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Read    | Přečte základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/Read    | Číst vlastnost Contacts. memberOf v Azure Active Directory. |
| Microsoft. Directory/Contracts/Basic/Read    | Přečte základní vlastnosti smluv v Azure Active Directory. |
| Microsoft. Directory/zařízení/Basic/číst    | Přečte základní vlastnosti na zařízeních v Azure Active Directory. |
| Microsoft. Directory/zařízení/memberOf/číst    | Číst vlastnost Devices. memberOf v Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Read    | Číst vlastnost Devices. registeredOwners v Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Read    | Číst vlastnost Devices. registeredUsers v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/Read    | Přečte základní vlastnosti directoryRoles v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/eligibleMembers/Read    | Číst vlastnost directoryRoles. eligibleMembers v Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Members/Read    | Načte vlastnost directoryRoles. Members v Azure Active Directory. |
| Microsoft. Directory/domény/Basic/číst    | Přečte základní vlastnosti domén v Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/Read | Načte všechny vlastnosti v Azure AD – Správa nároků. |
| Microsoft. Directory/Groups/appRoleAssignments/Read    | Načte vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Read    | Přečte základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read    | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| Microsoft. Directory/Groups/memberOf/Read    | Načte vlastnost groups. memberOf v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Read    | Přečte vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Read    | Číst vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Read    | Číst vlastnost groups. Settings v Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Read    | Přečte základní vlastnosti groupSettings v Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read    | Přečte základní vlastnosti groupSettingTemplates v Azure Active Directory. |
| Microsoft. Directory/oAuth2PermissionGrants/Basic/Read    | Přečte základní vlastnosti oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/Organization/Basic/Read    | Přečte základní vlastnosti v organizaci v Azure Active Directory. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read    | Číst vlastnost Organization. trustedCAsForPasswordlessAuth v Azure Active Directory. |
| Microsoft. Directory/policies/Standard/Read    | Číst standardní zásady v Azure Active Directory. |
| Microsoft. Directory/roleAssignments/Basic/Read    | Přečte základní vlastnosti roleAssignments v Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/Basic/Read    | Přečte základní vlastnosti roleDefinitions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Read    | Číst vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Read    | Číst vlastnost servicePrincipals. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Read    | Přečte základní vlastnosti servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/memberOf/Read    | Načte vlastnost servicePrincipals. memberOf v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/Basic/Read    | Číst vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/ownedObjects/Read    | Číst vlastnost servicePrincipals. ownedObjects v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Read    | Číst vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Read    | Číst vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read    | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/Basic/Read    | Přečte základní vlastnosti subscribedSkus v Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Read    | Číst vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/číst    | Číst základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read    | Číst vlastnost Users. directReports v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Správce/číst    | Umožňuje číst vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/uživatelé/memberOf/číst    | Číst vlastnost Users. memberOf v Azure Active Directory. |
| Microsoft. Directory/Users/oAuth2PermissionGrants/Basic/Read    | Číst vlastnost Users. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read    | Číst vlastnost Users. ownedDevices v Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read    | Číst vlastnost Users. ownedObjects v Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read    | Číst vlastnost Users. registeredDevices v Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Read    | Přečtěte si vlastnosti silného ověřování, jako jsou přihlašovací údaje MFA. |
| Microsoft. Office 365. Exchange/allEntities/Read    | Přečtěte si všechny aspekty Exchange Online. |
| Microsoft. Office 365. messageCenter/Messages/Read    | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/Read    | Přečtěte si securityMessages v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Přečtěte si stránky o výkonu sítě v centru pro správu Microsoft 365. |
| Microsoft. Office 365. protectionCenter/allEntities/Read    | Přečtěte si všechny aspekty centra ochrany Office 365. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/Read    | Načte všechny standardní vlastnosti v Microsoft. Office 365. securityComplianceCenter. |
| Microsoft. Office 365. usageReports/allEntities/Read    | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Standard/Read    | Načte standardní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="groups-administrator-permissions"></a>Oprávnění správce skupin
Může spravovat všechny aspekty skupin a nastavení skupiny, jako jsou zásady pro pojmenování a vypršení platnosti.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Groups/Basic/Read | Přečte standardní vlastnosti pro skupiny v Azure Active Directory.  |
| Microsoft. Directory/Groups/Basic/Update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Vytvořte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/Groups/DELETE | Odstraní skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/skupiny/obnovení | Obnovte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="guest-inviter-permissions"></a>Oprávnění pozvat hosta
Může pozvat uživatele typu Host bez ohledu na nastavení členové můžou pozvat hosty.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Users/appRoleAssignments/Read | Číst vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/číst | Číst základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read | Číst vlastnost Users. directReports v Azure Active Directory. |
| Microsoft. Directory/Users/inviteGuest | Pozvání uživatelů typu Host v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Správce/číst | Umožňuje číst vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/uživatelé/memberOf/číst | Číst vlastnost Users. memberOf v Azure Active Directory. |
| Microsoft. Directory/Users/oAuth2PermissionGrants/Basic/Read | Číst vlastnost Users. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read | Číst vlastnost Users. ownedDevices v Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read | Číst vlastnost Users. ownedObjects v Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read | Číst vlastnost Users. registeredDevices v Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Oprávnění správce helpdesku

Může resetovat hesla pro správce bez oprávnění správce a helpdesku.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="hybrid-identity-administrator-permissions"></a>Oprávnění správce hybridní identity

Může spravovat AD do zřizování cloudu Azure AD a nastavení federace. 

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/Applications/publikum/Update  | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory.  |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vytvořit | Vytváření aplikací v Azure Active Directory. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| Microsoft. Directory/aplikace/odstranit | Odstraní aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/applicationTemplates/instance | Vytvoří instanci aplikací galerie z šablon aplikací. |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/cloudProvisioning/allProperties/allTasks | Přečtěte si a nakonfigurujte všechny vlastnosti služby Azure AD Cloud Provisioning. |
| Microsoft. Directory/domén/allProperties/Read | Načte všechny vlastnosti domén. |
| Microsoft. Directory/domény/federace/aktualizace | Aktualizuje vlastnost federace domén. |
| Microsoft. Directory/Organization/dirSync/Update | Aktualizuje vlastnost Organization. dirSync v Azure Active Directory. |
| Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
| Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Create | Vytvořte servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/DELETE | Odstraní servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Spravujte všechny aspekty úloh synchronizace ve službě Azure AD. |
| Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Umožňuje spravovat všechny aspekty schématu synchronizace ve službě Azure AD. |
| Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Spravujte všechny aspekty synchronizačních přihlašovacích údajů ve službě Azure AD. |
| Microsoft. Directory/servicePrincipals/tag/Update | Aktualizujte vlastnost servicePrincipals. tag v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="insights-administrator-permissions"></a>Přehledy oprávnění správce

Má v aplikaci Microsoft 365 Insights přístup pro správu. 

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Insights/allEntities/allTasks | Spravujte všechny aspekty přehledů. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="insights-business-leader-permissions"></a>Oprávnění pro vedoucí firmy pro Insights

Může zobrazit a sdílet řídicí panely a přehledy prostřednictvím aplikace M365 Insights.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Insights/sestavy/číst | Zobrazte si sestavy a řídicí panel v aplikaci Insights. |
| Microsoft. Insights/programy/aktualizace | Nasaďte a spravujte programy v aplikaci Insights. |

### <a name="intune-service-administrator-permissions"></a>Oprávnění správce služby Intune

Může spravovat všechny aspekty produktu Intune.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/bitlockerKeys/Key/Read | Čtení objektů a vlastností klíče nástroje BitLocker (včetně obnovovacího klíče) v Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Vytvoří kontakty v Azure Active Directory. |
| Microsoft. Directory/Contacts/DELETE | Odstraní kontakty v Azure Active Directory. |
| Microsoft. Directory/Devices/Basic/Update | Aktualizuje základní vlastnosti na zařízeních v Azure Active Directory. |
| Microsoft. Directory/Devices/Create | Vytvořte zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/DELETE | Odstraňte zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Zakáže zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/Enable | Povolte zařízení v Azure Active Directory. |
| Microsoft. Directory/Devices/extensionAttributes/Update | Aktualizujte všechny hodnoty vlastnosti Devices. extensionAttributes v Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Update | V Azure Active Directory aktualizovat vlastnost Devices. registeredOwners. |
| Microsoft. Directory/Devices/registeredUsers/Update | V Azure Active Directory aktualizovat vlastnost Devices. registeredUsers. |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Vytvořte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/Groups/DELETE | Odstraní skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/skupiny/obnovení | Obnovte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/Update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/správce/aktualizace | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="kaizala-administrator-permissions"></a>Oprávnění správce Kaizala

Může spravovat nastavení pro Microsoft Kaizala.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Čtení centra pro správu Microsoft 365. |

### <a name="license-administrator-permissions"></a>Oprávnění správce licence

Může spravovat licence na produkty pro uživatele a skupiny.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/usageLocation/Update | Umožňuje aktualizovat vlastnost Users. usageLocation v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |

### <a name="lync-service-administrator-permissions"></a>Oprávnění správce služby Lync

Může spravovat všechny aspekty produktu Skype pro firmy.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. usageReports/allEntities/Read    | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |


### <a name="message-center-privacy-reader-permissions"></a>Oprávnění čtenářů ochrany osobních údajů centra zpráv

Může číst příspěvky centra zpráv, zprávy o ochraně dat, skupiny, domény a odběry.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/Read | Přečtěte si securityMessages v Microsoft. Office 365. messageCenter. |

### <a name="message-center-reader-permissions"></a>Oprávnění čtenáře centra zpráv
Může číst zprávy a aktualizace jejich organizace pouze v centru zpráv. 

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |

### <a name="modern-commerce-user-permissions"></a>Oprávnění pro moderní obchodní uživatele
Může spravovat komerční nákupy pro firmu, oddělení nebo tým. 

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. obchod. fakturace/partneři/čtení | Přečtěte si vlastnost partner u Microsoft 365 fakturace. |
| Microsoft. Commerce. volumeLicenseServiceCenter/allEntities/allTasks | Spravujte všechny aspekty služby Volume Licensing Service Center. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a zobrazování lístků podpory pro Office 365 |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |


### <a name="network-administrator-permissions"></a>Oprávnění správce sítě
Může spravovat síťová umístění a prohlížet informace o návrhu podnikové sítě, které Microsoft 365 software jako aplikace služby.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Přečtěte si stránky výkon sítě v centru pro správu M365.  |
| Microsoft. Office 365. Network/Locations/allProperties/allTasks | Číst a konfigurovat vlastnosti síťových umístění pro každé umístění. |

### <a name="office-apps-administrator-permissions"></a>Oprávnění správce aplikací Office
Může spravovat cloudové služby aplikací Office, včetně správy zásad a nastavení, a spravovat možnost výběru, zrušení výběru a publikování obsahu funkcí co je nového pro zařízení koncových uživatelů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. userCommunication/allEntities/allTasks | Přečtěte si a aktualizujte viditelnost nových zpráv. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="partner-tier1-support-permissions"></a>Oprávnění podpory partnerského Tier1u

Nepoužívejte – Neurčeno pro obecné použití.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Applications/appRoles/Update | Spravujte aplikační role a požádejte o delegovaná oprávnění pro aplikace. |
| Microsoft. Directory/Applications/publikum/Update | Aktualizuje cílovou skupinu u všech typů aplikací. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje ověřování u všech typů aplikací. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti všech typů aplikací. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizujte přihlašovací údaje u všech typů aplikací. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastníky u všech typů aplikací. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizujte vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Vytvoří kontakty v Azure Active Directory. |
| Microsoft. Directory/Contacts/DELETE | Odstraní kontakty v Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Vytvořte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/Update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/DELETE | Umožňuje odstranit uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/správce/aktualizace | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft. Directory/Users/Restore | Obnovení odstraněných uživatelů v Azure Active Directory. |
| Microsoft. Directory/uživatelé/userPrincipalName/Update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="partner-tier2-support-permissions"></a>Oprávnění podpory partnerského 2U

Nepoužívejte – Neurčeno pro obecné použití.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Applications/appRoles/Update | Spravujte aplikační role a požádejte o delegovaná oprávnění pro aplikace. |
| Microsoft. Directory/Applications/publikum/Update | Aktualizuje cílovou skupinu u všech typů aplikací. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje ověřování u všech typů aplikací. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti všech typů aplikací. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizujte přihlašovací údaje u všech typů aplikací. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastníky u všech typů aplikací. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizujte vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Vytvoří kontakty v Azure Active Directory. |
| Microsoft. Directory/Contacts/DELETE | Odstraní kontakty v Azure Active Directory. |
| Microsoft. Directory/domén/allTasks | Vytváření a odstraňování domén a čtení a aktualizace standardních vlastností v Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Vytvořte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/DELETE | Odstraní skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/skupiny/obnovení | Obnovte skupiny v Azure Active Directory. |
| Microsoft. Directory/Organization/Basic/Update | Aktualizuje základní vlastnosti v organizaci v Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/Update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/DELETE | Umožňuje odstranit uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/správce/aktualizace | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft. Directory/Users/Restore | Obnovení odstraněných uživatelů v Azure Active Directory. |
| Microsoft. Directory/uživatelé/userPrincipalName/Update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="password-administrator-permissions"></a>Oprávnění správce hesel

Může resetovat hesla správců, kteří nejsou správci a hesla.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI oprávnění správce služby

Může spravovat všechny aspekty Power BI produktu.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>
| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. powerApps. powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |


### <a name="power-platform-administrator-permissions"></a>Oprávnění správce Power Platform

Může vytvářet a spravovat všechny aspekty Microsoft Dynamics 365, PowerApps a Power automatizuje.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>
| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| Microsoft. Flow/allEntities/allTasks | Spravujte všechny aspekty Power automatizuje. |
| Microsoft. powerApps/allEntities/allTasks | Umožňuje spravovat všechny aspekty PowerApps. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="printer-administrator-permissions"></a>Oprávnění správce tiskárny

Může spravovat všechny aspekty tiskáren a konektorů tiskáren.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>
| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. Print/allEntities/allProperties/allTasks | Vytvářejte a odstraňujte tiskárny a konektory a přečtěte si a aktualizujte všechny vlastnosti v tištěné společnosti Microsoft. |

### <a name="printer-technician-permissions"></a>Oprávnění technika tiskárny

Může registrovat a rušit registraci tiskáren a aktualizovat stav tiskárny.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>
| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. Print/Connectors/allProperties/Read | Načte všechny vlastnosti konektorů v Microsoft tisku. |
| Microsoft. Azure. Printing/Printers/allProperties/Read | Přečtěte si všechny vlastnosti tiskáren v Microsoft Print. |
| Microsoft. Azure. tisk/tiskárny/Basic/Update | Aktualizuje základní vlastnosti tiskáren v tištěné společnosti Microsoft. |
| Microsoft. Azure. tisk/tiskárny/registrovat | Zaregistrujte tiskárny v Microsoft tisku. |
| Microsoft. Azure. tisk/tiskárny/zrušit registraci | Zrušte registraci tiskáren v Microsoft tisku. |

### <a name="privileged-authentication-administrator-permissions"></a>Oprávnění správce privilegovaného ověřování

Povoleno zobrazení, nastavení a resetování informací o metodě ověřování pro libovolného uživatele (správce nebo jiný správce).

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Update | Aktualizujte vlastnosti silného ověřování jako informace o přihlašovacích údajích MFA. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v organizaci Microsoft 365. Další podrobnosti najdete v online dokumentaci. |

### <a name="privileged-role-administrator-permissions"></a>Oprávnění správce privilegovaných rolí

Může spravovat přiřazení rolí v Azure AD a všechny aspekty Privileged Identity Management.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Aktualizujte skupiny s vlastností isAssignableToRole nastavenou na hodnotu true v Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/Create | V Azure Active Directory vytvořit skupiny s vlastností isAssignableToRole nastavenou na hodnotu true. |
| Microsoft. Directory/groupsAssignableToRoles/DELETE | V Azure Active Directory odstraňte skupiny s vlastností isAssignableToRole nastavenou na hodnotu true. |
| Microsoft. Directory/privilegedIdentityManagement/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/allTasks | Přečte a nakonfiguruje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Přečte a nakonfiguruje vlastnost servicePrincipals. oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/allProperties/allTasks | Vytváření a Správa jednotek pro správu (včetně členů) |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Vytváření a správa přiřazení rolí. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Vytvářejte a spravujte definice rolí. |

### <a name="reports-reader-permissions"></a>Oprávnění čtenáře sestav

Může číst sestavy pro přihlášení a audit.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. usageReports/allEntities/Read | Přečtěte si sestavy o využití Office 365. |

### <a name="search-administrator-permissions"></a>Hledat oprávnění správce

Může vytvářet a spravovat všechny aspekty nastavení služby Microsoft Search.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. Search/allEntities/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat všechny vlastnosti v Microsoft. Office 365. Search. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="search-editor-permissions"></a>Oprávnění editoru hledání

Může vytvářet a spravovat redakční obsah, jako jsou záložky, Q a as, Locations, FloorPlan.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. messageCenter/Messages/Read | Přečte zprávy v Microsoft. Office 365. messageCenter. |
| Microsoft. Office 365. Search/Content/allProperties/allTasks | Vytváření a odstraňování obsahu a čtení a aktualizace všech vlastností v Microsoft. Office 365. Search. |

### <a name="security-administrator-permissions"></a>Oprávnění správce zabezpečení

Může číst informace o zabezpečení a sestavy a spravovat konfiguraci v Azure AD a Microsoft 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Čtení objektů a vlastností klíče nástroje BitLocker (včetně obnovovacího klíče) v Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/Read | Načte všechny vlastnosti v Azure AD – Správa nároků. |
| Microsoft. Directory/identityProtection/allProperties/Read | Načte všechny prostředky v Microsoft. aad. identityProtection. |
| Microsoft. Directory/identityProtection/allProperties/Update | Aktualizuje všechny prostředky v Microsoft. aad. identityProtection. |
| Microsoft. Directory/policies/Basic/Update | Aktualizuje základní vlastnosti zásad v Azure Active Directory. |
| Microsoft. Directory/policies/Create | Vytvořte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/Owners/Update | V Azure Active Directory vlastnost Update policies. Owners. |
| Microsoft. Directory/policies/tenantDefault/Update | Aktualizuje vlastnost policies. tenantDefault v Azure Active Directory. |
| Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. Office 365. protectionCenter/allEntities/Read | Přečtěte si všechny aspekty centra ochrany Office 365. |
| Microsoft. Office 365. protectionCenter/allEntities/Update | Aktualizuje všechny prostředky v Microsoft. Office 365. protectionCenter. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="security-operator-permissions"></a>Oprávnění operátora zabezpečení

Vytvoří a spravuje události zabezpečení.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. advancedThreatProtection/allEntities/Read | Čtení a konfigurace rozšířené ochrany před internetovými útoky Azure AD |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Čtení a konfigurace Microsoft Cloud App Security. |
| Microsoft. Directory/identityProtection/allProperties/Read | Načte všechny prostředky v Microsoft. aad. identityProtection. |
| Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Přečtěte si a nakonfigurujte & Security Center pro dodržování předpisů. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/Read | Čtení a konfigurace rozšířené ochrany před internetovými útoky v programu Windows Defender |

### <a name="security-reader-permissions"></a>Oprávnění čtenáře zabezpečení

Může číst informace o zabezpečení a sestavy v Azure AD a Microsoft 365.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Čtení objektů a vlastností klíče nástroje BitLocker (včetně obnovovacího klíče) v Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/Read | Načte všechny vlastnosti v Azure AD – Správa nároků. |
| Microsoft. Directory/policies/conditionalAccess/Basic/Read | Číst zásady. conditionalAccess vlastnosti v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |
| Microsoft. aad. identityProtection/allEntities/Read | Načte všechny prostředky v Microsoft. aad. identityProtection. |
| Microsoft. aad. privilegedIdentityManagement/allEntities/Read | Načte všechny prostředky v Microsoft. aad. privilegedIdentityManagement. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. protectionCenter/allEntities/Read | Přečtěte si všechny aspekty centra ochrany Office 365. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |

### <a name="service-support-administrator-permissions"></a>Služba Service support – oprávnění správce

Může číst informace o stavu služby a spravovat lístky podpory.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Oprávnění správce služby SharePoint

Může spravovat všechny aspekty služby SharePoint.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizuje vlastnost groups. Unified v Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/Basic/Update | Aktualizuje základní vlastnosti Microsoft 365 skupin. |
| Microsoft. Directory/Groups/Unified/Create | Vytvořte Microsoft 365 skupiny. |
| Microsoft. Directory/Groups/Unified/DELETE | Odstraní Microsoft 365 skupiny. |
| Microsoft. Directory/Groups/Unified/Members/Update | Aktualizuje členství Microsoft 365ch skupin. |
| Microsoft. Directory/Groups/Unified/Owners/Update | Aktualizuje vlastnictví skupin Microsoft 365. |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Přečtěte si stránky výkon sítě v centru pro správu M365. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. SharePoint/allEntities/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. usageReports/allEntities/Read    | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |

### <a name="teams-communications-administrator-permissions"></a>Teams Communications – oprávnění správce

Může spravovat funkce volání a schůzek ve službě Microsoft Teams.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. usageReports/allEntities/Read | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Teams/schůze/allProperties/allTasks | Spravujte schůzky, včetně zásad schůzky, konfigurací a konferenčních mostů. |
| Microsoft. Teams/Voice/allProperties/allTasks | Spravujte hlas, včetně zásad volání a inventáře a přiřazení telefonního čísla. |
| Microsoft. Teams/callQuality/allProperties/Read | Přečte všechna data na řídicím panelu kvality volání (CQD). |

### <a name="teams-communications-support-engineer-permissions"></a>Komunikace týmů podporuje oprávnění inženýra

Může řešit problémy s komunikací v rámci týmů pomocí pokročilých nástrojů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Teams/callQuality/allProperties/Read | Přečte všechna data na řídicím panelu kvality volání (CQD). |

### <a name="teams-communications-support-specialist-permissions"></a>Komunikace týmů podpora Specialistových oprávnění

Může řešit problémy s komunikací v rámci týmů pomocí základních nástrojů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Teams/callQuality/Basic/Read | Přečte základní data na řídicím panelu kvality volání (CQD). |

### <a name="teams-devices-administrator-permissions"></a>Týmy zařízení oprávnění správce

Může provádět úlohy související se správou na certifikovaných zařízeních týmů.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Teams/Devices/Basic/Read | Spravujte všechny aspekty zařízení, která jsou certifikována pro týmy, včetně zásad konfigurace. |

### <a name="teams-service-administrator-permissions"></a>Oprávnění správce služby Teams

Může spravovat službu Microsoft Teams.

> [!NOTE]
> Tato role má další oprávnění mimo Azure Active Directory. Další informace najdete v tématu Popis role výše.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizuje vlastnost groups. Unified v Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/Basic/Update | Aktualizuje základní vlastnosti Microsoft 365 skupin. |
| Microsoft. Directory/Groups/Unified/Create | Vytvořte Microsoft 365 skupiny. |
| Microsoft. Directory/Groups/Unified/DELETE | Odstraní Microsoft 365 skupiny. |
| Microsoft. Directory/Groups/Unified/Members/Update | Aktualizuje členství Microsoft 365ch skupin. |
| Microsoft. Directory/Groups/Unified/Owners/Update | Aktualizuje vlastnictví skupin Microsoft 365. |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Přečtěte si stránky výkon sítě v centru pro správu M365. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |
| Microsoft. Office 365. usageReports/allEntities/Read | Přečtěte si sestavy o využití Office 365. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Teams/allEntities/allProperties/allTasks | Spravujte všechny prostředky v týmech. |

### <a name="usage-summary-reports-reader-permissions"></a>Oprávnění čtenáře sestav souhrnu využití
V M365 analýza využití a hodnocení produktivity se můžou zobrazit jenom agregace na úrovni tenanta.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Office 365. usageReports/allEntities/Standard/Read | Přečtěte si agregované sestavy využití Office 365 na úrovni tenanta. |
| Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort.|

### <a name="user-administrator-permissions"></a>Oprávnění správce uživatele
Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/appRoleAssignments/Create | Vytvořte appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/DELETE | Odstraní appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizuje základní vlastnosti kontaktů v Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Vytvoří kontakty v Azure Active Directory. |
| Microsoft. Directory/Contacts/DELETE | Odstraní kontakty v Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/allTasks | Vytvářejte a odstraňujte prostředky a přečtěte si a aktualizujte všechny vlastnosti ve správě nároků ve službě Azure AD. |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Vytvořte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Vytvořte skupiny v Azure Active Directory. Tvůrce se přidá jako první vlastník a vytvořený objekt se počítá s kvótou vytvořenou pro objekty tvůrce 250. |
| Microsoft. Directory/Groups/DELETE | Odstraní skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Načte vlastnost groups. hiddenMembers v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/skupiny/obnovení | Obnovte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Spravujte licence na uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/Basic/Update | Umožňuje aktualizovat základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/vytvořit | Vytvářejte uživatele ve službě Azure Active Directory. |
| Microsoft. Directory/Users/DELETE | Umožňuje odstranit uživatele v Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Zruší platnost všech tokenů aktualizace uživatele v Azure Active Directory. |
| Microsoft. Directory/uživatelé/správce/aktualizace | Umožňuje aktualizovat vlastnost Users. Manager v Azure Active Directory. |
| Microsoft. Directory/uživatelé/heslo/aktualizace | Aktualizuje hesla pro všechny uživatele v Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft. Directory/Users/Restore | Obnovení odstraněných uživatelů v Azure Active Directory. |
| Microsoft. Directory/uživatelé/userPrincipalName/Update | Umožňuje aktualizovat vlastnost Users. userPrincipalName v Azure Active Directory. |
| Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure pro služby na úrovni adresáře. |
| Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v Microsoft. Office 365. WebPort. |
| Microsoft. Office 365. serviceHealth/allEntities/allTasks | Přečte a nakonfiguruje Microsoft 365 Service Health. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Office 365. |

## <a name="role-template-ids"></a>ID šablon rolí

ID šablon rolí se používají hlavně pomocí Microsoft Graph API nebo uživatelů prostředí PowerShell.

Zobrazovaný název grafu | Zobrazované jméno Azure Portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Správce aplikace | Správce aplikace | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Vývojář aplikace | Vývojář aplikace | CF1C38E5-3621-4004-A7CB-879624DCED7C
Správce ověřování | Správce ověřování | c4e39bd9-1100-46d3-8c65-fb160da0071f
Autor datové části útoku | Autor datové části útoku | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f
Správce simulace útoků | Správce simulace útoků | c430b396-e693-46cc-96f3-db01bf8bb62a
Místní správce zařízení připojený k Azure AD | Místní správce zařízení připojený k Azure AD | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Správce Azure DevOps | Správce Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Správce Azure Information Protection | Správce Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C IEF, správce sady klíčů | B2C IEF, správce sady klíčů | aaf43236-0c0d-4d5f-883a-6955382ac081
Správce zásad IEF B2C | Správce zásad IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Správce fakturace | Správce fakturace | b0f54661-2d74-4c50-afa3-1ec803f12efe
Správce cloudové aplikace | Správce cloudové aplikace | 158c047a-c907-4556-b7ef-446551a6b5f7
Správce cloudového zařízení | Správce cloudového zařízení | 7698a772-787b-4ac8-901f-60d6b08affd2
Správce dodržování předpisů | Správce dodržování předpisů | 17315797-102d-40b4-93e0-432062caca18
Správce dat dodržování předpisů | Správce dat dodržování předpisů | e6d1a23a-da11-4be4-9570-befc86d067a7
Správce podmíněného přístupu | Správce podmíněného přístupu | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Schvalovatel přístupu k bezpečnostnímu modulu zákazníka | Schvalovatel přístupu Customer Lockbox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Správce Desktop Analytics | Správce Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Připojení zařízení | Zastaralé | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Správci zařízení | Zastaralé | 2b499bcd-da44-4968-8aec-78e1674fa64d
Uživatelé zařízení | Zastaralé | d405c6df-0af8-4e3b-95e4-4d06e542189e
Čtečky adresářů | Čtečky adresářů | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Účty synchronizace adresářů | Nezobrazeno, protože by neměl být použit | d29b2b05-8046-44ba-8758-1e26182fcf32
Zapisovače adresářů | Zapisovače adresářů | 9360feb5-f418-4baa-8175-e2a00bac4301
Správce Dynamics 365 | Správce Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Správce Exchange | Správce Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Správce toku externího ID uživatele | Správce toku externího ID uživatele | 6e591065-9bad-43ed-90f3-e9424366d2f0
Správce atributů toku uživatele externího ID | Správce atributů toku uživatele externího ID | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Správce externích zprostředkovatelů identity | Správce externích zprostředkovatelů identity | be2f45a1-457d-42af-a067-6ec1fa63bc45
Globální správce | Globální správce | 62e90394-69f5-4237-9190-012177145e10
Globální čtenář | Globální čtenář | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Správce skupin | Správce skupin | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Pozvánka hosta | Pozvánka hosta | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Správce helpdesku | Správce helpdesku | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Správce hybridní identity | Správce hybridní identity | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Správce Insights | Správce Insights | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c
Vedoucí firmy pro Insights | Vedoucí firmy pro Insights | 31e939ad-9672-4796-9c2e-873181342d2d
Správce Intune | Správce Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Správce Kaizala | Správce Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Správce licencí | Správce licencí | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Čtenář ochrany osobních údajů centra zpráv | Čtenář ochrany osobních údajů centra zpráv | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Čtenář centra zpráv | Čtenář centra zpráv | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Moderní uživatel pro obchodování | Moderní uživatel pro obchodování | d24aef57-1500-4070-84db-2666f29cf966
Správce sítě | Správce sítě | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Správce aplikací Office | Správce aplikací Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Podpora partnerů Tier1 | Nezobrazeno, protože by neměl být použit | 4ba39ca4-527c-499a-b93d-d9b492c50246
Podpora partnerů 2 | Nezobrazeno, protože by neměl být použit | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Správce hesel | Správce hesel | 966707d0-3269-4727-9be2-8c3a10f19b9d
Správce Power BI | Správce Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Správce Power Platform | Správce platforma Power | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Správce tiskárny | Správce tiskárny | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Technik tiskárny | Technik tiskárny | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Správce privilegovaného ověřování | Správce privilegovaného ověřování | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Správce privilegovaných rolí | Správce privilegovaných rolí | e8611ab8-c189-46e8-94e1-60213ab1f814
Čtečka sestav | Čtečka sestav | 4a5d8f65-41da-4de4-8968-e035b65339cf
Správce hledání | Správce hledání | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor hledání | Editor hledání | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Správce zabezpečení | Správce zabezpečení | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operátor zabezpečení | Operátor zabezpečení | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Čtenář zabezpečení | Čtenář zabezpečení | 5d6b6bb7-de71-4623-b4af-96380a352509
Správce služby Service support | Správce služeb | f023fd81-a637-4b56-95fd-791ac0226033
Správce služby SharePoint | Správce SharePointu | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Správce Skypu pro firmy | Správce Skypu pro firmy | 75941009-915a-4869-abe7-691bff18279e
Správce komunikace týmů | Správce komunikace týmů | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Týmy Communications support inženýr | Týmy Communications support inženýr | f70938a0-fc10-4177-9e90-2178f8765737
Týmy Communications support specialisty | Týmy Communications support specialisty | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Správci zařízení Teams | Správci zařízení Teams | 3d762c5a-1b6c-493f-843e-55a3b42923d4
Správce týmů | Správce týmů | 69091246-20e8-4a56-aa4d-066075b2a7a8
Čtečka sestav Souhrn využití | Čtečka sestav Souhrn využití | 75934031-6c7e-415a-99d7-48dbd49e875e
User | Nezobrazeno, protože nemůže být použit | a0b1b346-4d3e-4e8b-98f8-753987be4970
Správce uživatelů | Správce uživatelů | fe930be7-5e62-47db-91af-98c3a49a38b1
Připojení zařízení na pracovišti | Zastaralé | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Zastaralé role

Následující role by se neměly používat. Jsou zastaralé a v budoucnu se odeberou z Azure AD.

* Správce licencí ad hoc
* Připojení zařízení
* Správci zařízení
* Uživatelé zařízení
* Autor ověření uživatele e-mailem
* Správce poštovní schránky
* Připojení zařízení na pracovišti

## <a name="roles-not-shown-in-the-portal"></a>Role neuvedené na portálu

Ne všechny role vracené prostředím PowerShell nebo MS Graph API jsou viditelné v Azure Portal. Tyto rozdíly jsou uspořádány v následující tabulce.

Název rozhraní API | Název Azure Portal | Poznámky
-------- | ------------------- | -------------
Připojení zařízení | Zastaralé | [Dokumentace k zastaralým rolím](permissions-reference.md#deprecated-roles)
Správci zařízení | Zastaralé | [Dokumentace k zastaralým rolím](permissions-reference.md#deprecated-roles)
Uživatelé zařízení | Zastaralé | [Dokumentace k zastaralým rolím](permissions-reference.md#deprecated-roles)
Účty synchronizace adresářů | Nezobrazeno, protože by neměl být použit | [Dokumentace k účtům synchronizace adresářů](permissions-reference.md#directory-synchronization-accounts)
Uživatel typu host | Nezobrazeno, protože nemůže být použit  | NA
Podpora partnerské vrstvy 1 | Nezobrazeno, protože by neměl být použit | [Dokumentace k podpoře partnerů Tier1](permissions-reference.md#partner-tier1-support)
Podpora partnerské úrovně 2 | Nezobrazeno, protože by neměl být použit | [Dokumentace k podpoře partnerů 2](permissions-reference.md#partner-tier2-support)
Omezený uživatel typu Host | Nezobrazeno, protože nemůže být použit | NA
User | Nezobrazeno, protože nemůže být použit | NA
Připojení zařízení na pracovišti | Zastaralé | [Dokumentace k zastaralým rolím](permissions-reference.md#deprecated-roles)

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak přiřadit uživatele jako správce předplatného Azure, najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) .
* Další informace o tom, jak se řídí přístup k prostředkům v Microsoft Azure, najdete v tématu [pochopení různých rolí](../../role-based-access-control/rbac-and-directory-admin-roles.md) .
* Podrobnosti o vztahu mezi předplatnými a klientem služby Azure AD nebo pokyny k přidružení nebo přidání předplatného najdete v tématu [přidružení nebo přidání předplatného Azure do tenanta Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md) .
